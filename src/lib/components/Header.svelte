<script>
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

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

  // ----- Desktop hover logic -----
  function setActiveWithDelay(dropdown) {
    if (closeTimeout) clearTimeout(closeTimeout);
    if (openTimeout) clearTimeout(openTimeout);
    if (activeDropdown === dropdown) return;
    openTimeout = setTimeout(() => {
      activeDropdown = dropdown;
      openTimeout = null;
    }, 150); // 150ms delay before opening
  }

  function scheduleClose() {
    if (openTimeout) clearTimeout(openTimeout);
    if (closeTimeout) clearTimeout(closeTimeout);
    closeTimeout = setTimeout(() => {
      activeDropdown = null;
      closeTimeout = null;
    }, 200); // 200ms delay before closing
  }

  // Called when mouse enters the dropdown content – cancel close, and if still in open delay, open immediately
  function cancelCloseAndOpenImmediately(dropdown) {
    if (closeTimeout) clearTimeout(closeTimeout);
    if (openTimeout) {
      clearTimeout(openTimeout);
      activeDropdown = dropdown;
      openTimeout = null;
    }
  }

  // Called when mouse leaves the dropdown content – schedule close
  function onDropdownLeave() {
    scheduleClose();
  }

  // Called when mouse leaves the trigger – schedule close
  function onTriggerLeave() {
    scheduleClose();
  }

  // Called when mouse enters the trigger – open with delay
  function onTriggerEnter(dropdown) {
    setActiveWithDelay(dropdown);
  }

  // Helper to close all desktop dropdowns immediately
  function closeAll() {
    if (openTimeout) clearTimeout(openTimeout);
    if (closeTimeout) clearTimeout(closeTimeout);
    activeDropdown = null;
    showActions = false;
  }

  // Action button (click toggles)
  function toggleActions(e) {
    e.stopPropagation();
    showActions = !showActions;
    if (showActions) {
      activeDropdown = null; // close any open nav dropdown
    }
  }

  // Navigation functions
  function goToRegistration(e) {
    e?.stopPropagation();
    goto('/Employer-Registration');
  }
  function goToPostJob(e) {
    e?.stopPropagation();
    goto('/employer/post-job');
  }

  // Mobile sidebar toggle
  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
    document.body.style.overflow = isMenuOpen ? 'hidden' : 'auto';
  }
  function closeMenu() {
    isMenuOpen = false;
    document.body.style.overflow = 'auto';
  }

  // Mobile accordion toggles
  function toggleMobileAbout() { mobileAboutOpen = !mobileAboutOpen; }
  function toggleMobileManagement() { mobileManagementOpen = !mobileManagementOpen; }
  function toggleMobileServices() { mobileServicesOpen = !mobileServicesOpen; }
  function toggleMobileEmployer() { mobileEmployerOpen = !mobileEmployerOpen; }
  function toggleMobileJobSeeker() { mobileJobSeekerOpen = !mobileJobSeekerOpen; }
  function toggleMobileUniversity() { mobileUniversityOpen = !mobileUniversityOpen; }
  function toggleMobileTraining() { mobileTrainingOpen = !mobileTrainingOpen; }
  function toggleMobileMedia() { mobileMediaOpen = !mobileMediaOpen; }

  // Close on Escape key
  function handleKeydown(e) {
    if (e.key === 'Escape') {
      closeAll();
      if (isMenuOpen) closeMenu();
    }
  }

  // Click outside to close dropdowns
  onMount(() => {
    const handleClickOutside = (e) => {
      if (!e.target.closest('.dropdown-trigger') && !e.target.closest('.action-wrapper')) {
        closeAll();
      }
    };
    document.addEventListener('click', handleClickOutside);
    document.addEventListener('keydown', handleKeydown);
    return () => {
      document.removeEventListener('click', handleClickOutside);
      document.removeEventListener('keydown', handleKeydown);
    };
  });
</script>

<header class="header-fixed">
  <div class="header-container">
    <div class="navbar">

      <!-- LOGO -->
      <div class="logo-container" on:click={() => { closeAll(); goto('/'); }}>
        <img class="logo-img" src="/images/LOGO_PDF_invertedColor_page-0001.jpg" alt="R Square HR" />
        <span class="logo-text">R SQUARE <span class="logo-hr">HR Services</span></span>
      </div>

      <!-- DESKTOP NAVIGATION -->
      <nav class="nav-links desktop-only">

        <!-- About Us -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter('about')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">About Us ▾</span>
          {#if activeDropdown === 'about'}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('about')}
              on:mouseleave={onDropdownLeave}
            >
              <a href="/about/introduction">Introduction</a>
              <a href="/about/mission">Mission</a>
              <a href="/about/vision">Vision</a>
              <a href="/about/quality-policy">Quality Policy</a>
            </div>
          {/if}
        </div>

        <!-- MANAGEMENT TEAM -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter('management')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Management Team ▾</span>
          {#if activeDropdown === 'management'}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('management')}
              on:mouseleave={onDropdownLeave}
            >
              <a href="#">S N Rao</a>
              <a href="#">Riddhish Rao</a>
              <a href="#">Resource Team</a>
            </div>
          {/if}
        </div>

        <!-- Services (mega dropdown) -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter('services')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Services ▾</span>
          {#if activeDropdown === 'services'}
            <div
              class="dropdown mega-dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('services')}
              on:mouseleave={onDropdownLeave}
            >
              <div class="mega-columns">
                <div class="mega-column">
                  <h4>Core HR</h4>
                  <a href="#">Consultancy for HR Strategies, Policies & Services</a>
                  <a href="#">Psychometric Test Services</a>
                  <a href="#">Permanent Recruitment Services</a>
                  <a href="#">Recruitment Process Outsourcing</a>
                  <a href="#">Contract Staffing (Workforce Service on Outsourcing basis)</a>
                  <a href="#">National Apprenticeship Training Scheme (NATS)</a>
                  <a href="#">National Apprenticeship Promotion Scheme (NAPS)</a>
                  <a href="#">Functional HR Services on Outsourcing basis</a>
                  <a href="#">Payroll Outsourcing</a>
                  <a href="#">Legal Compliance (HR related)</a>
                </div>
                <div class="mega-column">
                  <h4>Advisory & Training</h4>
                  <a href="#">Third Party Auditor for HR Services</a>
                  <a href="#">Employment Background Verification</a>
                  <a href="#">Advisory Services on Business Reforms & Improvement</a>
                  <a href="#">Training & Development</a>
                  <a href="#">Training on RTI and Legal Services under RTI</a>
                  <a href="#">Training on PoSH</a>
                  <a href="#">Training on New Labour Laws</a>
                </div>
                <div class="mega-column">
                  <h4>Specialized</h4>
                  <a href="#">Tourism Specific Solutions & Services</a>
                  <a href="#">Supporting CSR Activity</a>
                  <a href="#">Collaborating with various institutions</a>
                  <a href="#">Cultural Activities (musical programs, films, Gujarati serials, drama etc.)</a>
                  <a href="#">IT Consultancy & solutions</a>
                </div>
              </div>
            </div>
          {/if}
        </div>

        <!-- Employer's Corner -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter('employer')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Employers ▾</span>
          {#if activeDropdown === 'employer'}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('employer')}
              on:mouseleave={onDropdownLeave}
            >
              <a href="#">Registration</a>
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
          on:mouseenter={() => onTriggerEnter('jobSeeker')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Job Seekers ▾</span>
          {#if activeDropdown === 'jobSeeker'}
            <div
              class="dropdown mega-dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('jobSeeker')}
              on:mouseleave={onDropdownLeave}
            >
              <div class="mega-columns">
                <div class="mega-column">
                  <h4>Registration</h4>
                  <a href="#">Registration</a>
                  <a href="#">Job Seeker's Profile with CV Attachment</a>
                  <a href="#">My Profile (Registration with Mobile/Email – OTP)</a>
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
          on:mouseenter={() => onTriggerEnter('university')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">University ▾</span>
          {#if activeDropdown === 'university'}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('university')}
              on:mouseleave={onDropdownLeave}
            >
              <a href="#">Registration</a>
              <a href="#">Institute Profile With Brochure Attachment</a>
            </div>
          {/if}
        </div>

        <!-- TRAINING -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter('training')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Training ▾</span>
          {#if activeDropdown === 'training'}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('training')}
              on:mouseleave={onDropdownLeave}
            >
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
        <a href="/clients" class="nav-link" on:click={closeAll}>Clients</a>

        <!-- Media dropdown -->
        <div
          class="dropdown-trigger"
          on:mouseenter={() => onTriggerEnter('media')}
          on:mouseleave={onTriggerLeave}
        >
          <span class="nav-link">Media ▾</span>
          {#if activeDropdown === 'media'}
            <div
              class="dropdown"
              on:mouseenter={() => cancelCloseAndOpenImmediately('media')}
              on:mouseleave={onDropdownLeave}
            >
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
            <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M20 7H4C2.9 7 2 7.9 2 9V19C2 20.1 2.9 21 4 21H20C21.1 21 22 20.1 22 19V9C22 7.9 21.1 7 20 7Z" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M16 21V5C16 3.9 15.1 3 14 3H10C8.9 3 8 3.9 8 5V21" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M12 13H12.01" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M2 13H22" stroke="white" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
          </div>
          {#if showActions}
            <div class="action-dropdown">
              <button class="btn-header btn-register" on:click={goToRegistration}>Registration</button>
              <button class="btn-header btn-post" on:click={goToPostJob}>Post Job</button>
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

<!-- OVERLAY & SIDEBAR (unchanged) -->
<div class="sidebar-overlay" class:open={isMenuOpen} on:click={closeMenu}></div>
<div class="sidebar" class:open={isMenuOpen}>
  <div class="sidebar-header">
    <img src="/images/LOGO_PDF_invertedColor_page-0001.jpg" class="sidebar-logo" alt="R Square HR" />
    <button class="close-sidebar" on:click={closeMenu}>×</button>
  </div>
  <div class="sidebar-nav">

    <!-- About Us -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileAbout}>
        <span>About Us</span>
        <span class="arrow">{mobileAboutOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileAboutOpen}
        <div class="sidebar-sublinks">
          <a href="/about/introduction" on:click={closeMenu}>Introduction</a>
          <a href="/about/mission" on:click={closeMenu}>Mission</a>
          <a href="/about/vision" on:click={closeMenu}>Vision</a>
          <a href="/about/quality-policy" on:click={closeMenu}>Quality Policy</a>
        </div>
      {/if}
    </div>

    <!-- Management Team -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileManagement}>
        <span>Management Team</span>
        <span class="arrow">{mobileManagementOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileManagementOpen}
        <div class="sidebar-sublinks">
          <a href="#" on:click={closeMenu}>S N Rao</a>
          <a href="#" on:click={closeMenu}>Riddhish Rao</a>
          <a href="#" on:click={closeMenu}>Resource Team</a>
        </div>
      {/if}
    </div>

    <!-- Services -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileServices}>
        <span>Services</span>
        <span class="arrow">{mobileServicesOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileServicesOpen}
        <div class="sidebar-sublinks">
          <a href="#">Consultancy for HR Strategies, Policies & Services</a>
          <a href="#">Psychometric Test Services</a>
          <a href="#">Permanent Recruitment Services</a>
          <a href="#">Recruitment Process Outsourcing</a>
          <a href="#">Contract Staffing (Workforce Service on Outsourcing basis)</a>
          <a href="#">National Apprenticeship Training Scheme (NATS)</a>
          <a href="#">National Apprenticeship Promotion Scheme (NAPS)</a>
          <a href="#">Functional HR Services on Outsourcing basis</a>
          <a href="#">Payroll Outsourcing</a>
          <a href="#">Legal Compliance (HR related)</a>
          <a href="#">Third Party Auditor for HR Services</a>
          <a href="#">Employment Background Verification</a>
          <a href="#">Advisory Services on Business Reforms & Improvement</a>
          <a href="#">Training & Development</a>
          <a href="#">Training on RTI and Legal Services under RTI</a>
          <a href="#">Training on PoSH</a>
          <a href="#">Training on New Labour Laws</a>
          <a href="#">Tourism Specific Solutions & Services</a>
          <a href="#">Supporting CSR Activity</a>
          <a href="#">Collaborating with various institutions</a>
          <a href="#">Cultural Activities (musical programs, films, Gujarati serials, drama etc.)</a>
          <a href="#">IT Consultancy & solutions</a>
        </div>
      {/if}
    </div>

    <!-- Employer's Corner -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileEmployer}>
        <span>Employers</span>
        <span class="arrow">{mobileEmployerOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileEmployerOpen}
        <div class="sidebar-sublinks">
          <a href="#">Registration</a>
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

    <!-- Job Seeker's Corner -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileJobSeeker}>
        <span>Job Seekers</span>
        <span class="arrow">{mobileJobSeekerOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileJobSeekerOpen}
        <div class="sidebar-sublinks">
          <a href="#">Registration</a>
          <a href="#">Job Seeker's Profile with CV Attachment</a>
          <a href="#">My Profile (Registration with Mobile/Email – OTP)</a>
          <a href="#">Jobs by Type</a>
          <a href="#">Jobs by City</a>
          <a href="#">Jobs by Department</a>
          <a href="#">Jobs by Qualification</a>
          <a href="#">Jobs by Role</a>
          <a href="#">Jobs by Skill</a>
          <a href="#">Jobs by Company</a>
          <a href="#">Jobs by Employer Type (Direct/Consultant)</a>
        </div>
      {/if}
    </div>

    <!-- University -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileUniversity}>
        <span>University</span>
        <span class="arrow">{mobileUniversityOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileUniversityOpen}
        <div class="sidebar-sublinks">
          <a href="#">Registration</a>
          <a href="#">Institute Profile With Brochure Attachment</a>
        </div>
      {/if}
    </div>

    <!-- Training -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileTraining}>
        <span>Training</span>
        <span class="arrow">{mobileTrainingOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileTrainingOpen}
        <div class="sidebar-sublinks">
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

    <!-- Clients -->
    <a href="/clients" class="sidebar-link simple" on:click={closeMenu}>Clients</a>

    <!-- Media -->
    <div class="sidebar-item">
      <div class="sidebar-link" on:click={toggleMobileMedia}>
        <span>Media</span>
        <span class="arrow">{mobileMediaOpen ? '▾' : '▸'}</span>
      </div>
      {#if mobileMediaOpen}
        <div class="sidebar-sublinks">
          <a href="#">Announcements</a>
          <a href="#">News</a>
          <a href="#">Photographs</a>
          <a href="#">Videos</a>
        </div>
      {/if}
    </div>

    <!-- Contact -->
    <a href="/#contact" class="sidebar-link simple" on:click={closeMenu}>Contact</a>

  </div>

  <div class="sidebar-buttons">
    <button class="btn-header btn-register" on:click={() => { goToRegistration(); closeMenu(); }}>Registration</button>
    <button class="btn-header btn-post" on:click={() => { goToPostJob(); closeMenu(); }}>Post Job</button>
  </div>
</div>

<style>
  @import '$lib/styles/header.css';
</style>