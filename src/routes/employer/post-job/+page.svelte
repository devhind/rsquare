<script>
  import { onMount } from "svelte";
  import flatpickr from "flatpickr";
  import "flatpickr/dist/flatpickr.css";
  import intlTelInput from "intl-tel-input";
  import "intl-tel-input/build/css/intlTelInput.css";

  let phoneInput;

  // ========== DEPARTMENT STATE ==========
  let departments = ["Engineering", "Marketing", "Sales", "HR", "Finance", "Operations"];
  let departmentSearch = "";
  let filteredDepartments = [];
  let showDepartmentDropdown = false;
  let closeTimeout;

  // ========== SUBJECT & SUB-SUBJECT STATE ==========
  let mainSubjects = [];
  let subSubjectsMap = {};
  let selectedMainSubject = "";
  let selectedSubSubject = "";
  let newMainSubjectInput = "";
  let showNewMainSubjectInput = false;
  let newSubSubjectInput = "";
  let showNewSubSubjectInput = false;

  // ========== LANGUAGES STATE ==========
  let defaultLanguages = ["English", "Hindi", "Gujarati", "Spanish", "German", "French", "Tamil", "Telugu"];
  let customLanguages = [];
  let newLanguageInput = "";
  let showNewLanguageInput = false;
  let selectedLanguages = [];

  // ========== CERTIFICATES DROPDOWN (MULTI-SELECT) ==========
  let defaultCertificates = ["Computer", "Accounts", "Marketing", "Management", "Digital Marketing", "Data Science", "Cybersecurity", "AWS", "Azure"];
  let customCertificates = [];
  let selectedCertificates = [];
  let showCertDropdown = false;
  let certSearchTerm = "";
  let certHighlightedIndex = -1;

  // ========== SKILLS DROPDOWN (MULTI-SELECT) ==========
  let defaultSkillsList = ["Java", "Python", "React", "Node.js", "Angular", "JavaScript", "HTML/CSS", "SQL", "AWS", "Docker", "UI/UX", "Project Management", "Leadership", "Communication"];
  let customSkillsList = [];
  let selectedSkillsList = [];
  let showSkillsDropdown = false;
  let skillsSearchTerm = "";
  let skillsHighlightedIndex = -1;

  // ========== MAIN FORM DATA ==========
  let form = {
    company: "", contact: "", email: "", phone: "", website: "", companySize: "", briefDetails: "",
    hireSpeed: "", hireFrequency: "",
    jobTitle: "", department: "", location: "", workMode: "", employmentType: "", positionType: "", positions: 1,
    mainSubject: "", subSubject: "", expType: "any", minExp: "", maxExp: "", anyExperience: "", skills: "",
    languages: [], certificates: [], skillsList: [],
    stipendMin: "", stipendMax: "", salaryMin: "", salaryMax: "", bonus: "",
    summary: "", responsibilities: "", requirements: "",
    minAge: "", maxAge: "",
    carFacilities: "", fuelAllowance: "", foodAllowance: "", washingAllowance: "", canteen: "", otRate: "", otherFacilities: "",
    accommodationType: "", transportType: "", transportRoute: "", transportOther: "",
    deadline: "", joiningDate: "",
  };

  let errors = {};

  // ========== REACTIVE SYNC ==========
  $: form.mainSubject = selectedMainSubject;
  $: form.subSubject = selectedSubSubject;
  $: form.languages = selectedLanguages;
  $: form.certificates = selectedCertificates;
  $: form.skillsList = selectedSkillsList;

  $: isInternship = form.employmentType === "Internship";
  $: showStipend = isInternship || form.expType === "fresher";
  $: showSalary = !isInternship && form.expType !== "fresher";

  // ========== FACILITIES MULTI-SELECT ==========
  let defaultFacilities = ["Gym", "Medical", "Insurance", "Transport", "Bonus"];
  let customFacilities = [];
  let selectedFacilities = [];
  let showFacilityDropdown = false;
  let facilitySearchTerm = "";
  let facilityHighlightedIndex = -1;

  function loadFacilities() {
    const stored = localStorage.getItem("job_facilities");
    if (stored) customFacilities = JSON.parse(stored);
  }
  function saveFacilities() {
    localStorage.setItem("job_facilities", JSON.stringify(customFacilities));
  }
  $: allFacilities = [...defaultFacilities, ...customFacilities];
  $: filteredFacilities = allFacilities.filter(item =>
    item.toLowerCase().includes(facilitySearchTerm.toLowerCase())
  );

  function addFacility(name) {
    const value = name.trim();
    if (!value) return;
    if (!allFacilities.includes(value)) {
      customFacilities = [...customFacilities, value];
      saveFacilities();
    }
    if (!selectedFacilities.includes(value)) {
      selectedFacilities = [...selectedFacilities, value];
    }
    facilitySearchTerm = "";
    showFacilityDropdown = false;
  }

  function toggleFacility(item) {
    if (selectedFacilities.includes(item)) {
      selectedFacilities = selectedFacilities.filter(i => i !== item);
    } else {
      selectedFacilities = [...selectedFacilities, item];
    }
  }

  function handleFacilityKey(e) {
    if (!showFacilityDropdown) return;
    if (e.key === "ArrowDown") { e.preventDefault(); facilityHighlightedIndex = (facilityHighlightedIndex + 1) % filteredFacilities.length; }
    else if (e.key === "ArrowUp") { e.preventDefault(); facilityHighlightedIndex = (facilityHighlightedIndex - 1 + filteredFacilities.length) % filteredFacilities.length; }
    else if (e.key === "Enter") {
      e.preventDefault();
      if (facilityHighlightedIndex >= 0) toggleFacility(filteredFacilities[facilityHighlightedIndex]);
      else if (facilitySearchTerm.trim()) addFacility(facilitySearchTerm);
      facilitySearchTerm = ""; showFacilityDropdown = false;
    } else if (e.key === "Escape") { showFacilityDropdown = false; }
  }

  // ========== CERTIFICATES FUNCTIONS ==========
  function loadCustomCertificates() {
    const stored = localStorage.getItem("job_custom_certificates");
    customCertificates = stored ? JSON.parse(stored) : [];
  }
  function saveCustomCertificates() {
    localStorage.setItem("job_custom_certificates", JSON.stringify(customCertificates));
  }
  $: allCertificates = [...defaultCertificates, ...customCertificates];
  $: filteredCertificates = allCertificates.filter(item =>
    item.toLowerCase().includes(certSearchTerm.toLowerCase())
  );

  function addCertificate(name) {
    const value = name.trim();
    if (!value) return;
    if (!allCertificates.includes(value)) { customCertificates = [...customCertificates, value]; saveCustomCertificates(); }
    if (!selectedCertificates.includes(value)) selectedCertificates = [...selectedCertificates, value];
    certSearchTerm = ""; showCertDropdown = false;
  }

  function toggleCertificate(item) {
    selectedCertificates = selectedCertificates.includes(item)
      ? selectedCertificates.filter(i => i !== item)
      : [...selectedCertificates, item];
  }

  function handleCertKey(e) {
    if (!showCertDropdown) return;
    if (e.key === "ArrowDown") { e.preventDefault(); certHighlightedIndex = (certHighlightedIndex + 1) % filteredCertificates.length; }
    else if (e.key === "ArrowUp") { e.preventDefault(); certHighlightedIndex = (certHighlightedIndex - 1 + filteredCertificates.length) % filteredCertificates.length; }
    else if (e.key === "Enter") {
      e.preventDefault();
      if (certHighlightedIndex >= 0) toggleCertificate(filteredCertificates[certHighlightedIndex]);
      else if (certSearchTerm.trim()) addCertificate(certSearchTerm);
      certSearchTerm = ""; showCertDropdown = false;
    } else if (e.key === "Escape") { showCertDropdown = false; }
  }

  // ========== SKILLS FUNCTIONS ==========
  function loadCustomSkillsList() {
    const stored = localStorage.getItem("job_custom_skills_list");
    customSkillsList = stored ? JSON.parse(stored) : [];
  }
  function saveCustomSkillsList() {
    localStorage.setItem("job_custom_skills_list", JSON.stringify(customSkillsList));
  }
  $: allSkills = [...defaultSkillsList, ...customSkillsList];
  $: filteredSkills = allSkills.filter(item =>
    item.toLowerCase().includes(skillsSearchTerm.toLowerCase())
  );

  function addSkill(name) {
    const value = name.trim();
    if (!value) return;
    if (!allSkills.includes(value)) { customSkillsList = [...customSkillsList, value]; saveCustomSkillsList(); }
    if (!selectedSkillsList.includes(value)) selectedSkillsList = [...selectedSkillsList, value];
    skillsSearchTerm = ""; showSkillsDropdown = false;
  }

  function toggleSkill(item) {
    selectedSkillsList = selectedSkillsList.includes(item)
      ? selectedSkillsList.filter(i => i !== item)
      : [...selectedSkillsList, item];
  }

  function handleSkillsKey(e) {
    if (!showSkillsDropdown) return;
    if (e.key === "ArrowDown") { e.preventDefault(); skillsHighlightedIndex = (skillsHighlightedIndex + 1) % filteredSkills.length; }
    else if (e.key === "ArrowUp") { e.preventDefault(); skillsHighlightedIndex = (skillsHighlightedIndex - 1 + filteredSkills.length) % filteredSkills.length; }
    else if (e.key === "Enter") {
      e.preventDefault();
      if (skillsHighlightedIndex >= 0) toggleSkill(filteredSkills[skillsHighlightedIndex]);
      else if (skillsSearchTerm.trim()) addSkill(skillsSearchTerm);
      skillsSearchTerm = ""; showSkillsDropdown = false;
    } else if (e.key === "Escape") { showSkillsDropdown = false; }
  }

  // ========== LANGUAGES FUNCTIONS ==========
  function loadCustomLanguages() {
    const stored = localStorage.getItem("job_custom_languages");
    customLanguages = stored ? JSON.parse(stored) : [];
  }
  function saveCustomLanguages() {
    localStorage.setItem("job_custom_languages", JSON.stringify(customLanguages));
  }
  function addNewLanguage() {
    const name = newLanguageInput.trim();
    if (name && !defaultLanguages.includes(name) && !customLanguages.includes(name)) {
      customLanguages = [...customLanguages, name];
      saveCustomLanguages();
      selectedLanguages = [...selectedLanguages, name];
    }
    newLanguageInput = ""; showNewLanguageInput = false;
  }
  function removeCustomLanguage(lang) {
    if (confirm(`Remove "${lang}" from language list?`)) {
      customLanguages = customLanguages.filter(l => l !== lang);
      selectedLanguages = selectedLanguages.filter(l => l !== lang);
      saveCustomLanguages();
    }
  }

  // ========== SUBJECT DATA MANAGEMENT ==========
  function loadSubjectData() {
    const storedSubjects = localStorage.getItem("job_main_subjects");
    const storedSubMap = localStorage.getItem("job_subjects_map");
    if (storedSubjects && storedSubMap) {
      mainSubjects = JSON.parse(storedSubjects);
      subSubjectsMap = JSON.parse(storedSubMap);
    } else {
      mainSubjects = ["B.Tech", "BCA", "MCA", "MBA", "MSc", "BSc", "B.Com", "M.Com", "PhD", "Diploma"];
      subSubjectsMap = {
        "B.Tech": ["Computer Science", "Mechanical", "Civil", "Electrical", "Electronics", "IT"],
        "BCA": ["Computer Applications", "Programming", "Web Development", "Cloud Computing"],
        "MCA": ["Advanced Computing", "Data Science", "Software Engineering", "AI/ML"],
        "MBA": ["Finance", "Marketing", "HR", "Operations", "Business Analytics"],
        "MSc": ["Physics", "Chemistry", "Mathematics", "Zoology", "Botany", "Biotechnology"],
        "BSc": ["Physics", "Chemistry", "Mathematics", "Computer Science", "IT"],
        "B.Com": ["Accountancy", "Business Administration", "Economics", "Banking"],
        "M.Com": ["Advanced Accountancy", "Finance", "Business Management", "International Business"],
        "PhD": ["Research", "Specialized Field"],
        "Diploma": ["Engineering", "Computer Applications", "Business Management"]
      };
      saveSubjectData();
    }
  }
  function saveSubjectData() {
    localStorage.setItem("job_main_subjects", JSON.stringify(mainSubjects));
    localStorage.setItem("job_subjects_map", JSON.stringify(subSubjectsMap));
  }
  function addNewMainSubject() {
    const name = newMainSubjectInput.trim();
    if (name && !mainSubjects.includes(name)) {
      mainSubjects = [...mainSubjects, name];
      subSubjectsMap[name] = [];
      saveSubjectData();
      selectedMainSubject = name;
      selectedSubSubject = "";
    }
    newMainSubjectInput = ""; showNewMainSubjectInput = false;
  }
  function addNewSubSubject() {
    if (!selectedMainSubject) { alert("Please select a main subject first"); return; }
    const name = newSubSubjectInput.trim();
    if (name && !subSubjectsMap[selectedMainSubject].includes(name)) {
      subSubjectsMap[selectedMainSubject] = [...subSubjectsMap[selectedMainSubject], name];
      saveSubjectData();
      selectedSubSubject = name;
    }
    newSubSubjectInput = ""; showNewSubSubjectInput = false;
  }
  function onMainSubjectChange(value) {
    selectedMainSubject = value;
    selectedSubSubject = "";
  }

  // ========== VALIDATION ==========
  function validateForm() {
    const newErrors = {};
    if (!form.company.trim()) newErrors.company = "Company name is required";
    if (!form.contact.trim()) newErrors.contact = "Contact person is required";
    if (!form.email.trim()) newErrors.email = "Email is required";
    else if (!/^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/.test(form.email)) newErrors.email = "Valid email required";
    if (!form.phone) newErrors.phone = "Phone number is required";
    if (!form.jobTitle.trim()) newErrors.jobTitle = "Job title is required";
    if (!form.location.trim()) newErrors.location = "Job location is required";
    if (!form.workMode) newErrors.workMode = "Work mode is required";
    if (!form.employmentType) newErrors.employmentType = "Employment type is required";
    if (!selectedMainSubject) newErrors.mainSubject = "Main subject/degree is required";
    if (!form.hireSpeed) newErrors.hireSpeed = "Please select hiring timeline";
    if (!form.hireFrequency) newErrors.hireFrequency = "Please select hiring frequency";
    if (showStipend) {
      if (!form.stipendMin) newErrors.stipendMin = "Minimum stipend is required";
      if (!form.stipendMax) newErrors.stipendMax = "Maximum stipend is required";
    } else if (showSalary) {
      if (!form.salaryMin) newErrors.salaryMin = "Minimum salary is required";
      if (!form.salaryMax) newErrors.salaryMax = "Maximum salary is required";
    }
    errors = newErrors;
    return Object.keys(newErrors).length === 0;
  }

  function submitForm() {
    if (phoneInput) form.phone = phoneInput.getNumber();
    if (!validateForm()) {
      alert("Please fill all required fields correctly.");
      const firstError = document.querySelector(".error-msg");
      if (firstError) firstError.scrollIntoView({ behavior: "smooth", block: "center" });
      return;
    }
    console.log("Form Data:", form);
    alert("Hiring request submitted successfully! We'll contact you within 24 hours.");
  }

  // ========== DEPARTMENT DROPDOWN ==========
  function searchDepartment() {
    filteredDepartments = departmentSearch.trim() === ""
      ? departments
      : departments.filter(d => d.toLowerCase().includes(departmentSearch.toLowerCase()));
    showDepartmentDropdown = true;
  }
  function selectDepartment(dept) {
    form.department = dept; departmentSearch = dept;
    showDepartmentDropdown = false; clearTimeout(closeTimeout);
  }
  function addNewDepartment() {
    const newDept = departmentSearch.trim();
    if (newDept === "") return;
    if (!departments.includes(newDept)) {
      departments = [...departments, newDept];
      localStorage.setItem("departments", JSON.stringify(departments));
    }
    form.department = newDept; departmentSearch = newDept;
    showDepartmentDropdown = false; clearTimeout(closeTimeout);
  }
  function closeDropdownDelayed() { closeTimeout = setTimeout(() => { showDepartmentDropdown = false; }, 200); }
  function toggleDepartmentDropdown() {
    showDepartmentDropdown = !showDepartmentDropdown;
    if (showDepartmentDropdown) searchDepartment();
  }

  // ========== OUTSIDE CLICK HANDLERS ==========
  function handleClickOutside(event) {
    const facility = document.querySelector(".facility-multi-select");
    if (facility && !facility.contains(event.target)) showFacilityDropdown = false;
    const cert = document.querySelector(".cert-multi-select");
    if (cert && !cert.contains(event.target)) showCertDropdown = false;
    const skill = document.querySelector(".skills-multi-select");
    if (skill && !skill.contains(event.target)) showSkillsDropdown = false;
    const dept = document.querySelector(".dept-wrapper");
    if (dept && !dept.contains(event.target)) showDepartmentDropdown = false;
  }

  // ========== ON MOUNT ==========
  onMount(() => {
    const storedDepartments = localStorage.getItem("departments");
    if (storedDepartments) departments = JSON.parse(storedDepartments);
    loadSubjectData(); loadCustomLanguages(); loadCustomCertificates();
    loadCustomSkillsList(); loadFacilities();

    flatpickr("#deadline", { dateFormat: "d/m/Y", minDate: "today", allowInput: true });
    flatpickr("#joiningDate", { dateFormat: "d/m/Y", minDate: "today", allowInput: true });

    const input = document.querySelector("#phone");
    if (input) {
      phoneInput = intlTelInput(input, {
        initialCountry: "in",
        separateDialCode: true,
        preferredCountries: ["in", "us", "gb", "ae", "sg"],
        utilsScript: "https://cdn.jsdelivr.net/npm/intl-tel-input@18.1.1/build/js/utils.js"
      });
    }
    document.addEventListener("click", handleClickOutside);
    return () => document.removeEventListener("click", handleClickOutside);
  });
</script>

<div class="page-bg">
  <div class="page-wrapper">

    <!-- Header Banner -->
    <div class="form-header">
      <div class="header-icon">
        <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <rect x="2" y="7" width="20" height="14" rx="2"/><path d="M16 7V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v2"/>
          <line x1="12" y1="12" x2="12" y2="16"/><line x1="10" y1="14" x2="14" y2="14"/>
        </svg>
      </div>
      <div>
        <h1>Post a New Job Opening</h1>
        <p>Fill in the details below to find the perfect candidate</p>
      </div>
    </div>

    <form on:submit|preventDefault={submitForm} class="main-form">

      <!-- ── 1. COMPANY INFORMATION ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">01</span>
          <h2>Company Information</h2>
        </div>
        <div class="section-body">
          <div class="grid-2">
            <div class="field">
              <label>Company Name <span class="req">*</span></label>
              <input type="text" bind:value={form.company} class:err={errors.company} placeholder="e.g. Acme Technologies" />
              {#if errors.company}<span class="error-msg">{errors.company}</span>{/if}
            </div>
            <div class="field">
              <label>Contact Person <span class="req">*</span></label>
              <input type="text" bind:value={form.contact} class:err={errors.contact} placeholder="Full name" />
              {#if errors.contact}<span class="error-msg">{errors.contact}</span>{/if}
            </div>
          </div>
          <div class="grid-2">
            <div class="field">
              <label>Email Address <span class="req">*</span></label>
              <input type="email" bind:value={form.email} class:err={errors.email} placeholder="contact@company.com" />
              {#if errors.email}<span class="error-msg">{errors.email}</span>{/if}
            </div>
            <div class="field">
              <label>Phone Number <span class="req">*</span></label>
              <div class="phone-wrap">
                <input id="phone" type="tel" class:err={errors.phone} />
              </div>
              {#if errors.phone}<span class="error-msg">{errors.phone}</span>{/if}
            </div>
          </div>
          <div class="grid-2">
            <div class="field">
              <label>Company Website</label>
              <input type="url" bind:value={form.website} placeholder="https://company.com" />
            </div>
            <div class="field">
              <label>Company Size</label>
              <select bind:value={form.companySize}>
                <option value="">Select range</option>
                <option>1–10</option><option>11–50</option><option>51–200</option><option>201–500</option><option>500+</option>
              </select>
            </div>
          </div>
          <div class="field">
            <label>Brief Company Description</label>
            <textarea rows="3" bind:value={form.briefDetails} placeholder="Tell candidates a bit about your company culture, mission, and values…"></textarea>
          </div>
        </div>
      </section>

      <!-- ── 2. HIRING PREFERENCE ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">02</span>
          <h2>Hiring Preference</h2>
        </div>
        <div class="section-body">
          <div class="grid-2">
            <div class="field">
              <label>How soon do you want to fill this position? <span class="req">*</span></label>
              <div class="pill-group">
                <button type="button" class:active={form.hireSpeed === "immediate"} on:click={() => form.hireSpeed = "immediate"}>
                  ⚡ Immediately (1–2 weeks)
                </button>
                <button type="button" class:active={form.hireSpeed === "wait"} on:click={() => form.hireSpeed = "wait"}>
                  🕐 Can wait (3–4 weeks)
                </button>
              </div>
              {#if errors.hireSpeed}<span class="error-msg">{errors.hireSpeed}</span>{/if}
            </div>
            <div class="field">
              <label>How often do you need to hire? <span class="req">*</span></label>
              <select bind:value={form.hireFrequency} class:err={errors.hireFrequency}>
                <option value="">Select frequency</option>
                <option>One-time hire</option><option>Every Month</option>
                <option>Once in a few months</option><option>Not sure</option>
              </select>
              {#if errors.hireFrequency}<span class="error-msg">{errors.hireFrequency}</span>{/if}
            </div>
          </div>
        </div>
      </section>

      <!-- ── 3. JOB DETAILS ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">03</span>
          <h2>Job Details</h2>
        </div>
        <div class="section-body">
          <div class="grid-2">
            <div class="field">
              <label>Job Title <span class="req">*</span></label>
              <input type="text" bind:value={form.jobTitle} class:err={errors.jobTitle} placeholder="e.g. Senior Software Engineer" />
              {#if errors.jobTitle}<span class="error-msg">{errors.jobTitle}</span>{/if}
            </div>
            <div class="field dept-wrapper">
              <label>Department</label>
              <div class="combo-wrap">
                <input type="text" placeholder="Search or add department"
                  bind:value={departmentSearch}
                  on:input={searchDepartment}
                  on:focus={() => showDepartmentDropdown = true}
                  on:blur={closeDropdownDelayed} />
                <button type="button" class="caret-btn" on:click={toggleDepartmentDropdown}>{showDepartmentDropdown ? '▲' : '▼'}</button>
                {#if showDepartmentDropdown}
                  <div class="float-dropdown">
                    {#each filteredDepartments as dept}
                      <div class="dd-item" on:mousedown|preventDefault={() => selectDepartment(dept)}>{dept}</div>
                    {/each}
                    {#if filteredDepartments.length === 0 && departmentSearch.trim()}
                      <div class="dd-item dd-add" on:mousedown|preventDefault={addNewDepartment}>+ Add "{departmentSearch}"</div>
                    {/if}
                  </div>
                {/if}
              </div>
            </div>
          </div>
          <div class="grid-2">
            <div class="field">
              <label>Job Location <span class="req">*</span></label>
              <input type="text" bind:value={form.location} class:err={errors.location} placeholder="e.g. Ahmedabad, Gujarat" />
              {#if errors.location}<span class="error-msg">{errors.location}</span>{/if}
            </div>
            <div class="field">
              <label>Work Mode <span class="req">*</span></label>
              <select bind:value={form.workMode} class:err={errors.workMode}>
                <option value="">Select mode</option>
                <option>Onsite</option><option>Remote</option><option>Hybrid</option>
              </select>
              {#if errors.workMode}<span class="error-msg">{errors.workMode}</span>{/if}
            </div>
          </div>
          <div class="grid-3">
            <div class="field">
              <label>Employment Type <span class="req">*</span></label>
              <select bind:value={form.employmentType} class:err={errors.employmentType}>
                <option value="">Select type</option>
                <option>Full-time</option><option>Part-time</option><option>Contract</option><option>Internship</option>
              </select>
              {#if errors.employmentType}<span class="error-msg">{errors.employmentType}</span>{/if}
            </div>
            <div class="field">
              <label>Position Type</label>
              <select bind:value={form.positionType}>
                <option value="">Select type</option>
                <option>Skilled</option><option>Semi Skilled</option><option>Unskilled</option>
              </select>
            </div>
            <div class="field">
              <label>No. of Positions</label>
              <input type="number" bind:value={form.positions} min="1" placeholder="1" />
            </div>
          </div>
        </div>
      </section>

      <!-- ── 4. EDUCATION & EXPERIENCE ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">04</span>
          <h2>Education &amp; Experience</h2>
        </div>
        <div class="section-body">

          <!-- Degree Selection -->
          <div class="grid-2">
            <div class="field">
              <label>Main Subject / Degree <span class="req">*</span></label>
              <div class="add-select-row">
                <select bind:value={selectedMainSubject} on:change={(e) => onMainSubjectChange(e.target.value)} class:err={errors.mainSubject}>
                  <option value="">— Select Degree —</option>
                  {#each mainSubjects as s}<option value={s}>{s}</option>{/each}
                </select>
                <button type="button" class="add-chip-btn" on:click={() => showNewMainSubjectInput = !showNewMainSubjectInput}>+ Add</button>
              </div>
              {#if errors.mainSubject}<span class="error-msg">{errors.mainSubject}</span>{/if}
              {#if showNewMainSubjectInput}
                <div class="inline-add-row">
                  <input type="text" bind:value={newMainSubjectInput} placeholder="e.g. PhD" />
                  <button type="button" class="save-btn" on:click={addNewMainSubject}>Save</button>
                  <button type="button" class="cancel-btn" on:click={() => showNewMainSubjectInput = false}>Cancel</button>
                </div>
              {/if}
            </div>

            {#if selectedMainSubject}
              <div class="field">
                <label>Specialization / Sub-Subject</label>
                <div class="add-select-row">
                  <select bind:value={selectedSubSubject}>
                    <option value="">— Select Specialization —</option>
                    {#each subSubjectsMap[selectedMainSubject] || [] as sub}<option value={sub}>{sub}</option>{/each}
                  </select>
                  <button type="button" class="add-chip-btn" on:click={() => showNewSubSubjectInput = !showNewSubSubjectInput}>+ Add</button>
                </div>
                {#if showNewSubSubjectInput}
                  <div class="inline-add-row">
                    <input type="text" bind:value={newSubSubjectInput} placeholder="e.g. Quantum Physics" />
                    <button type="button" class="save-btn" on:click={addNewSubSubject}>Save</button>
                    <button type="button" class="cancel-btn" on:click={() => showNewSubSubjectInput = false}>Cancel</button>
                  </div>
                {/if}
              </div>
            {/if}
          </div>

          <!-- Experience Type -->
          <div class="field">
            <label>Experience Required <span class="req">*</span></label>
            <div class="pill-group trio">
              <button type="button" class:active={form.expType === "any"} on:click={() => form.expType = "any"}>Any Experience</button>
              <button type="button" class:active={form.expType === "fresher"} on:click={() => form.expType = "fresher"}>Fresher Only</button>
              <button type="button" class:active={form.expType === "experienced"} on:click={() => form.expType = "experienced"}>Experienced Only</button>
            </div>
          </div>

          {#if form.expType === "any"}
            <div class="info-pill">ℹ Both freshers and experienced candidates can apply.</div>
            <div class="field half-width">
              <label>Preferred Experience</label>
              <select bind:value={form.anyExperience}>
                <option value="">Select preferred range</option>
                <option>Fresher</option><option>0–1 Years</option><option>1–2 Years</option>
                <option>2–3 Years</option><option>3–5 Years</option><option>5+ Years</option>
              </select>
            </div>
          {:else if form.expType === "fresher"}
            <div class="info-pill">ℹ Only fresher candidates will be considered for this role.</div>
          {:else if form.expType === "experienced"}
            <div class="grid-2 half-mt">
              <div class="field">
                <label>Minimum Experience</label>
                <select bind:value={form.minExp}>
                  <option value="">Select min</option>
                  {#each ["1 Year","2 Years","3 Years","4 Years","5 Years","6 Years","7 Years","8 Years","9 Years","10 Years"] as y}<option>{y}</option>{/each}
                </select>
              </div>
              <div class="field">
                <label>Maximum Experience</label>
                <select bind:value={form.maxExp}>
                  <option value="">Select max</option>
                  {#each ["2 Years","3 Years","4 Years","5 Years","6 Years","7 Years","8 Years","9 Years","10 Years","12 Years","15 Years"] as y}<option>{y}</option>{/each}
                </select>
              </div>
            </div>
          {/if}

          <!-- Compensation -->
          <div class="comp-card">
            <div class="comp-card-header">💰 Compensation</div>
            {#if showStipend}
              <div class="grid-2">
                <div class="field">
                  <label>Min. Stipend (₹) <span class="req">*</span></label>
                  <input type="number" bind:value={form.stipendMin} class:err={errors.stipendMin} placeholder="e.g. 8000" />
                  {#if errors.stipendMin}<span class="error-msg">{errors.stipendMin}</span>{/if}
                </div>
                <div class="field">
                  <label>Max. Stipend (₹) <span class="req">*</span></label>
                  <input type="number" bind:value={form.stipendMax} class:err={errors.stipendMax} placeholder="e.g. 15000" />
                  {#if errors.stipendMax}<span class="error-msg">{errors.stipendMax}</span>{/if}
                </div>
              </div>
            {/if}
            {#if showSalary}
              <div class="grid-2">
                <div class="field">
                  <label>Min. Salary (₹/yr) <span class="req">*</span></label>
                  <input type="number" bind:value={form.salaryMin} class:err={errors.salaryMin} placeholder="e.g. 300000" />
                  {#if errors.salaryMin}<span class="error-msg">{errors.salaryMin}</span>{/if}
                </div>
                <div class="field">
                  <label>Max. Salary (₹/yr) <span class="req">*</span></label>
                  <input type="number" bind:value={form.salaryMax} class:err={errors.salaryMax} placeholder="e.g. 600000" />
                  {#if errors.salaryMax}<span class="error-msg">{errors.salaryMax}</span>{/if}
                </div>
              </div>
              <div class="field">
                <label>Performance Bonus / Incentives?</label>
                <div class="pill-group small">
                  <button type="button" class:active={form.bonus === "yes"} on:click={() => form.bonus = "yes"}>✓ Yes</button>
                  <button type="button" class:active={form.bonus === "no"} on:click={() => form.bonus = "no"}>✗ No</button>
                </div>
              </div>
            {/if}
          </div>

          <!-- Certificates Multi-Select -->
          <div class="field">
            <label>Certificates / Courses Required</label>
            <div class="multi-select cert-multi-select">
              <div class="ms-box" on:click={() => showCertDropdown = true}>
                <div class="ms-tags">
                  {#each selectedCertificates as item}
                    <span class="ms-tag">{item}<span class="ms-remove" on:click|stopPropagation={() => toggleCertificate(item)}>×</span></span>
                  {/each}
                  <input type="text" placeholder={selectedCertificates.length ? "" : "Search certificates…"}
                         bind:value={certSearchTerm} on:focus={() => showCertDropdown = true} on:keydown={handleCertKey} />
                </div>
                <button type="button" class="caret-btn" on:click|stopPropagation={() => showCertDropdown = !showCertDropdown}>{showCertDropdown ? '▲' : '▼'}</button>
              </div>
              {#if showCertDropdown}
                <div class="ms-dropdown">
                  {#each filteredCertificates as item, i}
                    <div class="ms-item" class:ms-active={certHighlightedIndex === i} on:mousedown={() => toggleCertificate(item)}>
                      <input type="checkbox" checked={selectedCertificates.includes(item)} readonly /> {item}
                    </div>
                  {/each}
                  {#if filteredCertificates.length === 0}<div class="ms-empty">No matches found</div>{/if}
                  {#if certSearchTerm && !allCertificates.includes(certSearchTerm)}
                    <div class="ms-add-new" on:mousedown={() => addCertificate(certSearchTerm)}>➕ Add "{certSearchTerm}"</div>
                  {/if}
                </div>
              {/if}
            </div>
          </div>

          <!-- Skills Multi-Select -->
          <div class="field">
            <label>Skills Required</label>
            <div class="multi-select skills-multi-select">
              <div class="ms-box" on:click={() => showSkillsDropdown = true}>
                <div class="ms-tags">
                  {#each selectedSkillsList as item}
                    <span class="ms-tag">{item}<span class="ms-remove" on:click|stopPropagation={() => toggleSkill(item)}>×</span></span>
                  {/each}
                  <input type="text" placeholder={selectedSkillsList.length ? "" : "Search skills…"}
                         bind:value={skillsSearchTerm} on:focus={() => showSkillsDropdown = true} on:keydown={handleSkillsKey} />
                </div>
                <button type="button" class="caret-btn" on:click|stopPropagation={() => showSkillsDropdown = !showSkillsDropdown}>{showSkillsDropdown ? '▲' : '▼'}</button>
              </div>
              {#if showSkillsDropdown}
                <div class="ms-dropdown">
                  {#each filteredSkills as item, i}
                    <div class="ms-item" class:ms-active={skillsHighlightedIndex === i} on:mousedown={() => toggleSkill(item)}>
                      <input type="checkbox" checked={selectedSkillsList.includes(item)} readonly /> {item}
                    </div>
                  {/each}
                  {#if filteredSkills.length === 0}<div class="ms-empty">No matches found</div>{/if}
                  {#if skillsSearchTerm && !allSkills.includes(skillsSearchTerm)}
                    <div class="ms-add-new" on:mousedown={() => addSkill(skillsSearchTerm)}>➕ Add "{skillsSearchTerm}"</div>
                  {/if}
                </div>
              {/if}
            </div>
          </div>

          <div class="field">
            <label>Additional Skills (free text)</label>
            <textarea rows="2" bind:value={form.skills} placeholder="Any other skills, tools or technologies not listed above…"></textarea>
          </div>

          <!-- Languages -->
          <div class="field">
            <label>Languages Required</label>
            <div class="lang-grid">
              {#each defaultLanguages as lang}
                <label class="lang-chip" class:lang-selected={selectedLanguages.includes(lang)}>
                  <input type="checkbox" value={lang} bind:group={selectedLanguages} />
                  {lang}
                </label>
              {/each}
              {#each customLanguages as lang}
                <label class="lang-chip lang-custom" class:lang-selected={selectedLanguages.includes(lang)}>
                  <input type="checkbox" value={lang} bind:group={selectedLanguages} />
                  {lang}
                  <button type="button" class="lang-remove" on:click={() => removeCustomLanguage(lang)}>×</button>
                </label>
              {/each}
              <button type="button" class="add-chip-btn" on:click={() => showNewLanguageInput = !showNewLanguageInput}>+ Add</button>
            </div>
            {#if showNewLanguageInput}
              <div class="inline-add-row">
                <input type="text" bind:value={newLanguageInput} placeholder="e.g. Japanese" />
                <button type="button" class="save-btn" on:click={addNewLanguage}>Save</button>
                <button type="button" class="cancel-btn" on:click={() => showNewLanguageInput = false}>Cancel</button>
              </div>
            {/if}
          </div>

        </div>
      </section>

      <!-- ── 5. JOB DESCRIPTION ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">05</span>
          <h2>Job Description</h2>
        </div>
        <div class="section-body">
          <div class="field">
            <label>Job Summary / Role Overview</label>
            <textarea rows="3" bind:value={form.summary} placeholder="Briefly describe the role, its purpose and the team it belongs to…"></textarea>
          </div>
          <div class="field">
            <label>Key Responsibilities</label>
            <textarea rows="4" bind:value={form.responsibilities} placeholder="• Lead development of new features&#10;• Collaborate with cross-functional teams&#10;• Mentor junior developers"></textarea>
          </div>
          <div class="field">
            <label>Requirements &amp; Qualifications</label>
            <textarea rows="4" bind:value={form.requirements} placeholder="• Bachelor's degree in relevant field&#10;• 3+ years of experience&#10;• Strong communication skills"></textarea>
          </div>
        </div>
      </section>

      <!-- ── 6. AGE CRITERIA ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">06</span>
          <h2>Age Criteria</h2>
        </div>
        <div class="section-body">
          <div class="grid-2 half-width-grid">
            <div class="field">
              <label>Minimum Age</label>
              <input type="number" bind:value={form.minAge} min="18" placeholder="e.g. 21" />
            </div>
            <div class="field">
              <label>Maximum Age</label>
              <input type="number" bind:value={form.maxAge} placeholder="e.g. 45" />
            </div>
          </div>
        </div>
      </section>

      <!-- ── 7. FACILITIES & BENEFITS ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">07</span>
          <h2>Facilities &amp; Benefits</h2>
        </div>
        <div class="section-body">

          <!-- Benefits Multi-Select -->
          <div class="field">
            <label>Benefits &amp; Perks</label>
            <div class="multi-select facility-multi-select">
              <div class="ms-box" on:click={() => showFacilityDropdown = true}>
                <div class="ms-tags">
                  {#each selectedFacilities as item}
                    <span class="ms-tag">{item}<span class="ms-remove" on:click|stopPropagation={() => toggleFacility(item)}>×</span></span>
                  {/each}
                  <input type="text" placeholder={selectedFacilities.length ? "" : "Search or add benefits…"}
                         bind:value={facilitySearchTerm} on:focus={() => showFacilityDropdown = true} on:keydown={handleFacilityKey} />
                </div>
                <button type="button" class="caret-btn" on:click|stopPropagation={() => showFacilityDropdown = !showFacilityDropdown}>{showFacilityDropdown ? '▲' : '▼'}</button>
              </div>
              {#if showFacilityDropdown}
                <div class="ms-dropdown">
                  {#each filteredFacilities as item, i}
                    <div class="ms-item" class:ms-active={facilityHighlightedIndex === i} on:mousedown={() => toggleFacility(item)}>
                      <input type="checkbox" checked={selectedFacilities.includes(item)} readonly /> {item}
                    </div>
                  {/each}
                  {#if filteredFacilities.length === 0}<div class="ms-empty">No matches</div>{/if}
                  {#if facilitySearchTerm && !allFacilities.includes(facilitySearchTerm)}
                    <div class="ms-add-new" on:mousedown={() => addFacility(facilitySearchTerm)}>➕ Add "{facilitySearchTerm}"</div>
                  {/if}
                </div>
              {/if}
            </div>
          </div>

          <!-- Accommodation -->
          <div class="sub-block">
            <div class="sub-block-title">🏠 Accommodation</div>
            <div class="radio-row">
              <label class="radio-chip" class:radio-active={form.accommodationType === "sharing"}><input type="radio" value="sharing" bind:group={form.accommodationType} /> Sharing</label>
              <label class="radio-chip" class:radio-active={form.accommodationType === "separateRoom"}><input type="radio" value="separateRoom" bind:group={form.accommodationType} /> Separate Room</label>
              <label class="radio-chip" class:radio-active={form.accommodationType === "quarter"}><input type="radio" value="quarter" bind:group={form.accommodationType} /> Quarter</label>
              <label class="radio-chip" class:radio-active={form.accommodationType === "none"}><input type="radio" value="none" bind:group={form.accommodationType} /> Not Provided</label>
            </div>
          </div>

          <!-- Transportation -->
          <div class="sub-block">
            <div class="sub-block-title">🚌 Transportation</div>
            <div class="radio-row">
              <label class="radio-chip" class:radio-active={form.transportType === "free"}><input type="radio" value="free" bind:group={form.transportType} /> Free</label>
              <label class="radio-chip" class:radio-active={form.transportType === "chargeable"}><input type="radio" value="chargeable" bind:group={form.transportType} /> Chargeable</label>
              <label class="radio-chip" class:radio-active={form.transportType === "none"}><input type="radio" value="none" bind:group={form.transportType} /> Not Provided</label>
            </div>
            {#if form.transportType && form.transportType !== "none"}
              <div class="field" style="margin-top: 12px;">
                <label>Route</label>
                <input type="text" bind:value={form.transportRoute} placeholder="e.g. City Centre → Office Campus" />
              </div>
            {/if}
            <div class="field" style="margin-top: 12px;">
              <label>Other Transport Details</label>
              <input type="text" bind:value={form.transportOther} placeholder="Any additional transport information…" />
            </div>
          </div>

        </div>
      </section>

      <!-- ── 8. HIRING TIMELINE ── -->
      <section class="form-section">
        <div class="section-header">
          <span class="section-number">08</span>
          <h2>Hiring Timeline</h2>
        </div>
        <div class="section-body">
          <div class="grid-2 half-width-grid">
            <div class="field">
              <label>Application Deadline</label>
              <input id="deadline" type="text" bind:value={form.deadline} placeholder="DD/MM/YYYY" readonly />
            </div>
            <div class="field">
              <label>Expected Joining Date</label>
              <input id="joiningDate" type="text" bind:value={form.joiningDate} placeholder="DD/MM/YYYY" readonly />
            </div>
          </div>
        </div>
      </section>

      <!-- Submit -->
      <div class="submit-row">
        <button type="submit" class="submit-btn">
          <span>Post Job &amp; Find Talent</span>
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="5" y1="12" x2="19" y2="12"/><polyline points="12 5 19 12 12 19"/></svg>
        </button>
      </div>

    </form>
  </div>
</div>

<style>
  @import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600;700&family=DM+Mono:wght@400;500&display=swap');

  /* ── CSS Variables ── */
  :global(*) { box-sizing: border-box; margin: 0; padding: 0; }
  :global(body) { font-family: 'DM Sans', sans-serif; background: #f0f4f8; }

  /* ── Page background ── */
  .page-bg {
    min-height: 100vh;
    background: linear-gradient(160deg, #eef2ff 0%, #f0f9ff 50%, #f8fafc 100%);
    padding: 32px 16px 60px;
  }

  .page-wrapper {
    max-width: 860px;
    margin: 0 auto;
  }

  /* ── Header ── */
  .form-header {
    display: flex;
    align-items: center;
    gap: 16px;
    background: linear-gradient(135deg, #1e3a8a 0%, #2563eb 100%);
    color: white;
    padding: 24px 32px;
    border-radius: 16px 16px 0 0;
    box-shadow: 0 4px 20px rgba(37, 99, 235, 0.3);
  }
  .header-icon {
    width: 52px; height: 52px;
    background: rgba(255,255,255,0.15);
    border-radius: 14px;
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
  }
  .form-header h1 { font-size: 1.45rem; font-weight: 700; line-height: 1.2; }
  .form-header p { font-size: 0.875rem; opacity: 0.8; margin-top: 3px; }

  /* ── Main Form ── */
  .main-form {
    background: #ffffff;
    border-radius: 0 0 16px 16px;
    box-shadow: 0 8px 30px rgba(0,0,0,0.07);
    overflow: hidden;
  }

  /* ── Sections ── */
  .form-section {
    border-bottom: 1px solid #f1f5f9;
  }
  .form-section:last-of-type { border-bottom: none; }

  .section-header {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 20px 28px 0;
  }
  .section-number {
    font-family: 'DM Mono', monospace;
    font-size: 0.7rem;
    font-weight: 500;
    color: #2563eb;
    background: #eff6ff;
    border: 1px solid #bfdbfe;
    border-radius: 6px;
    padding: 3px 8px;
    letter-spacing: 0.05em;
    flex-shrink: 0;
  }
  .section-header h2 {
    font-size: 1rem;
    font-weight: 650;
    color: #0f172a;
  }

  .section-body {
    padding: 16px 28px 24px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  /* ── Grids ── */
  .grid-2 {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
  }
  .grid-3 {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 16px;
  }
  .half-width-grid { max-width: 560px; }

  /* ── Fields ── */
  .field {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  .half-width { max-width: 380px; }
  .half-mt { margin-top: 4px; }

  label {
    font-size: 0.8rem;
    font-weight: 600;
    color: #374151;
    letter-spacing: 0.01em;
  }
  .req { color: #ef4444; }

  /* ── Base Inputs ── */
  input:not([type="checkbox"]):not([type="radio"]):not(.iti__tel-input),
  select,
  textarea {
    height: 42px;
    padding: 0 12px;
    border: 1.5px solid #e2e8f0;
    border-radius: 9px;
    font-size: 0.875rem;
    font-family: 'DM Sans', sans-serif;
    color: #0f172a;
    background: #fafbfc;
    transition: border-color 0.18s, box-shadow 0.18s, background 0.18s;
    width: 100%;
    appearance: none;
    -webkit-appearance: none;
  }
  select {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%236b7280' d='M6 8L1 3h10z'/%3E%3C/svg%3E");
    background-repeat: no-repeat;
    background-position: right 12px center;
    padding-right: 36px;
  }
  textarea {
    height: auto;
    padding: 10px 12px;
    resize: vertical;
    line-height: 1.55;
  }
  input:focus, select:focus, textarea:focus {
    outline: none;
    border-color: #3b82f6;
    background: #fff;
    box-shadow: 0 0 0 3px rgba(59,130,246,0.12);
  }
  .err { border-color: #f87171 !important; background: #fff8f8 !important; }
  .error-msg { font-size: 0.72rem; color: #ef4444; font-weight: 500; margin-top: 2px; }

  /* ── Phone Input ── */
  .phone-wrap { width: 100%; }
  .phone-wrap :global(.iti) { width: 100%; }
  .phone-wrap :global(.iti__flag-container) { z-index: 3; }
  .phone-wrap :global(input.iti__tel-input),
  .phone-wrap :global(#phone) {
    height: 42px;
    width: 100%;
    padding-left: 90px;
    border: 1.5px solid #e2e8f0;
    border-radius: 9px;
    font-size: 0.875rem;
    font-family: 'DM Sans', sans-serif;
    background: #fafbfc;
    transition: border-color 0.18s, box-shadow 0.18s;
  }
  .phone-wrap :global(#phone:focus) {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59,130,246,0.12);
    background: #fff;
  }

  /* ── Pill / Button Groups ── */
  .pill-group {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }
  .pill-group button {
    padding: 9px 18px;
    border-radius: 50px;
    border: 1.5px solid #e2e8f0;
    background: #f8fafc;
    font-size: 0.82rem;
    font-weight: 500;
    font-family: 'DM Sans', sans-serif;
    color: #475569;
    cursor: pointer;
    transition: all 0.18s;
    white-space: nowrap;
  }
  .pill-group button:hover { border-color: #93c5fd; background: #eff6ff; color: #2563eb; }
  .pill-group button.active {
    background: #2563eb;
    border-color: #2563eb;
    color: #fff;
    box-shadow: 0 3px 10px rgba(37,99,235,0.25);
  }
  .pill-group.trio button { flex: 1; text-align: center; min-width: 130px; }
  .pill-group.small button { padding: 7px 20px; }

  /* ── Compensation Card ── */
  .comp-card {
    background: linear-gradient(135deg, #f0f9ff, #eff6ff);
    border: 1.5px solid #bfdbfe;
    border-radius: 12px;
    padding: 16px 20px;
    display: flex;
    flex-direction: column;
    gap: 14px;
  }
  .comp-card-header {
    font-size: 0.875rem;
    font-weight: 700;
    color: #1d4ed8;
    letter-spacing: 0.01em;
  }

  /* ── Info Pill ── */
  .info-pill {
    background: #f0fdf4;
    border: 1px solid #bbf7d0;
    border-radius: 8px;
    padding: 9px 14px;
    font-size: 0.8rem;
    color: #166534;
    font-weight: 500;
  }

  /* ── Combo Input (Department) ── */
  .combo-wrap {
    position: relative;
    display: flex;
    gap: 0;
  }
  .combo-wrap input {
    border-radius: 9px 0 0 9px;
    border-right: none;
    flex: 1;
  }
  .combo-wrap input:focus { z-index: 1; }
  .caret-btn {
    height: 42px;
    padding: 0 13px;
    border: 1.5px solid #e2e8f0;
    border-left: none;
    border-radius: 0 9px 9px 0;
    background: #f1f5f9;
    font-size: 0.7rem;
    color: #2563eb;
    cursor: pointer;
    transition: background 0.15s;
    flex-shrink: 0;
  }
  .caret-btn:hover { background: #e2e8f0; }
  .float-dropdown {
    position: absolute;
    top: calc(100% + 4px);
    left: 0; right: 0;
    background: #fff;
    border: 1.5px solid #e2e8f0;
    border-radius: 10px;
    box-shadow: 0 8px 24px rgba(0,0,0,0.1);
    max-height: 200px;
    overflow-y: auto;
    z-index: 50;
  }
  .dd-item {
    padding: 9px 14px;
    font-size: 0.85rem;
    cursor: pointer;
    color: #374151;
  }
  .dd-item:hover { background: #eff6ff; color: #1d4ed8; }
  .dd-add { color: #2563eb; font-weight: 600; border-top: 1px solid #f1f5f9; }

  /* ── Multi-Select ── */
  .multi-select { position: relative; }
  .ms-box {
    min-height: 42px;
    border: 1.5px solid #e2e8f0;
    border-radius: 9px;
    padding: 5px 6px 5px 8px;
    display: flex;
    align-items: flex-start;
    background: #fafbfc;
    gap: 6px;
    cursor: text;
    transition: border-color 0.18s, box-shadow 0.18s;
  }
  .ms-box:focus-within {
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59,130,246,0.12);
    background: #fff;
  }
  .ms-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 5px;
    flex: 1;
    align-items: center;
    min-height: 30px;
  }
  .ms-tags input {
    height: 28px;
    border: none;
    padding: 0 4px;
    background: transparent;
    font-size: 0.85rem;
    min-width: 100px;
    flex: 1;
    box-shadow: none !important;
  }
  .ms-tag {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    background: #2563eb;
    color: #fff;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 0.75rem;
    font-weight: 500;
    white-space: nowrap;
  }
  .ms-remove { cursor: pointer; font-size: 1rem; line-height: 1; opacity: 0.7; }
  .ms-remove:hover { opacity: 1; }
  .ms-dropdown {
    position: absolute;
    top: calc(100% + 4px);
    left: 0; right: 0;
    background: #fff;
    border: 1.5px solid #e2e8f0;
    border-radius: 10px;
    box-shadow: 0 8px 24px rgba(0,0,0,0.1);
    max-height: 210px;
    overflow-y: auto;
    z-index: 60;
  }
  .ms-item {
    display: flex;
    align-items: center;
    gap: 9px;
    padding: 8px 12px;
    font-size: 0.85rem;
    cursor: pointer;
    color: #374151;
  }
  .ms-item:hover, .ms-active { background: #eff6ff; color: #1d4ed8; }
  .ms-item input[type="checkbox"] { width: 14px; height: 14px; accent-color: #2563eb; pointer-events: none; }
  .ms-add-new {
    padding: 9px 12px;
    font-size: 0.82rem;
    color: #2563eb;
    font-weight: 600;
    cursor: pointer;
    border-top: 1px solid #f1f5f9;
    background: #f8fafc;
    text-align: center;
  }
  .ms-add-new:hover { background: #eff6ff; }
  .ms-empty { padding: 12px; text-align: center; color: #94a3b8; font-size: 0.8rem; }

  /* ── Language Grid ── */
  .lang-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 7px;
    align-items: center;
  }
  .lang-chip {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    padding: 5px 12px;
    border-radius: 30px;
    border: 1.5px solid #e2e8f0;
    background: #f8fafc;
    font-size: 0.8rem;
    font-weight: 500;
    color: #475569;
    cursor: pointer;
    transition: all 0.15s;
    user-select: none;
  }
  .lang-chip input[type="checkbox"] { display: none; }
  .lang-chip:hover { border-color: #93c5fd; background: #eff6ff; color: #2563eb; }
  .lang-selected { background: #dbeafe; border-color: #2563eb; color: #1d4ed8; }
  .lang-custom { border-style: dashed; }
  .lang-remove {
    background: none; border: none; color: #ef4444;
    font-size: 1rem; font-weight: bold; cursor: pointer;
    margin-left: 2px; padding: 0 2px;
  }

  /* ── Add Select Row ── */
  .add-select-row {
    display: flex;
    gap: 8px;
    align-items: center;
  }
  .add-select-row select { flex: 1; }
  .add-chip-btn {
    height: 42px;
    padding: 0 16px;
    border: 1.5px solid #bfdbfe;
    border-radius: 9px;
    background: #eff6ff;
    font-size: 0.8rem;
    font-weight: 600;
    color: #1d4ed8;
    cursor: pointer;
    white-space: nowrap;
    transition: all 0.15s;
    flex-shrink: 0;
  }
  .add-chip-btn:hover { background: #dbeafe; border-color: #2563eb; }

  /* ── Inline Add Row ── */
  .inline-add-row {
    display: flex;
    gap: 8px;
    align-items: center;
    margin-top: 8px;
  }
  .inline-add-row input { flex: 1; height: 38px; }
  .save-btn {
    height: 38px; padding: 0 16px;
    background: #2563eb; color: #fff;
    border: none; border-radius: 8px;
    font-size: 0.8rem; font-weight: 600;
    cursor: pointer; transition: background 0.15s;
    white-space: nowrap;
  }
  .save-btn:hover { background: #1d4ed8; }
  .cancel-btn {
    height: 38px; padding: 0 14px;
    background: #f1f5f9; color: #475569;
    border: 1.5px solid #e2e8f0; border-radius: 8px;
    font-size: 0.8rem; font-weight: 600;
    cursor: pointer; transition: background 0.15s;
    white-space: nowrap;
  }
  .cancel-btn:hover { background: #e2e8f0; }

  /* ── Sub Blocks (Accommodation, Transport) ── */
  .sub-block {
    background: #f8fafc;
    border: 1px solid #e2e8f0;
    border-radius: 10px;
    padding: 14px 16px;
  }
  .sub-block-title {
    font-size: 0.85rem;
    font-weight: 700;
    color: #1e40af;
    margin-bottom: 12px;
  }
  .radio-row {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }
  .radio-chip {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    padding: 7px 14px;
    border-radius: 30px;
    border: 1.5px solid #e2e8f0;
    background: #fff;
    font-size: 0.8rem;
    font-weight: 500;
    color: #475569;
    cursor: pointer;
    transition: all 0.15s;
    user-select: none;
  }
  .radio-chip input[type="radio"] { display: none; }
  .radio-chip:hover { border-color: #93c5fd; color: #2563eb; }
  .radio-active { background: #dbeafe; border-color: #2563eb; color: #1d4ed8; }

  /* ── Submit ── */
  .submit-row {
    padding: 24px 28px 32px;
    display: flex;
    justify-content: center;
  }
  .submit-btn {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    padding: 14px 44px;
    border-radius: 50px;
    border: none;
    background: linear-gradient(135deg, #1e3a8a, #2563eb);
    color: #fff;
    font-size: 0.95rem;
    font-weight: 700;
    font-family: 'DM Sans', sans-serif;
    letter-spacing: 0.02em;
    cursor: pointer;
    box-shadow: 0 6px 20px rgba(37, 99, 235, 0.35);
    transition: transform 0.2s, box-shadow 0.2s;
  }
  .submit-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 28px rgba(37, 99, 235, 0.45);
  }
  .submit-btn:active { transform: translateY(0); }

  /* ── Responsive ── */
  @media (max-width: 720px) {
    .page-bg { padding: 16px 10px 48px; }
    .form-header { padding: 20px 22px; border-radius: 12px 12px 0 0; }
    .form-header h1 { font-size: 1.2rem; }
    .section-header { padding: 18px 20px 0; }
    .section-body { padding: 14px 20px 20px; gap: 14px; }
    .grid-2, .grid-3 { grid-template-columns: 1fr; }
    .half-width-grid { max-width: 100%; }
    .pill-group.trio { flex-direction: column; }
    .pill-group.trio button { min-width: unset; }
    .submit-btn { padding: 13px 32px; font-size: 0.9rem; }
  }

  @media (max-width: 480px) {
    .form-header { gap: 12px; }
    .header-icon { width: 44px; height: 44px; border-radius: 10px; }
    .submit-row { padding: 20px 20px 28px; }
    .submit-btn { width: 100%; justify-content: center; }
    .add-select-row { flex-direction: column; align-items: stretch; }
    .add-chip-btn { width: 100%; justify-content: center; }
    .inline-add-row { flex-wrap: wrap; }
    .inline-add-row input { min-width: 100%; }
  }
  
</style>