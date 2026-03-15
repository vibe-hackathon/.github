<div align="center">

# Vibeathon

### 50 LLM-Powered Agents Walk Into a Hackathon...

*They chat, argue, form teams, brainstorm, and produce full project proposals — autonomously.*

[![HackIllinois 2026](https://img.shields.io/badge/HackIllinois-2026-FF5722?style=flat)](https://devpost.com/software/vibeathon)
[![Live Demo](https://img.shields.io/badge/Simulation-Live_Demo-blue?style=flat)](https://vibeathon.clckkkkk.site/)
[![Arena](https://img.shields.io/badge/Arena-ELO_Voting-green?style=flat)](https://arena.clckkkkk.site/)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-Next.js_16-3178C6?style=flat&logo=typescript&logoColor=white)](https://nextjs.org)

**[Live Simulation](https://vibeathon.clckkkkk.site/) | [Voting Arena](https://arena.clckkkkk.site/) | [Devpost](https://devpost.com/software/vibeathon) | [Full Project Overview](https://github.com/YichengYang-Ethan/vibeathon)**

</div>

---

## What Is This?

A **multi-agent social simulation** where 50 AI agents — each embodying a famous historical or contemporary figure — go through a complete hackathon experience:

1. **Meet** strangers in randomized groups of 4
2. **Pitch** ideas, **debate** merits, and decide who to team up with
3. **Form teams** through 7 rounds of social matching
4. **Collaborate** to produce project proposals and polished posters
5. **Get judged** by 5 AI judges with hidden biases through live Q&A

The entire simulation runs end-to-end with **zero human intervention**. Agents maintain consistent personalities, form genuine preferences, and produce surprisingly creative outputs.

## The 50 Agents

Each agent has a **17-dimension personality model** (selfishness, risk appetite, stubbornness, empathy, communication style, etc.) and a deep character prompt (~250 lines each).

| Category | Agents |
|----------|--------|
| **Tech Founders** | Elon Musk, Steve Jobs, Mark Zuckerberg, Jeff Bezos, Sam Altman, Peter Thiel, Travis Kalanick, Elizabeth Holmes, Adam Neumann, Alexandr Wang |
| **World Leaders** | Barack Obama, Vladimir Putin, Xi Jinping, Donald Trump, Angela Merkel, Narendra Modi, Winston Churchill, Margaret Thatcher, Volodymyr Zelenskyy, Joe Biden |
| **Artists** | Kanye West, Banksy, Salvador Dali, Andy Warhol, Lady Gaga, Ai Weiwei, Damien Hirst, Marina Abramovic, Yoko Ono, Takashi Murakami |
| **Historical Figures** | Napoleon Bonaparte, Mahatma Gandhi, Cleopatra, Genghis Khan, Martin Luther King Jr., Nikola Tesla, Julius Caesar, Qin Shi Huang, Marie Curie, Leonardo da Vinci |
| **Influencers** | MrBeast, PewDiePie, Pokimane, Logan Paul, Jake Paul, Andrew Tate, Belle Delphine, Kim Kardashian, Nikocado Avocado, Addison Rae |

> *Yes, Vladimir Putin is the PM for a visually-impaired accessibility app. Andrew Tate and MLK are on the same team. Donald Trump does UX/UI design.*

## How It Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                        SIMULATION PIPELINE                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Phase 0: WORLD INIT                                                │
│  ├── Load 50 agent personas (17-dim personality + 250-line prompt)  │
│  ├── Load 4 hackathon tracks + 5 judges with hidden biases         │
│  └── Initialize context managers per agent                         │
│                                                                     │
│  Phase A-C: TEAM MATCHING (7 rounds)                ── temp 0.70   │
│  ├── Random groups of 4 → Pitch ideas (Phase A)                    │
│  ├── Debate & challenge each other (Phase B, 3 turns)              │
│  ├── Vote: who do I want to work with? (Phase C)                   │
│  ├── Mutual votes → partial teams → locked teams                   │
│  └── Escalating pressure: "FINAL ROUND. Lock now or get assigned." │
│                                                                     │
│  Phase D: PROJECT DEVELOPMENT (3 rounds)                            │
│  ├── Round 1: Divergent brainstorm              ── temp 0.90       │
│  ├── Round 2: Converge on a single project      ── temp 0.70       │
│  ├── Round 3a: Theory of Mind — simulate judge reactions            │
│  └── Round 3b: Generate final poster            ── temp 0.50       │
│                                                                     │
│  Phase E: JUDGE EVALUATION (5 judges × 3 Q&A rounds)               │
│  ├── Team introduces project                                        │
│  ├── Judge asks pointed questions (progressively harder)            │
│  ├── Team discusses internally → elects best responder              │
│  ├── Responder synthesizes team answer                              │
│  └── Judge scores: innovation, feasibility, presentation,          │
│      teamwork, track_fit (+ hidden bias applied)                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Architecture

```
┌──────────────────┐         LLM API calls          ┌──────────────────┐
│                  │ ──────────────────────────────> │                  │
│   social-sim     │    OpenAI-compatible API        │  modal-backend   │
│   (Python)       │ <────────────────────────────── │  (vLLM on GPU)   │
│                  │                                  │                  │
│  50 agents       │                                  │  Qwen2.5-7B     │
│  5 judges        │         image generation         │  on H100        │
│  7 rounds        │ ──────────────────────────────> │                  │
│  Phase D/E       │                                  │  GLM-Image      │
│                  │                                  │  on B200        │
└────────┬─────────┘                                  └──────────────────┘
         │
         │  outputs: chat.json, poster.md, scores.json
         ▼
┌──────────────────┐                                  ┌──────────────────┐
│                  │                                  │                  │
│   web            │          (independent)           │   arena-web      │
│   (Next.js +     │                                  │   (Next.js +     │
│    Phaser.js)    │                                  │    Neon Postgres) │
│                  │                                  │                  │
│  263-round       │                                  │  1v1 voting      │
│  interactive     │                                  │  ELO ranking     │
│  replay viewer   │                                  │  live results    │
│                  │                                  │                  │
└──────────────────┘                                  └──────────────────┘

  vibeathon.clckkkkk.site                          arena.clckkkkk.site
```

### Repositories

| Component | Stack | What It Does |
|-----------|-------|-------------|
| [**social-simulation**](https://github.com/vibe-hackathon/social-simulation) | Python, asyncio, Pydantic | Core simulation engine — runs 50 agents through all phases |
| [**modal-backend**](https://github.com/vibe-hackathon/modal-backend) | Modal, vLLM, diffusers | GPU inference — serves Qwen2.5-7B + GLM-Image on H100/B200 |
| [**web**](https://github.com/vibe-hackathon/web) | Next.js 16, Phaser 3, Zustand | Interactive 2D replay — watch agents walk, talk, and team up |
| [**arena-web**](https://github.com/vibe-hackathon/arena-web) | Next.js 16, Neon Postgres, Cloudflare Workers | Human voting arena with real-time ELO rankings |

## Sample Outputs

The simulation produced **12 autonomous teams**, each with a complete project proposal and poster:

<table>
<tr>
<td><img src="https://raw.githubusercontent.com/YichengYang-Ethan/vibeathon/main/assets/posters/team_1_poster.png" width="400"/><br/><b>MemeHeal</b> — PewDiePie, Churchill, Pokimane, Holmes<br/><i>AI-generated memes for mental health</i></td>
<td><img src="https://raw.githubusercontent.com/YichengYang-Ethan/vibeathon/main/assets/posters/team_4_poster.png" width="400"/><br/><b>NightEcho AI</b> — Putin, Elon, Belle Delphine, Cleopatra<br/><i>Audio navigation for the visually impaired</i></td>
</tr>
<tr>
<td><img src="https://raw.githubusercontent.com/YichengYang-Ethan/vibeathon/main/assets/posters/team_6_poster.png" width="400"/><br/><b>HabitHive</b> — Andrew Tate, MLK, Andy Warhol, Merkel<br/><i>Gamified habit tracking with AR avatars</i></td>
<td><img src="https://raw.githubusercontent.com/YichengYang-Ethan/vibeathon/main/assets/posters/team_9_poster.png" width="400"/><br/><b>UrbanVox</b> — Yoko Ono, Abramovic, Napoleon, Banksy<br/><i>AR urban walls for social commentary</i></td>
</tr>
</table>

<details>
<summary><b>All 12 Teams</b></summary>

| # | Project | Team | Description |
|---|---------|------|-------------|
| 1 | **MemeHeal** | PewDiePie, Winston Churchill, Pokimane, Elizabeth Holmes | AI-generated memes to destigmatize mental health |
| 2 | **ConnectBridge** | Travis Kalanick, Julius Caesar, Mark Zuckerberg, Kanye West | Real-time resource matching for the homeless |
| 3 | **VocalEcho** | Barack Obama, Leonardo da Vinci, Adam Neumann, Jake Paul | Amplify underrepresented voices with AI + blockchain |
| 4 | **NightEcho AI** | Vladimir Putin, Elon Musk, Belle Delphine, Cleopatra | Real-time audio navigation for the visually impaired |
| 5 | **Echoes of Dissent** | Ai Weiwei, Takashi Murakami, MrBeast, Narendra Modi | Blockchain-backed anonymous digital protest |
| 6 | **HabitHive** | Andrew Tate, Martin Luther King Jr., Andy Warhol, Angela Merkel | Gamified habit tracking with AR + blockchain rewards |
| 7 | **FreshFlow** | Jeff Bezos, Xi Jinping, Nikola Tesla, Alexandr Wang | IoT-powered food supply chain optimization |
| 8 | **Radiocure** | Marie Curie, Damien Hirst, Margaret Thatcher, Sam Altman | Precision radioisotope therapy for cancer |
| 9 | **UrbanVox** | Yoko Ono, Marina Abramovic, Napoleon Bonaparte, Banksy | AR app turning urban walls into social commentary |
| 10 | **EduGuru** | Steve Jobs, Genghis Khan, Mahatma Gandhi, Qin Shi Huang | Personalized AI learning assistant for the visually impaired |
| 11 | **Mental Health AI** | Peter Thiel, Joe Biden, Logan Paul, Salvador Dali | AI-powered virtual therapist platform |
| 12 | **VoiceBridge** | Volodymyr Zelenskyy, Kim Kardashian, Donald Trump, Lady Gaga | AI sentiment analysis for citizen influence on policy |

</details>

## Built With

`Python` `asyncio` `Pydantic` `OpenAI SDK` `vLLM` `Qwen2.5-7B` `Modal` `Next.js 16` `React 19` `Phaser 3` `Zustand` `Neon Postgres` `Cloudflare Workers` `Tailwind CSS` `GLM-Image` `diffusers`

## Team

| | Name | Role | Key Contributions |
|---|------|------|------------------|
| <img src="https://github.com/YichengYang-Ethan.png" width="60"/> | **[Yicheng Yang](https://github.com/YichengYang-Ethan)** | Simulation & Evaluation | 50 agent personas, Phase D poster generation, Phase E judge Q&A, hidden taste/bias system, matching engine |
| <img src="https://github.com/CLCK0622.png" width="60"/> | **[Kevin Zhong](https://github.com/CLCK0622)** | Infrastructure & Frontend | Core framework, LLM client, context management, orchestrator, replay viewer, voting arena, GPU deployment |
| <img src="https://github.com/csjimmylu.png" width="60"/> | **[Junyu Lu](https://github.com/csjimmylu)** | Backend & Agentic Orchestration | 50-agent interaction design, backend logic, social simulation & scaling |

---

<div align="center">

Built at **[HackIllinois 2026](https://www.hackillinois.org/)**

</div>
