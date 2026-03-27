# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static single-page website for **Aura Contable SAS**, an Argentine accounting firm. Built with pure HTML, CSS, and vanilla JavaScript — no frameworks, no build tools, no package manager.

## Development

There is no build step. The entire site lives in `index.html` (~2,166 lines) with embedded `<style>` and `<script>` blocks. To develop, open `index.html` in a browser.

Deployment is via **Vercel** (config in `vercel.json`), triggered automatically on push to `main`.

## Architecture

**Single-file structure:**
- `index.html` — all markup, styles (~1,360 lines of CSS), and JS
- `favicon.png` — site favicon
- `vercel.json` — Vercel deployment config

**CSS design system** uses custom properties defined in `:root`:
- Navy/blue palette (`#0d1b3e`, `#1a3a6e`, `#2563c0`) for primary/accent
- Gold (`#c4922a`, `#e8b84b`) for highlights
- Fonts: Playfair Display (headings), DM Sans (body) via Google Fonts
- Responsive sizing with `clamp()`

**JavaScript** is minimal (~24 lines):
- `setStar(n)` — star rating for the survey section
- `enviarContacto()` / `enviarEncuesta()` — form submission handlers
- Intersection Observer for scroll-triggered fade-in animations

**Page sections** (in order): Navigation → Hero (#inicio) → About (#nosotros) → Org Chart (#organigrama) → Services (#servicios) → Contact (#contacto) → Survey (#encuesta) → Footer

## Language

All content is in **Spanish (es-AR)**. Maintain Spanish for user-facing text.
