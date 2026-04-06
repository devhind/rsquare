<script>
  import { goto } from '$app/navigation';

  let email = "";
  let password = "";
  let showPassword = false;
  let emailError = "";
  let passwordError = "";

  function validateForm() {
    emailError = "";
    passwordError = "";
    let isValid = true;

    const emailRegex = /^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/;
    if (!email.trim()) {
      emailError = "Email is required";
      isValid = false;
    } else if (!emailRegex.test(email.trim())) {
      emailError = "Enter a valid email address";
      isValid = false;
    }

    if (!password) {
      passwordError = "Password is required";
      isValid = false;
    } else if (password.length < 6) {
      passwordError = "Password must be at least 6 characters";
      isValid = false;
    }

    return isValid;
  }

  function handleLogin() {
    if (validateForm()) {
      alert("✅ Login Successful 🚀");
      console.log({ email, password });
      goto('/');
    }
  }
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"
  />
</svelte:head>

<div class="login-page">
  <div class="login-card">
    <div class="header">
      <div class="icon-circle">
        <i class="fas fa-user-astronaut"></i>
      </div>
      <h1>Welcome Back</h1>
      <p>Login to continue your journey</p>
    </div>

    <form on:submit|preventDefault={handleLogin}>
      <div class="input-group">
        <i class="fas fa-envelope input-icon"></i>
        <input
          type="email"
          placeholder="Email address"
          bind:value={email}
          class:error={emailError}
        />
        {#if emailError}
          <span class="error-text">{emailError}</span>
        {/if}
      </div>

      <div class="input-group">
        <i class="fas fa-lock input-icon"></i>
        <input
          type={showPassword ? "text" : "password"}
          placeholder="Password"
          bind:value={password}
          class:error={passwordError}
        />
        <button type="button" class="password-toggle" on:click={() => showPassword = !showPassword}>
          <i class={showPassword ? "fas fa-eye-slash" : "fas fa-eye"}></i>
        </button>
        {#if passwordError}
          <span class="error-text">{passwordError}</span>
        {/if}
      </div>

      <div class="options">
        <label class="checkbox-label">
          <input type="checkbox" />
          <span>Remember me</span>
        </label>
        <a href="#" class="forgot-link">Forgot password?</a>
      </div>

      <button type="submit" class="login-btn">
        <span>Login</span>
        <i class="fas fa-arrow-right"></i>
      </button>
    </form>

    <p class="signup">
      Don't have an account? <a href="/job-seekers/registration">Sign up</a>
    </p>
  </div>
</div>

<style>
  /* Scoped styles – no global leakage */
  .login-page {
    flex: 1;                     /* fills remaining space between header & footer */
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(145deg, #eef2ff 0%, #e0e7ff 100%);
    padding: 2rem 1.5rem;
    width: 100%;
    min-height: 0;
                   /* allows flex child to shrink */
  }

  .login-card {
    max-width: 440px;
    width: 100%;
    background: rgba(255, 255, 255, 0.94);
    backdrop-filter: blur(16px);
    border-radius: 2rem;
    padding: 2rem 2rem 2.2rem;
    box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 255, 255, 0.5);
    transition: transform 0.2s ease;
    margin-top: 80px;
  }

  .login-card:hover {
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

  .password-toggle {
    position: absolute;
    right: 1rem;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    color: #94a3b8;
    cursor: pointer;
    font-size: 1rem;
    padding: 0;
    display: flex;
    align-items: center;
  }

  .password-toggle:hover {
    color: #6366f1;
  }

  .error-text {
    font-size: 0.7rem;
    color: #e11d48;
    margin-top: 0.3rem;
    display: block;
    margin-left: 0.5rem;
  }

  .options {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1.8rem;
    font-size: 0.85rem;
  }

  .checkbox-label {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    cursor: pointer;
    color: #334155;
  }

  .checkbox-label input {
    width: 1rem;
    height: 1rem;
    cursor: pointer;
    accent-color: #6366f1;
  }

  .forgot-link {
    color: #6366f1;
    text-decoration: none;
    font-weight: 500;
  }

  .forgot-link:hover {
    text-decoration: underline;
  }

  .login-btn {
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

  .login-btn:hover {
    transform: scale(1.02);
    background: linear-gradient(105deg, #4f46e5, #2563eb);
    box-shadow: 0 12px 22px -10px #6366f1;
  }

  .signup {
    text-align: center;
    margin-top: 1.8rem;
    font-size: 0.85rem;
    color: #475569;
  }

  .signup a {
    color: #6366f1;
    font-weight: 600;
    text-decoration: none;
  }

  .signup a:hover {
    text-decoration: underline;
  }

  /* Responsive */
  @media (max-width: 550px) {
    .login-page {
      padding: 1rem;
    }
    .login-card {
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
    .options {
      flex-direction: column;
      gap: 0.8rem;
      align-items: flex-start;
    }
  }

  /* Very short screens – allow scrolling */
  @media (max-height: 550px) {
    .login-page {
      align-items: flex-start;
      padding: 1rem 0;
    }
  }
</style>