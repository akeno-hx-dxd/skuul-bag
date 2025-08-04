<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/state';
	import { gsap } from 'gsap';
	
	let headerRef: HTMLElement;
	let logoRef: HTMLElement;
	let navRef: HTMLElement;
	let mobileMenuOpen = false;
	let selectedLogo = "/GsapResources/logos/3dicons-money-bag-dynamic-color.png";
	
	onMount(() => {
		// Set random logo on client side to prevent hydration error
		selectedLogo = getRandomLogo();
		
		// Initial header animation
		gsap.from(headerRef, {
			duration: 1,
			y: -100,
			opacity: 0,
			ease: "power3.out",
			delay: 0.2
		});
		
		// Logo animation
		gsap.from(logoRef, {
			duration: 0.8,
			scale: 0,
			rotation: 360,
			ease: "back.out(1.7)",
			delay: 0.5
		});
		
		// Navigation items stagger
		gsap.from(".nav-item", {
			duration: 0.6,
			x: 30,
			opacity: 0,
			stagger: 0.1,
			ease: "power2.out",
			delay: 0.8
		});
	});
	
	function toggleMobileMenu() {
		mobileMenuOpen = !mobileMenuOpen;
		
		if (mobileMenuOpen) {
			gsap.to(".mobile-menu", {
				duration: 0.3,
				x: 0,
				ease: "power2.out"
			});
		} else {
			gsap.to(".mobile-menu", {
				duration: 0.3,
				x: "100%",
				ease: "power2.in"
			});
		}
	}

	const availbale_logos = [
		"/GsapResources/logos/3dicons-money-bag-dynamic-color.png",
		"/GsapResources/logos/3dicons-money-bag-dynamic-gradient.png",
		"/GsapResources/logos/3dicons-money-bag-dynamic-premium.png"
	]

	function getRandomLogo() {
		const index = Math.floor(Math.random() * 3);
		return availbale_logos[index];
	}
</script>

<header bind:this={headerRef} class="rainbow-header">
	<div class="header-container">
		<!-- Logo Section -->
		<div bind:this={logoRef} class="logo-section">
			<a href="/" class="logo-link">
				<h1 class="font-milky-walky rainbow-text-gradient">Skuulbag</h1>
				<img src={selectedLogo} alt="Skuulbag" class="logo-img" />
			</a>
		</div>

		<!-- Desktop Navigation -->
		<nav bind:this={navRef} class="desktop-nav">
			<ul>
				<li class="nav-item" aria-current={page.url.pathname === '/' ? 'page' : undefined}>
					<a href="/" class="font-indie-flower">Home</a>
				</li>
				<li class="nav-item" aria-current={page.url.pathname === '/about' ? 'page' : undefined}>
					<a href="/about" class="font-indie-flower">About</a>
				</li>
				<li class="nav-item" aria-current={page.url.pathname === '/contact' ? 'page' : undefined}>
					<a href="/contact" class="font-indie-flower">Contact</a>
				</li>
			</ul>
		</nav>

		<!-- Mobile Menu Button -->
		<button 
			class="mobile-menu-btn md:hidden"
			on:click={toggleMobileMenu}
			aria-label="Toggle menu"
		>
			<span class="hamburger-line"></span>
			<span class="hamburger-line"></span>
			<span class="hamburger-line"></span>
		</button>
	</div>

	<!-- Mobile Menu -->
	<div class="mobile-menu rainbow-gradient">
		<nav>
			<ul>
				<li><a href="/" class="font-indie-flower" on:click={toggleMobileMenu}>Home</a></li>
				<li><a href="/about" class="font-indie-flower" on:click={toggleMobileMenu}>About</a></li>
				<li><a href="/contact" class="font-indie-flower" on:click={toggleMobileMenu}>Contact</a></li>
			</ul>
		</nav>
	</div>
</header>

<style>
	.rainbow-header {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		z-index: 1000;
		backdrop-filter: blur(20px);
		background: rgba(255, 255, 255, 0.1);
		/* border-bottom: 1px solid rgba(255, 255, 255, 0.2); */
		padding: 0.75rem 0;
	}

	.header-container {
		max-width: 1200px;
		margin: 0 auto;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 2rem;
	}

	.logo-section {
		display: flex;
		align-items: center;
	}

	.logo-link {
		text-decoration: none;
		display: flex;
		align-items: center;
	}

	.logo-img {
		height: 45px;
		width: auto;
		filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.1));
	}

	.desktop-nav {
		display: none;
	}

	.desktop-nav ul {
		display: flex;
		list-style: none;
		margin: 0;
		padding: 0;
		gap: 2rem;
	}

	.desktop-nav a {
		text-decoration: none;
		color: var(--rainbow-dark);
		font-weight: 500;
		font-size: 1.2rem;
		transition: all 0.3s ease;
		position: relative;
		padding: 0.5rem 1rem;
		border-radius: 25px;
	}

	.desktop-nav a:hover {
		/* background: rgba(255, 255, 255, 0.2); */
		/* backdrop-filter: blur(10px); */
		text-decoration: underline 2px;
		text-underline-offset: 2px;
		transform: translateY(-2px);
	}

	.nav-item[aria-current='page'] a {
		text-decoration: underline;
		text-underline-offset: 4px;
		text-decoration-thickness: 2px;
		text-decoration-color: var(--rainbow-primary);
	}

	/* Mobile Menu Button */
	.mobile-menu-btn {
		display: flex;
		flex-direction: column;
		gap: 4px;
		background: none;
		border: none;
		cursor: pointer;
		padding: 0.5rem;
		border-radius: 8px;
		transition: background-color 0.3s ease;
	}

	.mobile-menu-btn:hover {
		background: rgba(255, 255, 255, 0.1);
	}

	.hamburger-line {
		width: 24px;
		height: 2px;
		background: var(--rainbow-dark);
		border-radius: 2px;
		transition: all 0.3s ease;
	}

	/* Mobile Menu */
	.mobile-menu {
		/* border: 2px solid black; */
		position: fixed;
		top: 20;
		right: 0;
		width: 100vw;
		height: 100vh;
		/* background: var(--rainbow-gradient); */
		transform: translateX(100%);
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 999;
	}

	.mobile-menu nav ul {
		list-style: none;
		margin: 0;
		padding: 0;
		text-align: center;
	}

	.mobile-menu nav li {
		margin: 2rem 0;
	}

	.mobile-menu nav a {
		color: rgb(0, 0, 0);
		text-decoration: none;
		font-size: 1.5rem;
		font-weight: 600;
		padding: 1rem 2rem;
		border-radius: 12px;
		transition: all 0.3s ease;
		display: block;
	}

	.mobile-menu nav a:hover {
		/* background: rgba(255, 255, 255, 0.2); */
		text-decoration: underlines 2px;
		text-underline-offset: 2px;
		transform: scale(1.05);
	}

	/* Desktop Styles */
	@media (min-width: 768px) {
		.desktop-nav {
			display: block;
		}

		.mobile-menu-btn {
			display: none;
		}

		.logo-img {
			height: 50px;
		}
	}

	/* Large Desktop */
	@media (min-width: 1024px) {
		.header-container {
			padding: 0 4rem;
		}

		.desktop-nav ul {
			gap: 3rem;
		}

		.desktop-nav a {
			font-size: 1.1rem;
		}
	}
</style>
