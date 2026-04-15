<!-- src/routes/change-password/+page.svelte -->
<script>
  import { fly, fade } from 'svelte/transition';

  let currentPassword = '';
  let newPassword = '';
  let confirmPassword = '';

  let isLoading = false;
  let successMessage = '';
  let errorMessage = '';

  let showCurrentPassword = false;
  let showNewPassword = false;
  let showConfirmPassword = false;

  let errors = {
    current: '',
    new: '',
    confirm: ''
  };

  // Password strength calculation
  function computeStrength(pwd) {
    return {
      length: pwd.length >= 8,
      uppercase: /[A-Z]/.test(pwd),
      lowercase: /[a-z]/.test(pwd),
      number: /[0-9]/.test(pwd),
      special: /[^A-Za-z0-9]/.test(pwd)
    };
  }

  function getStrength() {
    return computeStrength(newPassword);
  }

  function getStrengthScore() {
    return Object.values(getStrength()).filter(Boolean).length;
  }

  function getStrengthInfo() {
    const score = getStrengthScore();
    const labels = ['Very Weak', 'Weak', 'Fair', 'Good', 'Strong'];
    const colors = ['#ef4444', '#f97316', '#eab308', '#22c55e', '#10b981'];
    const gradients = [
      'linear-gradient(135deg, #ef4444, #dc2626)',
      'linear-gradient(135deg, #f97316, #ea580c)',
      'linear-gradient(135deg, #eab308, #ca8a04)',
      'linear-gradient(135deg, #22c55e, #16a34a)',
      'linear-gradient(135deg, #10b981, #059669)'
    ];
    return {
      label: labels[score],
      color: colors[score],
      gradient: gradients[score],
      width: (score / 5) * 100
    };
  }

  function validateCurrent() {
    if (!currentPassword) {
      errors.current = 'Current password is required';
      return false;
    }
    if (currentPassword.length < 6) {
      errors.current = 'Password must be at least 6 characters';
      return false;
    }
    errors.current = '';
    return true;
  }

  function validateNew() {
    const s = getStrength();

    if (!newPassword) {
      errors.new = 'New password is required';
      return false;
    }
    if (!s.length) {
      errors.new = 'Password must be at least 8 characters';
      return false;
    }
    if (!s.uppercase) {
      errors.new = 'Include at least one uppercase letter';
      return false;
    }
    if (!s.lowercase) {
      errors.new = 'Include at least one lowercase letter';
      return false;
    }
    if (!s.number) {
      errors.new = 'Include at least one number';
      return false;
    }
    if (!s.special) {
      errors.new = 'Include at least one special character';
      return false;
    }

    errors.new = '';
    return true;
  }

  function validateConfirm() {
    if (!confirmPassword) {
      errors.confirm = 'Please confirm your new password';
      return false;
    }
    if (confirmPassword !== newPassword) {
      errors.confirm = 'Passwords do not match';
      return false;
    }
    errors.confirm = '';
    return true;
  }

  async function handleSubmit() {
    if (!validateCurrent() || !validateNew() || !validateConfirm()) {
      errorMessage = 'Please fix the errors above';
      setTimeout(() => errorMessage = '', 3000);
      return;
    }

    isLoading = true;
    errorMessage = '';

    try {
      await new Promise(r => setTimeout(r, 1800));
      
      console.log('Password changed successfully');
      
      successMessage = 'Password changed successfully!';
      currentPassword = '';
      newPassword = '';
      confirmPassword = '';
      
      setTimeout(() => successMessage = '', 4000);
    } catch {
      errorMessage = 'Something went wrong. Please try again.';
      setTimeout(() => errorMessage = '', 3000);
    } finally {
      isLoading = false;
    }
  }
</script>

<div class="change-password-page">
  <!-- Animated Background -->
  <div class="bg-animation">
    <div class="bg-gradient-1"></div>
    <div class="bg-gradient-2"></div>
    <div class="bg-gradient-3"></div>
    <div class="bg-grid"></div>
  </div>

  <div class="container" in:fly={{ y: 30, duration: 600 }}>
    <div class="card">
      <!-- Decorative Top Bar -->
      <div class="card-accent"></div>
      
      <div class="card-header">
        <div class="icon-wrapper">
          <div class="icon-circle">
            <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
              <rect x="3" y="11" width="18" height="11" rx="2" ry="2"/>
              <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
            </svg>
          </div>
        </div>
        <h1>Change Password</h1>
        <p>Update your password to keep your account secure</p>
      </div>

      <!-- Success Alert -->
      {#if successMessage}
        <div class="alert alert-success" in:fly={{ y: -20, duration: 300 }} out:fade>
          <div class="alert-icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <circle cx="12" cy="12" r="10"/>
              <path d="M8 12l3 3 6-6"/>
            </svg>
          </div>
          <span>{successMessage}</span>
        </div>
      {/if}

      <!-- Error Alert -->
      {#if errorMessage}
        <div class="alert alert-error" in:fly={{ y: -20, duration: 300 }} out:fade>
          <div class="alert-icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <circle cx="12" cy="12" r="10"/>
              <line x1="12" y1="8" x2="12" y2="12"/>
              <line x1="12" y1="16" x2="12.01" y2="16"/>
            </svg>
          </div>
          <span>{errorMessage}</span>
        </div>
      {/if}

      <div class="form">
        <!-- Current Password -->
        <div class="form-group">
          <label for="current-password">Current Password</label>
          <div class="input-field {errors.current ? 'error' : ''}">
            <span class="field-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                <rect x="3" y="11" width="18" height="11" rx="2" ry="2"/>
                <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
              </svg>
            </span>
            <input
              type={showCurrentPassword ? 'text' : 'password'}
              bind:value={currentPassword}
              on:input={validateCurrent}
              id="current-password"
              placeholder="Enter your current password"
            />
            <button
              type="button"
              class="toggle-visibility"
              on:click={() => showCurrentPassword = !showCurrentPassword}
              aria-label={showCurrentPassword ? 'Hide password' : 'Show password'}
            >
              {#if showCurrentPassword}
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
                  <circle cx="12" cy="12" r="3"/>
                </svg>
              {:else}
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"/>
                  <line x1="1" y1="1" x2="23" y2="23"/>
                </svg>
              {/if}
            </button>
          </div>
          {#if errors.current}<span class="error-text">{errors.current}</span>{/if}
        </div>

        <!-- New Password -->
        <div class="form-group">
          <label for="new-password">New Password</label>
          <div class="input-field {errors.new ? 'error' : ''}">
            <span class="field-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                <path d="M12 2v4M12 18v4M4.93 4.93l2.83 2.83M16.24 16.24l2.83 2.83M2 12h4M18 12h4M4.93 19.07l2.83-2.83M16.24 7.76l2.83-2.83"/>
                <circle cx="12" cy="12" r="3"/>
              </svg>
            </span>
            <input
              type={showNewPassword ? 'text' : 'password'}
              bind:value={newPassword}
              on:input={validateNew}
              id="new-password"
              placeholder="Create a new password"
            />
            <button
              type="button"
              class="toggle-visibility"
              on:click={() => showNewPassword = !showNewPassword}
              aria-label={showNewPassword ? 'Hide password' : 'Show password'}
            >
              {#if showNewPassword}
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
                  <circle cx="12" cy="12" r="3"/>
                </svg>
              {:else}
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"/>
                  <line x1="1" y1="1" x2="23" y2="23"/>
                </svg>
              {/if}
            </button>
          </div>
          {#if errors.new}<span class="error-text">{errors.new}</span>{/if}

          <!-- Password Strength Meter -->
          {#if newPassword}
            <div class="strength-section">
              <div class="strength-header">
                <span class="strength-label">Password Strength</span>
                <span class="strength-value" style="color: {getStrengthInfo().color};">{getStrengthInfo().label}</span>
              </div>
              <div class="strength-meter">
                <div class="strength-bar" style="width: {getStrengthInfo().width}%; background: {getStrengthInfo().gradient};"></div>
              </div>
              <div class="criteria-grid">
                <div class="criteria {getStrength().length ? 'met' : ''}">
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <span>8+ characters</span>
                </div>
                <div class="criteria {getStrength().uppercase ? 'met' : ''}">
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <span>Uppercase</span>
                </div>
                <div class="criteria {getStrength().lowercase ? 'met' : ''}">
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <span>Lowercase</span>
                </div>
                <div class="criteria {getStrength().number ? 'met' : ''}">
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <span>Number</span>
                </div>
                <div class="criteria {getStrength().special ? 'met' : ''}">
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <span>Special char</span>
                </div>
              </div>
            </div>
          {/if}
        </div>

        <!-- Confirm Password -->
        <div class="form-group">
          <label for="confirm-password">Confirm New Password</label>
          <div class="input-field {errors.confirm ? 'error' : ''}">
            <span class="field-icon">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                <rect x="3" y="11" width="18" height="11" rx="2" ry="2"/>
                <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
                <line x1="12" y1="15" x2="12" y2="17"/>
              </svg>
            </span>
            <input
              type={showConfirmPassword ? 'text' : 'password'}
              bind:value={confirmPassword}
              on:input={validateConfirm}
              id="confirm-password"
              placeholder="Confirm your new password"
            />
            <button
              type="button"
              class="toggle-visibility"
              on:click={() => showConfirmPassword = !showConfirmPassword}
              aria-label={showConfirmPassword ? 'Hide password' : 'Show password'}
            >
              {#if showConfirmPassword}
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
                  <circle cx="12" cy="12" r="3"/>
                </svg>
              {:else}
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"/>
                  <line x1="1" y1="1" x2="23" y2="23"/>
                </svg>
              {/if}
            </button>
          </div>
          {#if errors.confirm}<span class="error-text">{errors.confirm}</span>{/if}
          {#if newPassword && confirmPassword && newPassword === confirmPassword && !errors.confirm && newPassword !== ''}
            <span class="success-text">
              <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3">
                <polyline points="20 6 9 17 4 12"/>
              </svg>
              Passwords match
            </span>
          {/if}
        </div>
      </div>

      <button class="submit-button" on:click={handleSubmit} disabled={isLoading}>
        {#if isLoading}
          <span class="spinner"></span>
          <span>Updating Password...</span>
        {:else}
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
            <circle cx="12" cy="12" r="3"/>
          </svg>
          <span>Update Password</span>
        {/if}
      </button>

      <div class="security-tips">
        <div class="tip-header">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/>
            <path d="M12 8v4M12 16h.01"/>
          </svg>
          <span>Password Security Tips</span>
        </div>
        <div class="tip-list">
          <span>• Use at least 8 characters</span>
          <span>• Mix uppercase & lowercase letters</span>
          <span>• Include numbers and symbols</span>
          <span>• Avoid common words or personal info</span>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  .change-password-page {
    min-height: 100vh;
    background: linear-gradient(145deg, #f1f5f9 0%, #e2e8f0 100%);
    position: relative;
    overflow-x: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 95px;
  }

  /* Animated Background */
  .bg-animation {
    position: fixed;
    inset: 0;
    overflow: hidden;
    z-index: 0;
  }

  .bg-gradient-1 {
    position: absolute;
    top: -30%;
    right: -20%;
    width: 600px;
    height: 600px;
    background: radial-gradient(circle, rgba(37, 99, 235, 0.12) 0%, transparent 70%);
    border-radius: 50%;
    animation: float1 25s ease-in-out infinite;
  }

  .bg-gradient-2 {
    position: absolute;
    bottom: -30%;
    left: -20%;
    width: 500px;
    height: 500px;
    background: radial-gradient(circle, rgba(139, 92, 246, 0.08) 0%, transparent 70%);
    border-radius: 50%;
    animation: float2 20s ease-in-out infinite;
  }

  .bg-gradient-3 {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 400px;
    height: 400px;
    background: radial-gradient(circle, rgba(236, 72, 153, 0.05) 0%, transparent 70%);
    border-radius: 50%;
    animation: pulse 8s ease-in-out infinite;
  }

  .bg-grid {
    position: absolute;
    inset: 0;
    background-image: 
      linear-gradient(rgba(0, 0, 0, 0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0, 0, 0, 0.03) 1px, transparent 1px);
    background-size: 40px 40px;
  }

  @keyframes float1 {
    0%, 100% { transform: translate(0, 0) rotate(0deg); }
    33% { transform: translate(40px, -30px) rotate(5deg); }
    66% { transform: translate(-30px, 20px) rotate(-3deg); }
  }

  @keyframes float2 {
    0%, 100% { transform: translate(0, 0) rotate(0deg); }
    33% { transform: translate(-40px, 30px) rotate(-5deg); }
    66% { transform: translate(30px, -20px) rotate(3deg); }
  }

  @keyframes pulse {
    0%, 100% { transform: translate(-50%, -50%) scale(1); opacity: 0.5; }
    50% { transform: translate(-50%, -50%) scale(1.2); opacity: 0.3; }
  }

  .container {
    position: relative;
    z-index: 1;
    width: 100%;
    max-width: 520px;
    margin: 0 auto;
    padding: 24px;
  }

  /* Card */
  .card {
    background: white;
    border-radius: 48px;
    padding: 40px;
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
    transition: all 0.3s ease;
    position: relative;
    overflow: hidden;
  }

  .card:hover {
    transform: translateY(-3px);
    box-shadow: 0 30px 60px -15px rgba(0, 0, 0, 0.3);
  }

  .card-accent {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 4px;
    background: linear-gradient(90deg, #2563eb, #7c3aed, #ec4899);
  }

  .card-header {
    text-align: center;
    margin-bottom: 32px;
  }

  .icon-wrapper {
    margin-bottom: 20px;
  }

  .icon-circle {
    width: 72px;
    height: 72px;
    background: linear-gradient(135deg, #2563eb, #1e40af);
    border-radius: 28px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    color: white;
    box-shadow: 0 12px 24px -8px rgba(37, 99, 235, 0.4);
    transition: transform 0.3s ease;
  }

  .card:hover .icon-circle {
    transform: scale(1.05);
  }

  .card-header h1 {
    font-size: 32px;
    font-weight: 700;
    background: linear-gradient(135deg, #0f172a, #1e293b);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    margin-bottom: 10px;
    letter-spacing: -0.02em;
  }

  .card-header p {
    color: #64748b;
    font-size: 15px;
    font-weight: 500;
  }

  /* Alerts */
  .alert {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 14px 20px;
    border-radius: 24px;
    margin-bottom: 28px;
    font-size: 14px;
    font-weight: 500;
    animation: slideDown 0.3s ease;
  }

  @keyframes slideDown {
    from {
      opacity: 0;
      transform: translateY(-10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .alert-success {
    background: linear-gradient(135deg, #ecfdf5, #d1fae5);
    color: #059669;
    border-left: 4px solid #10b981;
  }

  .alert-error {
    background: linear-gradient(135deg, #fff7ed, #ffedd5);
    color: #ea580c;
    border-left: 4px solid #f97316;
  }

  .alert-icon {
    display: flex;
    align-items: center;
  }

  /* Form */
  .form {
    display: flex;
    flex-direction: column;
    gap: 24px;
  }

  .form-group {
    width: 100%;
  }

  .form-group label {
    display: block;
    font-size: 13px;
    font-weight: 600;
    color: #334155;
    margin-bottom: 8px;
  }

  .input-field {
    position: relative;
    display: flex;
    align-items: center;
  }

  .input-field .field-icon {
    position: absolute;
    left: 16px;
    color: #94a3b8;
    pointer-events: none;
    transition: color 0.2s;
  }

  .input-field input:focus ~ .field-icon {
    color: #2563eb;
  }

  .input-field input {
    width: 100%;
    padding: 15px 48px 15px 46px;
    border: 2px solid #e2e8f0;
    border-radius: 24px;
    font-size: 14px;
    transition: all 0.2s ease;
    background: white;
    font-family: inherit;
    font-weight: 500;
  }

  .input-field input:focus {
    outline: none;
    border-color: #2563eb;
    box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.1);
  }

  .input-field.error input {
    border-color: #f97316;
    background: #fff7ed;
  }

  .toggle-visibility {
    position: absolute;
    right: 16px;
    background: none;
    border: none;
    cursor: pointer;
    color: #94a3b8;
    display: flex;
    align-items: center;
    padding: 0;
    transition: color 0.2s;
  }

  .toggle-visibility:hover {
    color: #2563eb;
  }

  .error-text {
    display: block;
    font-size: 11px;
    color: #f97316;
    margin-top: 6px;
    margin-left: 16px;
    font-weight: 500;
  }

  .success-text {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    color: #10b981;
    margin-top: 6px;
    margin-left: 16px;
    font-weight: 500;
  }

  /* Strength Meter */
  .strength-section {
    margin-top: 16px;
    padding: 16px;
    background: #f8fafc;
    border-radius: 20px;
    border: 1px solid #e2e8f0;
  }

  .strength-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }

  .strength-label {
    font-size: 12px;
    font-weight: 600;
    color: #64748b;
  }

  .strength-value {
    font-size: 12px;
    font-weight: 700;
  }

  .strength-meter {
    height: 6px;
    background: #e2e8f0;
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 14px;
  }

  .strength-bar {
    height: 100%;
    width: 0%;
    transition: width 0.4s cubic-bezier(0.4, 0, 0.2, 1);
    border-radius: 6px;
  }

  .criteria-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
  }

  .criteria {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    font-weight: 500;
    color: #94a3b8;
    transition: color 0.2s;
    padding: 4px 8px;
    background: white;
    border-radius: 20px;
  }

  .criteria svg {
    stroke: currentColor;
  }

  .criteria.met {
    color: #10b981;
    background: #ecfdf5;
  }

  /* Submit Button */
  .submit-button {
    width: 100%;
    padding: 16px 24px;
    background: linear-gradient(135deg, #2563eb, #1e40af);
    color: white;
    border: none;
    border-radius: 60px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
    margin-top: 16px;
    box-shadow: 0 8px 20px -8px rgba(37, 99, 235, 0.5);
    position: relative;
    overflow: hidden;
  }

  .submit-button::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
    transition: left 0.5s;
  }

  .submit-button:hover:not(:disabled)::before {
    left: 100%;
  }

  .submit-button:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 12px 28px -8px rgba(37, 99, 235, 0.6);
  }

  .submit-button:active:not(:disabled) {
    transform: translateY(0);
  }

  .submit-button:disabled {
    opacity: 0.7;
    cursor: not-allowed;
    transform: none;
  }

  .spinner {
    width: 20px;
    height: 20px;
    border: 2px solid rgba(255, 255, 255, 0.3);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.6s linear infinite;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* Security Tips */
  .security-tips {
    margin-top: 28px;
    padding: 16px 20px;
    background: linear-gradient(135deg, #f8fafc, #f1f5f9);
    border-radius: 24px;
    border: 1px solid #e2e8f0;
  }

  .tip-header {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 12px;
    color: #475569;
    font-size: 13px;
    font-weight: 600;
  }

  .tip-list {
    display: flex;
    flex-wrap: wrap;
    gap: 8px 16px;
    font-size: 11px;
    color: #64748b;
    line-height: 1.5;
  }

  .tip-list span {
    display: inline-flex;
    align-items: center;
  }

  /* Responsive Design */
  @media (max-width: 640px) {
    .container {
      padding: 16px;
    }

    .card {
      padding: 28px 24px;
      border-radius: 36px;
    }

    .card-header h1 {
      font-size: 26px;
    }

    .icon-circle {
      width: 60px;
      height: 60px;
      border-radius: 22px;
    }

    .icon-circle svg {
      width: 28px;
      height: 28px;
    }

    .input-field input {
      padding: 13px 44px 13px 42px;
      font-size: 14px;
    }

    .criteria-grid {
      gap: 8px;
    }

    .criteria {
      font-size: 10px;
      padding: 3px 8px;
    }
  }

  @media (max-width: 480px) {
    .card {
      padding: 24px 20px;
      border-radius: 32px;
    }

    .card-header h1 {
      font-size: 24px;
    }

    .card-header p {
      font-size: 13px;
    }

    .input-field input {
      padding: 12px 40px 12px 38px;
      font-size: 13px;
    }

    .field-icon svg,
    .toggle-visibility svg {
      width: 16px;
      height: 16px;
    }

    .field-icon {
      left: 14px;
    }

    .toggle-visibility {
      right: 14px;
    }

    .submit-button {
      padding: 14px 20px;
      font-size: 15px;
    }

    .strength-section {
      padding: 12px;
    }

    .criteria-grid {
      gap: 6px;
    }

    .criteria {
      font-size: 9px;
      padding: 2px 6px;
    }

    .security-tips {
      padding: 14px 16px;
    }

    .tip-header {
      font-size: 12px;
    }

    .tip-list {
      font-size: 10px;
      gap: 6px 12px;
    }
  }

  @media (min-width: 1200px) {
    .container {
      max-width: 560px;
    }
  }
</style>