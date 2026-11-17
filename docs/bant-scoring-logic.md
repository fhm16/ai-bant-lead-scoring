# BANT Lead Scoring & AI Evaluation Logic

This document explains how incoming form leads are evaluated, scored, and categorized using **Google Gemini AI** before routing.

## 1. Input Parameters (BANT Criteria)
The n8n Form Trigger captures three core qualification metrics:
- **Budget:** Financial capacity of the lead (e.g., `$1000+`, `$500 below`).
- **Authority:** Whether the submitter is the final decision-maker (`Yes` / `No`).
- **Timeline:** Expected project kickoff or purchase timeframe (e.g., `1 month`, `Not sure`).

## 2. AI Prompt & Structured JSON Output
The `Message a model` (Google Gemini) node evaluates the combined weight of all three inputs and returns a strict JSON object:

```json
{
  "score": 70,
  "status": "HOT",
  "reason": "The lead has decision-making authority and an immediate timeline, which outweighs the low budget."
}