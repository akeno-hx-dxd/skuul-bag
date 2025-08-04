<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let contactTitle: HTMLElement;
	let contactForm: HTMLElement;
	let formFields: HTMLElement[] = [];
	
	// Form data
	let formData = {
		name: '',
		email: '',
		subject: '',
		customSubject: '',
		message: '',
		priority: 'normal'
	};

	// Predefined subject options
	const subjectOptions = [
		{ value: 'product-inquiry', label: 'Product Inquiry' },
		{ value: 'bulk-order', label: 'Bulk Order Request' },
		{ value: 'quality-issue', label: 'Quality Issue' },
		{ value: 'partnership', label: 'Partnership Opportunity' },
		{ value: 'wholesale', label: 'Wholesale Pricing' },
		{ value: 'support', label: 'Customer Support' },
		{ value: 'feedback', label: 'Feedback & Suggestions' },
		{ value: 'custom', label: 'Other (Custom Subject)' }
	];

	function getSubjectText(): string {
		if (formData.subject === 'custom') {
			return formData.customSubject || 'General Inquiry';
		}
		const option = subjectOptions.find(opt => opt.value === formData.subject);
		return option?.label || 'General Inquiry';
	}

	function handleSubmit() {
		// Validate form
		if (!formData.name.trim()) {
			alert('Please enter your name');
			return;
		}
		if (!formData.email.trim()) {
			alert('Please enter your email');
			return;
		}
		if (!formData.message.trim()) {
			alert('Please enter your message');
			return;
		}
		if (formData.subject === 'custom' && !formData.customSubject.trim()) {
			alert('Please enter a custom subject');
			return;
		}

		// Prepare email data
		const subject = encodeURIComponent(`[Skuulbag] ${getSubjectText()}`);
		const body = encodeURIComponent(
			`Name: ${formData.name}\n` +
			`Email: ${formData.email}\n` +
			`Priority: ${formData.priority.charAt(0).toUpperCase() + formData.priority.slice(1)}\n` +
			`Subject: ${getSubjectText()}\n\n` +
			`Message:\n${formData.message}\n\n` +
			`---\n` +
			`Sent from Skuulbag Contact Form`
		);

		// Create mailto link
		const mailtoLink = `mailto:hello@skuulbag.com?subject=${subject}&body=${body}`;
		
		// Try to open email client
		try {
			const link = document.createElement('a');
			link.href = mailtoLink;
			link.target = '_blank';
			link.rel = 'noopener noreferrer';
			document.body.appendChild(link);
			link.click();
			document.body.removeChild(link);
			
			// Show fallback options after a delay
			setTimeout(() => {
				showEmailFallback(mailtoLink, subject, body);
			}, 2000);
		} catch (error) {
			console.error('Failed to open email client:', error);
			showEmailFallback(mailtoLink, subject, body);
		}
	}

	function showEmailFallback(mailtoLink: string, subject: string, body: string) {
		const decodedSubject = decodeURIComponent(subject);
		const decodedBody = decodeURIComponent(body);
		
		const fallbackMessage = 
			`If your email client didn't open, you can:\n\n` +
			`1. Copy this email address: hello@skuulbag.com\n` +
			`2. Copy this subject: ${decodedSubject}\n` +
			`3. Copy this message:\n\n${decodedBody}\n\n` +
			`Or try the mailto link again?`;
			
		if (confirm(fallbackMessage)) {
			// Try mailto link one more time
			window.open(mailtoLink, '_blank');
		} else {
			// Copy email details to clipboard
			navigator.clipboard.writeText(
				`To: hello@skuulbag.com\nSubject: ${decodedSubject}\n\n${decodedBody}`
			).then(() => {
				alert('Email details copied to clipboard!');
			}).catch(() => {
				alert('Please manually copy the email details from the previous message.');
			});
		}
	}

	function resetForm() {
		formData = {
			name: '',
			email: '',
			subject: '',
			customSubject: '',
			message: '',
			priority: 'normal'
		};
	}

	onMount(() => {
		// Register ScrollTrigger
		gsap.registerPlugin(ScrollTrigger);

		// Initial animations
		gsap.set([contactTitle, contactForm], { y: 60, opacity: 0 });

		// Create timeline for form entrance
		const formTimeline = gsap.timeline({
			scrollTrigger: {
				trigger: contactForm,
				start: "top 80%",
				toggleActions: "play none none none",
				once: true
			}
		});

		formTimeline
			.to(contactTitle, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			})
			.to(contactForm, {
				duration: 0.8,
				y: 0,
				opacity: 1,
				ease: "power2.out"
			}, "-=0.4");

		// Stagger form field animations
		gsap.set(".form-field", { y: 30, opacity: 0 });
		gsap.to(".form-field", {
			duration: 0.6,
			y: 0,
			opacity: 1,
			ease: "power2.out",
			stagger: 0.1,
			scrollTrigger: {
				trigger: contactForm,
				start: "top 70%",
				toggleActions: "play none none none",
				once: true
			},
			delay: 0.4
		});

		// Cleanup
		return () => {
			ScrollTrigger.getAll().forEach(trigger => trigger.kill());
		};
	});
</script>

<svelte:head>
	<title>Contact Us - Skuulbag</title>
	<meta name="description" content="Get in touch with Skuulbag for product inquiries, bulk orders, partnerships, and customer support. Quality stationery that doesn't break the bank." />
</svelte:head>

<div class="contact-wrapper">
	<div class="contact-content">
		<!-- Header Section -->
		<section class="contact-header">
			<h1 bind:this={contactTitle} class="contact-title font-milky-walky rainbow-text-gradient">
				Contact Us
			</h1>
			<p class="contact-subtitle font-indie-flower">
				We'd love to hear from you! Get in touch for any inquiries.
			</p>
		</section>

		<!-- Contact Form -->
		<section bind:this={contactForm} class="contact-form-section">
			<div class="form-container">
				<h2 class="form-title font-indie-flower">Send us a message</h2>
				
				<form on:submit|preventDefault={handleSubmit} class="contact-form">
					<!-- Name Field -->
					<div class="form-field">
						<label for="name" class="form-label font-jetbrains-mono">Your Name *</label>
						<input
							type="text"
							id="name"
							bind:value={formData.name}
							placeholder="Enter your full name"
							class="form-input font-jetbrains-mono"
							required
						/>
					</div>

					<!-- Email Field -->
					<div class="form-field">
						<label for="email" class="form-label font-jetbrains-mono">Email Address *</label>
						<input
							type="email"
							id="email"
							bind:value={formData.email}
							placeholder="your.email@example.com"
							class="form-input font-jetbrains-mono"
							required
						/>
					</div>

					<!-- Subject Selection -->
					<div class="form-field">
						<label for="subject" class="form-label font-jetbrains-mono">Subject *</label>
						<select
							id="subject"
							bind:value={formData.subject}
							class="form-select font-jetbrains-mono"
							required
						>
							<option value="">Select a subject...</option>
							{#each subjectOptions as option}
								<option value={option.value}>{option.label}</option>
							{/each}
						</select>
					</div>

					<!-- Custom Subject Field -->
					{#if formData.subject === 'custom'}
						<div class="form-field custom-subject">
							<label for="customSubject" class="form-label font-jetbrains-mono">Custom Subject *</label>
							<input
								type="text"
								id="customSubject"
								bind:value={formData.customSubject}
								placeholder="Enter your custom subject"
								class="form-input font-jetbrains-mono"
								required
							/>
						</div>
					{/if}

					<!-- Priority Selection -->
					<div class="form-field">
						<label for="priority" class="form-label font-jetbrains-mono">Priority Level</label>
						<select
							id="priority"
							bind:value={formData.priority}
							class="form-select font-jetbrains-mono"
						>
							<option value="low">Low Priority</option>
							<option value="normal">Normal Priority</option>
							<option value="high">High Priority</option>
							<option value="urgent">Urgent</option>
						</select>
					</div>

					<!-- Message Field -->
					<div class="form-field">
						<label for="message" class="form-label font-jetbrains-mono">Your Message *</label>
						<textarea
							id="message"
							bind:value={formData.message}
							placeholder="Tell us about your inquiry, requirements, or any questions you have..."
							rows="6"
							class="form-textarea font-jetbrains-mono"
							required
						></textarea>
					</div>

					<!-- Form Actions -->
					<div class="form-actions">
						<button type="submit" class="btn-primary font-jetbrains-mono" on:click={handleSubmit}>
							<svg width="18" height="18" viewBox="0 0 24 24" fill="none" class="btn-icon">
								<path d="M4 7L10.94 13.94L20 4.94" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
								<path d="M20 4.94V18.94C20 19.49 19.55 19.94 19 19.94H5C4.45 19.94 4 19.49 4 18.94V7L12 13L20 7" fill="currentColor" opacity="0.1"/>
							</svg>
							Open Email Client
						</button>
						<button type="button" on:click={resetForm} class="btn-secondary font-jetbrains-mono">
							<svg width="18" height="18" viewBox="0 0 24 24" fill="none" class="btn-icon">
								<path d="M3 12C3 7.02944 7.02944 3 12 3C16.9706 3 21 7.02944 21 12C21 16.9706 16.9706 21 12 21C7.02944 21 3 16.9706 3 12Z" stroke="currentColor" stroke-width="2"/>
								<path d="M9 15L15 9" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
								<path d="M15 15L9 9" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
							</svg>
							Clear Form
						</button>
					</div>
				</form>

				<!-- Contact Info -->
				<div class="contact-info">
					<h3 class="info-title font-indie-flower">Other ways to reach us</h3>
					<div class="info-grid">
						<div class="info-item">
							<h4 class="font-jetbrains-mono">
								<svg width="20" height="20" viewBox="0 0 24 24" fill="none" class="info-icon">
									<path d="M4 4H20C21.1 4 22 4.9 22 6V18C22 19.1 21.1 20 20 20H4C2.9 20 2 19.1 2 18V6C2 4.9 2.9 4 4 4Z" stroke="currentColor" stroke-width="2"/>
									<path d="M22 6L12 13L2 6" stroke="currentColor" stroke-width="2"/>
								</svg>
								Email
							</h4>
							<p class="font-jetbrains-mono">hello@skuulbag.com</p>
						</div>
						<div class="info-item">
							<h4 class="font-jetbrains-mono">
								<svg width="20" height="20" viewBox="0 0 24 24" fill="none" class="info-icon">
									<path d="M22 16.92V19.92C22 20.52 21.39 21 20.66 21C9.93 21 1 12.07 1 1.34C1 0.61 1.48 0 2.08 0H5.08C5.68 0 6.16 0.48 6.16 1.08C6.16 3.12 6.86 5.1 8.12 6.78C8.51 7.31 8.38 8.07 7.85 8.46L6.31 9.57C7.91 12.85 11.15 16.09 14.43 17.69L15.54 16.15C15.93 15.62 16.69 15.49 17.22 15.88C18.9 17.14 20.88 17.84 22.92 17.84C23.52 17.84 24 18.32 24 18.92Z" fill="currentColor"/>
								</svg>
								Phone
							</h4>
							<p class="font-jetbrains-mono">+91 98765 43210</p>
						</div>
						<div class="info-item">
							<h4 class="font-jetbrains-mono">
								<svg width="20" height="20" viewBox="0 0 24 24" fill="none" class="info-icon">
									<circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="2"/>
									<path d="M12 6V12L16 14" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
								</svg>
								Response Time
							</h4>
							<p class="font-jetbrains-mono">Within 24 hours</p>
						</div>
						<div class="info-item">
							<h4 class="font-jetbrains-mono">
								<svg width="20" height="20" viewBox="0 0 24 24" fill="none" class="info-icon">
									<rect x="3" y="4" width="18" height="18" rx="2" ry="2" stroke="currentColor" stroke-width="2"/>
									<line x1="16" y1="2" x2="16" y2="6" stroke="currentColor" stroke-width="2"/>
									<line x1="8" y1="2" x2="8" y2="6" stroke="currentColor" stroke-width="2"/>
									<line x1="3" y1="10" x2="21" y2="10" stroke="currentColor" stroke-width="2"/>
								</svg>
								Business Hours
							</h4>
							<p class="font-jetbrains-mono">Mon-Fri, 9 AM - 6 PM IST</p>
						</div>
					</div>
				</div>
			</div>
		</section>
	</div>
</div>

<style>
	/* Base Layout */
	.contact-wrapper {
		min-height: 100vh;
		background: #f5deb3; /* Wheat color */
		scroll-behavior: smooth;
		padding: 2rem 0;
	}

	.contact-content {
		max-width: 1000px;
		margin: 0 auto;
		padding: 6rem 2rem 4rem;
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

	/* Header Section */
	.contact-header {
		text-align: center;
		margin-bottom: 4rem;
	}

	.contact-title {
		font-size: clamp(3rem, 8vw, 5rem);
		margin: 0 0 1rem 0;
		line-height: 0.9;
		font-weight: normal;
	}

	.contact-subtitle {
		font-size: clamp(1.1rem, 2.5vw, 1.4rem);
		color: var(--rainbow-dark);
		opacity: 0.8;
		margin: 0;
	}

	/* Form Section */
	.contact-form-section {
		margin-top: 3rem;
	}

	.form-container {
		background: rgba(255, 255, 255, 0.25);
		backdrop-filter: blur(15px);
		border: 1px solid rgba(255, 255, 255, 0.3);
		border-radius: 25px;
		padding: 3rem;
		box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
	}

	.form-title {
		font-size: clamp(1.5rem, 3vw, 2rem);
		color: var(--rainbow-dark);
		opacity: 0.9;
		margin: 0 0 2rem 0;
		text-align: center;
	}

	.contact-form {
		display: grid;
		gap: 1.5rem;
		margin-bottom: 3rem;
	}

	.form-field {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}

	.form-field.custom-subject {
		animation: slideDown 0.3s ease-out;
	}

	@keyframes slideDown {
		from { opacity: 0; transform: translateY(-10px); }
		to { opacity: 1; transform: translateY(0); }
	}

	.form-label {
		font-size: 0.9rem;
		font-weight: 600;
		color: #8B4513;
		margin-bottom: 0.5rem;
	}

	.form-input,
	.form-select,
	.form-textarea {
		padding: 1rem 1.2rem;
		border: 2px solid rgba(139, 69, 19, 0.2);
		border-radius: 12px;
		background: rgba(255, 255, 255, 0.6);
		backdrop-filter: blur(10px);
		font-size: 0.95rem;
		color: #654321;
		transition: all 0.3s ease;
		resize: vertical;
	}

	.form-input:focus,
	.form-select:focus,
	.form-textarea:focus {
		outline: none;
		border-color: #CD853F;
		background: rgba(255, 255, 255, 0.8);
		box-shadow: 0 0 0 3px rgba(205, 133, 63, 0.2);
		transform: translateY(-2px);
	}

	.form-textarea {
		min-height: 120px;
		font-family: inherit;
	}

	.form-actions {
		display: flex;
		gap: 1rem;
		justify-content: center;
		flex-wrap: wrap;
		margin-top: 2rem;
	}

	.btn-primary,
	.btn-secondary {
		padding: 1rem 2rem;
		border-radius: 12px;
		font-size: 1rem;
		font-weight: 600;
		text-transform: uppercase;
		letter-spacing: 0.05em;
		transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
		border: 2px solid transparent;
		cursor: pointer;
		position: relative;
		overflow: hidden;
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.btn-icon {
		transition: transform 0.3s ease;
	}

	.btn-primary:hover .btn-icon,
	.btn-secondary:hover .btn-icon {
		transform: scale(1.1);
	}

	.btn-primary {
		background: linear-gradient(135deg, #8B4513, #CD853F, #D2691E);
		color: white;
		border: 2px solid rgba(139, 69, 19, 0.3);
		box-shadow: 0 8px 20px rgba(139, 69, 19, 0.3);
	}

	.btn-primary:hover {
		transform: translateY(-3px) scale(1.02);
		background: linear-gradient(135deg, #A0522D, #DEB887, #F4A460);
		box-shadow: 0 12px 30px rgba(139, 69, 19, 0.4);
	}

	.btn-secondary {
		background: rgba(139, 69, 19, 0.1);
		backdrop-filter: blur(10px);
		color: #8B4513;
		border: 2px solid rgba(139, 69, 19, 0.3);
	}

	.btn-secondary:hover {
		transform: translateY(-3px) scale(1.02);
		background: rgba(139, 69, 19, 0.2);
		border-color: rgba(139, 69, 19, 0.5);
	}

	/* Contact Info */
	.contact-info {
		border-top: 1px solid rgba(139, 69, 19, 0.2);
		padding-top: 2rem;
	}

	.info-title {
		font-size: 1.3rem;
		color: var(--rainbow-dark);
		opacity: 0.9;
		margin: 0 0 1.5rem 0;
		text-align: center;
	}

	.info-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		gap: 1.5rem;
	}

	.info-item {
		text-align: center;
		padding: 1.5rem;
		background: rgba(255, 255, 255, 0.3);
		backdrop-filter: blur(10px);
		border-radius: 15px;
		border: 1px solid rgba(255, 255, 255, 0.4);
		transition: all 0.3s ease;
	}

	.info-item:hover {
		transform: translateY(-3px);
		background: rgba(255, 255, 255, 0.4);
		box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
	}

	.info-item h4 {
		font-size: 1rem;
		color: #8B4513;
		margin: 0 0 0.5rem 0;
		font-weight: 600;
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
	}

	.info-icon {
		transition: transform 0.3s ease;
	}

	.info-item:hover .info-icon {
		transform: scale(1.1);
	}

	.info-item p {
		font-size: 0.9rem;
		color: #654321;
		margin: 0;
		opacity: 0.8;
	}

	/* Mobile Optimizations */
	@media (max-width: 768px) {
		.contact-content {
			padding: 4rem 1rem 3rem;
		}

		.form-container {
			padding: 2rem 1.5rem;
			border-radius: 20px;
		}

		.form-actions {
			flex-direction: column;
			align-items: center;
		}

		.btn-primary,
		.btn-secondary {
			width: 100%;
			max-width: 300px;
		}

		.info-grid {
			grid-template-columns: 1fr;
			gap: 1rem;
		}

		.info-item {
			padding: 1.2rem;
		}
	}

	/* Extra small mobile screens */
	@media (max-width: 480px) {
		.contact-content {
			padding: 3rem 0.5rem 2rem;
		}

		.form-container {
			padding: 1.5rem 1rem;
			border-radius: 15px;
		}

		.form-input,
		.form-select,
		.form-textarea {
			padding: 0.8rem 1rem;
		}

		.btn-primary,
		.btn-secondary {
			padding: 0.8rem 1.5rem;
			font-size: 0.9rem;
		}
	}
</style>