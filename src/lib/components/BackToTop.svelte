<script>
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';

  let showBackToTop = false;

  function handleScroll() {
    if (browser) {
      showBackToTop = window.scrollY > 300;
    }
  }

   /* ================= SCROLL TO TOP ================= */
  function scrollToTop() {
    if (browser) {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  }
   // ✅ ADD THIS (YOU MISSED THIS)
  onMount(() => {
  if (!browser) return;

  handleScroll(); // ✅ ADD THIS LINE

  window.addEventListener('scroll', handleScroll);
});

  onDestroy(() => {
    if (!browser) return;
    window.removeEventListener('scroll', handleScroll);
  });
</script>

{#if showBackToTop}
  <!-- <button class="back-to-top" on:click={scrollToTop} aria-label="Back to top"> -->
   <button 
  class="back-to-top {showBackToTop ? 'show' : ''}" 
  on:click={scrollToTop}
>
  <img src="/images/up-arrow.png" alt="Go to top" />
</button>
{/if}

<style>
 /* ===== PREMIUM BACK TO TOP BUTTON ===== */
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

/* Image styling */
.back-to-top img {
  width: 100%;
  height: 100%;
  object-fit: contain;

  transition: all 0.3s ease;
}

/* Hover animation */
.back-to-top:hover img {
  transform: translateY(-8px) scale(1.1);
}

/* Click effect */
.back-to-top:active img {
  transform: scale(0.9);
}
  /* ===== RESPONSIVE (additional) ===== */
  @media (max-width: 1024px) {
    .slide-content { max-width: 90%; }
  }

  @media (max-width: 820px) {
    .navbar {
      flex-direction: row; /* keep row on mobile */
      justify-content: space-between;
    }
  }

  @media (max-width: 768px) {
    :global(body) {
      padding-top: 80px;
    }
    .site-container {
      padding: 0 20px;
    }
    .hero {
      flex-direction: column;
    }
    .stats-row {
      flex-direction: column;
      align-items: center;
      text-align: center;
    }
    .carousel-control {
      width: 42px;
      height: 42px;
      font-size: 24px;
    }
    .prev { left: 12px; }
    .next { right: 12px; }
    .carousel-slide {
      padding: 40px 24px;
      min-height: 350px;
    }
    .footer-links {
      gap: 40px;
    }
    .back-to-top {
      bottom: 20px;
      right: 20px;
      width: 55px;
      height: 55px;
      font-size: 26px;
    }
  }

  @media (max-width: 480px) {
    .hero-content h1 { font-size: 38px; }
    .btn-large { width: 100%; }
    .service-card { padding: 24px 18px; }
    .partner-grid { padding: 30px 16px; }
    .contact-container { padding: 32px 20px; }
    .footer { flex-direction: column; }
  }

  img {
    max-width: 100%;
    height: auto;
  }
  .back-to-top {
  opacity: 0;
  transform: translateY(20px);
  pointer-events: none;
}

.back-to-top.show {
  opacity: 1;
  transform: translateY(0);
  pointer-events: auto;
}
</style>