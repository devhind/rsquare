<!-- src/routes/COMPANY_PROFILE/+page.svelte -->
<script>
  import { onMount } from 'svelte';
  import { fly } from 'svelte/transition';

  // Form fields
  let userId = '';
  let companyName = '';
  let gstn = '';
  let logoFile = null;
  let logoPreview = '';
  let yearEst = '';
  let companyType = '';
  let ce = '';
  let briefProfile = '';
  let website = '';
  let employeeCount = '';
  let address = '';

  // UI state
  let isLoading = false;
  let activeTab = 'basic';
  let isDragging = false;
  let showSuccess = false;

  let errors = {
    userId: '',
    companyName: '',
    gstn: '',
    logo: '',
    yearEst: '',
    companyType: '',
    ce: '',
    briefProfile: '',
    website: '',
    employeeCount: '',
    address: ''
  };

  // Options for dropdowns
  const companyTypes = [
    'Private Limited Company',
    'Public Limited Company',
    'Limited Liability Partnership (LLP)',
    'Partnership Firm',
    'Sole Proprietorship',
    'One Person Company (OPC)',
    'Section 8 Company (Non-Profit)',
    'Government Undertaking',
    'Others'
  ];

  const ceOptions = [
    'Company',
    'Enterprise',
    'Startup',
    'MSME',
    'Large Enterprise'
  ];

  const employeeOptions = [
    '1-10 employees',
    '11-50 employees',
    '51-200 employees',
    '201-500 employees',
    '501-1000 employees',
    '1000+ employees'
  ];

  onMount(() => {
    const storedUserId = localStorage.getItem('hrv_userId') || sessionStorage.getItem('hrv_userId');
    if (storedUserId) {
      userId = storedUserId;
    } else {
      userId = 'DEMO_USER_001';
    }
  });

  function validateUserId() {
    if (!userId) {
      errors.userId = 'User ID is required';
      return false;
    } else {
      errors.userId = '';
      return true;
    }
  }

  function validateCompanyName() {
    if (!companyName.trim()) {
      errors.companyName = 'Company name is required';
      return false;
    } else if (companyName.length < 3) {
      errors.companyName = 'Company name must be at least 3 characters';
      return false;
    } else {
      errors.companyName = '';
      return true;
    }
  }

  function validateGSTN() {
    const gstnRegex = /^[0-9]{2}[A-Z]{5}[0-9]{4}[A-Z]{1}[1-9A-Z]{1}Z[0-9A-Z]{1}$/;
    if (!gstn.trim()) {
      errors.gstn = 'GSTIN is required';
      return false;
    } else if (!gstnRegex.test(gstn.toUpperCase())) {
      errors.gstn = 'Invalid GSTIN format (e.g., 22AAAAA0000A1Z5)';
      return false;
    } else {
      errors.gstn = '';
      return true;
    }
  }

  function validateLogo() {
    if (!logoFile) {
      errors.logo = 'Company logo is required';
      return false;
    } else if (logoFile.size > 2 * 1024 * 1024) {
      errors.logo = 'Logo must be less than 2MB';
      return false;
    } else if (!['image/jpeg', 'image/png', 'image/webp', 'image/svg+xml'].includes(logoFile.type)) {
      errors.logo = 'Only JPG, PNG, WEBP, or SVG images are allowed';
      return false;
    } else {
      errors.logo = '';
      return true;
    }
  }

  function validateYearEst() {
    const currentYear = new Date().getFullYear();
    const year = parseInt(yearEst);
    if (!yearEst) {
      errors.yearEst = 'Year of establishment is required';
      return false;
    } else if (isNaN(year) || year < 1900 || year > currentYear) {
      errors.yearEst = `Please enter a valid year between 1900 and ${currentYear}`;
      return false;
    } else {
      errors.yearEst = '';
      return true;
    }
  }

  function validateCompanyType() {
    if (!companyType) {
      errors.companyType = 'Please select a company type';
      return false;
    } else {
      errors.companyType = '';
      return true;
    }
  }

  function validateCE() {
    if (!ce) {
      errors.ce = 'Please select Company/Enterprise type';
      return false;
    } else {
      errors.ce = '';
      return true;
    }
  }

  function validateBriefProfile() {
    if (!briefProfile.trim()) {
      errors.briefProfile = 'Brief profile is required';
      return false;
    } else if (briefProfile.length < 50) {
      errors.briefProfile = 'Please provide at least 50 characters describing your company';
      return false;
    } else {
      errors.briefProfile = '';
      return true;
    }
  }

  function validateWebsite() {
    if (website && !/^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/.test(website)) {
      errors.website = 'Please enter a valid website URL';
      return false;
    } else {
      errors.website = '';
      return true;
    }
  }

  function validateEmployeeCount() {
    if (!employeeCount) {
      errors.employeeCount = 'Please select employee count';
      return false;
    } else {
      errors.employeeCount = '';
      return true;
    }
  }

  function validateAddress() {
    if (!address.trim()) {
      errors.address = 'Company address is required';
      return false;
    } else if (address.length < 10) {
      errors.address = 'Please provide a complete address';
      return false;
    } else {
      errors.address = '';
      return true;
    }
  }

  function validateAll() {
    return (
      validateUserId() &&
      validateCompanyName() &&
      validateGSTN() &&
      validateLogo() &&
      validateYearEst() &&
      validateCompanyType() &&
      validateCE() &&
      validateBriefProfile() &&
      validateWebsite() &&
      validateEmployeeCount() &&
      validateAddress()
    );
  }

  function handleLogoUpload(event) {
    const file = event.target.files[0];
    if (file) {
      logoFile = file;
      const reader = new FileReader();
      reader.onload = (e) => {
        logoPreview = e.target.result;
      };
      reader.readAsDataURL(file);
      validateLogo();
    }
  }

  function handleDragOver(event) {
    event.preventDefault();
    isDragging = true;
  }

  function handleDragLeave(event) {
    event.preventDefault();
    isDragging = false;
  }

  function handleDrop(event) {
    event.preventDefault();
    isDragging = false;
    const file = event.dataTransfer.files[0];
    if (file && file.type.startsWith('image/')) {
      logoFile = file;
      const reader = new FileReader();
      reader.onload = (e) => {
        logoPreview = e.target.result;
      };
      reader.readAsDataURL(file);
      validateLogo();
    }
  }

  function removeLogo() {
    logoFile = null;
    logoPreview = '';
  }

  async function handleSubmit() {
    if (!validateAll()) {
      showToast('Please fix the errors above', 'error');
      return;
    }

    isLoading = true;

    await new Promise(resolve => setTimeout(resolve, 1500));

    const formData = {
      userId,
      companyName,
      gstn: gstn.toUpperCase(),
      logo: logoFile ? logoFile.name : null,
      yearEst,
      companyType,
      ce,
      briefProfile,
      website,
      employeeCount,
      address
    };
    
    console.log('Submitting company profile:', formData);

    isLoading = false;
    showSuccess = true;
    showToast('Company profile saved successfully!', 'success');
    
    setTimeout(() => {
      showSuccess = false;
    }, 3000);
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

  function nextTab() {
    if (activeTab === 'basic') activeTab = 'details';
    else if (activeTab === 'details') activeTab = 'profile';
  }

  function prevTab() {
    if (activeTab === 'profile') activeTab = 'details';
    else if (activeTab === 'details') activeTab = 'basic';
  }
</script>

<div class="company-profile-page">
  <!-- Background Pattern -->
  <div class="bg-pattern"></div>
  
  <div class="profile-container" in:fly={{ y: 20, duration: 500 }}>
    <!-- Header Section -->
    <div class="profile-header">
      <div class="header-left">
        <div class="brand-icon">
          <svg width="32" height="32" viewBox="0 0 32 32" fill="none">
            <path d="M16 2L4 9L16 16L28 9L16 2Z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M4 16L16 23L28 16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M4 23L16 30L28 23" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
          <span>HRV Portal</span>
        </div>
        <div class="header-text">
          <h1>Complete Your Company Profile</h1>
          <p>Help us know your business better to unlock premium features</p>
        </div>
      </div>
      <div class="progress-indicator">
        <div class="progress-steps">
          <div class="step {activeTab === 'basic' ? 'active' : ''} {activeTab !== 'basic' ? 'completed' : ''}">
            <span class="step-number">1</span>
            <span class="step-label">Basic Info</span>
          </div>
          <div class="step-line {activeTab !== 'basic' ? 'completed' : ''}"></div>
          <div class="step {activeTab === 'details' ? 'active' : ''} {activeTab === 'profile' ? 'completed' : ''}">
            <span class="step-number">2</span>
            <span class="step-label">Company Details</span>
          </div>
          <div class="step-line {activeTab === 'profile' ? 'completed' : ''}"></div>
          <div class="step {activeTab === 'profile' ? 'active' : ''}">
            <span class="step-number">3</span>
            <span class="step-label">Business Profile</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Success Banner -->
    {#if showSuccess}
      <div class="success-banner" transition:fly={{ y: -10, duration: 300 }}>
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/>
          <polyline points="22 4 12 14.01 9 11.01"/>
        </svg>
        <span>Your company profile has been successfully saved!</span>
      </div>
    {/if}

    <!-- Main Form Card -->
    <div class="form-card">
      <div class="form-content">
        {#if activeTab === 'basic'}
          <!-- Tab 1: Basic Information -->
          <div class="form-section">
            <div class="section-title">
              <h3>Basic Information</h3>
              <p>Essential details about your company identity</p>
            </div>

            <div class="form-grid">
              <!-- User ID -->
              <div class="input-group full-width">
                <label>User ID</label>
                <div class="input-wrapper">
                  <span class="input-icon">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                      <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" stroke="currentColor" stroke-width="1.5"/>
                      <circle cx="12" cy="7" r="4" stroke="currentColor" stroke-width="1.5"/>
                    </svg>
                  </span>
                  <input type="text" bind:value={userId} disabled placeholder="Linked to your account" />
                </div>
                {#if errors.userId}<span class="error-message">{errors.userId}</span>{/if}
              </div>

              <!-- Company Name & GSTN 2-col -->
              <div class="input-group">
                <label>Company Name <span class="required">*</span></label>
                <div class="input-wrapper {errors.companyName ? 'error' : ''}">
                  <span class="input-icon">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                      <path d="M3 9L12 3L21 9L12 15L3 9Z" stroke="currentColor" stroke-width="1.5"/>
                      <path d="M5 12v6h14v-6" stroke="currentColor" stroke-width="1.5"/>
                    </svg>
                  </span>
                  <input type="text" placeholder="Enter registered company name" bind:value={companyName} on:input={validateCompanyName} />
                </div>
                {#if errors.companyName}<span class="error-message">{errors.companyName}</span>{/if}
              </div>

              <div class="input-group">
                <label>GSTIN <span class="required">*</span></label>
                <div class="input-wrapper {errors.gstn ? 'error' : ''}">
                  <span class="input-icon">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                      <rect x="3" y="3" width="18" height="18" rx="2" stroke="currentColor" stroke-width="1.5"/>
                      <path d="M8 7h8M8 11h6M8 15h4" stroke="currentColor" stroke-width="1.5"/>
                    </svg>
                  </span>
                  <input type="text" placeholder="22AAAAA0000A1Z5" bind:value={gstn} on:input={validateGSTN} />
                </div>
                {#if errors.gstn}<span class="error-message">{errors.gstn}</span>{/if}
              </div>

              <!-- Logo Upload -->
              <div class="input-group full-width">
                <label>Company Logo <span class="required">*</span></label>
                <div 
                  class="logo-upload-area {errors.logo ? 'error' : ''} {isDragging ? 'dragging' : ''}"
                  on:dragover={handleDragOver}
                  on:dragleave={handleDragLeave}
                  on:drop={handleDrop}
                >
                  <input type="file" accept="image/jpeg,image/png,image/webp,image/svg+xml" on:change={handleLogoUpload} id="logo-upload" class="hidden-input" />
                  <label for="logo-upload" class="upload-label">
                    {#if logoPreview}
                      <div class="logo-preview">
                        <img src={logoPreview} alt="Company Logo" />
                        <button type="button" class="remove-logo" on:click|stopPropagation={removeLogo}>✕</button>
                      </div>
                    {:else}
                      <div class="upload-placeholder">
                        <div class="upload-icon">
                          <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                            <path d="M12 4v16M4 12h16" stroke="currentColor"/>
                          </svg>
                        </div>
                        <span>Click to upload or drag & drop</span>
                        <small>PNG, JPG, WEBP, SVG (Max 2MB)</small>
                      </div>
                    {/if}
                  </label>
                </div>
                {#if errors.logo}<span class="error-message">{errors.logo}</span>{/if}
              </div>

              <!-- Year & Type 2-col -->
              <div class="input-group">
                <label>Year of Establishment <span class="required">*</span></label>
                <div class="input-wrapper {errors.yearEst ? 'error' : ''}">
                  <span class="input-icon">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                      <rect x="3" y="4" width="18" height="18" rx="2" stroke="currentColor" stroke-width="1.5"/>
                      <path d="M8 2v4M16 2v4M3 10h18" stroke="currentColor" stroke-width="1.5"/>
                    </svg>
                  </span>
                  <input type="number" placeholder="e.g., 2010" bind:value={yearEst} on:input={validateYearEst} min="1900" max={new Date().getFullYear()} />
                </div>
                {#if errors.yearEst}<span class="error-message">{errors.yearEst}</span>{/if}
              </div>

              <div class="input-group">
                <label>Company Type <span class="required">*</span></label>
                <div class="select-wrapper {errors.companyType ? 'error' : ''}">
                  <select bind:value={companyType} on:change={validateCompanyType}>
                    <option value="" disabled>Select company type</option>
                    {#each companyTypes as type}
                      <option value={type}>{type}</option>
                    {/each}
                  </select>
                  <span class="select-arrow">▼</span>
                </div>
                {#if errors.companyType}<span class="error-message">{errors.companyType}</span>{/if}
              </div>
            </div>
          </div>
        {:else if activeTab === 'details'}
          <!-- Tab 2: Company Details -->
          <div class="form-section">
            <div class="section-title">
              <h3>Company Details</h3>
              <p>Additional information about your organization</p>
            </div>

            <div class="form-grid">
              <div class="input-group">
                <label>Company/Enterprise Type <span class="required">*</span></label>
                <div class="select-wrapper {errors.ce ? 'error' : ''}">
                  <select bind:value={ce} on:change={validateCE}>
                    <option value="" disabled>Select category</option>
                    {#each ceOptions as option}
                      <option value={option}>{option}</option>
                    {/each}
                  </select>
                  <span class="select-arrow">▼</span>
                </div>
                {#if errors.ce}<span class="error-message">{errors.ce}</span>{/if}
              </div>

              <div class="input-group">
                <label>Employee Count <span class="required">*</span></label>
                <div class="select-wrapper {errors.employeeCount ? 'error' : ''}">
                  <select bind:value={employeeCount} on:change={validateEmployeeCount}>
                    <option value="" disabled>Select employee range</option>
                    {#each employeeOptions as option}
                      <option value={option}>{option}</option>
                    {/each}
                  </select>
                  <span class="select-arrow">▼</span>
                </div>
                {#if errors.employeeCount}<span class="error-message">{errors.employeeCount}</span>{/if}
              </div>

              <div class="input-group full-width">
                <label>Website URL</label>
                <div class="input-wrapper {errors.website ? 'error' : ''}">
                  <span class="input-icon">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                      <circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="1.5"/>
                      <path d="M2 12h20M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z" stroke="currentColor" stroke-width="1.5"/>
                    </svg>
                  </span>
                  <input type="url" placeholder="https://www.yourcompany.com" bind:value={website} on:input={validateWebsite} />
                </div>
                {#if errors.website}<span class="error-message">{errors.website}</span>{/if}
              </div>

              <div class="input-group full-width">
                <label>Company Address <span class="required">*</span></label>
                <div class="textarea-wrapper {errors.address ? 'error' : ''}">
                  <textarea 
                    placeholder="Enter complete registered office address" 
                    bind:value={address}
                    on:input={validateAddress}
                    rows="3"
                  ></textarea>
                </div>
                {#if errors.address}<span class="error-message">{errors.address}</span>{/if}
              </div>
            </div>
          </div>
        {:else}
          <!-- Tab 3: Business Profile -->
          <div class="form-section">
            <div class="section-title">
              <h3>Business Profile</h3>
              <p>Tell us about your company's vision and mission</p>
            </div>

            <div class="form-grid">
              <div class="input-group full-width">
                <label>Brief Profile <span class="required">*</span></label>
                <div class="textarea-wrapper {errors.briefProfile ? 'error' : ''}">
                  <textarea 
                    placeholder="Describe your company's mission, products, services, and key achievements (min. 50 characters)" 
                    bind:value={briefProfile}
                    on:input={validateBriefProfile}
                    rows="6"
                  ></textarea>
                  <div class="char-counter">{briefProfile.length} / 50+ characters</div>
                </div>
                {#if errors.briefProfile}<span class="error-message">{errors.briefProfile}</span>{/if}
              </div>
            </div>

            <!-- Preview Card -->
            <div class="preview-card">
              <h4>Profile Preview</h4>
              <div class="preview-content">
                <div class="preview-logo">
                  {#if logoPreview}
                    <img src={logoPreview} alt="Logo" />
                  {:else}
                    <div class="preview-logo-placeholder">
                      <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                        <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/>
                      </svg>
                    </div>
                  {/if}
                </div>
                <div class="preview-info">
                  <strong>{companyName || 'Your Company Name'}</strong>
                  <span>{ce || 'Company Type'} • {employeeCount || 'Employee Count'}</span>
                  <span>{yearEst || 'Year'} • {companyType?.split(' ')[0] || 'Company Type'}</span>
                </div>
              </div>
            </div>
          </div>
        {/if}
      </div>

      <!-- Form Actions -->
      <div class="form-actions">
        {#if activeTab !== 'basic'}
          <button class="btn-secondary" on:click={prevTab}>
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M15 18l-6-6 6-6"/>
            </svg>
            Back
          </button>
        {/if}
        
        {#if activeTab !== 'profile'}
          <button class="btn-primary" on:click={nextTab}>
            Continue
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M9 18l6-6-6-6"/>
            </svg>
          </button>
        {:else}
          <button class="btn-primary" on:click={handleSubmit} disabled={isLoading}>
            {#if isLoading}
              <span class="spinner"></span>
            {/if}
            {isLoading ? 'Saving Profile...' : 'Complete Registration →'}
          </button>
        {/if}
      </div>

      <!-- Security Note -->
      <div class="security-note">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/>
          <path d="M12 8v4M12 16h.01"/>
        </svg>
        <span>Your information is encrypted and secure. We never share your data with third parties.</span>
      </div>
    </div>
  </div>
</div>

<style>
  /* Professional Premium Styling */
  .company-profile-page {
    min-height: 100vh;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    position: relative;
    padding: 40px 24px;
    padding-top: 95px;
  }

  .bg-pattern {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-image: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" opacity="0.05"><path fill="white" d="M20,30 L30,20 L40,30 L30,40 Z M60,70 L70,60 L80,70 L70,80 Z M50,50 L55,45 L60,50 L55,55 Z"/></svg>');
    background-repeat: repeat;
    pointer-events: none;
  }

  .profile-container {
    max-width: 900px;
    margin: 0 auto;
    position: relative;
    z-index: 1;
  }

  /* Header */
  .profile-header {
    margin-bottom: 32px;
  }

  .header-left {
    display: flex;
    align-items: center;
    gap: 20px;
    margin-bottom: 28px;
    flex-wrap: wrap;
  }

  .brand-icon {
    display: flex;
    align-items: center;
    gap: 8px;
    background: rgba(255, 255, 255, 0.15);
    backdrop-filter: blur(10px);
    padding: 10px 20px;
    border-radius: 100px;
    color: white;
    font-weight: 600;
  }

  .header-text h1 {
    color: white;
    font-size: 28px;
    font-weight: 700;
    margin: 0 0 6px 0;
  }

  .header-text p {
    color: rgba(255, 255, 255, 0.8);
    margin: 0;
    font-size: 14px;
  }

  /* Progress Steps */
  .progress-indicator {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border-radius: 60px;
    padding: 12px 24px;
  }

  .progress-steps {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .step {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .step-number {
    width: 32px;
    height: 32px;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-weight: 600;
    font-size: 14px;
    transition: all 0.3s;
  }

  .step.active .step-number {
    background: white;
    color: #667eea;
    box-shadow: 0 0 0 4px rgba(255, 255, 255, 0.3);
  }

  .step.completed .step-number {
    background: #10b981;
    color: white;
  }

  .step.completed .step-number::after {
    content: "✓";
    font-size: 12px;
  }

  .step.completed .step-number span {
    display: none;
  }

  .step-label {
    color: white;
    font-size: 14px;
    font-weight: 500;
  }

  .step-line {
    flex: 1;
    height: 2px;
    background: rgba(255, 255, 255, 0.2);
    margin: 0 16px;
    transition: all 0.3s;
  }

  .step-line.completed {
    background: #10b981;
  }

  /* Form Card */
  .form-card {
    background: white;
    border-radius: 32px;
    padding: 40px;
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  }

  /* Success Banner */
  .success-banner {
    background: #10b981;
    color: white;
    padding: 14px 20px;
    border-radius: 16px;
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 24px;
    font-size: 14px;
    font-weight: 500;
  }

  /* Section Title */
  .section-title {
    margin-bottom: 32px;
    padding-bottom: 16px;
    border-bottom: 2px solid #f0f2f5;
  }

  .section-title h3 {
    font-size: 22px;
    font-weight: 600;
    color: #1a1a2e;
    margin: 0 0 6px 0;
  }

  .section-title p {
    color: #666;
    font-size: 14px;
    margin: 0;
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
    margin-left: 2px;
  }

  .input-wrapper, .select-wrapper, .textarea-wrapper {
    position: relative;
    width: 100%;
  }

  .input-icon {
    position: absolute;
    left: 14px;
    top: 50%;
    transform: translateY(-50%);
    display: flex;
    align-items: center;
    color: #94a3b8;
    pointer-events: none;
  }

  .input-wrapper input, .textarea-wrapper textarea, .select-wrapper select {
    width: 100%;
    padding: 12px 16px 12px 42px;
    border: 1.5px solid #e2e8f0;
    border-radius: 14px;
    font-size: 14px;
    transition: all 0.2s ease;
    background: white;
    font-family: inherit;
  }

  .textarea-wrapper textarea {
    padding: 12px 16px;
    resize: vertical;
  }

  .select-wrapper select {
    padding: 12px 16px;
    appearance: none;
    cursor: pointer;
  }

  .select-arrow {
    position: absolute;
    right: 16px;
    top: 50%;
    transform: translateY(-50%);
    color: #94a3b8;
    pointer-events: none;
    font-size: 10px;
  }

  .input-wrapper input:focus, .textarea-wrapper textarea:focus, .select-wrapper select:focus {
    outline: none;
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
  }

  .input-wrapper.error input, .select-wrapper.error select, .textarea-wrapper.error textarea {
    border-color: #ef4444;
  }

  .input-wrapper input:disabled {
    background: #f8fafc;
    color: #64748b;
    cursor: not-allowed;
  }

  .char-counter {
    text-align: right;
    font-size: 11px;
    color: #94a3b8;
    margin-top: 6px;
  }

  .error-message {
    display: block;
    font-size: 11px;
    color: #ef4444;
    margin-top: 6px;
  }

  /* Logo Upload */
  .logo-upload-area {
    border: 2px dashed #e2e8f0;
    border-radius: 20px;
    transition: all 0.2s;
    background: #fafcff;
    cursor: pointer;
    overflow: hidden;
  }

  .logo-upload-area.error {
    border-color: #ef4444;
  }

  .logo-upload-area.dragging {
    border-color: #667eea;
    background: #f0f4fe;
  }

  .hidden-input {
    display: none;
  }

  .upload-label {
    display: block;
    width: 100%;
    cursor: pointer;
  }

  .logo-preview {
    position: relative;
    width: 100%;
    height: 160px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #f8fafc;
  }

  .logo-preview img {
    max-width: 100%;
    max-height: 100%;
    object-fit: contain;
  }

  .remove-logo {
    position: absolute;
    top: 12px;
    right: 12px;
    background: white;
    border: 1px solid #e2e8f0;
    border-radius: 50%;
    width: 32px;
    height: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    color: #64748b;
    transition: all 0.2s;
    font-size: 16px;
  }

  .remove-logo:hover {
    background: #fee2e2;
    color: #ef4444;
    border-color: #ef4444;
  }

  .upload-placeholder {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
    color: #94a3b8;
  }

  .upload-icon {
    width: 64px;
    height: 64px;
    background: #f1f5f9;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 16px;
  }

  .upload-placeholder span {
    font-size: 14px;
    font-weight: 500;
    color: #334155;
  }

  .upload-placeholder small {
    font-size: 11px;
    margin-top: 6px;
  }

  /* Preview Card */
  .preview-card {
    margin-top: 32px;
    padding: 20px;
    background: #f8fafc;
    border-radius: 20px;
    border: 1px solid #e2e8f0;
  }

  .preview-card h4 {
    font-size: 14px;
    font-weight: 600;
    color: #64748b;
    margin: 0 0 16px 0;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .preview-content {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .preview-logo img {
    width: 56px;
    height: 56px;
    object-fit: contain;
    border-radius: 12px;
  }

  .preview-logo-placeholder {
    width: 56px;
    height: 56px;
    background: white;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #94a3b8;
  }

  .preview-info {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .preview-info strong {
    font-size: 16px;
    color: #1a1a2e;
  }

  .preview-info span {
    font-size: 12px;
    color: #64748b;
  }

  /* Form Actions */
  .form-actions {
    display: flex;
    justify-content: space-between;
    gap: 16px;
    margin-top: 40px;
    padding-top: 24px;
    border-top: 1px solid #e2e8f0;
  }

  .btn-primary, .btn-secondary {
    padding: 14px 28px;
    border-radius: 14px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    gap: 8px;
    border: none;
  }

  .btn-primary {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    flex: 1;
    justify-content: center;
  }

  .btn-primary:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 10px 25px -5px rgba(102, 126, 234, 0.4);
  }

  .btn-primary:disabled {
    opacity: 0.7;
    cursor: not-allowed;
  }

  .btn-secondary {
    background: #f1f5f9;
    color: #475569;
  }

  .btn-secondary:hover {
    background: #e2e8f0;
  }

  .spinner {
    width: 18px;
    height: 18px;
    border: 2px solid rgba(255, 255, 255, 0.3);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.6s linear infinite;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* Security Note */
  .security-note {
    margin-top: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    font-size: 12px;
    color: #94a3b8;
    text-align: center;
  }

  /* Toast */
  :global(.toast) {
    position: fixed;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%) translateY(100px);
    padding: 14px 28px;
    border-radius: 60px;
    font-size: 14px;
    font-weight: 500;
    z-index: 1000;
    transition: transform 0.3s ease;
    box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.2);
  }

  :global(.toast.show) {
    transform: translateX(-50%) translateY(0);
  }

  :global(.toast-success) {
    background: #10b981;
    color: white;
  }

  :global(.toast-error) {
    background: #ef4444;
    color: white;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .company-profile-page {
      padding: 20px 16px;
    }

    .form-card {
      padding: 28px 20px;
    }

    .form-grid {
      grid-template-columns: 1fr;
      gap: 20px;
    }

    .full-width {
      grid-column: span 1;
    }

    .progress-steps {
      flex-direction: column;
      gap: 12px;
    }

    .step-line {
      display: none;
    }

    .step {
      width: 100%;
      justify-content: space-between;
    }

    .step-label {
      font-size: 13px;
    }

    .progress-indicator {
      border-radius: 24px;
    }

    .header-left {
      flex-direction: column;
      text-align: center;
      gap: 16px;
    }

    .header-text h1 {
      font-size: 24px;
    }

    .form-actions {
      flex-direction: column;
    }

    .btn-primary, .btn-secondary {
      width: 100%;
      justify-content: center;
    }

    .section-title h3 {
      font-size: 20px;
    }

    .upload-placeholder {
      padding: 30px 16px;
    }

    .upload-icon {
      width: 48px;
      height: 48px;
    }

    .upload-icon svg {
      width: 24px;
      height: 24px;
    }

    .preview-content {
      flex-direction: column;
      text-align: center;
    }

    .security-note {
      flex-direction: column;
      text-align: center;
      line-height: 1.5;
    }
  }

  @media (max-width: 480px) {
    .form-card {
      padding: 20px 16px;
      border-radius: 24px;
    }

    .header-text h1 {
      font-size: 20px;
    }

    .section-title h3 {
      font-size: 18px;
    }

    .brand-icon {
      padding: 6px 14px;
      font-size: 13px;
    }

    .brand-icon svg {
      width: 24px;
      height: 24px;
    }

    .step-number {
      width: 28px;
      height: 28px;
      font-size: 12px;
    }

    .step-label {
      font-size: 12px;
    }
  }
</style>