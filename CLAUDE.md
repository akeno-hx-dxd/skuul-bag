# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a SvelteKit stationery exhibition SPA with TypeScript, TailwindCSS, and GSAP animations. The application showcases Skuulbag's product catalog with a rainbow theme and interactive features.

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
- `+page.ts` files handle client-side page data loading

### Key Components
- **Header** (`src/routes/Header.svelte`): Rainbow navigation with glassmorphism effects
- **Home Page** (`src/routes/+page.svelte`): Product gallery with modal functionality
- **About Page** (`src/routes/about/+page.svelte`): Company information with rainbow scribble effects
- **Contact Page** (`src/routes/contact/+page.svelte`): Contact form with mailto functionality

### Styling & Animations
- **Custom fonts**: MilkyWalky, IndieFlower, JetBrains Mono
- **GSAP animations**: ScrollTrigger, timeline-based animations
- **Rainbow theme**: Custom CSS variables for consistent theming
- **Glassmorphism**: Backdrop blur effects throughout
- **Responsive design**: Mobile-first approach with breakpoints

### Features
- **Product modal**: Detailed product view with tags and descriptions
- **Contact form**: Professional form with predefined subjects
- **Lazy loading**: Progressive image loading for performance
- **Rainbow effects**: Animated scribbles and gradients
- **Accessibility**: ARIA labels, keyboard navigation, screen reader support

### Build Configuration
- **Vite**: Main build tool with TypeScript support
- **SvelteKit**: Framework with auto adapter
- **GSAP**: Animation library for smooth interactions
- **Custom assets**: 3D icons, backgrounds, and fonts in static folder

## Development Notes

- **Performance optimized**: Reduced DOM elements, efficient animations
- **TypeScript strict mode**: Full type safety enabled
- **Svelte 5**: Uses modern runes syntax (`$props()`, `$derived()`)
- **Responsive**: Optimized for all device sizes
- **SEO ready**: Proper meta tags and semantic HTML