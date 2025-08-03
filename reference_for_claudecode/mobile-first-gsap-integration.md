# Mobile-First GSAP Integration for SvelteKit

## Overview

This guide covers mobile-first GSAP integration patterns specifically tailored for SvelteKit projects, focusing on performance, touch interactions, responsive design, and dynamic layouts optimized for mobile devices.

## Research Sources

1. **Oxygen4Fun Mobile Menu Tutorial** - https://oxygen4fun.supadezign.com/tutorials/how-to-make-an-animated-mobile-menu-with-gsap/
2. **CSS-Tricks Responsive Animations** - https://css-tricks.com/responsive-animations-for-every-screen-size-and-device/
3. **GSAP Community Forums** - Mobile optimization discussions and best practices
4. **Joy of Code Svelte Animations** - https://joyofcode.xyz/impossible-layout-animations-with-svelte
5. **GreenSock Documentation** - matchMedia() and Observer plugins

## Mobile-First Philosophy

### Start Small, Scale Up
```javascript
// Mobile-first breakpoint approach
const breakpoints = {
  mobile: '(max-width: 767px)',
  tablet: '(min-width: 768px) and (max-width: 1023px)',
  desktop: '(min-width: 1024px)',
  reduced: '(prefers-reduced-motion: reduce)'
};
```

### Performance Priorities
1. **Minimize animations on mobile** - Sometimes the best mobile animation is no animation
2. **Use hardware acceleration** - Prefer `transform` and `opacity` properties
3. **Respect user preferences** - Always check `prefers-reduced-motion`
4. **Optimize for touch** - Larger tap targets, appropriate timing

## Mobile Menu Implementation

### Basic Mobile Menu with GSAP
```svelte
<!-- src/lib/components/MobileMenu.svelte -->
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  
  export let isOpen = false;
  
  let menuOverlay;
  let menuItems;
  let hamburgerLines;
  let timeline;
  
  onMount(() => {
    // Initialize menu in closed state
    gsap.set(menuOverlay, {
      x: '100%',
      visibility: 'hidden'
    });
    
    gsap.set(menuItems, {
      x: 50,
      opacity: 0
    });
    
    // Create reusable timeline
    timeline = gsap.timeline({ paused: true })
      .set(menuOverlay, { visibility: 'visible' })
      .to(menuOverlay, {
        duration: 0.4,
        x: '0%',
        ease: 'power2.out'
      })
      .to(menuItems, {
        duration: 0.6,
        x: 0,
        opacity: 1,
        stagger: 0.1,
        ease: 'back.out(1.7)'
      }, '-=0.2')
      .to(hamburgerLines, {
        duration: 0.3,
        rotation: 45,
        transformOrigin: 'center'
      }, 0);
  });
  
  // Reactive menu state
  $: if (timeline) {
    if (isOpen) {
      timeline.play();
      // Prevent body scroll on mobile
      document.body.style.overflow = 'hidden';
    } else {
      timeline.reverse();
      document.body.style.overflow = '';
    }
  }
  
  onDestroy(() => {
    timeline?.kill();
    document.body.style.overflow = '';
  });
  
  function handleMenuItemClick() {
    isOpen = false;
  }
</script>

<!-- Hamburger Icon -->
<button 
  class="hamburger-btn"
  on:click={() => isOpen = !isOpen}
  aria-label="Toggle menu"
  aria-expanded={isOpen}
>
  <span bind:this={hamburgerLines} class="line"></span>
  <span bind:this={hamburgerLines} class="line"></span>
  <span bind:this={hamburgerLines} class="line"></span>
</button>

<!-- Mobile Menu Overlay -->
<nav 
  bind:this={menuOverlay}
  class="mobile-menu-overlay"
  class:open={isOpen}
>
  <ul class="menu-list">
    <li bind:this={menuItems}>
      <a href="/" on:click={handleMenuItemClick}>Home</a>
    </li>
    <li bind:this={menuItems}>
      <a href="/about" on:click={handleMenuItemClick}>About</a>
    </li>
    <li bind:this={menuItems}>
      <a href="/contact" on:click={handleMenuItemClick}>Contact</a>
    </li>
  </ul>
</nav>

<style>
  .hamburger-btn {
    display: flex;
    flex-direction: column;
    gap: 4px;
    background: none;
    border: none;
    padding: 8px;
    cursor: pointer;
    z-index: 1001;
  }
  
  .line {
    width: 24px;
    height: 2px;
    background-color: currentColor;
    transition: transform 0.3s ease;
  }
  
  .mobile-menu-overlay {
    position: fixed;
    top: 0;
    right: 0;
    width: 100vw;
    height: 100vh;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    z-index: 1000;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .menu-list {
    list-style: none;
    padding: 0;
    margin: 0;
    text-align: center;
  }
  
  .menu-list li {
    margin: 2rem 0;
  }
  
  .menu-list a {
    color: white;
    text-decoration: none;
    font-size: 1.5rem;
    font-weight: 600;
    padding: 1rem 2rem;
    border-radius: 8px;
    transition: background-color 0.3s ease;
  }
  
  .menu-list a:hover {
    background-color: rgba(255, 255, 255, 0.1);
  }
</style>
```

## Responsive GSAP with matchMedia()

### Breakpoint-Based Animations
```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  
  let element;
  let ctx; // GSAP context for cleanup
  
  onMount(() => {
    ctx = gsap.context(() => {
      // Mobile animations
      gsap.matchMedia().add("(max-width: 767px)", () => {
        gsap.fromTo(element, 
          { y: 20, opacity: 0 },
          { 
            y: 0, 
            opacity: 1, 
            duration: 0.6,
            ease: "power2.out"
          }
        );
      });
      
      // Tablet animations
      gsap.matchMedia().add("(min-width: 768px) and (max-width: 1023px)", () => {
        gsap.fromTo(element,
          { x: -30, opacity: 0 },
          { 
            x: 0, 
            opacity: 1, 
            duration: 0.8,
            ease: "back.out(1.7)"
          }
        );
      });
      
      // Desktop animations
      gsap.matchMedia().add("(min-width: 1024px)", () => {
        gsap.fromTo(element,
          { scale: 0.8, opacity: 0 },
          { 
            scale: 1, 
            opacity: 1, 
            duration: 1,
            ease: "elastic.out(1, 0.3)"
          }
        );
      });
      
      // Respect reduced motion preference
      gsap.matchMedia().add("(prefers-reduced-motion: reduce)", () => {
        gsap.set(element, { opacity: 1 }); // Instant appearance, no animation
      });
    });
  });
  
  onDestroy(() => {
    ctx?.revert(); // Clean up all matchMedia animations
  });
</script>

<div bind:this={element}>Responsive animated element</div>
```

## Touch Interactions and Mobile Performance

### GSAP Observer for Touch Events
```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  import { Observer } from 'gsap/Observer';
  
  gsap.registerPlugin(Observer);
  
  let cardElement;
  let observer;
  
  onMount(() => {
    // Detect touch capability
    const isTouch = gsap.utils.toArray('.touch-test').length > 0 || 
                   'ontouchstart' in window;
    
    if (isTouch) {
      observer = Observer.create({
        target: cardElement,
        type: "touch,pointer",
        onPress: () => {
          gsap.to(cardElement, {
            scale: 0.95,
            duration: 0.1,
            ease: "power2.out"
          });
        },
        onRelease: () => {
          gsap.to(cardElement, {
            scale: 1,
            duration: 0.2,
            ease: "back.out(1.7)"
          });
        },
        onDrag: (self) => {
          gsap.set(cardElement, {
            x: self.deltaX,
            y: self.deltaY
          });
        },
        onDragEnd: () => {
          gsap.to(cardElement, {
            x: 0,
            y: 0,
            duration: 0.5,
            ease: "elastic.out(1, 0.3)"
          });
        }
      });
    }
  });
  
  onDestroy(() => {
    observer?.kill();
  });
</script>

<div bind:this={cardElement} class="touch-card">
  Swipe me on mobile!
</div>

<style>
  .touch-card {
    width: 200px;
    height: 200px;
    background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
    border-radius: 16px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-weight: bold;
    cursor: pointer;
    user-select: none;
    touch-action: none; /* Prevent default touch behaviors */
  }
</style>
```

## Dynamic Layout Animations

### Mobile-First Grid Layout
```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  import { Flip } from 'gsap/Flip';
  
  gsap.registerPlugin(Flip);
  
  export let items = [];
  export let layout = 'grid'; // 'grid' | 'list' | 'stack'
  
  let container;
  let itemElements = [];
  
  // Reactive layout changes
  $: if (container && itemElements.length > 0) {
    animateLayout(layout);
  }
  
  function animateLayout(newLayout) {
    // Capture current state
    const state = Flip.getState(itemElements);
    
    // Apply new layout classes
    container.setAttribute('data-layout', newLayout);
    
    // Animate to new positions
    Flip.from(state, {
      duration: 0.6,
      ease: "power2.inOut",
      stagger: 0.05,
      absolute: true,
      onComplete: () => {
        // Reset any Flip-applied styles
        gsap.set(itemElements, { clearProps: "all" });
      }
    });
  }
  
  onMount(() => {
    // Initial animation
    gsap.from(itemElements, {
      duration: 0.8,
      y: 30,
      opacity: 0,
      stagger: 0.1,
      ease: "back.out(1.7)"
    });
  });
</script>

<div 
  bind:this={container} 
  class="layout-container" 
  data-layout={layout}
>
  {#each items as item, i}
    <div 
      bind:this={itemElements[i]}
      class="layout-item"
      data-item-id={item.id}
    >
      <img src={item.image} alt={item.title} />
      <h3>{item.title}</h3>
      <p>{item.description}</p>
    </div>
  {/each}
</div>

<!-- Layout Controls -->
<div class="layout-controls">
  <button on:click={() => layout = 'grid'}>Grid</button>
  <button on:click={() => layout = 'list'}>List</button>
  <button on:click={() => layout = 'stack'}>Stack</button>
</div>

<style>
  .layout-container {
    transition: all 0.3s ease;
  }
  
  /* Mobile-first grid */
  .layout-container[data-layout="grid"] {
    display: grid;
    grid-template-columns: 1fr;
    gap: 1rem;
  }
  
  /* Tablet grid */
  @media (min-width: 768px) {
    .layout-container[data-layout="grid"] {
      grid-template-columns: repeat(2, 1fr);
    }
  }
  
  /* Desktop grid */
  @media (min-width: 1024px) {
    .layout-container[data-layout="grid"] {
      grid-template-columns: repeat(3, 1fr);
    }
  }
  
  .layout-container[data-layout="list"] {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }
  
  .layout-container[data-layout="stack"] {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2rem;
  }
  
  .layout-item {
    background: white;
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease;
  }
  
  .layout-item:hover {
    transform: translateY(-4px);
  }
  
  .layout-item img {
    width: 100%;
    height: 200px;
    object-fit: cover;
  }
  
  .layout-item h3, 
  .layout-item p {
    padding: 0 1rem;
  }
  
  .layout-controls {
    display: flex;
    gap: 1rem;
    margin: 2rem 0;
    justify-content: center;
  }
  
  .layout-controls button {
    padding: 0.5rem 1rem;
    border: 2px solid #3b82f6;
    background: white;
    color: #3b82f6;
    border-radius: 8px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
  }
  
  .layout-controls button:hover {
    background: #3b82f6;
    color: white;
  }
</style>
```

## Mobile Performance Optimization

### Performance-First Animation Patterns
```javascript
// Mobile performance optimization utilities
export const mobileOptimizations = {
  // Detect device capabilities
  detectDevice() {
    const isLowEnd = navigator.hardwareConcurrency <= 2 || 
                    navigator.deviceMemory <= 4;
    const isTouch = 'ontouchstart' in window;
    const prefersReduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    
    return { isLowEnd, isTouch, prefersReduced };
  },
  
  // Adaptive animation settings
  getOptimalSettings() {
    const { isLowEnd, prefersReduced } = this.detectDevice();
    
    if (prefersReduced) {
      return {
        duration: 0,
        ease: "none",
        stagger: 0
      };
    }
    
    if (isLowEnd) {
      return {
        duration: 0.3,
        ease: "power2.out",
        stagger: 0.05
      };
    }
    
    return {
      duration: 0.6,
      ease: "back.out(1.7)",
      stagger: 0.1
    };
  },
  
  // Optimized GSAP settings for mobile
  applyMobileSettings(animation) {
    const settings = this.getOptimalSettings();
    return gsap.to(animation.target, {
      ...animation.props,
      ...settings,
      force3D: true, // Force hardware acceleration
      willChange: "transform, opacity"
    });
  }
};
```

### Mobile-Optimized Page Transitions
```svelte
<!-- src/lib/components/PageTransition.svelte -->
<script>
  import { onMount, onDestroy } from 'svelte';
  import { page } from '$app/stores';
  import { gsap } from 'gsap';
  import { mobileOptimizations } from '$lib/utils/performance';
  
  let pageContainer;
  let isTransitioning = false;
  
  $: if ($page.url.pathname && !isTransitioning) {
    transitionPage();
  }
  
  async function transitionPage() {
    isTransitioning = true;
    
    const { isTouch, prefersReduced } = mobileOptimizations.detectDevice();
    
    if (prefersReduced) {
      // Skip animation for users who prefer reduced motion
      isTransitioning = false;
      return;
    }
    
    const settings = mobileOptimizations.getOptimalSettings();
    
    // Mobile-optimized page transition
    const timeline = gsap.timeline();
    
    if (isTouch) {
      // Simpler mobile transition
      timeline
        .to(pageContainer, {
          opacity: 0,
          duration: settings.duration * 0.5,
          ease: settings.ease
        })
        .to(pageContainer, {
          opacity: 1,
          duration: settings.duration,
          ease: settings.ease
        });
    } else {
      // More complex desktop transition
      timeline
        .to(pageContainer, {
          y: -20,
          opacity: 0,
          duration: settings.duration,
          ease: settings.ease
        })
        .to(pageContainer, {
          y: 0,
          opacity: 1,
          duration: settings.duration,
          ease: "back.out(1.7)"
        });
    }
    
    await timeline.play();
    isTransitioning = false;
  }
  
  onMount(() => {
    // Initial page load animation
    const settings = mobileOptimizations.getOptimalSettings();
    
    gsap.from(pageContainer, {
      opacity: 0,
      y: 20,
      duration: settings.duration,
      ease: settings.ease
    });
  });
</script>

<main bind:this={pageContainer} class="page-container">
  <slot />
</main>

<style>
  .page-container {
    min-height: 100vh;
    will-change: transform, opacity;
  }
</style>
```

## Accessibility and User Experience

### Respecting User Preferences
```svelte
<script>
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  
  let prefersReducedMotion = false;
  
  onMount(() => {
    // Check user's motion preference
    const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)');
    prefersReducedMotion = mediaQuery.matches;
    
    // Listen for changes
    mediaQuery.addEventListener('change', (e) => {
      prefersReducedMotion = e.matches;
    });
    
    // Set up GSAP defaults based on preference
    if (prefersReducedMotion) {
      gsap.globalTimeline.timeScale(100); // Speed up dramatically
      gsap.defaults({ duration: 0.01 }); // Near-instant
    }
  });
  
  function createAccessibleAnimation(element, props) {
    if (prefersReducedMotion) {
      // Instant state change
      gsap.set(element, {
        ...props,
        duration: 0
      });
    } else {
      // Full animation
      gsap.to(element, props);
    }
  }
</script>
```

## Best Practices Summary

### Mobile-First Principles
1. **Start with mobile constraints** - Design animations for the smallest screen first
2. **Progressive enhancement** - Add complexity for larger screens and capable devices
3. **Touch-friendly interactions** - Larger tap targets, appropriate feedback
4. **Performance awareness** - Monitor frame rates, respect device limitations

### GSAP Integration
1. **Use matchMedia()** - Different animations for different breakpoints
2. **Leverage Observer** - Unified touch/mouse/pointer event handling
3. **Flip plugin** - Smooth layout transitions
4. **Context management** - Proper cleanup with gsap.context()

### Performance Optimization
1. **Hardware acceleration** - Prefer transform and opacity
2. **Conditional animations** - Reduce or remove on low-end devices
3. **Respect preferences** - Honor prefers-reduced-motion
4. **Cleanup properly** - Prevent memory leaks in SPAs

### SvelteKit Specific
1. **Use onMount/onDestroy** - Proper lifecycle management
2. **Reactive patterns** - Trigger animations on state changes
3. **SSR considerations** - Handle client-side only animations
4. **Component actions** - Reusable animation behaviors

This mobile-first approach ensures your GSAP animations work beautifully across all devices while maintaining performance and accessibility standards.