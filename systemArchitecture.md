# Moodly Health system architecture

## Overview
Moodly Health is a simple web application that lets users log moods, see patterns, and get small self-care suggestions. It uses a React web client that calls a Node.js API. Data is stored in PostgreSQL. The design favors low cost, privacy, and easy growth.

## Goals
- Make mood logging fast and reliable
- Turn logs into simple insights and small actions
- Keep user data private and secure
- Keep the stack simple so it is easy to build and maintain

## Out of scope for MVP
- Real time chat or therapy
- Medical diagnosis or treatment
- Complex social features

## Stack choices
- Frontend: Next.js 14 with React, TypeScript, Tailwind CSS
- Backend: Node.js 20 with Express
- Database: PostgreSQL with Prisma ORM
- Auth: JWT access tokens plus refresh tokens in HttpOnly cookies
- Deployment: Vercel (frontend), Railway (backend), Neon (database)
- Monitoring: Sentry for errors, simple health check endpoint

## High level architecture
```mermaid
flowchart LR
  U[User] --> FE[Next.js Web App]
  FE --> API[Express API]
  API --> DB[(PostgreSQL Database)]

sequenceDiagram
  participant U as User
  participant FE as Frontend
  participant API as Backend
  participant DB as Database

  U->>FE: Select mood + optional note
  FE->>API: POST /moods {emotion, note}
  API->>DB: Save mood entry
  DB-->>API: Success
  API-->>FE: 201 Created (Mood saved)

sequenceDiagram
  participant FE as Frontend
  participant API as Backend
  participant DB as Database

  FE->>API: GET /summaries/weekly
  API->>DB: Fetch moods and calculate averages
  DB-->>API: Send summary data
  API-->>FE: Return weekly mood summary

{
  "weekly_summary": {
    "week_range": "2025-11-02 to 2025-11-08",
    "entries": 12,
    "top_emotions": ["Calm", "Tired"],
    "suggestions": ["Take a walk", "Try deep breathing"]
  }
}
