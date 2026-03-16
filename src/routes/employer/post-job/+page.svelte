<!-- <script>
let form = {
company:"",
contact:"",
email:"",
phone:"",
jobTitle:"",
department:"",
type:"",
positions:"",
description:"",

qualification:"",
experience:"",
salary:"",
allowance:"",
holidays:"",
skills:"",
languages:[]
};

function toggleLanguage(lang){
if(form.languages.includes(lang)){
form.languages = form.languages.filter(l => l !== lang);
}else{
form.languages = [...form.languages, lang];
}
}

function submitForm(){
console.log(form);
alert("Hiring request submitted!");
}
</script>

<section class="container">

<h1>Submit Your Hiring Request</h1>
<p class="subtitle">
Tell us who you need — we'll find the right talent for you.
</p>

<div class="form-card">

<form on:submit|preventDefault={submitForm}>

<div class="grid">

<div class="field">
<label>Company Name *</label>
<input type="text" placeholder="Acme Corp" bind:value={form.company}>
</div>

<div class="field">
<label>Contact Person *</label>
<input type="text" placeholder="John Doe" bind:value={form.contact}>
</div>

<div class="field">
<label>Email *</label>
<input type="email" placeholder="john@acme.com" bind:value={form.email}>
</div>

<div class="field">
<label>Phone *</label>
<input type="text" placeholder="+1 (555) 000-0000" bind:value={form.phone}>
</div>

</div>

<hr>

<h2>Position Details</h2>

<div class="grid">

<div class="field">
<label>Job Title *</label>
<input type="text" placeholder="Senior Software Engineer" bind:value={form.jobTitle}>
</div>

<div class="field">
<label>Department *</label>
<select bind:value={form.department}>
<option>Select department</option>
<option>Engineering</option>
<option>Marketing</option>
<option>Sales</option>
<option>HR</option>
</select>
</div>

<div class="field">
<label>Employment Type *</label>
<select bind:value={form.type}>
<option>Select type</option>
<option>Full-time</option>
<option>Part-time</option>
<option>Contract</option>
<option>Temporary</option>
<option>Internship</option>
</select>
</div>

<div class="field">
<label>Number of Positions *</label>
<input type="number" placeholder="1" bind:value={form.positions}>
</div>

</div>

<div class="field">
<label> Job Description</label>
<textarea rows="4" placeholder="Describe the role, requirements, experience level and any other details..." bind:value={form.description}></textarea>
</div>
<hr>

<h2>Requirements</h2>

<div class="grid">

<div class="field">
<label>Qualification *</label>
<input type="text" placeholder="B.Tech / BCA / MCA" bind:value={form.qualification}>
</div>

<div class="field">
<label>Experience *</label>
<input type="text" placeholder="0-2 Years / 3+ Years" bind:value={form.experience}>
</div>

<div class="field">
<label>Salary *</label>
<input type="text" placeholder="₹4L - ₹8L per year" bind:value={form.salary}>
</div>

<div class="field">
<label>Allowance</label>
<input type="text" placeholder="Travel / Food Allowance" bind:value={form.allowance}>
</div>

<div class="field">
<label>Holidays</label>
<input type="text" placeholder="Saturday Sunday Off" bind:value={form.holidays}>
</div>

<div class="field">
<label>Skill Requirements *</label>
<input type="text" placeholder="Java, Spring Boot, React" bind:value={form.skills}>
</div>

</div>
<div class="field">
<label>Required Languages</label>

<div class="languages">

<label>
<input type="checkbox" on:change={() => toggleLanguage('English')}>
English
</label>

<label>
<input type="checkbox" on:change={() => toggleLanguage('Hindi')}>
Hindi
</label>

<label>
<input type="checkbox" on:change={() => toggleLanguage('Gujarati')}>
Gujarati
</label>

<label>
<input type="checkbox" on:change={() => toggleLanguage('Spanish')}>
Spanish
</label>

<label>
<input type="checkbox" on:change={() => toggleLanguage('German')}>
German
</label>

</div>

</div>

<button type="submit">Submit Hiring Request</button>

</form>

</div>

</section>

<style>

.container{
text-align:center;
padding:40px 20px;
background:#f4f6f8;
min-height:100vh;
}

h1{
font-size:36px;
font-weight:700;
color:#1f2937;
margin-bottom:10px;
}

.subtitle{
color:#6b7280;
font-size:18px;
margin-bottom:40px;
}

.form-card{
max-width:900px;
width:100%;
margin:auto;
background:white;
padding:40px;
border-radius:12px;
box-shadow:0 10px 30px rgba(0,0,0,0.08);
text-align:left;
}

/* GRID LAYOUT */
.grid{
display:grid;
grid-template-columns:1fr 1fr;
gap:20px;
margin-bottom:20px;
}

.field{
display:flex;
flex-direction:column;
}

label{
font-size:14px;
margin-bottom:6px;
color:#374151;
font-weight:500;
}

input,select,textarea{
width:100%;
padding:12px;
border-radius:8px;
border:1px solid #d1d5db;
font-size:14px;
}

input:focus,select:focus,textarea:focus{
outline:none;
border-color:#f59e0b;
box-shadow:0 0 0 3px rgba(245,158,11,0.2);
}

textarea{
resize:none;
margin-top:10px;
}

hr{
margin:30px 0;
border:none;
border-top:1px solid #e5e7eb;
}

h2{
margin-bottom:20px;
color:#1f2937;
}

button{
width:100%;
background:#f59e0b;
border:none;
padding:14px;
border-radius:8px;
font-size:16px;
font-weight:600;
cursor:pointer;
margin-top:20px;
transition:0.3s;
}

button:hover{
background:#d97706;
}

/* Tablet */
@media(max-width:992px){

.form-card{
padding:30px;
}

h1{
font-size:30px;
}

}

/* Mobile */
@media(max-width:768px){

.grid{
grid-template-columns:1fr;
}

.form-card{
padding:25px;
}

h1{
font-size:26px;
}

.subtitle{
font-size:16px;
}

}

/* Small Mobile */
@media(max-width:480px){

.container{
padding:20px 10px;
}

.form-card{
padding:20px;
}

button{
padding:12px;
font-size:15px;
}

}
.languages{
display:flex;
flex-wrap:wrap;
gap:15px;
margin-top:8px;
}

.languages label{
display:flex;
align-items:center;
gap:6px;
font-size:14px;
color:#374151;
}
</style> -->
<script>
import flatpickr from "flatpickr";
import "flatpickr/dist/flatpickr.css";
import intlTelInput from "intl-tel-input";
import "intl-tel-input/build/css/intlTelInput.css";
import { onMount } from "svelte";

let phoneInput;

// ========== DEPARTMENT STATE ==========
let departments = ["Engineering", "Marketing", "Sales", "HR"];
let departmentSearch = "";
let filteredDepartments = [];
let showDropdown = false;
let closeTimeout;

// ========== MAIN FORM DATA ==========
let form = {
  company: "",
  contact: "",
  email: "",
  phone: "",
  website: "",
  companySize: "",
  industry: "",

  jobTitle: "",
  department: "",
  location: "",
  workMode: "",
  employmentType: "",
  positionType: "",
  positions: 1,

  qualification: "",
  experience: "",
  skills: "",

  expType: "any",
  minExp: "",
  maxExp: "",
  salaryMin: "",
  salaryMax: "",
  bonus: "",

  hireSpeed: "",
  hireFrequency: "",

  languages: [],

  salary: "",
  benefits: "",
  allowance: "",
  holidays: "",

  summary: "",
  responsibilities: "",
  requirements: "",

  deadline: "",
  joiningDate: "",
};

// ========== SUBMIT ==========
function submitForm() {
  const fullPhone = phoneInput.getNumber();
  form.phone = fullPhone;
  console.log(form);
  alert("Hiring request submitted successfully!");
}

// ========== ON MOUNT ==========
onMount(() => {
  // Load departments from localStorage
  const storedDepartments = localStorage.getItem("departments");
  if (storedDepartments) {
    departments = JSON.parse(storedDepartments);
  }

  // Initialize date pickers
  flatpickr("#deadline", {
    dateFormat: "d/m/Y",
    minDate: "today"
  });
  flatpickr("#joiningDate", {
    dateFormat: "d/m/Y",
    minDate: "today"
  });

  // Initialize international telephone input
  const input = document.querySelector("#phone");
  phoneInput = intlTelInput(input, {
    initialCountry: "in",
    separateDialCode: true,
    preferredCountries: ["in", "us", "gb"],
    utilsScript:
      "https://cdn.jsdelivr.net/npm/intl-tel-input@18.1.1/build/js/utils.js"
  });
});

// ========== DEPARTMENT DROPDOWN FUNCTIONS ==========
function searchDepartment() {
  if (departmentSearch.trim() === "") {
    filteredDepartments = departments;
  } else {
    filteredDepartments = departments.filter(d =>
      d.toLowerCase().includes(departmentSearch.toLowerCase())
    );
  }
  showDropdown = true;
}

function selectDepartment(dept) {
  form.department = dept;
  departmentSearch = dept;
  showDropdown = false;
  clearTimeout(closeTimeout);
}

function addNewDepartment() {
  const newDept = departmentSearch.trim();
  if (newDept === "") return;

  if (!departments.includes(newDept)) {
    departments = [...departments, newDept];
    localStorage.setItem("departments", JSON.stringify(departments));
  }

  form.department = newDept;
  departmentSearch = newDept;
  showDropdown = false;
  clearTimeout(closeTimeout);
}

function closeDropdownDelayed() {
  closeTimeout = setTimeout(() => {
    showDropdown = false;
  }, 200);
}
</script>

<section class="container">
  <h1>Post a Job</h1>
  <p class="subtitle">Tell us about the role and we'll help you find the right candidate.</p>

  <div class="form-card">
    <form on:submit|preventDefault={submitForm}>
      <!-- COMPANY INFORMATION -->
      <fieldset>
        <h2>Company Information</h2>
        <div class="grid">
          <div class="field">
            <label>Company Name *</label>
            <input type="text" bind:value={form.company} placeholder="e.g. Acme Inc." />
          </div>
          <div class="field">
            <label>Contact Person *</label>
            <input type="text" bind:value={form.contact} placeholder="Full name" />
          </div>
          <div class="field">
            <label>Email *</label>
            <input type="email" bind:value={form.email} placeholder="hr@company.com" />
          </div>
          <div class="field">
            <label>Phone *</label>
            <input id="phone" type="tel" placeholder="Enter phone number" />
          </div>
          <div class="field">
            <label>Company Website</label>
            <input type="url" bind:value={form.website} placeholder="https://example.com" />
          </div>
          <div class="field">
            <label>Company Size</label>
            <select bind:value={form.companySize}>
              <option value="">Select</option>
              <option>1-10</option>
              <option>11-50</option>
              <option>51-200</option>
              <option>200+</option>
            </select>
          </div>
        </div>
      </fieldset>

      <!-- HIRING PREFERENCE -->
      <fieldset>
        <h2>Hiring Preference</h2>
        <div class="field">
          <label>How soon do you want to fill the position? *</label>
          <div class="hire-speed">
            <button
              type="button"
              class:selected={form.hireSpeed === "immediate"}
              on:click={() => form.hireSpeed = "immediate"}
            >
              Immediately (1-2 weeks)
            </button>
            <button
              type="button"
              class:selected={form.hireSpeed === "wait"}
              on:click={() => form.hireSpeed = "wait"}
            >
              Can wait
            </button>
          </div>
        </div>
        <div class="field">
          <label>How often do you need to hire? *</label>
          <select bind:value={form.hireFrequency}>
            <option value="">Select</option>
            <option>Every Month</option>
            <option>Once in a few months</option>
            <option>Not sure</option>
          </select>
        </div>
      </fieldset>

      <!-- JOB DETAILS -->
      <fieldset>
        <h2>Job Details</h2>
        <div class="grid">
          <div class="field">
            <label>Job Title *</label>
            <input type="text" bind:value={form.jobTitle} placeholder="e.g. Frontend Developer" />
          </div>

          <!-- ===== DEPARTMENT DROPDOWN ===== -->
          <div class="field department-wrapper">
            <label>Department</label>
            <div class="dropdown-container">
              <input
                type="text"
                placeholder="Search or add department"
                bind:value={departmentSearch}
                on:input={searchDepartment}
                on:focus={searchDepartment}
                on:blur={closeDropdownDelayed}
              />
              {#if showDropdown}
                <div class="dropdown">
                  {#each filteredDepartments as dept}
                    <div
                      class="dropdown-item"
                      on:mousedown|preventDefault={() => selectDepartment(dept)}
                    >
                      {dept}
                    </div>
                  {/each}
                  {#if filteredDepartments.length === 0 && departmentSearch.trim() !== ''}
                    <div
                      class="dropdown-item add"
                      on:mousedown|preventDefault={addNewDepartment}
                    >
                      Add "{departmentSearch}"
                    </div>
                  {/if}
                </div>
              {/if}
            </div>
          </div>

          <div class="field">
            <label>Location *</label>
            <input type="text" bind:value={form.location} placeholder="City, Country" />
          </div>

          <div class="field">
            <label>Work Mode *</label>
            <select bind:value={form.workMode}>
              <option>Onsite</option>
              <option>Remote</option>
              <option>Hybrid</option>
            </select>
          </div>
        </div>

        <div class="triple-row">
          <div class="field">
            <label>Employment Type *</label>
            <select bind:value={form.employmentType}>
              <option>Full-time</option>
              <option>Part-time</option>
              <option>Contract</option>
              <option>Internship</option>
            </select>
          </div>
          <div class="field">
            <label>Position Type</label>
            <select bind:value={form.positionType}>
              <option>Blue Collar</option>
              <option>White Collar</option>
            </select>
          </div>
          <div class="field">
            <label>Number of Positions</label>
            <input type="number" bind:value={form.positions} min="1" placeholder="e.g. 2" />
          </div>
        </div>
      </fieldset>

      <!-- JOB DESCRIPTION -->
      <fieldset>
        <h2>Job Description</h2>
        <div class="field">
          <label>Job Summary</label>
          <textarea rows="3" bind:value={form.summary} placeholder="Brief overview of the role..."></textarea>
        </div>
        <div class="field">
          <label>Responsibilities</label>
          <textarea rows="3" bind:value={form.responsibilities} placeholder="Key duties and tasks..."></textarea>
        </div>
        <div class="field">
          <label>Requirements</label>
          <textarea rows="3" bind:value={form.requirements} placeholder="Must-have skills and qualifications..."></textarea>
        </div>
      </fieldset>

      <!-- CANDIDATE REQUIREMENTS -->
      <fieldset>
        <h2>Candidate Requirements</h2>

        <div class="field">
          <label>Educational Qualification</label>
          <textarea rows="3" bind:value={form.qualification} placeholder="e.g. B.Tech, BCA, MCA"></textarea>
        </div>

        <!-- EXPERIENCE UI -->
        <div class="experience-box">
          <label>Total Experience of Candidate *</label>
          <div class="exp-buttons">
            <button
              type="button"
              class:selected={form.expType === "any"}
              on:click={() => form.expType = "any"}
            >
              Any
            </button>
            <button
              type="button"
              class:selected={form.expType === "fresher"}
              on:click={() => form.expType = "fresher"}
            >
              Fresher Only
            </button>
            <button
              type="button"
              class:selected={form.expType === "experienced"}
              on:click={() => form.expType = "experienced"}
            >
              Experienced Only
            </button>
          </div>
        </div>

        {#if form.expType === "any"}
          <div class="info-box">
            Both freshers and experienced candidates will be able to Call/Apply.
          </div>
          <div class="field">
            <label>Total Experience</label>
            <select bind:value={form.anyExperience}>
              <option value="">Select Experience</option>
              <option>Fresher</option>
              <option>0-1 Years</option>
              <option>1-2 Years</option>
              <option>2-3 Years</option>
              <option>3-5 Years</option>
              <option>5+ Years</option>
            </select>
          </div>
        {/if}

        {#if form.expType === "fresher"}
          <div class="info-box">
            Only Fresher candidates upto 6 months of experience will be able to Call/Apply.
          </div>
        {/if}

        {#if form.expType === "experienced"}
          <div class="grid">
            <div class="field">
              <label>Minimum Experience *</label>
              <select bind:value={form.minExp}>
                <option value="">Select Min</option>
                <option>1 Year</option>
                <option>2 Years</option>
                <option>3 Years</option>
                <option>4 Years</option>
                <option>5 Years</option>
              </select>
            </div>
            <div class="field">
              <label>Maximum Experience *</label>
              <select bind:value={form.maxExp}>
                <option value="">Select Max</option>
                <option>2 Years</option>
                <option>3 Years</option>
                <option>4 Years</option>
                <option>5 Years</option>
                <option>10 Years</option>
              </select>
            </div>
          </div>
        {/if}

        <div class="salary-range">
          <label>Monthly In-hand Salary *</label>
          <div class="salary-input">
            <input type="number" placeholder="Eg. 10000" bind:value={form.salaryMin} />
            <span>to</span>
            <input type="number" placeholder="Eg. 15000" bind:value={form.salaryMax} />
          </div>
        </div>

        <div class="field">
          <label>Do you offer bonus in addition to monthly salary?</label>
          <div class="radio-group">
            <label>
              <input type="radio" value="yes" bind:group={form.bonus} /> Yes
            </label>
            <label>
              <input type="radio" value="no" bind:group={form.bonus} /> No
            </label>
          </div>
        </div>

        <div class="field">
          <label>Skills</label>
          <textarea rows="3" bind:value={form.skills} placeholder="e.g. Java, Spring Boot, React"></textarea>
        </div>

        <div class="field">
          <label>Languages Required</label>
          <div class="language-row">
            <label class="lang-option">
              <input type="checkbox" value="English" bind:group={form.languages} />
              <span>English</span>
            </label>
            <label class="lang-option">
              <input type="checkbox" value="Hindi" bind:group={form.languages} />
              <span>Hindi</span>
            </label>
            <label class="lang-option">
              <input type="checkbox" value="Gujarati" bind:group={form.languages} />
              <span>Gujarati</span>
            </label>
            <label class="lang-option">
              <input type="checkbox" value="Spanish" bind:group={form.languages} />
              <span>Spanish</span>
            </label>
            <label class="lang-option">
              <input type="checkbox" value="German" bind:group={form.languages} />
              <span>German</span>
            </label>
          </div>
          {#if form.languages.length > 0}
            <div class="selected-preview">
              Selected: {form.languages.join(', ')}
            </div>
          {/if}
        </div>
      </fieldset>

      <!-- SALARY & BENEFITS -->
      <fieldset>
        <h2>Salary & Benefits</h2>
        <div class="grid">
          <div class="field">
            <label>Salary</label>
            <input type="text" bind:value={form.salary} placeholder="₹4L - ₹8L per year" />
          </div>
          <div class="field">
            <label>Benefits</label>
            <input type="text" bind:value={form.benefits} placeholder="Insurance, Bonus, PF" />
          </div>
          <div class="field">
            <label>Allowance</label>
            <input type="text" bind:value={form.allowance} placeholder="Travel, meals, etc." />
          </div>
          <div class="field">
            <label>Holidays</label>
            <input type="text" bind:value={form.holidays} placeholder="Paid time off, public holidays" />
          </div>
        </div>
      </fieldset>

      <!-- HIRING TIMELINE -->
      <fieldset>
        <h2>Hiring Timeline</h2>
        <div class="grid">
          <div class="field">
            <label>Application Deadline</label>
            <input id="deadline" type="text" bind:value={form.deadline} placeholder="DD/MM/YYYY" />
          </div>
          <div class="field">
            <label>Joining Date</label>
            <input id="joiningDate" type="text" bind:value={form.joiningDate} placeholder="DD/MM/YYYY" />
          </div>
        </div>
      </fieldset>

      <button type="submit">Submit Hiring Request</button>
    </form>
  </div>
</section>

<style>
  /* ---------- GLOBAL / RESET ---------- */
  .container {
    text-align: center;
    padding: 40px 20px;
    background: #f4f6f8;
    min-height: 100vh;
    font-family: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  }
  h1 {
    font-size: clamp(2rem, 5vw, 2.5rem);
    font-weight: 700;
    margin: 0 0 0.5rem;
    color: #1f2937;
  }
  .subtitle {
    color: #6b7280;
    margin-bottom: 2.5rem;
    font-size: clamp(1rem, 3vw, 1.15rem);
  }
  .form-card {
    max-width: 1000px;
    margin: auto;
    background: white;
    padding: clamp(20px, 5vw, 40px);
    border-radius: 24px;
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.15);
    text-align: left;
  }
  fieldset {
    border: none;
    padding: 0;
    margin: 0 0 2rem 0;
  }
  h2 {
    font-size: clamp(1.3rem, 4vw, 1.5rem);
    font-weight: 600;
    margin: 0 0 1.25rem 0;
    padding-bottom: 0.5rem;
    border-bottom: 2px solid #f0f2f5;
    color: #1f2937;
  }

  /* ---------- GRID LAYOUTS ---------- */
  .grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
    width: 100%;
  }
  .triple-row {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 24px;
    margin-top: 20px;
  }

  /* ---------- FORM FIELDS ---------- */
  .field {
    display: flex;
    flex-direction: column;
    margin-bottom: 16px;
  }
  label {
    font-size: 0.9rem;
    font-weight: 500;
    margin-bottom: 0.4rem;
    color: #374151;
  }
  input, select, textarea {
    padding: 12px 16px;
    border-radius: 12px;
    border: 1px solid #d1d5db;
    font-size: 0.95rem;
    transition: all 0.2s;
    width: 100%;
    box-sizing: border-box;
  }
  input:focus, select:focus, textarea:focus {
    outline: none;
    border-color: #f59e0b;
    box-shadow: 0 0 0 4px rgba(245, 158, 11, 0.15);
  }
  textarea {
    resize: vertical;
    min-height: 90px;
  }
  select:not([multiple]) {
    appearance: none;
    background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="%236b7280" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="6 9 12 15 18 9"/></svg>');
    background-repeat: no-repeat;
    background-position: right 1rem center;
    background-size: 1rem;
  }

  /* ---------- LANGUAGE SECTION ---------- */
  .language-row {
    display: flex;
    flex-wrap: wrap;
    gap: 18px;
    margin-top: 10px;
    background: #f9fafb;
    padding: 1rem 1.25rem;
    border-radius: 12px;
    border: 1px solid #e9ecef;
  }
  .lang-option {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    cursor: pointer;
    font-weight: 400;
    color: #2d3a4a;
  }
  .lang-option input[type="checkbox"] {
    width: 1.1rem;
    height: 1.1rem;
    accent-color: #f59e0b;
    margin: 0;
    border-radius: 4px;
  }
  .selected-preview {
    margin-top: 8px;
    font-size: 0.85rem;
    color: #4b5563;
    background: #f3f4f6;
    padding: 6px 12px;
    border-radius: 20px;
    display: inline-block;
  }

  /* ---------- BUTTONS ---------- */
  button {
    width: 100%;
    background: #f59e0b;
    border: none;
    padding: 1rem 1.5rem;
    border-radius: 40px;
    font-size: 1.1rem;
    font-weight: 600;
    color: white;
    cursor: pointer;
    transition: background 0.2s, transform 0.1s, box-shadow 0.2s;
    margin-top: 1.5rem;
    box-shadow: 0 8px 20px -8px rgba(245, 158, 11, 0.5);
  }
  button:hover {
    background: #d97706;
    transform: scale(1.02);
    box-shadow: 0 12px 28px -8px rgba(245, 158, 11, 0.6);
  }
  button:active {
    transform: scale(0.98);
  }
  ::placeholder {
    color: #9ca3af;
    opacity: 1;
  }

  /* ---------- CUSTOM BUTTON GROUPS (exp / hire speed) ---------- */
  .exp-buttons, .hire-speed {
    display: flex;
    gap: 10px;
    margin-top: 8px;
    flex-wrap: wrap;
  }
  .exp-buttons button, .hire-speed button {
    padding: 10px 18px;
    border-radius: 6px;
    border: 1px solid #080808;
    background: #f8f8f8;
    cursor: pointer;
    color: #000;
    font-weight: 500;
    box-shadow: none;
    width: auto;
    margin: 0;
    font-size: 0.9rem;
  }
  .exp-buttons button.selected, .hire-speed button.selected {
    background: #4f5db8;
    color: #ffffff;
    border-color: #4f5db8;
  }

  /* ---------- INFO BOX, SALARY INPUT, RADIO ---------- */
  .info-box {
    background: #5f6dbd;
    color: white;
    padding: 12px 16px;
    border-radius: 6px;
    margin: 15px 0;
    font-size: 14px;
  }
  .salary-input {
    display: flex;
    gap: 10px;
    align-items: center;
    flex-wrap: wrap;
  }
  .salary-input input {
    flex: 1 1 120px;
  }
  .salary-input span {
    font-weight: 500;
  }
  .radio-group {
    display: flex;
    gap: 20px;
    margin-top: 8px;
    flex-wrap: wrap;
  }

  /* ---------- DEPARTMENT DROPDOWN ---------- */
  .department-wrapper {
    position: relative;
    width: 100%;
  }
  .dropdown-container {
    position: relative;
    width: 100%;
    display: block;
  }
  .department-wrapper input {
    width: 100%;
    box-sizing: border-box;
  }
  .dropdown {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    z-index: 10;
    border: 1px solid #ddd;
    background: white;
    border-radius: 8px;
    margin-top: 4px;
    max-height: 200px;
    overflow-y: auto;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  .dropdown-item {
    padding: 10px 16px;
    cursor: pointer;
    font-size: 0.95rem;
  }
  .dropdown-item:hover {
    background: #f3f4f6;
  }
  .dropdown-item.add {
    color: #4f5db8;
    font-weight: 600;
  }

  /* ---------- RESPONSIVE MEDIA QUERIES ---------- */
  @media (max-width: 992px) {
    .form-card {
      padding: 30px;
    }
  }

  @media (max-width: 768px) {
    .grid, .triple-row {
      grid-template-columns: 1fr;
      gap: 16px;
    }
    .language-row {
      gap: 12px;
      padding: 0.75rem 1rem;
    }
    .exp-buttons, .hire-speed {
      flex-direction: row;
      justify-content: flex-start;
    }
  }

  @media (max-width: 576px) {
    .exp-buttons, .hire-speed {
      flex-direction: column;
    }
    .exp-buttons button, .hire-speed button {
      width: 100%;
    }
    .salary-input {
      flex-direction: column;
      align-items: stretch;
    }
    .salary-input input {
      width: 100%;
    }
    .salary-input span {
      align-self: center;
    }
    .radio-group {
      flex-direction: column;
      gap: 8px;
    }
  }

  @media (max-width: 480px) {
    .language-row {
      flex-direction: column;
      gap: 0.5rem;
    }
    .form-card {
      padding: 20px;
    }
    h1 {
      font-size: 1.8rem;
    }
    .subtitle {
      font-size: 0.95rem;
    }
  }

  /* intl-tel-input specific: ensure it doesn't overflow */
  .iti {
    width: 100%;
  }
  .iti__flag-container {
    z-index: 2;
  }
  .iti--separate-dial-code .iti__selected-flag {
    background-color: #f3f4f6;
    border-radius: 12px 0 0 12px;
  }
</style>