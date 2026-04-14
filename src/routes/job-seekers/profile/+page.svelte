<script>
  let form = {
    name: "",
    email: "",
    phone: "",
    password: ""
  };

  let errors = {
    name: "",
    email: "",
    phone: "",
    password: ""
  };

  let showPassword = false;

  function validateForm() {
    let isValid = true;
    errors = { name: "", email: "", phone: "", password: "" };

    // Name validation
    if (!form.name.trim()) {
      errors.name = "Full name is required";
      isValid = false;
    } else if (form.name.trim().length < 2) {
      errors.name = "Name must be at least 2 characters";
      isValid = false;
    }

    // Email validation
    const emailRegex = /^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/;
    if (!form.email.trim()) {
      errors.email = "Email address is required";
      isValid = false;
    } else if (!emailRegex.test(form.email.trim())) {
      errors.email = "Enter a valid email (e.g., name@domain.com)";
      isValid = false;
    }

    // Phone validation
    const phoneDigits = form.phone.replace(/\D/g, "");
    if (!form.phone.trim()) {
      errors.phone = "Phone number is required";
      isValid = false;
    } else if (phoneDigits.length < 8 || phoneDigits.length > 15) {
      errors.phone = "Phone number must have 8–15 digits";
      isValid = false;
    }

    // Password validation
    if (!form.password) {
      errors.password = "Password is required";
      isValid = false;
    } else if (form.password.length < 6) {
      errors.password = "Password must be at least 6 characters";
      isValid = false;
    }

    return isValid;
  }

  function submitForm() {
    if (validateForm()) {
      alert("✅ Registered Successfully!");
      console.log("Registration Data:", form);
    } else {
      const firstError = document.querySelector(".error-message");
      if (firstError) firstError.scrollIntoView({ behavior: "smooth", block: "center" });
    }
  }
</script>

<svelte:head>
  <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,400;14..32,500;14..32,600;14..32,700&display=swap" rel="stylesheet" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" />
</svelte:head>

<div class="page">
  <div class="card">
    <div class="card-header">
      <h1>Create Account</h1>
      <p>Join thousands of job seekers</p>
    </div>

    <form on:submit|preventDefault={submitForm}>
      <div class="input-group">
        <label for="name">Full Name</label>
        <input id="name" type="text" placeholder="John Doe" bind:value={form.name} class={errors.name ? "error" : ""} />
        {#if errors.name}<span class="error-message">{errors.name}</span>{/if}
      </div>

      <div class="input-group">
        <label for="email">Email Address</label>
        <input id="email" type="email" placeholder="hello@example.com" bind:value={form.email} class={errors.email ? "error" : ""} />
        {#if errors.email}<span class="error-message">{errors.email}</span>{/if}
      </div>

      <div class="input-group">
        <label for="phone">Phone Number</label>
        <input id="phone" type="tel" placeholder="+1 234 567 8900" bind:value={form.phone} class={errors.phone ? "error" : ""} />
        {#if errors.phone}<span class="error-message">{errors.phone}</span>{/if}
      </div>

      <div class="input-group">
        <label for="password">Password</label>
        <div class="password-wrapper">
          <input id="password" type={showPassword ? "text" : "password"} placeholder="••••••" bind:value={form.password} class={errors.password ? "error" : ""} />
          <button type="button" class="toggle-pw" on:click={() => showPassword = !showPassword}>
            <i class={showPassword ? "far fa-eye" : "far fa-eye-slash"}></i>
          </button>
        </div>
        {#if errors.password}<span class="error-message">{errors.password}</span>{/if}
      </div>

      <button type="submit" class="submit-btn">Register Now →</button>
    </form>

    <p class="login-link">
      Already have an account? <a href="/login">Sign in</a>
    </p>
  </div>
</div>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

 .page {
  min-height: 100vh;

  display: flex;
  justify-content: center;
  align-items: center;

  background: linear-gradient(135deg, #f5f7ff 0%, #e9eef5 100%);
  
  padding: 1.5rem 1.5rem 0 1.5rem; /* remove bottom padding */
}

  .card {
    max-width: 480px;
    width: 100%;
    background: rgba(255, 255, 255, 0.96);
    backdrop-filter: blur(2px);
    border-radius: 2rem;
    box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
    padding: 2rem 2rem 2.2rem;
    transition: transform 0.2s ease;
  }

  .card-header {
    text-align: center;
    margin-bottom: 1.8rem;
  }

  .card-header h1 {
    font-size: 1.9rem;
    font-weight: 700;
    background: linear-gradient(135deg, #1e3a8a, #2563eb);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    letter-spacing: -0.3px;
    margin-bottom: 0.5rem;
  }

  .card-header p {
    color: #5b6e8c;
    font-size: 0.9rem;
  }

  .input-group {
    margin-bottom: 1.2rem;
  }

  label {
    display: block;
    font-size: 0.85rem;
    font-weight: 600;
    color: #1e2a3e;
    margin-bottom: 0.4rem;
  }

  input {
    width: 100%;
    padding: 0.85rem 1rem;
    border-radius: 1rem;
    border: 1.5px solid #e2edf2;
    background: #ffffff;
    font-family: 'Inter', sans-serif;
    font-size: 0.9rem;
    transition: all 0.2s;
    outline: none;
  }

  input:focus {
    border-color: #2563eb;
    box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.15);
  }

  input.error {
    border-color: #e11d48;
    background-color: #fff5f7;
  }

  .error-message {
    display: block;
    font-size: 0.7rem;
    color: #e11d48;
    margin-top: 0.35rem;
    margin-left: 0.5rem;
    font-weight: 500;
  }

  .password-wrapper {
    position: relative;
  }

  .toggle-pw {
    position: absolute;
    right: 14px;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    cursor: pointer;
    color: #8ba0bc;
    font-size: 1rem;
    transition: color 0.2s;
  }

  .toggle-pw:hover {
    color: #2563eb;
  }

  .submit-btn {
    width: 100%;
    margin-top: 0.5rem;
    padding: 0.9rem;
    background: linear-gradient(105deg, #2563eb, #4f46e5);
    border: none;
    border-radius: 1.8rem;
    font-weight: 700;
    font-size: 1rem;
    color: white;
    cursor: pointer;
    transition: all 0.3s ease;
    box-shadow: 0 8px 18px -8px rgba(37, 99, 235, 0.4);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.6rem;
  }

  .submit-btn:hover {
    transform: translateY(-2px);
    background: linear-gradient(105deg, #1d4ed8, #4338ca);
    box-shadow: 0 14px 24px -10px #2563eb;
  }

  .submit-btn:active {
    transform: translateY(1px);
  }

  .login-link {
    text-align: center;
    margin-top: 1.8rem;
    font-size: 0.85rem;
    color: #4b5565;
  }

  .login-link a {
    color: #2563eb;
    font-weight: 600;
    text-decoration: none;
    border-bottom: 1px dotted transparent;
    transition: 0.2s;
  }

  .login-link a:hover {
    border-bottom-color: #2563eb;
  }

  @media (max-width: 550px) {
    .card {
      padding: 1.5rem;
    }
    .card-header h1 {
      font-size: 1.6rem;
    }
    input {
      padding: 0.75rem;
    }
  }

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

  .card {
    animation: fadeSlideUp 0.5s ease-out;
  }
</style>