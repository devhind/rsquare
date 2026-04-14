<script>
  import { goto } from '$app/navigation';

  let email = "";
  let emailError = "";
  let isSubmitted = false;
  let isLoading = false;
  let resendCountdown = 0;
  let resendInterval;

  function validateEmail() {
    const emailRegex = /^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/;
    if (!email.trim()) {
      emailError = "Email is required";
      return false;
    } else if (!emailRegex.test(email.trim())) {
      emailError = "Enter a valid email address (e.g., name@domain.com)";
      return false;
    }
    emailError = "";
    return true;
  }

  // Clear error on typing
  function handleEmailInput() {
    if (emailError) emailError = "";
  }

  async function sendResetLink() {
    if (!validateEmail()) return;

    isLoading = true;

    // Simulate API call – replace with your actual endpoint
    try {
      await new Promise(resolve => setTimeout(resolve, 1500));
      console.log("Password reset requested for:", email);
      isSubmitted = true;
    } catch (error) {
      emailError = "Something went wrong. Please try again.";
    } finally {
      isLoading = false;
    }
  }

  async function handleResend() {
    if (resendCountdown > 0) return;
    isLoading = true;
    try {
      await new Promise(resolve => setTimeout(resolve, 1000));
      console.log("Reset link resent to:", email);
      startResendCooldown();
    } catch (error) {
      emailError = "Failed to resend. Please try again.";
    } finally {
      isLoading = false;
    }
  }

  function startResendCooldown() {
    resendCountdown = 30;
    if (resendInterval) clearInterval(resendInterval);
    resendInterval = setInterval(() => {
      if (resendCountdown <= 1) {
        clearInterval(resendInterval);
        resendCountdown = 0;
      } else {
        resendCountdown--;
      }
    }, 1000);
  }

  function handleResetPassword(e) {
    e.preventDefault();
    sendResetLink();
  }

  // Cleanup interval on component destroy
  import { onDestroy } from 'svelte';
  onDestroy(() => {
    if (resendInterval) clearInterval(resendInterval);
  });
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
            on:input={handleEmailInput}
            aria-invalid={!!emailError}
            aria-describedby="email-error"
          />
          {#if emailError}
            <span id="email-error" class="error-text">{emailError}</span>
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
        <div class="success-actions">
          <button class="back-to-login" on:click={() => goto('/login')}>
            <i class="fas fa-sign-in-alt"></i> Back to Login
          </button>
          <button
            class="resend-link"
            on:click={handleResend}
            disabled={resendCountdown > 0 || isLoading}
          >
            {#if resendCountdown > 0}
              <i class="fas fa-clock"></i> Resend in {resendCountdown}s
            {:else}
              <i class="fas fa-redo-alt"></i> Resend Link
            {/if}
          </button>
        </div>
      </div>
    {/if}

    <p class="login-link">
      Remember your password? <a href="/job-seekers/otp">Sign in</a>
    </p>
  </div>
</div>

<style>
  .forgot-page {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(145deg, #eef2ff 0%, #e0e7ff 100%);
    padding: 2rem 1.5rem;
    width: 100%;
    min-height: 100vh;
  }

  .forgot-card {
    max-width: 440px;
    width: 100%;
    background: rgba(255, 255, 255, 0.96);
    backdrop-filter: blur(16px);
    border-radius: 2rem;
    padding: 2rem 2rem 2.2rem;
    box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 255, 255, 0.5);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    animation: fadeSlideUp 0.5s cubic-bezier(0.2, 0.9, 0.4, 1.1) forwards;
  }

  .forgot-card:hover {
    transform: translateY(-4px);
    box-shadow: 0 30px 50px -16px rgba(0, 0, 0, 0.3);
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
    box-shadow: 0 8px 20px rgba(99, 102, 241, 0.35);
    transition: transform 0.2s;
  }

  .forgot-card:hover .icon-circle {
    transform: scale(1.02);
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
    letter-spacing: -0.3px;
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
    z-index: 1;
  }

  input {
    width: 100%;
    padding: 0.9rem 1rem 0.9rem 2.8rem;
    border-radius: 1rem;
    border: 1.5px solid #e2e8f0;
    background: white;
    font-family: inherit;
    font-size: 0.95rem;
    transition: all 0.2s ease;
    outline: none;
  }

  input:focus {
    border-color: #6366f1;
    box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2);
  }

  input.error {
    border-color: #e11d48;
    background-color: #fff5f7;
  }

  input:disabled {
    background-color: #f8fafc;
    cursor: not-allowed;
    opacity: 0.7;
  }

  .error-text {
    font-size: 0.7rem;
    color: #e11d48;
    margin-top: 0.35rem;
    display: block;
    margin-left: 0.5rem;
    font-weight: 500;
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
    transition: all 0.3s ease;
    box-shadow: 0 5px 15px rgba(99, 102, 241, 0.3);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.6rem;
  }

  .reset-btn:hover:not(:disabled) {
    transform: translateY(-2px);
    background: linear-gradient(105deg, #4f46e5, #2563eb);
    box-shadow: 0 12px 22px -10px #6366f1;
  }

  .reset-btn:active:not(:disabled) {
    transform: translateY(1px);
  }

  .reset-btn:disabled {
    opacity: 0.7;
    cursor: not-allowed;
    transform: none;
  }

  .success-message {
    text-align: center;
    padding: 1rem 0;
  }

  .success-message i {
    font-size: 3.5rem;
    color: #10b981;
    margin-bottom: 1rem;
    animation: popIn 0.4s cubic-bezier(0.34, 1.2, 0.64, 1);
  }

  .success-message h3 {
    font-size: 1.5rem;
    color: #1e293b;
    margin-bottom: 0.5rem;
  }

  .success-message p {
    color: #475569;
    margin-bottom: 1.8rem;
    word-break: break-word;
  }

  .success-actions {
    display: flex;
    gap: 1rem;
    justify-content: center;
    flex-wrap: wrap;
  }

  .back-to-login, .resend-link {
    padding: 0.7rem 1.5rem;
    border-radius: 2rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    font-size: 0.85rem;
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
  }

  .back-to-login {
    background: linear-gradient(105deg, #6366f1, #3b82f6);
    border: none;
    color: white;
    box-shadow: 0 2px 8px rgba(99, 102, 241, 0.3);
  }

  .back-to-login:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 14px rgba(99, 102, 241, 0.4);
  }

  .resend-link {
    background: transparent;
    border: 1.5px solid #6366f1;
    color: #6366f1;
  }

  .resend-link:hover:not(:disabled) {
    background: rgba(99, 102, 241, 0.08);
    transform: translateY(-1px);
  }

  .resend-link:disabled {
    opacity: 0.5;
    cursor: not-allowed;
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
    transition: color 0.2s;
  }

  .login-link a:hover {
    text-decoration: underline;
    color: #4f46e5;
  }

  /* Animations */
  @keyframes fadeSlideUp {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  @keyframes popIn {
    0% {
      opacity: 0;
      transform: scale(0.5);
    }
    80% {
      transform: scale(1.1);
    }
    100% {
      opacity: 1;
      transform: scale(1);
    }
  }

  /* Responsive */
  @media (max-width: 550px) {
    .forgot-page {
      padding: 1rem;
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
    .success-actions {
      flex-direction: column;
    }
    .back-to-login, .resend-link {
      justify-content: center;
    }
  }

  @media (max-height: 600px) {
    .forgot-page {
      align-items: flex-start;
      padding-top: 3rem;
    }
  }
</style>