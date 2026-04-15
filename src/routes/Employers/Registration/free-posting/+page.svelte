<!-- src/routes/post-job/+page.svelte -->
<script>
  import { fly } from 'svelte/transition';

  // ------------------------------
  // Form Fields
  // ------------------------------
  let jobTitle = '';
  let industry = '';
  let openings = '';
  let minExp = '';
  let maxExp = '';
  let genderPreference = 'Any';
  let minSalary = '';
  let maxSalary = '';
  let hideSalary = false;
  let country = '';
  let jobLocation = '';
  let jobDescription = '';

  // Company Details (Step 2)
  let companyName = '';
  let operatingAreas = '';
  let industryCovered = '';
  let companyWebsite = '';
  let companySize = '';

  // UI State
  let currentStep = 1;
  let isLoading = false;
  let isVerified = false;
  let mobileNumber = '';
  let emailId = '';
  let otp = '';
  let generatedOtp = '';
  let isOtpSent = false;
  let resendCooldown = 0;
  let countdownInterval = null;

  // Error states
  let errors = {
    jobTitle: '',
    industry: '',
    openings: '',
    minExp: '',
    maxExp: '',
    minSalary: '',
    maxSalary: '',
    country: '',
    jobLocation: '',
    jobDescription: '',
    companyName: '',
    operatingAreas: '',
    industryCovered: '',
    mobileNumber: '',
    emailId: '',
    otp: ''
  };

  let toasts = [];
  let showSuccess = false;

  // Industry options
  const industries = [
    'Information Technology',
    'Healthcare',
    'Finance & Banking',
    'Education',
    'Manufacturing',
    'Retail',
    'Construction',
    'Real Estate',
    'Hospitality',
    'Transportation',
    'Media & Entertainment',
    'Telecommunications',
    'Agriculture',
    'Energy & Utilities',
    'Consulting',
    'Other'
  ];

  const countries = [
    'India', 'United States', 'United Kingdom', 'Canada', 'Australia',
    'Germany', 'France', 'Singapore', 'UAE', 'Other'
  ];

  const companySizeOptions = [
    '1-10 employees',
    '11-50 employees',
    '51-200 employees',
    '201-500 employees',
    '501-1000 employees',
    '1000+ employees'
  ];

  function showToast(message, type = 'success') {
    const id = Date.now();
    toasts = [...toasts, { id, message, type }];
    setTimeout(() => {
      toasts = toasts.filter(t => t.id !== id);
    }, 5000);
  }

  function removeToast(id) {
    toasts = toasts.filter(t => t.id !== id);
  }

  // Step 1 Validation
  function validateStep1() {
    let isValid = true;
    const newErrors = { ...errors };

    if (!jobTitle.trim()) {
      newErrors.jobTitle = 'Job title is required';
      isValid = false;
    } else {
      newErrors.jobTitle = '';
    }

    if (!industry) {
      newErrors.industry = 'Please select an industry';
      isValid = false;
    } else {
      newErrors.industry = '';
    }

    if (!openings || parseInt(openings) < 1) {
      newErrors.openings = 'Enter valid number of openings';
      isValid = false;
    } else {
      newErrors.openings = '';
    }

    if (minExp && maxExp && parseInt(minExp) > parseInt(maxExp)) {
      newErrors.minExp = 'Min experience cannot exceed max';
      newErrors.maxExp = 'Max experience must be greater than min';
      isValid = false;
    } else {
      if (minExp && parseInt(minExp) < 0) {
        newErrors.minExp = 'Enter valid experience';
        isValid = false;
      } else {
        newErrors.minExp = '';
      }
      if (maxExp && parseInt(maxExp) < 0) {
        newErrors.maxExp = 'Enter valid experience';
        isValid = false;
      } else {
        newErrors.maxExp = '';
      }
    }

    if (minSalary && maxSalary && parseInt(minSalary) > parseInt(maxSalary)) {
      newErrors.minSalary = 'Min salary cannot exceed max';
      newErrors.maxSalary = 'Max salary must be greater than min';
      isValid = false;
    } else {
      newErrors.minSalary = '';
      newErrors.maxSalary = '';
    }

    if (!country) {
      newErrors.country = 'Please select a country';
      isValid = false;
    } else {
      newErrors.country = '';
    }

    if (!jobLocation.trim()) {
      newErrors.jobLocation = 'Job location is required';
      isValid = false;
    } else {
      newErrors.jobLocation = '';
    }

    if (!jobDescription.trim() || jobDescription.length < 50) {
      newErrors.jobDescription = 'Please provide detailed description (min 50 characters)';
      isValid = false;
    } else {
      newErrors.jobDescription = '';
    }

    errors = newErrors;
    return isValid;
  }

  // Step 2 Validation
  function validateStep2() {
    let isValid = true;
    const newErrors = { ...errors };

    if (!companyName.trim()) {
      newErrors.companyName = 'Company name is required';
      isValid = false;
    } else {
      newErrors.companyName = '';
    }

    if (!operatingAreas.trim()) {
      newErrors.operatingAreas = 'Operating areas is required';
      isValid = false;
    } else {
      newErrors.operatingAreas = '';
    }

    if (!industryCovered) {
      newErrors.industryCovered = 'Please select industry covered';
      isValid = false;
    } else {
      newErrors.industryCovered = '';
    }

    errors = newErrors;
    return isValid;
  }

  // Step 3 Validation
  function validateStep3() {
    let isValid = true;
    const newErrors = { ...errors };

    if (!mobileNumber.trim() || !/^[0-9]{10}$/.test(mobileNumber)) {
      newErrors.mobileNumber = 'Valid 10-digit mobile number required';
      isValid = false;
    } else {
      newErrors.mobileNumber = '';
    }

    if (!emailId.trim() || !/^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/.test(emailId)) {
      newErrors.emailId = 'Valid email address required';
      isValid = false;
    } else {
      newErrors.emailId = '';
    }

    if (!isVerified) {
      newErrors.otp = 'Please verify your mobile/email with OTP';
      isValid = false;
    } else {
      newErrors.otp = '';
    }

    errors = newErrors;
    return isValid;
  }

  // OTP Functions
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

  async function sendOtp() {
    if (!mobileNumber || !/^[0-9]{10}$/.test(mobileNumber)) {
      showToast('Enter valid mobile number first', 'error');
      return;
    }
    if (!emailId || !/^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/.test(emailId)) {
      showToast('Enter valid email address first', 'error');
      return;
    }

    isLoading = true;
    await new Promise(r => setTimeout(r, 800));
    generatedOtp = Math.floor(100000 + Math.random() * 900000).toString();
    console.log('OTP:', generatedOtp);
    showToast(`OTP sent to ${mobileNumber} & ${emailId}`, 'success');
    isOtpSent = true;
    startResendCooldown();
    isLoading = false;
  }

  function verifyOtp() {
    if (!otp) {
      errors.otp = 'Enter OTP';
      return;
    }
    if (otp === generatedOtp) {
      isVerified = true;
      errors.otp = '';
      showToast('Verification successful!', 'success');
    } else {
      errors.otp = 'Invalid OTP. Please try again.';
      showToast('Invalid OTP', 'error');
    }
  }

  function nextStep() {
    if (currentStep === 1 && validateStep1()) {
      currentStep = 2;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    } else if (currentStep === 2 && validateStep2()) {
      currentStep = 3;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  }

  function prevStep() {
    if (currentStep > 1) {
      currentStep--;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  }

  async function handleSubmit() {
    if (!validateStep3()) return;

    isLoading = true;

    await new Promise(r => setTimeout(r, 1500));

    const jobData = {
      jobTitle, industry, openings, minExp, maxExp, genderPreference,
      minSalary, maxSalary, hideSalary, country, jobLocation, jobDescription,
      companyName, operatingAreas, industryCovered, companyWebsite, companySize,
      mobileNumber, emailId, postedAt: new Date().toISOString()
    };

    console.log('Job Posted:', jobData);
    showToast('Job posted successfully!', 'success');
    showSuccess = true;

    setTimeout(() => {
      showSuccess = false;
    }, 3000);

    isLoading = false;
  }
</script>

<svelte:head>
  <title>Post a Job | HRV Portal</title>
</svelte:head>

<div class="post-job-page">
  <!-- Premium Animated Background -->
  <div class="bg-premium">
    <div class="gradient-orb-1"></div>
    <div class="gradient-orb-2"></div>
    <div class="gradient-orb-3"></div>
    <div class="grid-pattern"></div>
  </div>

  <div class="container">
    <!-- Premium Header -->
    <div class="header" in:fly={{ y: 20, duration: 500 }}>
      <div class="brand-section">
        <div class="logo">
          <div class="logo-icon">
            <svg width="28" height="28" viewBox="0 0 32 32" fill="none">
              <path d="M16 2L4 9L16 16L28 9L16 2Z" stroke="currentColor" stroke-width="1.5"/>
              <path d="M4 16L16 23L28 16" stroke="currentColor" stroke-width="1.5"/>
              <path d="M4 23L16 30L28 23" stroke="currentColor" stroke-width="1.5"/>
            </svg>
          </div>
          <span class="logo-text">HRV<span>Portal</span></span>
        </div>
        <div class="login-prompt">
          <a href="/login" class="login-btn">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M15 3h4a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2h-4"/>
              <polyline points="10 17 15 12 10 7"/>
              <line x1="15" y1="12" x2="3" y2="12"/>
            </svg>
            Already Registered? Login
          </a>
        </div>
      </div>

      <div class="hero-text">
        <h1>Post a Job Opening</h1>
        <p>Connect with top talent across India's largest professional network</p>
      </div>

      <!-- Premium Step Indicator -->
      <div class="step-indicator">
        <div class="step {currentStep >= 1 ? 'active' : ''} {currentStep > 1 ? 'completed' : ''}">
          <div class="step-circle">
            {#if currentStep > 1}
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3">
                <polyline points="20 6 9 17 4 12"/>
              </svg>
            {:else}
              1
            {/if}
          </div>
          <div class="step-info">
            <span class="step-label">Job Details</span>
            <span class="step-desc">Position & requirements</span>
          </div>
        </div>
        <div class="step-connector {currentStep > 1 ? 'active' : ''}"></div>
        <div class="step {currentStep >= 2 ? 'active' : ''} {currentStep > 2 ? 'completed' : ''}">
          <div class="step-circle">
            {#if currentStep > 2}
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3">
                <polyline points="20 6 9 17 4 12"/>
              </svg>
            {:else}
              2
            {/if}
          </div>
          <div class="step-info">
            <span class="step-label">Company Details</span>
            <span class="step-desc">Organization info</span>
          </div>
        </div>
        <div class="step-connector {currentStep > 2 ? 'active' : ''}"></div>
        <div class="step {currentStep >= 3 ? 'active' : ''}">
          <div class="step-circle">3</div>
          <div class="step-info">
            <span class="step-label">Verification</span>
            <span class="step-desc">Confirm identity</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Success Banner -->
    {#if showSuccess}
      <div class="success-banner" in:fly={{ y: -20, duration: 300 }}>
        <div class="success-icon">✓</div>
        <div class="success-content">
          <strong>Job Posted Successfully!</strong>
          <span>Your job listing is now live. Access our job-seekers database now.</span>
        </div>
      </div>
    {/if}

    <!-- Main Form Card -->
    <div class="form-card">
      <!-- Step 1: Job Details -->
      {#if currentStep === 1}
        <div class="form-step" in:fly={{ y: 20, duration: 400 }}>
          <div class="step-header">
            <div class="step-badge">Step 01</div>
            <h2>Job Detail</h2>
            <p>Provide comprehensive information about the position you're hiring for</p>
          </div>

          <div class="form-grid">
            <div class="input-group full-width">
              <label>Job Title / Role <span class="required">*</span></label>
              <div class="input-wrapper">
                <span class="input-icon">💼</span>
                <input type="text" bind:value={jobTitle} placeholder="e.g., Senior Software Engineer" class={errors.jobTitle ? 'error' : ''}>
              </div>
              {#if errors.jobTitle}<span class="error-text">{errors.jobTitle}</span>{/if}
            </div>

            <div class="input-group">
              <label>Select Industry <span class="required">*</span></label>
              <div class="select-wrapper">
                <select bind:value={industry} class={errors.industry ? 'error' : ''}>
                  <option value="" disabled>Select Industry</option>
                  {#each industries as ind}
                    <option value={ind}>{ind}</option>
                  {/each}
                </select>
              </div>
              {#if errors.industry}<span class="error-text">{errors.industry}</span>{/if}
            </div>

            <div class="input-group">
              <label>No. of Openings <span class="required">*</span></label>
              <div class="input-wrapper">
                <span class="input-icon">👥</span>
                <input type="number" bind:value={openings} placeholder="e.g., 5" min="1" class={errors.openings ? 'error' : ''}>
              </div>
              {#if errors.openings}<span class="error-text">{errors.openings}</span>{/if}
            </div>

            <div class="input-group">
              <label>Min Experience (years)</label>
              <div class="input-wrapper">
                <span class="input-icon">📅</span>
                <input type="number" bind:value={minExp} placeholder="0" min="0" step="0.5">
              </div>
            </div>

            <div class="input-group">
              <label>Max Experience (years)</label>
              <div class="input-wrapper">
                <span class="input-icon">📅</span>
                <input type="number" bind:value={maxExp} placeholder="10" min="0" step="0.5">
              </div>
              {#if errors.minExp}<span class="error-text">{errors.minExp}</span>{/if}
            </div>

            <div class="input-group">
              <label>Gender Preference</label>
              <div class="select-wrapper">
                <select bind:value={genderPreference}>
                  <option value="Any">👥 Any Gender</option>
                  <option value="Male">👨 Male Only</option>
                  <option value="Female">👩 Female Only</option>
                </select>
              </div>
            </div>

            <div class="input-group">
              <label>Annual Salary (min) ₹</label>
              <div class="input-wrapper">
                <span class="input-icon">💰</span>
                <input type="number" bind:value={minSalary} placeholder="e.g., 300000" step="10000">
              </div>
            </div>

            <div class="input-group">
              <label>Annual Salary (max) ₹</label>
              <div class="input-wrapper">
                <span class="input-icon">💰</span>
                <input type="number" bind:value={maxSalary} placeholder="e.g., 500000" step="10000">
              </div>
              {#if errors.minSalary}<span class="error-text">{errors.minSalary}</span>{/if}
            </div>

            <div class="input-group full-width">
              <label class="checkbox-wrapper">
                <input type="checkbox" bind:checked={hideSalary}>
                <span class="checkbox-custom"></span>
                <span class="checkbox-label">Hide Salary from jobseekers</span>
              </label>
            </div>

            <div class="input-group">
              <label>Select Country <span class="required">*</span></label>
              <div class="select-wrapper">
                <select bind:value={country} class={errors.country ? 'error' : ''}>
                  <option value="" disabled>Select Country</option>
                  {#each countries as c}
                    <option value={c}>{c}</option>
                  {/each}
                </select>
              </div>
              {#if errors.country}<span class="error-text">{errors.country}</span>{/if}
            </div>

            <div class="input-group full-width">
              <label>Job Location <span class="required">*</span></label>
              <div class="input-wrapper">
                <span class="input-icon">📍</span>
                <input type="text" bind:value={jobLocation} placeholder="City, State / Remote" class={errors.jobLocation ? 'error' : ''}>
              </div>
              {#if errors.jobLocation}<span class="error-text">{errors.jobLocation}</span>{/if}
            </div>

            <div class="input-group full-width">
              <label>Job Description <span class="required">*</span></label>
              <textarea bind:value={jobDescription} rows="6" placeholder="Please specify job requirements, desired skills, responsibilities, benefits, etc. (min 50 characters)" class={errors.jobDescription ? 'error' : ''}></textarea>
              <div class="char-counter {jobDescription.length >= 50 ? 'valid' : ''}">
                {jobDescription.length} / 50+ characters required
              </div>
              {#if errors.jobDescription}<span class="error-text">{errors.jobDescription}</span>{/if}
            </div>
          </div>
        </div>
      {/if}

      <!-- Step 2: Company Details -->
      {#if currentStep === 2}
        <div class="form-step" in:fly={{ y: 20, duration: 400 }}>
          <div class="step-header">
            <div class="step-badge">Step 02</div>
            <h2>Company Details</h2>
            <p>Share key information about your organization to attract the right talent</p>
          </div>

          <div class="form-grid">
            <div class="input-group full-width">
              <label>Company Name <span class="required">*</span></label>
              <div class="input-wrapper">
                <span class="input-icon">🏢</span>
                <input type="text" bind:value={companyName} placeholder="Enter your registered company name" class={errors.companyName ? 'error' : ''}>
              </div>
              {#if errors.companyName}<span class="error-text">{errors.companyName}</span>{/if}
            </div>

            <div class="input-group">
              <label>Operating Areas <span class="required">*</span></label>
              <div class="input-wrapper">
                <span class="input-icon">🌍</span>
                <input type="text" bind:value={operatingAreas} placeholder="e.g., Mumbai, Delhi, Bangalore" class={errors.operatingAreas ? 'error' : ''}>
              </div>
              {#if errors.operatingAreas}<span class="error-text">{errors.operatingAreas}</span>{/if}
            </div>

            <div class="input-group">
              <label>Industry Covered <span class="required">*</span></label>
              <div class="select-wrapper">
                <select bind:value={industryCovered} class={errors.industryCovered ? 'error' : ''}>
                  <option value="" disabled>Select Industry Covered</option>
                  {#each industries as ind}
                    <option value={ind}>{ind}</option>
                  {/each}
                </select>
              </div>
              {#if errors.industryCovered}<span class="error-text">{errors.industryCovered}</span>{/if}
            </div>

            <div class="input-group">
              <label>Company Website</label>
              <div class="input-wrapper">
                <span class="input-icon">🌐</span>
                <input type="url" bind:value={companyWebsite} placeholder="https://www.company.com">
              </div>
            </div>

            <div class="input-group">
              <label>Company Size</label>
              <div class="select-wrapper">
                <select bind:value={companySize}>
                  <option value="" disabled>Select company size</option>
                  {#each companySizeOptions as size}
                    <option value={size}>{size}</option>
                  {/each}
                </select>
              </div>
            </div>
          </div>

          <!-- How It Works Section -->
          <div class="how-it-works">
            <div class="section-badge">How It Works</div>
            <h3>Hire with Easy: Simple Steps, Perfect Candidate!</h3>
            <div class="steps-grid">
              <div class="work-card">
                <div class="work-icon">📝</div>
                <h4>Add Job Details</h4>
                <p>Post jobs easily and for free. Simply add job details and reach your ideal candidates effortlessly on our user-friendly platform.</p>
              </div>
              <div class="work-card">
                <div class="work-icon">🏢</div>
                <h4>Provide Company Details</h4>
                <p>Enhance job visibility by sharing key company information like Operating Areas, Industry Covered, etc. for attracting top talent effortlessly.</p>
              </div>
              <div class="work-card">
                <div class="work-icon">✅</div>
                <h4>Verify Mobile/Email</h4>
                <p>Ensure authenticity: Verify your mobile/email after posting a job on our portal to boost trust and security in your job listings.</p>
              </div>
              <div class="work-card">
                <div class="work-icon">📊</div>
                <h4>Access Database</h4>
                <p>After posting a job, clients gain access to our extensive database of job-seekers, streamlining the hiring process effectively.</p>
              </div>
            </div>
          </div>
        </div>
      {/if}

      <!-- Step 3: Verification -->
      {#if currentStep === 3}
        <div class="form-step" in:fly={{ y: 20, duration: 400 }}>
          <div class="step-header">
            <div class="step-badge">Step 03</div>
            <h2>Verify Your Identity</h2>
            <p>Complete verification to activate your job posting</p>
          </div>

          <div class="verification-container">
            <div class="verification-card">
              <div class="verification-icon">📱</div>
              <div class="form-grid">
                <div class="input-group full-width">
                  <label>Mobile Number <span class="required">*</span></label>
                  <div class="input-wrapper">
                    <span class="input-icon">+91</span>
                    <input type="tel" bind:value={mobileNumber} placeholder="9876543210" maxlength="10" class={errors.mobileNumber ? 'error' : ''}>
                  </div>
                  {#if errors.mobileNumber}<span class="error-text">{errors.mobileNumber}</span>{/if}
                </div>

                <div class="input-group full-width">
                  <label>Email ID <span class="required">*</span></label>
                  <div class="input-wrapper">
                    <span class="input-icon">@</span>
                    <input type="email" bind:value={emailId} placeholder="your@company.com" class={errors.emailId ? 'error' : ''}>
                  </div>
                  {#if errors.emailId}<span class="error-text">{errors.emailId}</span>{/if}
                </div>

                <div class="input-group full-width">
                  <label>OTP Verification</label>
                  <div class="otp-container">
                    <div class="otp-input-group">
                      <input type="text" bind:value={otp} placeholder="Enter 6-digit OTP" maxlength="6" class={errors.otp ? 'error' : ''} disabled={isVerified}>
                      <button class="otp-send-btn" on:click={sendOtp} disabled={isLoading || isVerified}>
                        {#if isLoading}<span class="spinner-small"></span>{/if}
                        {isVerified ? 'Verified ✓' : (isOtpSent ? 'Resend OTP' : 'Send OTP')}
                      </button>
                    </div>
                    {#if isOtpSent && !isVerified && resendCooldown > 0}
                      <span class="resend-timer">Resend available in {resendCooldown}s</span>
                    {/if}
                    {#if !isVerified && isOtpSent}
                      <button class="verify-otp-btn" on:click={verifyOtp}>Verify OTP</button>
                    {/if}
                    {#if errors.otp}<span class="error-text">{errors.otp}</span>{/if}
                    {#if isVerified}
                      <div class="verified-badge">
                        <span class="verified-icon">✓</span>
                        Mobile number and email verified successfully!
                      </div>
                    {/if}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      {/if}

      <!-- Form Actions -->
      <div class="form-actions">
        {#if currentStep > 1}
          <button class="btn-secondary" on:click={prevStep}>
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M15 18l-6-6 6-6"/>
            </svg>
            Back
          </button>
        {/if}
        
        {#if currentStep < 3}
          <button class="btn-primary" on:click={nextStep}>
            Continue
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M9 18l6-6-6-6"/>
            </svg>
          </button>
        {:else}
          <button class="btn-primary btn-submit" on:click={handleSubmit} disabled={isLoading || !isVerified}>
            {#if isLoading}
              <span class="spinner"></span>
              Posting Job...
            {:else}
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z"/>
              </svg>
              Post Job for Free
            {/if}
          </button>
        {/if}
      </div>

      <!-- Trust Badge -->
      <div class="trust-badge">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/>
        </svg>
        <span>Your information is secure. We never share your data with third parties.</span>
      </div>
    </div>
  </div>

  <!-- Toast Container -->
  <div class="toast-container">
    {#each toasts as toast (toast.id)}
      <div class="toast toast-{toast.type}" in:fly={{ y: 20, duration: 300 }}>
        <span class="toast-icon">{toast.type === 'success' ? '✓' : '⚠'}</span>
        <span>{toast.message}</span>
        <button on:click={() => removeToast(toast.id)}>✕</button>
      </div>
    {/each}
  </div>
</div>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  .post-job-page {
    min-height: 100vh;
    background: linear-gradient(145deg, #f5f7fa 0%, #eef2f6 100%);
    position: relative;
    overflow-x: hidden;
    padding-top: 95px;
  }

  /* Premium Background */
  .bg-premium {
    position: fixed;
    inset: 0;
    overflow: hidden;
    z-index: 0;
  }

  .gradient-orb-1 {
    position: absolute;
    top: -20%;
    right: -10%;
    width: 600px;
    height: 600px;
    background: radial-gradient(circle, rgba(59, 130, 246, 0.08) 0%, transparent 70%);
    border-radius: 50%;
    animation: float1 20s ease-in-out infinite;
  }

  .gradient-orb-2 {
    position: absolute;
    bottom: -20%;
    left: -10%;
    width: 500px;
    height: 500px;
    background: radial-gradient(circle, rgba(139, 92, 246, 0.06) 0%, transparent 70%);
    border-radius: 50%;
    animation: float2 25s ease-in-out infinite;
  }

  .gradient-orb-3 {
    position: absolute;
    top: 40%;
    left: 30%;
    width: 400px;
    height: 400px;
    background: radial-gradient(circle, rgba(236, 72, 153, 0.04) 0%, transparent 70%);
    border-radius: 50%;
    animation: pulse 8s ease-in-out infinite;
  }

  .grid-pattern {
    position: absolute;
    inset: 0;
    background-image: linear-gradient(rgba(0, 0, 0, 0.02) 1px, transparent 1px),
                      linear-gradient(90deg, rgba(0, 0, 0, 0.02) 1px, transparent 1px);
    background-size: 50px 50px;
  }

  @keyframes float1 {
    0%, 100% { transform: translate(0, 0); }
    33% { transform: translate(50px, -40px); }
    66% { transform: translate(-40px, 30px); }
  }

  @keyframes float2 {
    0%, 100% { transform: translate(0, 0); }
    33% { transform: translate(-50px, 40px); }
    66% { transform: translate(40px, -30px); }
  }

  @keyframes pulse {
    0%, 100% { transform: scale(1); opacity: 0.5; }
    50% { transform: scale(1.15); opacity: 0.3; }
  }

  .container {
    position: relative;
    z-index: 1;
    max-width: 1000px;
    margin: 0 auto;
    padding: 40px 24px;
  }

  /* Header Styles */
  .header {
    margin-bottom: 40px;
  }

  .brand-section {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 20px;
    margin-bottom: 40px;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .logo-icon {
    width: 44px;
    height: 44px;
    background: linear-gradient(135deg, #2563eb, #1e40af);
    border-radius: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    box-shadow: 0 8px 20px -6px rgba(37, 99, 235, 0.4);
  }

  .logo-text {
    font-size: 24px;
    font-weight: 700;
    background: linear-gradient(135deg, #0f172a, #1e293b);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
  }

  .logo-text span {
    background: linear-gradient(135deg, #2563eb, #7c3aed);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
  }

  .login-btn {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 10px 20px;
    background: white;
    border-radius: 40px;
    text-decoration: none;
    color: #2563eb;
    font-weight: 600;
    font-size: 14px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    transition: all 0.2s;
  }

  .login-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 20px -6px rgba(37, 99, 235, 0.2);
  }

  .hero-text {
    text-align: center;
    margin-bottom: 40px;
  }

  .hero-text h1 {
    font-size: 38px;
    font-weight: 800;
    background: linear-gradient(135deg, #0f172a, #1e293b);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    margin-bottom: 12px;
  }

  .hero-text p {
    color: #64748b;
    font-size: 16px;
  }

  /* Step Indicator */
  .step-indicator {
    display: flex;
    align-items: center;
    justify-content: center;
    background: white;
    padding: 20px 32px;
    border-radius: 80px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.05);
  }

  .step {
    display: flex;
    align-items: center;
    gap: 14px;
  }

  .step-circle {
    width: 48px;
    height: 48px;
    background: #e2e8f0;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 18px;
    color: #64748b;
    transition: all 0.3s;
  }

  .step.active .step-circle {
    background: linear-gradient(135deg, #2563eb, #1e40af);
    color: white;
    box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.2);
  }

  .step.completed .step-circle {
    background: #10b981;
    color: white;
  }

  .step-info {
    display: flex;
    flex-direction: column;
  }

  .step-label {
    font-size: 14px;
    font-weight: 700;
    color: #1e293b;
  }

  .step-desc {
    font-size: 11px;
    color: #94a3b8;
  }

  .step-connector {
    width: 60px;
    height: 2px;
    background: #e2e8f0;
    margin: 0 16px;
    transition: all 0.3s;
  }

  .step-connector.active {
    background: #10b981;
  }

  /* Form Card */
  .form-card {
    background: white;
    border-radius: 40px;
    padding: 48px;
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.15);
  }

  .step-header {
    margin-bottom: 36px;
    text-align: center;
  }

  .step-badge {
    display: inline-block;
    padding: 6px 16px;
    background: #eef2ff;
    color: #2563eb;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 600;
    margin-bottom: 16px;
  }

  .step-header h2 {
    font-size: 28px;
    font-weight: 700;
    color: #0f172a;
    margin-bottom: 8px;
  }

  .step-header p {
    color: #64748b;
    font-size: 14px;
  }

  /* Form Grid */
  .form-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }

  .full-width {
    grid-column: span 2;
  }

  .input-group {
    width: 100%;
  }

  .input-group label {
    display: block;
    font-size: 13px;
    font-weight: 600;
    color: #334155;
    margin-bottom: 8px;
  }

  .required {
    color: #ef4444;
  }

  .input-wrapper, .select-wrapper {
    position: relative;
  }

  .input-icon {
    position: absolute;
    left: 14px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 16px;
    pointer-events: none;
  }

  .input-wrapper input, .select-wrapper select, textarea {
    width: 100%;
    padding: 14px 16px 14px 42px;
    border: 1.5px solid #e2e8f0;
    border-radius: 16px;
    font-size: 14px;
    transition: all 0.2s;
    font-family: inherit;
  }

  .select-wrapper select {
    padding: 14px 16px;
    appearance: none;
    background: white;
    cursor: pointer;
  }

  .input-wrapper input:focus, .select-wrapper select:focus, textarea:focus {
    outline: none;
    border-color: #2563eb;
    box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
  }

  .input-wrapper input.error, .select-wrapper select.error, textarea.error {
    border-color: #ef4444;
  }

  textarea {
    padding: 14px 16px;
    resize: vertical;
  }

  .char-counter {
    text-align: right;
    font-size: 11px;
    color: #94a3b8;
    margin-top: 6px;
  }

  .char-counter.valid {
    color: #10b981;
  }

  .error-text {
    display: block;
    font-size: 11px;
    color: #ef4444;
    margin-top: 6px;
  }

  /* Checkbox */
  .checkbox-wrapper {
    display: flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
  }

  .checkbox-wrapper input {
    display: none;
  }

  .checkbox-custom {
    width: 20px;
    height: 20px;
    border: 2px solid #cbd5e1;
    border-radius: 6px;
    display: inline-block;
    position: relative;
  }

  .checkbox-wrapper input:checked + .checkbox-custom {
    background: #2563eb;
    border-color: #2563eb;
  }

  .checkbox-wrapper input:checked + .checkbox-custom::after {
    content: "✓";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: white;
    font-size: 12px;
  }

  .checkbox-label {
    font-size: 13px;
    color: #475569;
  }

  /* OTP */
  .otp-container {
    width: 100%;
  }

  .otp-input-group {
    display: flex;
    gap: 12px;
  }

  .otp-input-group input {
    flex: 2;
    padding: 14px 16px;
    border: 1.5px solid #e2e8f0;
    border-radius: 16px;
    font-size: 16px;
    text-align: center;
    letter-spacing: 4px;
  }

  .otp-send-btn {
    padding: 14px 24px;
    background: #f1f5f9;
    border: none;
    border-radius: 16px;
    font-weight: 600;
    font-size: 13px;
    cursor: pointer;
    transition: all 0.2s;
  }

  .otp-send-btn:hover:not(:disabled) {
    background: #e2e8f0;
  }

  .verify-otp-btn {
    margin-top: 12px;
    width: 100%;
    padding: 14px;
    background: linear-gradient(135deg, #2563eb, #1e40af);
    color: white;
    border: none;
    border-radius: 16px;
    font-weight: 600;
    cursor: pointer;
  }

  .resend-timer {
    font-size: 11px;
    color: #94a3b8;
    margin-top: 8px;
    display: block;
  }

  .verified-badge {
    margin-top: 12px;
    padding: 12px;
    background: #ecfdf5;
    border-radius: 12px;
    display: flex;
    align-items: center;
    gap: 8px;
    color: #059669;
    font-size: 13px;
    font-weight: 500;
  }

  .verified-icon {
    width: 20px;
    height: 20px;
    background: #10b981;
    color: white;
    border-radius: 50%;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
  }

  /* How It Works */
  .how-it-works {
    margin-top: 48px;
    padding-top: 40px;
    border-top: 1px solid #e2e8f0;
    text-align: center;
  }

  .section-badge {
    display: inline-block;
    padding: 6px 16px;
    background: #f1f5f9;
    color: #475569;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 600;
    margin-bottom: 16px;
  }

  .how-it-works h3 {
    font-size: 22px;
    font-weight: 700;
    color: #0f172a;
    margin-bottom: 32px;
  }

  .steps-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 24px;
  }

  .work-card {
    text-align: center;
    padding: 24px 20px;
    background: #f8fafc;
    border-radius: 24px;
    transition: all 0.3s;
  }

  .work-card:hover {
    transform: translateY(-4px);
    box-shadow: 0 10px 30px -10px rgba(0,0,0,0.1);
  }

  .work-icon {
    font-size: 40px;
    margin-bottom: 16px;
  }

  .work-card h4 {
    font-size: 15px;
    font-weight: 700;
    color: #0f172a;
    margin-bottom: 10px;
  }

  .work-card p {
    font-size: 12px;
    color: #64748b;
    line-height: 1.5;
  }

  /* Form Actions */
  .form-actions {
    display: flex;
    justify-content: space-between;
    gap: 16px;
    margin-top: 40px;
    padding-top: 32px;
    border-top: 1px solid #e2e8f0;
  }

  .btn-primary, .btn-secondary {
    padding: 14px 32px;
    border-radius: 60px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    gap: 8px;
    border: none;
  }

  .btn-primary {
    background: linear-gradient(135deg, #2563eb, #1e40af);
    color: white;
    flex: 1;
    justify-content: center;
  }

  .btn-primary:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 10px 25px -5px rgba(37, 99, 235, 0.4);
  }

  .btn-primary:disabled {
    opacity: 0.7;
    cursor: not-allowed;
  }

  .btn-submit {
    background: linear-gradient(135deg, #10b981, #059669);
  }

  .btn-submit:hover:not(:disabled) {
    box-shadow: 0 10px 25px -5px rgba(16, 185, 129, 0.4);
  }

  .btn-secondary {
    background: #f1f5f9;
    color: #475569;
  }

  .btn-secondary:hover {
    background: #e2e8f0;
  }

  .spinner, .spinner-small {
    width: 18px;
    height: 18px;
    border: 2px solid rgba(255,255,255,0.3);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.6s linear infinite;
  }

  .spinner-small {
    width: 14px;
    height: 14px;
    border-top-color: #2563eb;
    border-color: rgba(37,99,235,0.3);
    border-top-color: #2563eb;
    display: inline-block;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* Trust Badge */
  .trust-badge {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
    padding: 12px;
    background: #f8fafc;
    border-radius: 60px;
    font-size: 12px;
    color: #64748b;
  }

  /* Success Banner */
  .success-banner {
    background: linear-gradient(135deg, #10b981, #059669);
    color: white;
    padding: 16px 24px;
    border-radius: 20px;
    display: flex;
    align-items: center;
    gap: 16px;
    margin-bottom: 24px;
  }

  .success-icon {
    width: 32px;
    height: 32px;
    background: rgba(255,255,255,0.2);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
    font-weight: 700;
  }

  .success-content strong {
    display: block;
    margin-bottom: 4px;
  }

  .success-content span {
    font-size: 13px;
    opacity: 0.9;
  }

  /* Toast */
  .toast-container {
    position: fixed;
    bottom: 24px;
    right: 24px;
    z-index: 1000;
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .toast {
    background: white;
    border-radius: 16px;
    padding: 14px 20px;
    display: flex;
    align-items: center;
    gap: 12px;
    box-shadow: 0 10px 30px -8px rgba(0,0,0,0.15);
    font-size: 13px;
    font-weight: 500;
    min-width: 280px;
  }

  .toast-success {
    border-left: 4px solid #10b981;
  }

  .toast-error {
    border-left: 4px solid #ef4444;
  }

  .toast-icon {
    font-size: 16px;
  }

  .toast button {
    background: none;
    border: none;
    cursor: pointer;
    color: #94a3b8;
    margin-left: auto;
    font-size: 16px;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .container {
      padding: 20px 16px;
    }

    .form-card {
      padding: 28px 20px;
      border-radius: 28px;
    }

    .form-grid {
      grid-template-columns: 1fr;
      gap: 20px;
    }

    .full-width {
      grid-column: span 1;
    }

    .step-indicator {
      flex-direction: column;
      gap: 16px;
      background: transparent;
      padding: 0;
      box-shadow: none;
    }

    .step {
      width: 100%;
      background: white;
      padding: 16px 20px;
      border-radius: 60px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    }

    .step-connector {
      display: none;
    }

    .steps-grid {
      grid-template-columns: 1fr;
      gap: 16px;
    }

    .work-card {
      display: flex;
      text-align: left;
      gap: 16px;
      align-items: center;
      padding: 20px;
    }

    .work-icon {
      margin-bottom: 0;
      font-size: 32px;
    }

    .brand-section {
      flex-direction: column;
      text-align: center;
    }

    .hero-text h1 {
      font-size: 28px;
    }

    .form-actions {
      flex-direction: column;
    }

    .btn-primary, .btn-secondary {
      width: 100%;
      justify-content: center;
    }

    .otp-input-group {
      flex-direction: column;
    }

    .step-header h2 {
      font-size: 24px;
    }

    .how-it-works h3 {
      font-size: 18px;
    }
  }

  @media (max-width: 480px) {
    .form-card {
      padding: 20px 16px;
      border-radius: 24px;
    }

    .step-circle {
      width: 40px;
      height: 40px;
      font-size: 14px;
    }

    .step-label {
      font-size: 13px;
    }

    .step-desc {
      font-size: 10px;
    }

    .hero-text h1 {
      font-size: 24px;
    }
  }
</style>