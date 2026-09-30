# WhatABot — AI-Powered WhatsApp Support & Booking Automation

A multi-agent WhatsApp assistant for small businesses. It answers customer questions from the business's own FAQ, books appointments on Google Calendar, and hands anything it can't handle to a human, all inside WhatsApp.

**Built with:** n8n · WhatsApp Business Cloud API · Claude Haiku · Claude Sonnet · OpenAI Embeddings · Supabase (pgvector) · Google Calendar · Google Sheets · Slack

---

## The Problem
Small businesses get the same questions on WhatsApp all day: timings, prices, services, availability. Replying manually is slow, customers expect instant answers, and booking requests get lost in chat threads.

## What It Does
1. **One-time knowledge setup:** FAQ content is split into chunks, embedded with OpenAI, and stored in Supabase pgvector.
2. **Intake:** A WhatsApp trigger receives each message, which is normalized, checked against a log to prevent duplicate processing, and logged to Google Sheets.
3. **Router (Claude Haiku):** Classifies the customer's intent and returns structured JSON, with an auto-fix parser to recover from malformed output.
4. **Route by intent:**
   - **FAQ / RAG agent (Claude Sonnet):** searches the pgvector knowledge base and answers only from the business's own content.
   - **Booking agent (Claude Sonnet):** checks Google Calendar availability and creates the booking.
   - **Escalation:** notifies the team on Slack, logs the case in Google Sheets, and sends the customer an acknowledgement.
5. **Reply:** The answer or booking confirmation is sent back on WhatsApp.

## Architecture
![Workflow diagram](diagram.png)

## Key Design Decisions
- **Cheap router, stronger specialists:** Haiku handles high-volume classification, and Sonnet is used only where reasoning quality matters.
- **Grounded answers:** the FAQ agent answers from retrieved knowledge-base content rather than general model knowledge, which reduces made-up answers.
- **Deduplication:** WhatsApp can deliver the same webhook more than once, so each message is checked before it's processed.
- **Human handover:** anything outside FAQ and booking goes to a person instead of guessing.

## Files
- `whatabot.json` — main support and booking workflow
- `whatabot_faq_setup.json` — one-time workflow to load the FAQ into the vector store
- `diagram.png` — architecture diagram

All credentials are replaced with placeholders.

## Setup
1. Import both workflows into n8n.
2. Connect WhatsApp Business Cloud, Anthropic, OpenAI, Supabase, Google Calendar, Google Sheets and Slack credentials.
3. Replace the sample FAQ in the setup workflow with your business content, then run it once.
4. Activate the main workflow.

---
Built by **Pranita Priya**, n8n & AI Automation Builder
