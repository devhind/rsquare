<script>
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';

  let showBackToTop = false;

  function handleScroll() {
    if (browser) {
      showBackToTop = window.scrollY > 300;
    }
  }

  function scrollToTop() {
    if (browser) {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  }

  onMount(() => {
    if (!browser) return;
    handleScroll();
    window.addEventListener('scroll', handleScroll);
  });

  onDestroy(() => {
    if (!browser) return;
    window.removeEventListener('scroll', handleScroll);
  });
</script>

{#if showBackToTop}
  <button 
    class="back-to-top {showBackToTop ? 'show' : ''}" 
    on:click={scrollToTop}
    aria-label="Back to top"
  >
    <span class="custom-arrow"></span>
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
    background: linear-gradient(135deg, #1E63D6, #0F4FCB);
    border-radius: 50%;
    cursor: pointer;
    z-index: 999;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
    opacity: 0;
    transform: translateY(20px);
    pointer-events: none;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }

  .back-to-top.show {
    opacity: 1;
    transform: translateY(0);
    pointer-events: auto;
  }

  /* Custom broad arrow */
  .custom-arrow {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 3px;
  }

  /* Arrowhead: a wide triangle */
  .custom-arrow::before {
    content: '';
    width: 0;
    height: 0;
    border-left: 12px solid transparent;
    border-right: 12px solid transparent;
    border-bottom: 18px solid #f0f0f0;   /* off‑white, slightly darker than pure white */
  }

  /* Stem: a broad rectangle */
  .custom-arrow::after {
    content: '';
    width: 16px;
    height: 12px;
    background-color: #f0f0f0;
    border-radius: 2px;
  }

  /* Hover effects */
  .back-to-top:hover {
    transform: scale(1.05);
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.25);
  }

  .back-to-top:hover .custom-arrow::before {
    transform: translateY(-2px);
  }

  .back-to-top:hover .custom-arrow::after {
    transform: translateY(-2px);
  }

  .back-to-top:active {
    transform: scale(0.98);
  }

  /* Responsive adjustments */
  @media (max-width: 768px) {
    .back-to-top {
      bottom: 20px;
      right: 20px;
      width: 55px;
      height: 55px;
    }
    .custom-arrow::before {
      border-left: 10px solid transparent;
      border-right: 10px solid transparent;
      border-bottom: 15px solid #f0f0f0;
    }
    .custom-arrow::after {
      width: 13px;
      height: 10px;
    }
  }

  @media (max-width: 480px) {
    .back-to-top {
      width: 48px;
      height: 48px;
    }
    .custom-arrow::before {
      border-left: 8px solid transparent;
      border-right: 8px solid transparent;
      border-bottom: 12px solid #f0f0f0;
    }
    .custom-arrow::after {
      width: 11px;
      height: 8px;
    }
  }
</style>