---
name: ai-pm-quiz-build-spec
description: Build spec for the "AI PM Quiz" app — navy-brand design tokens, screen-by-screen content (header, home, quiz, results, booking modal), interaction states, and the questions API contract. Use whenever building, editing, or reviewing the AI PM Quiz product UI or its question-fetching backend. Not a general design system — scoped to this one quiz app.
---

```yaml
title: "AI PM Quiz — Build Spec"

tokens:
  font:
    family: Inter
    source: Google Fonts
    opsz: "14..32"
    weights: [400, 500, 600, 700]
    font-optical-sizing: auto
    letter-spacing: "-0.01em"

  colors:
    navy:          { hex: "#002862", use: "Brand, primary buttons, selected" }
    navy_700:      { hex: "#0a3578", use: "Gradient stop" }
    navy_500:      { hex: "#1a4a92", use: "Gradient stop" }
    white:         { hex: "#ffffff", use: "Cards, CTA on navy" }
    ink:           { hex: "#0f1b33", use: "Text" }
    ink_2:         { hex: "#2e3856", use: "Secondary text" }
    muted:         { hex: "#586380", use: "Labels" }
    faint:         { hex: "#939bb4", use: "Hints" }
    disabled:      { hex: "#c3c8d8", use: "Disabled" }
    canvas:        { hex: "#f4f6fa", use: "Page bg" }
    wash:          { hex: "#eef2f9", use: "Selected / badges" }
    border:        { hex: "#e3e7ef", hover: "#b9c5dc", use: "Hairlines" }
    on_navy:       { hex: ["#d6def0", "#a9bcdf"], use: "Text on navy" }
    correct:       { fg: "#1f7a52", bg: "#e6f5ed" }
    incorrect:     { fg: "#c23b3b", bg: "#fbeaea" }

  gradient:
    used_on: [hero, unlock banner, feedback card, quiz top band]
    css: >-
      radial-gradient(120% 140% at 100% 0%, rgba(255,255,255,.14), transparent 50%),
      linear-gradient(135deg, #002862, #0a3578 55%, #1a4a92);

  type:
    display:  "56/60"
    h1:       "36/42"
    question: "28/36"
    h2:       "24/32"
    card:     "20/28"
    body:     "16/24"
    small:    "14/20"
    caption:  "12"
    headings:
      weight: 600
      tracking: "-0.02em to -0.03em"

  radius:
    pills: 200
    feature: 24
    quiz_card: 20
    cards: 16
    options: 12
    inputs: 4

  shadow:
    card: "0 1px 2px rgba(0,40,98,.05), 0 10px 28px rgba(0,40,98,.07)"
    feature: "0 12px 32px rgba(0,40,98,.28)"

  layout:
    max_width: 1200
    quiz_column: 860
    grid: 8px
    method: "flex/grid + gap"
    pills: "white-space: nowrap"

screens:
  header:
    applies_to: all
    height: 64px
    background: white
    border: bottom hairline
    left: wordmark
    center: "module title (quiz)"
    right: 'outline pill "Book 1-on-1 feedback"'

  home:
    hero:
      background: gradient
      content:
        - chip
        - H1
        - paragraph
        - 'white pill "Start assessment"'
        - '"N of 3 tracks completed"'
    tracks_grid:
      card: white
      content:
        - number badge
        - title
        - description
        - '"9 questions · ~14 min"'
        - '"Start →"'
      completed: 'green "Scored NN%" pill'
      hover: lifts 2px
    unlock_banner:
      background: gradient
      cta: 'white "Book a call"'
    locked_cards:
      border: dashed
      cta: '"Book a call to unlock →"'
    loading: 3 skeleton cards
    error: red strip + Retry

  quiz:
    top_band:
      color: navy
      height: 300px
      content:
        - '"← All tracks"'
        - '"N correct" chip'
        - '"N of 9 answered"'
        - segmented progress
    card:
      background: white
      radius: 20
      content:
        - 'number badge "01" + "Question 1 of 9 · Topic · Difficulty"'
        - question
        - options
        - explanation
        - actions
    below_card:
      - keyboard hint
      - '"Submit quiz" link'
    rule: One question visible at a time.

  results:
    score_card:
      ring: { r: 52, stroke: 10 }
      content: ["%", level label]
    feedback_card:
      background: gradient
      role: the hero
      content:
        - headline
        - body
        - focus-area chips (missed topics)
        - 'white CTA "Book my free 1-on-1"'
    question_review:
      filter: [All, Incorrect]
      rows: expandable
    sticky_bar:
      position: bottom
      content: ['"N focus areas identified"', CTA]

  booking_modal:
    flow:
      - day (next 5 weekdays)
      - time pill
      - work email (validated)
      - optional note
      - Confirm
      - success view

states:
  option:
    default:        { border: border, fill: white, mark: letter }
    selected:       { border: navy, fill: wash, mark: "navy, white letter" }
    correct:        { border: green, fill: green tint, mark: "✓ + tag" }
    wrong_picked:   { border: red, fill: red tint, mark: '✕ + "Your answer"' }

  progress_segment:
    default: "rgba(255,255,255,.18)"
    current: { height: 8px, color: "rgba(255,255,255,.6)" }
    answered: white
    correct: "#7cc4a0"
    wrong: "#e7a3a3"

  primary_button:
    sequence:
      - label: Check answer
        note: disabled until selected
      - label: Next question
      - label: See results

  score_level:
    - { min: 80, label: "AI-ready", color: green }
    - { min: 50, max: 79, label: "Building momentum", color: "#b86e00" }
    - { max: 49, label: "Keep practicing", color: red }

interactions:
  feedback_mode:
    prop: feedbackMode
    options:
      instant: check each answer
      end: reveal on results
  keys:
    "1-4": select
    Enter: "primary (one step per press, preventDefault)"
    "← →": navigate
  submit_with_gaps:
    first_click: warns
    second_click: submits
    unanswered_label: Skipped
  motion:
    fade_up: "0.25s on explanation/modal"
    hover: lift
    ring: 0.8s
    rule: Don't re-mount the question block.
  scores:
    persist: localStorage
    key: aipm_scores

backend:
  description: Questions are fetched, not hard-coded.
  api_base:
    prop: apiBase
    default: ./api
    note: static JSON in this prototype

  endpoints:
    - method: GET
      path: "{apiBase}/modules.json"
      response_example:
        modules:
          - id: m1
            num: "01"
            title: Agentic AI Product Foundations
            desc: "…"
            questionCount: 9
            minutes: 14
            locked: false
      notes:
        - "locked: true modules render as locked cards."

    - method: GET
      path: "{apiBase}/modules/{id}.json"
      response_example:
        moduleId: m1
        questions:
          - id: m1-q1
            text: "…"
            topic: Opportunity assessment
            difficulty: Easy
            options: ["…", "…", "…", "…"]
            correct: 1
            explanation: "…"
      notes:
        - Fetched on "Start", cached per module.
        - "topic drives the focus-area chips on results."

  production_note:
    rule: "Don't ship correct/explanation to the client."
    replace_with:
      - method: POST
        path: "/modules/{id}/check"
        request: { questionId: string, answer: number }
        response: { correct: boolean, correctIndex: number, explanation: string }
      - method: POST
        path: "/modules/{id}/submit"
        response: score

rules:
  - Navy is the only brand color; white is the CTA on navy. No orange/violet.
  - Status = tint + colored glyph, never solid discs.
  - One question at a time, content aligned to top (no vertical centering).
  - Headings max weight 600.
```
