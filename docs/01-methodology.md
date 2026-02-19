[01-methodology.md](https://github.com/user-attachments/files/25425788/01-methodology.md)
# Methodology

## Overview

This document describes how the forensic audit was conducted, the classification framework used, and the evidence standards applied. The goal was to evaluate every task assigned to the AI agent with the same rigor you'd apply to a financial audit — no assumptions, no benefit of the doubt, evidence-based classification only.

## Data Sources

### Primary Source: Chat Export
- **Platform**: Telegram (bot integration via open-source agent framework)
- **Duration**: 13 days
- **Total Messages**: 2,131
- **Participants**: The developer (human) and the Agent (AI assistant)
- **Format**: JSON export containing message ID, timestamp, sender, and full text

### Secondary Source: Task Ledger
A consolidated spreadsheet cataloguing every discrete task extracted from the chat history, containing:
- Task ID and batch grouping
- Date and task type (EXPLICIT, PROPOSED, STANDING, CONDITIONAL, IMPLIED)
- Description of the task
- What the developer said (verbatim or summarized)
- What the Agent said (verbatim or summarized)
- Whether the Agent claimed completion (YES, NO, PARTIAL)
- Message IDs for cross-referencing
- Completion claim message IDs
- Photo references (screenshots sent during the conversation)
- Related tasks and repetition flags

## Audit Process

### Step 1: Full Data Ingestion
Both the chat export (2,131 messages) and the task ledger (283 tasks) were loaded into a structured analysis environment. A message lookup index was built mapping message IDs to full message content for rapid cross-referencing.

### Step 2: Batch Processing
The chat history was split into 5 batches by date to manage context window limits:

| Batch | Period | Messages | Focus |
|-------|--------|----------|-------|
| 1 | Days 1–3 | 852 | Setup & Configuration |
| 2 | Days 3–5 | 327 | Integration & Research |
| 3 | Days 5–7 | 267 | Architecture & Planning |
| 4 | Days 7–10 | 482 | Hardware Build & Migration |
| 5 | Days 10–13 | 203 | Post-Migration Crisis |

Each batch was independently analyzed for task extraction, then results were consolidated into a single master ledger.

### Step 3: Task-by-Task Review
Every single task was reviewed individually. For each task:

1. **Read the ledger entry** — understand what was asked and what was claimed
2. **Cross-reference original messages** — verify the ledger summary against raw chat data using message IDs
3. **Evaluate the completion claim** — did the Agent actually show evidence of completion?
4. **Apply the red flag checklist** — check for the 7 hallucination indicators
5. **Assign classification** — based on evidence, not assumption

### Step 4: Pattern Identification
After classifying all 283 tasks, patterns were identified across hallucinated and likely-hallucinated tasks. These patterns were validated by checking whether they appeared consistently across different task types, dates, and contexts.

### Step 5: Physical Verification
A verification script was run on the actual hardware to confirm or deny specific claims. This converted UNKNOWN classifications to CONFIRMED or HALLUCINATED based on ground truth evidence (file existence, installed tools, running services, cron jobs).

### Step 6: Verification Checklist Generation
For remaining unverified tasks, specific shell commands were generated — exact commands the developer can run on the physical hardware to determine ground truth.

## Classification Framework

### Status Categories

| Status | Definition | Evidence Required |
|---|---|---|
| **CONFIRMED** | Task was actually completed | Developer verified, system output shown, or task is purely conversational/advisory |
| **HALLUCINATED** | Clear evidence the task was NOT done despite claims | Proven false by physical verification, logical impossibility, or developer inspection |
| **LIKELY_HALLUCINATED** | No direct proof of fabrication, but matches hallucination patterns | 3+ red flags triggered, no artifacts shown, fits known patterns |
| **UNKNOWN** | Cannot determine from chat evidence alone | Needs physical verification on the machine |
| **ABANDONED** | Developer explicitly moved on or cancelled | Clear pivot in conversation |
| **NOT_APPLICABLE** | Not actually a task | Conversational, informational, or context-only exchange |

### Confidence Levels

| Level | Meaning |
|---|---|
| **HIGH** | Clear evidence — developer confirmed, or multiple red flags present |
| **MEDIUM** | Pattern-matching suggests this status but no definitive proof |
| **LOW** | Best guess based on limited evidence |

### The Red Flag Checklist

Applied to every task where the Agent claimed completion:

1. **Suspiciously Specific Numbers** — Exact metrics delivered without raw output
2. **Narrative Completion** — Prose description of success without terminal output or file contents
3. **No Artifacts** — Claims of file/code creation with nothing shown
4. **Impossible Speed** — Multi-step tasks "completed" in a single response
5. **Claims of Inaccessible Resources** — Tasks requiring access the agent doesn't have
6. **Instant Recovery** — Smooth "fix" immediately after being caught
7. **Echoed Instructions** — Completion report mirrors the request in past tense

### Task Type Taxonomy

| Type | Definition |
|---|---|
| **EXPLICIT** | Developer directly told the Agent to do something |
| **IMPLIED** | Conversational statement carrying an implicit instruction |
| **PROPOSED** | Agent suggested a plan, developer approved |
| **CONDITIONAL** | Task dependent on external conditions being met |
| **STANDING** | Ongoing instruction applying to all future interactions |

## Meta-Methodology Note

This audit was conducted using a frontier AI model (different from the one being audited) as the primary analysis tool. This creates an inherent irony: using one AI to audit another AI's fabrications. The approach was designed to mitigate this risk:

- The auditing model had access to raw chat data, not just summaries
- All classifications were based on evidence in the chat export, not the auditor's assumptions
- Physical verification was used to ground-truth the auditor's classifications
- The auditor was explicitly instructed to default to skepticism

The audit methodology is reproducible. Anyone with a chat export from an AI agent can follow these steps to evaluate their own agent's reliability.

## Reproducing This Audit

1. Export your full chat history with the AI agent (JSON format preferred)
2. Use a frontier model to extract all tasks using the taxonomy above
3. For each task, cross-reference the agent's claims against the original messages
4. Apply the 7-point red flag checklist
5. Classify each task using the status categories
6. Run physical verification commands on the actual system
7. Compile patterns and generate recommendations

The prompts used for each phase are available on request.
