# Working with GSAP in SvelteKit - Complete Guide

## Overview

GSAP (GreenSock Animation Platform) is a high-performance animation library that's up to 20x faster than jQuery and provides extensive capabilities for animating CSS, SVG, canvas, React, Vue, WebGL, colors, strings, motion paths, and generic objects. This guide covers GSAP v3 integration with SvelteKit.

## Installation

### NPM Installation (Recommended)
```bash
npm install gsap
```

### CDN Alternative
```html
<script src="https://cdn.jsdelivr.net/npm/gsap@3.13/dist/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.8.0/ScrollTrigger.min.js"></script>
```

## Core GSAP Methods

### Basic Animation Methods
```javascript
// Animate TO specified properties
gsap.to(target, { duration: 2, x: 100, rotation: 45 });

// Animate FROM specified properties to current state
gsap.from(target, { duration: 2, x: -100, opacity: 0 });

// Animate FROM one set TO another
gsap.fromTo(target, 
  { x: -100, opacity: 0 }, 
  { x: 100, opacity: 1, duration: 2 }
);

// Set properties instantly (no animation)
gsap.set(target, { x: 100, opacity: 0.5 });
```

### Timeline Management
```javascript
// Create timeline for sequencing animations
const tl = gsap.timeline({ paused: true });

// Add animations to timeline
tl.to(".element1", { duration: 1, x: 100 })
  .to(".element2", { duration: 1, y: 50 }, "-=0.5") // Start 0.5s before previous ends
  .from(".element3", { duration: 1, opacity: 0 });

// Control timeline
tl.play();
tl.pause();
tl.reverse();
tl.restart();
```

## SvelteKit Integration Patterns

### Pattern 1: Basic onMount Integration
```svelte
<script>
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  
  let elementRef;
  
  onMount(() => {
    // Ensure DOM is ready before animating
    gsap.from(elementRef, {
      duration: 1,
      y: 50,
      opacity: 0,
      ease: "power2.out"
    });
  });
</script>

<div bind:this={elementRef}>Animated content</div>
```

### Pattern 2: Element Reference (Recommended)
```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  
  let containerRef;
  let animation;
  
  onMount(() => {
    // Use element reference instead of selectors
    animation = gsap.timeline()
      .from(containerRef.children, {
        duration: 0.8,
        y: 30,
        opacity: 0,
        stagger: 0.1,
        ease: "back.out(1.7)"
      });
  });
  
  onDestroy(() => {
    // Clean up animations
    animation?.kill();
  });
</script>

<div bind:this={containerRef}>
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

### Pattern 3: Custom Svelte Action
```javascript
// lib/actions/gsap.js
import { gsap } from 'gsap';

export function animate(node, { type = 'to', ...props }) {
  const animation = gsap[type](node, props);
  
  return {
    destroy() {
      animation.kill();
    }
  };
}

export function timeline(node, animations = []) {
  const tl = gsap.timeline();
  
  animations.forEach(({ selector, ...props }) => {
    const target = selector ? node.querySelector(selector) : node;
    tl.to(target, props);
  });
  
  return {
    destroy() {
      tl.kill();
    }
  };
}
```

```svelte
<script>
  import { animate, timeline } from '$lib/actions/gsap.js';
</script>

<!-- Use as Svelte action -->
<div use:animate={{ duration: 1, x: 100, ease: "power2.out" }}>
  Animated with action
</div>

<div use:timeline={[
  { selector: '.item', duration: 0.5, x: 50, stagger: 0.1 },
  { selector: '.title', duration: 0.8, opacity: 1, delay: 0.5 }
]}>
  <h2 class="title">Title</h2>
  <div class="item">Item 1</div>
  <div class="item">Item 2</div>
</div>
```

## Plugin Integration

### ScrollTrigger Setup
```javascript
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

// Register plugin
gsap.registerPlugin(ScrollTrigger);

onMount(() => {
  gsap.to(elementRef, {
    scrollTrigger: {
      trigger: elementRef,
      start: "top 80%",
      end: "bottom 20%",
      scrub: 1,
      markers: true // Remove in production
    },
    x: 100,
    rotation: 360
  });
});
```

### Common Plugins
```javascript
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { TextPlugin } from 'gsap/TextPlugin';
import { MorphSVGPlugin } from 'gsap/MorphSVGPlugin';
import { MotionPathPlugin } from 'gsap/MotionPathPlugin';

gsap.registerPlugin(ScrollTrigger, TextPlugin, MorphSVGPlugin, MotionPathPlugin);
```

## Lifecycle Management & Cleanup

### Proper Cleanup Pattern
```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  
  let animations = [];
  let timelines = [];
  let scrollTriggers = [];
  
  onMount(() => {
    // Store references to all animations
    const tl = gsap.timeline();
    timelines.push(tl);
    
    const anim = gsap.to('.element', { duration: 1, x: 100 });
    animations.push(anim);
    
    const st = ScrollTrigger.create({
      trigger: '.trigger',
      onEnter: () => console.log('entered')
    });
    scrollTriggers.push(st);
  });
  
  onDestroy(() => {
    // Clean up all animations and prevent memory leaks
    animations.forEach(anim => anim.kill());
    timelines.forEach(tl => tl.kill());
    scrollTriggers.forEach(st => st.kill());
    
    // Clear arrays
    animations.length = 0;
    timelines.length = 0;
    scrollTriggers.length = 0;
  });
</script>
```

### Reactive Cleanup
```svelte
<script>
  import { onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  
  export let isVisible = false;
  let animation;
  
  $: if (isVisible) {
    animation?.kill(); // Kill previous animation
    animation = gsap.to('.element', {
      duration: 0.5,
      opacity: 1,
      scale: 1
    });
  } else {
    animation?.kill();
    animation = gsap.to('.element', {
      duration: 0.3,
      opacity: 0,
      scale: 0.8
    });
  }
  
  onDestroy(() => {
    animation?.kill();
  });
</script>
```

## Performance Best Practices

### 1. Use transform and opacity for best performance
```javascript
// Good - GPU accelerated
gsap.to(element, { x: 100, y: 50, scale: 1.2, opacity: 0.8 });

// Avoid - causes layout/paint
gsap.to(element, { left: 100, top: 50, width: 200, height: 150 });
```

### 2. Batch DOM reads/writes
```javascript
// Good - batch operations
gsap.set(elements, { opacity: 0 });
gsap.to(elements, { opacity: 1, stagger: 0.1 });

// Avoid - multiple DOM operations
elements.forEach(el => {
  gsap.set(el, { opacity: 0 });
  gsap.to(el, { opacity: 1 });
});
```

### 3. Use will-change for complex animations
```css
.animated-element {
  will-change: transform, opacity;
}
```

## Common SvelteKit Patterns

### Page Transitions
```svelte
<!-- src/app.html or layout -->
<script>
  import { page } from '$app/stores';
  import { gsap } from 'gsap';
  import { onMount } from 'svelte';
  
  let pageContainer;
  
  onMount(() => {
    // Page enter animation
    gsap.from(pageContainer, {
      duration: 0.5,
      opacity: 0,
      y: 20,
      ease: "power2.out"
    });
  });
  
  // React to route changes
  $: if ($page.url.pathname) {
    gsap.from(pageContainer, {
      duration: 0.3,
      opacity: 0,
      x: 20
    });
  }
</script>

<main bind:this={pageContainer}>
  <slot />
</main>
```

### Component Enter/Exit Animations
```svelte
<script>
  import { createEventDispatcher, onMount } from 'svelte';
  import { gsap } from 'gsap';
  
  const dispatch = createEventDispatcher();
  
  export let show = false;
  let container;
  
  $: if (show) {
    enter();
  } else {
    exit();
  }
  
  function enter() {
    gsap.fromTo(container, 
      { opacity: 0, scale: 0.8, y: 20 },
      { 
        opacity: 1, 
        scale: 1, 
        y: 0, 
        duration: 0.4,
        ease: "back.out(1.7)",
        onComplete: () => dispatch('entered')
      }
    );
  }
  
  function exit() {
    gsap.to(container, {
      opacity: 0,
      scale: 0.9,
      y: -10,
      duration: 0.3,
      ease: "power2.in",
      onComplete: () => dispatch('exited')
    });
  }
</script>

{#if show}
  <div bind:this={container}>
    <slot />
  </div>
{/if}
```

## SSR Considerations

### Disable SSR for GSAP components
```javascript
// +page.js or +layout.js
export const ssr = false;
```

### Or check for browser environment
```svelte
<script>
  import { browser } from '$app/environment';
  import { onMount } from 'svelte';
  
  onMount(() => {
    if (browser) {
      import('gsap').then(({ gsap }) => {
        gsap.to('.element', { duration: 1, x: 100 });
      });
    }
  });
</script>
```

## Troubleshooting

### Common Issues

1. **Elements not found**: Ensure DOM is ready with `onMount`
2. **Animations not working**: Check element references and CSS transforms
3. **Memory leaks**: Always clean up animations in `onDestroy`
4. **SSR conflicts**: Disable SSR or check browser environment

### Debug Tips
```javascript
// Enable GSAP's debug mode
gsap.globalTimeline.eventCallback("onUpdate", () => {
  console.log("Global timeline progress:", gsap.globalTimeline.progress());
});

// Check if element exists
if (!element) {
  console.warn("Element not found for animation");
  return;
}

// Use markers for ScrollTrigger debugging
ScrollTrigger.create({
  trigger: element,
  markers: true, // Remove in production
  onToggle: (self) => console.log("toggled, isActive:", self.isActive)
});
```

## Useful Resources

- **GSAP Documentation**: https://gsap.com/docs/v3/
- **ScrollTrigger Guide**: https://gsap.com/scrolltrigger/
- **GSAP Forum**: https://gsap.com/community/
- **SvelteKit GSAP Examples**: https://github.com/mcgrealife/gsap-sveltekit
- **Interactive Learning**: https://gsap.com/learning/

## Licensing

GSAP is now 100% FREE including all bonus plugins (SplitText, MorphSVG, etc.) for commercial use, thanks to Webflow sponsorship.