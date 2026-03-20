<script>
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';
  import Header from '$lib/components/Header.svelte';
  import Footer from '$lib/components/Footer.svelte';

  /* ================= CAROUSEL ================= */
  let currentIndex = 0;

  const slides = [
    {
      image: 'https://images.unsplash.com/photo-1581091226033-d5c48150dbaa',
      tag: 'Tourism & skill development',
      title: '132 Tourist Guides trained for Statue of Unity',
      desc: 'R Square HR trained tribal youth as guides.'
    },
    {
      image: 'https://images.unsplash.com/photo-1541339907198-e08756dedf3f',
      tag: 'HR consulting',
      title: 'HR Policy Manuals for IIM Ahmedabad & IIM Udaipur',
      desc: 'Trusted by premier institutes.'
    },
    {
      image: 'https://images.unsplash.com/photo-1524178232363-1fb2b075b655',
      tag: 'Training',
      title: 'PMKVY & DDU-GKY programs',
      desc: 'Government skill programs.'
    },
    {
      image: 'https://images.unsplash.com/photo-1577563908411-5077b6dc7624',
      tag: 'Staffing',
      title: 'Science City Educators',
      desc: 'Specialised guides.'
    }
  ];

  let autoInterval;
  const AUTO_INTERVAL = 5000;

  function next() {
    currentIndex = (currentIndex + 1) % slides.length;
  }

  function prev() {
    currentIndex = (currentIndex - 1 + slides.length) % slides.length;
  }

  function startAuto() {
    stopAuto();
    autoInterval = setInterval(next, AUTO_INTERVAL);
  }

  function stopAuto() {
    if (autoInterval) clearInterval(autoInterval);
  }

  /* ================= BACK TO TOP ================= */
  let showBackToTop = false;

  function handleScroll() {
    if (browser) {
      showBackToTop = window.scrollY > 300;
    }
  }

  /* ================= SCROLL ANIMATION ================= */
  let observer;

  onMount(() => {
    if (!browser) return; // 🔥 important

    startAuto();

    const sections = document.querySelectorAll('.section-animate');

    sections.forEach(el => el.classList.add('section-visible'));

    observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('section-visible');
        }
      });
    }, { threshold: 0.1 });

    sections.forEach(el => observer.observe(el));

    window.addEventListener('scroll', handleScroll);
  });

  onDestroy(() => {
    stopAuto();

    if (browser) {
      window.removeEventListener('scroll', handleScroll);
    }

    if (observer) observer.disconnect();
  });

  /* ================= SCROLL TO TOP ================= */
  function scrollToTop() {
    if (browser) {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  }
</script>
<!-- MAIN CONTENT (everything below is exactly as in your original) -->
<main class="site-container">
  <!-- HERO -->
  <section class="hero section-animate">
    <div class="hero-content">
     <h1>R Square HR Services <span>– a company that cares</span></h1> 
      <p class="hero-desc">We all know that Management of any organisation needs “7 Ms” like men, materials, machines, methods, morals, minutes and money to perform their functions and achieve their objectives and goals. R SQUARE HR SERVICES is known for contribution towards “men, methods, morals and minutes” ultimately saving machine time, money and enhancing service and profitability.</p>
      <p class="hero-desc">R SQUARE HR Services (R SQUARE) is an MSME company founded by Shri S N Rao, Former Head-HR of Indian Institute of Management Ahmedabad and Former Chief General Manager, The Gujarat State Civil Supplies Corporation Limited, a public sector enterprise of the Government of Gujarat. During his tenure with GSCSC, Mr. S N Rao and his team were awarded with CSI-Nihilent Award for e-Governance: 2011-12, National Award for e-Governance: 2012-13, and CSI-Nihilent Award for Supply-Logistics using Information & Communication Technology: 2012-13 from the Government of India. R Square is an innovator in HR solutions with strategy consulting and analytics. R Square core focus is to support business operations of the clients to enhance their efficiency and effectiveness and in turn increase overall productivity and profit by providing all possible combinations of HR and IT solutions.</p>
      <div class="hero-buttons">
        <button class="btn-primary btn-large">Explore services</button>
        <button class="btn-outline btn-large">Talk to our expert</button>
      </div>
    </div>
    <div class="hero-image">
      <img class="hero-img" src="https://media.istockphoto.com/id/2083454645/photo/male-boss-discussing-online-project-with-employee-in-the-office.jpg?s=170667a&w=0&k=20&c=YAjWMxEJRSlaQeT0N7Ki3moDBi-hU3ky8s9IHEzXcs8=" alt="Boss and employee">
    </div>
  </section>

  <!-- MISSION & VISION -->
  <section id="mission" class="mission-vision section-animate">
    <h2 class="section-heading">Mission & Vision</h2>
    <p class="mv-subhead">R SQUARE HR SERVICE’s vision is to emerge as one of the most respected HR services companies in the world anchored on the values of growth, professionalism, dignity and diversity.</p>
    <p class="mv-subhead">R SQUARE HR promotes learning with humility, serving with dignity and growing with integrity. Members of R SQUARE HR care about customers deeply and deliver best-in-class solutions keeping the interest of all other stakeholders in mind. R SQUARE HR will combine the power of technology with the capability of its members to deliver value to its stakeholders through rigorous execution.</p>
    <!-- All other paragraphs from the original go here (keep them) -->
  </section>

  <!-- SERVICES -->
  <section id="services" class="services section-animate">
    <h2 class="section-heading">Our comprehensive HR services</h2>
    <h3 class="service-section-title">PERMANENT RECRUITMENT:</h3>
    <p class="service-text">R SQUARE HR Services is basically an effort to create value in talent management domains and business consulting services. We specialize in talent acquisition, deployment & outsourcing, corporate learning & development to name a few. We also cater to the organization consulting space primarily servicing the industry through business process improvement, managerial outsourcing and other organizational development interventions. We are connected to companies in India helping them source best solutions to their intellectual & resourcing needs for both their onsite / offshore requirements. Team R SQUARE HR is a pool of well qualified and experienced professionals in their respective areas, which enables them to serve key consulting needs across all corporate disciplines & promote quality service delivery through its people, affiliations & associates in India & Overseas.</p>
    <!-- more paragraphs... -->

    <div class="services-grid">
      <!-- Card 1 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-bullhorn"></i><img src="/images/strategy.png" alt=""></div>
        <h4>Consultancy for HR Strategies, Policies & Services</h4>
        <p>Consultancy for HR strategies, policies & services.</p>
      </div>
      <!-- Card 2 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-chalkboard-user"></i><img src="/images/training.png" alt=""></div>
        <h4>Training & Development</h4>
        <p>Upskill teams with expert modules (soft skills, leadership, techno‑management).</p>
      </div>
      <!-- Card 3 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/cv.png" alt=""></div>
        <h4>Search & Recruitment Services</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 4 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/hr-outsourcing.png" alt=""></div>
        <h4>Recruitment Process Outsourcing</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 5 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-people-arrows"></i><img src="/images/job.png" alt=""></div>
        <h4>Contract Staffing (Workforce Service on Outsourcing basis)</h4>
        <p>Workforce on outsourcing, flexi‑staffing with full compliance.</p>
      </div>
      <!-- Card 6 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/outsourcing.png" alt=""></div>
        <h4>Functional HR Services on Outsourcing basis</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 7 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-calculator"></i><img src="/images/insight.png" alt=""></div>
        <h4>Processing Payroll and maintaining legal compliance</h4>
        <p>Payroll processing, legal compliance, NAPS, apprenticeship management.</p>
      </div>
      <!-- Card 8 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/OIP%20(16).webp" alt=""></div>
        <h4>National Apprenticeship Promotion Scheme (NAPS)</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 9 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/legal-system.png" alt=""></div>
        <h4>Legal Compliance</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 10 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-gavel"></i><img src="/images/compliant.png" alt=""></div>
        <h4>Legal Services under RTI</h4>
        <p>Legal compliance, RTI advisory, labour law liaison.</p>
      </div>
      <!-- Card 11 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-umbrella-beach"></i><img src="/images/web.png" alt=""></div>
        <h4>Tourism Specific Solutions</h4>
        <p>Tourist guide training, cultural programs, destination management.</p>
      </div>
      <!-- Card 12 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-hand-holding-heart"></i><img src="/images/collective.png" alt=""></div>
        <h4>Supporting CSR Activity</h4>
        <p>Support CSR activities, collaborate with institutions & universities.</p>
      </div>
      <!-- Card 13 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/9887500.png" alt=""></div>
        <h4>Collaborating with various institutions</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 14 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-magnifying-glass"></i><img src="/images/cultural-activities-concept-icon-leisure-pastime-entertainment-idea-thin-line-illustration-visiting-cinema-city-sightseeing-tour-isolated-outline-drawing-editable-stroke-vector.jpg" alt=""></div>
        <h4>Cultural Activities (musical programs, films, Gujarati serials, drama etc.)</h4>
        <p>Executive search, mass hiring, RPO – permanent & contract staffing.</p>
      </div>
      <!-- Card 15 -->
      <div class="service-card">
        <div class="service-icon"><i class="fas fa-laptop-code"></i><img src="/images/consultant.png" alt=""></div>
        <h4>IT Consultancy</h4>
        <p>Tech solutions, e‑governance, digital transformation support.</p>
      </div>
    </div>
  </section>

  <!-- ABOUT -->
  <section id="about" class="about section-animate">
  <h2 class="section-heading">About R Square HR Services</h2>
  <div class="about-grid">
    <!-- Left column: descriptive text -->
    <div class="about-text">
      <p><strong>R SQUARE HR Services (R SQUARE)</strong> is an MSME company founded by <strong>Shri S N Rao</strong>, Former Head-HR of IIM Ahmedabad and Former Chief General Manager, The Gujarat State Civil Supplies Corporation Limited (a PSU of Government of Gujarat). During his tenure, his team was awarded the CSI-Nihilent Award for e‑Governance (2011‑12), National Award for e‑Governance (2012‑13), and the CSI-Nihilent Award for Supply‑Logistics using ICT from the Government of India.</p>
      <p><strong>Mission & Vision:</strong> To emerge as one of the most respected HR services companies, anchored on growth, professionalism, dignity and diversity. We promote learning with humility, serving with dignity and growing with integrity. We combine the power of technology with the capability of our members to deliver value through rigorous execution.</p>
      <p>We have prepared HR Policy Manuals for IIM Ahmedabad, IIM Udaipur, India International Boolean Exchange (IFSC) Ltd., Sahaj Solar Limited, and provided training, recruitment & staffing to Tourism Corporation of Gujarat, Statue of Unity, Forest Department, Gujarat Council of Science City, Voltbek, Tenneco and many more.</p>
    </div>

    <!-- Right column: founder card with background image -->
    <div class="founder-card">
      <div class="card-overlay"></div>
      <div class="card-content">
        <h3 class="founder-name">Shri S N Rao</h3>
        <p class="founder-title">Founder & Mentor</p>
        <p class="founder-bio">
          Former Head-HR, IIM Ahmedabad<br />
          Former CGM, Gujarat State Civil Supplies Corp.<br />
          Over 4 decades of experience in HR, governance & training.
        </p>
        <div class="founder-awards">
          <span class="award-badge">CSI-Nihilent Award</span>
          <span class="award-badge">National e‑Governance Award</span>
        </div>
      </div>
    </div>
  </div>
</section>

  <!-- CAROUSEL -->
  <section class="carousel-section section-animate">
    <h2 class="section-heading">Success stories in action</h2>
    <p class="section-subhead">Real impact – from tourist guides to large‑scale training programs.</p>
    <div class="carousel-container" on:mouseenter={stopAuto} on:mouseleave={startAuto}>
      <div class="carousel-inner" style="transform: translateX(-{currentIndex * 100}%);">
        {#each slides as slide, i}
          <div class="carousel-slide" style="background-image: url({slide.image});">
            <div class="slide-content">
              <span class="slide-tag">{slide.tag}</span>
              <h3>{slide.title}</h3>
              <p>{slide.desc}</p>
            </div>
          </div>
        {/each}
      </div>
      <button class="carousel-control prev" on:click={prev}><i class="fas fa-chevron-left"></i></button>
      <button class="carousel-control next" on:click={next}><i class="fas fa-chevron-right"></i></button>
      <div class="carousel-indicators">
        {#each slides as _, i}
          <span class="indicator {i === currentIndex ? 'active' : ''}" on:click={() => currentIndex = i}></span>
        {/each}
      </div>
    </div>
    </section>
  
  <!-- PARTNERS -->
  <section class="partners section-animate">
    <h2 class="section-heading">Our valued partners & collaborators</h2>
    <div class="partner-grid">
      <div class="partner-item"><img class="partner-icon" src="/images/IIM.webp" alt=""><p>IIM Ahmedabad</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/IIMU_Logo.jpg" alt=""><p>IIM Udaipur</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/silver-oak-university-logo.jpg" alt=""><p>Silver Oak University</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Sardarkrushinagar_Dantiwada_Agricultural_University_Gujarat.jpg" alt=""><p>Sardarkrushinagar Agricultural University</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/gujarat%20university.webp" alt=""><p>Gujarat University</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Nirma%20University.jpg" alt=""><p>Nirma University</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/GLS%20university.png" alt=""><p>GLS University</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Ahmedabad%20Management%20Association.jpeg" alt=""><p>Ahmedabad Management Association</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/SARDAR%20institute.webp" alt=""><p>Sardar Patel Institute</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Knowledge%20Consortium%20Of%20Gujarat.webp" alt=""><p>Knowledge Consortium of Gujarat</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Gujarat%20Disaster%20Management%20Institute.png" alt=""><p>Gujarat Disaster Management Institute</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/computer-society-of-india.jpg" alt=""><p>Computer Society of India</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Balaji%20Group%20of%20Colleges.webp" alt=""><p>Balaji Group of Colleges</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Tirupati group.png" alt=""><p>Tirupati Institutions</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/FOREST%20DEPT.jpg" alt=""><p>Forest Department, Gujarat</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/Tourism%20Corporation%20of%20gujarat.png" alt=""><p>Tourism Corporation of Gujarat</p></div>
      <div class="partner-item"><img class="partner-icon" src="/images/sarwamangal school.webp" alt=""><p>sarwamangal school</p></div>
    </div>
  </section>

  <!-- CONTACT -->
  <section id="contact" class="contact section-animate">
    <h2 class="section-heading">Get in touch</h2>
    <div class="contact-container">
      <div class="contact-info">
        <h3>R Square HR Services</h3>
        <div class="contact-detail"><img src="/images/placeholder.png" alt=""><span>421, 4th Floor, Samaan Complex, Opp. Satyam Mall, Near Jodhpur Cross Roads, Satellite, Ahmedabad‑380015.</span></div>
        <div class="contact-detail"><img src="/images/telephone.png" alt=""><span>+91 99784 06366 , +91 99246 67855 <br> Phone: +91 79 4009 3259</span></div>
        <div class="contact-detail"><img src="/images/gmail.png" alt=""><span>riddhish@rsquarehr.com, snrao@rsquarehr.com</span></div>
        <div style="margin-top:32px;"><button class="btn-primary">Send enquiry</button></div>
      </div>
      <div class="map-container">
        <iframe src="https://maps.google.com/maps?q=421%204th%20Floor%20Samaan%20Complex%20Opp%20Satyam%20Mall%20Jodhpur%20Cross%20Roads%20Satellite%20Ahmedabad%20380015&z=16&output=embed" loading="lazy"></iframe>
      </div>
    </div>
  </section>
</main>
<!-- PERFECT BACK-TO-TOP BUTTON -->
{#if showBackToTop}
  <button class="back-to-top" on:click={scrollToTop} aria-label="Back to top">
  <img src="/images/up-arrow.png" alt="Go to top" />
</button>
{/if}

<!-- GLOBAL STYLES (scoped) -->
<style>
  /* ========== GLOBAL RESET & BASE ========== */
  /* ===== RESET & BASE (unchanged) ===== */
  :global(body) {
    margin: 0;
    font-family: 'Inter', sans-serif;
    background-color: #FAF7F2;
    color: #2E3B4E;
    scroll-behavior: smooth;
    padding-top: 90px;
    overflow-x: hidden;
  }

  .site-container {
    max-width: 1440px;
    margin: 0 auto;
    padding: 0 32px;
    width: 100%;
  }

  /* ===== ANIMATIONS ===== */
  .section-animate {
    opacity: 1;
    transform: translateY(0);
    transition: opacity 0.8s cubic-bezier(0.2, 0.9, 0.3, 1), transform 0.8s cubic-bezier(0.2, 0.9, 0.3, 1);
  }

  /* ===== The rest of your original styles (unchanged) ===== */
  /* (All your existing styles from .hero to the end remain exactly as you wrote them) */

  /* ===== HERO ===== */
  .hero {
    display: flex;
    align-items: center;
    gap: 50px;
    padding: 30px 0 70px;
    flex-wrap: wrap;
  }

  .hero-content {
    flex: 1 1 45%;
    min-width: 300px;
  }

  .hero-content h1 {
    font-size: clamp(36px, 5.5vw, 56px);
    font-weight: 700;
    line-height: 1.2;
    letter-spacing: -1.5px;
    color: #1E3A4A;
    margin-bottom: 24px;
  }

  .hero-content h1 span {
    color: #D96C4E;
    border-bottom: 3px solid rgba(239,185,120,0.3);
  }

  .hero-desc {
    text-align: justify;
    font-size: 1.05rem;
    color: #4a5a6b;
    margin-bottom: 28px;
    max-width: 620px;
  }

  .hero-buttons {
    display: flex;
    gap: 16px;
    flex-wrap: wrap;
  }

  .btn-large {
    padding: 15px 42px;
    font-size: 16px;
    border-radius: 50px;
    font-weight: 600;
    transition: all 0.25s;
  }

  /* ===== PRIMARY BUTTON (GRADIENT THEME) ===== */
.btn-primary {
  background: linear-gradient(135deg, #6B0F9C 0%, #B91372 40%, #B91372 100%);
  color: white;
  border: none;
  border-radius: 40px;
  padding: 12px 28px;
  font-weight: 600;
  letter-spacing: 0.4px;
  cursor: pointer;
  transition: all 0.35s ease;
  box-shadow: 0 10px 20px -10px rgba(185, 19, 114, 0.6);
  position: relative;
  overflow: hidden;
}

/* Hover */
.btn-primary:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 20px 30px -12px rgba(185, 19, 114, 0.8);
  background: linear-gradient(135deg, #7c18b5 0%, #d81b80 40%, #d81b80 100%);
}

/* Shine effect */
.btn-primary::before {
  content: "";
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(
    120deg,
    transparent,
    rgba(255, 255, 255, 0.4),
    transparent
  );
  transition: 0.6s;
}

.btn-primary:hover::before {
  left: 100%;
}


/* ===== OUTLINE BUTTON (MATCH THEME) ===== */
.btn-outline {
  background: transparent;
  border: 2px solid #B91372;
  color: #B91372;
  border-radius: 40px;
  padding: 12px 28px;
  font-weight: 600;
  letter-spacing: 0.4px;
  cursor: pointer;
  transition: all 0.35s ease;
}

/* Hover */
.btn-outline:hover {
  background: linear-gradient(135deg, #6B0F9C 0%, #B91372 40%, #B91372 100%);
  color: white;
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 15px 25px -10px rgba(185, 19, 114, 0.6);
}

  .hero-image {
    flex: 1 1 45%;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 300px;
  }

  .hero-img {
    max-width: 100%;
    border-radius: 30px;
    box-shadow: 0 25px 40px -14px rgba(30,58,74,0.25);
    transition: transform 0.4s;
    width: 100%;
    height: auto;
    object-fit: cover;
    aspect-ratio: 4/3;
  }

  .hero-image:hover .hero-img {
    transform: scale(1.01);
  }

  /* ===== MISSION/VISION ===== */
  .mission-vision {
    background: linear-gradient(145deg, #ffffff, #f9f4ed);
    border-radius: 48px;
    padding: 48px 40px;
    /* margin: 40px 0 20px; */
    box-shadow: 0 15px 35px -12px rgba(30,58,74,0.15);
    border: 1px solid #efe2d4;
  }

  .section-heading {
    font-size: clamp(32px, 4vw, 42px);
    font-weight: 700;
    color: #1E3A4A;
    margin-bottom: 28px;
    letter-spacing: -1px;
    position: relative;
    display: inline-block;
  }

  .section-heading::after {
    content: '';
    position: absolute;
    bottom: -8px;
    left: 0;
    width: 80px;
    height: 4px;
    background: #EFB978;
    border-radius: 4px;
  }

  .mv-subhead {
    color: #4a5a6b;
    font-size: 1.05rem;
    margin-bottom: 18px;
    text-align: justify;
    line-height: 1.6;
  }

  /* ===== SERVICES ===== */
  .services {
    margin: 60px 0;
  }

  .service-section-title {
    color: #1E3A4A;
    font-size: clamp(22px, 3vw, 28px);
    margin: 32px 0 16px;
    font-weight: 600;
  }

  .service-text {
    color: #4a5a6b;
    font-size: 1.05rem;
    margin-bottom: 18px;
    text-align: justify;
  }

  .services-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 30px;
    margin: 40px 0;
  }

  .service-card {
    background: #ffffff;
    border: 1px solid #efe2d4;
    border-radius: 36px;
    padding: 32px 28px;
    transition: all 0.4s cubic-bezier(0.2, 0, 0, 1);
    box-shadow: 0 8px 18px -8px rgba(0,0,0,0.05);
    position: relative;
    overflow: hidden;
  }

  .service-card::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 6px;
    height: 0%;
    background: linear-gradient(135deg, #6B0F9C 0%, #B91372  40%, #B91372 100%);
    transition: height 0.4s;
  }

  .service-card:hover {
    transform: translateY(-12px);
    border-color: #EFB978;
    box-shadow: 0 30px 45px -15px rgba(30,58,74,0.25);
  }

  .service-card:hover::before {
    height: 100%;
  }

  .service-icon {
    background: #f5ede4;
    width: 70px;
    height: 70px;
    border-radius: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 24px;
    transition: 0.3s;
  }

  .service-card:hover .service-icon {
    background: #EFB978;
  }

  .service-icon i {
    font-size: 36px;
    color: #1E3A4A;
  }

  .service-card:hover .service-icon i {
    color: white;
  }

  .service-icon img {
    width: 36px;
    height: 36px;
    object-fit: contain;
  }

  .service-card h4 {
    font-size: 22px;
    margin-bottom: 16px;
    color: #1E3A4A;
    font-weight: 600;
  }

  .service-card p {
    color: #4a5a6b;
    line-height: 1.6;
  }

  /* ===== ABOUT ===== */
 /* ===== ABOUT (UPDATED – FULLY RESPONSIVE) ===== */
.about {
  margin: 60px 0;
}

.about-grid {
  display: flex;
  gap: 40px;
  align-items: stretch;
  background: white;
  border-radius: 48px;
  padding: 48px;

  /* Enhanced shadow – multiple layers for realistic depth */
  box-shadow: 
    0 30px 60px -20px rgba(30, 58, 74, 0.3),
    0 10px 30px -12px rgba(0, 0, 0, 0.2),
    inset 0 1px 2px rgba(255, 255, 255, 0.8);

  border: 1px solid rgba(239, 185, 120, 0.2);
  transition: box-shadow 0.4s ease, transform 0.4s ease, border-color 0.3s ease;
}

.about-grid:hover {
  box-shadow: 
    0 40px 80px -20px rgba(30, 58, 74, 0.4),
    0 15px 40px -10px rgba(0, 0, 0, 0.25),
    inset 0 1px 3px rgba(255, 255, 255, 0.9);
  transform: translateY(-6px);
  border-color: rgba(239, 185, 120, 0.5);
}

.about-text {
  flex: 2;
  min-width: 300px;
}

.about-text p {
  text-align: justify;
  margin-bottom: 22px;
  color: #4a5a6b;
  font-size: 1.05rem;
  line-height: 1.7;
}

/* Founder card – background image */
.founder-card {
  flex: 1;
  min-width: 280px;
  border-radius: 40px;
  overflow: hidden;
  position: relative;
  background-image: url('/images/SNR-IIMA-PIC-NANDA-SIR.jpg'); /* Replace with your image path */
  background-size: cover;
  background-position: center 20%;
  background-repeat: no-repeat;
  box-shadow: 0 20px 30px -12px rgba(30,58,74,0.2);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  display: flex;
  align-items: flex-end;
}

.founder-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 30px 40px -15px rgba(30,58,74,0.3);
}

/* Gradient overlay for text readability */
.card-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(0deg, rgba(30,58,74,0.9) 0%, rgba(30,58,74,0.4) 70%, rgba(30,58,74,0.1) 100%);
  z-index: 1;
}

.card-content {
  position: relative;
  z-index: 2;
  color: white;
  padding: 30px 25px;
  width: 100%;
  text-align: left;
  backdrop-filter: blur(2px);
}

.founder-name {
  font-weight: 700;
  font-size: 28px;
  margin: 0 0 5px;
  color: white;
  text-shadow: 0 2px 5px rgba(0,0,0,0.3);
}

.founder-title {
  color: #EFB978;
  font-size: 18px;
  font-weight: 500;
  margin-bottom: 15px;
  letter-spacing: 0.5px;
  text-shadow: 0 1px 3px rgba(0,0,0,0.3);
}

.founder-bio {
  font-size: 0.95rem;
  line-height: 1.6;
  margin-bottom: 20px;
  color: rgba(255,255,255,0.9);
  text-shadow: 0 1px 3px rgba(0,0,0,0.3);
}

.founder-awards {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.award-badge {
  background: rgba(255,255,255,0.15);
  backdrop-filter: blur(4px);
  color: white;
  font-size: 0.75rem;
  font-weight: 600;
  padding: 5px 12px;
  border-radius: 30px;
  border: 1px solid rgba(239,185,120,0.5);
  box-shadow: 0 2px 5px rgba(0,0,0,0.2);
}

/* ----- RESPONSIVE FIXES FOR ABOUT SECTION ----- */
@media (max-width: 820px) {
  .about-grid {
    flex-direction: column;
    padding: 32px;
    gap: 30px;
  }

  .about-text {
    min-width: auto;
  }

  .about-text p {
    font-size: 1rem;
  }

  .founder-card {
    max-width: 100%;
    min-width: auto;
    width: 100%;
  }

  .founder-name {
    font-size: 26px;
  }

  .founder-title {
    font-size: 17px;
  }

  .founder-bio {
    font-size: 0.9rem;
  }
}

@media (max-width: 480px) {
  .about-grid {
    padding: 24px;
    border-radius: 32px;
  }

  .about-text p {
    font-size: 0.95rem;
    margin-bottom: 18px;
  }

  .founder-card {
    border-radius: 30px;
  }

  .card-content {
    padding: 25px 20px;
  }

  .founder-name {
    font-size: 24px;
  }

  .founder-title {
    font-size: 16px;
  }

  .founder-bio {
    font-size: 0.85rem;
    margin-bottom: 15px;
  }

  .award-badge {
    font-size: 0.7rem;
    padding: 4px 10px;
  }
}
  /* ===== CAROUSEL ===== */
  .carousel-section {
    margin: 40px 0;
  }

  .section-subhead {
    color: #4a5a6b;
    font-size: 18px;
    max-width: 600px;
    margin-bottom: 40px;
  }

  .carousel-container {
    position: relative;
    width: 100%;
    border-radius: 48px;
    overflow: hidden;
    box-shadow: 0 30px 50px -20px rgba(30,58,74,0.35);
  }

  .carousel-inner {
    display: flex;
    transition: transform 0.6s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  }

  .carousel-slide {
    flex: 0 0 100%;
    min-height: 420px;
    padding: 64px 60px;
    background-size: cover;
    background-position: center;
    display: flex;
    align-items: center;
    position: relative;
  }

  .carousel-slide::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: linear-gradient(95deg, rgba(30,58,74,0.9) 10%, rgba(30,58,74,0.3) 90%);
    border-radius: 48px;
  }

  .slide-content {
    position: relative;
    z-index: 2;
    color: white;
    max-width: 70%;
  }

  .slide-tag {
    background: #D96C4E;
    color: white;
    border-radius: 40px;
    padding: 6px 20px;
    font-size: 14px;
    font-weight: 700;
    display: inline-block;
    margin-bottom: 24px;
    letter-spacing: 0.3px;
  }

  .slide-content h3 {
    font-size: clamp(26px, 3.5vw, 38px);
    font-weight: 600;
    margin-bottom: 20px;
  }

  .slide-content p {
    font-size: 1.1rem;
    max-width: 600px;
    opacity: 0.9;
  }

  .carousel-control {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: white;
    border: none;
    width: 52px;
    height: 52px;
    border-radius: 60px;
    box-shadow: 0 12px 25px -8px rgba(0,0,0,0.3);
    font-size: 28px;
    cursor: pointer;
    color: #1E3A4A;
    transition: 0.2s;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10;
  }

  .carousel-control:hover {
    background: #EFB978;
    color: white;
    transform: translateY(-50%) scale(1.07);
  }

  .prev { left: 24px; }
  .next { right: 24px; }

  .carousel-indicators {
    position: absolute;
    bottom: 28px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 14px;
    z-index: 10;
  }

  .indicator {
    width: 12px;
    height: 12px;
    border-radius: 20px;
    background: rgba(255,255,255,0.5);
    cursor: pointer;
    transition: 0.25s;
  }

  .indicator.active {
    background: #EFB978;
    width: 34px;
  }

  /* ===== PARTNERS ===== */
  .partners {
    margin: 20px 0;
  }

 .partner-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 20px;
  margin-top: 18px;
}

/* GLASS CARD */
.partner-item {
  background: rgba(255, 255, 255, 0.25);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);

  border-radius: 20px;
  padding: 18px 12px;
  text-align: center;

  border: 1px solid rgba(255,255,255,0.3);

  transition: all 0.4s ease;
  cursor: pointer;

  box-shadow: 0 8px 25px rgba(0,0,0,0.1);
}

/* HOVER */
.partner-item:hover {
  transform: translateY(-10px) scale(1.05);
  background: #FFFFFF;
  box-shadow: 0 20px 40px rgba(0,0,0,0.2);
}

/* LOGO */
.partner-icon {
  height: 42px;
  object-fit: contain;
  margin-bottom: 10px;
  transition: 0.3s;
  filter: grayscale(100%);
}

/* LOGO HOVER */
.partner-item:hover .partner-icon {
  filter: grayscale(0%);
  transform: scale(1.1);
}

/* TEXT */
.partner-item p {
  font-size: 13px;
  font-weight: 600;
  color:#FFFFFF;
  margin: 0;
  transition: 0.3s;
}

/* TEXT HOVER */
.partner-item:hover p {
  color: #EFB978;
}

  .partner-icon {
    width: 60px;
    height: 40px;
    object-fit: contain;
    margin-bottom: 6px;
    filter: grayscale(0.2);
    transition: 0.2s;
  }

  /* .partner-item:hover .partner-icon {
    filter: brightness(0) invert(1);
  } */
   .partner-icon {
  filter: grayscale(100%);
  transition: 0.3s;
}

.partner-item:hover .partner-icon {
  filter: grayscale(0%);
  transform: scale(1.1);
}

  /* ===== CONTACT ===== */
  .contact {
    margin-bottom: 38px;
  } 

  .contact-container {
    display: flex;
    gap: 50px;
    flex-wrap: wrap;
    background: white;
    border-radius: 56px;
    padding: 56px 48px;
    border: 1px solid #efe2d4;
    box-shadow: 0 15px 35px -12px rgba(30,58,74,0.1);
  }

  .contact-info {
    flex: 1;
    min-width: 280px;
  }

  .contact-info h3 {
    font-size: 32px;
    color: #1E3A4A;
    margin-bottom: 30px;
  }

  .contact-detail {
    display: flex;
    align-items: center;
    gap: 16px;
    margin-bottom: 24px;
  }

  .contact-detail img {
    width: 38px;
    height: 38px;
    object-fit: contain;
    filter: brightness(0.2) sepia(1) hue-rotate(150deg) saturate(2);
  }

  .contact-detail span {
    color: #4a5a6b;
    font-size: 1.05rem;
  }

  .map-container {
    flex: 1;
    min-height: 280px;
    border-radius: 40px;
    overflow: hidden;
    box-shadow: 0 15px 30px -12px rgba(0,0,0,0.15);
  }

  .map-container iframe {
    width: 100%;
    height: 100%;
    border: 0;
    min-height: 280px;
  }

  /* ===== FOOTER ===== */
  /* footer {
    background: #1E3A4A;
     margin-top: 80px; 
    background: linear-gradient(135deg, #1f1c2c 0%, #fcb045  40%, #fcb045 100%);
  } */

 
  /* ===== BACK-TO-TOP (elegant) ===== */
  /* .back-to-top {
    position: fixed;
    bottom: 40px;
    right: 40px;
    width: 65px;
    height: 65px;
    background: linear-gradient(145deg, #EFB978, #D96C4E);
    color: #1E3A4A;
    border: none;
    border-radius: 50%;
    font-size: 30px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 15px 25px -8px rgba(200, 120, 70, 0.6);
    transition: all 0.3s cubic-bezier(0.25, 0.46, 0.45, 0.94);
    z-index: 999;
    animation: float 3s ease-in-out infinite;
  }

  .back-to-top:hover {
    transform: scale(1.15) translateY(-5px);
    background: linear-gradient(145deg, #f3ca9c, #EFB978);
    box-shadow: 0 25px 35px -10px #b28b5e;
  }

  .back-to-top .tooltip {
    position: absolute;
    top: -30px;
    right: 50%;
    transform: translateX(50%);
    background: #1E3A4A;
    color: #EFB978;
    font-size: 12px;
    font-weight: 600;
    padding: 4px 12px;
    border-radius: 20px;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.2s;
    white-space: nowrap;
    box-shadow: 0 5px 10px rgba(0,0,0,0.2);
  }

  .back-to-top:hover .tooltip {
    opacity: 1;
  }

  @keyframes float {
    0% { transform: translateY(0px); }
    50% { transform: translateY(-8px); }
    100% { transform: translateY(0px); }
  } */
/* ===== PREMIUM BACK TO TOP BUTTON ===== */
.back-to-top {
  position: fixed;
  bottom: 30px;
  right: 30px;
  width: 70px;
  height: 70px;
  border: none;
  background: transparent;
  cursor: pointer;
  z-index: 999;

  display: flex;
  align-items: center;
  justify-content: center;

  transition: all 0.3s ease;
}

/* Image styling */
.back-to-top img {
  width: 100%;
  height: 100%;
  object-fit: contain;

  transition: all 0.3s ease;
}

/* Hover animation */
.back-to-top:hover img {
  transform: translateY(-8px) scale(1.1);
}

/* Click effect */
.back-to-top:active img {
  transform: scale(0.9);
}
  /* ===== RESPONSIVE (additional) ===== */
  @media (max-width: 1024px) {
    .slide-content { max-width: 90%; }
  }

  @media (max-width: 820px) {
    .navbar {
      flex-direction: row; /* keep row on mobile */
      justify-content: space-between;
    }
  }

  @media (max-width: 768px) {
    :global(body) {
      padding-top: 80px;
    }
    .site-container {
      padding: 0 20px;
    }
    .hero {
      flex-direction: column;
    }
    .stats-row {
      flex-direction: column;
      align-items: center;
      text-align: center;
    }
    .carousel-control {
      width: 42px;
      height: 42px;
      font-size: 24px;
    }
    .prev { left: 12px; }
    .next { right: 12px; }
    .carousel-slide {
      padding: 40px 24px;
      min-height: 350px;
    }
    .footer-links {
      gap: 40px;
    }
    .back-to-top {
      bottom: 20px;
      right: 20px;
      width: 55px;
      height: 55px;
      font-size: 26px;
    }
  }

  @media (max-width: 480px) {
    .hero-content h1 { font-size: 38px; }
    .btn-large { width: 100%; }
    .service-card { padding: 24px 18px; }
    .partner-grid { padding: 30px 16px; }
    .contact-container { padding: 32px 20px; }
    .footer { flex-direction: column; }
  }

  img {
    max-width: 100%;
    height: auto;
  }

  /* ===== PARTNER GRID (COMPACT) ===== */
  .partner-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
    gap: 18px;
    margin-top: 18px;
  }

  .partner-item {
    background: #ffffff;
    border-radius: 18px;
    padding: 14px 10px;
    text-align: center;
    border: 1px solid #efe2d4;
    transition: all 0.35s ease;
    cursor: pointer;
    position: relative;
    overflow: hidden;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.6s ease forwards;
  }

  .partner-item:nth-child(1) { animation-delay: 0.1s; }
  .partner-item:nth-child(2) { animation-delay: 0.2s; }
  .partner-item:nth-child(3) { animation-delay: 0.3s; }
  .partner-item:nth-child(4) { animation-delay: 0.4s; }
  .partner-item:nth-child(5) { animation-delay: 0.5s; }
  .partner-item:nth-child(6) { animation-delay: 0.6s; }
  .partner-item:nth-child(7) { animation-delay: 0.7s; }
  .partner-item:nth-child(8) { animation-delay: 0.8s; }
  .partner-item:nth-child(9) { animation-delay: 0.9s; }
  .partner-item:nth-child(10) { animation-delay: 1s; }

  .partner-item::before {
    content: "";
    position: absolute;
    left: 0;
    top: 0;
    width: 4px;
    height: 0%;
    background: linear-gradient(135deg, #6B0F9C 0%, #B91372  40%, #B91372 100%);
    transition: 0.4s;
  }

  .partner-item:hover {
    transform: translateY(-8px) scale(1.05);
    box-shadow: 0 20px 30px -10px rgba(30,58,74,0.25);
    border-color: #EFB978;
  }

  .partner-item:hover::before {
    height: 100%;
  }

  .partner-icon {
    height: 40px;
    object-fit: contain;
    margin-bottom: 8px;
    transition: 0.3s;
    filter: grayscale(100%);
  }

  .partner-item:hover .partner-icon {
    filter: grayscale(0%);
    transform: scale(1.1);
  }

  .partner-item p {
    font-size: 12px;
    font-weight: 600;
    color: #1E3A4A;
    margin: 0;
    line-height: 1.3;
  }

  .partner-item:hover p {
    color: #D96C4E;
  }

  @keyframes fadeUp {
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
  #services,
#about,
#mission,
#contact {
  scroll-margin-top: 110px;
}
</style>