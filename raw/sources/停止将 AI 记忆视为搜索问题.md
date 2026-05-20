---
title: "停止将 AI 记忆视为搜索问题"
source: "https://www.bestblogs.dev/article/c230749d?entry=resource_card&from=%2Fexplore%3Ftype%3Darticle%26page%3D3"
author:
  - "[[Benjamin Nweke]]"
published: 2026-04-12
created: 2026-04-17
description: "本文批判了传统的 AI 记忆‘存储-检索’方法，提出了一种基于生命周期的系统，包含衰减、矛盾检测、置信度评分、压缩和过期机制，旨在像管理人脑而非静态档案一样管理记忆。"
tags:
  - "clippings"
---
92

## Stop Treating AI Memory Like a Search Problem

本文批判了传统的 AI 记忆‘存储-检索’方法，提出了一种基于生命周期的系统，包含衰减、矛盾检测、置信度评分、压缩和过期机制，旨在像管理人脑而非静态档案一样管理记忆。

[Towards Data Science](https://www.bestblogs.dev/articles?sourceid=0c32dbfa "查看该来源的更多文章")

04-124540 字 (约 19 分钟)

[查看原文 →](https://towardsdatascience.com/stop-treating-ai-memory-like-a-search-problem/)

### 摘要

文章认为，将 AI 记忆视为简单的搜索问题，会导致助手变得不可靠，积累过时和矛盾的信息。作者指出，核心问题在于无法遗忘，而非无法记忆。为解决此问题，他们提出并实现了一个基于纯文本 SQLite 数据库的综合性记忆生命周期管理系统。关键组件包括：基于访问新近度的记忆衰减、使用 LLM 自动检测矛盾以取代旧事实、置信度评分以区分明确陈述与推断、定期压缩以合并重复或相关的记忆、以及临时信息的过期机制。该系统设计为异步运行后台维护任务（衰减、压缩、过期），以保持写入路径的快速性。作者为每个组件提供了完整、可用于生产的 Python 代码，并讨论了权衡和实现细节。

### 主要内容

- 1\. AI 记忆系统必须管理生命周期，而不仅仅是存储和检索。
	随着记忆老化、矛盾或变得无关，静态档案会变得不可靠。一个类脑系统需要衰减、取代和清理机制，以长期保持准确性。
- 2\. 矛盾检测是一个关键但常被忽略的组件，可防止 AI 基于过时事实采取行动。
	当存储新记忆时，应使用 LLM 检查其是否与现有的高优先级记忆相矛盾（例如，‘使用 PostgreSQL’ 与 ‘已迁移到 MySQL’），并将旧记忆标记为已取代，保留可追溯的历史记录。
- 3\. 记忆置信度评分区分了明确的用户陈述和 AI 推断，从而改进了检索优先级排序。
	记忆的置信度评分（在创建时通过一次小型 LLM 调用评估）会影响其检索排名。一个高置信度、中等重要性的记忆可能比一个高重要性、低置信度的记忆更可靠。

Back in October, my AI assistant stored a memory with an importance score of 8/10. Content: “Investigating Bun.js as a potential runtime swap.”

I never actually switched to Bun. To be fair, it was a two-day curiosity that went nowhere. But this memory persisted for six months, popping up each time I asked about my build process and quietly pushing the AI toward a Bun solution with confidence.

There was nothing wrong with the system; it was doing exactly what it was supposed to do. That was the issue.

Here’s the failure mode no one talks about when building AI memory systems. You make it work properly. It remembers things, retrieves things, all of the good stuff. And for a while, the AI seems clever.

Then you actually start using it.

Memories pile up. Decisions get reversed. Preferences shift. The system doesn’t notice.

You casually mention something in January, and it gets stored with high importance.

Cool.

By April, the AI treats it like a current fact. And sometimes, it takes a while to realize you’ve been working from outdated data.

A system that remembers everything doesn’t have a memory. It has an archive. And an archive that grows without hygiene quickly becomes messier than having no memory at all.

Nick Lawson wrote a great [piece](https://towardsdatascience.com/i-replaced-vector-dbs-with-googles-memory-agent-pattern-for-my-notes-in-obsidian/) here on TDS describing how he implemented just that. You’ll want to read it; the storage/retrieval architecture is really good.

But there’s a problem with this kind of system: what happens to memories as they age?

When should they die?

Which memory is more reliable than the others?

How many overlapping memories should be combined into one?

That’s what this article is about. Not storing and not retrieving, but what happens in between.

I’ll cover enough of the base layer to follow along, even if you haven’t read Nick’s piece. But the new ground starts where his article ends.

Let’s get into it.

## The Problem With “Store and Retrieve”

Most memory systems typically assume a two-step process. Write. Read. Checkmate.

Sure, that’s fine if you’re building a filing cabinet. Not if you’re trying to build an assistant that you can rely on for months.

What does that look like?

The memory you wrote in week one remains in week eight just as fresh and high-priority as the day you made it, even though the decision you made was reversed two weeks ago.

The other memory, which contradicts your earlier decision, was filed away casually and simply never had time to become a priority because it hasn’t received nearly enough accesses to push itself up the queue.

And so, without hesitation, your assistant pulls a decision you unmade. It’s not until the third attempt that you finally catch onto the pattern that your assistant has been relying on obsolete information the whole time.

The problem isn’t remembering, it’s failing to let go.

![](https://contributor.insightmediagroup.io/wp-content/uploads/2026/04/mermaid-diagram-2026-04-07-174645-scaled.png)

A comparison between a standard append-only archive and a lifecycle memory system that actively manages superseded information. Image by author.

The difference I wanted to build: an approach to memory that works like a brain, not like a database. Memory decays. It gets superseded.

Some memories aren’t very reliable from the start. Others expire after a certain period. The brain manages all of these automatically and without you doing anything. That was my aim.

## The Foundation (Brief, I Promise)

Let’s get a quick context check.

Rather than encoding your memories and running cosine similarity searches, you keep them in plain text inside an SQLite database, which the LLM can consult for a concise index on every request.

There’s no need for any embedding process, third-party API, or extra files. The LLM’s language understanding performs the retrieval task. It seems too simple. But it actually does surprisingly well on a personal level.

My schema builds on top of that with lifecycle fields:

```python
# memory_store.py
import sqlite3
import json
from datetime import datetime
from pathlib import Path
from contextlib import contextmanager

DB_PATH = Path("agent_memory.db")

@contextmanager
def _db():
    conn = sqlite3.connect(DB_PATH)
    conn.row_factory = sqlite3.Row
    try:
        yield conn
    finally:
        conn.close()

def init_db():
    with _db() as conn:
        conn.execute("""
            CREATE TABLE IF NOT EXISTS memories (
                id              INTEGER PRIMARY KEY AUTOINCREMENT,
                content         TEXT NOT NULL,
                summary         TEXT,
                tags            TEXT DEFAULT '[]',

                -- Lifecycle fields — this is what this article adds
                importance      REAL DEFAULT 5.0,
                confidence      REAL DEFAULT 1.0,
                access_count    INTEGER DEFAULT 0,
                decay_score     REAL DEFAULT 1.0,
                status          TEXT DEFAULT 'active',
                contradicted_by INTEGER REFERENCES memories(id),

                created_at      TEXT NOT NULL,
                last_accessed   TEXT,
                expires_at      TEXT
            )
        """)
        conn.execute("""
            CREATE TABLE IF NOT EXISTS memory_events (
                id          INTEGER PRIMARY KEY AUTOINCREMENT,
                memory_id   INTEGER REFERENCES memories(id),
                event_type  TEXT NOT NULL,
                detail      TEXT,
                occurred_at TEXT NOT NULL
            )
        """)
        conn.commit()

def store_memory(content: str, summary: str = None, tags: list[str] = None,
                 importance: float = 5.0, confidence: float = 1.0) -> int:
    with _db() as conn:
        cur = conn.execute("""
            INSERT INTO memories
                (content, summary, tags, importance, confidence, created_at)
            VALUES (?, ?, ?, ?, ?, ?)
        """, (
            content,
            summary or content[:120],
            json.dumps(tags or []),
            importance,
            confidence,
            datetime.now().isoformat()
        ))
        conn.commit()
        return cur.lastrowid

def log_event(memory_id: int, event_type: str, detail: str = ""):
    # Pulled this out of every module that needed it — was copy-pasting
    # the same INSERT four times. Classic.
    with _db() as conn:
        conn.execute("""
            INSERT INTO memory_events (memory_id, event_type, detail, occurred_at)
            VALUES (?, ?, ?, ?)
        """, (memory_id, event_type, detail, datetime.now().isoformat()))
        conn.commit()

init_db()
```

The interesting columns are the ones you don’t see in a standard memory schema: `confidence`, `decay_score`, `status`, `contradicted_by`, `expires_at`. Each one answers a question about a memory’s health that “does it exist?” can’t.

## Memory Decay

The first problem is pretty much simple: old memories do not tidy themselves.

Each memory in the database is assigned a `decay_score` from 0 to 1. It starts at 1.0 at the point of creation and decays over time, depending on how long ago the memory was last accessed.

Memories you keep referencing stay fresh. While memories that are not consulted for several months fade towards zero.

Once they fall below the relevance threshold, they’re archived, not deleted, because fading away doesn’t mean they were wrong, just no longer useful.

```python
# decay.py
import math
from datetime import datetime
from memory_store import _db, log_event

HALF_LIFE_DAYS = 30  # tune this — 30 works well for conversational memory,
                     # push to 90+ if you're tracking long-running projects

def _decay_score(last_accessed: str | None, created_at: str, access_count: int) -> float:
    ref = last_accessed or created_at
    days_idle = (datetime.now() - datetime.fromisoformat(ref)).days

    # Standard exponential decay: e^(-ln2 * t / half_life)
    # (In practice, the score halves every HALF_LIFE_DAYS.)

    score = math.exp(-0.693 * days_idle / HALF_LIFE_DAYS)

    # Frequently accessed memories earn a small freshness bonus.
    # Cap at 1.0 — this isn't meant to inflate beyond fresh.
    return min(1.0, score + min(0.3, access_count * 0.03))

def run_decay_pass():
    """Run daily. Updates scores, archives anything below 0.1."""
    with _db() as conn:
        rows = conn.execute("""
            SELECT id, created_at, last_accessed, access_count
            FROM memories WHERE status = 'active'
        """).fetchall()

        to_archive = [(r["id"],) for r in rows
                      if _decay_score(r["last_accessed"], r["created_at"], r["access_count"]) < 0.1]
        to_update  = [(_decay_score(r["last_accessed"], r["created_at"], r["access_count"]), r["id"])
                      for r in rows
                      if _decay_score(r["last_accessed"], r["created_at"], r["access_count"]) >= 0.1]

        if to_archive:
            conn.executemany(
                "UPDATE memories SET status='archived', decay_score=0.0 WHERE id=?",
                to_archive
            )
        if to_update:
            conn.executemany(
                "UPDATE memories SET decay_score=? WHERE id=?",
                to_update
            )
        conn.commit()

    for (mid,) in to_archive:
        log_event(mid, "archived", "decay below threshold")

    print(f"Decay pass: {len(to_update)} updated, {len(to_archive)} archived.")
```

`HALF_LIFE_DAYS` lives at the module level because that is the number you will likely want to change, and default values for functions live somewhere in limbo.

The batched `executemany` instead of looping `execute` matters once you’ve accumulated a few hundred memories. SQLite is fast, but not “500 individual commits in a daily cron job” fast.

This is also what would have caught the issue with Bun.js back at the intro of this post. My forgotten memory would have faded away within two months, without me even having to delete it.

## Contradiction Detection

This is the part nobody builds and the one that causes the most damage when it’s missing.

Let’s take this scenario: you tell the AI that you’re using PostgreSQL. Then three months later, you migrate to MySQL, briefly mentioning it in conversation.

Now, you have fourteen memories related to PostgreSQL with high importance, while your single memory involving MySQL has low importance.

So when you ask about your database setup six months from now, the AI confidently says “you’re using PostgreSQL,” and you spend ten minutes confused before you realise what’s happening.

I ran into this myself. I’d stopped using `poetry` and started using `uv` as my dependency manager, I mentioned it once, without triggering a high importance score, and then spent a week wondering why the assistant kept suggesting `poetry` commands. The old memory wasn’t wrong; it just hadn’t been superseded.

The fix: when a new memory is created, check whether it contradicts anything already stored and actively mark older ones as superseded.

```python
# contradiction.py
import json
from openai import OpenAI
from memory_store import _db, log_event

client = OpenAI()

def _build_index(exclude_id: int) -> str:
    with _db() as conn:
        rows = conn.execute("""
            SELECT id, summary FROM memories
            WHERE status = 'active' AND id != ?
            ORDER BY importance DESC, created_at DESC
            LIMIT 80
        """, (exclude_id,)).fetchall()
    return "\n".join(f"[{r['id']}] {r['summary']}" for r in rows)

def check_for_contradictions(new_content: str, new_id: int) -> list[int]:
    """
    Call immediately after storing a new memory.
    Returns IDs of memories now superseded by the new one.
    """
    index = _build_index(exclude_id=new_id)
    if not index:
        return []

    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        temperature=0,
        messages=[{"role": "user", "content": f"""A new memory was just stored:
"{new_content}"

Which of these existing memories does it directly contradict or supersede?
A contradiction means the new info makes the old one factually wrong or outdated.

NOT contradictions:
- "User likes Python" vs "User also uses JavaScript" (additive, not contradictory)
- "Working on study tracker" vs "Added auth to study tracker" (same project, progression)

CONTRADICTIONS:
- "Uses PostgreSQL" vs "Migrated to MySQL" (one replaces the other)
- "Deadline is March 15" vs "Deadline pushed to April 1" (superseded)

EXISTING MEMORIES:
{index}

JSON array of IDs only. [] if none."""}]
    )

    raw = resp.choices[0].message.content.strip()
    try:
        old_ids = json.loads(raw)
        if not isinstance(old_ids, list):
            return []
    except json.JSONDecodeError:
        return []

    if not old_ids:
        return []

    now = __import__("datetime").datetime.now().isoformat()
    with _db() as conn:
        conn.executemany("""
            UPDATE memories
            SET status = 'superseded', contradicted_by = ?
            WHERE id = ? AND status = 'active'
        """, [(new_id, oid) for oid in old_ids])
        conn.commit()

    for oid in old_ids:
        log_event(oid, "superseded", f"by #{new_id}: {new_content[:100]}")

    return old_ids
```

But the `contradicted_by` deserves an extra mention. When a memory is superseded by a newer one, it is not simply deleted. Rather, a reference to the replacement is added to it, enabling you to backtrack to the original memory from the updated one when needed.

If you’re debugging why the AI said something weird, you can pull up the memory it used and trace its history through `memory_events`. Turns out, “why does the AI think this?” is a question you ask more often than you’d expect.

As for the 80-memory limit in the contradiction check, it is quite reasonable since you don’t necessarily need all of the memories available to find conflicts. Those memories that have the highest chances of contradicting the new memory are recent and highly important anyway, which is what the `ORDER BY` reflects.

## Confidence Scoring

Two memories can be about the same fact. In one case, the claim is explicitly made: “I use FastAPI, always have.” In another case, the other was inferred (“they seem to prefer async frameworks”). These shouldn’t be weighted equally.

Confidence scores are what help the system differentiate between what you said to it and what it figured out about you. It starts at assessment time, at the moment a memory is stored, with one small LLM call:

```python
# confidence.py
from openai import OpenAI
from memory_store import _db, log_event
from datetime import datetime

client = OpenAI()

def assess_confidence(content: str, user_msg: str, assistant_msg: str) -> float:
    """
    Synchronous LLM call in the write path. Adds ~200ms.
    Worth it for memories that'll influence responses for months.
    """
    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        temperature=0,
        messages=[{"role": "user", "content": f"""Rate confidence in this memory (0.0-1.0):

MEMORY: {content}

FROM THIS EXCHANGE:
User: {user_msg}
Assistant: {assistant_msg}

Scale:
1.0 = explicit, direct statement ("I use Python", "deadline is March 15")
0.7 = clearly implied but not stated outright
0.5 = reasonable inference, could be wrong
0.3 = weak inference — user might disagree
0.1 = speculation

Single float only."""}]
    )

    try:
        return max(0.0, min(1.0, float(resp.choices[0].message.content.strip())))
    except ValueError:
        return 0.5

def reinforce(memory_id: int, bump: float = 0.1):
    """
    Bump confidence when a later conversation confirms something the system already knew.

    TODO: I haven't wired up the detection that triggers this yet —
    figuring out "this new conversation confirms memory X" is harder than it sounds.
    The function works, the caller doesn't exist. Will update when I have something
    that doesn't produce too many false positives.
    """
    with _db() as conn:
        conn.execute("""
            UPDATE memories
            SET confidence    = MIN(1.0, confidence + ?),
                access_count  = access_count + 1,
                last_accessed = ?
            WHERE id = ?
        """, (bump, datetime.now().isoformat(), memory_id))
        conn.commit()
    log_event(memory_id, "reinforced", f"+{bump:.2f}")
```

The `reinforce` function is partially complete, and I’m being upfront about that.

The logic for detecting “this conversation confirms an existing memory” is genuinely hard to get right without producing false positives, and I’d rather ship honest, incomplete code than confident code that does the wrong thing quietly. It’s in there, it works, the trigger just doesn’t exist yet.

Confidence directly influences the retrieval sorting. A memory that’s rated at 8 importance but only 0.3 confidence ranks behind a memory with importance at 6 and confidence at 0.9.

This is exactly the idea. High confidence in a weaker memory beats low confidence in a strong-seeming one when the question is “what does the AI actually know?”

## Compression and Elevation

Nick’s consolidation agent looks for similarities across memories. But what I would like to do is be even more aggressive: find groups of memories that are basically repeating themselves in other conversations, and replace those with one better entry.

Not “what connects these?”; “can I replace these five with one?”

In other words, you’re not grouping memories, you’re rewriting them into a cleaner version of the truth.

After a few months of working with a personal assistant, you get quite a few duplicate memories. “User prefers short function names” from January. “User mentioned keeping code readable over clever” from February. “User asked to avoid one-liners in the refactor” from March.

This is the same preference. It should be put together into a single memory.

```python
# compression.py
import json
from openai import OpenAI
from memory_store import _db, log_event, store_memory
from datetime import datetime

client = OpenAI()

def run_compression_pass():
    """
    Full compression cycle: find clusters, merge each, archive originals.
    Runs weekly. Calls gpt-4o for synthesis so it's not cheap — don't
    trigger this on every session.
    """
    with _db() as conn:
        rows = conn.execute("""
            SELECT id, summary, confidence, access_count, importance
            FROM memories
            WHERE status = 'active'
            ORDER BY importance DESC, access_count DESC
            LIMIT 100
        """).fetchall()

    if len(rows) < 5:
        return

    index = "\n".join(
        f"[{r['id']}] (conf:{r['confidence']:.1f} hits:{r['access_count']}) {r['summary']}"
        for r in rows
    )

    # gpt-4o-mini for cluster identification — just grouping, not synthesising
    cluster_resp = client.chat.completions.create(
        model="gpt-4o-mini",
        temperature=0,
        messages=[{"role": "user", "content": f"""Review this memory index and identify groups that
could be merged into a single, more useful memory.

Merge candidates:
- Multiple memories about the same topic from different conversations
- Incremental updates that could be expressed as one current state
- Related preferences that form a clear pattern

Do NOT merge:
- Different topics that share a tag
- Memories where each individual detail matters separately

MEMORY INDEX:
{index}

JSON array of arrays. Example: [[3,7,12],[5,9]]
Return [] if nothing qualifies."""}]
    )

    try:
        clusters = json.loads(cluster_resp.choices[0].message.content.strip())
        clusters = [c for c in clusters if isinstance(c, list) and len(c) >= 2]
    except (json.JSONDecodeError, TypeError):
        return

    if not clusters:
        return

    row_map = {r["id"]: r for r in rows}
    for cluster_ids in clusters:
        valid = [mid for mid in cluster_ids if mid in row_map]
        if len(valid) >= 2:
            _compress(valid, row_map)

def _compress(memory_ids: list[int], row_map: dict):
    """Synthesise a cluster into one elevated memory, archive the rest."""
    with _db() as conn:
        ph = ",".join("?" * len(memory_ids))
        source_rows = conn.execute(
            f"SELECT id, content, importance, access_count FROM memories WHERE id IN ({ph})",
            memory_ids
        ).fetchall()

    if not source_rows:
        return

    bullets       = "\n".join(f"- {r['content']}" for r in source_rows)
    avg_importance = sum(r["importance"] for r in source_rows) / len(source_rows)
    peak_access    = max(r["access_count"] for r in source_rows)

    # gpt-4o for the actual merge — this is the step that decides
    # what survives, so use the better model
    synth_resp = client.chat.completions.create(
        model="gpt-4o",
        temperature=0,
        messages=[{"role": "user", "content": f"""Compress these related memories into one better memory.
Be specific. Keep all important details. Don't repeat yourself.

MEMORIES:
{bullets}

JSON: {{"content": "...", "summary": "max 120 chars", "tags": ["..."]}}"""}]
    )

    try:
        merged = json.loads(synth_resp.choices[0].message.content.strip())
    except json.JSONDecodeError:
        return  # synthesis failed, leave originals alone

    with _db() as conn:
        ph = ",".join("?" * len(memory_ids))
        conn.execute(
            f"UPDATE memories SET status='compressed' WHERE id IN ({ph})",
            memory_ids
        )
        cur = conn.execute("""
            INSERT INTO memories
                (content, summary, tags, importance, confidence,
                 access_count, decay_score, status, created_at)
            VALUES (?, ?, ?, ?, 0.85, ?, 1.0, 'active', ?)
        """, (
            merged["content"],
            merged.get("summary", merged["content"][:120]),
            json.dumps(merged.get("tags", [])),
            min(10.0, avg_importance * 1.2),
            peak_access,
            datetime.now().isoformat()
        ))
        conn.commit()
        new_id = cur.lastrowid

    for mid in memory_ids:
        log_event(mid, "compressed", f"merged into #{new_id}")

    print(f"[compression] {len(memory_ids)} memories collapsed into #{new_id}")
```

The cluster identification uses `gpt-4o-mini` since that’s all we’re doing at this point. The synthesis uses `gpt-4o` because that’s where actual information is being created from multiple sources.

Doing both with the cheap model to save a few cents felt like the wrong trade-off for something that runs once a week and makes permanent decisions.

The merged memory gets `confidence=0.85`. Definitely not 1.0, since compression remains a synthesis process, which may result in loss of nuance. But 0.85 recognizes the high signal strength in multiple converging conversations.

## Expiring Memories

Some things shouldn’t last forever by design. A deadline. A temporary blocker. “Waiting to hear back from Alice about the API spec.” That’s useful context today. In three weeks, it’s just noise.

```python
# expiry.py
import json
from openai import OpenAI
from memory_store import _db, log_event
from datetime import datetime

client = OpenAI()

def maybe_set_expiry(content: str, memory_id: int):
    """Check at write time whether this memory has a natural end date."""
    today = datetime.now().strftime("%Y-%m-%d")

    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        temperature=0,
        messages=[{"role": "user", "content": f"""Does this memory have a natural expiration?

MEMORY: "{content}"
TODAY: {today}

Expires if it contains:
- A deadline or specific due date
- A temporary state ("currently blocked on...", "waiting for...")
- A one-time event ("meeting Thursday", "presenting tomorrow")
- An explicit time bound ("this sprint", "until we ship v2")

If yes: {{"expires": true, "date": "YYYY-MM-DD"}}
If no:  {{"expires": false}}

JSON only."""}]
    )

    try:
        parsed = json.loads(resp.choices[0].message.content.strip())
    except json.JSONDecodeError:
        return

    if parsed.get("expires") and parsed.get("date"):
        with _db() as conn:
            conn.execute(
                "UPDATE memories SET expires_at=? WHERE id=?",
                (parsed["date"], memory_id)
            )
            conn.commit()

def purge_expired():
    """Archive anything past its expiry date. Safe to call daily."""
    now = datetime.now().isoformat()

    with _db() as conn:
        expired = [
            r["id"] for r in conn.execute("""
                SELECT id FROM memories
                WHERE expires_at IS NOT NULL
                  AND expires_at < ?
                  AND status = 'active'
            """, (now,)).fetchall()
        ]
        if expired:
            conn.executemany(
                "UPDATE memories SET status='expired' WHERE id=?",
                [(mid,) for mid in expired]
            )
            conn.commit()

    # Log events after closing the write connection.
    # log_event opens its own connection — nesting them on the same
    # SQLite file can deadlock in default journal mode.
    for mid in expired:
        log_event(mid, "expired", "past expiry date")

    if expired:
        print(f"Expired {len(expired)} memories.")
```

The `reason` field that was in an earlier version of this got cut. It was satisfying to model, but nothing ever read it. Unused columns in SQLite are still columns you have to remember exist. The date string is enough.

## Wiring It Together

![](https://contributor.insightmediagroup.io/wp-content/uploads/2026/04/mermaid-diagram-2026-04-07-174748-966x1024.png)

The complete architecture separating the fast, synchronous write path from the asynchronous background lifecycle scheduler. Image by author.

All five passes need a scheduler. Here’s the coordinator, with threading done properly:

```python
# lifecycle.py
import time
import threading
from datetime import datetime, timedelta
from decay import run_decay_pass
from expiry import purge_expired
from compression import run_compression_pass

class LifecycleScheduler:
    """
    Background maintenance for the memory store.
    Decay + expiry run daily. Compression runs weekly (calls gpt-4o).

    Usage:
        scheduler = LifecycleScheduler()
        scheduler.start()      # once at startup
        scheduler.force_run()  # for testing
        scheduler.stop()       # clean shutdown
    """

    def __init__(self, decay_interval_h: int = 23, compression_interval_days: int = 6):
        self._decay_interval    = timedelta(hours=decay_interval_h)
        self._compress_interval = timedelta(days=compression_interval_days)
        self._last_decay        = None
        self._last_compression  = None
        self._stop_event        = threading.Event()
        self._thread            = None

    def start(self):
        if self._thread and self._thread.is_alive():
            return
        self._stop_event.clear()
        self._thread = threading.Thread(target=self._loop, daemon=True)
        self._thread.start()

    def stop(self):
        self._stop_event.set()

    def force_run(self):
        self._run(force=True)

    def _loop(self):
        while not self._stop_event.is_set():
            self._run()
            # Sleep in short increments so stop() is actually responsive.
            # threading.Event().wait() in a loop creates a new Event every
            # iteration that's never set — looks right, blocks correctly,
            # but stop() never actually wakes it up.
            for _ in range(60):
                if self._stop_event.is_set():
                    break
                time.sleep(60)

    def _run(self, force: bool = False):
        now = datetime.now()
        print(f"[lifecycle] {now.strftime('%H:%M:%S')}")

        purge_expired()

        if force or not self._last_decay or (now - self._last_decay) >= self._decay_interval:
            run_decay_pass()
            self._last_decay = now

        if force or not self._last_compression or (now - self._last_compression) >= self._compress_interval:
            run_compression_pass()
            self._last_compression = now

        print("[lifecycle] done.")
```

And the write path, where contradiction detection, confidence scoring, and expiry all get triggered every time a memory is stored:

```python
# memory_writer.py
import json
from openai import OpenAI
from memory_store import store_memory
from confidence import assess_confidence
from contradiction import check_for_contradictions
from expiry import maybe_set_expiry

client = OpenAI()

def maybe_store(user_msg: str, assistant_msg: str) -> int | None:
    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        temperature=0,
        messages=[{"role": "user", "content": f"""Should this conversation turn be saved to long-term memory?

USER: {user_msg}
ASSISTANT: {assistant_msg}

Save if it contains:
- user preferences or personal context
- project decisions, trade-offs made
- bugs found, fixes applied, approaches ruled out
- explicit instructions ("always...", "never...", "I prefer...")

Don't save: greetings, one-off lookups, generic back-and-forth.

If yes: {{"save": true, "content": "...", "summary": "max 100 chars", "tags": [...], "importance": 1-10}}
If no:  {{"save": false}}
JSON only."""}]
    )

    try:
        decision = json.loads(resp.choices[0].message.content.strip())
    except json.JSONDecodeError:
        return None

    if not decision.get("save"):
        return None

    confidence = assess_confidence(decision["content"], user_msg, assistant_msg)

    mid = store_memory(
        content    = decision["content"],
        summary    = decision.get("summary"),
        tags       = decision.get("tags", []),
        importance = decision.get("importance", 5),
        confidence = confidence
    )

    superseded = check_for_contradictions(decision["content"], mid)
    if superseded:
        print(f"[memory] #{mid} superseded {superseded}")

    maybe_set_expiry(decision["content"], mid)

    return mid
```

## What Retrieval Looks Like Now

With the lifecycle running, the memory index the LLM reads on every query carries an actual signal about each memory’s health:

```python
# retrieval.py
import json
from datetime import datetime
from openai import OpenAI
from memory_store import _db

client = OpenAI()

def get_active_memories(limit: int = 60) -> list[dict]:
    with _db() as conn:
        rows = conn.execute("""
            SELECT id, content, summary, tags, importance,
                   confidence, decay_score, access_count, created_at
            FROM memories
            WHERE status = 'active'
              AND decay_score > 0.15
            ORDER BY (importance * confidence * decay_score) DESC
            LIMIT ?
        """, (limit,)).fetchall()
    return [dict(r) for r in rows]

def retrieve_relevant_memories(query: str, top_n: int = 6) -> list[dict]:
    memories = get_active_memories()
    if not memories:
        return []

    index = "\n".join(
        f"[{m['id']}] (conf:{m['confidence']:.1f} fresh:{m['decay_score']:.1f}) {m['summary']}"
        for m in memories
    )

    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        temperature=0,
        messages=[{"role": "user", "content": f"""Pick the most relevant memories for this message.

MEMORY INDEX (conf=confidence 0-1, fresh=recency 0-1):
{index}

MESSAGE: {query}

Prefer high-conf, high-fresh memories when relevance is otherwise equal.
JSON array of IDs, max {top_n}. Return [] if nothing fits."""}]
    )

    raw = resp.choices[0].message.content.strip()
    try:
        ids = json.loads(raw)
        if not isinstance(ids, list):
            return []
    except json.JSONDecodeError:
        return []

    mem_by_id = {m["id"]: m for m in memories}
    selected  = []
    now       = datetime.now().isoformat()

    with _db() as conn:
        for mid in ids:
            if mid not in mem_by_id:
                continue
            conn.execute("""
                UPDATE memories
                SET access_count = access_count + 1, last_accessed = ?
                WHERE id = ?
            """, (now, mid))
            selected.append(mem_by_id[mid])
        conn.commit()

    return selected
```

The sort order in `get_active_memories` is `importance * confidence * decay_score`. That composite score is where all five lifecycle concepts converge into one number. A memory that’s important but poorly supported surfaces below one that’s moderately important and consistently reinforced. One that hasn’t been touched in three months competes poorly against a recent one, regardless of its original score.

This is what the state of health of the information looks like. And that’s exactly what we want!

## Is This Overkill?

No. But I thought it was, for longer than I’d like to admit.

I kept telling myself I’d add this stuff “later, when the system got bigger.” But that’s not true. It’s not about how large the system is; it’s about how long it’s been around. Just three months of everyday usage is more than enough.

In my case, I found myself manually battling decay by the second month, opening up the SQLite file via the DB Browser, manually deleting rows, and manually updating the importance scores.

And that’s precisely what you should never do: if you’re manually cleaning the system, the system isn’t really working.

The overhead is real, but it’s small. Decay and expiry are pure SQLite, milliseconds. Contradiction detection adds one `gpt-4o-mini` call per write, maybe 200ms. Compression calls `gpt-4o` but runs once a week on a handful of clusters.

Overall, the cost for a daily personal assistant is a few extra mini calls per conversation and a weekly synthesis job that probably costs less than a cup of coffee per month.

Well, it depends on your intention. If you are building a system you are going to use for two weeks and then put to some other use, forget about everything below. Store-and-retrieve is enough. But if you are working on something you intend to get to know you, which is what is intriguing here, what we are talking about is non-negotiable.

---

## Where This Actually Leaves You

Nick Lawson showed that the embedding pipeline can be optional at a personal scale. This opened up the possibility of a simpler architecture. What this article provides is the operational framework that makes that architecture work beyond the first month.

There are other possible concepts for the design of the memory lifecycle; decay, contradiction, confidence, compression, and expiry are not the only options, but these are the ones that I kept wishing I had for debugging my own database.

And because each of these relies on the same SQLite data structure and LLM judgment-based framework that Nick introduced, you are still zero infrastructure. You only need one local file. You can read it all. You can trace the events of your entire memory lifecycle in `memory_events`.

You can open the database and ask: why does the agent think this? What got superseded? What decayed? What got merged into what? The system’s reasoning is transparent in a way that a vector index never is.

That matters more than I expected it to. Not just for debugging. For trust. An AI assistant you can audit is one you’ll trust. Trust is what turns a tool into something you actually rely on.

And that only happens when your system knows not just how to remember, but when to forget.

---

## Before you go!

I’m building a community for developers and data scientists where I share practical tutorials, break down complex CS concepts, and drop the occasional rant about the tech industry.

If that sounds like your kind of space, **join my free [newsletter](https://thatcsguy.substack.com/?r=79lldp&utm_campaign=pub-share-checklist).**