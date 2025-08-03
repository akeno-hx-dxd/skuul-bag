# GSAP Cheatsheet - Complete Reference

## Core Animation Methods

### Basic Tween Methods
```javascript
// Animate TO specified values
gsap.to(".selector", {
  x: 100,
  y: 50,
  opacity: 0.5,
  duration: 1,
  ease: "power2.out"
});

// Animate FROM specified values to current state
gsap.from(".selector", {
  x: -100,
  opacity: 0,
  duration: 1
});

// Animate FROM one set of values TO another
gsap.fromTo(".selector", 
  { x: -100, opacity: 0 },        // from
  { x: 100, opacity: 1, duration: 1 }  // to
);

// Set values immediately (no animation)
gsap.set(".selector", {
  x: 100,
  scale: 1.5,
  opacity: 0
});
```

## Transform Properties

### 2D Transforms
```javascript
{
  x: 100,              // translateX(100px)
  y: 50,               // translateY(50px)
  rotation: 45,        // rotate(45deg)
  rotationX: 45,       // rotateX(45deg)
  rotationY: 45,       // rotateY(45deg)
  scale: 1.5,          // scale(1.5)
  scaleX: 2,           // scaleX(2)
  scaleY: 0.5,         // scaleY(0.5)
  skew: 15,            // skew(15deg)
  skewX: 15,           // skewX(15deg)
  skewY: 10,           // skewY(10deg)
  transformOrigin: "50% 50%"  // transform-origin
}
```

### 3D Transforms
```javascript
{
  z: 100,              // translateZ(100px)
  rotationZ: 45,       // rotateZ(45deg)
  scaleZ: 2,           // scaleZ(2)
  perspective: 400,    // perspective(400px)
  force3D: true        // force hardware acceleration
}
```

## CSS Properties
```javascript
{
  // Colors
  color: "#ff0000",
  backgroundColor: "rgb(255, 0, 0)",
  borderColor: "hsl(0, 100%, 50%)",
  
  // Dimensions
  width: 200,
  height: 100,
  fontSize: "2em",
  lineHeight: 1.5,
  
  // Positioning
  left: 100,
  top: 50,
  right: 0,
  bottom: 0,
  
  // Display
  opacity: 0.5,
  visibility: "hidden",
  display: "none",
  
  // Border & Layout
  borderRadius: 10,
  padding: 20,
  margin: 10,
  borderWidth: 5
}
```

## Timeline Creation & Control

### Basic Timeline
```javascript
// Create timeline
let tl = gsap.timeline();

// Timeline with properties
let tl = gsap.timeline({
  delay: 0.5,           // delay entire timeline
  paused: true,         // start paused
  repeat: 2,            // repeat 2 times (3 total)
  repeatDelay: 1,       // 1 second between repeats
  repeatRefresh: true,  // re-record start/end values on repeat
  yoyo: true,           // alternate direction each repeat
  defaults: {           // default properties for children
    duration: 1,
    ease: "power2.out"
  }
});

// Chain animations
tl.to(".box1", { x: 100, duration: 1 })
  .to(".box2", { y: 50, duration: 0.5 })
  .to(".box3", { opacity: 0, duration: 1 });
```

### Timeline Methods
```javascript
// Adding content
tl.add(gsap.to(".class", { x: 100 }), "label");  // add tween at label
tl.call(myFunction, ["param1", "param2"], "+=1"); // call function
tl.addLabel("myLabel", "+=0.5");                  // add label
tl.addPause("myLabel");                           // add pause point

// Utility methods
tl.getChildren();        // get array of timeline's children
tl.clear();             // empty the timeline
tl.removeLabel("label"); // remove specific label
tl.shiftChildren(2);    // shift all children 2 seconds later
```

## Position Parameter
```javascript
// Absolute time
tl.to(".class", { x: 100 }, 2);        // start at 2 seconds

// Relative to timeline end
tl.to(".class", { x: 100 }, "+=1");    // 1 second after timeline end
tl.to(".class", { x: 100 }, "-=0.5");  // overlap by 0.5 seconds

// Relative to labels
tl.to(".class", { x: 100 }, "myLabel");     // at label
tl.to(".class", { x: 100 }, "myLabel+=1");  // 1 second after label
tl.to(".class", { x: 100 }, "myLabel-=0.5"); // 0.5 seconds before label

// Relative to previous animation
tl.to(".class", { x: 100 }, "<");      // start with previous
tl.to(".class", { x: 100 }, "<0.5");   // 0.5 seconds after previous starts
tl.to(".class", { x: 100 }, ">");      // start when previous ends
```

## Control Methods
```javascript
// Playback control
anim.play();            // play forward
anim.pause();           // pause
anim.resume();          // resume (respects direction)
anim.reverse();         // reverse direction
anim.restart();         // restart from beginning

// Time control
anim.timeScale(2);      // double speed (0.5 = half speed)
anim.seek(1.5);         // jump to 1.5 seconds
anim.progress(0.5);     // jump to 50% progress
anim.totalProgress(0.8); // jump to 80% (includes repeats)

// State
anim.isActive();        // true if currently animating
anim.paused();          // true if paused
anim.reversed();        // true if reversed

// Cleanup
anim.kill();            // destroy animation
anim.invalidate();      // clear recorded start/end values
```

## Easing Functions

### Built-in Eases
```javascript
// Linear
"none"

// Power (quad, cubic, quart, quint)
"power1.out"     // default ease
"power2.in"
"power3.inOut"
"power4.out"

// Back
"back.out(1.7)"  // overshoot amount
"back.in"
"back.inOut"

// Bounce
"bounce.out"
"bounce.in"
"bounce.inOut"

// Elastic
"elastic.out(1, 0.3)"  // amplitude, period
"elastic.in"
"elastic.inOut"

// Other
"circ.out"
"expo.out"
"sine.out"
```

### Custom Eases
```javascript
// Custom Ease (requires CustomEase plugin)
"M0,0 C0.126,0.382 0.282,0.674 0.44,0.822 0.632,1.002 0.818,1.001 1,1"

// Steps
"steps(5)"       // 5 equal steps

// Rough (requires RoughEase plugin)
"rough({ template: power1.out, strength: 1, points: 20 })"
```

## Special Properties

### Animation Properties
```javascript
{
  duration: 2,          // animation duration
  delay: 0.5,           // delay before start
  ease: "power2.out",   // easing function
  paused: true,         // start paused
  overwrite: "auto",    // overwrite mode
  immediateRender: false, // don't apply values immediately
  lazy: false,          // don't wait for first tick
  force3D: true,        // force hardware acceleration
  autoAlpha: 0          // combination of opacity and visibility
}
```

### Repeat Properties
```javascript
{
  repeat: 3,            // repeat 3 times (4 total)
  repeatDelay: 1,       // delay between repeats
  repeatRefresh: true,  // refresh start/end values
  yoyo: true,           // alternate direction
  yoyoEase: "power2.in" // ease for yoyo returns
}
```

### Stagger Properties
```javascript
{
  stagger: 0.1,         // 0.1 second between each
  stagger: {
    amount: 1.5,        // total time for all staggers
    from: "center",     // start from center
    grid: [7, 3],       // 7x3 grid
    axis: "y",          // stagger along y-axis
    ease: "power2.inOut"
  }
}
```

## Event Callbacks
```javascript
{
  onComplete: myFunction,       // when animation completes
  onStart: myFunction,          // when animation starts
  onUpdate: myFunction,         // every frame during animation
  onRepeat: myFunction,         // each time animation repeats
  onReverseComplete: myFunction, // when reverse completes
  onInterrupt: myFunction,      // when animation is interrupted
  onOverwrite: myFunction,      // when animation is overwritten
  
  // Callback parameters
  onCompleteParams: ["param1", "param2"],
  onStartParams: [this],
  onUpdateParams: ["{self}"]    // {self} passes the tween
}
```

## ScrollTrigger Plugin

### Basic ScrollTrigger
```javascript
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

// Simple trigger
gsap.to(".box", {
  scrollTrigger: ".box",  // trigger when .box enters viewport
  x: 500
});

// With configuration
gsap.to(".box", {
  scrollTrigger: {
    trigger: ".box",
    start: "top 80%",     // when top of trigger hits 80% of viewport
    end: "bottom 20%",    // when bottom of trigger hits 20% of viewport
    scrub: true,          // tie animation to scroll position
    pin: true,            // pin trigger element during animation
    markers: true,        // show start/end markers (debugging)
    toggleActions: "play pause resume reverse",
    snap: { snapTo: 0.5, duration: 0.3 }
  },
  x: 500
});

// ScrollTrigger.create()
ScrollTrigger.create({
  trigger: "#element",
  start: "top center",
  end: "bottom center",
  onEnter: () => console.log("entered"),
  onLeave: () => console.log("left"),
  onUpdate: self => console.log("progress:", self.progress),
  toggleClass: {
    targets: ".my-element",
    className: "active"
  }
});
```

### ScrollTrigger Batch
```javascript
ScrollTrigger.batch(".item", {
  onEnter: elements => gsap.from(elements, {
    opacity: 0, 
    y: 100, 
    stagger: 0.15
  }),
  onLeave: elements => gsap.to(elements, {
    opacity: 0, 
    y: -100, 
    stagger: 0.15
  }),
  onEnterBack: elements => gsap.to(elements, {
    opacity: 1, 
    y: 0, 
    stagger: 0.15
  }),
  onLeaveBack: elements => gsap.to(elements, {
    opacity: 0, 
    y: 100, 
    stagger: 0.15
  })
});
```

## Other Popular Plugins

### Draggable
```javascript
import { Draggable } from "gsap/Draggable";
gsap.registerPlugin(Draggable);

Draggable.create(".box", {
  type: "x,y",           // drag horizontally and vertically
  bounds: "#container",  // constrain to container
  inertia: true,         // momentum-based continuation
  onDrag: function() {
    console.log("dragging");
  }
});
```

### MotionPath
```javascript
import { MotionPathPlugin } from "gsap/MotionPathPlugin";
gsap.registerPlugin(MotionPathPlugin);

gsap.to(".box", {
  duration: 5,
  motionPath: {
    path: "#path",        // SVG path element
    align: "#path",       // align to path
    alignOrigin: [0.5, 0.5],
    autoRotate: true      // rotate to face direction
  }
});
```

### TextPlugin
```javascript
import { TextPlugin } from "gsap/TextPlugin";
gsap.registerPlugin(TextPlugin);

gsap.to("#text", {
  duration: 2,
  text: "This text will animate in!",
  ease: "none",
  delimiter: " "         // animate word by word
});
```

### Flip
```javascript
import { Flip } from "gsap/Flip";
gsap.registerPlugin(Flip);

// Capture initial state
const state = Flip.getState(".box");

// Make changes (add class, move DOM elements, etc.)
document.querySelector(".box").classList.add("moved");

// Animate to new state
Flip.from(state, {
  duration: 1,
  ease: "power2.inOut",
  absolute: true        // use absolute positioning during animation
});
```

## Utility Functions

### gsap.utils
```javascript
// Number utilities
gsap.utils.clamp(0, 100, 150);        // returns 100
gsap.utils.lerp(0, 100, 0.5);         // returns 50
gsap.utils.normalize(50, 0, 100);     // returns 0.5
gsap.utils.mapRange(0, 100, 0, 1, 50); // returns 0.5

// Array utilities
gsap.utils.toArray(".class");         // convert to array
gsap.utils.shuffle([1,2,3,4,5]);      // randomize array order
gsap.utils.distribute({               // distribute values
  base: 0,
  amount: 100,
  from: "center"
});

// Selector utilities
gsap.utils.selector("#container");    // scoped selector function

// Random utilities
gsap.utils.random(0, 100);           // random number
gsap.utils.random([1, 5, 10, 20]);   // random from array
gsap.utils.random(0, 100, 5);        // random in steps of 5
```

### Global Configuration
```javascript
// Set global defaults
gsap.defaults({
  duration: 1,
  ease: "power2.out"
});

// Global config
gsap.config({
  autoSleep: 60,        // automatically sleep after 60 seconds
  force3D: false,       // don't force 3D by default
  nullTargetWarn: false, // don't warn about null targets
  trialWarn: false      // don't show trial warnings
});

// Register custom effects
gsap.registerEffect({
  name: "fade",
  effect: (targets, config) => {
    return gsap.to(targets, {
      duration: config.duration,
      opacity: 0
    });
  },
  defaults: { duration: 2 },
  extendTimeline: true
});

// Use custom effect
gsap.effects.fade(".box", { duration: 3 });
tl.fade(".box", { duration: 3 });  // if extendTimeline: true
```

## SvelteKit Integration Patterns

### Basic Component Integration
```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  
  let element;
  let animation;
  
  onMount(() => {
    animation = gsap.from(element, {
      duration: 1,
      y: 50,
      opacity: 0,
      ease: "back.out(1.7)"
    });
  });
  
  onDestroy(() => {
    animation?.kill();
  });
</script>

<div bind:this={element}>Animated content</div>
```

### Reactive Animations
```svelte
<script>
  import { gsap } from 'gsap';
  
  export let isVisible = false;
  let element;
  
  $: if (element) {
    gsap.to(element, {
      duration: 0.3,
      opacity: isVisible ? 1 : 0,
      scale: isVisible ? 1 : 0.8,
      ease: "power2.out"
    });
  }
</script>

<div bind:this={element}>Toggle content</div>
```

### Custom Action
```javascript
// lib/actions/gsap.js
import { gsap } from 'gsap';

export function animate(node, params = {}) {
  const animation = gsap.from(node, {
    duration: 0.6,
    y: 20,
    opacity: 0,
    ease: "power2.out",
    ...params
  });
  
  return {
    destroy() {
      animation.kill();
    }
  };
}
```

```svelte
<script>
  import { animate } from '$lib/actions/gsap.js';
</script>

<div use:animate={{ duration: 1, x: 100 }}>
  Animated with action
</div>
```

## Quick Reference

### Most Used Properties
- `x`, `y`, `scale`, `rotation`, `opacity`
- `duration`, `delay`, `ease`
- `stagger`, `repeat`, `yoyo`

### Most Used Methods
- `gsap.to()`, `gsap.from()`, `gsap.timeline()`
- `.play()`, `.pause()`, `.reverse()`, `.restart()`
- `.kill()`, `.progress()`, `.timeScale()`

### Most Used Plugins
- ScrollTrigger (scroll-based animations)
- Draggable (drag and drop)
- TextPlugin (text animations)
- Flip (layout animations)

### Performance Tips
- Use `transform` properties (`x`, `y`, `scale`, etc.) over layout properties
- Use `autoAlpha` instead of separate `opacity` and `visibility`
- Set `force3D: true` for complex animations
- Kill animations in component cleanup
- Use `will-change: transform` CSS for animated elements