<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let wrapper: HTMLElement;
	let content: HTMLElement;
	let circles: HTMLElement[] = [];
	let aboutTitle: HTMLElement;
	let aboutDescription: HTMLElement;
	let portfolioTitle: HTMLElement;
	let portfolioDescription: HTMLElement;
	let servicesSection: HTMLElement;
	let ctaSection: HTMLElement;

	function createRainbowScribble() {
		if (!content) return;
		
		// Clear existing elements
		circles = [];
		const existingElements = content.querySelectorAll('.scribble-path');
		existingElements.forEach(element => element.remove());

		// Create SVG scribble paths
		const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
		svg.classList.add('scribble-canvas');
		svg.setAttribute('viewBox', '0 0 1200 800');
		svg.style.cssText = `
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			pointer-events: none;
			z-index: 2;
			opacity: 0;
		`;

		// Create 5 rainbow scribble paths
		for (let i = 0; i < 5; i++) {
			const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
			
			// Generate smooth curved path
			const startX = 100 + Math.random() * 1000;
			const startY = 100 + Math.random() * 600;
			let pathData = `M ${startX} ${startY}`;
			
			// Create flowing curves
			for (let j = 0; j < 8; j++) {
				const x = startX + (j * 120) + (Math.random() - 0.5) * 200;
				const y = startY + Math.sin(j * 0.8) * 150 + (Math.random() - 0.5) * 100;
				const cx1 = x - 60 + Math.random() * 120;
				const cy1 = y - 60 + Math.random() * 120;
				const cx2 = x - 30 + Math.random() * 60;
				const cy2 = y - 30 + Math.random() * 60;
				pathData += ` C ${cx1} ${cy1}, ${cx2} ${cy2}, ${x} ${y}`;
			}
			
			path.setAttribute('d', pathData);
			path.setAttribute('fill', 'none');
			path.setAttribute('stroke', `hsl(${i * 72}, 70%, 60%)`);
			path.setAttribute('stroke-width', '3');
			path.setAttribute('stroke-linecap', 'round');
			path.setAttribute('stroke-linejoin', 'round');
			path.classList.add('scribble-path');
			
			svg.appendChild(path);
		}
		
		content.appendChild(svg);
		circles.push(svg as any); // Reuse circles array for cleanup
	}

	onMount(() => {
		// Register ScrollTrigger
		gsap.registerPlugin(ScrollTrigger);
		
		// Create rainbow scribble
		createRainbowScribble();
		
		// Animate scribble on scroll - much more performant
		gsap.to('.scribble-canvas', {
			opacity: 0.6,
			scrollTrigger: {
				trigger: content,
				start: "top 50%",
				end: "bottom bottom",
				scrub: 1
			}
		});

		// Add drawing animation to scribble paths
		const paths = content.querySelectorAll('.scribble-path');
		paths.forEach((path, index) => {
			const pathLength = (path as SVGPathElement).getTotalLength();
			gsap.set(path, {
				strokeDasharray: pathLength,
				strokeDashoffset: pathLength
			});
			
			gsap.to(path, {
				strokeDashoffset: 0,
				duration: 2,
				ease: "power2.out",
				scrollTrigger: {
					trigger: content,
					start: "top 40%",
					toggleActions: "play none none none",
					once: true
				},
				delay: index * 0.3
			});
		});

		// Optimize content animations for smoother performance
		gsap.set([aboutTitle, aboutDescription, portfolioTitle, portfolioDescription, servicesSection, ctaSection], { 
			y: 60, 
			opacity: 0 
		});

		// Create a master timeline for better performance
		const contentTimeline = gsap.timeline({
			scrollTrigger: {
				trigger: ".about-content",
				start: "top 70%",
				end: "bottom 20%",
				toggleActions: "play none none none",
				once: true
			}
		});

		// Stagger all animations for smoother experience
		contentTimeline
			.to(aboutTitle, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			})
			.to(aboutDescription, {
				duration: 0.6,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			}, "-=0.4")
			.to(portfolioTitle, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			}, "-=0.2")
			.to(portfolioDescription, {
				duration: 0.6,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			}, "-=0.4")
			.to(servicesSection, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			}, "-=0.3")
			.to(ctaSection, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				ease: "back.out(1.2)"
			}, "-=0.4");

		// Cleanup
		return () => {
			ScrollTrigger.getAll().forEach(trigger => trigger.kill());
		};
	});
</script>

<svelte:head>
	<title>About - Skuulbag</title>
	<meta name="description" content="Learn about Skuulbag - Quality stationery that doesn't break the bank. From students' first notebooks to industrial printing needs." />
</svelte:head>

<div bind:this={wrapper} id="wrapper">
	<div bind:this={content} id="content">
		<!-- Scroll Indicator -->
		<div class="scroll">
			<span class="font-indie-flower">SCROLL</span>
			<svg viewBox="0 0 24 24">
				<line class="st1" x1="12" y1="1" x2="12" y2="22.5" />
				<line class="st1" x1="12.1" y1="22.4" x2="18.9" y2="15.6" />
				<line class="st1" x1="11.9" y1="22.4" x2="5.1" y2="15.6" />
			</svg>
		</div>

		<!-- Main Content -->
		<div class="about-content">
			<!-- Hero Section -->
			<section class="about-hero">
				<h1 bind:this={aboutTitle} class="about-title font-milky-walky rainbow-text-gradient">
					About Skuulbag
				</h1>
				<h2 class="tagline font-indie-flower">Beyond Basic Supplies</h2>
			</section>

			<!-- About Description -->
			<section class="about-description">
				<p bind:this={aboutDescription} class="about-text font-jetbrains-mono">
					At Skuulbag, we believe quality stationery shouldn't break the bank. We've built our business on honest partnerships with trusted manufacturers, ensuring every pen, paper, and printing material meets our standards while keeping prices fair. From students' first notebooks to industrial printing needs, we're committed to delivering reliable supplies that work as hard as you do.
				</p>
			</section>

			<!-- Portfolio Section -->
			<section class="portfolio-section">
				<h2 bind:this={portfolioTitle} class="portfolio-title font-milky-walky rainbow-text-gradient">
					Discover Our Difference
				</h2>
				<p bind:this={portfolioDescription} class="portfolio-text font-jetbrains-mono">
					Experience quality stationery crafted by proven producers worldwide. Our carefully curated portfolio spans everyday essentials to specialized industrial materials—all at prices that make sense. From foam boards to premium glues, each product is selected for reliability and value. Give our collection a try and see why businesses and individuals trust Skuulbag for their complete stationery needs.
				</p>
			</section>

			<!-- Services Highlights -->
			<section bind:this={servicesSection} class="services-section">
				<h3 class="services-title font-indie-flower">What We Offer</h3>
				<div class="services-grid">
					<div class="service-item">
						<h4 class="font-jetbrains-mono">Retail & Bulk Orders</h4>
						<p class="font-jetbrains-mono">From single items to large quantity orders</p>
					</div>
					<div class="service-item">
						<h4 class="font-jetbrains-mono">Quality Guaranteed</h4>
						<p class="font-jetbrains-mono">Every product meets our strict standards</p>
					</div>
					<div class="service-item">
						<h4 class="font-jetbrains-mono">Competitive Pricing</h4>
						<p class="font-jetbrains-mono">Fair prices without compromising quality</p>
					</div>
					<div class="service-item">
						<h4 class="font-jetbrains-mono">Industrial Materials</h4>
						<p class="font-jetbrains-mono">Specialized supplies for professional needs</p>
					</div>
				</div>
			</section>

			<!-- CTA Section -->
			<section bind:this={ctaSection} class="cta-section">
				<h3 class="cta-title font-indie-flower">Ready to Explore?</h3>
				<div class="cta-buttons">
					<a href="/" class="cta-btn primary font-jetbrains-mono">Shop Collection</a>
					<a href="/contact" class="cta-btn secondary font-jetbrains-mono">Contact Us</a>
				</div>
			</section>
		</div>
	</div>
</div>

<style>
	/* Base Layout */
	#wrapper {
		position: relative;
		width: 100%;
		min-height: 100vh;
		background: #f5deb3; /* Wheat color */
		overflow-x: hidden;
		scroll-behavior: smooth;
	}

	#content {
		position: relative;
		width: 100%;
		min-height: 200vh; /* Reduced height for better performance */
		z-index: 1;
	}

	/* Fade overlays */
	#wrapper:before,
	#wrapper:after {
		content: "";
		position: fixed;
		z-index: 10;
		top: 0;
		left: 0;
		width: 100%;
		height: 60px;
		background: linear-gradient(to bottom, #f5deb3 10%, rgba(245, 222, 179, 0));
		pointer-events: none;
	}

	#wrapper:after {
		top: auto;
		bottom: 0;
		background: linear-gradient(to top, #f5deb3 50%, rgba(245, 222, 179, 0));
	}

	/* Scroll indicator */
	.scroll {
		width: 100%;
		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		position: absolute;
		z-index: 5;
		letter-spacing: 0.2em;
		font-size: 11px;
		color: var(--rainbow-dark);
	}

	.scroll span {
		display: block;
		text-transform: uppercase;
		opacity: 0.7;
	}

	.scroll svg {
		margin-top: 10px;
		width: 18px;
		height: 18px;
		animation: scroll 0.95s ease-in-out alternate infinite;
		fill: none;
		stroke: var(--rainbow-dark);
		stroke-linecap: round;
		stroke-miterlimit: 10;
		stroke-width: 1;
	}

	@keyframes scroll {
		0% { transform: translateY(0); }
		100% { transform: translateY(10px); }
	}

	/* Rainbow scribble canvas */
	:global(.scribble-canvas) {
		filter: drop-shadow(0 0 10px rgba(0, 0, 0, 0.1));
	}

	/* Rainbow text gradient optimized for wheat background */
	:global(.rainbow-text-gradient) {
		background: linear-gradient(135deg, 
			#8B4513 0%,    /* Dark brown for contrast */
			#CD853F 15%,   /* Peru */
			#D2691E 30%,   /* Chocolate */
			#FF6347 45%,   /* Tomato */
			#FF4500 60%,   /* Orange red */
			#FF8C00 75%,   /* Dark orange */
			#B8860B 90%,   /* Dark goldenrod */
			#8B4513 100%   /* Back to dark brown */
		) !important;
		-webkit-background-clip: text !important;
		-webkit-text-fill-color: transparent !important;
		background-clip: text !important;
		text-shadow: none !important;
		filter: drop-shadow(0 2px 4px rgba(139, 69, 19, 0.3)) !important;
	}

	/* Main content */
	.about-content {
		position: relative;
		z-index: 6;
		max-width: 1200px;
		margin: 0 auto;
		padding: 8rem 2rem 4rem;
		min-height: 200vh; /* Reduced for better performance */
	}

	/* Hero Section */
	.about-hero {
		text-align: center;
		margin-bottom: 6rem;
		padding-top: 4rem;
	}

	.about-title {
		font-size: clamp(3rem, 8vw, 6rem);
		margin: 0 0 1rem 0;
		line-height: 0.9;
		font-weight: normal;
	}

	.tagline {
		font-size: clamp(1.2rem, 3vw, 2rem);
		color: var(--rainbow-dark);
		opacity: 0.8;
		margin: 0;
	}

	/* About Description */
	.about-description {
		margin: 6rem 0;
		text-align: center;
	}

	.about-text {
		font-size: clamp(1.1rem, 2.5vw, 1.4rem);
		line-height: 1.8;
		color: var(--rainbow-dark);
		opacity: 0.9;
		max-width: 800px;
		margin: 0 auto;
		background: rgba(255, 255, 255, 0.3);
		backdrop-filter: blur(10px);
		padding: 3rem;
		border-radius: 20px;
		border: 1px solid rgba(255, 255, 255, 0.4);
		box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
	}

	/* Portfolio Section */
	.portfolio-section {
		margin: 8rem 0;
		text-align: center;
	}

	.portfolio-title {
		font-size: clamp(2.5rem, 6vw, 4rem);
		margin: 0 0 3rem 0;
		line-height: 0.9;
	}

	.portfolio-text {
		font-size: clamp(1rem, 2vw, 1.2rem);
		line-height: 1.7;
		color: var(--rainbow-dark);
		opacity: 0.9;
		max-width: 900px;
		margin: 0 auto;
		background: rgba(255, 255, 255, 0.25);
		backdrop-filter: blur(10px);
		padding: 2.5rem;
		border-radius: 20px;
		border: 1px solid rgba(255, 255, 255, 0.3);
		box-shadow: 0 15px 30px rgba(0, 0, 0, 0.08);
	}

	/* Services Section */
	.services-section {
		margin: 8rem 0;
	}

	.services-title {
		font-size: clamp(1.8rem, 4vw, 2.5rem);
		text-align: center;
		margin: 0 0 3rem 0;
		color: var(--rainbow-dark);
		opacity: 0.9;
	}

	.services-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 2rem;
		max-width: 1000px;
		margin: 0 auto;
	}

	.service-item {
		background: rgba(255, 255, 255, 0.2);
		backdrop-filter: blur(10px);
		padding: 2rem;
		border-radius: 15px;
		border: 1px solid rgba(255, 255, 255, 0.3);
		text-align: center;
		transition: all 0.3s ease;
		box-shadow: 0 10px 20px rgba(0, 0, 0, 0.05);
	}

	.service-item:hover {
		transform: translateY(-5px);
		box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
		background: rgba(255, 255, 255, 0.3);
	}

	.service-item h4 {
		font-size: 1.2rem;
		margin: 0 0 1rem 0;
		color: var(--rainbow-primary);
		font-weight: 600;
	}

	.service-item p {
		font-size: 0.95rem;
		margin: 0;
		color: var(--rainbow-dark);
		opacity: 0.8;
		line-height: 1.5;
	}

	/* CTA Section */
	.cta-section {
		text-align: center;
		margin: 6rem 0 4rem;
	}

	.cta-title {
		font-size: clamp(1.6rem, 3vw, 2rem);
		margin: 0 0 2rem 0;
		color: var(--rainbow-dark);
		opacity: 0.9;
	}

	.cta-buttons {
		display: flex;
		gap: 1.5rem;
		justify-content: center;
		flex-wrap: wrap;
	}

	.cta-btn {
		padding: 1.2rem 3rem;
		border-radius: 15px;
		text-decoration: none;
		font-size: 1.1rem;
		font-weight: 700;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
		border: 3px solid transparent;
		display: inline-block;
		position: relative;
		overflow: hidden;
	}

	.cta-btn::before {
		content: '';
		position: absolute;
		top: 0;
		left: -100%;
		width: 100%;
		height: 100%;
		background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
		transition: left 0.6s ease;
	}

	.cta-btn:hover::before {
		left: 100%;
	}

	.cta-btn.primary {
		background: linear-gradient(135deg, #8B4513, #CD853F, #D2691E);
		color: white;
		box-shadow: 
			0 15px 30px rgba(139, 69, 19, 0.4),
			inset 0 1px 0 rgba(255, 255, 255, 0.2);
		border: 3px solid rgba(139, 69, 19, 0.3);
		text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
	}

	.cta-btn.primary:hover {
		transform: translateY(-5px) scale(1.02);
		box-shadow: 
			0 25px 50px rgba(139, 69, 19, 0.6),
			inset 0 1px 0 rgba(255, 255, 255, 0.3);
		background: linear-gradient(135deg, #A0522D, #DEB887, #F4A460);
	}

	.cta-btn.secondary {
		background: rgba(139, 69, 19, 0.1);
		backdrop-filter: blur(15px);
		color: #8B4513;
		border: 3px solid rgba(139, 69, 19, 0.4);
		box-shadow: 
			0 10px 20px rgba(139, 69, 19, 0.2),
			inset 0 1px 0 rgba(255, 255, 255, 0.3);
	}

	.cta-btn.secondary:hover {
		transform: translateY(-5px) scale(1.02);
		background: rgba(139, 69, 19, 0.2);
		border-color: rgba(139, 69, 19, 0.6);
		box-shadow: 
			0 20px 40px rgba(139, 69, 19, 0.3),
			inset 0 1px 0 rgba(255, 255, 255, 0.4);
		color: #654321;
	}

	/* Mobile Optimizations */
	@media (max-width: 768px) {
		.about-content {
			padding: 6rem 1rem 3rem;
		}

		.about-text,
		.portfolio-text {
			padding: 2rem 1.5rem;
		}

		.services-grid {
			grid-template-columns: 1fr;
			gap: 1.5rem;
		}

		.service-item {
			padding: 1.5rem;
		}

		.cta-buttons {
			flex-direction: column;
			align-items: center;
			gap: 1rem;
		}

		.cta-btn {
			padding: 1rem 2.5rem;
			font-size: 1rem;
			border-radius: 12px;
		}

		.cta-btn:hover {
			transform: translateY(-3px) scale(1.01);
		}
	}

	/* Extra small mobile screens */
	@media (max-width: 480px) {
		.about-content {
			padding: 5rem 0.5rem 2rem;
		}

		.about-text,
		.portfolio-text {
			padding: 1.5rem 1rem;
		}

		.service-item {
			padding: 1.2rem;
		}
	}
</style>