# Ask Your Data Assistant (Text-to-SQL)

Lets a business user ask a database question in plain English instead of writing SQL --
generates the query, safety-checks it, runs it, and explains the result in a sentence.

## Problem
Business users want answers from company data without learning SQL or waiting on the
analytics team. This builds that natural-language interface, with a hard safety check so an
LLM-generated query never runs unvalidated against a real database.

## What It Does
- Loads real data into SQLite and documents the schema so the LLM is grounded in actual table
  and column names, not guessing
- Generates a SQL query from a plain-English question
- **Rejects any query that isn't a pure `SELECT`** -- blocks DROP/DELETE/UPDATE/INSERT/ALTER
  before anything is ever executed
- Runs the validated query and turns the result into a one-sentence plain-English answer

## Real Results (real dataset -- 99,441 orders, 112,650 order items, 32,951 products)
All 5 test questions produced correct SQL, passed the safety check, and returned a correct
answer:
- *"What was the total number of orders?"* -> **99,441**
- *"How many orders were canceled?"* -> **625**
- *"What is the average freight value per order?"* -> **$22.82**
- *"What is the total price of all order items?"* -> **$13,591,640**
- *"Which product category has the most items sold?"* -> correctly joined `order_items` to
  `products` and identified **cama_mesa_banho** (bed/bath/table)

That last one is the important result: it wrote a correct multi-table JOIN from a plain-English
question with no hint that a join was even needed.

## Tech Stack
Python, SQLite, Cerebras / Groq (free tier, via shared `call_llm()`), Pandas

## How to Run
Open in Google Colab, run all cells, enter a free Cerebras and Groq API key when prompted.
Dataset auto-downloads via `kagglehub` with a synthetic fallback if it ever fails.
