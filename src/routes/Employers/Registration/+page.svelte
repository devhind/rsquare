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
  let selectedCompanyType = 'employer';
  let agreeToTerms = false;

  // --- Validation Errors ---
  let mobileError = '';
  let emailError = '';
  let userIdError = '';
  let passwordError = '';
  let confirmPasswordError = '';
  let otpError = '';
  let companyTypeError = '';
  let companyNameError = '';
  let nameError = '';
  let locationError = '';

  // Additional fields from the image
  let companyName = '';
  let yourName = '';
  let location = '';

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

  function validateCompanyType() {
    if (!selectedCompanyType) {
      companyTypeError = 'Please select company type';
      return false;
    } else {
      companyTypeError = '';
      return true;
    }
  }

  function validateCompanyName() {
    if (!companyName) {
      companyNameError = 'Company name is required';
      return false;
    } else {
      companyNameError = '';
      return true;
    }
  }

  function validateName() {
    if (!yourName) {
      nameError = 'Your name is required';
      return false;
    } else {
      nameError = '';
      return true;
    }
  }

  function validateLocation() {
    if (!location) {
      locationError = 'Location is required';
      return false;
    } else {
      locationError = '';
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
    const isCompanyTypeValid = validateCompanyType();
    const isCompanyNameValid = validateCompanyName();
    const isNameValid = validateName();
    const isLocationValid = validateLocation();
    
    return isMobileValid && isEmailValid && isUserIdValid && 
           isPasswordValid && isConfirmPasswordValid && isOtpValid &&
           isCompanyTypeValid && isCompanyNameValid && isNameValid && isLocationValid &&
           agreeToTerms;
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
    
    otp = '';
    otpError = '';
    
    setTimeout(() => {
      const otpInput = document.querySelector('.otp-input');
      if (otpInput) otpInput.focus();
    }, 100);
  }

  function handleOtpInput() {
    validateOtp();
    if (otp.length === 6 && !otpError && isOtpSent) {
      // Optional auto-submit
    }
  }

  // --- Submit Registration ---
  async function handleSubmit() {
    if (!validateAllFields()) {
      showToast('Please fix the errors above', 'error');
      return;
    }
    
    if (!agreeToTerms) {
      showToast('Please agree to the Terms & Conditions', 'error');
      return;
    }
    
    if (otp !== generatedOtp) {
      otpError = 'Invalid OTP. Please try again.';
      showToast('Invalid OTP', 'error');
      return;
    }
    
    isLoading = true;
    
    await new Promise(resolve => setTimeout(resolve, 1500));
    
    const payload = {
      mobile,
      email,
      userId,
      password,
      otp,
      companyType: selectedCompanyType,
      companyName,
      yourName,
      location
    };
    console.log('Submitting registration to database:', payload);
    
    isLoading = false;
    showToast('Registration successful!', 'success');
    
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

<div class="landing-container">
  <!-- Left Hero Section -->
  <div class="hero-section" in:fly={{ x: -20, duration: 500 }}>
    <div class="hero-content">
      <div class="hero-badge">
        <svg width="32" height="32" viewBox="0 0 32 32" fill="none">
          <path d="M16 2L4 9L16 16L28 9L16 2Z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
          <path d="M4 16L16 23L28 16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
          <path d="M4 23L16 30L28 23" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
        </svg>
        <span>HRV Portal</span>
      </div>
      <h1 class="hero-title">
        Welcome
        <span class="hero-highlight">One stop recruitment solution</span>
        <span>for both employers / placement agencies</span>
      </h1>
      
      <div class="feature-list">
        <div class="feature-item">
          <div class="feature-icon">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
              <circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="1.5"/>
              <path d="M12 6v6l4 2" stroke="currentColor" stroke-width="1.5"/>
            </svg>
          </div>
          <div>
            <strong>Fulfill your Hiring Process within 5 Days</strong>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
              <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2" stroke="currentColor" stroke-width="1.5"/>
              <circle cx="9" cy="7" r="4" stroke="currentColor" stroke-width="1.5"/>
              <path d="M23 21v-2a4 4 0 0 0-3-3.87" stroke="currentColor" stroke-width="1.5"/>
              <path d="M16 3.13a4 4 0 0 1 0 7.75" stroke="currentColor" stroke-width="1.5"/>
            </svg>
          </div>
          <div>
            <strong>Share your job opening with 10 Million+ Candidates</strong>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
              <rect x="2" y="3" width="20" height="14" rx="2" stroke="currentColor" stroke-width="1.5"/>
              <line x1="8" y1="21" x2="16" y2="21" stroke="currentColor" stroke-width="1.5"/>
              <line x1="12" y1="17" x2="12" y2="21" stroke="currentColor" stroke-width="1.5"/>
            </svg>
          </div>
          <div>
            <strong>Find the right candidates from our Large Database</strong>
          </div>
        </div>
      </div>

      <div class="login-prompt">
        Already registered? <a href="#" class="login-link">Login Now →</a>
      </div>
    </div>
  </div>

  <!-- Right Registration Form -->
  <div class="form-section" in:fly={{ x: 20, duration: 500, delay: 100 }}>
    <div class="form-card">
      <div class="form-header">
        <h2>Create an account now</h2>
        <p class="form-subtitle">Join thousands of businesses using HRV Portal</p>
      </div>

      <div class="form-grid">
        <!-- Company Type Selection -->
        <div class="input-group full-width">
          <label>Select Company Type <span class="required">*</span></label>
          <div class="company-type-group">
            <label class="company-type-option {selectedCompanyType === 'employer' ? 'active' : ''}">
              <input type="radio" value="employer" bind:group={selectedCompanyType} on:change={validateCompanyType}>
              <span class="company-type-content">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
                  <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" stroke="currentColor" stroke-width="1.5"/>
                  <polyline points="9 22 9 12 15 12 15 22" stroke="currentColor" stroke-width="1.5"/>
                </svg>
                Employer
              </span>
            </label>
            <label class="company-type-option {selectedCompanyType === 'agency' ? 'active' : ''}">
              <input type="radio" value="agency" bind:group={selectedCompanyType} on:change={validateCompanyType}>
              <span class="company-type-content">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
                  <path d="M20 7h-4.5L15 4H9L8.5 7H4" stroke="currentColor" stroke-width="1.5"/>
                  <rect x="4" y="10" width="16" height="11" rx="2" stroke="currentColor" stroke-width="1.5"/>
                  <circle cx="12" cy="15" r="2" stroke="currentColor" stroke-width="1.5"/>
                </svg>
                Placement Agency
              </span>
            </label>
          </div>
          {#if companyTypeError}<span class="error-message">{companyTypeError}</span>{/if}
        </div>

        <!-- Two column layout for Company Name and Your Name -->
        <div class="input-group">
          <label>Company Name <span class="required">*</span></label>
          <div class="input-wrapper {companyNameError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" stroke="currentColor" stroke-width="1.5"/>
                <polyline points="9 22 9 12 15 12 15 22" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type="text" placeholder="e.g., Tech Solutions Inc." bind:value={companyName} on:input={validateCompanyName}>
          </div>
          {#if companyNameError}<span class="error-message">{companyNameError}</span>{/if}
        </div>

        <div class="input-group">
          <label>Your Name <span class="required">*</span></label>
          <div class="input-wrapper {nameError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" stroke="currentColor" stroke-width="1.5"/>
                <circle cx="12" cy="7" r="4" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type="text" placeholder="Full name" bind:value={yourName} on:input={validateName}>
          </div>
          {#if nameError}<span class="error-message">{nameError}</span>{/if}
        </div>

        <!-- Email -->
        <div class="input-group">
          <label>Company Email <span class="required">*</span></label>
          <div class="input-wrapper {emailError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z" stroke="currentColor" stroke-width="1.5"/>
                <path d="M22 6L12 13L2 6" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type="email" placeholder="you@company.com" bind:value={email} on:input={validateEmail}>
          </div>
          {#if emailError}<span class="error-message">{emailError}</span>{/if}
        </div>

        <!-- Mobile with country code -->
        <div class="input-group">
          <label>Mobile Number <span class="required">*</span></label>
          <div class="mobile-input-wrapper">
            <div class="country-code">
              <span>IN</span>
              <svg width="12" height="12" viewBox="0 0 24 24" fill="none">
                <polyline points="6 9 12 15 18 9" stroke="currentColor" stroke-width="2"/>
              </svg>
              <span>+91</span>
            </div>
            <div class="input-wrapper {mobileError ? 'error' : ''}" style="flex: 1;">
              <input type="tel" placeholder="9876543210" bind:value={mobile} maxlength="10" on:input={validateMobile} on:keypress={(e) => handleKeyPress(e, generateAndSendOtp)}>
            </div>
          </div>
          {#if mobileError}<span class="error-message">{mobileError}</span>{/if}
        </div>

        <!-- Location -->
        <div class="input-group">
          <label>Your Location <span class="required">*</span></label>
          <div class="input-wrapper {locationError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z" stroke="currentColor" stroke-width="1.5"/>
                <circle cx="12" cy="10" r="3" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type="text" placeholder="City, State" bind:value={location} on:input={validateLocation}>
          </div>
          {#if locationError}<span class="error-message">{locationError}</span>{/if}
        </div>

        <!-- User ID -->
        <div class="input-group">
          <label>User ID <span class="required">*</span></label>
          <div class="input-wrapper {userIdError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" stroke="currentColor" stroke-width="1.5"/>
                <circle cx="12" cy="7" r="4" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type="text" placeholder="Choose a username" bind:value={userId} on:input={validateUserId}>
          </div>
          {#if userIdError}<span class="error-message">{userIdError}</span>{/if}
        </div>

        <!-- Password -->
        <div class="input-group">
          <label>Password <span class="required">*</span></label>
          <div class="input-wrapper {passwordError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <rect x="5" y="11" width="14" height="11" rx="2" stroke="currentColor" stroke-width="1.5"/>
                <path d="M12 16v2M8 11V8a4 4 0 0 1 8 0v3" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type={showPassword ? "text" : "password"} placeholder="Create a strong password" bind:value={password} on:input={validatePassword}>
            <button type="button" class="password-toggle" on:click={() => showPassword = !showPassword}>
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
          <label>Confirm Password <span class="required">*</span></label>
          <div class="input-wrapper {confirmPasswordError ? 'error' : ''}">
            <span class="input-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                <path d="M12 16v2M8 11V8a4 4 0 0 1 8 0v3" stroke="currentColor" stroke-width="1.5"/>
                <rect x="5" y="11" width="14" height="11" rx="2" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <input type={showConfirmPassword ? "text" : "password"} placeholder="Confirm your password" bind:value={confirmPassword} on:input={validateConfirmPassword}>
            <button type="button" class="password-toggle" on:click={() => showConfirmPassword = !showConfirmPassword}>
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z" stroke="currentColor" stroke-width="1.5"/>
                <circle cx="12" cy="12" r="3" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </button>
          </div>
          {#if confirmPasswordError}<span class="error-message">{confirmPasswordError}</span>{/if}
        </div>

        <!-- OTP Section -->
        <div class="input-group full-width">
          <label>Verification Code <span class="required">*</span></label>
          <div class="otp-container">
            <div class="otp-wrapper">
              <input type="text" placeholder="••••••" bind:value={otp} maxlength="6" class="otp-input {otpError ? 'error' : ''} {isOtpSent ? 'active' : ''}" on:input={handleOtpInput} on:keypress={(e) => handleKeyPress(e, handleSubmit)} disabled={!isOtpSent} autocomplete="off">
              <button class="generate-otp-btn" on:click={isOtpSent ? resendOtp : generateAndSendOtp} disabled={isLoading || (isOtpSent && resendCooldown > 0)}>
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
            
            {#if isOtpSent}
              <div class="otp-digits">
                {#each [0,1,2,3,4,5] as i}
                  <span class="otp-digit {otp[i] ? 'filled' : ''}">{otp[i] || '•'}</span>
                {/each}
              </div>
            {/if}
            
            {#if otpError}
              <span class="error-message">{otpError}</span>
            {:else if isOtpSent}
              <span class="hint-text success">✓ OTP sent to {mobile}</span>
            {:else}
              <span class="hint-text">Click "Send OTP" to receive 6-digit code</span>
            {/if}
          </div>
        </div>

        <!-- Terms Checkbox -->
        <div class="terms-checkbox">
          <label class="checkbox-label">
            <input type="checkbox" bind:checked={agreeToTerms}>
            <span class="checkmark"></span>
            <span class="terms-text">I agree to all the <a href="#">Terms & Conditions</a>, <a href="#">Privacy Policy</a> stated herein.</span>
          </label>
        </div>

        <!-- Submit Button -->
        <button class="btn-primary" on:click={handleSubmit} disabled={isLoading || !isOtpSent} class:loading={isLoading}>
          {#if isLoading}
            <span class="spinner"></span>
          {/if}
          {isLoading ? 'Creating Account...' : 'Create Account →'}
        </button>
      </div>
    </div>
  </div>
</div>

<style>
  /* Reset & Base */
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  /* CSS Custom Properties for consistent spacing */
  :root {
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 2rem;
    --spacing-2xl: 3rem;
    --spacing-3xl: 4rem;
    --border-radius-sm: 0.5rem;
    --border-radius-md: 0.75rem;
    --border-radius-lg: 1rem;
    --border-radius-xl: 1.5rem;
    --border-radius-2xl: 2rem;
    --font-size-xs: 0.75rem;
    --font-size-sm: 0.875rem;
    --font-size-base: 1rem;
    --font-size-lg: 1.125rem;
    --font-size-xl: 1.25rem;
    --font-size-2xl: 1.5rem;
    --font-size-3xl: 1.875rem;
    --font-size-4xl: 2.25rem;
    --font-size-5xl: 3rem;
    --container-padding: 1.5rem;
  }

  .landing-container {
    min-height: 100vh;
    display: flex;
    background: linear-gradient(135deg, #0a2540 0%, #1a4a6f 100%);
    position: relative;
    overflow-x: hidden;
    width: 100%;
    padding-top: 94px;
  }

  /* Left Hero Section */
  .hero-section {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: var(--container-padding);
    color: white;
    position: relative;
    background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" opacity="0.05"><path fill="white" d="M20,30 L30,20 L40,30 L30,40 Z M60,70 L70,60 L80,70 L70,80 Z M50,50 L55,45 L60,50 L55,55 Z"/></svg>');
    background-repeat: repeat;
    min-width: 0;
  }

  .hero-content {
    max-width: 500px;
    width: 100%;
  }

  .hero-badge {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    background: rgba(255, 255, 255, 0.15);
    backdrop-filter: blur(10px);
    padding: 10px 20px;
    border-radius: 100px;
    margin-bottom: var(--spacing-2xl);
    font-weight: 600;
    font-size: var(--font-size-sm);
  }

  .hero-title {
    font-size: clamp(var(--font-size-3xl), 5vw, var(--font-size-5xl));
    font-weight: 700;
    line-height: 1.2;
    margin-bottom: var(--spacing-2xl);
  }

  .hero-title span {
    display: block;
  }

  .hero-highlight {
    background: linear-gradient(135deg, #FFD700, #FFA500);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    font-size: clamp(var(--font-size-2xl), 4.5vw, var(--font-size-4xl));
    margin: var(--spacing-sm) 0;
  }

  .feature-list {
    display: flex;
    flex-direction: column;
    gap: var(--spacing-lg);
    margin: var(--spacing-3xl) 0;
  }

  .feature-item {
    display: flex;
    align-items: center;
    gap: var(--spacing-md);
    font-size: clamp(var(--font-size-sm), 2vw, var(--font-size-lg));
  }

  .feature-icon {
    width: 48px;
    height: 48px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: var(--border-radius-md);
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
  }

  .login-prompt {
    font-size: clamp(var(--font-size-xs), 2vw, var(--font-size-base));
    opacity: 0.9;
  }

  .login-link {
    color: #FFD700;
    text-decoration: none;
    font-weight: 600;
    transition: opacity 0.2s;
  }

  .login-link:hover {
    opacity: 0.8;
  }

  /* Right Form Section */
  .form-section {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: var(--container-padding);
    background: white;
    border-radius: 48px 0 0 48px;
    box-shadow: -10px 0 40px rgba(0, 0, 0, 0.1);
    min-width: 0;
    overflow-y: auto;
  }

  .form-card {
    width: 100%;
    max-width: 550px;
  }

  .form-header {
    text-align: center;
    margin-bottom: var(--spacing-2xl);
  }

  .form-header h2 {
    font-size: clamp(var(--font-size-xl), 4vw, var(--font-size-3xl));
    font-weight: 700;
    color: #0a2540;
    margin-bottom: var(--spacing-sm);
  }

  .form-subtitle {
    color: #64748b;
    font-size: clamp(var(--font-size-xs), 2vw, var(--font-size-sm));
  }

  .form-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: var(--spacing-lg);
  }

  .full-width {
    grid-column: span 2;
  }

  .input-group {
    width: 100%;
  }

  .input-group label {
    display: block;
    font-size: var(--font-size-xs);
    font-weight: 600;
    color: #334155;
    margin-bottom: 6px;
  }

  .required {
    color: #f97316;
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
    border-radius: var(--border-radius-md);
    font-size: var(--font-size-sm);
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
  }

  .password-toggle:hover {
    color: #2563eb;
  }

  /* Company Type Selection */
  .company-type-group {
    display: flex;
    gap: var(--spacing-sm);
  }

  .company-type-option {
    flex: 1;
    cursor: pointer;
  }

  .company-type-option input {
    display: none;
  }

  .company-type-content {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    padding: 12px;
    border: 1.5px solid #e2e8f0;
    border-radius: var(--border-radius-md);
    font-size: var(--font-size-xs);
    font-weight: 500;
    color: #64748b;
    transition: all 0.2s;
    background: white;
  }

  .company-type-option.active .company-type-content {
    border-color: #2563eb;
    background: #eff6ff;
    color: #2563eb;
  }

  /* Mobile Input with Country Code */
  .mobile-input-wrapper {
    display: flex;
    gap: 8px;
  }

  .country-code {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 0 12px;
    background: #f8fafc;
    border: 1.5px solid #e2e8f0;
    border-radius: var(--border-radius-md);
    font-size: var(--font-size-sm);
    font-weight: 500;
    color: #334155;
    flex-shrink: 0;
  }

  /* OTP Section */
  .otp-container {
    width: 100%;
  }

  .otp-wrapper {
    display: flex;
    gap: 12px;
    align-items: center;
  }

  .otp-input {
    flex: 2;
    padding: 12px;
    border: 1.5px solid #e2e8f0;
    border-radius: var(--border-radius-md);
    font-size: clamp(var(--font-size-lg), 3vw, 20px);
    text-align: center;
    letter-spacing: 4px;
    font-weight: 600;
    font-family: 'Courier New', monospace;
    transition: all 0.2s;
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
  }

  .generate-otp-btn {
    background: #f1f5f9;
    border: none;
    padding: 0 20px;
    height: 48px;
    border-radius: var(--border-radius-md);
    font-weight: 600;
    font-size: var(--font-size-sm);
    color: #1e40af;
    cursor: pointer;
    transition: all 0.2s;
    white-space: nowrap;
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .generate-otp-btn:hover:not(:disabled) {
    background: #e2e8f0;
    transform: translateY(-1px);
  }

  .generate-otp-btn:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  .otp-digits {
    display: flex;
    gap: 8px;
    justify-content: center;
    margin-top: var(--spacing-sm);
  }

  .otp-digit {
    width: 36px;
    height: 36px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #f8fafc;
    border-radius: 10px;
    font-size: var(--font-size-lg);
    font-weight: 600;
    font-family: monospace;
    color: #94a3b8;
  }

  .otp-digit.filled {
    background: #eef2ff;
    color: #2563eb;
  }

  .error-message {
    display: block;
    font-size: var(--font-size-xs);
    color: #f97316;
    margin-top: 6px;
  }

  .hint-text {
    display: flex;
    align-items: center;
    gap: 4px;
    font-size: var(--font-size-xs);
    color: #64748b;
    margin-top: 8px;
  }

  .hint-text.success {
    color: #10b981;
  }

  /* Terms Checkbox */
  .terms-checkbox {
    grid-column: span 2;
    margin: var(--spacing-sm) 0;
  }

  .checkbox-label {
    display: flex;
    align-items: center;
    gap: 12px;
    cursor: pointer;
    font-size: var(--font-size-xs);
    color: #64748b;
  }

  .checkbox-label input {
    display: none;
  }

  .checkmark {
    width: 20px;
    height: 20px;
    border: 2px solid #cbd5e1;
    border-radius: 6px;
    display: inline-block;
    position: relative;
    transition: all 0.2s;
    flex-shrink: 0;
  }

  .checkbox-label input:checked + .checkmark {
    background: #2563eb;
    border-color: #2563eb;
  }

  .checkbox-label input:checked + .checkmark::after {
    content: "✓";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: white;
    font-size: 12px;
  }

  .terms-text {
    line-height: 1.4;
  }

  .terms-text a {
    color: #2563eb;
    text-decoration: none;
  }

  .terms-text a:hover {
    text-decoration: underline;
  }

  /* Submit Button */
  .btn-primary {
    grid-column: span 2;
    padding: 14px;
    background: linear-gradient(135deg, #0066ff, #0044cc);
    color: white;
    border: none;
    border-radius: var(--border-radius-md);
    font-size: var(--font-size-base);
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: var(--spacing-sm);
  }

  .btn-primary:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 10px 25px -5px rgba(0, 102, 255, 0.3);
  }

  .btn-primary:disabled {
    opacity: 0.5;
    cursor: not-allowed;
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
    font-size: var(--font-size-sm);
    font-weight: 500;
    z-index: 1000;
    transition: transform 0.3s ease;
    box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
    white-space: nowrap;
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

  /* Responsive Design - Tablet */
  @media (max-width: 1024px) {
    :root {
      --container-padding: 2rem;
    }
    
    .feature-icon {
      width: 40px;
      height: 40px;
    }
    
    .otp-digit {
      width: 32px;
      height: 32px;
      font-size: var(--font-size-base);
    }
  }

  /* Responsive Design - Mobile Landscape */
  @media (max-width: 900px) {
    .landing-container {
      flex-direction: column;
    }
    
    .hero-section {
      padding: var(--spacing-2xl) var(--container-padding);
      text-align: center;
    }
    
    .hero-content {
      max-width: 100%;
    }
    
    .feature-list {
      align-items: center;
    }
    
    .feature-item {
      text-align: left;
      width: 100%;
      max-width: 400px;
      margin: 0 auto;
    }
    
    .form-section {
      border-radius: var(--border-radius-2xl) var(--border-radius-2xl) 0 0;
      padding: var(--spacing-2xl) var(--container-padding);
    }
    
    .hero-badge {
      margin-bottom: var(--spacing-xl);
    }
  }

  /* Responsive Design - Mobile Portrait */
  @media (max-width: 640px) {
    :root {
      --container-padding: 1rem;
      --spacing-lg: 1rem;
      --spacing-xl: 1.25rem;
      --spacing-2xl: 1.5rem;
    }
    
    .form-grid {
      grid-template-columns: 1fr;
      gap: var(--spacing-md);
    }
    
    .full-width,
    .terms-checkbox,
    .btn-primary {
      grid-column: span 1;
    }
    
    .company-type-group {
      flex-direction: column;
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
      width: 28px;
      height: 28px;
      font-size: var(--font-size-sm);
    }
    
    .mobile-input-wrapper {
      flex-direction: column;
    }
    
    .country-code {
      width: 100%;
      justify-content: center;
      padding: 10px;
    }
    
    .feature-icon {
      width: 36px;
      height: 36px;
    }
    
    .feature-list {
      gap: var(--spacing-md);
      margin: var(--spacing-xl) 0;
    }
    
    .hero-badge {
      padding: 6px 16px;
      font-size: var(--font-size-xs);
    }
    
    .hero-badge svg {
      width: 24px;
      height: 24px;
    }
    
    .login-prompt {
      margin-top: var(--spacing-md);
    }
    
    /* Better touch targets for mobile */
    .btn-primary,
    .generate-otp-btn,
    .company-type-content,
    .checkbox-label {
      cursor: pointer;
      -webkit-tap-highlight-color: transparent;
    }
    
    .btn-primary:active,
    .generate-otp-btn:active {
      transform: scale(0.98);
    }
    
    :global(.toast) {
      white-space: normal;
      text-align: center;
      max-width: 90%;
      font-size: var(--font-size-xs);
    }
  }

  /* Extra small devices */
  @media (max-width: 380px) {
    .otp-digit {
      width: 24px;
      height: 24px;
      font-size: var(--font-size-xs);
    }
    
    .otp-digits {
      gap: 4px;
    }
    
    .otp-input {
      letter-spacing: 2px;
      font-size: var(--font-size-base);
    }
    
    .feature-item {
      gap: var(--spacing-sm);
    }
    
    .feature-icon {
      width: 32px;
      height: 32px;
    }
    
    .feature-icon svg {
      width: 18px;
      height: 18px;
    }
  }

  /* Large screens (Desktop) */
  @media (min-width: 1400px) {
    :root {
      --container-padding: 3rem;
    }
    
    .hero-content {
      max-width: 600px;
    }
    
    .form-card {
      max-width: 650px;
    }
    
    .feature-icon {
      width: 56px;
      height: 56px;
    }
    
    .feature-icon svg {
      width: 28px;
      height: 28px;
    }
    
    .otp-digit {
      width: 44px;
      height: 44px;
      font-size: var(--font-size-xl);
    }
    
    .input-wrapper input,
    .generate-otp-btn,
    .btn-primary {
      padding: 14px;
    }
    
    .input-icon svg {
      width: 20px;
      height: 20px;
    }
    
    .input-wrapper input {
      padding: 14px 14px 14px 46px;
    }
  }

  /* Handle landscape orientation on mobile */
  @media (max-width: 900px) and (orientation: landscape) {
    .landing-container {
      flex-direction: row;
    }
    
    .hero-section,
    .form-section {
      overflow-y: auto;
      padding: var(--spacing-xl);
    }
    
    .hero-title {
      font-size: var(--font-size-2xl);
    }
    
    .hero-highlight {
      font-size: var(--font-size-xl);
    }
    
    .feature-list {
      gap: var(--spacing-sm);
      margin: var(--spacing-lg) 0;
    }
    
    .feature-item {
      font-size: var(--font-size-xs);
    }
    
    .feature-icon {
      width: 28px;
      height: 28px;
    }
    
    .form-header {
      margin-bottom: var(--spacing-lg);
    }
    
    .form-header h2 {
      font-size: var(--font-size-xl);
    }
  }

  /* Improve scrolling on form section */
  .form-section {
    scrollbar-width: thin;
  }
  
  .form-section::-webkit-scrollbar {
    width: 6px;
  }
  
  .form-section::-webkit-scrollbar-track {
    background: #f1f1f1;
    border-radius: 3px;
  }
  
  .form-section::-webkit-scrollbar-thumb {
    background: #c1c1c1;
    border-radius: 3px;
  }
  
  .form-section::-webkit-scrollbar-thumb:hover {
    background: #a8a8a8;
  }
</style>