<script>
  import { goto } from '$app/navigation';

  let email = "";
  let emailError = "";
  let isSubmitted = false;
  let isLoading = false;

  function validateEmail() {
    const emailRegex = /^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/;
    if (!email.trim()) {
      emailError = "Email is required";
      return false;
    } else if (!emailRegex.test(email.trim())) {
      emailError = "Enter a valid email address";
      return false;
    }
    emailError = "";
    return true;
  }

  async function handleResetPassword() {
    if (!validateEmail()) return;

    isLoading = true;

    // Simulate API call (replace with actual reset endpoint)
    await new Promise(resolve => setTimeout(resolve, 1500));

    // Success – show message and optionally redirect
    isSubmitted = true;
    isLoading = false;

    // Log for debugging
    console.log("Password reset requested for:", email);

    // You could also redirect to login after a delay:
    // setTimeout(() => goto('/login'), 3000);
  }
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"
  />
</svelte:head>

<div class="forgot-page">
  <div class="forgot-card">
    <div class="header">
      <div class="icon-circle">
        <i class="fas fa-key"></i>
      </div>
      <h1>Forgot Password?</h1>
      <p>No worries, we'll send you reset instructions</p>
    </div>

    {#if !isSubmitted}
      <form on:submit|preventDefault={handleResetPassword}>
        <div class="input-group">
          <i class="fas fa-envelope input-icon"></i>
          <input
            type="email"
            placeholder="Email address"
            bind:value={email}
            class:error={emailError}
            disabled={isLoading}
          />
          {#if emailError}
            <span class="error-text">{emailError}</span>
          {/if}
        </div>

        <button type="submit" class="reset-btn" disabled={isLoading}>
          {#if isLoading}
            <i class="fas fa-spinner fa-pulse"></i> Sending...
          {:else}
            <span>Send Reset Link</span>
            <i class="fas fa-arrow-right"></i>
          {/if}
        </button>
      </form>
    {:else}
      <div class="success-message">
        <i class="fas fa-check-circle"></i>
        <h3>Check your email</h3>
        <p>We've sent a password reset link to <strong>{email}</strong></p>
        <button class="back-to-login" on:click={() => goto('/login')}>
          Back to Login
        </button>
      </div>
    {/if}

    <p class="login-link">
      Remember your password? <a href="/job-seekers/otp">Sign in</a>
    </p>
  </div>
</div>

<style>
  /* Scoped styles – matches login page perfectly */
  .forgot-page {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(145deg, #eef2ff 0%, #e0e7ff 100%);
    padding: 4rem 1.5rem;
    width: 100%;
    min-height: 0;
  }

  .forgot-card {
    max-width: 440px;
    width: 100%;
    background: rgba(255, 255, 255, 0.94);
    backdrop-filter: blur(16px);
    border-radius: 2rem;
    padding: 2rem 2rem 2.2rem;
    box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 255, 255, 0.5);
    transition: transform 0.2s ease;
  }

  .forgot-card:hover {
    transform: translateY(-5px);
  }

  .header {
    text-align: center;
    margin-bottom: 2rem;
  }

  .icon-circle {
    width: 70px;
    height: 70px;
    background: linear-gradient(135deg, #6366f1, #3b82f6);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto 1rem;
    box-shadow: 0 8px 20px rgba(99, 102, 241, 0.3);
  }

  .icon-circle i {
    font-size: 2rem;
    color: white;
  }

  h1 {
    font-size: 1.9rem;
    font-weight: 700;
    background: linear-gradient(135deg, #0f172a, #1e293b);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    margin-bottom: 0.3rem;
  }

  .header p {
    color: #475569;
    font-size: 0.9rem;
  }

  .input-group {
    margin-bottom: 1.2rem;
    position: relative;
  }

  .input-icon {
    position: absolute;
    left: 1rem;
    top: 50%;
    transform: translateY(-50%);
    color: #94a3b8;
    font-size: 1rem;
    pointer-events: none;
  }

  input {
    width: 100%;
    padding: 0.9rem 1rem 0.9rem 2.8rem;
    border-radius: 1rem;
    border: 1px solid #e2e8f0;
    background: white;
    font-family: inherit;
    font-size: 0.95rem;
    transition: all 0.2s;
    outline: none;
  }

  input:focus {
    border-color: #6366f1;
    box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2);
  }

  input.error {
    border-color: #e11d48;
  }

  .error-text {
    font-size: 0.7rem;
    color: #e11d48;
    margin-top: 0.3rem;
    display: block;
    margin-left: 0.5rem;
  }

  .reset-btn {
    width: 100%;
    padding: 1rem;
    background: linear-gradient(105deg, #6366f1, #3b82f6);
    border: none;
    border-radius: 1.5rem;
    font-weight: 600;
    font-size: 1rem;
    color: white;
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: 0 5px 15px rgba(99, 102, 241, 0.3);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.6rem;
  }

  .reset-btn:hover:not(:disabled) {
    transform: scale(1.02);
    background: linear-gradient(105deg, #4f46e5, #2563eb);
    box-shadow: 0 12px 22px -10px #6366f1;
  }

  .reset-btn:disabled {
    opacity: 0.7;
    cursor: not-allowed;
  }

  .success-message {
    text-align: center;
    padding: 1rem 0;
  }

  .success-message i {
    font-size: 3.5rem;
    color: #10b981;
    margin-bottom: 1rem;
  }

  .success-message h3 {
    font-size: 1.5rem;
    color: #1e293b;
    margin-bottom: 0.5rem;
  }

  .success-message p {
    color: #475569;
    margin-bottom: 1.5rem;
    word-break: break-word;
  }

  .back-to-login {
    background: transparent;
    border: 1px solid #6366f1;
    color: #6366f1;
    padding: 0.7rem 1.5rem;
    border-radius: 2rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
  }

  .back-to-login:hover {
    background: #6366f1;
    color: white;
  }

  .login-link {
    text-align: center;
    margin-top: 1.8rem;
    font-size: 0.85rem;
    color: #475569;
  }

  .login-link a {
    color: #6366f1;
    font-weight: 600;
    text-decoration: none;
  }

  .login-link a:hover {
    text-decoration: underline;
  }

  /* Responsive */
  @media (max-width: 550px) {
    .forgot-page {
      padding: 2rem 1rem;
    }
    .forgot-card {
      padding: 1.5rem;
    }
    h1 {
      font-size: 1.6rem;
    }
    .icon-circle {
      width: 55px;
      height: 55px;
    }
    .icon-circle i {
      font-size: 1.5rem;
    }
  }

  @media (max-height: 550px) {
    .forgot-page {
      align-items: flex-start;
      padding: 2rem 0;
    }
  }
</style>