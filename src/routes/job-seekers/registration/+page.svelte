<script>
  // --- Reactive state ---
  let form = {
    name: "",
    email: "",
    phone: "",
    password: "",
    gender: ""
  };

  let selectedFile = null;        // stores the File object
  let fileError = "";
  let showPassword = false;

  // For drag & drop styling
  let dragActive = false;

  // Refs for file input (to reset value when needed)
  let fileInputRef;

  // --- Validation errors ---
  let errors = {
    gender: "",
    name: "",
    phone: "",
    email: "",
    password: ""
  };

  // --- Helper: clear all errors ---
  function clearErrors() {
    errors = { gender: "", name: "", phone: "", email: "", password: "" };
    fileError = "";
  }

  // --- Validation logic ---
  function validateForm() {
    clearErrors();
    let isValid = true;

    // Gender
    if (!form.gender) {
      errors.gender = "Please select gender";
      isValid = false;
    }

    // Full name
    if (form.name.trim().length < 2) {
      errors.name = "Enter valid full name (min 2 chars)";
      isValid = false;
    } else if (!/^[a-zA-Z\s\-'.]+$/.test(form.name.trim())) {
      errors.name = "Name should contain only letters";
      isValid = false;
    }

    // Mobile number
    const mobileVal = form.phone.trim();
    const mobileRegex = /^[\+\d\s\-\(\)]{8,20}$/;
    if (!mobileVal) {
      errors.phone = "Mobile number is required";
      isValid = false;
    } else if (!mobileRegex.test(mobileVal)) {
      errors.phone = "Enter a valid phone number";
      isValid = false;
    } else if (!/\d{8,}/.test(mobileVal.replace(/\D/g, ""))) {
      errors.phone = "At least 8 digits required";
      isValid = false;
    }

    // Email
    const emailVal = form.email.trim();
    const emailRegex = /^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/;
    if (!emailVal) {
      errors.email = "Email address is required";
      isValid = false;
    } else if (!emailRegex.test(emailVal)) {
      errors.email = "Enter a valid email (e.g., name@domain.com)";
      isValid = false;
    }

    // Password
    if (!form.password) {
      errors.password = "Password is required";
      isValid = false;
    } else if (form.password.length < 6) {
      errors.password = "Password must be at least 6 characters";
      isValid = false;
    }

    // Resume file (optional but validated if present)
    if (selectedFile) {
      const allowedTypes = [
        "application/pdf",
        "application/msword",
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
      ];
      const maxSize = 2 * 1024 * 1024; // 2MB
      if (!allowedTypes.includes(selectedFile.type)) {
        fileError = "❌ Only PDF, DOC, DOCX files are allowed.";
        isValid = false;
      } else if (selectedFile.size > maxSize) {
        fileError = "❌ File size exceeds 2MB limit.";
        isValid = false;
      }
    } else {
      // Optional: show a soft warning (not blocking)
      fileError = "⚠️ Resume recommended (optional)";
    }

    return isValid;
  }

  // --- Build final object & submit ---
  function submitForm() {
    if (validateForm()) {
      const payload = {
        ...form,
        resume: selectedFile
          ? {
              name: selectedFile.name,
              size: (selectedFile.size / 1024).toFixed(2) + " KB",
              type: selectedFile.type,
            }
          : "No file uploaded",
      };
      alert("✅ Registered Successfully 🚀\nWelcome to JobPortal!");
      console.log("Registration Data:", payload);
      // Optional: reset form after success
      // form = { name: "", email: "", phone: "", password: "", gender: "" };
      // selectedFile = null;
      // if (fileInputRef) fileInputRef.value = "";
    } else {
      // Scroll to first error
      const firstError = document.querySelector(".error-text:not(:empty)");
      if (firstError) firstError.scrollIntoView({ behavior: "smooth", block: "center" });
    }
  }

  // --- File handling ---
  function handleFileSelect(event) {
    const file = event.target.files[0];
    processFile(file);
  }

  function processFile(file) {
    if (!file) {
      selectedFile = null;
      fileError = "";
      return;
    }
    const allowedTypes = [
      "application/pdf",
      "application/msword",
      "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
    ];
    if (!allowedTypes.includes(file.type)) {
      fileError = "❌ Only PDF, DOC, DOCX files are allowed.";
      selectedFile = null;
      if (fileInputRef) fileInputRef.value = "";
      return;
    }
    if (file.size > 2 * 1024 * 1024) {
      fileError = "❌ File size exceeds 2MB limit.";
      selectedFile = null;
      if (fileInputRef) fileInputRef.value = "";
      return;
    }
    fileError = "";
    selectedFile = file;
  }

  function removeFile() {
    selectedFile = null;
    fileError = "";
    if (fileInputRef) fileInputRef.value = "";
  }

  // --- Drag & drop handlers ---
  function onDragOver(event) {
    event.preventDefault();
    dragActive = true;
  }
  function onDragLeave(event) {
    event.preventDefault();
    dragActive = false;
  }
  function onDrop(event) {
    event.preventDefault();
    dragActive = false;
    const file = event.dataTransfer.files[0];
    if (file) {
      processFile(file);
      // sync the file input
      if (fileInputRef) {
        const dataTransfer = new DataTransfer();
        dataTransfer.items.add(file);
        fileInputRef.files = dataTransfer.files;
      }
    }
  }
</script>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700&display=swap"
    rel="stylesheet"
  />
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"
  />
</svelte:head>

<div class="bg-blob blob-1"></div>
<div class="bg-blob blob-2"></div>

<section class="page">
  <!-- LEFT CONTENT -->
  <div class="left">
    <h1>Your Dream Job <br />Starts Here</h1>
    <p class="highlight">
      <i class="fas fa-cloud-upload-alt" style="margin-right: 8px"></i>
      Upload Your Resume for Free!
    </p>
    <div class="features">
      <div class="card">
        <div class="icon"><i class="fas fa-briefcase"></i></div>
        <p>Build a profile that attracts top recruiters</p>
      </div>
      <div class="card">
        <div class="icon"><i class="fas fa-envelope-open-text"></i></div>
        <p>Receive relevant jobs based on preferences</p>
      </div>
      <div class="card">
        <div class="icon"><i class="fas fa-chart-line"></i></div>
        <p>Find a job and grow your career</p>
      </div>
    </div>
  </div>

  <!-- RIGHT FORM -->
  <div class="form-box">
    <h2>Post your Resume <span>Free</span></h2>
    <p class="sub">Get matched with suitable jobs</p>

    <!-- Upload zone -->
    <div
      class="upload-zone"
      class:drag-over={dragActive}
      on:dragover={onDragOver}
      on:dragleave={onDragLeave}
      on:drop={onDrop}
      on:click={() => fileInputRef.click()}
    >
      <div class="upload-icon">
        <i class="fas fa-file-pdf"></i> <i class="fas fa-file-word"></i>
      </div>
      <p><b>Upload resume</b></p>
      <small>doc, docx, pdf (max 2MB)</small>
      <input
        bind:this={fileInputRef}
        type="file"
        accept=".pdf,.doc,.docx"
        style="display: none"
        on:change={handleFileSelect}
      />
      {#if selectedFile}
        <div class="file-info">
          <span><i class="fas fa-paperclip"></i> {selectedFile.name} ({(selectedFile.size / 1024).toFixed(1)} KB)</span>
          <button type="button" class="remove-file" on:click|stopPropagation={removeFile}>
            <i class="fas fa-times-circle"></i>
          </button>
        </div>
      {/if}
      {#if fileError}
        <div class="error-msg">{fileError}</div>
      {/if}
    </div>

    <form on:submit|preventDefault={submitForm}>
      <div class="row">
        <div class="input-group">
          <select bind:value={form.gender}>
            <option value="">Gender</option>
            <option value="Male">Male</option>
            <option value="Female">Female</option>
            <option value="Prefer not to say">Prefer not to say</option>
          </select>
          {#if errors.gender}
            <span class="error-text">{errors.gender}</span>
          {/if}
        </div>
        <div class="input-group">
          <input type="text" placeholder="Full Name" bind:value={form.name} />
          {#if errors.name}
            <span class="error-text">{errors.name}</span>
          {/if}
        </div>
      </div>

      <div class="input-group">
        <input type="tel" placeholder="Mobile Number" bind:value={form.phone} />
        {#if errors.phone}
          <span class="error-text">{errors.phone}</span>
        {/if}
      </div>

      <div class="input-group">
        <input type="email" placeholder="Email ID" bind:value={form.email} />
        {#if errors.email}
          <span class="error-text">{errors.email}</span>
        {/if}
      </div>

      <div class="input-group">
        <div class="password-wrapper">
          <input
            type={showPassword ? "text" : "password"}
            placeholder="Password"
            bind:value={form.password}
          />
          <button type="button" class="toggle-pw" on:click={() => (showPassword = !showPassword)}>
            <i class={showPassword ? "far fa-eye" : "far fa-eye-slash"}></i>
          </button>
        </div>
        {#if errors.password}
          <span class="error-text">{errors.password}</span>
        {/if}
      </div>

      <button type="submit">Continue →</button>
    </form>

    <p class="login">
      Already have an account?
      <a href="/job-seekers/otp">Log in</a>
    </p>
  </div>
</section>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  body {
    font-family: "Inter", sans-serif;
    background: linear-gradient(145deg, #eef2ff 0%, #e0e7ff 100%);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* animated background blobs */
  .bg-blob {
    position: fixed;
    width: 55vw;
    height: 55vw;
    background: radial-gradient(
      circle,
      rgba(59, 130, 246, 0.12) 0%,
      rgba(99, 102, 241, 0.08) 100%
    );
    border-radius: 50%;
    z-index: 0;
    filter: blur(70px);
    pointer-events: none;
  }

  .blob-1 {
    top: -20vh;
    right: -10vw;
  }

  .blob-2 {
    bottom: -25vh;
    left: -15vw;
    background: radial-gradient(
      circle,
      rgba(239, 68, 68, 0.1) 0%,
      rgba(249, 115, 22, 0.06) 100%
    );
  }

  /* main container */
  .page {
    position: relative;
    z-index: 2;
    display: flex;
    min-height: 100vh;
    align-items: center;
    justify-content: center;
    padding: 2rem;
    gap: 2rem;
    max-width: 1400px;
    margin: 0 auto;
  }

  /* LEFT SECTION */
  .left {
    flex: 1.2;
    padding: 2rem 2rem 2rem 3rem;
    backdrop-filter: blur(2px);
  }

  .left h1 {
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 700;
    background: linear-gradient(135deg, #0f172a, #1e293b);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    line-height: 1.2;
    margin-bottom: 0.8rem;
  }

  .highlight {
    font-size: clamp(1.1rem, 3vw, 1.5rem);
    font-weight: 600;
    color: #dc2626;
    background: rgba(220, 38, 38, 0.08);
    display: inline-block;
    padding: 0.2rem 1rem;
    border-radius: 40px;
    letter-spacing: -0.2px;
    margin-bottom: 2rem;
  }

  .features {
    display: flex;
    flex-wrap: wrap;
    gap: 1.5rem;
    margin-top: 1rem;
  }

  .card {
    background: rgba(255, 255, 255, 0.75);
    backdrop-filter: blur(12px);
    border-radius: 28px;
    padding: 1.5rem 1.2rem;
    width: 190px;
    text-align: center;
    transition: all 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
    border: 1px solid rgba(255, 255, 255, 0.5);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.02);
  }

  .card:hover {
    transform: translateY(-10px);
    background: rgba(255, 255, 255, 0.9);
    border-color: rgba(59, 130, 246, 0.3);
    box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.15);
  }

  .icon {
    font-size: 2.3rem;
    margin-bottom: 1rem;
    color: #2563eb;
  }

  .card p {
    font-weight: 500;
    color: #1e293b;
    font-size: 0.95rem;
    line-height: 1.4;
  }

  /* FORM BOX */
  .form-box {
    width: 100%;
    max-width: 480px;
    background: rgba(255, 255, 255, 0.94);
    backdrop-filter: blur(16px);
    border-radius: 2rem;
    padding: 2rem 2rem 2.2rem;
    box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 255, 255, 0.5);
    transition: transform 0.2s ease;
  }

  .form-box h2 {
    font-size: 1.9rem;
    font-weight: 700;
    color: #0f172a;
  }

  .form-box h2 span {
    color: #dc2626;
    background: linear-gradient(120deg, #fee2e2, #fff);
    padding: 0 0.2rem;
    border-radius: 12px;
  }

  .sub {
    color: #475569;
    margin-bottom: 1.5rem;
    font-size: 0.9rem;
    border-left: 3px solid #3b82f6;
    padding-left: 0.8rem;
    margin-top: 0.2rem;
  }

  /* upload zone */
  .upload-zone {
    border: 2px dashed #cbd5e1;
    background: #f8fafc;
    border-radius: 1.2rem;
    padding: 1rem 0.8rem;
    text-align: center;
    margin-bottom: 1.5rem;
    transition: all 0.2s;
    cursor: pointer;
    position: relative;
  }

  .upload-zone.drag-over {
    border-color: #3b82f6;
    background: #eff6ff;
  }

  .upload-icon {
    font-size: 2rem;
    color: #3b82f6;
    margin-bottom: 0.4rem;
  }

  .upload-zone p {
    font-weight: 600;
    color: #1e293b;
  }

  .upload-zone small {
    color: #64748b;
    font-size: 0.7rem;
  }

  .file-info {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: #eef2ff;
    border-radius: 50px;
    padding: 0.5rem 1rem;
    margin-top: 0.7rem;
    font-size: 0.8rem;
  }

  .file-info span {
    font-weight: 500;
    color: #1e40af;
    word-break: break-all;
  }

  .remove-file {
    background: none;
    border: none;
    color: #ef4444;
    cursor: pointer;
    font-size: 1rem;
    transition: 0.2s;
  }

  .error-msg {
    color: #e11d48;
    font-size: 0.7rem;
    margin-top: 0.3rem;
    text-align: left;
  }

  /* form inputs */
  .input-group {
    margin-bottom: 1rem;
    position: relative;
  }

  .input-group input,
  .input-group select {
    width: 100%;
    padding: 0.9rem 1rem;
    border-radius: 1rem;
    border: 1px solid #e2e8f0;
    background: white;
    font-family: "Inter", sans-serif;
    font-size: 0.9rem;
    transition: all 0.2s;
    outline: none;
  }

  .input-group input:focus,
  .input-group select:focus {
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
  }

  .row {
    display: flex;
    gap: 1rem;
  }

  .row .input-group {
    flex: 1;
  }

  .password-wrapper {
    position: relative;
  }

  .toggle-pw {
    position: absolute;
    right: 15px;
    top: 50%;
    transform: translateY(-50%);
    cursor: pointer;
    color: #94a3b8;
    background: transparent;
    border: none;
    font-size: 1.1rem;
  }

  .error-text {
    font-size: 0.7rem;
    color: #e11d48;
    margin-top: 0.25rem;
    margin-left: 0.5rem;
    display: block;
  }

  button[type="submit"] {
    width: 100%;
    padding: 1rem;
    background: linear-gradient(105deg, #2563eb, #4f46e5);
    border: none;
    border-radius: 1.5rem;
    font-weight: 700;
    font-size: 1rem;
    color: white;
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: 0 5px 15px rgba(37, 99, 235, 0.3);
    margin-top: 0.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.6rem;
  }

  button[type="submit"]:hover {
    transform: scale(1.02);
    background: linear-gradient(105deg, #1d4ed8, #4338ca);
    box-shadow: 0 12px 22px -10px #2563eb;
  }

  .login {
    text-align: center;
    margin-top: 1.5rem;
    font-size: 0.85rem;
    color: #334155;
  }

  .login a {
    color: #2563eb;
    font-weight: 600;
    text-decoration: none;
    transition: 0.2s;
  }

  .login a:hover {
    text-decoration: underline;
  }

  /* Responsive */
  @media (max-width: 950px) {
    .page {
      flex-direction: column;
      padding: 1.5rem;
    }
    .left {
      padding: 1rem;
      text-align: center;
    }
    .features {
      justify-content: center;
    }
    .form-box {
      max-width: 520px;
      margin-bottom: 2rem;
    }
  }

  @media (max-width: 550px) {
    .row {
      flex-direction: column;
      gap: 0;
    }
    .card {
      width: 100%;
    }
    .form-box {
      padding: 1.5rem;
    }
    .left h1 {
      font-size: 1.8rem;
    }
  }

  /* animations */
  @keyframes fadeUp {
    0% {
      opacity: 0;
      transform: translateY(20px);
    }
    100% {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .left,
  .form-box {
    animation: fadeUp 0.6s ease-out;
  }

  .card {
    animation: fadeUp 0.5s backwards;
    animation-delay: calc(0.07s * var(--i, 1));
  }
</style>