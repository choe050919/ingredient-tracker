# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Vanilla HTML/CSS/JavaScript kitchen ingredient tracker with meal logging and AI prompt generation. No build system, no dependencies, no package manager. Runs by opening HTML files directly in the browser.

## Running the App

Open `index.html` in a browser. No build or dev server required.

## Architecture

**Single-file SPA**: All logic (HTML, CSS, JS) lives in `index.html` (~1100 lines).

**Three tabs**:
1. 재료창고 (Ingredients) — CRUD with categories, drag-and-drop reordering, freeze toggle, search/filter
2. 레시피 (Recipes) — Create/edit/delete recipes with a list+editor layout
3. 식사기록 (Meal Log) — Log meals by date, used by the AI prompt generator for context

**Data persistence**: All state stored in `localStorage` under keys `kitchen_ingredients_v1`, `kitchen_recipes_v1`, `kitchen_meals_v1`. Export/import via JSON file backup.

**AI integration**: `generatePrompt()` builds an LLM-friendly ingredient list with recent meal history (configurable lookback window) for recipe suggestions. Copied to clipboard via `copyPrompt()`.

## Key Conventions

- UI language is Korean throughout (categories, labels, help text, day names)
- Dark theme using CSS custom properties (`--bg`, `--surface`, `--accent`, `--danger`, `--green`)
- Fonts: IBM Plex Mono + IBM Plex Sans KR (loaded from Google Fonts)
- Mobile-responsive with 700px breakpoint
- HTML5 native drag-and-drop API for ingredient category moves
- Inline editing pattern: click element → replace with input → save on blur/Enter
