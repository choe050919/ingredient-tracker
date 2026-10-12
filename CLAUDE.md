# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Vanilla HTML/CSS/JavaScript kitchen ingredient tracker with meal logging and AI prompt generation. No build system, no package manager. Firebase (Auth + Firestore) is loaded as ES modules from the gstatic CDN.

## Running the App

Serve the folder over http (`python -m http.server 8756`, also in `.claude/launch.json`) and open `http://localhost:8756`. Google sign-in does not work from `file://`; `localhost` and `choe050919.github.io` are the authorized domains.

## Architecture

**Single-file SPA**: All logic (HTML, CSS, JS) lives in `index.html` (~2000 lines): a classic script with the app logic, then a module script with the Firebase code (exposed as `window.cloud`).

**Sign-in**: Google sign-in (popup) gates the whole app.

**Three tabs**:
1. 재료창고 (Ingredients) — CRUD with categories, drag-and-drop reordering, freeze toggle, search/filter
2. 레시피 (Recipes) — Create/edit/delete recipes with a list+editor layout
3. 식사기록 (Meal Log) — Log meals by date, used by the AI prompt generator for context

**Data persistence**: Firestore project `ingredient-tracker-4c6fa`, one document per item at `users/{uid}/{ingredients|recipes|meals|mealBundles}/{id}`. Recipes carry a `pos` field for their user-defined order; the other kinds are ordered by `id`. Security rules (set in the Firebase console, not in this repo) allow only the owner's uid. The app keeps an in-memory copy (`store`); `load*()` return clones and `save*()` write only the items that changed or were removed, so callers keep the synchronous load → mutate → save pattern. Item ids come from `newId()` (monotonic, never two in the same millisecond) because ids are document keys. Firestore's persistent cache gives offline use; unsent writes show as 저장 대기 in the header. Export/import via JSON file backup; import reads the collections back from the server and compares them with the file. Only the theme stays in `localStorage` (`kitchen_theme`).

**External reader**: `C:\Users\choe0\Desktop\일상\.meal-rec\read_kitchen.py` (outside this repo) reads `ingredients`, `meals`, `recipes` and `mealBundles` with a read-only service account; a daily meal-recommendation routine and the `kitchen-data` skill depend on it. Changing collection paths or field names requires updating that script too.

**AI integration**: `generatePrompt()` builds an LLM-friendly ingredient list with recent meal history (configurable lookback window) for recipe suggestions. Copied to clipboard via `copyPrompt()`.

## Key Conventions

- UI language is Korean throughout (categories, labels, help text, day names)
- Dark theme using CSS custom properties (`--bg`, `--surface`, `--accent`, `--danger`, `--green`)
- Fonts: IBM Plex Mono + IBM Plex Sans KR (loaded from Google Fonts)
- Mobile-responsive with 700px breakpoint
- HTML5 native drag-and-drop API for ingredient category moves
- Inline editing pattern: click element → replace with input → save on blur/Enter
