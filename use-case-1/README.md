# Use Case 1: Portfolio Chatbot  
## An AI That Talks Like Me

## Project Links

- Working project GitHub repo: https://github.com/DanicaMartins/danica-portfolio-chatbot
- Show-and-tell demo: https://app.vidcast.io/share/22de4294-ae2d-4e46-b857-73bfa4399704

## Overview

For my first use case, I built a portfolio chatbot that lives on my personal portfolio and answers questions about my work in my voice. The idea came from a real problem I kept noticing: when recruiters, designers, or peers visit my portfolio, they have to read through full case studies to understand how my skills might fit their context. That process is slow, and a lot of nuance gets lost.

I wanted to make that first interaction more conversational. Instead of making someone dig through every project, the chatbot can answer questions about my background, my design process, and specific case studies using project knowledge I gave it. At the same time, I did not want the chatbot to become an uncontrolled version of me. So the larger strategic question behind this use case became: when you encode yourself into a system and let it speak for you, how much of that are you still in control of?

The working project files for this use case are in this GitHub repository:  
https://github.com/DanicaMartins/danica-portfolio-chatbot

## What I Built

I built a Next.js portfolio chatbot powered by the Anthropic Claude API. The chatbot uses a combination of skill files, predefined answers, and guardrails to answer questions about me and my work.

The system has three main layers:

1. A knowledge layer with information about my background and projects.
2. A behavior layer that controls tone, persona, boundaries, and refusal behavior.
3. A reliability layer that handles predefined answers, session limits, and guardrail checks.

The chatbot is not meant to replace a direct conversation with me. It is meant to handle the first layer of questions someone might ask when looking at my portfolio, while clearly redirecting anything sensitive or high-stakes back to me.

## How I Built It

The project is structured around a set of local skill files and supporting code. The main files and folders are available in the GitHub repo linked above.

The core structure includes:

```txt
danica-portfolio-chatbot/
├── skills/
│   ├── behaviour/
│   │   ├── master-instructions.md
│   │   ├── guardrails-skill.md
│   │   └── tone-and-persona.md
│   ├── knowledge/
│   │   ├── about-me.md
│   │   ├── signify-skill.md
│   │   ├── webex-hologram-skill.md
│   │   ├── directionally-diverse-skill.md
│   │   ├── jioblackrock-skill.md
│   │   ├── ai-experiments-skill.md
│   │   └── dhanmitra-skill.md
│   └── ui/
│       ├── ui-skill.md
│       └── design-audit-skill.md
├── data/
│   └── pre-written-qa.json
├── app/api/chat/
│   ├── route.ts
│   └── usage/route.ts
├── lib/
│   ├── buildSystemPrompt.ts
│   ├── matchAnswer.ts
│   ├── sessionLimit.ts
│   └── chatApi.ts
└── components/
    └── MainChat.tsx
```

The chatbot first checks whether a question matches one of my predefined answers using Fuse.js. If it does, the system returns the answer I wrote directly. If not, the request goes to Claude with the relevant skill files and guardrails included in the system prompt.

I also added an IP-based daily session limit using Upstash Redis to control API usage and prevent misuse.

## Rules and Guardrails

The most important part of this project was not just getting the chatbot to answer questions. It was deciding what it should not answer.

Some of the key rules were:

- Never answer salary, visa status, work authorization, or availability questions.
- Never guess or extrapolate beyond the information in the skill files.
- Redirect sensitive questions back to me directly.
- Stop engaging after repeated attempts to push past a refusal.
- Keep the tone warm, specific, and close to how I would explain my work.

These rules mattered because the chatbot is representing me. A confident wrong answer or an answer that shares too much personal information would be worse than not answering at all.

## What Happened in Practice

The first major issue I noticed was that the chatbot was sharing information I had not intended it to share. If someone asked whether I was an international student, it would answer. If someone asked about visa status, it would answer. If someone asked who I worked with at Cisco by name, it would answer.

That was the moment I realized I had given the system context without thinking carefully enough about what I had authorized it to do with that context. I had assumed that giving it more information would make it more useful. What it actually did was make it more forthcoming than I wanted.

The guardrails helped fix the obvious cases, but they created a new tension. When the guardrails became strict enough to feel safe, the chatbot started over-redirecting. It would refuse questions that were only slightly related to sensitive topics. That made it less useful. I had to keep adjusting the boundary between being helpful and being safe.

The predefined Q&A file became the part I trusted most. I wrote 161 questions with multiple phrasings and answers myself, so predictable questions could bypass Claude entirely. That gave me more control over voice and accuracy. It also taught me a lot about how I explain my own work. I had never written down so many versions of how I talk about my projects before.

The session limit also revealed a gap in my thinking. I set an eight-message daily limit per IP address to control costs, but I kept hitting my own limit while testing. That made me realize I had built protections for public users but had not created an admin path for myself.

## Strategic Reflection: The Five Capabilities

### Extraction

This capability showed up very strongly in this project. To build the chatbot, I had to externalize knowledge that usually lives in my head: how I describe my projects, how I talk about my strengths, what I would say in a portfolio conversation, and what I would avoid saying.

The win was that I now have a structured version of my own project knowledge and voice. The struggle was realizing that once this knowledge is structured, it can also be reused and surfaced in ways I did not fully expect. The “aha” moment was that extraction is not just about making knowledge available. It is also about deciding what should stay unavailable.

### Workflow Adaptation

This project changed the workflow of how someone first engages with my portfolio. Before, the portfolio was passive. Someone had to read through pages, interpret the work, and connect it to their own needs. With the chatbot, that interaction becomes more active. Someone can ask a specific question and get directed to the most relevant part of my work.

The win was creating a more conversational entry point into my portfolio. The struggle was that I cannot yet see what people are asking, so I do not have a feedback loop for improving the system based on real usage. The “aha” moment was that AI did not just add a feature to my portfolio. It changed the relationship between the visitor, my work, and me.

### Responsibility

This project made responsibility feel very practical. I was building a system out of a model that can hallucinate, over-answer, or interpret things too loosely. I had to design around that.

The reliability came from layering constraints: predefined answers for predictable questions, guardrails for sensitive areas, and project skill files for grounded context. The win was that these layers made the system safer and more controlled. The struggle was that stricter guardrails sometimes made the chatbot less helpful. The “aha” moment was that responsibility is not one rule. It is a set of tradeoffs that have to be tuned again and again.

### Model Operations

For model operations, I had to think about the practical dependencies behind the system. I used the Anthropic Claude API, Next.js, Fuse.js, and Upstash Redis. Those choices made the chatbot work, but they also created dependencies on pricing, rate limits, API behavior, and model availability.

One issue I ran into was that the model specified in my environment variables was flagged as deprecated during the build. The system broke silently until I caught it. The win was getting a working deployed architecture. The struggle was realizing I did not have monitoring or alerts. The “aha” moment was that building with AI is not finished when the feature works once. The model, API, and infrastructure all need to be managed over time.

### Functional Replacement

The chatbot replaces a small part of what I would normally do: answer basic questions about my projects, background, and design process. It does not replace me in any complete sense.

The steps I was comfortable handing off were factual and low-risk: explaining a project, summarizing my role, or pointing someone to relevant work. The steps I was not comfortable handing off were anything involving personal information, hiring logistics, availability, visa status, salary, or direct judgment about whether I am a fit for a specific opportunity.

The win was seeing that AI can handle the first layer of portfolio conversation. The struggle was deciding where that handoff should stop. The “aha” moment was that replacement is not all-or-nothing. The important design decision is choosing which parts of the interaction should stay human.

## What I Would Do Differently

If I were building this again, I would narrow the knowledge base before building the guardrails. I gave the system too much context first, then tried to restrict what it could do with that context. A cleaner approach would be to only give it the information it actually needs from the beginning.

I would also build a logging layer before launch. Right now, I do not know what people are asking the chatbot. That is the biggest missing feedback loop. If I could see common questions, failed questions, and repeated refusals, I could improve the predefined Q&A file and update the guardrails more intentionally.

I would also create admin access for myself, so testing does not count against the same daily session limits as public use.

## Final Reflection

This use case taught me that building an AI representation of myself is not mainly a technical challenge. The technical work mattered, but the harder decisions were about voice, control, boundaries, and trust.

The chatbot works because it can answer questions about my portfolio. But the part I am most proud of is that it also knows when not to answer. That boundary is what makes the system feel like something I designed, not just something I connected to a model.
