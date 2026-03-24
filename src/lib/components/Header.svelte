<script>
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let isMenuOpen = false;
  let isMobile = false;

  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
    document.body.style.overflow = isMenuOpen ? 'hidden' : 'auto';
  }

  function closeMenu() {
    isMenuOpen = false;
    document.body.style.overflow = 'auto';
  }

  onMount(() => {
    const mediaQuery = window.matchMedia('(max-width: 820px)');

    function handleMediaChange(e) {
      isMobile = e.matches;
      if (!isMobile) isMenuOpen = false;
    }

    mediaQuery.addEventListener('change', handleMediaChange);
    isMobile = mediaQuery.matches;
  });

  function goToRegistration() {
    goto('/Employer-Registration');
  }

  function goToPostJob() {
    goto('/employer/post-job');
  }
</script>

<!-- HEADER -->
<header class="header-fixed">
  <div class="header-container">
    <div class="navbar">

      <!-- LOGO -->
      <div class="logo-container">
        <img class="logo-img" src="/images/LOGO_PDF_invertedColor_page-0001.jpg" alt="R Square" />
        <span class="logo-text">
          R SQUARE <span class="logo-hr">HR Services</span>
        </span>
      </div>

      <!-- DESKTOP NAV -->
      <nav class="nav-links desktop-only">
        <a href="/">Home</a>
        <a href="/#services">Services</a>
        <a href="/#about">About</a>
        <a href="/#mission">Mission & Vision</a>
        <a href="/#contact">Contact</a>
      </nav>

      <!-- DESKTOP BUTTONS -->
      <div class="header-buttons desktop-only">
        <button class="btn-header btn-register" on:click={goToRegistration}>
          Registration
        </button>

        <button class="btn-header btn-post" on:click={goToPostJob}>
          Post Job
        </button>
      </div>

      <!-- HAMBURGER -->
      <button class="hamburger mobile-only" on:click={toggleMenu} aria-label="Menu">
        <span class="bar"></span>
        <span class="bar"></span>
        <span class="bar"></span>
      </button>

    </div>
  </div>
</header>

<!-- SIDEBAR (always present, controlled by CSS) -->
<div class="sidebar-overlay" class:open={isMenuOpen} on:click={closeMenu}></div>

<div class="sidebar" class:open={isMenuOpen}>

  <!-- CLOSE BUTTON -->
  <div class="sidebar-header">
    <img src="/images/LOGO_PDF_invertedColor_page-0001.jpg" class="sidebar-logo" />
    <button class="close-sidebar" on:click={closeMenu}>×</button>
  </div>

  <!-- NAV LINKS -->
  <div class="sidebar-nav">
    <a href="/" on:click={closeMenu}>Home</a>
    <a href="/#services" on:click={closeMenu}>Services</a>
    <a href="/#about" on:click={closeMenu}>About</a>
    <a href="/#mission" on:click={closeMenu}>Mission</a>
    <a href="/#contact" on:click={closeMenu}>Contact</a>
  </div>

  <!-- BUTTONS -->
  <div class="sidebar-buttons">
    <button class="btn-header btn-register"
      on:click={() => { goToRegistration(); closeMenu(); }}>
      Registration
    </button>

    <button class="btn-header btn-post"
      on:click={() => { goToPostJob(); closeMenu(); }}>
      Post Job
    </button>
  </div>

</div>

<style>
  @import '$lib/styles/header.css';

</style>