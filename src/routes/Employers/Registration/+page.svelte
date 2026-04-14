<!-- src/routes/+page.svelte -->
<script>
  import { onMount } from 'svelte';
  import { fly } from 'svelte/transition';

  // --- Form Fields ---
  let mobile = '';
  let email = '';
  let userId = '';
  let password = '';
  let confirmPassword = '';
  let otp = '';
  let generatedOtp = '';
  
  // --- UI State ---
  let isLoading = false;
  let isOtpSent = false;
  let resendCooldown = 0;
  let countdownInterval = null;
  let showPassword = false;
  let showConfirmPassword = false;
  let otpInputRef = null;

  // --- Validation Errors ---
  let mobileError = '';
  let emailError = '';
  let userIdError = '';
  let passwordError = '';
  let confirmPasswordError = '';
  let otpError = '';

  // --- Validation Functions ---
  function validateMobile() {
    const mobileRegex = /^[0-9]{10}$/;
    if (!mobile) {
      mobileError = 'Mobile number is required';
      return false;
    } else if (!mobileRegex.test(mobile)) {
      mobileError = 'Please enter a valid 10-digit mobile number';
      return false;
    } else {
      mobileError = '';
      return true;
    }
  }

  function validateEmail() {
    const emailRegex = /^[^\s@]+@([^\s@]+\.)+[^\s@]+$/;
    if (!email) {
      emailError = 'Email address is required';
      return false;
    } else if (!emailRegex.test(email)) {
      emailError = 'Please enter a valid email address';
      return false;
    } else {
      emailError = '';
      return true;
    }
  }

  function validateUserId() {
    if (!userId) {
      userIdError = 'User ID is required';
      return false;
    } else if (userId.length < 4) {
      userIdError = 'User ID must be at least 4 characters';
      return false;
    } else if (!/^[a-zA-Z0-9_]+$/.test(userId)) {
      userIdError = 'Use only letters, numbers, and underscores';
      return false;
    } else {
      userIdError = '';
      return true;
    }
  }

  function validatePassword() {
    if (!password) {
      passwordError = 'Password is required';
      return false;
    } else if (password.length < 8) {
      passwordError = 'Password must be at least 8 characters';
      return false;
    } else if (!/(?=.*[A-Z])(?=.*[a-z])(?=.*\d)/.test(password)) {
      passwordError = 'Include uppercase, lowercase, and number';
      return false;
    } else {
      passwordError = '';
      return true;
    }
  }

  function validateConfirmPassword() {
    if (password !== confirmPassword) {
      confirmPasswordError = 'Passwords do not match';
      return false;
    } else {
      confirmPasswordError = '';
      return true;
    }
  }

  function validateOtp() {
    if (!otp) {
      otpError = 'OTP is required';
      return false;
    } else if (otp.length !== 6) {
      otpError = 'OTP must be 6 digits';
      return false;
    } else if (!/^[0-9]{6}$/.test(otp)) {
      otpError = 'OTP must contain only numbers';
      return false;
    } else {
      otpError = '';
      return true;
    }
  }

  function validateAllFields() {
    const isMobileValid = validateMobile();
    const isEmailValid = validateEmail();
    const isUserIdValid = validateUserId();
    const isPasswordValid = validatePassword();
    const isConfirmPasswordValid = validateConfirmPassword();
    const isOtpValid = validateOtp();
    
    return isMobileValid && isEmailValid && isUserIdValid && 
           isPasswordValid && isConfirmPasswordValid && isOtpValid;
  }

  // --- OTP Timer ---
  function startResendCooldown() {
    resendCooldown = 60;
    if (countdownInterval) clearInterval(countdownInterval);
    countdownInterval = setInterval(() => {
      if (resendCooldown > 0) {
        resendCooldown--;
      } else {
        clearInterval(countdownInterval);
        countdownInterval = null;
      }
    }, 1000);
  }

  // --- Generate & Send OTP ---
  async function generateAndSendOtp() {
    const isMobileValid = validateMobile();
    const isEmailValid = validateEmail();
    
    if (!isMobileValid || !isEmailValid) return;

    isLoading = true;
    await new Promise(resolve => setTimeout(resolve, 800));
    
    generatedOtp = Math.floor(100000 + Math.random() * 900000).toString();
    console.log('OTP Sent:', generatedOtp);
    
    showToast('OTP sent successfully!', 'success');
    isOtpSent = true;
    startResendCooldown();
    isLoading = false;
    
    // Auto-focus OTP input after sending
    setTimeout(() => {
      const otpInput = document.querySelector('.otp-input');
      if (otpInput) otpInput.focus();
    }, 100);
  }

  function resendOtp() {
    if (resendCooldown > 0) return;
    
    generatedOtp = Math.floor(100000 + Math.random() * 900000).toString();
    console.log('New OTP:', generatedOtp);
    showToast('New OTP sent!', 'success');
    startResendCooldown();
    
    // Clear previous OTP
    otp = '';
    otpError = '';
    
    // Focus OTP input
    setTimeout(() => {
      const otpInput = document.querySelector('.otp-input');
      if (otpInput) otpInput.focus();
    }, 100);
  }

  // Auto-submit when OTP reaches 6 digits
  function handleOtpInput() {
    validateOtp();
    if (otp.length === 6 && !otpError && isOtpSent) {
      // Optional: Auto-submit after OTP is fully entered
      // handleSubmit();
    }
  }

  // --- Submit Registration ---
  async function handleSubmit() {
    if (!validateAllFields()) {
      showToast('Please fix the errors above', 'error');
      return;
    }
    
    if (otp !== generatedOtp) {
      otpError = 'Invalid OTP. Please try again.';
      showToast('Invalid OTP', 'error');
      return;
    }
    
    isLoading = true;
    
    // Simulate API call to save to database
    await new Promise(resolve => setTimeout(resolve, 1500));
    
    const payload = {
      mobile,
      email,
      userId,
      password, // ⚠️ Hash in production!
      otp
    };
    console.log('Submitting registration to database:', payload);
    
    isLoading = false;
    showToast('Registration successful!', 'success');
    
    // Reset form
    setTimeout(() => {
      showToast('Redirecting to dashboard...', 'success');
    }, 1000);
  }

  function showToast(message, type) {
    const toast = document.createElement('div');
    toast.className = `toast toast-${type}`;
    toast.textContent = message;
    document.body.appendChild(toast);
    setTimeout(() => toast.classList.add('show'), 10);
    setTimeout(() => {
      toast.classList.remove('show');
      setTimeout(() => toast.remove(), 300);
    }, 3000);
  }

  function handleKeyPress(event, action) {
    if (event.key === 'Enter') action();
  }

  onMount(() => {
    return () => {
      if (countdownInterval) clearInterval(countdownInterval);
    };
  });
</script>

<div class="container">
  <div class="background-gradient"></div>
  
  <div class="card" in:fly={{ y: 20, duration: 500 }}>
    <div class="form-section">
      <!-- Header -->
      <div class="header">
        <div class="logo-badge">
          <svg width="28" height="28" viewBox="0 0 32 32" fill="none">
            <path d="M16 2L4 9L16 16L28 9L16 2Z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
            <path d="M4 16L16 23L28 16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
            <path d="M4 23L16 30L28 23" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
          </svg>
          <span>HRV Portal</span>
        </div>
        <h1>Create Account</h1>
        <p class="subtitle">Secure access management system</p>
      </div>

      <!-- Form Fields -->
      <div class="form-grid">
        <!-- Mobile Number -->
        <div class="input-group">
          <label>Mobile Number</label>
          <div class="input-wrapper {mobileError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72c.127.96.362 1.903.7 2.81a2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45c.907.338 1.85.573 2.81.7A2 2 0 0 1 22 16.92z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
              </svg>
            </span>
            <input 
              type="tel" 
              placeholder="9876543210" 
              bind:value={mobile} 
              maxlength="10"
              on:input={validateMobile}
              on:keypress={(e) => handleKeyPress(e, generateAndSendOtp)}
            />
          </div>
          {#if mobileError}<span class="error-message">{mobileError}</span>{/if}
        </div>

        <!-- Email Address -->
        <div class="input-group">
          <label>Email Address</label>
          <div class="input-wrapper {emailError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
                <path d="M22 6L12 13L2 6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
              </svg>
            </span>
            <input 
              type="email" 
              placeholder="you@example.com" 
              bind:value={email}
              on:input={validateEmail}
            />
          </div>
          {#if emailError}<span class="error-message">{emailError}</span>{/if}
        </div>

        <!-- User ID -->
        <div class="input-group">
          <label>User ID</label>
          <div class="input-wrapper {userIdError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                <circle cx="12" cy="7" r="4" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input 
              type="text" 
              placeholder="e.g., john_doe" 
              bind:value={userId} 
              on:input={validateUserId}
            />
          </div>
          {#if userIdError}<span class="error-message">{userIdError}</span>{/if}
        </div>

        <!-- Password -->
        <div class="input-group">
          <label>Password</label>
          <div class="input-wrapper {passwordError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <rect x="5" y="11" width="14" height="11" rx="2" stroke="currentColor" stroke-width="1.5"/>
                <path d="M12 16v2M8 11V8a4 4 0 0 1 8 0v3" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
              </svg>
            </span>
            <input 
              type={showPassword ? "text" : "password"} 
              placeholder="Min. 8 chars, 1 uppercase, 1 number" 
              bind:value={password} 
              on:input={validatePassword}
            />
            <button 
              type="button" 
              class="password-toggle"
              on:click={() => showPassword = !showPassword}
            >
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z" stroke="currentColor" stroke-width="1.5"/>
                <circle cx="12" cy="12" r="3" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </button>
          </div>
          {#if passwordError}<span class="error-message">{passwordError}</span>{/if}
        </div>

        <!-- Confirm Password -->
        <div class="input-group">
          <label>Confirm Password</label>
          <div class="input-wrapper {confirmPasswordError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M12 16v2M8 11V8a4 4 0 0 1 8 0v3" stroke="currentColor" stroke-width="1.5"/>
                <rect x="5" y="11" width="14" height="11" rx="2" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input 
              type={showConfirmPassword ? "text" : "password"} 
              placeholder="Re-enter password" 
              bind:value={confirmPassword} 
              on:input={validateConfirmPassword}
            />
            <button 
              type="button" 
              class="password-toggle"
              on:click={() => showConfirmPassword = !showConfirmPassword}
            >
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z" stroke="currentColor" stroke-width="1.5"/>
                <circle cx="12" cy="12" r="3" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </button>
          </div>
          {#if confirmPasswordError}<span class="error-message">{confirmPasswordError}</span>{/if}
        </div>

        <!-- OTP Section - Enhanced UX -->
        <div class="input-group">
          <label>Verification Code</label>
          <div class="otp-container">
            <div class="otp-wrapper">
              <input 
                type="text" 
                placeholder="••••••" 
                bind:value={otp}
                maxlength="6"
                class="otp-input {otpError ? 'error' : ''} {isOtpSent ? 'active' : ''}"
                on:input={handleOtpInput}
                on:keypress={(e) => handleKeyPress(e, handleSubmit)}
                disabled={!isOtpSent}
                autocomplete="off"
              />
              <button 
                class="generate-otp-btn" 
                on:click={isOtpSent ? resendOtp : generateAndSendOtp}
                disabled={isLoading || (isOtpSent && resendCooldown > 0)}
              >
                {#if isLoading && !isOtpSent}
                  <span class="spinner-small"></span>
                  Sending...
                {:else if isOtpSent && resendCooldown > 0}
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <circle cx="12" cy="12" r="10"/>
                    <polyline points="12 6 12 12 16 14"/>
                  </svg>
                  {resendCooldown}s
                {:else if isOtpSent}
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
                    <circle cx="12" cy="12" r="3"/>
                  </svg>
                  Resend
                {:else}
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z"/>
                  </svg>
                  Send OTP
                {/if}
              </button>
            </div>
            
            <!-- OTP Digits Indicator -->
            {#if isOtpSent}
              <div class="otp-digits">
                {#each [0,1,2,3,4,5] as i}
                  <span class="otp-digit {otp[i] ? 'filled' : ''}">
                    {otp[i] || '•'}
                  </span>
                {/each}
              </div>
            {/if}
            
            {#if otpError}
              <span class="error-message">{otpError}</span>
            {:else if isOtpSent}
              <span class="hint-text success">
                <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M20 6L9 17L4 12"/>
                </svg>
                OTP sent to {mobile}
              </span>
            {:else}
              <span class="hint-text">
                <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <circle cx="12" cy="12" r="10"/>
                  <line x1="12" y1="8" x2="12" y2="12"/>
                  <line x1="12" y1="16" x2="12.01" y2="16"/>
                </svg>
                Click "Send OTP" to receive 6-digit code
              </span>
            {/if}
          </div>
        </div>
      </div>

      <!-- Submit Button -->
      <button 
        class="btn-primary" 
        on:click={handleSubmit}
        disabled={isLoading || !isOtpSent}
        class:loading={isLoading}
      >
        {#if isLoading}
          <span class="spinner"></span>
        {/if}
        {isLoading ? 'Creating Account...' : 'Create Account →'}
      </button>

      <!-- Terms -->
      <p class="terms">
        By creating an account, you agree to our 
        <a href="#">Terms of Service</a> and 
        <a href="#">Privacy Policy</a>
      </p>
    </div>
  </div>
</div>

<style>
  /* Container & Background - Color preserved */
  .container {
    position: relative;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    background: #FAF7F2;
  }

  .background-gradient {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: #FAF7F2;
    z-index: -2;
  }

  /* Card */
  .card {
    width: 100%;
    max-width: 580px;
    background: white;
    border-radius: 32px;
    padding: 40px;
    box-shadow: 0 20px 40px -12px rgba(0, 0, 0, 0.1);
    transition: transform 0.2s ease;
  }

  /* Header */
  .header {
    text-align: center;
    margin-bottom: 32px;
  }

  .logo-badge {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: #f0f4fe;
    padding: 8px 16px;
    border-radius: 100px;
    margin-bottom: 20px;
    color: #2563eb;
    font-weight: 600;
    font-size: 14px;
  }

  h1 {
    font-size: 28px;
    font-weight: 700;
    color: #0f172a;
    margin: 0 0 8px 0;
  }

  .subtitle {
    color: #64748b;
    font-size: 14px;
    margin: 0;
  }

  /* Form Grid */
  .form-grid {
    display: flex;
    flex-direction: column;
    gap: 20px;
    margin-bottom: 28px;
  }

  /* Input Groups */
  .input-group {
    width: 100%;
  }

  .input-group label {
    display: block;
    font-size: 13px;
    font-weight: 600;
    color: #334155;
    margin-bottom: 6px;
  }

  .input-wrapper {
    position: relative;
    display: flex;
    align-items: center;
  }

  .input-icon {
    position: absolute;
    left: 14px;
    display: flex;
    align-items: center;
    color: #94a3b8;
    pointer-events: none;
  }

  .input-wrapper input {
    width: 100%;
    padding: 12px 12px 12px 42px;
    border: 1.5px solid #e2e8f0;
    border-radius: 14px;
    font-size: 15px;
    transition: all 0.2s ease;
    background: white;
    font-family: inherit;
  }

  .input-wrapper input:focus {
    outline: none;
    border-color: #2563eb;
    box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
  }

  .input-wrapper.error input {
    border-color: #f97316;
  }

  .password-toggle {
    position: absolute;
    right: 12px;
    background: none;
    border: none;
    cursor: pointer;
    color: #94a3b8;
    padding: 4px;
    display: flex;
    align-items: center;
    transition: color 0.2s;
  }

  .password-toggle:hover {
    color: #2563eb;
  }

  /* OTP Section - Enhanced */
  .otp-container {
    width: 100%;
  }

  .otp-wrapper {
    display: flex;
    gap: 12px;
    align-items: center;
    margin-bottom: 12px;
  }

  .otp-input {
    flex: 2;
    padding: 12px;
    border: 1.5px solid #e2e8f0;
    border-radius: 14px;
    font-size: 20px;
    text-align: center;
    letter-spacing: 4px;
    font-weight: 600;
    font-family: 'Courier New', monospace;
    transition: all 0.2s;
    background: white;
  }

  .otp-input:focus {
    outline: none;
    border-color: #2563eb;
    box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
  }

  .otp-input.active {
    background: #fefce8;
    border-color: #eab308;
  }

  .otp-input.error {
    border-color: #f97316;
  }

  .otp-input:disabled {
    background: #f8fafc;
    color: #94a3b8;
    cursor: not-allowed;
  }

  .generate-otp-btn {
    background: linear-gradient(135deg, #f1f5f9, #e2e8f0);
    border: none;
    padding: 0 20px;
    height: 48px;
    border-radius: 14px;
    font-weight: 600;
    font-size: 14px;
    color: #1e40af;
    cursor: pointer;
    transition: all 0.2s;
    white-space: nowrap;
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .generate-otp-btn:hover:not(:disabled) {
    background: linear-gradient(135deg, #e2e8f0, #cbd5e1);
    transform: translateY(-1px);
  }

  .generate-otp-btn:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  /* OTP Digits Visual Indicator */
  .otp-digits {
    display: flex;
    gap: 8px;
    justify-content: center;
    margin-top: 8px;
    margin-bottom: 8px;
  }

  .otp-digit {
    width: 36px;
    height: 36px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #f8fafc;
    border-radius: 10px;
    font-size: 18px;
    font-weight: 600;
    font-family: monospace;
    color: #94a3b8;
    transition: all 0.2s;
  }

  .otp-digit.filled {
    background: #eef2ff;
    color: #2563eb;
    transform: scale(1.05);
  }

  /* Error & Hint Messages */
  .error-message {
    display: block;
    font-size: 11px;
    color: #f97316;
    margin-top: 6px;
  }

  .hint-text {
    display: flex;
    align-items: center;
    gap: 4px;
    font-size: 11px;
    color: #64748b;
    margin-top: 8px;
  }

  .hint-text.success {
    color: #10b981;
  }

  .hint-text svg {
    flex-shrink: 0;
  }

  /* Primary Button */
  .btn-primary {
    width: 100%;
    padding: 14px;
    background: linear-gradient(135deg, #0066ff, #0044cc);
    color: white;
    border: none;
    border-radius: 14px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 8px;
  }

  .btn-primary:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 10px 25px -5px rgba(0, 102, 255, 0.3);
  }

  .btn-primary:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  /* Terms */
  .terms {
    text-align: center;
    font-size: 12px;
    color: #64748b;
    margin-top: 24px;
  }

  .terms a {
    color: #2563eb;
    text-decoration: none;
  }

  .terms a:hover {
    text-decoration: underline;
  }

  /* Spinners */
  .spinner {
    width: 18px;
    height: 18px;
    border: 2px solid rgba(255, 255, 255, 0.3);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.6s linear infinite;
  }

  .spinner-small {
    width: 14px;
    height: 14px;
    border: 2px solid rgba(30, 64, 175, 0.3);
    border-top-color: #1e40af;
    border-radius: 50%;
    animation: spin 0.6s linear infinite;
    display: inline-block;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* Toast Notifications */
  :global(.toast) {
    position: fixed;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%) translateY(100px);
    padding: 12px 24px;
    border-radius: 12px;
    font-size: 14px;
    font-weight: 500;
    z-index: 1000;
    transition: transform 0.3s ease;
    box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
  }

  :global(.toast.show) {
    transform: translateX(-50%) translateY(0);
  }

  :global(.toast-success) {
    background: #10b981;
    color: white;
  }

  :global(.toast-error) {
    background: #f97316;
    color: white;
  }

  /* Responsive */
  @media (max-width: 640px) {
    .card {
      padding: 28px 20px;
    }

    h1 {
      font-size: 24px;
    }

    .otp-wrapper {
      flex-direction: column;
    }

    .generate-otp-btn {
      width: 100%;
      justify-content: center;
    }

    .otp-digits {
      gap: 6px;
    }

    .otp-digit {
      width: 32px;
      height: 32px;
      font-size: 16px;
    }
  }
</style>