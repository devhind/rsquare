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

  // ========== LOAD & SAVE FUNCTIONS ==========
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
    if (e.key === "ArrowDown") {
      e.preventDefault();
      facilityHighlightedIndex = (facilityHighlightedIndex + 1) % filteredFacilities.length;
    } else if (e.key === "ArrowUp") {
      e.preventDefault();
      facilityHighlightedIndex = (facilityHighlightedIndex - 1 + filteredFacilities.length) % filteredFacilities.length;
    } else if (e.key === "Enter") {
      e.preventDefault();
      if (facilityHighlightedIndex >= 0) {
        toggleFacility(filteredFacilities[facilityHighlightedIndex]);
      } else if (facilitySearchTerm.trim()) {
        addFacility(facilitySearchTerm);
      }
      facilitySearchTerm = "";
      showFacilityDropdown = false;
    } else if (e.key === "Escape") {
      showFacilityDropdown = false;
    }
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
    if (!allCertificates.includes(value)) {
      customCertificates = [...customCertificates, value];
      saveCustomCertificates();
    }
    if (!selectedCertificates.includes(value)) {
      selectedCertificates = [...selectedCertificates, value];
    }
    certSearchTerm = "";
    showCertDropdown = false;
  }

  function toggleCertificate(item) {
    if (selectedCertificates.includes(item)) {
      selectedCertificates = selectedCertificates.filter(i => i !== item);
    } else {
      selectedCertificates = [...selectedCertificates, item];
    }
  }

  function handleCertKey(e) {
    if (!showCertDropdown) return;
    if (e.key === "ArrowDown") {
      e.preventDefault();
      certHighlightedIndex = (certHighlightedIndex + 1) % filteredCertificates.length;
    } else if (e.key === "ArrowUp") {
      e.preventDefault();
      certHighlightedIndex = (certHighlightedIndex - 1 + filteredCertificates.length) % filteredCertificates.length;
    } else if (e.key === "Enter") {
      e.preventDefault();
      if (certHighlightedIndex >= 0) {
        toggleCertificate(filteredCertificates[certHighlightedIndex]);
      } else if (certSearchTerm.trim()) {
        addCertificate(certSearchTerm);
      }
      certSearchTerm = "";
      showCertDropdown = false;
    } else if (e.key === "Escape") {
      showCertDropdown = false;
    }
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
    if (!allSkills.includes(value)) {
      customSkillsList = [...customSkillsList, value];
      saveCustomSkillsList();
    }
    if (!selectedSkillsList.includes(value)) {
      selectedSkillsList = [...selectedSkillsList, value];
    }
    skillsSearchTerm = "";
    showSkillsDropdown = false;
  }

  function toggleSkill(item) {
    if (selectedSkillsList.includes(item)) {
      selectedSkillsList = selectedSkillsList.filter(i => i !== item);
    } else {
      selectedSkillsList = [...selectedSkillsList, item];
    }
  }

  function handleSkillsKey(e) {
    if (!showSkillsDropdown) return;
    if (e.key === "ArrowDown") {
      e.preventDefault();
      skillsHighlightedIndex = (skillsHighlightedIndex + 1) % filteredSkills.length;
    } else if (e.key === "ArrowUp") {
      e.preventDefault();
      skillsHighlightedIndex = (skillsHighlightedIndex - 1 + filteredSkills.length) % filteredSkills.length;
    } else if (e.key === "Enter") {
      e.preventDefault();
      if (skillsHighlightedIndex >= 0) {
        toggleSkill(filteredSkills[skillsHighlightedIndex]);
      } else if (skillsSearchTerm.trim()) {
        addSkill(skillsSearchTerm);
      }
      skillsSearchTerm = "";
      showSkillsDropdown = false;
    } else if (e.key === "Escape") {
      showSkillsDropdown = false;
    }
  }

  // ========== LANGUAGES FUNCTIONS (existing checkboxes) ==========
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
    newLanguageInput = "";
    showNewLanguageInput = false;
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
    newMainSubjectInput = "";
    showNewMainSubjectInput = false;
  }
  function addNewSubSubject() {
    if (!selectedMainSubject) {
      alert("Please select a main subject first");
      return;
    }
    const name = newSubSubjectInput.trim();
    if (name && !subSubjectsMap[selectedMainSubject].includes(name)) {
      subSubjectsMap[selectedMainSubject] = [...subSubjectsMap[selectedMainSubject], name];
      saveSubjectData();
      selectedSubSubject = name;
    }
    newSubSubjectInput = "";
    showNewSubSubjectInput = false;
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
    else if (!/^[^\s@]+@([^\s@.,]+\.)+[^\s@.,]{2,}$/.test(form.email)) newErrors.email = "Valid email is required";
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
      const firstError = document.querySelector(".error");
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
    form.department = dept;
    departmentSearch = dept;
    showDepartmentDropdown = false;
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
    showDepartmentDropdown = false;
    clearTimeout(closeTimeout);
  }
  function closeDropdownDelayed() {
    closeTimeout = setTimeout(() => { showDepartmentDropdown = false; }, 200);
  }
  function toggleDepartmentDropdown() {
    showDepartmentDropdown = !showDepartmentDropdown;
    if (showDepartmentDropdown) searchDepartment();
  }

  // ========== TOGGLE FUNCTIONS FOR OTHER DROPDOWNS ==========
  function toggleCertDropdown() {
    showCertDropdown = !showCertDropdown;
    if (showCertDropdown) certSearchTerm = ""; // optional reset
  }
  function toggleSkillsDropdown() {
    showSkillsDropdown = !showSkillsDropdown;
  }
  function toggleFacilityDropdown() {
    showFacilityDropdown = !showFacilityDropdown;
  }

  // ========== OUTSIDE CLICK HANDLERS FOR DROPDOWNS ==========
  function handleClickOutside(event) {
    const facility = document.querySelector(".facility-multi-select");
    if (facility && !facility.contains(event.target)) showFacilityDropdown = false;
    const cert = document.querySelector(".cert-multi-select");
    if (cert && !cert.contains(event.target)) showCertDropdown = false;
    const skill = document.querySelector(".skills-multi-select");
    if (skill && !skill.contains(event.target)) showSkillsDropdown = false;
    const dept = document.querySelector(".department-wrapper");
    if (dept && !dept.contains(event.target)) showDepartmentDropdown = false;
  }

  // ========== ON MOUNT ==========
  onMount(() => {
    const storedDepartments = localStorage.getItem("departments");
    if (storedDepartments) departments = JSON.parse(storedDepartments);
    loadSubjectData();
    loadCustomLanguages();
    loadCustomCertificates();
    loadCustomSkillsList();
    loadFacilities();

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
    return () => {
      document.removeEventListener("click", handleClickOutside);
    };
  });
</script>

<section class="container">
  <div class="form-card">
    <div class="form-header">
      <h1>Post a New Job Opening</h1>
      <p>Fill in the details below to find the perfect candidate</p>
    </div>

    <form on:submit|preventDefault={submitForm}>
      <!-- 1. COMPANY INFORMATION -->
      <fieldset>
        <legend>Company Information</legend>
        <div class="grid">
          <div class="field">
            <label>Company Name <span class="required">*</span></label>
            <input type="text" bind:value={form.company} class={errors.company ? "error-input" : ""} />
            {#if errors.company}<span class="error">{errors.company}</span>{/if}
          </div>
          <div class="field">
            <label>Contact Person <span class="required">*</span></label>
            <input type="text" bind:value={form.contact} class={errors.contact ? "error-input" : ""} />
            {#if errors.contact}<span class="error">{errors.contact}</span>{/if}
          </div>
          <div class="field">
            <label>Email <span class="required">*</span></label>
            <input type="email" bind:value={form.email} class={errors.email ? "error-input" : ""} />
            {#if errors.email}<span class="error">{errors.email}</span>{/if}
          </div>
          <div class="field">
            <label>Phone <span class="required">*</span></label>
            <input id="phone" type="tel" class={errors.phone ? "error-input" : ""} />
            {#if errors.phone}<span class="error">{errors.phone}</span>{/if}
          </div>
          <div class="field">
            <label>Company Website</label>
            <input type="url" bind:value={form.website} placeholder="https://example.com" />
          </div>
          <div class="field">
            <label>Company Size</label>
            <select bind:value={form.companySize}>
              <option value="">Select</option>
              <option>1-10</option><option>11-50</option><option>51-200</option><option>201-500</option><option>500+</option>
            </select>
          </div>
          <div class="field full-width">
            <label>Brief Details about Company</label>
            <textarea rows="2" bind:value={form.briefDetails} placeholder="Tell us a little about your company..."></textarea>
          </div>
        </div>
      </fieldset>

      <!-- 2. HIRING PREFERENCE -->
      <fieldset>
        <legend>Hiring Preference</legend>
        <div class="field">
          <label>How soon do you want to fill the position? <span class="required">*</span></label>
          <div class="button-group">
            <button type="button" class:selected={form.hireSpeed === "immediate"} on:click={() => form.hireSpeed = "immediate"}>Immediately (1-2 weeks)</button>
            <button type="button" class:selected={form.hireSpeed === "wait"} on:click={() => form.hireSpeed = "wait"}>Can wait (3-4 weeks)</button>
          </div>
          {#if errors.hireSpeed}<span class="error">{errors.hireSpeed}</span>{/if}
        </div>
        <div class="field">
          <label>How often do you need to hire? <span class="required">*</span></label>
          <select bind:value={form.hireFrequency} class={errors.hireFrequency ? "error-input" : ""}>
            <option value="">Select</option>
            <option>One-time hire</option><option>Every Month</option><option>Once in a few months</option><option>Not sure</option>
          </select>
          {#if errors.hireFrequency}<span class="error">{errors.hireFrequency}</span>{/if}
        </div>
      </fieldset>

      <!-- 3. JOB DETAILS -->
      <fieldset>
        <legend>Job Details</legend>
        <div class="grid">
          <div class="field">
            <label>Job Title <span class="required">*</span></label>
            <input type="text" bind:value={form.jobTitle} class={errors.jobTitle ? "error-input" : ""} />
            {#if errors.jobTitle}<span class="error">{errors.jobTitle}</span>{/if}
          </div>
          <div class="field department-wrapper">
            <label>Department</label>
            <div class="dropdown-container">
              <div class="dropdown-input-group">
                <input type="text" placeholder="Search or add department" bind:value={departmentSearch}
                  on:input={searchDepartment} on:focus={() => showDepartmentDropdown = true} on:blur={closeDropdownDelayed} />
                <button type="button" class="dropdown-toggle-btn" on:click={toggleDepartmentDropdown}>
                  {showDepartmentDropdown ? '▲' : '▼'}
                </button>
              </div>
              {#if showDepartmentDropdown}
                <div class="dropdown">
                  {#each filteredDepartments as dept}
                    <div class="dropdown-item" on:mousedown|preventDefault={() => selectDepartment(dept)}>{dept}</div>
                  {/each}
                  {#if filteredDepartments.length === 0 && departmentSearch.trim() !== ""}
                    <div class="dropdown-item add" on:mousedown|preventDefault={addNewDepartment}>+ Add "{departmentSearch}"</div>
                  {/if}
                </div>
              {/if}
            </div>
          </div>
          <div class="field">
            <label>Job Location <span class="required">*</span></label>
            <input type="text" bind:value={form.location} class={errors.location ? "error-input" : ""} />
            {#if errors.location}<span class="error">{errors.location}</span>{/if}
          </div>
          <div class="field">
            <label>Work Mode <span class="required">*</span></label>
            <select bind:value={form.workMode} class={errors.workMode ? "error-input" : ""}>
              <option value="">Select</option><option>Onsite</option><option>Remote</option><option>Hybrid</option>
            </select>
            {#if errors.workMode}<span class="error">{errors.workMode}</span>{/if}
          </div>
        </div>
        <div class="triple-row">
          <div class="field">
            <label>Employment Type <span class="required">*</span></label>
            <select bind:value={form.employmentType} class={errors.employmentType ? "error-input" : ""}>
              <option value="">Select</option><option>Full-time</option><option>Part-time</option><option>Contract</option><option>Internship</option>
            </select>
            {#if errors.employmentType}<span class="error">{errors.employmentType}</span>{/if}
          </div>
          <div class="field">
            <label>Position Type</label>
            <select bind:value={form.positionType}>
              <option value="">Select</option><option>Skilled</option><option>Semi Skilled</option><option>Unskilled</option>
            </select>
          </div>
          <div class="field">
            <label>Number of Positions</label>
            <input type="number" bind:value={form.positions} min="1" />
          </div>
        </div>
      </fieldset>

      <!-- 4. EDUCATIONAL QUALIFICATION & EXPERIENCE -->
      <fieldset>
        <legend>Educational Qualification & Experience</legend>
        <div class="field">
          <label>Main Subject / Degree <span class="required">*</span></label>
          <div class="subject-select-wrapper">
            <select bind:value={selectedMainSubject} on:change={(e) => onMainSubjectChange(e.target.value)} class={errors.mainSubject ? "error-input" : ""}>
              <option value="">-- Select Main Subject --</option>
              {#each mainSubjects as subject}<option value={subject}>{subject}</option>{/each}
            </select>
            <button type="button" class="add-btn" on:click={() => showNewMainSubjectInput = !showNewMainSubjectInput}>+ Add New</button>
          </div>
          {#if errors.mainSubject}<span class="error">{errors.mainSubject}</span>{/if}
          {#if showNewMainSubjectInput}
            <div class="inline-add">
              <input type="text" bind:value={newMainSubjectInput} placeholder="e.g. PhD" />
              <button on:click={addNewMainSubject}>Save</button>
              <button on:click={() => showNewMainSubjectInput = false}>Cancel</button>
            </div>
          {/if}
        </div>

        {#if selectedMainSubject}
          <div class="field">
            <label>Sub-Subject / Specialization</label>
            <div class="subject-select-wrapper">
              <select bind:value={selectedSubSubject}>
                <option value="">-- Select Sub-Subject --</option>
                {#each subSubjectsMap[selectedMainSubject] || [] as sub}<option value={sub}>{sub}</option>{/each}
              </select>
              <button type="button" class="add-btn" on:click={() => showNewSubSubjectInput = !showNewSubSubjectInput}>+ Add New</button>
            </div>
            {#if showNewSubSubjectInput}
              <div class="inline-add">
                <input type="text" bind:value={newSubSubjectInput} placeholder="e.g. Quantum Physics" />
                <button on:click={addNewSubSubject}>Save</button>
                <button on:click={() => showNewSubSubjectInput = false}>Cancel</button>
              </div>
            {/if}
          </div>
        {/if}

        <div class="field">
          <label>Total Experience Required <span class="required">*</span></label>
          <div class="button-group">
            <button type="button" class:selected={form.expType === "any"} on:click={() => form.expType = "any"}>Any Experience</button>
            <button type="button" class:selected={form.expType === "fresher"} on:click={() => form.expType = "fresher"}>Fresher Only</button>
            <button type="button" class:selected={form.expType === "experienced"} on:click={() => form.expType = "experienced"}>Experienced Only</button>
          </div>
        </div>

        {#if form.expType === "any"}
          <div class="info-box">Both freshers and experienced candidates can apply.</div>
          <div class="field">
            <label>Preferred Experience</label>
            <select bind:value={form.anyExperience}>
              <option value="">Select Experience</option>
              <option>Fresher</option><option>0-1 Years</option><option>1-2 Years</option><option>2-3 Years</option><option>3-5 Years</option><option>5+ Years</option>
            </select>
          </div>
        {:else if form.expType === "fresher"}
          <div class="info-box">Only Fresher candidates can apply.</div>
        {:else if form.expType === "experienced"}
          <div class="grid">
            <div class="field">
              <label>Minimum Experience <span class="required">*</span></label>
              <select bind:value={form.minExp}>
                <option value="">Select Min</option>
                <option>1 Year</option><option>2 Years</option><option>3 Years</option><option>4 Years</option><option>5 Years</option>
                <option>6 Years</option><option>7 Years</option><option>8 Years</option><option>9 Years</option><option>10 Years</option>
              </select>
            </div>
            <div class="field">
              <label>Maximum Experience <span class="required">*</span></label>
              <select bind:value={form.maxExp}>
                <option value="">Select Max</option>
                <option>2 Years</option><option>3 Years</option><option>4 Years</option><option>5 Years</option><option>6 Years</option>
                <option>7 Years</option><option>8 Years</option><option>9 Years</option><option>10 Years</option><option>12 Years</option><option>15 Years</option>
              </select>
            </div>
          </div>
        {/if}

        <!-- Dynamic Compensation -->
        <div class="compensation-section">
          <h3>💰 Compensation</h3>
          {#if showStipend}
            <div class="grid">
              <div class="field">
                <label>Minimum Stipend (₹) <span class="required">*</span></label>
                <input type="number" bind:value={form.stipendMin} class={errors.stipendMin ? "error-input" : ""} />
                {#if errors.stipendMin}<span class="error">{errors.stipendMin}</span>{/if}
              </div>
              <div class="field">
                <label>Maximum Stipend (₹) <span class="required">*</span></label>
                <input type="number" bind:value={form.stipendMax} class={errors.stipendMax ? "error-input" : ""} />
                {#if errors.stipendMax}<span class="error">{errors.stipendMax}</span>{/if}
              </div>
            </div>
          {/if}
          {#if showSalary}
            <div class="grid">
              <div class="field">
                <label>Minimum Salary (₹) <span class="required">*</span></label>
                <input type="number" bind:value={form.salaryMin} class={errors.salaryMin ? "error-input" : ""} />
                {#if errors.salaryMin}<span class="error">{errors.salaryMin}</span>{/if}
              </div>
              <div class="field">
                <label>Maximum Salary (₹) <span class="required">*</span></label>
                <input type="number" bind:value={form.salaryMax} class={errors.salaryMax ? "error-input" : ""} />
                {#if errors.salaryMax}<span class="error">{errors.salaryMax}</span>{/if}
              </div>
            </div>
            <div class="field">
              <label>Performance Bonus / Incentives?</label>
              <div class="toggle-group">
                <button type="button" class:selected={form.bonus === "yes"} on:click={() => form.bonus = "yes"}>Yes</button>
                <button type="button" class:selected={form.bonus === "no"} on:click={() => form.bonus = "no"}>No</button>
              </div>
            </div>
          {/if}
        </div>

        <!-- Certificates Dropdown Multi-Select with toggle button -->
        <div class="field">
          <label>Certificates / Courses</label>
          <div class="multi-select cert-multi-select">
            <div class="select-box" on:click={() => showCertDropdown = true}>
              <div class="tags">
                {#each selectedCertificates as item}
                  <span class="tag">
                    {item}
                    <span class="remove" on:click|stopPropagation={() => toggleCertificate(item)}>×</span>
                  </span>
                {/each}
                <input type="text" placeholder="Search or add certificate..." bind:value={certSearchTerm}
                       on:focus={() => showCertDropdown = true}
                       on:keydown={handleCertKey} />
              </div>
              <button type="button" class="dropdown-toggle-btn" on:click|stopPropagation={toggleCertDropdown}>
                {showCertDropdown ? '▲' : '▼'}
              </button>
            </div>
            {#if showCertDropdown}
              <div class="dropdown-box">
                {#if filteredCertificates.length > 0}
                  {#each filteredCertificates as item, i}
                    <div class="dropdown-item {certHighlightedIndex === i ? 'active' : ''}"
                         on:mousedown={() => toggleCertificate(item)}>
                      <input type="checkbox" checked={selectedCertificates.includes(item)} readonly />
                      {item}
                    </div>
                  {/each}
                {:else}
                  <div class="no-data">No match found</div>
                {/if}
                {#if certSearchTerm && !allCertificates.includes(certSearchTerm)}
                  <div class="add-new" on:mousedown={() => addCertificate(certSearchTerm)}>
                    ➕ Add "{certSearchTerm}"
                  </div>
                {/if}
              </div>
            {/if}
          </div>
        </div>

        <!-- Skills Dropdown Multi-Select with toggle button -->
        <div class="field">
          <label>Skills Required</label>
          <div class="multi-select skills-multi-select">
            <div class="select-box" on:click={() => showSkillsDropdown = true}>
              <div class="tags">
                {#each selectedSkillsList as item}
                  <span class="tag">
                    {item}
                    <span class="remove" on:click|stopPropagation={() => toggleSkill(item)}>×</span>
                  </span>
                {/each}
                <input type="text" placeholder="Search or add skill..." bind:value={skillsSearchTerm}
                       on:focus={() => showSkillsDropdown = true}
                       on:keydown={handleSkillsKey} />
              </div>
              <button type="button" class="dropdown-toggle-btn" on:click|stopPropagation={toggleSkillsDropdown}>
                {showSkillsDropdown ? '▲' : '▼'}
              </button>
            </div>
            {#if showSkillsDropdown}
              <div class="dropdown-box">
                {#if filteredSkills.length > 0}
                  {#each filteredSkills as item, i}
                    <div class="dropdown-item {skillsHighlightedIndex === i ? 'active' : ''}"
                         on:mousedown={() => toggleSkill(item)}>
                      <input type="checkbox" checked={selectedSkillsList.includes(item)} readonly />
                      {item}
                    </div>
                  {/each}
                {:else}
                  <div class="no-data">No match found</div>
                {/if}
                {#if skillsSearchTerm && !allSkills.includes(skillsSearchTerm)}
                  <div class="add-new" on:mousedown={() => addSkill(skillsSearchTerm)}>
                    ➕ Add "{skillsSearchTerm}"
                  </div>
                {/if}
              </div>
            {/if}
          </div>
        </div>

        <div class="field">
          <label>Additional Skills (free text)</label>
          <textarea rows="2" bind:value={form.skills} placeholder="Any other skills not listed above..."></textarea>
        </div>

        <!-- Languages (checkboxes) -->
        <div class="field">
          <label>Languages Required</label>
          <div class="checkbox-group">
            <div class="checkbox-row">
              {#each defaultLanguages as lang}
                <label class="checkbox-option">
                  <input type="checkbox" value={lang} bind:group={selectedLanguages} />
                  <span>{lang}</span>
                </label>
              {/each}
              {#each customLanguages as lang}
                <label class="checkbox-option custom-option">
                  <input type="checkbox" value={lang} bind:group={selectedLanguages} />
                  <span>{lang}</span>
                  <button type="button" class="remove-option" on:click={() => removeCustomLanguage(lang)}>×</button>
                </label>
              {/each}
              <button type="button" class="add-option-btn" on:click={() => showNewLanguageInput = !showNewLanguageInput}>+ Add Other</button>
            </div>
          </div>
          {#if showNewLanguageInput}
            <div class="inline-add">
              <input type="text" bind:value={newLanguageInput} placeholder="e.g. French" />
              <button on:click={addNewLanguage}>Save</button>
              <button on:click={() => showNewLanguageInput = false}>Cancel</button>
            </div>
          {/if}
        </div>
      </fieldset>

      <!-- 5. JOB DESCRIPTION -->
      <fieldset>
        <legend>Job Description</legend>
        <div class="field">
          <label>Job Summary / Functions</label>
          <textarea rows="2" bind:value={form.summary} placeholder="Brief overview of the role..."></textarea>
        </div>
        <div class="field">
          <label>Key Responsibilities</label>
          <textarea rows="2" bind:value={form.responsibilities} placeholder="• Lead development of new features&#10;• Collaborate with cross-functional teams"></textarea>
        </div>
        <div class="field">
          <label>Requirements & Qualifications</label>
          <textarea rows="2" bind:value={form.requirements} placeholder="• Bachelor's degree in relevant field&#10;• 3+ years of experience"></textarea>
        </div>
      </fieldset>

      <!-- 6. AGE -->
      <fieldset>
        <legend>Age Criteria</legend>
        <div class="grid">
          <div class="field">
            <label>Minimum Age</label>
            <input type="number" bind:value={form.minAge} min="18" />
          </div>
          <div class="field">
            <label>Maximum Age</label>
            <input type="number" bind:value={form.maxAge} />
          </div>
        </div>
      </fieldset>

      <!-- 7. FACILITIES & BENEFITS -->
      <fieldset>
        <legend>Facilities & Benefits</legend>
        <div class="field full-width">
          <label>Other Facilities</label>
          <div class="multi-select facility-multi-select">
            <div class="select-box" on:click={() => showFacilityDropdown = true}>
              <div class="tags">
                {#each selectedFacilities as item}
                  <span class="tag">
                    {item}
                    <span class="remove" on:click|stopPropagation={() => toggleFacility(item)}>×</span>
                  </span>
                {/each}
                <input type="text" placeholder="Search or add..." bind:value={facilitySearchTerm}
                       on:focus={() => showFacilityDropdown = true}
                       on:keydown={handleFacilityKey} />
              </div>
              <button type="button" class="dropdown-toggle-btn" on:click|stopPropagation={toggleFacilityDropdown}>
                {showFacilityDropdown ? '▲' : '▼'}
              </button>
            </div>
            {#if showFacilityDropdown}
              <div class="dropdown-box">
                {#if filteredFacilities.length > 0}
                  {#each filteredFacilities as item, i}
                    <div class="dropdown-item {facilityHighlightedIndex === i ? 'active' : ''}"
                         on:mousedown={() => toggleFacility(item)}>
                      <input type="checkbox" checked={selectedFacilities.includes(item)} readonly />
                      {item}
                    </div>
                  {/each}
                {:else}
                  <div class="no-data">No match found</div>
                {/if}
                {#if facilitySearchTerm && !allFacilities.includes(facilitySearchTerm)}
                  <div class="add-new" on:mousedown={() => addFacility(facilitySearchTerm)}>
                    ➕ Add "{facilitySearchTerm}"
                  </div>
                {/if}
              </div>
            {/if}
          </div>
        </div>

        <div class="sub-section">
          <h3>🏠 Accommodation</h3>
          <div class="field">
            <label>Type of Accommodation</label>
            <div class="radio-group">
              <label><input type="radio" value="sharing" bind:group={form.accommodationType} /> Sharing</label>
              <label><input type="radio" value="separateRoom" bind:group={form.accommodationType} /> Separate Room</label>
              <label><input type="radio" value="quarter" bind:group={form.accommodationType} /> Quarter</label>
              <label><input type="radio" value="none" bind:group={form.accommodationType} /> Not provided</label>
            </div>
          </div>
        </div>

        <div class="sub-section">
          <h3>🚌 Transportation</h3>
          <div class="grid">
            <div class="field">
              <label>Transport Type</label>
              <div class="radio-group">
                <label><input type="radio" value="free" bind:group={form.transportType} /> Free</label>
                <label><input type="radio" value="chargeable" bind:group={form.transportType} /> Chargeable</label>
                <label><input type="radio" value="none" bind:group={form.transportType} /> Not provided</label>
              </div>
            </div>
            {#if form.transportType && form.transportType !== "none"}
              <div class="field">
                <label>Transportation Route</label>
                <input type="text" bind:value={form.transportRoute} placeholder="e.g. City → Office" />
              </div>
            {/if}
            <div class="field full-width">
              <label>Other Transport Details</label>
              <input type="text" bind:value={form.transportOther} placeholder="Any other transport information" />
            </div>
          </div>
        </div>
      </fieldset>

      <!-- 8. HIRING TIMELINE -->
      <fieldset>
        <legend>Hiring Timeline</legend>
        <div class="grid">
          <div class="field">
            <label>Application Deadline</label>
            <input id="deadline" type="text" bind:value={form.deadline} placeholder="DD/MM/YYYY" />
          </div>
          <div class="field">
            <label>Expected Joining Date</label>
            <input id="joiningDate" type="text" bind:value={form.joiningDate} placeholder="DD/MM/YYYY" />
          </div>
        </div>
      </fieldset>

      <button type="submit" class="submit-btn">Post Job & Find Talent</button>
    </form>
  </div>
</section>

<style>
 /* ================= GLOBAL RESET ================= */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Poppins", sans-serif;
  background: #f1f5f9;
  overflow-x: hidden;
}

/* ================= MAIN CONTAINER ================= */
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;

  /* 🔥 Header space handled cleanly */
  padding: 20px 10px 20px;
}

/* ================= FORM CARD ================= */
.form-card {
  width: 100%;
  max-width: 1000px;

  background: #ffffff;
  border-radius: 18px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.08);
  overflow: hidden;
}

/* ================= HEADER ================= */
.form-header {
  background: linear-gradient(135deg, #1e3a8a, #2563eb);
  padding: 24px 28px;
  color: white;
}

.form-header h1 {
  font-size: 1.6rem;
  font-weight: 600;
}

.form-header p {
  font-size: 0.9rem;
  opacity: 0.9;
  margin-top: 4px;
}

/* ================= FORM ================= */
form {
  padding: 28px;
}

/* ================= FIELDSET ================= */
fieldset {
  border: none;
  margin-bottom: 28px;
}

legend {
  font-size: 1.1rem;
  font-weight: 600;
  color: #1e3a8a;
  margin-bottom: 16px;
  border-bottom: 1px solid #e2e8f0;
  padding-bottom: 6px;
}

/* ================= GRID ================= */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 16px;
}

.triple-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
}

/* ================= FIELD ================= */
.field {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.full-width {
  grid-column: 1 / -1;
}

label {
  font-size: 0.85rem;
  font-weight: 500;
  color: #334155;
}

.required {
  color: red;
}

/* ================= INPUT ================= */
input:not([type="checkbox"]):not([type="radio"]),
select,
textarea {
  padding: 10px 12px;
  border-radius: 10px;
  border: 1px solid #cbd5e1;
  font-size: 0.85rem;
  transition: 0.2s;
  width: 100%;
}

input:focus,
select:focus,
textarea:focus {
  outline: none;
  border-color: #2563eb;
  box-shadow: 0 0 0 2px rgba(37, 99, 235, 0.15);
}

/* ================= ERROR ================= */
.error {
  font-size: 0.75rem;
  color: red;
}

.error-input {
  border-color: red;
}

/* ================= BUTTON GROUP ================= */
.button-group {
  display: flex;
  gap: 8px;
}

.button-group button {
  flex: 1;
  padding: 8px;
  border-radius: 20px;
  border: 1px solid #cbd5e1;
  background: #f8fafc;
  cursor: pointer;
  font-size: 0.8rem;
  transition: 0.2s;
}

.button-group button.selected {
  background: #2563eb;
  color: white;
  border: none;
}

/* ================= MULTI SELECT ================= */
.multi-select {
  position: relative;
}

.select-box {
  border: 1px solid #cbd5e1;
  border-radius: 10px;
  padding: 6px;
  min-height: 38px;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  background: white;
}

.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  width: 100%;
}

.tags input {
  border: none;
  outline: none;
  flex: 1;
  font-size: 0.8rem;
}

/* TAG */
.tag {
  background: #2563eb;
  color: white;
  padding: 3px 8px;
  border-radius: 14px;
  font-size: 0.7rem;
  display: flex;
  align-items: center;
  gap: 4px;
}

.tag .remove {
  cursor: pointer;
  font-weight: bold;
}

/* DROPDOWN */
.dropdown-box {
  position: absolute;
  top: 105%;
  width: 260px;
  max-height: 180px;
  overflow-y: auto;

  background: white;
  border: 1px solid #cbd5e1;
  border-radius: 10px;
  z-index: 50;
  box-shadow: 0 8px 20px rgba(0,0,0,0.1);
}

.dropdown-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px 10px;
  font-size: 0.8rem;
  cursor: pointer;
}

.dropdown-item:hover,
.dropdown-item.active {
  background: #eff6ff;
}

/* FIX CHECKBOX */
.dropdown-item input[type="checkbox"] {
  width: 14px !important;
  height: 14px;
}

/* ================= RADIO ================= */
.radio-group {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.radio-group label {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 0.8rem;
}

/* ================= INFO BOX ================= */
.info-box {
  background: #eff6ff;
  padding: 10px;
  border-radius: 8px;
  font-size: 0.8rem;
  margin-bottom: 10px;
}

/* ================= SUBMIT BUTTON ================= */
.submit-btn {
  display: block;
  margin: 30px auto 0;
  width: 60%;
  max-width: 400px;

  padding: 12px;
  border-radius: 30px;
  border: none;

  background: linear-gradient(135deg, #1e3a8a, #2563eb);
  color: white;
  font-size: 0.9rem;
  font-weight: 500;

  cursor: pointer;
  transition: 0.3s;
}

.submit-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(37, 99, 235, 0.3);
}

/* ================= RESPONSIVE ================= */
@media (max-width: 768px) {
  .container {
    padding: 100px 12px 30px;
  }

  .form-card {
    border-radius: 12px;
  }

  form {
    padding: 20px;
  }

  .submit-btn {
    width: 100%;
  }
}
</style>