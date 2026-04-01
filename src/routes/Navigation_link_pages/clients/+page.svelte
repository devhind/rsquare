<script>
  import { fly } from 'svelte/transition';
</script>

<main class="coming-page">
  <div class="container" in:fly={{ y: 20, duration: 500 }}>
    <div class="icon">🚧</div>
    <h1>Our Clients</h1>
    <p class="subtitle">
      We're preparing something amazing for you.
    </p>
    <p class="desc">
      This section will showcase our valued clients, partnerships, and success stories.<br />
      Stay tuned — it's coming very soon!
    </p>
    <a href="/" class="btn">Back to Home</a>
  </div>
</main>

<style>
  /* Compensate for fixed header – adjust to your header height (95px) */
 .coming-page {
  position: relative;
  padding-top: 95px;
  min-height: calc(100vh - 95px);

  display: flex;
  align-items: center;
  justify-content: center;

  font-family: 'Inter', sans-serif;
  text-align: center;

  padding: 95px 20px 20px 20px;
  margin: 0;
  box-sizing: border-box;

  /* ✅ PERFECT IMAGE SETTINGS */
  background: url('/images/under.png') no-repeat center center;
  background-size: cover;
}

  /* Subtle overlay to improve text readability */
  .coming-page::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.4); /* light dark overlay – adjust to taste */
    pointer-events: none;
  }

  /* Card stays white, positioned above overlay */
  .container {
    position: relative;
    z-index: 2;
    max-width: 600px;
    background: #ffffff;
    border-radius: 32px;
    padding: 48px 32px;
    box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.05), 0 0 0 1px rgba(0, 0, 0, 0.02);
    transition: all 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
    border: 1px solid transparent;
  }

  .container:hover {
    transform: translateY(-6px) scale(1.02);
    box-shadow: 0 30px 45px -15px rgba(44, 110, 158, 0.15), 0 0 0 1px rgba(44, 110, 158, 0.1);
    border-color: rgba(44, 110, 158, 0.2);
  }

  .icon {
    font-size: 3.5rem;
    margin-bottom: 24px;
    display: inline-block;
    animation: gentleBounce 2s infinite ease;
  }

  @keyframes gentleBounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-6px); }
  }

  h1 {
    font-size: 2.5rem;
    font-weight: 700;
    color: #1a2c3e;
    margin-bottom: 16px;
    letter-spacing: -0.5px;
  }

  .subtitle {
    font-size: 1.2rem;
    color: #2c6e9e;
    margin-bottom: 12px;
    font-weight: 500;
  }

  .desc {
    font-size: 1rem;
    color: #5a6e7e;
    margin-bottom: 32px;
    line-height: 1.6;
  }

  .btn {
    display: inline-block;
    padding: 12px 28px;
    border-radius: 40px;
    background: linear-gradient(135deg, #2c6e9e, #d97a4a);
    color: white;
    text-decoration: none;
    font-weight: 600;
    transition: all 0.3s ease;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  }

  .btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 18px rgba(0, 0, 0, 0.1);
  }

  /* Responsive */
  @media (max-width: 640px) {
    .coming-page {
      padding: 80px 16px 20px 16px;
    }
    .container {
      padding: 32px 24px;
    }
    h1 {
      font-size: 2rem;
    }
    .subtitle {
      font-size: 1.1rem;
    }
    .icon {
      font-size: 2.8rem;
    }
  }
</style>