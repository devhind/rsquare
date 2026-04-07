<script>
  import { onMount } from "svelte";
  import { goto, afterNavigate } from "$app/navigation";

  // Menu and dropdown states
  let isMenuOpen = false;
  let showActions = false;

  // Desktop dropdown – track which dropdown is currently open (or null)
  let activeDropdown = null;

  // Timeout variables for hover delays
  let openTimeout = null;
  let closeTimeout = null;

  // Mobile sidebar accordion states
  let mobileAboutOpen = false;
  let mobileManagementOpen = false;
  let mobileServicesOpen = false;
  let mobileEmployerOpen = false;
  let mobileJobSeekerOpen = false;
  let mobileUniversityOpen = false;
  let mobileTrainingOpen = false;
  let mobileMediaOpen = false;

  // ----- DYNAMIC HEADER OFFSET -----
  let headerHeight = 0;

  function updateHeaderHeight() {
    const header = document.querySelector(".header-fixed");
    if (header) headerHeight = header.offsetHeight;
  }

  // ----- SCROLL TO ELEMENT WITH OFFSET -----
  function scrollToElementWithOffset(element) {
    if (!element) return;
    const elementTop = element.getBoundingClientRect().top + window.scrollY;
    const offsetPosition = elementTop - headerHeight - 10; // 10px extra breathing space
    window.scrollTo({ top: offsetPosition, behavior: "smooth" });
  }

  // ----- SCROLL TO SECTION BY ID (handles cross-page) -----
  async function scrollToSection(id, targetPath = "/") {
    const currentPath = window.location.pathname;
    if (currentPath !== targetPath) {
      await goto(targetPath);
      setTimeout(() => {
        const element = document.getElementById(id);
        if (element) scrollToElementWithOffset(element);
      }, 200);
    } else {
      const element = document.getElementById(id);
      if (element) scrollToElementWithOffset(element);
    }
  }

  // ----- INTERCEPT HASH LINKS -----
  function handleHashClick(event) {
    const anchor = event.target.closest("a");
    if (!anchor) return;
    const href = anchor.getAttribute("href");
    if (!href) return;

    if (href.startsWith("/#") || href.startsWith("#")) {
      event.preventDefault();
      let hash = href.split("#")[1];
      if (!hash) return;
      if (href.startsWith("/#")) hash = href.slice(2);
      scrollToSection(hash, "/");
    } else if (href.includes("#") && !href.startsWith("http")) {
      event.preventDefault();
      const [path, hash] = href.split("#");
      if (!hash) return;
      goto(path).then(() => {
        setTimeout(() => {
          const element = document.getElementById(hash);
          if (element) scrollToElementWithOffset(element);
        }, 200);
      });
    }
  }

  // ----- DROPDOWN HOVER LOGIC -----
  function setActiveWithDelay(dropdown) {
    if (closeTimeout) clearTimeout(closeTimeout);
    if (openTimeout) clearTimeout(openTimeout);
    if (activeDropdown === dropdown) return;
    openTimeout = setTimeout(() => {
      activeDropdown = dropdown;
      openTimeout = null;
    }, 150);
  }

  function scheduleClose() {
    if (openTimeout) clearTimeout(openTimeout);
    if (closeTimeout) clearTimeout(closeTimeout);
    closeTimeout = setTimeout(() => {
      activeDropdown = null;
      closeTimeout = null;
    }, 200);
  }

  function cancelCloseAndOpenImmediately(dropdown) {
    if (closeTimeout) clearTimeout(closeTimeout);
    if (openTimeout) {
      clearTimeout(openTimeout);
      activeDropdown = dropdown;
      openTimeout = null;
    }
  }

  function onDropdownLeave() {
    scheduleClose();
  }

  function onTriggerLeave() {
    scheduleClose();
  }

  function onTriggerEnter(dropdown) {
    setActiveWithDelay(dropdown);
  }

  function closeAll() {
    if (openTimeout) clearTimeout(openTimeout);
    if (closeTimeout) clearTimeout(closeTimeout);
    activeDropdown = null;
    showActions = false;
  }

  // Action button toggle
  function toggleActions(e) {
    e.stopPropagation();
    showActions = !showActions;
    if (showActions) activeDropdown = null;
  }

  // Navigation functions
  function goToRegistration(e) {
    e?.stopPropagation();
    goto("/Employer-Registration");
  }
  function goToPostJob(e) {
    e?.stopPropagation();
    goto("/employer/post-job");
  }

  // Mobile sidebar
  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
    document.body.style.overflow = isMenuOpen ? "hidden" : "auto";
  }
  function closeMenu() {
    isMenuOpen = false;
    document.body.style.overflow = "auto";
  }

  // Mobile accordion toggles
  function toggleMobileAbout() {
    mobileAboutOpen = !mobileAboutOpen;
  }
  function toggleMobileManagement() {
    mobileManagementOpen = !mobileManagementOpen;
  }
  function toggleMobileServices() {
    mobileServicesOpen = !mobileServicesOpen;
  }
  function toggleMobileEmployer() {
    mobileEmployerOpen = !mobileEmployerOpen;
  }
  function toggleMobileJobSeeker() {
    mobileJobSeekerOpen = !mobileJobSeekerOpen;
  }
  function toggleMobileUniversity() {
    mobileUniversityOpen = !mobileUniversityOpen;
  }
  function toggleMobileTraining() {
    mobileTrainingOpen = !mobileTrainingOpen;
  }
  function toggleMobileMedia() {
    mobileMediaOpen = !mobileMediaOpen;
  }

  function handleKeydown(e) {
    if (e.key === "Escape") {
      closeAll();
      if (isMenuOpen) closeMenu();
    }
  }

  onMount(() => {
    updateHeaderHeight();
    window.addEventListener("resize", updateHeaderHeight);
    window.addEventListener("scroll", updateHeaderHeight);

    const handleClickOutside = (e) => {
      if (
        !e.target.closest(".dropdown-trigger") &&
        !e.target.closest(".action-wrapper")
      ) {
        closeAll();
      }
    };
    document.addEventListener("click", handleClickOutside);
    document.addEventListener("keydown", handleKeydown);
    document.addEventListener("click", handleHashClick);

    // Initial hash handling
    const initialHash = window.location.hash.slice(1);
    if (initialHash) {
      setTimeout(() => {
        const element = document.getElementById(initialHash);
        if (element) scrollToElementWithOffset(element);
      }, 200);
    }

    return () => {
      window.removeEventListener("resize", updateHeaderHeight);
      window.removeEventListener("scroll", updateHeaderHeight);
      document.removeEventListener("click", handleClickOutside);
      document.removeEventListener("keydown", handleKeydown);
      document.removeEventListener("click", handleHashClick);
    };
  });

  afterNavigate(() => {
    const hash = window.location.hash.slice(1);
    if (hash) {
      setTimeout(() => {
        const element = document.getElementById(hash);
        if (element) scrollToElementWithOffset(element);
      }, 200);
    }
  });
</script>

<header class="header-fixed">
  <div class="header-container">
    <div class="navbar">
      <!-- LOGO -->
      <div
        class="logo-container"
        on:click={() => {
          closeAll();
          goto("/");
        }}
      >
        <img class="logo-img" src="/images/LOGO.jpg" alt="R Square HR" />
      </div>
      <div class="logo-text">
        R&nbsp;SQUARE
        <div class="logo-hr">HR&nbsp; Services</div>
      </div>

      <!-- DESKTOP NAVIGATION -->
      <nav class="nav-links desktop-only">
        <!-- About Us -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("about")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">About Us ▾</span>
          {#if activeDropdown === "about"}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("about")}
              on:mouseleave={onDropdownLeave}
            >
              <h4>About Us</h4>
              <a href="/#introduction">Introduction</a>
              <a href="/#mission">Mission & Vision</a>
              <a href="/#quality">Quality Policy</a>
            </div>
          {/if}
        </div>

        <!-- MANAGEMENT TEAM -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("management")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Team ▾</span>
          {#if activeDropdown === "management"}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("management")}
              on:mouseleave={onDropdownLeave}
            >
              <h4>Management Team</h4>
              <a href="/Navigation_link_pages/Management_Team#sn-rao">S N Rao</a
              >
              <a href="/Navigation_link_pages/Management_Team#riddhish-rao"
                >Riddhish Rao</a
              >
              <a href="/Navigation_link_pages/Management_Team#resource-team"
                >Resource Team</a
              >
            </div>
          {/if}
        </div>

        <!-- Services (mega dropdown) -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("services")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Services ▾</span>
          {#if activeDropdown === "services"}
            <div
              class="dropdown mega-dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("services")}
              on:mouseleave={onDropdownLeave}
            >
              <div class="mega-columns">
                <div class="mega-column">
                  <h4>Core HR</h4>
                  <a href="/Navigation_link_pages/Services#hr-advisory"
                    >Consultancy for HR Strategies, Policies & Services</a
                  >
                  <a href="/Navigation_link_pages/Services#psychometric"
                    >Psychometric Test Services
                  </a>
                  <a
                    href="/Navigation_link_pages/Services#permanent-recruitment"
                    >Permanent Recruitment Services</a
                  >
                  <a href="/Navigation_link_pages/Services#rpo"
                    >Recruitment Process Outsourcing</a
                  >
                  <a href="/Navigation_link_pages/Services#contract-staffing"
                    >Contract Staffing (Workforce Service on Outsourcing basis)</a
                  >
                  <a href="/Navigation_link_pages/Services#nats"
                    >National Apprenticeship Training Scheme (NATS)
                  </a>
                  <a href="/Navigation_link_pages/Services#naps"
                    >National Apprenticeship Promotion Scheme (NAPS)
                  </a>
                  <a href="/Navigation_link_pages/Services#functional-hr"
                    >Functional HR Services on Outsourcing basis
                  </a>
                  <a href="/Navigation_link_pages/Services#payroll"
                    >Payroll Outsourcing</a
                  >
                  <a href="/Navigation_link_pages/Services#legal-compliance"
                    >Legal Compliance</a
                  >
                </div>
                <div class="mega-column">
                  <h4>Advisory & Training</h4>
                  <a href="/Navigation_link_pages/Services#third-party-auditor"
                    >Third Party Auditor for HR Services</a
                  >
                  <a
                    href="/Navigation_link_pages/Services#background-verification"
                    >Employment Background Verification</a
                  >
                  <a href="/Navigation_link_pages/Services#business-reforms"
                    >Advisory Services on Business Reforms & Improvement</a
                  >
                  <a href="/Navigation_link_pages/Services#training"
                    >Training & Development</a
                  >
                  <a href="/Navigation_link_pages/Services#rti"
                    >Training on RTI and Legal Services under RTI</a
                  >
                  <a href="/Navigation_link_pages/Services#posh"
                    >Training on Prevention of Sexual Harassment (PoSH) Act</a
                  >
                  <a href="/Navigation_link_pages/Services#labour-codes"
                    >Training on New Labour Codes</a
                  >
                </div>
                <div class="mega-column">
                  <h4>Specialized</h4>
                  <a href="/Navigation_link_pages/Services#tourism"
                    >Tourism Specific Solutions & Services</a
                  >
                  <a href="/Navigation_link_pages/Services#csr"
                    >Supporting CSR Activity</a
                  >
                  <a href="/Navigation_link_pages/Services#collaborations"
                    >Collaborating with various institutions</a
                  >
                  <a href="/Navigation_link_pages/Services#cultural"
                    >Cultural Activities (musical programs, films, Gujarati
                    serials, drama etc.)</a
                  >
                  <a href="/Navigation_link_pages/Services#it-consultancy"
                    >IT Consultancy & solutions</a
                  >
                </div>
              </div>
            </div>
          {/if}
        </div>

        <!-- Employer's Corner -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("employer")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Employers ▾</span>
          {#if activeDropdown === "employer"}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("employer")}
              on:mouseleave={onDropdownLeave}
            >
              <h4>Employers</h4>
              <a href="Employers/Registration">Registration</a>
              <a href="#">Company Profile</a>
              <a href="#">Change Password</a>
              <a href="#">Free Job Posting</a>
              <a href="#">Resume Search</a>
              <a href="#">Recruitment Packages for Employer</a>
              <a href="#">Consultant Membership Plan</a>
              <a href="#">Search Consultant</a>
            </div>
          {/if}
        </div>

        <!-- Job Seeker's Corner (mega) -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("jobSeeker")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Job Seekers ▾</span>
          {#if activeDropdown === "jobSeeker"}
            <div
              class="dropdown mega-dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("jobSeeker")}
              on:mouseleave={onDropdownLeave}
            >
              <div class="mega-columns">
                <div class="mega-column">
                  <h4>Registration</h4>
                  <a href="/job-seekers/registration">Registration</a>
                  <a href="/job-seekers/profile">Job Seeker's Profile with CV Attachment</a>
                  <a href="/job-seekers/otp"
                    >My Profile (Registration with Mobile/Email – OTP)</a
                  >
                </div>
                <div class="mega-column">
                  <h4>Jobs by Category</h4>
                  <a href="#">Jobs by Type</a>
                  <a href="#">Jobs by City</a>
                  <a href="#">Jobs by Department</a>
                  <a href="#">Jobs by Qualification</a>
                  <a href="#">Jobs by Role</a>
                  <a href="#">Jobs by Skill</a>
                  <a href="#">Jobs by Company</a>
                  <a href="#">Jobs by Employer Type (Direct/Consultant)</a>
                </div>
              </div>
            </div>
          {/if}
        </div>

        <!-- University Corner -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("university")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">University ▾</span>
          {#if activeDropdown === "university"}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("university")}
              on:mouseleave={onDropdownLeave}
            >
              <h4>University</h4>
              <a href="#">Registration</a>
              <a href="#">Institute Profile With Brochure Attachment</a>
            </div>
          {/if}
        </div>

        <!-- TRAINING -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("training")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Training ▾</span>
          {#if activeDropdown === "training"}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("training")}
              on:mouseleave={onDropdownLeave}
            >
              <h4>Training</h4>
              <a href="#">Faculty Registration</a>
              <a href="#">Faculty's Profile with CV</a>
              <a href="#">Student Registration</a>
              <a href="#">Student's Profile with CV</a>
              <a href="#">Registration For Webinar</a>
              <a href="#">Registration For Seminar</a>
              <a href="#">Registration For Online Courses</a>
              <a href="#">Registration For Offline Courses</a>
            </div>
          {/if}
        </div>

        <!-- Clients (simple link) -->
        <a href="/Navigation_link_pages/clients" class="nav-link" on:click={closeAll}>
  Clients
</a>

        <!-- Media dropdown -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter("media")}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Media ▾</span>
          {#if activeDropdown === "media"}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately("media")}
              on:mouseleave={onDropdownLeave}
            >
              <h4>Media</h4>
              <a href="#">Announcements</a>
              <a href="#">News</a>
              <a href="#">Photographs</a>
              <a href="#">Videos</a>
            </div>
          {/if}
        </div>

        <!-- Contact (simple link) -->
        <a href="/#contact" class="nav-link" on:click={closeAll}>Contact</a>
      </nav>

      <!-- ACTION ICON (Registration / Post Job) – click only -->
      <div class="header-buttons desktop-only">
        <div class="action-wrapper">
          <div class="icon-trigger" on:click={toggleActions}>
            <svg
              viewBox="0 0 24 24"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M20 7H4C2.9 7 2 7.9 2 9V19C2 20.1 2.9 21 4 21H20C21.1 21 22 20.1 22 19V9C22 7.9 21.1 7 20 7Z"
                stroke="white"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
              <path
                d="M16 21V5C16 3.9 15.1 3 14 3H10C8.9 3 8 3.9 8 5V21"
                stroke="white"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
              <path
                d="M12 13H12.01"
                stroke="white"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
              <path
                d="M2 13H22"
                stroke="white"
                stroke-width="1.5"
                stroke-linecap="round"
              />
            </svg>
          </div>
          {#if showActions}
            <div class="action-dropdown">
              <button
                class="btn-header btn-register"
                on:click={goToRegistration}>Registration</button
              >
              <button class="btn-header btn-post" on:click={goToPostJob}
                >Post Job</button
              >
            </div>
          {/if}
        </div>
      </div>

      <!-- HAMBURGER (MOBILE) -->
      <button class="hamburger mobile-only" on:click={toggleMenu}>
        <span class="bar"></span>
        <span class="bar"></span>
        <span class="bar"></span>
      </button>
    </div>
  </div>
</header>

<!-- OVERLAY & SIDEBAR -->
<div class="sidebar" class:open={isMenuOpen}>
  <div class="sidebar-header">
    <img src="/images/LOGO.jpg" class="sidebar-logo" alt="R Square HR" />
    <button class="close-sidebar" on:click={closeMenu}>×</button>
  </div>
  <div class="sidebar-nav">
    <!-- About Us -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileAbout}>
        <span>About Us</span>
        <span class="arrow">{mobileAboutOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileAboutOpen}
        <div class="sidebar-sublinks">
          <a href="/#introduction" on:click={closeMenu}>Introduction</a>
          <a href="/#mission" on:click={closeMenu}>Mission & Vision </a>
          <a href="/#quality" on:click={closeMenu}>Quality Policy</a>
        </div>
      {/if}
    </div>

    <!-- Management Team -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileManagement}>
        <span>Management Team</span>
        <span class="arrow">{mobileManagementOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileManagementOpen}
        <div class="sidebar-sublinks">
          <a
            href="/Navigation_link_pages/Management_Team#sn-rao"
            on:click={closeMenu}>S N Rao</a
          >
          <a
            href="/Navigation_link_pages/Management_Team#riddhish-rao"
            on:click={closeMenu}>Riddhish Rao</a
          >
          <a
            href="/Navigation_link_pages/Management_Team#resource-team"
            on:click={closeMenu}>Resource Team</a
          >
        </div>
      {/if}
    </div>

    <!-- Services -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileServices}>
        <span>Services</span>
        <span class="arrow">{mobileServicesOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileServicesOpen}
        <div class="sidebar-sublinks">
          <a
            href="/Navigation_link_pages/Services#hr-advisory"
            on:click={closeMenu}
            >Consultancy for HR Strategies, Policies & Services</a
          >
          <a
            href="/Navigation_link_pages/Services#psychometric"
            on:click={closeMenu}>Psychometric Test Services</a
          >
          <a
            href="/Navigation_link_pages/Services#permanent-recruitment"
            on:click={closeMenu}>Permanent Recruitment Services</a
          >
          <a href="/Navigation_link_pages/Services#rpo" on:click={closeMenu}
            >Recruitment Process Outsourcing</a
          >
          <a
            href="/Navigation_link_pages/Services#contract-staffing"
            on:click={closeMenu}
            >Contract Staffing (Workforce Service on Outsourcing basis)</a
          >
          <a href="/Navigation_link_pages/Services#nats" on:click={closeMenu}
            >National Apprenticeship Training Scheme (NATS)</a
          >
          <a href="/Navigation_link_pages/Services#naps" on:click={closeMenu}
            >National Apprenticeship Promotion Scheme (NAPS)</a
          >
          <a
            href="/Navigation_link_pages/Services#functional-hr"
            on:click={closeMenu}>Functional HR Services on Outsourcing basis</a
          >
          <a href="/Navigation_link_pages/Services#payroll" on:click={closeMenu}
            >Payroll Outsourcing</a
          >
          <a
            href="/Navigation_link_pages/Services#legal-compliance"
            on:click={closeMenu}>Legal Compliance</a
          >
          <a
            href="/Navigation_link_pages/Services#third-party-auditor"
            on:click={closeMenu}>Third Party Auditor for HR Services</a
          >
          <a
            href="/Navigation_link_pages/Services#background-verification"
            on:click={closeMenu}>Employment Background Verification</a
          >
          <a
            href="/Navigation_link_pages/Services#business-reforms"
            on:click={closeMenu}
            >Advisory Services on Business Reforms & Improvement</a
          >
          <a
            href="/Navigation_link_pages/Services#training"
            on:click={closeMenu}>Training & Development</a
          >
          <a href="/Navigation_link_pages/Services#rti" on:click={closeMenu}
            >Training on RTI and Legal Services under RTI</a
          >
          <a href="/Navigation_link_pages/Services#posh" on:click={closeMenu}
            >Training on Prevention of Sexual Harassment (PoSH) Act</a
          >
          <a
            href="/Navigation_link_pages/Services#labour-codes"
            on:click={closeMenu}>Training on New Labour Codes</a
          >
          <a href="/Navigation_link_pages/Services#tourism" on:click={closeMenu}
            >Tourism Specific Solutions & Services</a
          >
          <a href="/Navigation_link_pages/Services#csr" on:click={closeMenu}
            >Supporting CSR Activity</a
          >
          <a
            href="/Navigation_link_pages/Services#collaborations"
            on:click={closeMenu}>Collaborating with various institutions</a
          >
          <a
            href="/Navigation_link_pages/Services#cultural"
            on:click={closeMenu}
            >Cultural Activities (musical programs, films, Gujarati serials,
            drama etc.)</a
          >
          <a
            href="/Navigation_link_pages/Services#it-consultancy"
            on:click={closeMenu}>IT Consultancy & solutions</a
          >
        </div>
      {/if}
    </div>

    <!-- Employer's Corner -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileEmployer}>
        <span>Employers</span>
        <span class="arrow">{mobileEmployerOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileEmployerOpen}
        <div class="sidebar-sublinks">
          <a href="Employers/Registration" on:click={closeMenu}>Registration</a>
          <a href="#" on:click={closeMenu}>Company Profile</a>
          <a href="#" on:click={closeMenu}>Change Password</a>
          <a href="#" on:click={closeMenu}>Free Job Posting</a>
          <a href="#" on:click={closeMenu}>Resume Search</a>
          <a href="#" on:click={closeMenu}>Recruitment Packages for Employer</a>
          <a href="#" on:click={closeMenu}>Consultant Membership Plan</a>
          <a href="#" on:click={closeMenu}>Search Consultant</a>
        </div>
      {/if}
    </div>

    <!-- Job Seeker's Corner -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileJobSeeker}>
        <span>Job Seekers</span>
        <span class="arrow">{mobileJobSeekerOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileJobSeekerOpen}
        <div class="sidebar-sublinks">
          <a href="/job-seekers/registration" on:click={closeMenu}>Registration</a>
          <a href="/job-seekers/profile" on:click={closeMenu}
            >Job Seeker's Profile with CV Attachment</a
          >
          <a href="/job-seekers/otp" on:click={closeMenu}
            >My Profile (Registration with Mobile/Email – OTP)</a
          >
          <a href="#" on:click={closeMenu}>Jobs by Type</a>
          <a href="#" on:click={closeMenu}>Jobs by City</a>
          <a href="#" on:click={closeMenu}>Jobs by Department</a>
          <a href="#" on:click={closeMenu}>Jobs by Qualification</a>
          <a href="#" on:click={closeMenu}>Jobs by Role</a>
          <a href="#" on:click={closeMenu}>Jobs by Skill</a>
          <a href="#" on:click={closeMenu}>Jobs by Company</a>
          <a href="#" on:click={closeMenu}
            >Jobs by Employer Type (Direct/Consultant)</a
          >
        </div>
      {/if}
    </div>

    <!-- University Corner -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileUniversity}>
        <span>University</span>
        <span class="arrow">{mobileUniversityOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileUniversityOpen}
        <div class="sidebar-sublinks">
          <a href="#" on:click={closeMenu}>Registration</a>
          <a href="#" on:click={closeMenu}
            >Institute Profile With Brochure Attachment</a
          >
        </div>
      {/if}
    </div>

    <!-- Training -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileTraining}>
        <span>Training</span>
        <span class="arrow">{mobileTrainingOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileTrainingOpen}
        <div class="sidebar-sublinks">
          <a href="#" on:click={closeMenu}>Faculty Registration</a>
          <a href="#" on:click={closeMenu}>Faculty's Profile with CV</a>
          <a href="#" on:click={closeMenu}>Student Registration</a>
          <a href="#" on:click={closeMenu}>Student's Profile with CV</a>
          <a href="#" on:click={closeMenu}>Registration For Webinar</a>
          <a href="#" on:click={closeMenu}>Registration For Seminar</a>
          <a href="#" on:click={closeMenu}>Registration For Online Courses</a>
          <a href="#" on:click={closeMenu}>Registration For Offline Courses</a>
        </div>
      {/if}
    </div>

    <!-- Clients -->
    <a href="/clients" class="sidebar-link simple" on:click={closeMenu}
      >Clients</a
    >

    <!-- Media -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileMedia}>
        <span>Media</span>
        <span class="arrow">{mobileMediaOpen ? "▾" : "▸"}</span>
      </div>
      {#if mobileMediaOpen}
        <div class="sidebar-sublinks">
          <a href="#" on:click={closeMenu}>Announcements</a>
          <a href="#" on:click={closeMenu}>News</a>
          <a href="#" on:click={closeMenu}>Photographs</a>
          <a href="#" on:click={closeMenu}>Videos</a>
        </div>
      {/if}
    </div>

    <!-- Contact -->
    <a href="/#contact" class="sidebar-link simple" on:click={closeMenu}
      >Contact</a
    >
  </div>

  <div class="sidebar-buttons">
    <button
      class="btn-header btn-register"
      on:click={() => {
        goToRegistration();
        closeMenu();
      }}>Registration</button
    >
    <button
      class="btn-header btn-post"
      on:click={() => {
        goToPostJob();
        closeMenu();
      }}>Post Job</button
    >
  </div>
</div>

<style>
  @import "$lib/styles/header.css";
</style>
