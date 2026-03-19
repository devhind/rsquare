<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';

  let { children } = $props();

  let isMenuOpen = false;
  let isMobile = false;
  let showBackToTop = false;

  function handleScroll() {
    if (browser) {
      showBackToTop = window.scrollY > 300;
    }
  }

  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
    if (browser) {
      document.body.style.overflow = isMenuOpen ? 'hidden' : '';
    }
  }

  function closeMenu() {
    isMenuOpen = false;
    if (browser) {
      document.body.style.overflow = '';
    }
  }

  function scrollToTop() {
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }

  onMount(() => {
    const mediaQuery = window.matchMedia('(max-width: 820px)');

    function handleMediaChange(e: MediaQueryListEvent) {
      isMobile = e.matches;
      if (!isMobile) {
        closeMenu(); // ensure menu closes when switching to desktop
      }
    }

    mediaQuery.addEventListener('change', handleMediaChange);
    isMobile = mediaQuery.matches;

    window.addEventListener('scroll', handleScroll);

    return () => {
      mediaQuery.removeEventListener('change', handleMediaChange);
      window.removeEventListener('scroll', handleScroll);
    };
  });

  // Cleanup body overflow in case component unmounts while menu is open
  onDestroy(() => {
    if (browser) {
      document.body.style.overflow = '';
    }
  });
import { afterNavigate } from '$app/navigation';

afterNavigate(() => {
  if (location.hash) {
    const el = document.querySelector(location.hash);
    if (el) {
      el.scrollIntoView({ behavior: 'smooth' });
    }
  }
});
</script>

<!-- ENHANCED HEADER with responsive sidebar -->
<header class="header-fixed">
  <div class="header-container">
    <div class="navbar">
      <div class="logo-container">
        <img
          class="logo-img"
          src="/images/LOGO_PDF_invertedColor_page-0001.jpg"
          alt="R Square"
          loading="lazy"
        />
        <span class="logo-text">
          R Square <span class="logo-hr">HR Services</span>
        </span>
      </div>

      <!-- Desktop navigation -->
      <nav class="nav-links desktop-only" aria-label="Main navigation">
        <a href="/">Home</a>
        <a href="/#services">Services</a>
        <a href="/#about">About</a>
        <a href="/#mission">Mission & Vision</a>
        <a href="/#contact">Contact</a>
      </nav>

      <div class="header-buttons desktop-only">
        <a href="/Employer-Registration" class="btn-header btn-register">Registration</a>
        <a href="/employer/post-job" class="btn-header btn-post">Post Job</a>
      </div>

      <!-- Hamburger icon (mobile only) -->
      <button
        class="hamburger mobile-only"
        on:click={toggleMenu}
        aria-label="Menu"
        aria-expanded={isMenuOpen}
        aria-controls="mobile-sidebar"
      >
        <span class="bar"></span>
        <span class="bar"></span>
        <span class="bar"></span>
      </button>
    </div>
  </div>
</header>

<!-- Mobile Sidebar (rendered only on mobile) -->
{#if isMobile}
  <div
    class="sidebar-overlay"
    class:open={isMenuOpen}
    on:click={closeMenu}
    aria-hidden="true">
</div>
  <div
    id="mobile-sidebar"
    class="sidebar"
    class:open={isMenuOpen}
    role="dialog"
    aria-modal="true"
    aria-label="Mobile navigation menu">
    <div class="sidebar-header">
      <img
        class="sidebar-logo"
        src="/images/LOGO_PDF_invertedColor_page-0001.jpg"
        alt="R Square"
        loading="lazy"
      />
      <button class="close-sidebar" on:click={closeMenu} aria-label="Close menu">&times;</button>
    </div>
    <!-- <nav class="sidebar-nav" aria-label="Mobile navigation">
      <a href="/" on:click={closeMenu}>Home</a>
      <a href="#services" on:click={closeMenu}>Services</a>
      <a href="#about" on:click={closeMenu}>About</a>
      <a href="#mission" on:click={closeMenu}>Mission & Vision</a>
      <a href="#contact" on:click={closeMenu}>Contact</a>
    </nav> -->
	<nav class="sidebar-nav">
  <a href="/" on:click={closeMenu}>Home</a>
  <a href="/#services" on:click={closeMenu}>Services</a>
  <a href="/#about" on:click={closeMenu}>About</a>
  <a href="/#mission" on:click={closeMenu}>Mission</a>
  <a href="/#contact" on:click={closeMenu}>Contact</a>
</nav>
    <!-- Sidebar buttons (visible on mobile) – removed 'desktop-only' class -->
    <div class="sidebar-buttons">
      <a href="/Employer-Registration" class="btn-header btn-register" on:click={closeMenu}>
        Registration
      </a>
      <a href="/employer/post-job" class="btn-header btn-post" on:click={closeMenu}>
        Post Job
      </a>
    </div>
  </div>
{/if}

<!-- MAIN CONTENT -->
<main class="site-container" id="main-content">
  {@render children()}
</main>

<!-- FOOTER -->
<footer>
  <div class="footer">
    <div class="footer-inner">
      <div class="footer-col">
        <div class="footer-logo">
          <img
            class="logo-img"
            src="/images/LOGO_PDF_invertedColor_page-0001.jpg"
            alt="R Square HR"
            loading="lazy"
          />
          <span>R Square <span class="accent">HR</span></span>
        </div>
        <p>MSME company anchored on growth, professionalism, dignity & diversity.</p>
      </div>
      <div class="footer-links">
        <div>
          <h5>Services</h5>
          <a href="#services">Recruitment</a>
          <a href="#services">Training</a>
          <a href="#services">Payroll</a>
          <a href="#services">NAPS</a>
        </div>
        <div>
          <h5>About</h5>
          <a href="#about">Mission</a>
          <a href="#about">Founder</a>
          <a href="#about">Awards</a>
        </div>
        <div>
          <h5>Connect</h5>
          <a href="#contact">Contact</a>
          <a href="#">LinkedIn</a>
          <a href="#">Email</a>
        </div>
      </div>
    </div>
  </div>
</footer>

<!-- BACK TO TOP -->
{#if showBackToTop}
  <button class="back-to-top" on:click={scrollToTop} aria-label="Scroll to top">
    <img src="/images/up-arrow.png" alt="" />
  </button>
{/if}

<style>
  /* ===== RESET & BASE ===== */
  :global(body) {
    margin: 0;
    font-family: 'Inter', sans-serif;
    background-color: #faf7f2;
    color: #2e3b4e;
    scroll-behavior: smooth;
    padding-top: 90px;
    overflow-x: hidden;
  }

  .site-container {
    max-width: 1440px;
    margin: 0 auto;
    padding: 0 32px;
    width: 100%;
  }

  /* ===== ANIMATIONS ===== */
  .section-animate {
    opacity: 1;
    transform: translateY(0);
    transition: opacity 0.8s cubic-bezier(0.2, 0.9, 0.3, 1),
      transform 0.8s cubic-bezier(0.2, 0.9, 0.3, 1);
  }

  /* ===== HEADER ===== */
  .header-fixed {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    background: linear-gradient(135deg, #6b0f9c 0%, #b91372 40%, #b91372 100%);
    backdrop-filter: blur(8px);
    box-shadow: 0 8px 25px rgba(0, 20, 30, 0.3);
    z-index: 1000;
    border-bottom: 1px solid rgba(239, 185, 120, 0.3);
  }

  .header-container {
    max-width: 1440px;
    margin: 0 auto;
    padding: 0 32px;
    width: 100%;
  }

  .navbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 0;
    gap: 20px;
    flex-wrap: nowrap;
  }

  .logo-container {
    display: flex;
    align-items: center;
    gap: 8px; /* reduced from 12px to compensate for removed 'right' offsets */
    color: white;
  }

  .logo-img {
    width: 55px;
    height: auto;
    filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
  }

  .logo-text {
    font-weight: 700;
    font-size: 26px;
    letter-spacing: -0.5px;
    text-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    /* removed 'right: 30px' – use margin/gap instead */
  }

  .logo-hr {
    color: #efb978;
    font-weight: 600;
    font-size: 20px;
    margin-left: 5px;
  }

  .nav-links {
    display: flex;
    gap: 44px;
    font-weight: 500;
    flex-wrap: wrap;
  }

  .nav-links a {
    text-decoration: none;
    color: rgba(255, 255, 255, 0.9);
    transition: all 0.3s ease;
    position: relative;
    font-size: 1.05rem;
    padding: 5px 0;
  }

  .nav-links a::after {
    content: '';
    position: absolute;
    bottom: -3px;
    left: 0;
    width: 0%;
    height: 2px;
    background: linear-gradient(90deg, #efb978, #d96c4e);
    transition: width 0.3s ease;
    border-radius: 2px;
  }

  .nav-links a:hover {
    color: white;
    text-shadow: 0 0 8px rgba(239, 185, 120, 0.5);
  }

  .nav-links a:hover::after {
    width: 100%;
  }

  .header-buttons {
    display: flex;
    gap: 16px;
    align-items: center;
    flex-wrap: wrap;
  }

  .btn-header {
    padding: 10px 28px;
    border-radius: 40px;
    font-weight: 600;
    font-size: 0.95rem;
    border: 1.5px solid transparent;
    cursor: pointer;
    transition: all 0.3s ease;
    letter-spacing: 0.4px;
    background: transparent;
    color: white;
    border-color: rgba(255, 255, 255, 0.6);
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    text-decoration: none;
    display: inline-block;
    text-align: center;
  }

  .btn-header:hover {
    transform: translateY(-3px);
    box-shadow: 0 12px 20px -8px rgba(0, 0, 0, 0.3);
  }

  .btn-register {
    border-color: white;
    color: white;
    background: rgba(239, 185, 120, 0.05);
  }

  .btn-register:hover {
    background: white;
    color: #1e3a4a;
    border-color: white;
    box-shadow: 0 0 15px #efb978;
    background: linear-gradient(135deg, #6b0f9c 0%, #b91372 40%, #b91372 100%);
  }

  .btn-post {
    background: #efb978;
    border-color: #efb978;
    color: #1e3a4a;
  }

  .btn-post:hover {
    background: #f3ca9c;
    border-color: #f3ca9c;
    box-shadow: 0 0 15px #f3ca9c;
  }

  /* ----- Responsive helpers ----- */
  .desktop-only {
    display: flex;
  }

  .mobile-only {
    display: none;
  }

  @media (max-width: 820px) {
    .desktop-only {
      display: none !important;
    }
    .mobile-only {
      display: flex !important;
    }
  }

  /* Hamburger button */
  .hamburger {
    background: transparent;
    border: none;
    cursor: pointer;
    padding: 10px;
    flex-direction: column;
    gap: 6px;
  }

  .hamburger .bar {
    width: 30px;
    height: 3px;
    background-color: white;
    border-radius: 10px;
    transition: 0.3s;
  }

  /* Sidebar overlay */
  .sidebar-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(4px);
    z-index: 1001;
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.3s ease, visibility 0.3s ease;
  }

  .sidebar-overlay.open {
    opacity: 1;
    visibility: visible;
  }

  /* Sidebar (drawer) */
  .sidebar {
    position: fixed;
    top: 0;
    left: -320px;
    width: 280px;
    height: 100%;
    z-index: 1002;
    padding: 24px 20px;
    box-shadow: 4px 0 20px rgba(0, 0, 0, 0.3);
    transition: left 0.3s ease;
    display: flex;
    flex-direction: column;
    overflow-y: auto;
    background: linear-gradient(135deg, #6b0f9c 0%, #b91372 40%, #b91372 100%);
  }

  .sidebar.open {
    left: 0;
  }

  .sidebar-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 40px;
  }

  .sidebar-logo {
    width: 50px;
    height: auto;
    filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
  }

  .close-sidebar {
    background: transparent;
    border: none;
    color: white;
    font-size: 40px;
    line-height: 1;
    cursor: pointer;
    padding: 0;
  }

  .sidebar-nav {
    display: flex;
    flex-direction: column;
    gap: 24px;
    margin-bottom: 40px;
  }

  .sidebar-nav a {
    color: rgba(255, 255, 255, 0.9);
    text-decoration: none;
    font-size: 1.2rem;
    font-weight: 500;
    padding: 8px 0;
    border-bottom: 1px solid rgba(239, 185, 120, 0.2);
    transition: 0.2s;
  }

  .sidebar-nav a:hover {
    color: #efb978;
    padding-left: 8px;
  }

  /* Sidebar buttons (formerly .header-buttons) */
  .sidebar-buttons {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  .sidebar-buttons .btn-header {
    width: 100%;
    text-align: center;
  }

  /* ===== MAIN CONTENT AREAS (unchanged, but consolidated) ===== */
  /* (your existing styles for .hero, .mission-vision, .services, .about, etc. remain exactly as you wrote them – omitted here for brevity) */
  /* ... */

  /* ===== FOOTER (restructured) ===== */
  .footer {
    background: linear-gradient(135deg, #6b0f9c 0%, #b91372 40%, #b91372 100%);
    width: 100%;
  }

  .footer-inner {
    max-width: 1440px;
    margin: 0 auto;
    padding: 56px 32px 32px;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 50px;
  }

  .footer-col p {
    color: #b0c4de;
    max-width: 280px;
    margin-top: 20px;
    font-size: 0.95rem;
  }

  .footer-logo {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 26px;
    font-weight: 700;
    color: white;
  }

  /* .logo-img inside footer – no negative right offset */
 .footer {
    /* max-width: 1440px;
    margin: 0 auto;
    padding: 56px 32px 32px; */
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 50px;
    background: #1E3A4A;
    background: linear-gradient(135deg, #6B0F9C 0%, #B91372  40%, #B91372 100%);
    width: 100%;
  background: linear-gradient(135deg, #6B0F9C 0%, #B91372 40%, #B91372 100%);
  }

  .footer-col p {
    color: #b0c4de;
    max-width: 280px;
    margin-top: 20px;
    font-size: 0.95rem;
  }

  .footer-logo {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 26px;
    font-weight: 700;
    color: white;
  }

  .logo-img {
    width: 70px;
    height: auto;
    filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
    position: relative;
    right: 25px;
  }

  .accent {
    color: #EFB978;
  }

  .footer-links {
    display: flex;
    gap: 80px;
    flex-wrap: wrap;
  }

  .footer-links div h5 {
    font-size: 1.1rem;
    margin-bottom: 22px;
    color: white;
    font-weight: 600;
  }

  .footer-links div a {
    display: block;
    color: #b0c4de;
    text-decoration: none;
    margin-bottom: 14px;
    font-size: 0.95rem;
    transition: color 0.2s;
  }

  .footer-links div a:hover {
    color: #EFB978;
  }
  .footer-inner {
  max-width: 1440px;
  margin: 0 auto;
  padding: 56px 32px 32px;

  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 50px;
}

  .copyright {
    max-width: 1440px;
    height: 5px;
    margin: 0 auto;
    text-align: center;
    padding: 24px 32px;
    color: #9bb5d4;
    font-size: 14px;
    /* border-top: 1px solid #254b6d; */
    background: linear-gradient(135deg, #6B0F9C 0%, #B91372  40%, #B91372 100%);
  }

  /* ===== BACK TO TOP ===== */
  .back-to-top {
    position: fixed;
    bottom: 30px;
    right: 30px;
    width: 70px;
    height: 70px;
    border: none;
    background: transparent;
    cursor: pointer;
    z-index: 999;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.3s ease;
  }

  .back-to-top img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    transition: all 0.3s ease;
  }

  .back-to-top:hover img {
    transform: translateY(-8px) scale(1.1);
  }

  .back-to-top:active img {
    transform: scale(0.9);
  }

  /* ===== ACCESSIBILITY & MISC ===== */
  /* Focus styles for interactive elements */
  a:focus-visible,
  button:focus-visible {
    outline: 2px solid #efb978;
    outline-offset: 2px;
  }

  /* Smooth scroll offset for anchor links */
  #services,
  #about,
  #mission,
  #contact {
    scroll-margin-top: 110px;
  }

  /* ===== RESPONSIVE TWEAKS ===== */
  @media (max-width: 768px) {
    :global(body) {
      padding-top: 80px;
    }
    .site-container {
      padding: 0 20px;
    }
    .footer-inner {
      flex-direction: column;
      gap: 30px;
    }
    .footer-links {
      gap: 40px;
    }
  }

  @media (max-width: 480px) {
    .back-to-top {
      bottom: 20px;
      right: 20px;
      width: 55px;
      height: 55px;
    }
  }

  /* Duplicate partner-grid definitions removed – keep only one */
  .partner-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
    gap: 18px;
    margin-top: 40px;
  }

  .partner-item {
    background: #ffffff;
    border-radius: 18px;
    padding: 14px 10px;
    text-align: center;
    border: 1px solid #efe2d4;
    transition: all 0.35s ease;
    cursor: pointer;
    position: relative;
    overflow: hidden;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.6s ease forwards;
  }

  /* ... rest of partner-item styles ... */
  @keyframes fadeUp {
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
</style>