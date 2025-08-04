<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';
	
	let heroContainer: HTMLElement;
	let heroTitle: HTMLElement;
	let heroSubtitle: HTMLElement;
	let scrollIndicator: HTMLElement;
	let floatingIcons: HTMLElement[] = [];
	let galleryContainer: HTMLElement;
	let productCards: HTMLElement[] = [];
	let rainbowCanvas: HTMLCanvasElement;
	let imageCache: Map<string, HTMLImageElement> = new Map();
	let visibleImages: Set<number> = new Set();
	let thankYouTitle: HTMLElement;
	let thankYouMessage: HTMLElement;
	let contactUsText: HTMLElement;
	let socialLinks: HTMLElement;
	let modalOverlay: HTMLElement;
	let modalContainer: HTMLElement;
	
	// Modal state
	let isModalOpen = false;
	let selectedProduct: typeof productData[0] | null = null;
	
	const iconData = [
		{ src: '/GsapResources/3dicons/3dicons-pencil-dynamic-color.png', name: 'pencil' },
		{ src: '/GsapResources/3dicons/3dicons-notebook-dynamic-color.png', name: 'notebook' },
		{ src: '/GsapResources/3dicons/3dicons-scissor-dynamic-color.png', name: 'scissor' },
		{ src: '/GsapResources/3dicons/3dicons-mail-dynamic-color.png', name: 'mail' },
		{ src: '/GsapResources/3dicons/3dicons-copy-dynamic-color.png', name: 'copy' }
	];

	const productData = [
		{ src: '/product_images/item_dp158.webp', name: 'Premium Notebook', category: 'Notebooks', price: '₹1,099', colSpan: 2, rowSpan: 1, description: 'High-quality 200-page lined notebook with premium paper. Perfect for note-taking, journaling, or creative writing. Features a durable hardcover and elastic band closure.', tags: ['Premium', 'Hardcover', 'Lined', 'Durable'] },
		{ src: '/product_images/item_dp159.webp', name: 'Gel Pen Set', category: 'Writing', price: '₹719', colSpan: 1, rowSpan: 2, description: 'Smooth-writing gel pen set with 12 vibrant colors. Quick-drying ink prevents smudging. Comfortable grip design for extended writing sessions.', tags: ['12 Colors', 'Quick-Dry', 'Smooth', 'Comfort Grip'] },
		{ src: '/product_images/item_cn119.webp', name: 'Sticky Notes', category: 'Office', price: '₹339', colSpan: 1, rowSpan: 1, description: 'Colorful sticky notes pack with 6 different neon colors. Strong adhesive that repositions easily without leaving residue. Perfect for reminders and organization.', tags: ['6 Colors', 'Neon', 'Repositionable', 'No Residue'] },
		{ src: '/product_images/item_ft174.webp', name: 'Highlighter Pack', category: 'Writing', price: '₹575', colSpan: 1, rowSpan: 1, description: 'Professional highlighter set with 8 fluorescent colors. Chisel tip for both broad highlighting and fine underlining. Fade-resistant ink for long-lasting marks.', tags: ['8 Colors', 'Chisel Tip', 'Fade-Resistant', 'Professional'] },
		{ src: '/product_images/item_eb025.webp', name: 'Paper Clips', category: 'Office', price: '₹254', colSpan: 1, rowSpan: 1, description: 'Durable metal paper clips in assorted sizes. Rust-resistant coating ensures longevity. Essential for document organization and filing.', tags: ['Assorted Sizes', 'Rust-Resistant', 'Metal', 'Essential'] },
		{ src: '/product_images/item_dp114.webp', name: 'Mechanical Pencil', category: 'Writing', price: '₹423', colSpan: 2, rowSpan: 1, description: 'Precision mechanical pencil with 0.5mm lead. Comfortable rubber grip and refillable design. Includes extra lead refills and eraser replacements.', tags: ['0.5mm Lead', 'Refillable', 'Rubber Grip', 'Extra Refills'] },
		{ src: '/product_images/item_cn164.webp', name: 'Index Dividers', category: 'Office', price: '₹465', colSpan: 1, rowSpan: 1, description: 'Heavy-duty plastic dividers with reinforced tabs. Pre-printed numbers 1-12 with blank tabs included. Perfect for binders and filing systems.', tags: ['Heavy-Duty', 'Reinforced', 'Pre-Printed', 'Blank Tabs'] },
		{ src: '/product_images/item_ft180.webp', name: 'Correction Fluid', category: 'Writing', price: '₹279', colSpan: 1, rowSpan: 2, description: 'Fast-drying correction fluid with precision brush applicator. Covers mistakes completely with smooth, even coverage. Includes thinner for consistency.', tags: ['Fast-Drying', 'Precision Brush', 'Complete Coverage', 'Includes Thinner'] },
		{ src: '/product_images/item_dp133.webp', name: 'Ruler Set', category: 'Measuring', price: '₹678', colSpan: 1, rowSpan: 1, description: 'Professional ruler set including 6", 12", and 18" rulers. Clear markings in both metric and imperial units. Durable plastic construction.', tags: ['3 Rulers', 'Metric & Imperial', 'Clear Markings', 'Durable Plastic'] },
		{ src: '/product_images/item_gz111.webp', name: 'Stapler', category: 'Office', price: '₹1,017', colSpan: 2, rowSpan: 2, description: 'Heavy-duty desktop stapler with 25-sheet capacity. Includes 1000 staples and staple remover. Ergonomic design reduces hand fatigue during extended use.', tags: ['25-Sheet Capacity', '1000 Staples', 'Ergonomic', 'Desktop'] },
		{ src: '/product_images/item_ft131.webp', name: 'Marker Set', category: 'Writing', price: '₹847', colSpan: 1, rowSpan: 1, description: 'Versatile marker set with fine and broad tips. 24 vibrant colors with alcohol-based ink. Perfect for art projects, presentations, and creative work.', tags: ['24 Colors', 'Fine & Broad Tips', 'Alcohol-Based', 'Versatile'] },
		{ src: '/product_images/item_eb035.webp', name: 'Binder Clips', category: 'Office', price: '₹381', colSpan: 1, rowSpan: 1, description: 'Heavy-duty binder clips in assorted sizes. Strong spring steel construction holds up to 200 sheets. Removable handles for permanent binding.', tags: ['Assorted Sizes', 'Spring Steel', '200 Sheet Capacity', 'Removable Handles'] }
	];

	// Image caching and lazy loading functions
	function preloadImage(src: string): Promise<HTMLImageElement> {
		return new Promise((resolve, reject) => {
			if (imageCache.has(src)) {
				resolve(imageCache.get(src)!);
				return;
			}

			const img = new Image();
			img.onload = () => {
				imageCache.set(src, img);
				resolve(img);
			};
			img.onerror = reject;
			img.src = src;
		});
	}

	// Progressive image loading
	async function loadImagesProgressively() {
		// Load priority images first (first 4)
		const priorityImages = productData.slice(0, 4);
		await Promise.all(priorityImages.map(product => preloadImage(product.src)));
		
		// Load remaining images in background
		setTimeout(() => {
			productData.slice(4).forEach(product => preloadImage(product.src));
		}, 100);
	}

	// Modal functions
	function openModal(product: typeof productData[0]) {
		selectedProduct = product;
		isModalOpen = true;
		document.body.style.overflow = 'hidden'; // Prevent body scroll
		
		// GSAP animation for modal entrance
		if (modalOverlay && modalContainer) {
			gsap.set(modalOverlay, { opacity: 0, backdropFilter: 'blur(0px)' });
			gsap.set(modalContainer, { scale: 0.8, opacity: 0, y: 50 });
			
			gsap.to(modalOverlay, {
				opacity: 1,
				backdropFilter: 'blur(10px)',
				duration: 0.4,
				ease: "power3.out"
			});
			
			gsap.to(modalContainer, {
				scale: 1,
				opacity: 1,
				y: 0,
				duration: 0.4,
				ease: "power3.out",
				delay: 0.1
			});
		}
	}

	function closeModal() {
		if (!modalOverlay || !modalContainer) return;
		
		// GSAP animation for modal exit
		gsap.to(modalContainer, {
			scale: 0.8,
			opacity: 0,
			y: 50,
			duration: 0.3,
			ease: "power3.in"
		});
		
		gsap.to(modalOverlay, {
			opacity: 0,
			backdropFilter: 'blur(0px)',
			duration: 0.3,
			ease: "power3.in",
			onComplete: () => {
				isModalOpen = false;
				selectedProduct = null;
				document.body.style.overflow = 'auto'; // Restore body scroll
			}
		});
	}

	function handleCardClick(product: typeof productData[0]) {
		openModal(product);
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Escape' && isModalOpen) {
			closeModal();
		}
	}

	function handleOverlayClick(event: MouseEvent) {
		if (event.target === modalOverlay) {
			closeModal();
		}
	}

	// Rainbow animation setup
	function initRainbowStream() {
		if (!rainbowCanvas) return;
		
		// Set canvas size to window size
		rainbowCanvas.width = window.innerWidth;
		rainbowCanvas.height = window.innerHeight;
		
		const ctx = rainbowCanvas.getContext('2d');
		if (!ctx) return;

		// Handle resize
		window.addEventListener('resize', () => {
			rainbowCanvas.width = window.innerWidth;
			rainbowCanvas.height = window.innerHeight;
		});

		const particles: Array<{x: number, y: number, vx: number, vy: number, hue: number, alpha: number}> = [];
		
		function createParticle() {
			return {
				x: Math.random() * rainbowCanvas.width,
				y: rainbowCanvas.height + 20,
				vx: (Math.random() - 0.5) * 2,
				vy: -Math.random() * 3 - 1,
				hue: Math.random() * 360,
				alpha: Math.random() * 0.8 + 0.2
			};
		}

		let scrollVelocity = 0;
		let lastScrollY = 0;

		function updateParticles(deltaTime: number) {
			if (!ctx) return;
			
			ctx.clearRect(0, 0, rainbowCanvas.width, rainbowCanvas.height);
			
			// Add particles based on scroll velocity
			if (scrollVelocity > 2) {
				for (let i = 0; i < Math.min(scrollVelocity / 10, 5); i++) {
					particles.push(createParticle());
				}
			}

			particles.forEach((particle, index) => {
				particle.x += particle.vx;
				particle.y += particle.vy;
				particle.alpha -= 0.005;

				if (particle.alpha <= 0 || particle.y < -20) {
					particles.splice(index, 1);
					return;
				}

				if (ctx) {
					ctx.save();
					ctx.globalAlpha = particle.alpha;
					ctx.fillStyle = `hsl(${particle.hue}, 70%, 60%)`;
					ctx.beginPath();
					ctx.arc(particle.x, particle.y, 3, 0, Math.PI * 2);
					ctx.fill();
					ctx.restore();
				}
			});
		}

		function handleScroll() {
			const currentScrollY = window.scrollY;
			scrollVelocity = Math.abs(currentScrollY - lastScrollY);
			lastScrollY = currentScrollY;
		}

		window.addEventListener('scroll', handleScroll, { passive: true });

		function animate() {
			updateParticles(16);
			requestAnimationFrame(animate);
		}
		animate();
	}
	
	onMount(() => {
		// Register ScrollTrigger
		gsap.registerPlugin(ScrollTrigger);
		
		// Initialize image loading and rainbow effects
		loadImagesProgressively();
		initRainbowStream();
		
		// Add keyboard event listener for ESC key
		document.addEventListener('keydown', handleKeydown);
		
		// Background transition effect with snap
		gsap.to(galleryContainer, {
			scrollTrigger: {
				trigger: galleryContainer,
				start: "top bottom",
				end: "bottom top",
				scrub: 1,
				snap: {
					snapTo: "labels",
					duration: {min: 0.2, max: 3},
					delay: 0.2,
					ease: "power1.inOut"
				},
				onUpdate: (self) => {
					const progress = self.progress;
					// Smooth background transition with velocity
					gsap.set(galleryContainer, {
						y: -progress * 50,
						scale: 1 + progress * 0.05
					});
				}
			}
		});

		// Add snap animation for section transitions
		ScrollTrigger.create({
			trigger: ".thank-you-section",
			start: "top bottom",
			end: "top top",
			snap: {
				snapTo: 1,
				duration: {min: 0.2, max: 3},
				delay: 0.2,
				ease: "power2.inOut"
			}
		});
		
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
		
		// Floating icons animation - responsive spread
		floatingIcons.forEach((icon, index) => {
			// Responsive positioning based on screen width
			const isMobile = window.innerWidth <= 768;
			const isTablet = window.innerWidth <= 1024 && window.innerWidth > 768;
			
			let xRange, yRange, iconScale;
			
			if (isMobile) {
				xRange = [-150, 150]; // Much smaller range for mobile
				yRange = [-100, 100];
				iconScale = [0.6, 0.9]; // Smaller icons on mobile
			} else if (isTablet) {
				xRange = [-250, 250];
				yRange = [-150, 150];
				iconScale = [0.7, 1.0];
			} else {
				xRange = [-400, 400]; // Original desktop range
				yRange = [-200, 200];
				iconScale = [0.8, 1.2];
			}
			
			gsap.set(icon, {
				x: gsap.utils.random(xRange[0], xRange[1]),
				y: gsap.utils.random(yRange[0], yRange[1]),
				rotation: gsap.utils.random(-15, 15),
				scale: gsap.utils.random(iconScale[0], iconScale[1])
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

		// Intersection Observer for lazy loading
		const imageObserver = new IntersectionObserver((entries) => {
			entries.forEach((entry) => {
				if (entry.isIntersecting) {
					const index = parseInt(entry.target.getAttribute('data-index') || '0');
					if (index >= 4 && !visibleImages.has(index)) {
						visibleImages.add(index);
						visibleImages = visibleImages; // Trigger reactivity
					}
				}
			});
		}, { rootMargin: '50px' });

		// Product Gallery ScrollTrigger Animations
		gsap.set(productCards, { y: 100, opacity: 0, scale: 0.8 });
		
		productCards.forEach((card, index) => {
			// Add intersection observer for lazy loading
			card.setAttribute('data-index', index.toString());
			imageObserver.observe(card);

			gsap.to(card, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				scale: 1,
				ease: "power3.out",
				scrollTrigger: {
					trigger: card,
					start: "top 90%",
					toggleActions: "play none none none",
					once: true
				},
				delay: index * 0.1
			});

			// Floating animation on scroll
			gsap.to(card, {
				duration: gsap.utils.random(4, 6),
				y: "+=10",
				rotation: gsap.utils.random(-2, 2),
				repeat: -1,
				yoyo: true,
				ease: "sine.inOut",
				delay: gsap.utils.random(0, 2)
			});
		});

		// Handle window resize for icon repositioning
		function repositionIcons() {
			const isMobile = window.innerWidth <= 768;
			const isTablet = window.innerWidth <= 1024 && window.innerWidth > 768;
			
			floatingIcons.forEach((icon) => {
				let xRange, yRange, iconScale;
				
				if (isMobile) {
					xRange = [-150, 150];
					yRange = [-100, 100];
					iconScale = [0.6, 0.9];
				} else if (isTablet) {
					xRange = [-250, 250];
					yRange = [-150, 150];
					iconScale = [0.7, 1.0];
				} else {
					xRange = [-400, 400];
					yRange = [-200, 200];
					iconScale = [0.8, 1.2];
				}
				
				gsap.set(icon, {
					x: gsap.utils.random(xRange[0], xRange[1]),
					y: gsap.utils.random(yRange[0], yRange[1]),
					scale: gsap.utils.random(iconScale[0], iconScale[1])
				});
			});
		}

		window.addEventListener('resize', repositionIcons);

		// Thank You section animations
		gsap.set([thankYouTitle, thankYouMessage, contactUsText], { y: 100, opacity: 0 });
		gsap.set(socialLinks, { y: 50, opacity: 0 });

		gsap.to(thankYouTitle, {
			duration: 1.2,
			y: 0,
			opacity: 1,
			ease: "power3.out",
			scrollTrigger: {
				trigger: thankYouTitle,
				start: "top 80%",
				toggleActions: "play none none none",
				once: true
			}
		});

		gsap.to(thankYouMessage, {
			duration: 1,
			y: 0,
			opacity: 1,
			ease: "power3.out",
			scrollTrigger: {
				trigger: thankYouMessage,
				start: "top 85%",
				toggleActions: "play none none none",
				once: true
			},
			delay: 0.3
		});

		gsap.to(contactUsText, {
			duration: 0.8,
			y: 0,
			opacity: 1,
			ease: "power3.out",
			scrollTrigger: {
				trigger: contactUsText,
				start: "top 90%",
				toggleActions: "play none none none",
				once: true
			},
			delay: 0.5
		});

		gsap.to(socialLinks, {
			duration: 0.8,
			y: 0,
			opacity: 1,
			ease: "back.out(1.7)",
			scrollTrigger: {
				trigger: socialLinks,
				start: "top 90%",
				toggleActions: "play none none none",
				once: true
			},
			delay: 0.7
		});

		// Animate individual social icons
		const socialIconElements = socialLinks.querySelectorAll('.social-link');
		socialIconElements.forEach((icon, index) => {
			gsap.set(icon, { scale: 0, rotation: 180 });
			gsap.to(icon, {
				duration: 0.6,
				scale: 1,
				rotation: 0,
				ease: "back.out(1.7)",
				scrollTrigger: {
					trigger: socialLinks,
					start: "top 90%",
					toggleActions: "play none none none",
					once: true
				},
				delay: 0.9 + (index * 0.1)
			});
		});

		// Cleanup
		return () => {
			imageObserver.disconnect();
			window.removeEventListener('resize', repositionIcons);
			document.removeEventListener('keydown', handleKeydown);
		};
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

<!-- Rainbow Canvas Overlay -->
<canvas 
	bind:this={rainbowCanvas} 
	class="rainbow-canvas"
></canvas>

<!-- Product Gallery Section -->
<section bind:this={galleryContainer} class="product-gallery">
	<div class="gallery-header">
		<h2 class="gallery-title font-milky-walky rainbow-text-gradient">
			Featured Collection
		</h2>
		<p class="gallery-subtitle font-indie-flower">
			Handpicked stationery essentials
		</p>
	</div>
	
	<div class="product-grid">
		{#each productData as product, index}
			<div 
				bind:this={productCards[index]}
				class="product-card floating-3d gsap-optimized"
				style="--col-span: {product.colSpan}; --row-span: {product.rowSpan};"
				data-col-span={product.colSpan}
				data-row-span={product.rowSpan}
				on:click={() => handleCardClick(product)}
				role="button"
				tabindex="0"
				on:keydown={(e) => e.key === 'Enter' && handleCardClick(product)}
			>
				<div class="card-3d-wrapper">
					<div class="product-image">
						{#if visibleImages.has(index) || index < 4}
							<img 
								src={product.src} 
								alt={product.name} 
								loading={index < 4 ? "eager" : "lazy"}
								decoding="async"
								on:load={() => visibleImages.add(index)}
								class="floating-image"
							/>
						{:else}
							<div class="image-placeholder">
								<div class="loading-spinner"></div>
							</div>
						{/if}
					</div>
					<div class="product-info">
						<span class="product-category font-indie-flower">{product.category}</span>
						<span style="display: flex; flex-direction:row; gap:5%">
							<h3 class="product-name font-jetbrains-mono">{product.name}</h3>
							<p class="product-price font-jetbrains-mono">{product.price}</p>
						</span>
					</div>
				</div>
			</div>
		{/each}
	</div>
</section>

<!-- Thank You Section -->
<section class="thank-you-section">
	<div class="thank-you-content">
		<h2 bind:this={thankYouTitle} class="thank-you-title font-milky-walky rainbow-text-gradient">
			Thank You for Visiting!
		</h2>
		<p bind:this={thankYouMessage} class="thank-you-message font-indie-flower">
			We hope you found the perfect stationery for your creative journey
		</p>
		
		<p bind:this={contactUsText} class="contact-us-text font-jetbrains-mono">
			Contact Us At
		</p>
		
		<!-- Social Media Links -->
		<div bind:this={socialLinks} class="social-links">
			<a href="https://instagram.com" class="social-link" aria-label="Instagram">
				<img src="/GsapResources/socials/instagram-svgrepo-com.svg" alt="Instagram" />
			</a>
			<a href="https://facebook.com" class="social-link" aria-label="Facebook">
				<img src="/GsapResources/socials/facebook.com.svg" alt="Facebook" />
			</a>
			<a href="https://x.com" class="social-link" aria-label="X (Twitter)">
				<img src="/GsapResources/socials/x.com.svg" alt="X" />
			</a>
			<a href="https://wa.me/1234567890" class="social-link" aria-label="WhatsApp">
				<img src="/GsapResources/socials/whatsapp-svgrepo-com.svg" alt="WhatsApp" />
			</a>
			<a href="mailto:hello@skuulbag.com" class="social-link" aria-label="Email">
				<img src="/GsapResources/socials/email-8-svgrepo-com.svg" alt="Email" />
			</a>
		</div>
	</div>
</section>

<!-- Footer -->
<footer class="footer">
	<div class="footer-content">
		<p class="copyright font-jetbrains-mono">
			© 2024 Skuulbag. All rights reserved.
		</p>
		<p class="made-with-love font-indie-flower">
			Made with ❤️ by <a href="https://github.com/akeno-hx-dxd" target="_blank" rel="noopener noreferrer">github.com/akeno-hx-dxd</a>
		</p>
	</div>
</footer>

<!-- Product Modal -->
{#if isModalOpen && selectedProduct}
	<div 
		bind:this={modalOverlay}
		class="modal-overlay"
		on:click={handleOverlayClick}
		on:keydown={(e) => e.key === 'Escape' && closeModal()}
		role="dialog"
		aria-modal="true"
		aria-labelledby="modal-title"
		tabindex="-1"
	>
		<div 
			bind:this={modalContainer}
			class="modal-container"
		>
			<!-- Close Button -->
			<button 
				class="modal-close-btn"
				on:click={closeModal}
				aria-label="Close modal"
			>
				<svg width="24" height="24" viewBox="0 0 24 24" fill="none">
					<path d="M18 6L6 18M6 6L18 18" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
				</svg>
			</button>
			
			<!-- Modal Content -->
			<div class="modal-content">
				<!-- Product Image Section -->
				<div class="modal-image-section">
					<div class="modal-product-image">
						<img 
							src={selectedProduct.src} 
							alt={selectedProduct.name}
							class="modal-image"
						/>
					</div>
				</div>
				
				<!-- Product Details Section -->
				<div class="modal-details-section">
					<div class="modal-product-info">
						<span class="modal-category font-indie-flower">{selectedProduct.category}</span>
						<h2 id="modal-title" class="modal-product-name font-jetbrains-mono">{selectedProduct.name}</h2>
						<p class="modal-product-price font-jetbrains-mono">{selectedProduct.price}</p>
						<p class="modal-product-description font-jetbrains-mono">{selectedProduct.description}</p>
						
						<!-- Product Tags -->
						<div class="modal-product-tags">
							{#each selectedProduct.tags as tag}
								<span class="product-tag font-indie-flower">{tag}</span>
							{/each}
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
{/if}

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
		overflow: hidden; /* Prevent icons from going outside container */
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

		.floating-icons {
			/* Additional constraint for mobile */
			margin: 0 1rem;
			width: calc(100% - 2rem);
		}

		.floating-icon {
			/* Ensure icons don't go too far out */
			max-width: 40px;
			max-height: 40px;
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

	/* Rainbow Canvas */
	.rainbow-canvas {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 100vh;
		pointer-events: none;
		z-index: 1;
		opacity: 0.8;
	}

	/* Product Gallery Styles */
	.product-gallery {
		min-height: 100vh;
		padding: 4rem 2rem;
		background: url('/GsapResources/backgrounds/black_background_with_white_shade_and_a_black_rectangular_platform_at_center_bottom.jpg');
		background-size: cover;
		background-position: center;
		background-attachment: fixed;
		position: relative;
		z-index: 2;
	}

	.gallery-header {
		text-align: center;
		margin-bottom: 4rem;
	}

	.gallery-title {
		font-size: clamp(2.5rem, 6vw, 4rem);
		margin: 0 0 1rem 0;
		line-height: 0.9;
	}

	.gallery-subtitle {
		font-size: clamp(1.1rem, 2.5vw, 1.5rem);
		color: rgba(255, 255, 255, 0.9);
		opacity: 0.8;
		margin: 0;
		text-shadow: 0 0 10px rgba(255, 255, 255, 0.3);
	}

	.product-grid {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 1.5rem;
		max-width: 1400px;
		margin: 0 auto;
		grid-auto-rows: 280px;
		grid-auto-flow: dense;
	}

	.product-card {
		grid-column: span var(--col-span);
		grid-row: span var(--row-span);
		position: relative;
		cursor: pointer;
		perspective: 1000px;
		transform-style: preserve-3d;
	}

	.card-3d-wrapper {
		width: 100%;
		height: 100%;
		position: relative;
		transform-style: preserve-3d;
		transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
		border-radius: 20px;
		background: rgba(255, 255, 255, 0.03);
		backdrop-filter: blur(5px);
		border: 1px solid rgba(255, 255, 255, 0.1);
		overflow: hidden;
	}

	.floating-3d:hover .card-3d-wrapper {
		transform: translateY(-15px) rotateX(5deg) rotateY(5deg) scale(1.02);
		box-shadow: 
			0 30px 60px rgba(0, 0, 0, 0.3),
			0 0 0 1px rgba(255, 255, 255, 0.2),
			inset 0 1px 0 rgba(255, 255, 255, 0.3);
	}

	.product-image {
		width: 100%;
		height: 70%;
		border-radius: 15px 15px 0 0;
		overflow: hidden;
		position: relative;
		background: linear-gradient(135deg, rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.05));
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.floating-image {
		width: 90%;
		height: 90%;
		object-fit: contain;
		transition: all 0.4s ease;
		filter: drop-shadow(0 10px 25px rgba(0, 0, 0, 0.2));
		transform: translateZ(20px);
	}

	.floating-3d:hover .floating-image {
		transform: translateZ(40px) scale(1.05);
		filter: drop-shadow(0 20px 40px rgba(0, 0, 0, 0.4));
	}

	/* Free gravity effect for backgroundless images */
	.product-image::before {
		content: '';
		position: absolute;
		bottom: 10px;
		left: 50%;
		transform: translateX(-50%);
		width: 60%;
		height: 20px;
		background: radial-gradient(ellipse, rgba(0, 0, 0, 0.3) 0%, transparent 70%);
		border-radius: 50%;
		opacity: 0.6;
		transition: all 0.4s ease;
	}

	.floating-3d:hover .product-image::before {
		transform: translateX(-50%) scale(1.2);
		opacity: 0.8;
		background: radial-gradient(ellipse, rgba(0, 0, 0, 0.4) 0%, transparent 70%);
	}

	.product-info {
		height: 30%;
		min-height: 90px;
		padding: 0.8rem;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;
		text-align: center;
		position: relative;
		z-index: 10;
		background: linear-gradient(180deg, 
			rgba(255, 255, 255, 0.08) 0%, 
			rgba(255, 255, 255, 0.05) 100%);
		backdrop-filter: blur(10px);
		border-top: 1px solid rgba(255, 255, 255, 0.1);
		overflow: visible;
	}

	.product-category {
		display: inline-block;
		background: linear-gradient(135deg, var(--rainbow-primary), var(--rainbow-accent));
		color: white;
		padding: 0.4rem 1rem;
		border-radius: 20px;
		font-size: 0.7rem;
		margin-bottom: 0.5rem;
		text-transform: uppercase;
		letter-spacing: 0.08em;
		font-weight: 600;
		box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
		transform: translateZ(10px);
		transition: all 0.3s ease;
	}

	.floating-3d:hover .product-category {
		transform: translateZ(20px) scale(1.05);
		box-shadow: 0 8px 25px rgba(102, 126, 234, 0.5);
	}

	.product-name {
		font-size: 0.9rem;
		margin: 0;
		color: rgba(255, 255, 255, 0.9);
		font-weight: 500;
		text-shadow: 0 0 5px rgba(255, 255, 255, 0.2);
		flex: 1;
		text-align: left;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
		max-width: 60%;
	}

	.product-price {
		font-size: 1rem;
		color: var(--rainbow-accent);
		font-weight: 600;
		margin: 0;
		text-shadow: 0 0 5px rgba(240, 147, 251, 0.3);
		flex-shrink: 0;
		text-align: right;
		white-space: nowrap;
	}

	/* Style the flex container for name and price */
	.product-info span[style*="flex-direction:row"] {
		width: 100%;
		align-items: center;
		justify-content: space-between;
		gap: 0.5rem;
	}

	/* Image Loading States */
	.image-placeholder {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		background: rgba(255, 255, 255, 0.1);
		border-radius: 15px;
	}

	.loading-spinner {
		width: 40px;
		height: 40px;
		border: 3px solid rgba(255, 255, 255, 0.2);
		border-top: 3px solid var(--rainbow-primary);
		border-radius: 50%;
		animation: spin 1s linear infinite;
	}

	@keyframes spin {
		0% { transform: rotate(0deg); }
		100% { transform: rotate(360deg); }
	}

	/* Mobile Optimizations for Gallery */
	@media (max-width: 768px) {
		.product-gallery {
			background-attachment: scroll;
			padding: 3rem 0.5rem;
		}

		.product-grid {
			grid-template-columns: repeat(2, 1fr);
			gap: 0.8rem;
			grid-auto-rows: 240px;
			max-width: 100%;
			margin: 0;
			padding: 0 0.5rem;
		}

		.card-3d-wrapper {
			border-radius: 15px;
		}

		.product-info {
			padding: 0.6rem;
			min-height: 70px;
		}

		.product-category {
			font-size: 0.65rem;
			padding: 0.3rem 0.8rem;
			margin-bottom: 0.3rem;
		}

		.product-name {
			font-size: 0.8rem;
			margin: 0;
			max-width: 55%;
		}

		.product-price {
			font-size: 0.9rem;
		}

		.product-info span[style*="flex-direction:row"] {
			gap: 0.3rem;
		}

		/* Reduce 3D effects on mobile for performance */
		.floating-3d:hover .card-3d-wrapper {
			transform: translateY(-8px) scale(1.02);
		}

		.floating-3d:hover .floating-image {
			transform: translateZ(20px) scale(1.03);
		}

		/* Ensure cards don't overflow on small screens */
		.product-card {
			min-width: 0; /* Allow cards to shrink */
			width: 100%;
		}

		.card-3d-wrapper {
			width: 100%;
			box-sizing: border-box;
		}
	}

	/* Global scrollbar hiding - Chrome fix */
	:global(html::-webkit-scrollbar),
	:global(body::-webkit-scrollbar),
	:global(*::-webkit-scrollbar) {
		display: none !important;
		width: 0 !important;
		height: 0 !important;
		background: transparent !important;
	}

	:global(html),
	:global(body) {
		scrollbar-width: none !important;
		-ms-overflow-style: none !important;
	}


	/* Extra small mobile screens */
	@media (max-width: 480px) {
		.product-gallery {
			padding: 2rem 0.25rem;
		}

		.product-grid {
			gap: 0.6rem;
			padding: 0 0.25rem;
		}
	}

	/* Tablet styles */
	@media (max-width: 1024px) and (min-width: 769px) {
		.product-grid {
			grid-template-columns: repeat(3, 1fr);
			gap: 1.2rem;
			grid-auto-rows: 260px;
		}
	}

	/* Thank You Section */
	.thank-you-section {
		min-height: 100vh;
		padding: 6rem 2rem;
		background: url('/GsapResources/backgrounds/white_3d_background.jpg');
		background-size: cover;
		background-position: center;
		background-attachment: fixed;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
		z-index: 3;
	}

	.thank-you-content {
		text-align: center;
		max-width: 800px;
		margin: 0 auto;
	}

	.thank-you-title {
		font-size: clamp(3rem, 8vw, 6rem);
		margin: 0 0 2rem 0;
		line-height: 0.9;
		font-weight: normal;
	}

	.thank-you-message {
		font-size: clamp(1.2rem, 3vw, 2rem);
		margin: 0 0 3rem 0;
		background-clip: text;
		text-shadow: 0 0 20px rgba(146, 197, 247, 0.3);
		opacity: 0.95;
	}

	.contact-us-text {
		font-size: clamp(1rem, 2vw, 1.3rem);
		margin: 0 0 2rem 0;
		color: var(--rainbow-dark);
		opacity: 0.8;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		font-weight: 500;
	}

	/* Social Links */
	.social-links {
		display: flex;
		justify-content: center;
		gap: 2rem;
		margin-bottom: 2rem;
	}

	.social-link {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 60px;
		height: 60px;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.1);
		backdrop-filter: blur(10px);
		border: 1px solid rgba(255, 255, 255, 0.2);
		transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
		text-decoration: none;
		position: relative;
		overflow: hidden;
	}

	.social-link::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: linear-gradient(135deg, var(--rainbow-primary), var(--rainbow-accent));
		opacity: 0;
		transition: opacity 0.3s ease;
	}

	.social-link:hover {
		transform: translateY(-10px) scale(1.1);
		box-shadow: 0 20px 30px rgba(0, 0, 0, 0.2);
	}

	.social-link:hover::before {
		opacity: 1;
	}

	.social-link img {
		width: 30px;
		height: 30px;
		filter: brightness(0.7);
		transition: all 0.3s ease;
		position: relative;
		z-index: 1;
	}

	.social-link:hover img {
		filter: brightness(1) invert(1);
		transform: scale(1.1);
	}

	/* Footer */
	.footer {
		background: linear-gradient(180deg, 
			rgba(0, 0, 0, 0.95) 0%,
			rgba(15, 15, 25, 1) 100%
		);
		padding: 2rem;
		position: relative;
		z-index: 4;
	}

	.footer-content {
		max-width: 1200px;
		margin: 0 auto;
		text-align: center;
		display: flex;
		justify-content: space-between;
		align-items: center;
		flex-wrap: wrap;
		gap: 1rem;
	}

	.copyright {
		color: rgba(255, 255, 255, 0.7);
		margin: 0;
		font-size: 0.9rem;
	}

	.made-with-love {
		color: rgba(255, 255, 255, 0.8);
		margin: 0;
		font-size: 1rem;
	}

	.made-with-love a {
		color: var(--rainbow-accent);
		text-decoration: none;
		transition: color 0.3s ease;
	}

	.made-with-love a:hover {
		color: var(--rainbow-primary);
		text-decoration: underline;
	}

	/* Mobile optimizations for Thank You section */
	@media (max-width: 768px) {
		.thank-you-section {
			padding: 4rem 1rem;
			background-attachment: scroll;
		}

		.social-links {
			gap: 1.5rem;
		}

		.social-link {
			width: 50px;
			height: 50px;
		}

		.social-link img {
			width: 25px;
			height: 25px;
		}

		.footer-content {
			flex-direction: column;
			text-align: center;
		}

		.copyright, .made-with-love {
			font-size: 0.8rem;
		}
	}

	/* Modal Styles */
	.modal-overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 100vh;
		background: rgba(0, 0, 0, 0.8);
		backdrop-filter: blur(10px);
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 1000;
		padding: 2rem;
	}

	.modal-container {
		width: 90vw;
		height: 90vh;
		max-width: 1200px;
		max-height: 800px;
		background: rgba(255, 255, 255, 0.05);
		backdrop-filter: blur(20px);
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 25px;
		position: relative;
		overflow: hidden;
		box-shadow: 
			0 40px 80px rgba(0, 0, 0, 0.3),
			0 0 0 1px rgba(255, 255, 255, 0.1),
			inset 0 1px 0 rgba(255, 255, 255, 0.2);
	}

	.modal-close-btn {
		position: absolute;
		top: 1.5rem;
		right: 1.5rem;
		width: 50px;
		height: 50px;
		border: none;
		background: rgba(255, 255, 255, 0.1);
		backdrop-filter: blur(10px);
		border: 1px solid rgba(255, 255, 255, 0.2);
		border-radius: 50%;
		color: rgba(255, 255, 255, 0.8);
		cursor: pointer;
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 10;
		transition: all 0.3s ease;
	}

	.modal-close-btn:hover {
		background: rgba(255, 255, 255, 0.2);
		color: white;
		transform: scale(1.1);
		box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
	}

	.modal-content {
		display: flex;
		width: 100%;
		height: 100%;
	}

	.modal-image-section {
		flex: 60%;
		display: flex;
		align-items: center;
		justify-content: center;
		padding: 3rem;
		background: linear-gradient(135deg, 
			rgba(255, 255, 255, 0.1) 0%, 
			rgba(255, 255, 255, 0.05) 100%);
	}

	.modal-product-image {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 20px;
		background: rgba(255, 255, 255, 0.05);
		backdrop-filter: blur(5px);
		border: 1px solid rgba(255, 255, 255, 0.1);
		position: relative;
		overflow: hidden;
	}

	.modal-image {
		max-width: 80%;
		max-height: 80%;
		object-fit: contain;
		filter: drop-shadow(0 20px 40px rgba(0, 0, 0, 0.3));
		transition: all 0.4s ease;
	}

	.modal-image:hover {
		transform: scale(1.05);
		filter: drop-shadow(0 30px 60px rgba(0, 0, 0, 0.4));
	}

	.modal-details-section {
		flex: 40%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		padding: 3rem 2rem;
		background: linear-gradient(180deg, 
			rgba(255, 255, 255, 0.08) 0%, 
			rgba(255, 255, 255, 0.03) 100%);
		border-left: 1px solid rgba(255, 255, 255, 0.1);
	}

	.modal-product-info {
		display: flex;
		flex-direction: column;
		gap: 1.5rem;
		height: 100%;
		justify-content: center;
	}

	.modal-category {
		display: inline-block;
		background: linear-gradient(135deg, var(--rainbow-primary), var(--rainbow-accent));
		color: white;
		padding: 0.6rem 1.5rem;
		border-radius: 25px;
		font-size: 0.9rem;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		font-weight: 600;
		box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
		align-self: flex-start;
	}

	.modal-product-name {
		font-size: clamp(1.8rem, 4vw, 2.5rem);
		margin: 0;
		color: rgba(255, 255, 255, 0.95);
		font-weight: 600;
		text-shadow: 0 0 10px rgba(255, 255, 255, 0.3);
		line-height: 1.2;
	}

	.modal-product-price {
		font-size: clamp(1.4rem, 3vw, 2rem);
		color: var(--rainbow-accent);
		font-weight: 700;
		margin: 0;
		text-shadow: 0 0 10px rgba(240, 147, 251, 0.4);
	}

	.modal-product-description {
		font-size: 1.1rem;
		line-height: 1.6;
		color: rgba(255, 255, 255, 0.8);
		margin: 0;
		text-shadow: 0 0 5px rgba(255, 255, 255, 0.1);
	}

	.modal-product-tags {
		display: flex;
		flex-wrap: wrap;
		gap: 0.8rem;
		margin-top: 1rem;
	}

	.product-tag {
		background: rgba(255, 255, 255, 0.1);
		backdrop-filter: blur(10px);
		border: 1px solid rgba(255, 255, 255, 0.2);
		color: rgba(255, 255, 255, 0.9);
		padding: 0.5rem 1rem;
		border-radius: 20px;
		font-size: 0.85rem;
		font-weight: 500;
		text-transform: capitalize;
		letter-spacing: 0.03em;
		transition: all 0.3s ease;
		position: relative;
		overflow: hidden;
	}

	.product-tag::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: linear-gradient(135deg, var(--rainbow-primary), var(--rainbow-accent));
		opacity: 0;
		transition: opacity 0.3s ease;
	}

	.product-tag:hover {
		transform: translateY(-2px);
		box-shadow: 0 8px 20px rgba(102, 126, 234, 0.3);
		color: white;
	}

	.product-tag:hover::before {
		opacity: 1;
	}

	.product-tag:hover {
		position: relative;
		z-index: 1;
	}

	/* Modal Mobile Optimizations */
	@media (max-width: 768px) {
		.modal-overlay {
			padding: 1rem;
		}

		.modal-container {
			width: 95vw;
			height: 95vh;
			border-radius: 20px;
		}

		.modal-content {
			flex-direction: column;
		}

		.modal-image-section {
			flex: 50%;
			padding: 2rem 1rem;
		}

		.modal-details-section {
			flex: 50%;
			padding: 2rem 1.5rem;
			border-left: none;
			border-top: 1px solid rgba(255, 255, 255, 0.1);
		}

		.modal-product-info {
			gap: 1rem;
		}

		.modal-close-btn {
			top: 1rem;
			right: 1rem;
			width: 45px;
			height: 45px;
		}

		.modal-category {
			padding: 0.5rem 1.2rem;
			font-size: 0.8rem;
		}

		.modal-product-description {
			font-size: 1rem;
			line-height: 1.5;
		}

		.modal-product-tags {
			gap: 0.6rem;
		}

		.product-tag {
			padding: 0.4rem 0.8rem;
			font-size: 0.8rem;
		}
	}

	/* Extra small mobile screens */
	@media (max-width: 480px) {
		.modal-container {
			width: 98vw;
			height: 98vh;
			border-radius: 15px;
		}

		.modal-image-section {
			padding: 1.5rem 1rem;
		}

		.modal-details-section {
			padding: 1.5rem 1rem;
		}

		.modal-product-info {
			gap: 0.8rem;
		}
	}
</style>
