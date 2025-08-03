<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	
	let heroContainer: HTMLElement;
	let heroTitle: HTMLElement;
	let heroSubtitle: HTMLElement;
	let scrollIndicator: HTMLElement;
	let floatingIcons: HTMLElement[] = [];
	
	const iconData = [
		{ src: '/GsapResources/3dicons/3dicons-pencil-dynamic-color.png', name: 'pencil' },
		{ src: '/GsapResources/3dicons/3dicons-notebook-dynamic-color.png', name: 'notebook' },
		{ src: '/GsapResources/3dicons/3dicons-scissor-dynamic-color.png', name: 'scissor' },
		{ src: '/GsapResources/3dicons/3dicons-mail-dynamic-color.png', name: 'mail' },
		{ src: '/GsapResources/3dicons/3dicons-copy-dynamic-color.png', name: 'copy' }
	];
	
	onMount(() => {
		// Hero entrance animation
		const tl = gsap.timeline();
		
		tl.from(heroTitle, {
			duration: 1.5,
			y: 100,
			opacity: 0,
			ease: "power4.out",
			delay: 0.5
		})
		.from(heroSubtitle, {
			duration: 1,
			y: 50,
			opacity: 0,
			ease: "power3.out"
		}, "-=0.5")
		.from(scrollIndicator, {
			duration: 0.8,
			y: 30,
			opacity: 0,
			ease: "back.out(1.7)"
		}, "-=0.3");
		
		// Floating icons animation - increased spread
		floatingIcons.forEach((icon, index) => {
			gsap.set(icon, {
				x: gsap.utils.random(-400, 400),
				y: gsap.utils.random(-200, 200),
				rotation: gsap.utils.random(-15, 15),
				scale: gsap.utils.random(0.8, 1.2)
			});
			
			gsap.from(icon, {
				duration: 1,
				scale: 0,
				rotation: 360,
				ease: "back.out(1.7)",
				delay: 1 + (index * 0.1)
			});
			
			// Continuous floating animation - increased movement
			gsap.to(icon, {
				duration: gsap.utils.random(3, 5),
				y: "+=40",
				x: "+=30",
				rotation: "+=15",
				repeat: -1,
				yoyo: true,
				ease: "sine.inOut",
				delay: gsap.utils.random(0, 2)
			});
		});
		
		// Scroll indicator pulse
		gsap.to(".scroll-arrow", {
			duration: 1.5,
			y: 10,
			repeat: -1,
			yoyo: true,
			ease: "power2.inOut"
		});
	});
</script>

<svelte:head>
	<title>Skuulbag - Beyond Basic Supplies</title>
	<meta name="description" content="Quality stationery that doesn't break the bank. Discover our curated collection of office supplies, writing instruments, and printing materials." />
</svelte:head>

<section bind:this={heroContainer} class="rainbow-hero">
	<!-- Floating 3D Icons -->
	<div class="floating-icons">
		{#each iconData as icon, index}
			<div 
				bind:this={floatingIcons[index]}
				class="floating-icon gsap-optimized"
			>
				<img src={icon.src} alt={icon.name} />
			</div>
		{/each}
	</div>

	<!-- Hero Content -->
	<div class="hero-content">
		<h1 bind:this={heroTitle} class="hero-title font-milky-walky rainbow-text-gradient">
			Skuulbag
		</h1>
		<p bind:this={heroSubtitle} class="hero-subtitle font-indie-flower">
			Beyond Basic Supplies
		</p>
		<p class="hero-description font-jetbrains-mono">
			Quality stationery that doesn't break the bank. Discover our curated collection of office supplies, writing instruments, and printing materials.
		</p>
	</div>

	<!-- Scroll Indicator -->
	<div bind:this={scrollIndicator} class="scroll-indicator">
		<span class="font-indie-flower">Explore Collection</span>
		<div class="scroll-arrow">
			<svg width="24" height="24" viewBox="0 0 24 24" fill="none">
				<path d="M12 16L8 12H16L12 16Z" fill="currentColor"/>
			</svg>
		</div>
	</div>
</section>

<style>
	.rainbow-hero {
		min-height: 100vh;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		position: relative;
		overflow: hidden;
		background: url('/GsapResources/backgrounds/white_3d_background.jpg');
		background-size: cover;
		background-position: center;
		background-attachment: fixed;
	}

	.floating-icons {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		pointer-events: none;
		z-index: 1;
	}

	.floating-icon {
		position: absolute;
		top: 50%;
		left: 50%;
		opacity: 0.7;
		animation: iconGlow 4s ease-in-out infinite alternate;
	}

	.floating-icon img {
		width: 60px;
		height: 60px;
		/* Base glow will be overridden by animation */
	}

	.hero-content {
		text-align: center;
		z-index: 2;
		max-width: 800px;
		margin: 0 auto;
		padding: 2rem;
	}

	.hero-title {
		font-size: clamp(3rem, 8vw, 8rem);
		margin: 0 0 1rem 0;
		line-height: 0.9;
		font-weight: normal;
	}

	.hero-subtitle {
		font-size: clamp(1.2rem, 3vw, 2rem);
		margin: 0 0 2rem 0;
		color: var(--rainbow-dark);
		opacity: 0.9;
	}

	.hero-description {
		font-size: clamp(1rem, 2vw, 1.3rem);
		line-height: 1.6;
		color: var(--rainbow-dark);
		opacity: 0.8;
		max-width: 600px;
		margin: 0 auto;
		text-shadow: 0 0 20px rgba(255, 255, 255, 0.8), 0 0 40px rgba(255, 255, 255, 0.6);
		/* background: rgba(255, 255, 255, 0.1); */
		padding: 1.5rem 2rem;
		border-radius: 15px;
		backdrop-filter: blur(0.5px);
		border: 1px solid rgba(255, 255, 255, 0.2);
	}

	.scroll-indicator {
		position: absolute;
		bottom: 2rem;
		left: 50%;
		transform: translateX(-50%);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.5rem;
		color: var(--rainbow-dark);
		opacity: 0.7;
		z-index: 2;
	}

	.scroll-indicator span {
		font-size: 0.9rem;
		text-transform: uppercase;
		letter-spacing: 0.1em;
	}

	.scroll-arrow svg {
		color: var(--rainbow-primary);
	}

	/* Mobile Optimizations */
	@media (max-width: 768px) {
		.rainbow-hero {
			background-attachment: scroll;
		}

		.floating-icon img {
			width: 40px;
			height: 40px;
		}

		.hero-content {
			padding: 1rem;
		}

		.hero-description {
			font-size: 1.1rem;
			max-width: 90%;
			padding: 1rem 1.5rem;
		}
	}

	/* Icon Glow Animation */
	@keyframes iconGlow {
		0% {
			filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.1)) 
			        drop-shadow(0 0 15px rgba(255, 255, 255, 0.6))
			        drop-shadow(0 0 25px rgba(102, 126, 234, 0.3));
		}
		100% {
			filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.1)) 
			        drop-shadow(0 0 20px rgba(255, 255, 255, 1))
			        drop-shadow(0 0 40px rgba(102, 126, 234, 0.6));
		}
	}

	/* Large Desktop */
	@media (min-width: 1200px) {
		.floating-icon img {
			width: 80px;
			height: 80px;
		}
	}
</style>
