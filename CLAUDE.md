# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a SvelteKit application with TypeScript and TailwindCSS, created using the `sv` CLI tool. The project includes a Wordle-inspired game called "Sverdle" as a demo feature.

## Development Commands

- **Start development server**: `npm run dev` (or `npm run dev -- --open` to open browser)
- **Build for production**: `npm run build`
- **Preview production build**: `npm run preview`
- **Type checking**: `npm run check`
- **Type checking with watch mode**: `npm run check:watch`

## Architecture

### Routing Structure
- Uses SvelteKit's file-based routing in `src/routes/`
- `+layout.svelte` provides the main app layout with Header component
- `+page.svelte` files define route components
- `+page.server.ts` files handle server-side logic and actions
- `+page.ts` files handle client-side page data loading

### Key Components
- **Header** (`src/routes/Header.svelte`): Navigation component
- **Counter** (`src/routes/Counter.svelte`): Interactive counter demo
- **Sverdle Game** (`src/routes/sverdle/`): Complete Wordle clone with:
  - `game.ts`: Core game logic class
  - `+page.server.ts`: Server actions for game state management
  - `words.server.ts`: Word lists for the game
  - Cookie-based game state persistence

### Styling
- Uses TailwindCSS v4 with Vite plugin
- Custom CSS in component `<style>` blocks
- Global styles in `src/app.css`

### Build Configuration
- **Vite**: Main build tool with TypeScript support
- **SvelteKit**: Framework with auto adapter
- **Plugins**: TailwindCSS, SvelteKit, and devtools-json for development
- Uses Bun as package manager (bun.lock present)

## Development Notes

- Server actions handle form submissions and game logic server-side
- Cookie-based state management for the Sverdle game
- TypeScript strict mode enabled
- Svelte 5 with runes syntax (`$props()`, `{@render children()}`)