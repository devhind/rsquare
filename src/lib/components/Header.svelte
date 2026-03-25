<script>
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  // Menu and dropdown states
  let isMenuOpen = false;
  let showActions = false;
  let aboutOpen = false;
  let servicesOpen = false;
  let employerOpen = false;
  let jobSeekerOpen = false;
  let universityOpen = false;
  let mediaOpen = false;
  let moreOpen = false;
  let managementOpen = false;
  let trainingOpen = false;           // 👈 new dropdown

  // Toggle functions – each closes the others when opened
  function toggleAbout(e) {
    e.stopPropagation();
    aboutOpen = !aboutOpen;
    if (aboutOpen) {
      servicesOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleServices(e) {
    e.stopPropagation();
    servicesOpen = !servicesOpen;
    if (servicesOpen) {
      aboutOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleEmployer(e) {
    e.stopPropagation();
    employerOpen = !employerOpen;
    if (employerOpen) {
      aboutOpen = servicesOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleJobSeeker(e) {
    e.stopPropagation();
    jobSeekerOpen = !jobSeekerOpen;
    if (jobSeekerOpen) {
      aboutOpen = servicesOpen = employerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleUniversity(e) {
    e.stopPropagation();
    universityOpen = !universityOpen;
    if (universityOpen) {
      aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleMedia(e) {
    e.stopPropagation();
    mediaOpen = !mediaOpen;
    if (mediaOpen) {
      aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = universityOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleMore(e) {
    e.stopPropagation();
    moreOpen = !moreOpen;
    if (moreOpen) {
      aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = managementOpen = trainingOpen = showActions = false;
    }
  }
  function toggleManagement(e) {
    e.stopPropagation();
    managementOpen = !managementOpen;
    if (managementOpen) {
      aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = trainingOpen = showActions = false;
    }
  }
  function toggleTraining(e) {
    e.stopPropagation();
    trainingOpen = !trainingOpen;
    if (trainingOpen) {
      aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = showActions = false;
    }
  }
  function toggleActions(e) {
    e.stopPropagation();
    showActions = !showActions;
    if (showActions) {
      aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = false;
    }
  }

  function closeAll() {
    aboutOpen = servicesOpen = employerOpen = jobSeekerOpen = universityOpen = mediaOpen = moreOpen = managementOpen = trainingOpen = showActions = false;
  }

  function goToRegistration(e) {
    e.stopPropagation();
    goto('/Employer-Registration');
  }
  function goToPostJob(e) {
    e.stopPropagation();
    goto('/employer/post-job');
  }

  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
    document.body.style.overflow = isMenuOpen ? 'hidden' : 'auto';
  }
  function closeMenu() {
    isMenuOpen = false;
    document.body.style.overflow = 'auto';
  }

  onMount(() => {
    const handleClickOutside = (e) => {
      if (!e.target.closest('.dropdown-trigger')) closeAll();
    };
    document.addEventListener('click', handleClickOutside);
    return () => document.removeEventListener('click', handleClickOutside);
  });
</script>

<header class="header-fixed">
  <div class="header-container">
    <div class="navbar">

      <!-- LOGO -->
      <div class="logo-container" on:click={() => goto('/')}>
        <img class="logo-img" src="/images/LOGO_PDF_invertedColor_page-0001.jpg" alt="R Square HR" />
        <span class="logo-text">R SQUARE <span class="logo-hr">HR Services</span></span>
      </div>

      <!-- DESKTOP NAVIGATION -->
      <nav class="nav-links desktop-only">

        <!-- About Us -->
        <div class="dropdown-trigger" class:open={aboutOpen}>
          <span class="nav-link" on:click={toggleAbout}>About Us ▾</span>
          {#if aboutOpen}
            <div class="dropdown">
              <a href="/about/introduction">Introduction</a>
              <a href="/about/mission">Mission</a>
              <a href="/about/vision">Vision</a>
              <a href="/about/quality-policy">Quality Policy</a>
            </div>
          {/if}
        </div>

        <!-- MANAGEMENT TEAM -->
        <div class="dropdown-trigger" class:open={managementOpen}>
          <span class="nav-link" on:click={toggleManagement}>Management Team ▾</span>
          {#if managementOpen}
            <div class="dropdown">
              <a href="#">S N Rao</a>
              <a href="#">Riddhish Rao</a>
              <a href="#">Resource Team</a>
            </div>
          {/if}
        </div>

        <!-- Services (mega dropdown) -->
        <div class="dropdown-trigger" class:open={servicesOpen}>
          <span class="nav-link" on:click={toggleServices}>Services ▾</span>
          {#if servicesOpen}
            <div class="dropdown mega-dropdown">
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
        <div class="dropdown-trigger" class:open={employerOpen}>
          <span class="nav-link" on:click={toggleEmployer}>Employers ▾</span>
          {#if employerOpen}
            <div class="dropdown">
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
        <div class="dropdown-trigger" class:open={jobSeekerOpen}>
          <span class="nav-link" on:click={toggleJobSeeker}>Job Seekers ▾</span>
          {#if jobSeekerOpen}
            <div class="dropdown mega-dropdown">
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

        <!-- University Corner (mega) -->
        <div class="dropdown-trigger" class:open={universityOpen}>
          <span class="nav-link" on:click={toggleUniversity}>University ▾</span>
          {#if universityOpen}
             <div class="dropdown">
              <a href="#">Registartion</a>
              <a href="#">Institute Profile With Brochure Attachment</a>
            </div>
          {/if}
        </div>

        <!-- TRAINING (new dropdown with faculty/student links, no headers) -->
        <div class="dropdown-trigger" class:open={trainingOpen}>
          <span class="nav-link" on:click={toggleTraining}>Training ▾</span>
          {#if trainingOpen}
            <div class="dropdown">
              <a href="#">Faculty Registration</a>
              <a href="#">Faculty's Profile with CV</a>
              <a href="#">Student Registration</a>
              <a href="#">Student's Profile with CV</a>
              <a href="#">Registration For Webinar</a>
              <a href="#">Registration For Seminar</a>
              <a href="#">Registartion For Online Courses</a>
              <a href="#">Registartion For Offline Courses</a>
            </div>
          {/if}
        </div>

        <!-- Clients (simple link) -->
        <a href="/clients" class="nav-link">Clients</a>

        <!-- Media dropdown -->
        <div class="dropdown-trigger" class:open={mediaOpen}>
          <span class="nav-link" on:click={toggleMedia}>Media ▾</span>
          {#if mediaOpen}
            <div class="dropdown">
              <a href="#">Announcements</a>
              <a href="#">News</a>
              <a href="#">Photographs</a>
              <a href="#">Videos</a>
            </div>
          {/if}
        </div>

        <!-- Contact (simple link) -->
        <a href="/#contact" class="nav-link">Contact</a>

      </nav>

      <!-- ACTION ICON (Registration / Post Job) -->
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

<!-- OVERLAY & SIDEBAR -->
<div class="sidebar-overlay" class:open={isMenuOpen} on:click={closeMenu}></div>
<div class="sidebar" class:open={isMenuOpen}>
  <div class="sidebar-header">
    <img src="/images/LOGO_PDF_invertedColor_page-0001.jpg" class="sidebar-logo" alt="R Square HR" />
    <button class="close-sidebar" on:click={closeMenu}>×</button>
  </div>
  <div class="sidebar-nav">
    <a href="/" on:click={closeMenu}>Home</a>
    <a href="/about" on:click={closeMenu}>About Us</a>
    <a href="#" on:click={closeMenu}>Management Team</a>
    <a href="/services" on:click={closeMenu}>Services</a>
    <a href="/employers" on:click={closeMenu}>Employer's Corner</a>
    <a href="/job-seekers" on:click={closeMenu}>Job Seeker's Corner</a>
    <a href="/university" on:click={closeMenu}>University</a>
    <a href="#" on:click={closeMenu}>Training</a>              <!-- new mobile link -->
    <a href="/clients" on:click={closeMenu}>Clients</a>
    <a href="/media" on:click={closeMenu}>Media</a>
    <a href="/#contact" on:click={closeMenu}>Contact</a>
  </div>
  <div class="sidebar-buttons">
    <button class="btn-header btn-register" on:click={() => { goToRegistration(); closeMenu(); }}>Registration</button>
    <button class="btn-header btn-post" on:click={() => { goToPostJob(); closeMenu(); }}>Post Job</button>
  </div>
</div>

<style>
  @import '$lib/styles/header.css';
</style>