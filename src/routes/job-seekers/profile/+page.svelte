<script>
  let form = {
    name: "",
    email: "",
    phone: "",
    password: ""
  };

  function submitForm() {
    alert("Registered Successfully ✅");
    console.log(form);
  }
</script>

<section class="page">
  <div class="container">
    <h1>Job Seeker Registration</h1>

    <form on:submit|preventDefault={submitForm}>
      <input placeholder="Full Name" bind:value={form.name} required />
      <input type="email" placeholder="Email" bind:value={form.email} required />
      <input placeholder="Phone Number" bind:value={form.phone} required />
      <input type="password" placeholder="Password" bind:value={form.password} required />

      <button type="submit">Register</button>
    </form>
  </div>
</section>

<style>
  /* PAGE WRAPPER (fixes navbar overlap perfectly) */
  .page {
    min-height: 100vh;
    padding-top: 120px; /* adjusts below navbar */
    background: #f5f7fb;
    display: flex;
    justify-content: center;
    align-items: flex-start;
  }

  /* FORM CARD */
  .container {
    width: 100%;
    max-width: 420px;
    padding: 35px;
    background: white;
    border-radius: 14px;
    box-shadow: 0 15px 40px rgba(0,0,0,0.08);
    transition: 0.3s;
  }

  .container:hover {
    transform: translateY(-3px);
  }

  h1 {
    text-align: center;
    margin-bottom: 25px;
    color: #1e5fd8;
    font-size: 22px;
    font-weight: 600;
  }

  /* FORM */
  form {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }

  /* INPUTS */
  input {
    padding: 13px;
    border-radius: 10px;
    border: 1px solid #ddd;
    font-size: 14px;
    transition: 0.25s;
  }

  input:focus {
    border-color: #1e5fd8;
    outline: none;
    box-shadow: 0 0 0 3px rgba(30,95,216,0.1);
  }

  /* BUTTON */
  button {
    margin-top: 10px;
    padding: 13px;
    background: linear-gradient(135deg, #1e5fd8, #3a7afe);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 15px;
    font-weight: 500;
    cursor: pointer;
    transition: 0.3s;
  }

  button:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 20px rgba(30,95,216,0.3);
  }
</style>