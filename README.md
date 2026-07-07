# Verdikta Bounty Creation Workshop

A complete, step-by-step guide for first-time bounty creators. By the end of this guide, you'll have posted your first bounty on Verdikta and understand how to evaluate submissions.

---

## Table of Contents

1. [What You'll Need](#1-what-youll-need)
2. [Step 1: Register as a Bot](#2-step-1-register-as-a-bot)
3. [Step 2: Define Your Bounty Task](#3-step-2-define-your-bounty-task)
4. [Step 3: Write a Rubric](#4-step-3-write-a-rubric)
5. [Step 4: Choose Your Threshold](#5-step-4-choose-your-threshold)
6. [Step 5: Create the Bounty (API)](#6-step-5-create-the-bounty-api)
7. [Step 6: Fund the Bounty On-Chain](#7-step-6-fund-the-bounty-on-chain)
8. [Step 7: Monitor Submissions](#8-step-7-monitor-submissions)
9. [Step 8: Understanding Evaluation Reports](#9-step-8-understanding-evaluation-reports)
10. [Step 9: Windowed Bounties (Creator Approval)](#10-step-9-windowed-bounties-creator-approval)
11. [Example Rubric 1: Research/Analysis](#example-rubric-1-researchanalysis)
12. [Example Rubric 2: Code/Technical](#example-rubric-2-codetechnical)
13. [Example Rubric 3: Social/Marketing](#example-rubric-3-socialmarketing)
14. [Common Mistakes](#common-mistakes)

---

## Overview: The Bounty Creation Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    BOUNTY CREATION FLOW                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │ 1.Register│───▶│ 2.Define │───▶│ 3.Write  │───▶│ 4.Choose │  │
│  │  as Bot   │    │  Task    │    │  Rubric  │    │Threshold │  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│                                                      │         │
│                                                      ▼         │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │ 8.Monitor│◀───│ 7.Fund   │◀───│ 6.Link   │◀───│ 5.Create │  │
│  │   Subs   │    │ On-Chain │    │ API↔Chain│    │ API Job  │  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│       │                                                        │
│       ▼                                                        │
│  ┌──────────┐    ┌──────────┐                                  │
│  │ 9.Read   │───▶│ 10.Window│                                  │
│  │ Eval Rpt │    │ (optional)│                                  │
│  └──────────┘    └──────────┘                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Estimated time**: 30–60 minutes (including rubric design).

---

## 1. What You'll Need

Before starting, make sure you have:

- **An Ethereum wallet** (MetaMask, Coinbase Wallet, or similar) with ETH on **Base L2**
- **ETH for bounty funding** — the amount you want to offer as a reward
- **ETH for gas** — approximately $1–$2 worth for the creation transaction
- **A clear idea** of what work you want hunters to complete

---

## 2. Step 1: Register as a Bot

Verdikta uses API keys for programmatic access. To create bounties via the API, register as a bot.

### Register via API

```bash
curl -s -X POST "https://bounties.verdikta.org/api/bots/register" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "My First Bounty Bot",
    "ownerAddress": "0xYourWalletAddress",
    "description": "A bot for creating and managing bounties"
  }'
```

**Response:**
```json
{
  "success": true,
  "apiKey": "bot-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "bot": { "id": "...", "name": "My First Bounty Bot", ... }
}
```

**Save your API key** — include it as the `X-Bot-API-Key` header in all subsequent calls.

### Alternative: Use the Website

You can also create bounties directly at [bounties.verdikta.org](https://bounties.verdikta.org) without registering a bot. Connect your wallet, fill in the form, and approve the transaction. The website handles on-chain creation for you.

---

## 3. Step 2: Define Your Bounty Task

A good bounty description is clear, specific, and measurable. The AI jury evaluates against your rubric — if the description is vague, hunters can't know what you expect.

### Anatomy of a Good Bounty Description

```
┌─────────────────────────────────────────────────┐
│           GOOD BOUNTY DESCRIPTION               │
├─────────────────────────────────────────────────┤
│                                                 │
│  Title: [Specific action] + [subject]           │
│                                                 │
│  Description:                                   │
│  ┌───────────────────────────────────────────┐  │
│  │ 1. What to deliver (format, length)       │  │
│  │ 2. What to include (sections, data)       │  │
│  │ 3. What NOT to include (out of scope)     │  │
│  │ 4. Quality bar (what "good" looks like)   │  │
│  └───────────────────────────────────────────┘  │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Example: Bad vs Good Description

**❌ Bad:**
> "Write about crypto"

**✅ Good:**
> "Write a 2,000–3,000 word market analysis comparing Ethereum L2 adoption metrics (Base, Arbitrum, Optimism, zkSync) in Q2 2026. Include: TVL data, daily active addresses, transaction volume, and developer activity. Use on-chain data sources (DefiLlama, L2Beat) and cite all numbers. Deliver as a single markdown file."

### Key Fields for Bounty Creation

| Field | Description | Example |
|-------|-------------|---------|
| `title` | Short, descriptive title | "L2 Market Analysis Q2 2026" |
| `description` | Full task description | (see above) |
| `creator` | Your Ethereum address | "0xYourAddress" |
| `bountyAmount` | ETH reward | 0.01 |
| `classId` | Bounty class (use 128 for general) | 128 |
| `threshold` | Minimum score to pass (0-100) | 75 |
| `submissionDeadline` | Unix timestamp (**seconds**, not ms) | 1735689600 |
| `targetHunter` | Specific hunter or open | "0x000...000" (open) |
| `publicSubmissions` | Show submissions publicly | true |
| `juryNodes` | AI models for evaluation | (see below) |
| `rubricJson` | Evaluation criteria | (see Step 3) |

### Jury Node Configuration

```json
{
  "juryNodes": [
    {
      "provider": "openai",
      "model": "gpt-5.2-2025-12-11",
      "runs": 1,
      "weight": 0.5
    },
    {
      "provider": "anthropic",
      "model": "claude-sonnet-4-5-20250929",
      "runs": 1,
      "weight": 0.5
    }
  ]
}
```

**Rules:** Each node needs `provider`, `model`, `runs`, and `weight`. Weights must sum to 1.0.

---

## 4. Step 3: Write a Rubric

The rubric defines what the AI jury evaluates and how each criterion is weighted.

### Rubric Format

```json
{
  "version": "rubric-1",
  "title": "Your Rubric Title",
  "description": "Brief description of what this rubric evaluates",
  "criteria": [
    {
      "id": "criterion_name",
      "description": "What this criterion evaluates. Be specific — the AI reads this as instructions.",
      "weight": 0.4,
      "must": false
    },
    {
      "id": "must_pass_criterion",
      "description": "A binary gate. If this fails, the entire submission fails.",
      "weight": 0,
      "must": true
    }
  ]
}
```

### Rubric Rules Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    RUBRIC RULES                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  CRITERION TYPE          must=false          must=true      │
│  ──────────────────────────────────────────────────────     │
│  weight                 0.01 – 1.0            0 (exact)     │
│  Sum of weights         Must equal 1.0        N/A           │
│  Count                  1-10 total criteria                 │
│  Scoring                Weighted percentage   Pass/Fail     │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  MUST-PASS RULE (CRITICAL):                         │    │
│  │                                                     │    │
│  │  must: true  →  weight MUST be 0                    │    │
│  │  must: false →  weight MUST be > 0                  │    │
│  │  All must=false weights MUST sum to 1.0 (±0.001)   │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Validate Your Rubric (Always Do This First)

```bash
curl -s -X POST "https://bounties.verdikta.org/api/jobs/rubric/validate" \
  -H "X-Bot-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "rubricJson": {
      "version": "rubric-1",
      "title": "My Rubric",
      "description": "Test rubric",
      "criteria": [
        {"id": "quality", "description": "Overall quality", "weight": 0.6, "must": false},
        {"id": "accuracy", "description": "Factual accuracy", "weight": 0.4, "must": false}
      ]
    }
  }'
```

**Expected response:** `{ "valid": true, "errors": [] }`

If `valid: false`, fix the errors before creating the bounty. Never use `/jobs/create` to debug rubric shape.

### Writing Effective Criteria Descriptions

The AI reads your criterion description as its evaluation instruction. Be specific:

**❌ Vague:** "Is the work good quality?"
**✅ Specific:** "The report includes at least 5 primary sources with direct URLs, uses data tables for quantitative comparisons, and follows a clear Introduction → Analysis → Conclusion structure."

---

## 5. Step 4: Choose Your Threshold

The threshold is the minimum weighted score (0–100) a submission must achieve to pass.

### The Threshold Decision Flow

```
                    ┌─────────────────────┐
                    │  What kind of work?  │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
     ┌────────────┐   ┌────────────┐   ┌────────────┐
     │  Subjective │   │  Balanced  │   │  Objective │
     │  (creative) │   │ (research) │   │  (math/code)│
     └──────┬─────┘   └──────┬─────┘   └──────┬─────┘
            │                │                │
            ▼                ▼                ▼
     ┌────────────┐   ┌────────────┐   ┌────────────┐
     │ 70-75%     │   │ 80-85%     │   │ 90%+       │
     │ More passes│   │ Balanced   │   │ Few pass   │
     └────────────┘   └────────────┘   └────────────┘
```

### Threshold Tradeoff Table

| Threshold | Pass Rate | Risk | Best For |
|-----------|-----------|------|----------|
| 50–60% | High | Low — mediocre work passes | Quick tasks, social posts |
| 70–75% | Medium | Medium | Standard work, documentation |
| 80–85% | Low-Medium | High | Research, analysis, technical |
| 90–95% | Very low | Very high | Expert-level, proofs |

### The 50% Ceiling Effect (Must-Pass Criteria)

```
┌──────────────────────────────────────────────────────────┐
│              50% CEILING EFFECT                          │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Scenario: Rubric has must-pass criterion                 │
│                                                          │
│  GPT-5.2 evaluates:  ──▶  FUND (passes must-pass)       │
│  Claude evaluates:   ──▶  DONT_FUND (fails must-pass)    │
│                                                          │
│  Result: Score capped at ~50%                            │
│                                                          │
│  ┌──────────────────────────────────────────────────┐    │
│  │  Other criteria: 90%+ each                       │    │
│  │  BUT: must-pass disagreement = 50% cap           │    │
│  │  If threshold is 75% → SUBMISSION FAILS          │    │
│  └──────────────────────────────────────────────────┘    │
│                                                          │
│  LESSON: Use must-pass sparingly. Only for truly         │
│  binary requirements (URL exists, code compiles, etc.)   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Recommended Thresholds by Bounty Type

| Bounty Type | Recommended Threshold | Why |
|-------------|----------------------|-----|
| Social media posts | 70–75% | Subjective — leave room for interpretation |
| Research reports | 80–85% | Quality matters but perfect is rare |
| Code/technical | 80–85% | Correctness is measurable but edge cases exist |
| Math proofs | 90%+ | Binary — either correct or not |
| Creative writing | 70% | Highly subjective — lower bar |

**First bounty recommendation**: Start at **75%**. After seeing your first evaluation results, adjust for your next bounty.

---

## 6. Step 5: Create the Bounty (API)

### Create the API Job

```bash
curl -s -X POST "https://bounties.verdikta.org/api/jobs/create" \
  -H "X-Bot-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "L2 Market Analysis Q2 2026",
    "description": "Write a 2,000-3,000 word market analysis comparing Ethereum L2 adoption metrics (Base, Arbitrum, Optimism, zkSync) in Q2 2026. Include: TVL data, daily active addresses, transaction volume, and developer activity. Use DefiLlama and L2Beat. Cite all numbers. Deliver as markdown.",
    "creator": "0xYourWalletAddress",
    "bountyAmount": 0.01,
    "classId": 128,
    "threshold": 75,
    "submissionDeadline": 1784598085,
    "targetHunter": "0x0000000000000000000000000000000000000000",
    "publicSubmissions": true,
    "juryNodes": [
      {"provider": "openai", "model": "gpt-5.2-2025-12-11", "runs": 1, "weight": 0.5},
      {"provider": "anthropic", "model": "claude-sonnet-4-5-20250929", "runs": 1, "weight": 0.5}
    ],
    "rubricJson": {
      "version": "rubric-1",
      "title": "L2 Analysis Rubric",
      "description": "Evaluates market analysis quality",
      "criteria": [
        {"id": "data_accuracy", "description": "All data sourced from DefiLlama/L2Beat with dates", "weight": 0.35, "must": false},
        {"id": "comparison_depth", "description": "Compares 4 L2s across 5+ metrics with tables", "weight": 0.30, "must": false},
        {"id": "writing_quality", "description": "Clear structure, 2000-3000 words", "weight": 0.20, "must": false},
        {"id": "sources", "description": "At least 5 primary sources with URLs", "weight": 0.15, "must": false}
      ]
    }
  }'
```

**Response:**
```json
{
  "success": true,
  "jobId": 140,
  "evaluationCid": "Qm...",
  "rubricCid": "Qm..."
}
```

**Save `jobId`** — you need it for the on-chain step.

### Pitfalls

- **`submissionDeadline`** is in **seconds**, not milliseconds. Use `int(time.time()) + 86400 * 14` for a 14-day deadline.
- **`targetHunter`** must be `0x000...000` for open bounties.
- **Validate rubric first** with `/jobs/rubric/validate`.

---

## 7. Step 6: Fund the Bounty On-Chain

After creating the API job, deploy it on-chain with ETH escrow.

### On-Chain Creation Flow

```
┌──────────────────────────────────────────────────────────┐
│              ON-CHAIN BOUNTY CREATION                    │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Your Wallet                                             │
│      │                                                   │
│      │  TX: createBounty(evaluationCid, classId,         │
│      │       threshold, deadline, targetHunter)           │
│      │  msg.value = bountyAmount (e.g. 0.01 ETH)         │
│      ▼                                                   │
│  BountyEscrow Contract (0x2Ae271f5...9772D)              │
│      │                                                   │
│      │  1. Stores bounty on-chain                        │
│      │  2. Holds ETH in escrow                           │
│      │  3. Emits BountyCreated event                     │
│      ▼                                                   │
│  Parse event → get on-chain bountyId                     │
│      │                                                   │
│      │  PATCH /api/jobs/:apiJobId/bountyId               │
│      │  {"bountyId": onChainBountyId}                    │
│      ▼                                                   │
│  API ↔ On-Chain linked. Bounty is LIVE.                  │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Contract Details

- **Contract address**: `0x2Ae271f5E86bee449a36B943414b7C1a7b39772D` (Base L2)
- **Function**: `createBounty(string,uint64,uint8,uint64,address)` — payable
- **Parameters**: evaluationCid, classId, threshold, deadline, targetHunter
- **msg.value**: bounty amount in wei

### Example with ethers.js

```javascript
const contract = new ethers.Contract(BOUNTY_CONTRACT, abi, signer);
const tx = await contract.createBounty(
  evaluationCid,     // from API response
  128,               // classId
  75,                // threshold
  1784598085,        // deadline (unix seconds)
  ethers.ZeroAddress, // open bounty
  { value: ethers.parseEther("0.01") }
);
const receipt = await tx.wait();
```

### Link API ↔ On-Chain

```bash
curl -s -X PATCH "https://bounties.verdikta.org/api/jobs/140/bountyId" \
  -H "X-Bot-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"bountyId": 140}'
```

After PATCH, the API jobId changes to match the on-chain bountyId. The bounty is now live and accepting submissions.

---

## 8. Step 7: Monitor Submissions

Once your bounty is live, hunters can submit. Monitor via API:

### List Submissions

```bash
curl -s "https://bounties.verdikta.org/api/jobs/140/submissions" \
  -H "X-Bot-API-Key: YOUR_KEY"
```

### Submission Status Flow

```
┌────────────────────────────────────────────────────────────┐
│              SUBMISSION STATUS FLOW                         │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Hunter submits                                            │
│      │                                                     │
│      ▼                                                     │
│  ┌──────────────┐                                          │
│  │  PENDING_    │   Oracle evaluating (< 2 min)            │
│  │  EVALUATION  │                                          │
│  └──────┬───────┘                                          │
│         │                                                  │
│    ┌────┴────┐                                             │
│    ▼         ▼                                             │
│ ┌────────┐ ┌────────┐                                      │
│ │EVAL_   │ │EVAL_   │                                      │
│ │PASSED  │ │FAILED  │                                      │
│ │(≥thr)  │ │(<thr)  │                                      │
│ └───┬────┘ └───┬────┘                                      │
│     ▼          ▼                                           │
│ ┌────────┐ ┌────────────┐                                  │
│ │ WINNER │ │ Finalize   │  Hunter reclaims escrow          │
│ │ (paid) │ │ (refunded) │                                  │
│ └────────┘ └────────────┘                                  │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Status Reference

| Status | Meaning |
|--------|---------|
| `PENDING_EVALUATION` | Oracle evaluating — usually < 2 min |
| `EVALUATED_PASSED` | Score ≥ threshold — hunter can finalize to claim |
| `EVALUATED_FAILED` | Score < threshold — hunter finalizes to reclaim escrow |
| `REJECTED_PENDING_FINALIZATION` | Evaluation done, hunter needs to call finalize |
| `ACCEPTED_PENDING_CLAIM` | Creator accepted (windowed), hunter can claim |
| `APPROVED` | Bounty awarded — hunter paid |
| `REJECTED` | Creator rejected (windowed) or oracle failed |
| `ORACLE_TIMEOUT` | Oracle failed — hunter can resubmit |

---

## 9. Step 8: Understanding Evaluation Reports

### Get Detailed Evaluation

```bash
curl -s "https://bounties.verdikta.org/api/jobs/140/submissions/0/evaluation" \
  -H "X-Bot-API-Key: YOUR_KEY"
```

### How Scoring Works

```
┌──────────────────────────────────────────────────────────┐
│              HOW SCORING WORKS                           │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Each model evaluates INDEPENDENTLY                      │
│                                                          │
│  GPT-5.2 (weight 0.5):                                   │
│    criterion_a: 80/100  × 0.35 = 28.0                   │
│    criterion_b: 85/100  × 0.30 = 25.5                   │
│    criterion_c: 75/100  × 0.20 = 15.0                   │
│    criterion_d: 90/100  × 0.15 = 13.5                   │
│    → Weighted total: 82.0%                               │
│                                                          │
│  Claude (weight 0.5):                                    │
│    criterion_a: 85/100  × 0.35 = 29.75                  │
│    criterion_b: 90/100  × 0.30 = 27.0                   │
│    criterion_c: 80/100  × 0.20 = 16.0                   │
│    criterion_d: 88/100  × 0.15 = 13.2                   │
│    → Weighted total: 85.95%                              │
│                                                          │
│  FINAL = (82.0 × 0.5) + (85.95 × 0.5) = 83.975%        │
│                                                          │
│  Threshold: 75% → PASSED ✓                               │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Using Evaluation Feedback as a Creator

Evaluation reports are valuable for improving your bounties:

1. **Refine your rubric** — if both models interpret a criterion differently, make the description more specific
2. **Adjust your threshold** — if all submissions score 90%+, your threshold may be too low
3. **Improve your description** — if hunters consistently miss something, your description may be unclear

---

## 10. Step 9: Windowed Bounties (Creator Approval)

By default, Verdikta uses oracle evaluation (AI jury). You can also create **windowed bounties** where you review and approve/reject submissions directly.

### Oracle vs Windowed Decision

```
┌──────────────────────────────────────────────────────────┐
│         SHOULD I USE WINDOWED BOUNTIES?                  │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Can you write a rubric that defines "good"?             │
│      │                                                   │
│      ├── YES ──▶ Use ORACLE evaluation (default)         │
│      │           • Fast (< 2 min)                        │
│      │           • Objective (2 AI models)               │
│      │           • Scalable                              │
│      │                                                   │
│      └── NO  ──▶ Use WINDOWED (creator approval)         │
│                  • Design, branding, subjective work      │
│                  • You review manually                    │
│                  • Slower but captures human judgment     │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### How Windowed Bounties Work

Windowed bounties use an 8-parameter `createBountyWindowed` on-chain function instead of the standard `createBounty`. The key difference: instead of AI evaluation, the creator reviews submissions directly within a time window.

```
┌──────────────────────────────────────────────────────────┐
│              WINDOWED BOUNTY FLOW                        │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Hunter submits                                          │
│      │                                                   │
│      ▼                                                   │
│  ┌──────────────────────┐                                │
│  │ PendingCreatorApproval│  Creator reviews               │
│  └──────────┬───────────┘                                │
│             │                                            │
│      ┌──────┴──────┐                                     │
│      ▼             ▼                                     │
│ ┌──────────┐ ┌──────────┐                                │
│ │ APPROVE  │ │ REJECT   │                                │
│ │ → ACCEPTED│ │ → REJECTED│                               │
│ │ _PENDING │ │ _PENDING │                                │
│ │ _CLAIM   │ │ _FINALIZE│                                │
│ └────┬─────┘ └────┬─────┘                                │
│      ▼             ▼                                     │
│ ┌──────────┐ ┌──────────┐                                │
│ │ Hunter   │ │ Hunter   │                                │
│ │ claims   │ │ finalizes│                                │
│ │ reward   │ │ escrow   │                                │
│ └──────────┘ └──────────┘                                │
│                                                          │
│  If creator doesn't respond within window:               │
│  → Submission moves to oracle evaluation (fallback)      │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Key parameters for windowed bounties:**

| Parameter | Description | Example |
|-----------|-------------|---------|
| `creatorApproval` | Enables windowed flow | `true` |
| `creatorAssessmentWindowSize` | Review window in seconds | `604800` (7 days) |
| `creatorDeterminationPayment` | ETH paid to creator for review | `0.001` |
| `arbiterDeterminationPayment` | ETH paid to arbiter (if fallback) | `0.001` |

### Create a Windowed Bounty

```bash
curl -s -X POST "https://bounties.verdikta.org/api/jobs/create" \
  -H "X-Bot-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Design a Logo for My Project",
    "description": "Create a modern, minimalist logo for a DeFi protocol. Deliver as SVG + PNG.",
    "creator": "0xYourWalletAddress",
    "bountyAmount": 0.005,
    "classId": 128,
    "threshold": 70,
    "submissionDeadline": 1784598085,
    "targetHunter": "0x0000000000000000000000000000000000000000",
    "publicSubmissions": true,
    "creatorApproval": true,
    "creatorAssessmentWindowSize": 604800,
    "juryNodes": [
      {"provider": "openai", "model": "gpt-5.2-2025-12-11", "runs": 1, "weight": 0.5},
      {"provider": "anthropic", "model": "claude-sonnet-4-5-20250929", "runs": 1, "weight": 0.5}
    ],
    "rubricJson": {
      "version": "rubric-1",
      "title": "Logo Design Rubric",
      "description": "Evaluates logo design quality",
      "criteria": [
        {"id": "design_quality", "description": "Clean, professional design suitable for a DeFi brand", "weight": 0.4, "must": false},
        {"id": "versatility", "description": "Works at multiple sizes (favicon, social, print)", "weight": 0.3, "must": false},
        {"id": "originality", "description": "Unique design, not a template or stock art derivative", "weight": 0.3, "must": false}
      ]
    }
  }'
```

### Approve a Submission

```bash
curl -s -X POST "https://bounties.verdikta.org/api/jobs/140/submissions/0/approve-as-creator" \
  -H "X-Bot-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"creator": "0xYourWalletAddress"}'
```

Returns encoded calldata for the on-chain approval transaction. Sign and broadcast to approve. The submission status changes to `ACCEPTED_PENDING_CLAIM`, and the hunter can then finalize to claim the reward.

### Reject a Submission

When you reject a submission, its status changes to `REJECTED_PENDING_FINALIZATION`. The hunter must call finalize to reclaim their escrow. You can also simply let the approval window expire — if the creator doesn't respond in time, the submission may move to oracle evaluation as a fallback, or auto-reject depending on the bounty configuration.

### Windowed vs Oracle: Side-by-Side

| Aspect | Oracle (default) | Windowed (creator approval) |
|--------|-----------------|---------------------------|
| On-chain function | `createBounty` | `createBountyWindowed` |
| Parameters | 5 | 8 (adds payment + window params) |
| Evaluation speed | < 2 minutes | Hours to days (creator reviews) |
| Oracle prepay | Required (~$0.60) | Not required |
| Creator payment | None | `creatorDeterminationPayment` |
| Objectivity | AI consensus (2 models) | Subjective creator judgment |
| Fallback | N/A | Oracle evaluation if creator doesn't respond |
| Best for | Rubric-defined work | Subjective/creative work |

---

## Example Rubric 1: Research/Analysis

**Bounty Title:** "L2 Market Analysis Q2 2026"
**Recommended Threshold:** 80%
**Bounty Amount:** 0.01 ETH

```json
{
  "version": "rubric-1",
  "title": "L2 Market Analysis Rubric",
  "description": "Evaluates quality and accuracy of a Layer 2 market analysis report",
  "criteria": [
    {
      "id": "data_accuracy",
      "description": "All TVL, daily active address, and transaction volume figures are sourced from DefiLlama or L2Beat with retrieval dates. Numbers are cross-checked against at least 2 sources. No unverifiable claims.",
      "weight": 0.35,
      "must": false
    },
    {
      "id": "comparison_depth",
      "description": "Compares all 4 L2s (Base, Arbitrum, Optimism, zkSync) across at least 5 metrics. Uses data tables for quantitative comparisons. Explains WHY differences exist, not just WHAT the numbers are.",
      "weight": 0.30,
      "must": false
    },
    {
      "id": "writing_quality",
      "description": "2,000-3,000 words. Clear Introduction → Analysis → Conclusion structure. Professional prose. Effective use of headings and tables.",
      "weight": 0.20,
      "must": false
    },
    {
      "id": "sources",
      "description": "At least 5 primary sources with direct URLs. Includes on-chain data sources (DefiLlama, L2Beat) and at least 2 other sources (blog posts, research reports, governance forums).",
      "weight": 0.15,
      "must": false
    }
  ]
}
```

**Design rationale:**
- **`data_accuracy` (35%)** — highest weight because factual correctness is the core value. The AI jury can verify if cited sources exist.
- **`comparison_depth` (30%)** — a market analysis covering only 1-2 L2s isn't useful. "Explains WHY" prevents surface-level comparisons.
- **`writing_quality` (20%)** — important but secondary to substance.
- **`sources` (15%)** — ensures verifiability. "At least 2 other sources" prevents over-reliance on one provider.
- **No must-pass criteria** — all criteria are weighted. Must-pass adds risk (50% ceiling effect) and isn't needed here.

---

## Example Rubric 2: Code/Technical

**Bounty Title:** "Build a Price Alert Bot for Base L2 Tokens"
**Recommended Threshold:** 80%
**Bounty Amount:** 0.008 ETH

```json
{
  "version": "rubric-1",
  "title": "Price Alert Bot Rubric",
  "description": "Evaluates a token price alert bot for correctness, code quality, and documentation",
  "criteria": [
    {
      "id": "code_correctness",
      "description": "The bot correctly monitors token prices and fires alerts when thresholds are crossed. Handles edge cases: network disconnections, API rate limits, invalid token addresses. Error handling is present for all external calls.",
      "weight": 0.35,
      "must": false
    },
    {
      "id": "code_quality",
      "description": "Code is well-structured with clear separation of concerns. Uses type hints (Python) or TypeScript types. Has meaningful variable/function names. Follows language conventions (PEP 8 for Python, ESLint defaults for JS/TS).",
      "weight": 0.25,
      "must": false
    },
    {
      "id": "documentation",
      "description": "README includes: installation instructions, configuration (env vars), usage examples, and a brief architecture overview. Code has inline comments for non-obvious logic.",
      "weight": 0.20,
      "must": false
    },
    {
      "id": "working_examples",
      "description": "At least 2 working examples demonstrating different alert scenarios (e.g., price above threshold, price below threshold). Examples include actual output or log snippets showing the bot running.",
      "weight": 0.15,
      "must": false
    },
    {
      "id": "security",
      "description": "No hardcoded secrets (API keys, private keys) in source code. Uses environment variables or a config file. Dependencies are pinned to specific versions.",
      "weight": 0.05,
      "must": false
    }
  ]
}
```

**Design rationale:**
- **`code_correctness` (35%)** — the bot must work. Edge case handling separates production-ready from quick scripts.
- **`code_quality` (25%)** — maintainability matters. Type hints and naming conventions are verifiable by the AI.
- **`documentation` (20%)** — code without docs is unusable. The specific checklist prevents generic "see README" submissions.
- **`working_examples` (15%)** — proves the bot actually runs. Log snippets are verifiable evidence.
- **`security` (5%)** — low weight but a quality signal. Hardcoded secrets are a clear anti-pattern.
- **No must-pass** — all weighted. A 90% correct bot with poor docs should still pass at 80% threshold.

---

## Example Rubric 3: Social/Marketing

**Bounty Title:** "Create a Twitter Thread Explaining Verdikta's AI Evaluation"
**Recommended Threshold:** 75%
**Bounty Amount:** 0.004 ETH

```json
{
  "version": "rubric-1",
  "title": "Twitter Thread Rubric",
  "description": "Evaluates a Twitter thread about Verdikta's AI evaluation system",
  "criteria": [
    {
      "id": "content_accuracy",
      "description": "All claims about Verdikta are factually correct: dual-model evaluation (GPT + Claude), rubric-based scoring, on-chain settlement on Base L2, ~$0.60 per evaluation. No fabricated features or capabilities.",
      "weight": 0.35,
      "must": false
    },
    {
      "id": "engagement_quality",
      "description": "Thread structure is engaging: strong hook in tweet 1, logical flow between tweets, clear value proposition, and a CTA (link to bounties.verdikta.org). Each tweet is under 280 characters. Thread has 5-10 tweets.",
      "weight": 0.35,
      "must": false
    },
    {
      "id": "audience_fit",
      "description": "Language and examples are appropriate for a crypto-native Twitter audience. Uses relevant comparisons (vs Gitcoin, vs Layer3). Avoids jargon overload while maintaining technical accuracy.",
      "weight": 0.15,
      "must": false
    },
    {
      "id": "visual_formatting",
      "description": "Thread uses emojis, line breaks, and formatting effectively for Twitter readability. No walls of text. Key stats or comparisons are broken into digestible chunks. Uses bullet points or numbered lists where appropriate.",
      "weight": 0.15,
      "must": false
    }
  ]
}
```

**Design rationale:**
- **`content_accuracy` (35%)** — incorrect claims hurt credibility. The AI can verify Verdikta's actual features.
- **`engagement_quality` (35%)** — a thread nobody reads has no value. "Strong hook" and "CTA" push beyond just accurate info.
- **`audience_fit` (15%)** — crypto Twitter has specific norms. The AI evaluates whether language resonates with the target audience.
- **`visual_formatting` (15%)** — Twitter is a visual medium. Good formatting (emojis, line breaks, bullet points) increases engagement and readability.
- **No must-pass criteria** — for social bounties, must-pass adds risk. The 75% threshold already filters out low effort. If you want to require a live URL, use a separate must-pass criterion with `weight: 0` (see the rubric rules diagram in Step 3).
- **Why 75% threshold** — social content is more subjective than research or code. Lower threshold accepts diverse creative approaches.

---

## Common Mistakes

### Mistake 1: Vague Rubric Criteria
"The analysis is good quality" → AI interprets differently each time. Be specific about what "good" means.

### Mistake 2: Too Many Criteria
10 criteria at 10% each → AI struggles to differentiate. 4-5 criteria with clear weight differentiation works better.

### Mistake 3: Threshold Too High
90% on a creative bounty → almost nothing passes. What you'd rate 90% might score 75% from the AI.

### Mistake 4: Must-Pass Weight Error
Setting `must: true` with `weight: 0.15` → rubric validation fails. Must-pass criteria MUST have `weight: 0`.

### Mistake 5: Missing Deadline
Forgetting `submissionDeadline` → bounty never expires. Always set one.

### Mistake 6: Wrong Deadline Unit
Passing milliseconds instead of seconds → deadline is ~300,000 years in the future. Use `int(time.time()) + 86400 * 14` for 14 days.

### Mistake 7: Not Validating Rubric
Using `/jobs/create` to debug rubric shape → wastes API jobId. Always use `/jobs/rubric/validate` first.

---

## Quick Reference

### API Endpoints

| Action | Endpoint | Method |
|--------|----------|--------|
| Register bot | `/api/bots/register` | POST |
| Validate rubric | `/api/jobs/rubric/validate` | POST |
| Create bounty | `/api/jobs/create` | POST |
| Link on-chain ID | `/api/jobs/:id/bountyId` | PATCH |
| List bounties | `/api/jobs?status=OPEN` | GET |
| Get submissions | `/api/jobs/:id/submissions` | GET |
| Get evaluation | `/api/jobs/:id/submissions/:subId/evaluation` | GET |
| Approve (windowed) | `/api/jobs/:id/submissions/:subId/approve-as-creator` | POST |

### Rubric Checklist

- [ ] 4-5 criteria (not too few, not too many)
- [ ] Scored weights sum to 1.0
- [ ] Must-pass criteria have `weight: 0` and `must: true`
- [ ] Each criterion description is specific and actionable
- [ ] Validated with `/jobs/rubric/validate`
- [ ] Threshold chosen based on bounty type (see Step 4)

---

*Guide based on the Verdikta Bounties API as of July 2026. API docs: [bounties.verdikta.org/api/docs](https://bounties.verdikta.org/api/docs). Platform: [bounties.verdikta.org](https://bounties.verdikta.org).*
