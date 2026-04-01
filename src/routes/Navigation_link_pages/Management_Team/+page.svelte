<script>
  import { onMount } from 'svelte';
  import { fade } from 'svelte/transition';

  let snRaoExpanded = false;
  let riddhishExpanded = false;

  onMount(() => {
    // Scroll reveal: add a subtle accent when elements come into view (no hiding)
    const elements = document.querySelectorAll('.reveal');
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('in-view');
        }
      });
    }, { threshold: 0.2 });
    elements.forEach(el => observer.observe(el));

    // 3D tilt effect on cards
    const tiltCards = document.querySelectorAll('.tilt-card');
    tiltCards.forEach(card => {
      card.addEventListener('mousemove', (e) => {
        const rect = card.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;
        const rotateX = -(y / rect.height - 0.5) * 12;
        const rotateY = (x / rect.width - 0.5) * 12;
        card.style.transform = `rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale(1.02)`;
      });
      card.addEventListener('mouseleave', () => {
        card.style.transform = 'rotateX(0) rotateY(0) scale(1)';
      });
    });
  });
</script>

<main class="management-page">
  <!-- Hero -->
  <div class="hero reveal">
    <div class="hero-content">
      <span class="hero-badge">Leadership</span>
      <h1>Meet the<br /><span class="gradient-text">Management Team</span></h1>
      <p>With decades of combined experience in HR, IT, and public governance, our leadership drives excellence and innovation.</p>
    </div>
    <div class="hero-shapes">
      <div class="shape shape-1"></div>
      <div class="shape shape-2"></div>
    </div>
  </div>

  <!-- S N RAO -->
  <!-- S N RAO -->
<div id="sn-rao" class="profile-card tilt-card reveal">
    <div class="profile-header">
      <div class="avatar">
        <img src="/images/sn-rao.jpg" alt="S N Rao" on:error={(e) => e.target.style.display = 'none'} />
      </div>
      <div class="profile-info">
        <h2>S N Rao</h2>
        <p class="role">CEO & Founder</p>
        <p class="tagline">Business Analyst | Consultant | Trainer</p>
      </div>
    </div>

    <div class="summary">
      <p>Mr. S N Rao is a postgraduate in Statistics with specialization in Operations Research and Econometrics from The M S University of Baroda (1980). He is an alumnus of IIM Ahmedabad and a Life Member of the Computer Society of India. With over 45 years of professional experience across IT, HR, Supply Chain, and Governance, he has held key leadership roles including CEO of R Square HR Services, Former Head – HR at IIM Ahmedabad, and Former Chief General Manager at The Gujarat State Civil Supplies Corporation Ltd. He is the architect of award‑winning e‑Governance systems and food fortification programs that have benefited millions.</p>
    </div>

    <div class="highlights">
      <span>🏆 National e‑Governance Award (2012-13)</span>
      <span>🏅 CSI‑Nihilent Award (2011-12)</span>
      <span>🎓 IIM Ahmedabad Alumnus</span>
    </div>

    {#if snRaoExpanded}
      <div class="expanded" transition:fade={{ duration: 400 }}>
        <div>
          <h4>📌 Key Roles & Experience</h4>
          <ul>
            <li>CEO, R Square HR Services, Ahmedabad</li>
            <li>Former Head – HR, IIM Ahmedabad</li>
            <li>Former Chief General Manager, The Gujarat State Civil Supplies Corporation Ltd.</li>
            <li>Managing Partner, CIEL HR Services Ltd., Ahmedabad</li>
            <li>Director, Sahaj Solar Ltd., Ahmedabad</li>
            <li>Member, Board of Studies, Sardar Krushinagar Dantiwada Agricultural University</li>
            <li>Former HR Consultant to IIM Udaipur, India International Bullion Exchange IFSC Ltd., Gujarat Ecology Commission</li>
            <li>Trustee, Brahmbhatt Vidyottejak Mandal, Vadodara</li>
          </ul>
        </div>
        <div>
          <h4>🎓 Education & Background</h4>
          <p>Mr. Rao holds a postgraduate degree in Statistics with specialization in Operations Research and Econometrics from The M S University of Baroda (1980). He acquired knowledge in IT, Commerce, and HR from IIM Ahmedabad and IIM Bangalore. He is an alumnus of IIM Ahmedabad and a Life Member of the Computer Society of India.</p>
          <p>He began his career as a Faculty of Statistics at MSU Baroda (1980-81) before transitioning to IT in 1981-82. He rose from Programmer to Chief General Manager at GSCSC, heading IT, Commerce, and Administration. Later, he served as Head‑HR at IIM Ahmedabad.</p>
        </div>
        <div>
          <h4>⚡ Expertise Domains (45+ years)</h4>
          <div class="expertise-tags">
            <span>IT – ERP, MIS, DSS</span> <span>e-Tendering & Reverse Auction</span>
            <span>Quality Assurance</span> <span>Supply Chain & Logistics</span>
            <span>Food Fortification</span> <span>HR Management & Legal Compliance</span>
            <span>Tourism & Hospitality</span> <span>Education & Skill Development</span>
            <span>Psychometric Assessment</span> <span>Renewable Energy</span>
          </div>
        </div>
        <div>
          <h4>🌟 Notable Contributions</h4>
          <ul>
            <li>Conceptualized and implemented Edible Oil, Whole Wheat Flour, Soya Flour fortification, and Nutri Candy to combat malnutrition. Invited by ILSI-India to chair international sessions on food fortification.</li>
            <li>Key team member for total computerization of Public Distribution System – received National e‑Governance Awards (2011–13).</li>
            <li>Trained 400+ tribal youth near Statue of Unity, providing employment in tourism, housekeeping, AR/VR operations, etc.</li>
            <li>Trained and provided Tourist Guides at Statue of Unity and other tourist locations; work applauded by Honorable PM of India in Mann Ki Baat and public meetings.</li>
            <li>Guest faculty at Nirma University, Gujarat University, AMA, SPIPA, CSI, ILSI-India, GDM, and many more.</li>
            <li>Business partner for English Excellence Training Program (SCOPE, Education Department, Government of Gujarat).</li>
            <li>Works for farmers, rural students, tribal youth, Indian art & culture, environment, renewable energy.</li>
            <li>Motivational speaker, poet, singer, and actor in Gujarati TV serials.</li>
          </ul>
        </div>
      </div>
    {/if}

    <button class="toggle-btn" on:click={() => snRaoExpanded = !snRaoExpanded}>
      {snRaoExpanded ? 'Show less' : 'Read more'}
    </button>
  </div>

<!-- RIDDHISH -->
<div id="riddhish-rao" class="profile-card tilt-card reveal">
    <div class="profile-header">
      <div class="avatar">
        <img src="/images/riddhish-rao.jpg" alt="Riddhish Rao" on:error={(e) => e.target.style.display = 'none'} />
      </div>
      <div class="profile-info">
        <h2>Riddhish Rao</h2>
        <p class="role">Director</p>
        <p class="tagline">HR & IT Leader</p>
      </div>
    </div>

    <div class="summary">
      <p>Mr. Riddhish Rao brings 13 years of total experience – 8 years in HR and 5 years in IT. As Director of R Square HR Services for the past 8 years, he specializes in Recruitment, Training, Placement, Legal Compliance, and HR Policies. His IT background provides a unique advantage in integrating technology with human resources.</p>
    </div>

    <div class="highlights">
      <span>💻 Microsoft MTA Certified</span>
      <span>🎭 Acting, Music & Debate Champion</span>
    </div>

    {#if riddhishExpanded}
      <div class="expanded" transition:fade={{ duration: 400 }}>
        <div>
          <h4>💼 Professional Background</h4>
          <ul>
            <li><strong>IT (2013–2017):</strong> Ran own IT company, worked as IT Professional – conceptualization, design, development, implementation of business applications, MIS, ERP.</li>
            <li><strong>HR (2017–present):</strong> Recruitment, Training, Placement, Legal Compliance, HR Policies.</li>
          </ul>
        </div>
        <div>
          <h4>🏅 Achievements & Skills</h4>
          <ul>
            <li>Microsoft Technology Associate (MTA) certification.</li>
            <li>Certificate from Arena Animation for "Flash & Scripting" course (48 hrs).</li>
            <li>1st prize in State Level Debate – Cyber Contour, GLS Institute (2011).</li>
            <li>1st prize in Singing & Poetry Writing – H.L. Institute (2006, 2008).</li>
            <li>11 years of Tabla training under Pdt Divyang Vakil.</li>
            <li>Selected for acting audition at Anupam Kher’s Actor Prepares.</li>
            <li>Won multiple prizes in debate, story narrating, singing, dance, poetry.</li>
            <li>Organized and participated in school/college annual functions and technical events.</li>
          </ul>
        </div>
      </div>
    {/if}

    <button class="toggle-btn" on:click={() => riddhishExpanded = !riddhishExpanded}>
      {riddhishExpanded ? 'Show less' : 'Read more'}
    </button>
  </div>

  <!-- RESOURCE TEAM -->
 <!-- RESOURCE TEAM -->
<div id="resource-team" class="team-section reveal">
    <h2>Our Resource Team</h2>
    <p>Dedicated professionals across diverse domains ensuring excellence.</p>
    <div class="team-grid">
      {#each [
        { area: "HR & Admin", count: 22 }, { area: "IT", count: 25 },
        { area: "Legal", count: 5 }, { area: "Hospitality & Tourism", count: 14 },
        { area: "Solar Energy", count: 7 }, { area: "Agriculture", count: 11 },
        { area: "Automobiles", count: 6 }, { area: "Apparel", count: 6 },
        { area: "Health Care", count: 10 }, { area: "Finance", count: 8 },
        { area: "Electronics", count: 5 }, { area: "Logistics", count: 5 },
        { area: "Fire", count: 5 }, { area: "Retail", count: 6 },
        { area: "Films", count: 7 }, { area: "Music", count: 9 }
      ] as item}
        <div class="team-card tilt-card">
          <div class="team-icon">★</div>
          <h3>{item.area}</h3>
          <p>{item.count} professionals</p>
        </div>
      {/each}
    </div>
  </div>
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700&display=swap');

  .management-page {
    max-width: 1280px;
    margin: 0 auto;
    padding: 48px 24px 80px;
    background: #ffffff;
    color: #1e2f3a;
    font-family: 'Inter', system-ui, -apple-system, sans-serif;
    line-height: 1.5;
  }

  /* Reveal elements are fully visible by default; scroll adds subtle extra style */
  .reveal {
    transition: all 0.3s ease;
  }
  .reveal.in-view {
    /* Optional: add a small border glow or shadow when scrolled into view */
  }

  /* Hero Section */
  .hero {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 60px;
  margin-bottom: 100px;
  padding: 60px 0;
}
  .hero-content {
    flex: 1;
  }
  .hero-badge {
  background: linear-gradient(135deg, #e6f0ff, #f5f9ff);
  color: #0044cc;
  font-weight: 600;
}
  .hero-content h1 {
  font-size: 3.2rem;
  font-weight: 800;
  line-height: 1.1;
}
  .gradient-text {
    background: linear-gradient(135deg, #2c6e9e, #d97a4a);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
  }
  .hero p {
  font-size: 1.1rem;
  color: #6b7c8a;
  margin-top: 16px;
}
  .hero-shapes {
    position: relative;
    width: 280px;
    height: 280px;
    flex-shrink: 0;
  }
  .shape {
    position: absolute;
    border-radius: 50%;
    background: linear-gradient(135deg, #2c6e9e20, #d97a4a20);
    animation: float 8s infinite ease-in-out;
  }
  .shape-1 {
    width: 200px;
    height: 200px;
    top: 0;
    left: 0;
  }
  .shape-2 {
    width: 140px;
    height: 140px;
    bottom: 0;
    right: 0;
    animation-delay: -2s;
  }
  @keyframes float {
    0%, 100% { transform: translateY(0) rotate(0deg); }
    50% { transform: translateY(-20px) rotate(8deg); }
  }

  /* Profile Cards */
  .profile-card {
  background: white;
  border-radius: 24px;
  padding: 40px;
  margin-bottom: 50px;

  border: 1px solid #e6ecf2;
  box-shadow: 0 10px 30px rgba(0,0,0,0.04);

  transition: 0.3s;
}

.profile-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 25px 50px rgba(0,0,0,0.08);
}
 .profile-header {
  display: flex;
  align-items: center;
  gap: 30px;
}

.profile-info h2 {
  font-size: 1.9rem;
}

.role {
  color: #0066ff;
}
  .avatar {
    width: 110px;
    height: 110px;
    border-radius: 50%;
    overflow: hidden;
    border: 3px solid #e2e8f0;
    background: #f8fafc;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  }
  .avatar img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  .profile-info h2 {
    font-size: 2rem;
    font-weight: 700;
    margin: 0;
    color: #1e2f3a;
  }
  .role {
    font-size: 1.1rem;
    font-weight: 600;
    color: #d97a4a;
    margin: 8px 0 4px;
  }
  .tagline {
    color: #6c7e8e;
    font-size: 0.95rem;
  }
  .summary {
  background: #f8fbff;
  border-left: 4px solid #0066ff;
  border-radius: 16px;
  padding: 20px 25px;
  margin-top: 20px;
}
  .highlights {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    margin: 20px 0 24px;
  }
  .highlights span {
    background: #f8fafc;
    padding: 6px 18px;
    border-radius: 40px;
    font-size: 0.9rem;
    font-weight: 500;
    color: #2c6e9e;
    border: 1px solid #e2e8f0;
  }
  .toggle-btn {
  background: #0066ff;
  color: white;
  border: none;
  padding: 10px 24px;
  border-radius: 30px;
  font-weight: 600;
}

.toggle-btn:hover {
  background: #0044cc;
}
  .expanded {
    margin-top: 32px;
    padding-top: 28px;
    border-top: 1px solid #eef2f5;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 32px;
  }
  .expanded h4 {
    font-size: 1.2rem;
    font-weight: 600;
    margin-bottom: 16px;
    color: #1e2f3a;
  }
  .expanded ul {
    padding-left: 1.2rem;
    margin: 0;
  }
  .expanded li {
    margin: 8px 0;
    color: #3a5568;
    line-height: 1.5;
  }
  .expanded p {
    margin: 12px 0;
    color: #3a5568;
  }
  .expertise-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-top: 8px;
  }
  .expertise-tags span {
    background: #f0f4fa;
    padding: 4px 12px;
    border-radius: 30px;
    font-size: 0.85rem;
    font-weight: 500;
    color: #2c6e9e;
  }

  /* Team Section */
  .team-section {
    text-align: center;
    margin-top: 80px;
  }
  .team-section h2 {
    font-size: 2.5rem;
    font-weight: 700;
    margin-bottom: 12px;
    color: #1e2f3a;
  }
  .team-section > p {
    color: #6c7e8e;
    font-size: 1rem;
    margin-bottom: 48px;
  }
  .team-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    gap: 28px;
  }
 .team-card {
  background: white;
  border-radius: 20px;
  padding: 20px;

  border: 1px solid #e6ecf2;
  transition: 0.3s;
}

.team-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 20px 40px rgba(0,0,0,0.08);
}
  .team-icon {
    font-size: 2rem;
    margin-bottom: 12px;
    color: #2c6e9e;
  }
  .team-card h3 {
    font-size: 1.1rem;
    font-weight: 600;
    margin: 12px 0 8px;
    color: #1e2f3a;
  }
  .team-card p {
    font-size: 1.3rem;
    font-weight: 700;
    color: #d97a4a;
    margin: 0;
  }

  /* Tilt card effect */
  .tilt-card {
    transition: transform 0.2s ease-out;
    transform-style: preserve-3d;
    will-change: transform;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .management-page {
      padding: 32px 20px 64px;
    }
    .hero {
      flex-direction: column;
      text-align: center;
    }
    .hero-content {
      text-align: center;
    }
    .hero p {
      margin: 0 auto;
    }
    .hero-shapes {
      display: none;
    }
    .profile-header {
      flex-direction: column;
      text-align: center;
    }
    .avatar {
      margin: 0 auto;
    }
    .profile-card {
      padding: 24px;
    }
    .expanded {
      grid-template-columns: 1fr;
    }
    .team-grid {
      grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
      gap: 20px;
    }
  }
  .team-card {
  background: #ffffff;
  border: 1px solid #efe2d4;
  border-radius: 36px;
  padding: 24px 20px;

  transition: all 0.4s cubic-bezier(0.2, 0, 0, 1);
  box-shadow: 0 8px 18px -8px rgba(0,0,0,0.05);

  position: relative;
  overflow: hidden;
}

/* LEFT BORDER ANIMATION (same as service cards) */
.team-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 6px;
  height: 0%;
  background: linear-gradient(135deg, #0066ff 0%, #0044cc 40%, #0066ff 100%);
  transition: height 0.4s;
}

/* HOVER EFFECT */
.team-card:hover {
  transform: translateY(-12px);
  border-color: #EFB978;
  box-shadow: 0 30px 45px -15px rgba(30,58,74,0.25);
}

/* BORDER GROW ON HOVER */
.team-card:hover::before {
  height: 100%;
}
</style>