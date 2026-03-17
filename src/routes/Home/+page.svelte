<svelte:head>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,400;14..32,500;14..32,600;14..32,700&display=swap" rel="stylesheet" />
  <!-- Font Awesome (you can also use a Kit or the official CDN) -->
  
</svelte:head>

<script>
  import { onMount } from 'svelte';
  onMount(() => {
    // ----- CAROUSEL LOGIC -----
    const container = document.getElementById('carouselContainer');
    const inner = document.getElementById('carouselInner');
    const slides = document.querySelectorAll('.carousel-slide');
    const prevBtn = document.getElementById('prevBtn');
    const nextBtn = document.getElementById('nextBtn');
    const indicators = document.querySelectorAll('.indicator');
    let currentIndex = 0;
    const totalSlides = slides.length;
    let autoInterval = null;
    const AUTO_INTERVAL_MS = 5000;

    function updateCarousel(index) {
      if (index < 0) index = totalSlides - 1;
      if (index >= totalSlides) index = 0;
      inner.style.transform = `translateX(-${index * 100}%)`;
      indicators.forEach((ind, i) => {
        if (i === index) ind.classList.add('active');
        else ind.classList.remove('active');
      });
      currentIndex = index;
    }

    function nextSlide() { updateCarousel(currentIndex + 1); }
    function prevSlide() { updateCarousel(currentIndex - 1); }
    function startAutoPlay() { stopAutoPlay(); autoInterval = setInterval(nextSlide, AUTO_INTERVAL_MS); }
    function stopAutoPlay() { if (autoInterval) { clearInterval(autoInterval); autoInterval = null; } }

    if (nextBtn && prevBtn) {
      nextBtn.addEventListener('click', function(e) { e.stopPropagation(); nextSlide(); stopAutoPlay(); startAutoPlay(); });
      prevBtn.addEventListener('click', function(e) { e.stopPropagation(); prevSlide(); stopAutoPlay(); startAutoPlay(); });
    }
    indicators.forEach((ind, idx) => {
      ind.addEventListener('click', function(e) { e.stopPropagation(); updateCarousel(idx); stopAutoPlay(); startAutoPlay(); });
    });

    if (container) {
      container.addEventListener('mouseenter', stopAutoPlay);
      container.addEventListener('mouseleave', startAutoPlay);
      startAutoPlay();
    }

    // ----- BACK TO TOP -----
    const backToTop = document.getElementById('backToTop');
    function scrollHandler() {
      if (backToTop) {
        backToTop.style.display = window.scrollY > 300 ? 'flex' : 'none';
      }
    }
    window.addEventListener('scroll', scrollHandler);
    if (backToTop) {
      backToTop.addEventListener('click', () => {
        window.scrollTo({ top: 0, behavior: 'smooth' });
      });
    }

    // Cleanup on component destroy
    return () => {
      stopAutoPlay();
      window.removeEventListener('scroll', scrollHandler);
      if (container) {
        container.removeEventListener('mouseenter', stopAutoPlay);
        container.removeEventListener('mouseleave', startAutoPlay);
      }
      if (nextBtn) nextBtn.removeEventListener('click', nextSlide);
      if (prevBtn) prevBtn.removeEventListener('click', prevSlide);
      // remove other listeners if needed (simplified)
    };
  });
</script>

<!-- Fixed header -->
<header class="header-fixed">
  <div class="header-container">
    <div class="navbar">
      <div class="logo-container">
      <img class="logo-img" src="/images/LOGO_PDF_invertedColor_page-0001.jpg">
      </div>
      <div class="nav-links">
        <a href="#">Home</a>
        <a href="#services">Services</a>
        <a href="#about">About</a>
        <a href="#mission">Mission & Vision</a>
        <a href="#contact">Contact</a>
      </div>
    </div>
  </div>
</header>


<!-- Main content -->
<div class="site-container">
  <!-- Hero section -->
  <section class="hero">
    <div class="hero-content">
      <h1>R Square HR Services <span>– a company that cares</span></h1>
      <p class="hero-desc">We all know that Management of any organisation needs “7 Ms” like men, materials, machines, methods, morals, minutes and money to perform their functions and achieve their objectives and goals. R SQUARE HR SERVICES is known for contribution towards “men, methods, morals and minutes” ultimately saving machine time, money and enhancing service and profitability.</p>
      <p class="hero-desc">R SQUARE HR Services (R SQUARE) is an MSME company founded by Shri S N Rao, Former Head-HR of Indian Institute of Management Ahmedabad  and Former Chief General Manager, The Gujarat State Civil Supplies Corporation Limited, a public sector enterprise of the Government of Gujarat. During his tenure with GSCSC, Mr. S N Rao and his team were awarded with CSI-Nihilent Award for e-Governance: 2011-12, National Award for e-Governance: 2012-13, and CSI-Nihilent Award for Supply-Logistics using Information & Communication Technology: 2012-13 from the Government of India. R Square is an innovator in HR solutions with strategy consulting and analytics. R Square core focus is to support business operations of the clients to enhance their efficiency and effectiveness and in turn increase overall productivity and profit by providing all possible combinations of HR and IT solutions.</p>
      <div class="hero-buttons">
        <button class="btn-primary btn-large">Explore services</button>
        <button class="btn-outline btn-large btn-secondary">Talk to our expert</button>
      </div>
    </div>
    <div class="hero-image">
      <i class="fas fa-hand-holding-heart"><img class="hero-img" src="https://media.istockphoto.com/id/2083454645/photo/male-boss-discussing-online-project-with-employee-in-the-office.jpg?s=170667a&w=0&k=20&c=YAjWMxEJRSlaQeT0N7Ki3moDBi-hU3ky8s9IHEzXcs8=" alt="Boss and employee" /></i>
    </div>
  </section>
  <!-- Mission & Vision -->
    <section id="Mission-Vision" class="Mission_Vision">
      <h2 class="section-heading">Mission_Vision</h2>
      <p class="Mission-Vision-subhead">R SQUARE HR SERVICE’s vision is to emerge as one of the most respected HR services companies in the world anchored on the values of growth, professionalism, dignity and diversity.</p>
<p class="Mission-Vision-subhead">R SQUARE HR promotes learning with humility, serving with dignity and growing with integrity. Members of R SQUARE HR care about customers deeply and deliver best-in-class solutions keeping the interest of all other stakeholders in mind. R SQUARE HR will combine the power of technology with the capability of its members to deliver value to its stakeholders through rigorous execution.</p>
    </section>

  <!-- Services section -->
  <section id="services" class="services">
    <h2 class="section-heading">Our comprehensive HR services</h2>
    <h2 class="Mission-Vision-heading" >PERMANENT RECRUITMENT:</h2>
    <p class="Mission-Vision-subhead">R SQUARE HR Services is basically an effort to create value in talent management domains and business consulting services. We specialize in talent acquisition, deployment & outsourcing, corporate learning & development to name a few. We also cater to the organization consulting space primarily servicing the industry through business process improvement, managerial outsourcing and other organizational development interventions. We are connected to companies in India helping them source best solutions to their intellectual & resourcing needs for both their onsite / offshore requirements.

Team R SQUARE HR is a pool of well qualified and experienced professionals in their respective areas, which enables them to serve key consulting needs across all corporate disciplines & promote quality service delivery through its people, affiliations & associates in India & Overseas. </p>
<p class="Mission-Vision-subhead">The main and only concern for R SQUARE HR is Human Resources. Today the company has its remarkable presence in various industries like Tourism, Education, Information Technology, Pharmaceuticals, Engineering, etc.Further accentuating our presence in the enterprise restructuring area we now deliver critical competitive advantage packages to small and medium enterprises for business process improvements and organizational excellence.</p>
<p class="Mission-Vision-subhead">We understand your hiring philosophy and are perfectly placed to undertake all your recruitment and staffing needs. We understand the role context and advertise in online and offline channels to attract the best talent. We undertake recruitment outsourcing services, which equips us to understand clearly challenges faced by the employers.</p>
<p class="Mission-Vision-subhead">Job seekers are increasingly relying on smartphones and mobile devices to conduct job searches. Ensuring that job posts and accompanying images are mobile optimized is essential for the success of hiring strategies. With the prevalence of social media, HR recruitment agencies like us understand the benefits of enhancing candidate experience. A positive experience enhances employer brand image immediately and immensely. An HR recruitment agency needs to maintain a healthy pipeline of candidates to meet hiring demands on a daily basis. A focused approach to develop a passive candidate pool is vital. Engaging with passive job seekers and unsuccessful candidates helps in not only enlarging the talent pool to work with, but also helps in strengthening employer reputation. With an aim to reign in infrastructural costs, a number of companies are opting to allow remote working employees. Manpower recruiting companies have to develop specific strategies to tap into the target candidate pool.</p>
<h2 class="Mission-Vision-heading" >TALENT ACQUISITION:</h2>   
<p class="Mission-Vision-subhead">R SQUARE HR strives to make sure our services are dedicated to support HR solution. Our in-house research and recruiting capabilities are extensive. We follow the best systems & processes in the recruitment industry, and so have the ability to serve our clients the relevant & quality profiles with a minimum lead-time.</p> 
<p class="Mission-Vision-subhead">We are committed for the accurate search & selection of professionals with the help of highly trained search team, widely spread network as well as existing database of skilled and experienced professionals.</p>
<p class="Mission-Vision-subhead">We specialize in Executive Search/ Middle management/ Board Member & CXO Search. In addition, we have expertise in Mass Hiring or Team Hiring within a stipulated time and we take these assignments on a specific & reasonable term unlike general requisitions.</p>
<p class="Mission-Vision-subhead">For an excellent output in the client side we follow a robust internal process which is time tested and which intertwines the recruiters, leads and directors. We concentrate on research methods, client confidentiality, documentation, strict timelines, reporting and follow up process, constant service feedback.</p>
<p class="Mission-Vision-subhead">Our team includes experienced consultants, who work across the key industry segments and functions to deliver the executive search service. Whether you are a startup, a mid-market firm looking to expand your Board, or a Fortune 500 company working on your succession plan, we stand by to partner with you.</p>
<p class="Mission-Vision-subhead">•	R SQUARE Differentiation: Unlike our generalist competitors, we choose to specialize - in industries and applications that are highly information-intensive and such increasingly challenging areas as HR consulting and executive search along with our specialization in IT recruitment.</p>
<p class="Mission-Vision-subhead">•	Presence across Industries & Locations: Our clients are spread across India in different segments including Tourism, Education, Information Technology, Pharmaceuticals, Engineering, Manufacturing, Services and Retail etc.</p>
<p class="Mission-Vision-subhead">•	Leadership: They see R SQUARE HR as a leader too - often as the biggest "specialist" company, more often as leading consulting thinking and setting the standard in business values and service delivery.</p>
<p class="Mission-Vision-subhead">•	Secret of our success: We are sure that with our experience and expertise in the field of recruitment & training, we together with the client can accomplish optimum productivity commitment of client’s employees to achieve the goal of client organization.</p>
<h2 class="Mission-Vision-heading" >R SQUARE’s Executive Search:</h2>
<p class="Mission-Vision-subhead">At R SQUARE HR, we invest in understanding the context of the client, the organization’s structure, the role to be filled and its context. This understanding is captured in a document and forms the basis of the next few steps in the search process, followed by the premier staffing companies in India.</p>
<p class="Mission-Vision-subhead">Based on the brief prepared earlier in the process, we create a plan to identify the possible matches. We review this plan with the client and co-create it. Then, we plan the communication with the potential candidates. We get into the next step, talent attraction, followed by talent assessment.</p>
<p class="Mission-Vision-subhead">We present our assessment of the short-listed candidates to the client and discuss further to draw up meeting schedules. With debriefs and reference checks happening simultaneously, we arrive at the selects. Offers are worked out in collaboration with the client. Throughout the process, we anchor the relationship with the candidates and help them set the ‘right expectations’ about the process, the role, the support systems and the offer.</p>
<p class="Mission-Vision-subhead">We present our assessment of the short-listed candidates to the client and discuss further to draw up meeting schedules. With debriefs and reference checks happening simultaneously, we arrive at the selects. Offers are worked out in collaboration with the client. Throughout the process, we anchor the relationship with the candidates and help them set the ‘right expectations’ about the process, the role, the support systems and the offer.
<h2 class="Mission-Vision-heading-sub" >R SQUARE HR Advantages</h2>
<h2 class="Mission-Vision-heading-sub" >Process</h2>
<p class="Mission-Vision-subhead">•	Robust assignment tracking Process.</p>
<p class="Mission-Vision-subhead">•	Special 2-member team working on each assignment.</p>
<p class="Mission-Vision-subhead">•	Use of data sciences and analytics to match roles with candidates.</p>
<h2 class="Mission-Vision-heading-sub" >Research</h2>
<p class="Mission-Vision-subhead">•	Strong research team with exceptional market intelligence to give you the best service among the recruitment agencies in India.</p>
<p class="Mission-Vision-subhead">•	Internal Knowledge Management Framework.</p>
<h2 class="Mission-Vision-heading-sub" >Depth</h2>
<p class="Mission-Vision-subhead">•	Consultants professionally qualified with experience in the respective domains for providing you a detailed and customized experience.</p>
<p class="Mission-Vision-subhead">•	Proven expertise of having recruited top management hires.</p>
<h2 class="Mission-Vision-heading" >SELECTION SERVICE:</h2>
<p class="Mission-Vision-subhead">At R SQUARE HR Services, we understand the importance of filling up a vacancy whether it’s a new or a replacement position. Realizing fully well that speed and selection of the right profiles are of the essence, our experienced recruiters are specially trained to understand the context in which the candidate has to perform; this in tandem with our unique algorithm used in the matching process ensures that we find the best matches in the quickest possible time.</p>
<p class="Mission-Vision-subhead">We believe that context is very important in recruitment apart from the job description, the resume and the competencies that a candidate has. Our consultants are trained to understand the context of an organization and that of the role in the organisation’s structure. These contexts are unique and need to be understood. Similarly, a candidate has a context apart from the competencies that he/she possesses. We not only match the job description of the role with the competencies of a candidate but also assess if the context of the organization and the role match with the competencies of the candidate. Thus the match ensures finding the best candidate for the job while also improving the retention rate. We are aspiring to be one of the premier staffing companies in India.</p>
<h2 class="Mission-Vision-heading-sub" >Advantages of R SQUARE HR</h2>
<p class="Mission-Vision-subhead">•	Expertise in attracting and finding the best candidates using advanced data sciences.</p>
<p class="Mission-Vision-subhead">•	Expert consultants – that helps you reduce your hiring time and cost with their rich industry experience.</p>
<p class="Mission-Vision-subhead">•	Unsuccessful candidates are added to a database to aid a referral process</p>
<p class="Mission-Vision-subhead">•	Ability to identify individuals who technically and culturally match a client’s requirements with the advanced use of algorithms.</p>
<p class="Mission-Vision-subhead">•	Proven methodology for notifying unsuccessful candidates while protecting the client brand.</p>
<h2 class="Mission-Vision-heading" >Recruitment Process Outsourcing:</h2>
<p class="Mission-Vision-subhead">R SQUARE HR understands the significance of Onboarding a set of people as per a recruitment plan and what it takes to make it happen. A number of activities have to happen in harmony with one another and continuous monitoring is required to make sure that the harmony is maintained, an aspect we emphasize on; being a pioneer among the staffing companies in India.</p>
<h2 class="Mission-Vision-heading-sub">Scope of Work</h2>
<p class="Mission-Vision-subhead">•	Understanding the organization, the structure, the context of each role holder and the requirements.</p>
<p class="Mission-Vision-subhead">•	Designing a plan for talent attraction, candidate assessment and candidate communication.</p>
<p class="Mission-Vision-subhead">•	Advertising the jobs on a variety of media.</p>
<p class="Mission-Vision-subhead">•	Sourcing candidates through multiple channels: interest check and validate suitability.</p>
<p class="Mission-Vision-subhead">•	Managing vendors and internal referrals, if any.</p>
<p class="Mission-Vision-subhead">•	Receiving and screening applications sourced through all the channels – R SQUARE, internal database, external sources and referrals.</p>
<p class="Mission-Vision-subhead">•	Capturing details of the applicants in a format that is trackable over a period of time.</p>
<p class="Mission-Vision-subhead">•	Coordinating interviews and any other steps in the selection process with shortlisted candidates.</p>
<p class="Mission-Vision-subhead">•	Working with client teams for the issuance of offer letters to successful candidates.</p>
<p class="Mission-Vision-subhead">•	Negotiating offers with the candidates.</p>
<p class="Mission-Vision-subhead">•	Engaging offered candidates till their successful joining.</p>
<h2 class="Mission-Vision-heading-sub">R SQUARE HR Advantages</h2>
<p class="Mission-Vision-subhead">•	Matches your compliance requirements fully without taking any short-cuts unlike some other staffing companies in India.</p>
<p class="Mission-Vision-subhead">•	A team of expert consultants who have the experience in designing and executing some of the largest RPO programs in India will make sure that the client has a good plan and executes it rigorously.</p>
<p class="Mission-Vision-subhead">•	R SQUARE HR Team follows standardization and process rigours to ensure that the outcomes are up to your satisfaction levels and that best practices are put in to ensure that.</p>
<p class="Mission-Vision-subhead">•	Our client’s brand gets strengthened by the experience that the R SQUARE HR Team delivers to the applicants.</p>
<h2 class="Mission-Vision-heading" >STAFFING SERVICE:</h2>
<h2 class="Mission-Vision-heading-sub">R SQUARE’s Flex Staffing Services Process:</h2>
<p class="Mission-Vision-subhead">R SQUARE HR provides Flex staff with varied skill-sets on its payroll across multiple locations. We call them our Deputies (Flex Staff). They work at our client organizations for a specified period of time across different functions and levels. We do end-to-end employee life cycle management with an option to be hired on to the client payroll, offering complete flexibility. Our technology enabled transparent work process brings in proven efficiency in deputee engagement and reduces employee Query Resolution Time massively.</p>
<p class="Mission-Vision-subhead">R SQUARE HR is sensitive to the fact that in addition to Flex Staffing, organizations also need value added services not just limited to employee engagement, performance management, talent pipeline management etc. In such cases, we bring in a dedicated team for them; they operate out of the customer’s office/ location and do everything for that client – for the line managers, HR team, finance team, legal team and all the temp staff of R SQUARE HR working there; they bring in higher efficiency in staffing and a significant increase in productivity.</p>
<h2 class="Mission-Vision-heading-sub">R SQUARE’s Flex Staffing Services Process:</h2>
<p class="Mission-Vision-subhead">•	Rigorous management of compliance and risks</p>
<p class="Mission-Vision-subhead">•	Domain experts carry out payroll and all compliance matters to ensure that there are no errors and all risks are managed appropriately.</p>
<p class="Mission-Vision-subhead">•	Our client focus ensures that we align with the specific requirements of each client around their internal processes.</p>
<p class="Mission-Vision-subhead">•	Enhanced Employee Productivity</p>
<p class="Mission-Vision-subhead">•	Right selection of candidates using our expertise and unique matching algorithm.</p>
<p class="Mission-Vision-subhead">•	Transparent work processes using technology that leads to a huge reduction in queries from employees and line managers.</p>
<p class="Mission-Vision-subhead">•	Quick resolution of queries, if any, and thus, preventing the wastage of your precious time.</p>
<h2 class="Mission-Vision-heading" >STAFFING SERVICE:</h2>
<p class="Mission-Vision-subhead">National Apprenticeship Promotion Scheme (NAPS) is a scheme launched by the Government of India on 19th August 2016 to provide financial support to establishments undertaking the Apprenticeship training.</p>
<p class="Mission-Vision-subhead">Apprenticeship training is a course of training in an industry or establishment, under a contract of Apprenticeship which consists of:</p>
<p class="Mission-Vision-subhead">1. Provides an ‘learn while earn’ opportunity to the apprentice</p>
<p class="Mission-Vision-subhead">2. Tackle skill shortages and develop a talent pipeline for future needs</p>
<p class="Mission-Vision-subhead">All establishments having work force (regular and contract employees) of 30 or more are mandated to undertake Apprenticeship Programs in a range from 2.5% -15% of its workforce (including direct contractual employees) every year.</p>
<h2 class="Mission-Vision-heading-sub">Basic Training:</h2>
<p class="Mission-Vision-subhead">Basic training consists of theoretical and practical / lab instruction segment of every Apprenticeship Program related to a particular trade post for which on-the-Job-Training is imparted to the Apprentice. Basic training is an essential component of Apprenticeship training for those who have not undergone any institutional training / skill training before taking up On-the-Job-Training / practical training. Basic Training is imparted to fresher apprentices for acquiring a reasonable ability to handle Instruments / Machineries / Equipment independently prior to being moved to Shop Floor / Work Area for practical / On-Job training.</p>
<h2 class="Mission-Vision-heading-sub">On-the-Job-Training (OJT):</h2>
<p class="Mission-Vision-subhead">OJT is practical training imparted at the workplace premises of an establishment.</p>
<h2 class="Mission-Vision-heading-sub">Trades / Courses establishments under the Apprenticeship act:</h2>
<p class="Mission-Vision-subhead">There are two categories of Trades defined under the Apprenticeship act 1961.</p>
<p class="Mission-Vision-subhead">Designated Trades: Those notified by the Government are referred to as “Designated Trades”</p>
<p class="Mission-Vision-subhead">Optional Trades: The other trades which are not included in the notified list of the Designated Trades but opted as a Trade / Course to be run under the Apprentices Act by an establishment are referred to as “Optional Trades”.</p>
<h2 class="Mission-Vision-heading-sub">Categories covered under the program:</h2>
<p class="Mission-Vision-subhead">•	ITI Pass Outs</p>
<p class="Mission-Vision-subhead">•	All Pass Outs from the NSQF aligned courses including PMKVY / DDUGKY etc.</p>
<p class="Mission-Vision-subhead">•	Dual-Learning Mode from it is</p>
<p class="Mission-Vision-subhead">•	Fresh apprentices</p>
<h2 class="Mission-Vision-heading" >HR Advisory Services:</h2>
<p class="Mission-Vision-subhead">RSHRS provides comprehensive human capital management solutions, offering expertise in organisational structure design, strategic workforce planning, HR policy optimization, and the creation of competitive compensation and rewards strategies.</p>
<p class="Mission-Vision-subhead">In parallel, our business growth and transformation services cover sales strategy and enablement, digital transformation, supply chain management, and project management. Our strategies are tailor-made for customers to align with the culture, vision and mission unique to them. Each solution is customised to align with the client’s unique culture, vision, and mission, driving sustainable growth and long-term organisational success.</p>
<h2 class="Mission-Vision-heading" >Payroll and Compliance Outsourcing</h2>
<p class="Mission-Vision-subhead">RSHRS offers a comprehensive payroll management solution that integrates technology and streamlines processes. Our services cover leaves and attendance management, verification of employee investments and claims, payroll calculation, and the generation of payslips and statutory reports. Additionally, employees can access relevant employment details through a self-service portal, simplifying HR administration for businesses. We also provide labour law compliance services, including remitting statutory dues, filing returns, maintaining statutory registers, and liaising with government offices to address complex queries and disputes.</p>
<h2 class="Mission-Vision-heading" >Employment Background Verification:</h2>
<p class="Mission-Vision-subhead">Employment check is a process to validate an applicant's previous employment details including length of employment, position, and rehire eligibility. Frequently, information supplied by job applicants is either false or inflated. Having false information, one can make an incorrect decision for the applicant's position or salary.</p>
<h2 class="Mission-Vision-heading-sub">Education check:</h2>
<p class="Mission-Vision-subhead">Education check is an easy and cost effective way to determine the validity and accuracy of an applicant's educational history. Education check includes the degree received, dates of attendance, graduation date, and any special recognition or awards. Frequently, information supplied by the applicant is either false or inflated. Having false information, one can make an incorrect decision for the business.</p>
<h2 class="Mission-Vision-heading-sub">Reference check:</h2>
<p class="Mission-Vision-subhead">Reference check for a sensitive or confidential position, often a reference check is the best way to determine what an applicant is really like. If the job you are seeking to fill is sensitive or confidential in nature, often a personal reference check helps to ensure the employment references are valid in today's high-risk competitive marketplace. For middle and upper level management, this search is vital in determining the true character of an applicant.</p>
<p class="Mission-Vision-subhead">More and more companies are now checking job references, along with other background checks.</p>
<h2 class="Mission-Vision-heading-sub">Credential check:</h2>
<p class="Mission-Vision-subhead">Is your business highly dependent on the credentials of the individual? If so, are all the certificates and credentials accurate? Whether your business is involved in technological services to a Multi-National or public services like hospitals or civil contracts, the credentials of the individual and current competency are what make your relationship strong with your customers. Hiring a new resource is a major investment in both your business and the individual's future.</p>
<p class="Mission-Vision-subhead">State-of-the-art technology system helps our researchers to verify relevant education, training, professional state licensure and certification credentials. We expedite the process in gathering, formatting and reporting the information supplied by the applicant and information we have gathered by concerned agencies or institutions.</p>
<h2 class="Mission-Vision-heading-sub">Address Check:</h2>
<p class="Mission-Vision-subhead">Find out why the address check is very important during your business process. Problems arising from incorrect or missing address information are usually not obvious at first. You might encounter a surprise when the address does not exist or addressee never lived there or when your correspondence about the loan installment payment notice is undeliverable and returned.</p>
<h2 class="Mission-Vision-heading-sub">Identity Check:</h2>
<p class="Mission-Vision-subhead">Identity check is a process to find out if they are who they say they are. Negligent hiring may lead to a lawsuit or corporate culture breach. The most common scam, called "identity theft", occurs when the profile applies for a house loan, credit card or bank account with another person's name and address. The false identity misuses the resources under the true identity's name. A bad decision can wreak havoc on a company's budget and reputation as well as ruin the career of the hiring official.</p>
<h2 class="Mission-Vision-heading-sub">Criminal Check:</h2>
<p class="Mission-Vision-subhead">Criminal check is a process to reveal any past convictions or criminal activities by the applicant. A search of government records is conducted at the appropriate level. Jurisdictions searched are determined by the applicant's address, nationality, government intelligence, or as designated by the client. These searches include those associated with the subject's current and previous addresses. Reported information includes the location, case number, date filed, charges, conviction date, and disposition.</p>
<p class="Mission-Vision-subhead">Criminal checks help exclude people with criminal records who would be a crime risk. Organizations must conduct a criminal check to ensure other employees are safe and feel safe at their work environment. Our cutting edge technology system helps our researchers to expedite the process of gathering, analyzing, formatting and reporting the information supplied by the candidate and the information verified by the government authorities.</p>
<h2 class="Mission-Vision-heading-sub">Drug Check:</h2>
<p class="Mission-Vision-subhead">Drug check – 5 Panel: Indicates the presence of illegal or prescription drugs from laboratory results. We partner with an independent company to provide complete drug screening services. The 5-Panel drug screen includes testing for amphetamines, cocaine, marijuana, opiates, and phencyclidine (PCP).</p>
<p class="Mission-Vision-subhead">Drug check – 10 Panel: Indicates the presence of illegal or prescription drugs from laboratory results. We partner with an independent company to provide complete drug screening services. The 10-Panel drug screen includes testing for amphetamines, barbiturates, benzodiazepines, cocaine, marijuana, methadone, methaqualone, opiates, phencyclidine (PCP), and propoxyphene.</p>
<h2 class="Mission-Vision-heading" >Business Improvement Processes:</h2>
<p class="Mission-Vision-subhead">As the Business world tries to steer the present challenges, many of the management practices will undergo changes in the days ahead. The biggest challenge today is to achieve the strategic intent of the business, provide organizational scalability & sustained growth through optimized resources-manpower and material. We need to make our work force more engaged, enjoy what they do and draw motivation from their achievement. Performance parameters need to more align to key outcome.</p>
<p class="Mission-Vision-subhead">Our output needs to meet higher level quality. We should set objective without draining our self or burning out our talent pool. We need to understand that it’s not quantum but quality of our focus on the on the key areas that will give us the desired results. Quantum can in the short run give us a level of output though higher than the previous level and in line with our business target but not near the possible potential and perhaps on target but with a cost. Thus, many a times the approach is not sustainable.</p>
<h2 class="Mission-Vision-heading" >QUALITY POLICY</h2>
<p class="Mission-Vision-subhead">We at R SQUARE HR SERVICES are committed to partner with our customers in providing reliable recruitment and staffing services and enhancing customer satisfaction of all stakeholders (clients, candidates, deputees, partners, members) in the business through continual improvement in our Quality Management System.</p>
<h2 class="Mission-Vision-heading" >LEARNING & DEVELOPMENT:</h2>
<p class="Mission-Vision-subhead">Someone has beautifully said: What happens if we train our people and they leave? ….Wrong question! According to Zig Ziglar, you should be asking what happens if we don’t train our people, and they stay?</p>
<p class="Mission-Vision-subhead">R SQUARE HR employs a suite of interventions to enhance & develop general and professional skills of all our client’s employees which aim for betterment of basic knowledge and competency skills. All programs are delivered by well-qualified and renowned training professionals having rich experience in organizational learning and development. We offer various business improvement packages which are based on diagnostics studies of client model and business environment. Our learning and development processes are exhaustive in nature which takes care of all skill gaps, future learning and implementing change management at our client side.</p>
<p class="Mission-Vision-subhead">The entire L & D delivery model performed at 5 levels across the organizational hierarchy with specific modes at each level.</p>
<p class="Mission-Vision-subhead">Education and Learning Process covers a set of critical methodologies like Self Reflection, Action Therapy, Dramatics, and Case Studies & Role Plays to increase the retention level at the trainees end.</p>
<p class="Mission-Vision-subhead">R SQUARE HR undertakes a range of modules in learning & development across levels, out of those, few of the key modules are:</p>
<h2 class="Mission-Vision-heading-sub">Techno Management Modules:</h2>
<p class="Mission-Vision-subhead">•	Quality & Process Compliance</p>
<p class="Mission-Vision-subhead">•	Preventative Maintenance</p>
<p class="Mission-Vision-subhead">•	Cost Effectiveness</p>
<p class="Mission-Vision-subhead">•	Supply Chain & Logistics / Inventory Management</p>
<p class="Mission-Vision-subhead">•	Production Planning & Control (PPC)</p>
<p class="Mission-Vision-subhead">•	Six Sigma Applications</p>
<p class="Mission-Vision-subhead">•	Business Excellence</p>
<h2 class="Mission-Vision-heading-sub">Executive Coaching Modules:</h2>
<p class="Mission-Vision-subhead">•	Leadership Development</p>
<p class="Mission-Vision-subhead">•	Train the Trainer (TTT)</p>
<p class="Mission-Vision-subhead">•	Change Management</p>
<p class="Mission-Vision-subhead">•	Project Management</p>
<p class="Mission-Vision-subhead">•	Managerial Effectiveness Programme</p>
<p class="Mission-Vision-subhead">•	Coaching & Mentoring</p>
<p class="Mission-Vision-subhead">•	Vision & Mission Exercise for Senior Management Group</p>
<p class="Mission-Vision-subhead">•	Thinking Strategy</p>
<h2 class="Mission-Vision-heading-sub">Soft Skills / Behavioural Modules:</h2>
<p class="Mission-Vision-subhead">•	Magic of Teams</p>
<p class="Mission-Vision-subhead">•	Creating Right Attitude</p>
<p class="Mission-Vision-subhead">•	Motivation</p>
<p class="Mission-Vision-subhead">•	Managing Stress</p>
<p class="Mission-Vision-subhead">•	Problem Solving & Decision Making Skills</p>
<p class="Mission-Vision-subhead">•	Creativity and Out of Box Thinking</p>
<p class="Mission-Vision-subhead">•	Effective Time Management</p>
<p class="Mission-Vision-subhead">•	Communications</p>
<p class="Mission-Vision-subhead">•	Interpersonal Skills</p>
<p class="Mission-Vision-subhead">•	Presentation Skills</p>
<p class="Mission-Vision-subhead">•	CRM</p>
<p class="Mission-Vision-subhead">•	Business Etiquettes</p>
<p class="Mission-Vision-subhead">•	Transactional Analysis</p>
<p class="Mission-Vision-subhead">•	Self-Awareness</p>
<h2 class="Mission-Vision-heading" >MOU with Educational Institutes and other details:</h2>
<p class="Mission-Vision-subhead">We have signed MoU’s with renowned institutions, colleges and Universities for various educational and placement programs. The list of institutes with whom we are working in various capacities are as follows:</p>
<p class="Mission-Vision-subhead">•	IIM Ahmedabad</p>
<p class="Mission-Vision-subhead">•	IIM Udaipur</p>
<p class="Mission-Vision-subhead">•	Silver Oak University, Ahmedabad (all institutes working under the University)</p>
<p class="Mission-Vision-subhead">•	Sardarkrushinagar Dantiwada Agricultural University, Dantivada, Gujarat.</p>
<p class="Mission-Vision-subhead">•	Society For Creation of Opportunities Through Proficiency In English – SCOPE</p>
<p class="Mission-Vision-subhead">•	Knowledge Consortium of Gujarat</p>
<p class="Mission-Vision-subhead">•	Hk School of Foreign Languages & Hkumars Education Institute, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	All the Government Colleges & Schools of Gujarat</p>
<p class="Mission-Vision-subhead">•	Gujarat Disaster Management Institute, Gandhinagar.</p>
<p class="Mission-Vision-subhead">•	The M S University of Baroda, Vadodara</p>
<p class="Mission-Vision-subhead">•	Nirma University, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Gujarat University, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	GLS University, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Ahmedabad Management Association, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Saradar Patel Institute of Public Administration, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Computer Society of India, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Sardar Patel Institute of Economic & Social Research, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Prakshal IT Academy, Ahmedabad</p>
<p class="Mission-Vision-subhead">•	Sardar Patel College of Pharmacy</p>
<p class="Mission-Vision-subhead">•	Sardar Patel College of Engineering</p>
<p class="Mission-Vision-subhead">•	Sardar Patel College of Administration & Management</p>
<p class="Mission-Vision-subhead">•	Sardar Patel College of Education</p>
<p class="Mission-Vision-subhead">•	Sardar Patel College of Applied Science</p>
<p class="Mission-Vision-subhead">•	Sardar Patel College of Commerce</p>
<p class="Mission-Vision-subhead">•	Balaji College of Paramedical</p>
<p class="Mission-Vision-subhead">•	Balaji College of Law</p>
<p class="Mission-Vision-subhead">•	Balaji College of Physiotherapy</p>
<p class="Mission-Vision-subhead">•	Balaji College of Nursing - Approved by Guj.Nursing Council</p>
<p class="Mission-Vision-subhead">•	Tirupati School of Nursing - Approved by Guj.Nursing Council</p>
<p class="Mission-Vision-subhead">•	Tirupati Industrial Training Institute, Vadodara approved by the Government of Gujarat</p>
<p class="Mission-Vision-subhead">•	My Shanen School (GSEB) , Vadodara</p>
<p class="Mission-Vision-subhead">•	Sharda Mandir English Medium School (GSEB) , Vadodara</p>
<p class="Mission-Vision-subhead">•	Gujarat Refinery School (GSEB) , Vadodara</p>
<p class="Mission-Vision-subhead">•	Atmiya Vidyalay, Vadodara</p>
<p class="Mission-Vision-subhead">•	Shanen School (CBSE) , Vadodara</p>
<p class="Mission-Vision-subhead">•	My Shanen Pre-School, Vadodara</p>
<p class="Mission-Vision-subhead">•	Sarvamangal School, Vadodara</p>
<h2 class="Mission-Vision-heading" >SOME OF THE CLIENT’S AND SERVICES:</h2>
<p class="Mission-Vision-subhead">R SQUARE has handled a number of clients by providing various HR Services like preparation of HR Policy manual, recruitment, staffing, training, payroll, overall monitoring and supervision of the staff deployed etc.</p>
<p class="Mission-Vision-subhead">Mr. S N Rao has prepared HR Policy Manuals for</p>
<p class="Mission-Vision-subhead">•	IIM Ahmedabad,</p>
<p class="Mission-Vision-subhead">•	IIM Udaipur,</p>
<p class="Mission-Vision-subhead">•	India International Boolean Exchange (IFSC) Ltd. and</p>
<p class="Mission-Vision-subhead">•	Sahaj Solar Limited.</p>
<p class="Mission-Vision-subhead">Mr. S N Rao has also provided training, recruitment & staffing services to</p>
<p class="Mission-Vision-subhead">•	IIM Ahmedabad,</p>
<p class="Mission-Vision-subhead">•	Tourism Corporation of Gujarat Limited,</p>
<p class="Mission-Vision-subhead">•	Statue of Unity Area Development and Tourism Governance Authority,</p>
<p class="Mission-Vision-subhead">•	Gujarat Council of Science City,</p>
<p class="Mission-Vision-subhead">•	Forest Department of Gujarat,</p>
<p class="Mission-Vision-subhead">•	Children Nutrition Park,</p>
<p class="Mission-Vision-subhead">•	Human Development and Research Foundation,</p>
<p class="Mission-Vision-subhead">•	Voltbek Home Appliances Ltd.,</p>
<p class="Mission-Vision-subhead">•	Tenneco Automotive (India) Pvt. Ltd. etc.</p>
<p class="Mission-Vision-subhead">R Square HR Services has trained and provided 132 Tourist Guides to the Tourism Corporation of Gujarat Limited (TCGL) for the Statue of Unity and other 7 district locations of Gujarat. These Tourist Guides are performing their duty since July-2019 to till date. The Tourist Guides coming from the tribal background and trained by us performed so well that our Honorable Prime Minister Shri Narendra Modi ji praised their performance in “Man ki baat” and other public programs. Recently, we provided Aquatic Educators/ Guides to the Gujarat Council of Science City, Ahmedabad.</p>
<h2 class="Mission-Vision-heading-sub">We have organized and conducted training programs on</h2>
<p class="Mission-Vision-subhead">•	Tourist Guides,</p>
<p class="Mission-Vision-subhead">•	Housekeeping,</p>
<p class="Mission-Vision-subhead">•	Security,</p>
<p class="Mission-Vision-subhead">•	Spoken English,</p>
<p class="Mission-Vision-subhead">•	International Languages</p>
<p class="Mission-Vision-subhead">•	MIS</p>
<p class="Mission-Vision-subhead">•	MS-Office</p>
<p class="Mission-Vision-subhead">•	HR Management</p>
<p class="Mission-Vision-subhead">•	Communication Skils</p>
<p class="Mission-Vision-subhead">•	Pradhan Mantri Kaushal Vikas Yojna (PMKVY),</p>
<p class="Mission-Vision-subhead">•	Pradhan Mantri Dakshta Aur Kushalta Sampann Hitgrahi (PM-DAKSH) Yojana,</p>
<p class="Mission-Vision-subhead">•	Deen Dayal Upadhyaya Gramin Kaushalya Yojana (DDU-GKY),</p>
<p class="Mission-Vision-subhead">•	Deen Dayal Upadhyaya Gramin Kaushalya Yojana (DDU-GKY),</p>
<p class="Mission-Vision-subhead">•	Electronics System Design and Manufacturing (ESDM) Training Programs etc.</p>

<div class="services-grid">
    <div class="services-grid">
      <!-- Each card uses local images with leading slash -->
      <div class="service-card"><div class="service-icon"><i class="fas fa-bullhorn"></i><img class="icon-part" src="/images/strategy.png" alt=""></div><h4>Consultancy for HR Strategies, Policies & Services</h4><p>Consultancy for HR strategies, policies & services.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-chalkboard-user"></i><img class="icon-part" src="/images/training.png" alt=""></div><h4>Training & Development</h4><p>Upskill teams with expert modules (soft skills, leadership, techno‑management).</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/cv.png" alt=""></div><h4>Search & Recruitment Services</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/hr-outsourcing.png" alt=""></div><h4>Recruitment Process Outsourcing</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-people-arrows"></i><img class="icon-part" src="/images/job.png" alt=""></div><h4>Contract Staffing(Workforce Service on Outsourcing basis)</h4><p>Workforce on outsourcing, flexi‑staffing with full compliance.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/outsourcing.png" alt=""></div><h4>Functional HR Services on Outsourcing basis</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-calculator"></i><img class="icon-part" src="/images/insight.png" alt=""></div><h4>Processing Payroll and maintaining legal compliance</h4><p>Payroll processing, legal compliance, NAPS, apprenticeship management.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/OIP%20(16).webp" alt=""></div><h4>National Apprenticeship Promotion Scheme (NAPS)</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/legal-system.png" alt=""></div><h4>Legal Compliance</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-gavel"></i><img class="icon-part" src="/images/compliant.png" alt=""></div><h4>Legal Services under RTI</h4><p>Legal compliance, RTI advisory, labour law liaison.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-umbrella-beach"></i><img class="icon-part" src="/images/web.png" alt=""></div><h4>Tourism Specific Solutions</h4><p>Tourist guide training, cultural programs, destination management.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-hand-holding-heart"></i><img class="icon-part" src="/images/collective.png" alt=""></div><h4>Supporting CSR Activity</h4><p>Support CSR activities, collaborate with institutions & universities.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/9887500.png" alt=""></div><h4>Collaborating with various institutions</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-magnifying-glass"></i><img class="icon-part" src="/images/cultural-activities-concept-icon-leisure-pastime-entertainment-idea-thin-line-illustration-visiting-cinema-city-sightseeing-tour-isolated-outline-drawing-editable-stroke-vector.jpg" alt=""></div><h4>Cultural Activities(musical programs, films, Gujarati serials, drama etc.)</h4><p>Executive search, mass hiring, RPO – permanent & contract staffing.</p></div>
      <div class="service-card"><div class="service-icon"><i class="fas fa-laptop-code"></i><img class="icon-part" src="/images/consultant.png" alt=""></div><h4>IT Consultancy</h4><p>Tech solutions, e‑governance, digital transformation support.</p></div>
    </div>
  </section>

  <!-- About section -->
  <section id="about" class="about">
    <h2 class="section-heading">About R Square HR Services</h2>
    <div class="about-grid">
      <div class="about-text">
        <p><strong>R SQUARE HR Services (R SQUARE)</strong> is an MSME company founded by <strong>Shri S N Rao</strong>, Former Head-HR of IIM Ahmedabad and Former Chief General Manager, The Gujarat State Civil Supplies Corporation Limited (a PSU of Government of Gujarat). During his tenure, his team was awarded the CSI-Nihilent Award for e‑Governance (2011‑12), National Award for e‑Governance (2012‑13), and the CSI-Nihilent Award for Supply‑Logistics using ICT from the Government of India.</p>
        <p><strong>Mission & Vision:</strong> To emerge as one of the most respected HR services companies, anchored on growth, professionalism, dignity and diversity. We promote learning with humility, serving with dignity and growing with integrity. We combine the power of technology with the capability of our members to deliver value through rigorous execution.</p>
        <p>We have prepared HR Policy Manuals for IIM Ahmedabad, IIM Udaipur, India International Boolean Exchange (IFSC) Ltd., Sahaj Solar Limited, and provided training, recruitment & staffing to Tourism Corporation of Gujarat, Statue of Unity, Forest Department, Gujarat Council of Science City, Voltbek, Tenneco and many more.</p>
      </div>
      <div class="founder-box">
        <i class="fas fa-user-tie" style="font-size: 48px; color:#3b71fe; margin-bottom:16px;"></i>
        <div class="founder-name">Shri S N Rao</div>
        <div class="founder-title">Founder & Mentor</div>
        <p style="color:#475569;">Former Head-HR, IIM Ahmedabad<br>Former CGM, Gujarat State Civil Supplies Corp.<br>Over 4 decades of experience in HR, governance & training.</p>
      </div>
    </div>
  </section>

  <!-- Stats row -->
  <div class="stats-row">
    <div class="stat-item"><div class="stat-number">132+</div><div class="stat-label">Tourist Guides trained for Statue of Unity (praised by PM)</div></div>
    <div class="stat-item"><div class="stat-number">50+</div><div class="stat-label">Institutional partners (IIMs, Universities, Govt. bodies)</div></div>
    <div class="stat-item"><div class="stat-number">3</div><div class="stat-label">National e‑Governance Awards</div></div>
    <div class="stat-item"><div class="stat-number">15+</div><div class="stat-label">Years of leadership in HR</div></div>
  </div>

  <!-- Carousel -->
  <section class="carousel-section">
    <h2 class="section-heading">Success stories in action</h2>
    <p class="section-subhead">Real impact – from tourist guides to large‑scale training programs.</p>
    <div class="carousel-container" id="carouselContainer">
      <div class="carousel-inner" id="carouselInner">
        <div class="carousel-slide" style="background-image: url('https://images.unsplash.com/photo-1581091226033-d5c48150dbaa?ixlib=rb-4.0.3&auto=format&fit=crop&w=2070&q=80');">
          <div class="slide-content">
            <span class="slide-tag">Tourism & skill development</span>
            <h3>132 Tourist Guides trained for Statue of Unity</h3>
            <p>R Square HR trained tribal youth as guides. Their performance was praised by Hon. Prime Minister Shri Narendra Modi in "Man ki baat".</p>
          </div>
        </div>
        <div class="carousel-slide" style="background-image: url('https://images.unsplash.com/photo-1541339907198-e08756dedf3f?ixlib=rb-4.0.3&auto=format&fit=crop&w=2070&q=80');">
          <div class="slide-content">
            <span class="slide-tag">HR consulting</span>
            <h3>HR Policy Manuals for IIM Ahmedabad & IIM Udaipur</h3>
            <p>Trusted by premier institutes to design comprehensive HR frameworks.</p>
          </div>
        </div>
        <div class="carousel-slide" style="background-image: url('https://images.unsplash.com/photo-1524178232363-1fb2b075b655?ixlib=rb-4.0.3&auto=format&fit=crop&w=2070&q=80');">
          <div class="slide-content">
            <span class="slide-tag">Large‑scale training</span>
            <h3>PMKVY, DDU-GKY, DAY-NULM & ESDM programs</h3>
            <p>Delivered government‑sponsored skill development across Gujarat.</p>
          </div>
        </div>
        <div class="carousel-slide" style="background-image: url('https://images.unsplash.com/photo-1577563908411-5077b6dc7624?ixlib=rb-4.0.3&auto=format&fit=crop&w=2070&q=80');">
          <div class="slide-content">
            <span class="slide-tag">Innovative staffing</span>
            <h3>Aquatic Educators for Gujarat Council of Science City</h3>
            <p>Specialised guides for the science city, enhancing visitor experience.</p>
          </div>
        </div>
      </div>
      <button class="carousel-control prev" id="prevBtn"><img class="next-icon" src="/images/prev.png" alt="Previous"><i class="fas fa-chevron-left"></i></button>
      <button class="carousel-control next" id="nextBtn"><img class="next-icon" src="/images/right.png" alt="Next"><i class="fas fa-chevron-right"></i></button>
      <div class="carousel-indicators" id="indicators">
        <span class="indicator active" data-index="0"></span>
        <span class="indicator" data-index="1"></span>
        <span class="indicator" data-index="2"></span>
        <span class="indicator" data-index="3"></span>
      </div>
    </div>
  </section>

  <!-- Partners -->
  <section class="partners">
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

  <!-- Contact -->
  <section id="contact" class="contact">
    <h2 class="section-heading">Get in touch</h2>
    <div class="contact-container">
      <div class="contact-info">
        <h3>R Square HR Services</h3>
        <div class="contact-detail"><img class="contact-icon1" src="/images/placeholder.png" alt=""><i class="fas fa-map-marker-alt"></i><span>421, 4th Floor, Samaan Complex, Opp. Satyam Mall, Near Jodhpur Cross Roads, Satellite, Ahmedabad‑380015.</span></div>
        <div class="contact-detail"><img class="contact-icon1" src="/images/telephone.png" alt=""><i class="fas fa-phone-alt"></i><span>+91 99784 06366 , +91 99246 67855 <br> Phone: +91 79 4009 3259</span></div>
        <div class="contact-detail"><img class="contact-icon1" src="/images/gmail.png" alt=""><i class="far fa-envelope"></i><span>riddhish@rsquarehr.com, snrao@rsquarehr.com</span></div>
        <div style="margin-top:32px;"><button class="btn-primary">Send enquiry</button></div>
      </div>
      <div class="map-container">
        <iframe src="https://maps.google.com/maps?q=421%204th%20Floor%20Samaan%20Complex%20Opp%20Satyam%20Mall%20Jodhpur%20Cross%20Roads%20Satellite%20Ahmedabad%20380015&z=16&output=embed" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
      </div>
    </div>
  </section>
</div>

<!-- Footer -->
<footer>
  <div class="footer">
    <div class="footer-col">
      <div style="display: flex; align-items: center; gap: 8px;">
        <img class="logo-img" src="/images/LOGO_PDF_invertedColor_page-0001.jpg" style="width:100px; max-width:100%;" alt="R Square HR">
        <span style="font-size:24px; font-weight:700; color:white;">R Square <span style="color:#3b71fe;">HR</span></span>
      </div>
      <p>MSME company anchored on growth, professionalism, dignity & diversity.</p>
    </div>
    <div class="footer-links">
      <div><h5>Services</h5><a href="#services">Recruitment</a><a href="#services">Training</a><a href="#services">Payroll</a><a href="#services">NAPS</a></div>
      <div><h5>About</h5><a href="#mission">Mission</a><a href="#about">Founder</a><a href="#about">Awards</a></div>
      <div><h5>Connect</h5><a href="#contact">Contact</a><a href="#">LinkedIn</a><a href="#">Email</a></div>
    </div>
  </div>
  <div class="copyright">© 2025 R Square HR Services. All rights reserved. Fully responsive · professional HR homepage</div>
</footer>

<!-- Back to top button -->
<button id="backToTop" aria-label="Back to top"><i class="fas fa-arrow-up"></i><img class="up" src="/images/arrow.png" alt=""></button>

<style>
  /* ----- RESET & BASE ----- */
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  body {
    font-family: 'Inter', sans-serif;
    background-color: #ffffff;
    color: #1e293b;
    line-height: 1.5;
    scroll-behavior: smooth;
    padding-top: 90px; /* space for fixed header */
    width: 100%;
    overflow-x: hidden;
  }

  /* ----- FIXED HEADER (fully responsive) ----- */
  .header-fixed {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    background: #2C1B3D;
    box-shadow: 0 4px 20px rgba(0,0,0,0.05);
    z-index: 1000;
  }

  .header-container {
    max-width: 1440px;
    margin: 0 auto;
    padding: 0 32px;
    width: 100%;
  }

  .navbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 15px 0;
    flex-wrap: wrap;
    gap: 20px;
  }

  .logo-container {
    display: flex;
    align-items: center;
  }

  .logo-img {
    width: 50px;
    height: auto;
    object-fit: contain;
    max-width: 100%;
  }

  .nav-links {
    display: flex;
    gap: 40px;
    font-weight: 500;
    flex-wrap: wrap;
  }

  .nav-links a {
    text-decoration: none;
    color: white;
    transition: color 0.2s;
    font-size: 1rem;
  }

  .nav-links a:hover {
    color: #3b71fe;
  }

  /* header-btns removed as requested */

  /* ----- MAIN CONTENT CONTAINER (fluid) ----- */
  .site-container {
    max-width: 1440px;
    margin: 0 auto;
    padding: 0 32px;
    width: 100%;
  }

  /* ----- HERO SECTION (flexible) ----- */
  .hero {
    display: flex;
    align-items: center;
    gap: 40px;
    padding: 40px 0 60px;
    flex-wrap: wrap;
  }

  .hero-content {
    flex: 1 1 45%;
    min-width: 300px;
  }

  .hero-content h1 {
    font-size: clamp(32px, 5vw, 52px);
    font-weight: 700;
    line-height: 1.2;
    letter-spacing: -1.5px;
    color: #0a2540;
    margin-bottom: 24px;
    margin-top: 121px;
  }

  .hero-content h1 span {
    color: #3b71fe;
    border-bottom: 4px solid #d4e2ff;
  }

  .hero-desc {
    text-align: justify;
    font-size: 18px;
    color: #475569;
     /* max-width: 500px;  */
    margin-bottom: 32px;
    max-width: 100%;
  }

  .hero-buttons {
    display: flex;
    gap: 16px;
    flex-wrap: wrap;
  }

  .btn-large {
    padding: 14px 36px;
    font-size: 16px;
    border-radius: 60px;
  }

  .btn-secondary {
    background: white;
    border: 1px solid #cbd5e1;
    color: #1e293b;
  }

  .btn-secondary:hover {
    background: #f8fafc;
  }

  .btn-primary {
    background: #0a2540;
    color: white;
    border: none;
    padding: 10px 24px;
    border-radius: 40px;
    font-weight: 600;
    font-size: 14px;
    cursor: pointer;
    transition: 0.2s;
    display: inline-flex;
    align-items: center;
    gap: 6px;
  }

  .btn-primary:hover {
    background: #1e3a5f;
    transform: translateY(-2px);
    box-shadow: 0 10px 20px -10px #0a2540;
  }

  .hero-image {
    flex: 1 1 45%;
    border-radius: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 280px;
  }

  .hero-image i {
    font-size: clamp(120px, 12vw, 160px);
    color: #3b71fe;
    opacity: 0.8;
  }

  /* ----- SECTION HEADINGS ----- */
  .section-heading {
    font-size: clamp(28px, 4vw, 36px);
    font-weight: 700;
    color: #0a2540;
    margin-bottom: 16px;
    letter-spacing: -0.5px;
  }

  .section-subhead {
    color: #64748b;
    font-size: 18px;
    max-width: 600px;
    margin-bottom: 40px;
  }

  /* ----- CAROUSEL (fully responsive) ----- */
  .carousel-section {
    margin: 60px 0 40px;
  }

  .carousel-container {
    position: relative;
    width: 100%;
    border-radius: 40px;
    overflow: hidden;
    box-shadow: 0 25px 50px -12px rgba(0,0,0,0.2);
  }

  .carousel-inner {
    display: flex;
    transition: transform 0.5s ease;
  }

  .carousel-slide {
    flex: 0 0 100%;
    min-height: 380px;
    padding: 48px 56px;
    background-size: cover;
    background-position: center;
    display: flex;
    align-items: center;
    gap: 40px;
    flex-wrap: wrap;
    position: relative;
  }

  .carousel-slide::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: linear-gradient(90deg, rgba(0,20,40,0.85) 0%, rgba(0,20,40,0.4) 100%);
    border-radius: 40px;
  }

  .slide-content {
    flex: 1;
    position: relative;
    z-index: 2;
    color: white;
    max-width: 70%;
  }

  .slide-content h3 {
    font-size: clamp(24px, 3vw, 32px);
    font-weight: 600;
    margin-bottom: 16px;
  }

  .slide-content p {
    font-size: 18px;
    max-width: 600px;
    opacity: 0.9;
  }

  .slide-tag {
    background: #3b71fe;
    color: white;
    border-radius: 40px;
    padding: 6px 18px;
    font-size: 14px;
    font-weight: 600;
    display: inline-block;
    margin-bottom: 20px;
    letter-spacing: 0.5px;
  }

  .carousel-control {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: white;
    border: none;
    width: 48px;
    height: 48px;
    border-radius: 60px;
    box-shadow: 0 10px 25px -5px rgba(0,0,0,0.2);
    font-size: 32px;
    cursor: pointer;
    color: #0a2540;
    transition: 0.2s;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10;
  }

  .carousel-control:hover {
    background: #3b71fe;
    color: white;
    transform: translateY(-50%) scale(1.05);
  }

  .prev { left: 24px; }
  .next { right: 24px; }

  .carousel-indicators {
    position: absolute;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 12px;
    z-index: 10;
  }

  .indicator {
    width: 12px;
    height: 12px;
    border-radius: 20px;
    background: rgba(255,255,255,0.5);
    cursor: pointer;
    transition: 0.2s;
  }

  .indicator.active {
    background: white;
    width: 30px;
  }

  /* ----- SERVICES GRID (responsive) ----- */
  .services {
    scroll-margin-top: 30px;
  }

  .services-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 32px;
    margin-top: 40px;
  }

  .service-card {
    background: white;
    border: 1px solid #eef2f6;
    border-radius: 32px;
    padding: 32px 28px;
    transition: all 0.3s cubic-bezier(0.25, 0.46, 0.45, 0.94);
    box-shadow: 0 5px 15px -5px rgba(0,0,0,0.02);
  }

  .service-card:hover {
    transform: translateY(-8px);
    border-color: #bdd3ff;
    box-shadow: 0 25px 35px -12px rgba(59,113,254,0.15);
  }

  .service-icon {
    background: #eef4ff;
    width: 64px;
    height: 64px;
    border-radius: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 24px;
  }

  .service-icon i {
    font-size: 32px;
    color: #3b71fe;
  }

  /* hide local images if present, we use font awesome */
  .service-icon img {
    display: block;
  }

  .service-card h4 {
    font-size: 24px;
    margin-bottom: 16px;
    color: #0a2540;
    font-weight: 600;
  }

  .service-card p {
    color: #475569;
  }

  /* ----- ABOUT (flexible) ----- */
  .about {
    scroll-margin-top: 30px;
  }

  .about-grid {
    display: flex;
    gap: 60px;
    align-items: flex-start;
    flex-wrap: wrap;
  }

  .about-text {
    flex: 2;
    min-width: 300px;
  }

  .about-text p {
    text-align: justify;
    margin-bottom: 20px;
    color: #334155;
    font-size: 1.05rem;
  }

  .founder-box {
    flex: 1;
    min-width: 280px;
    background: #f8fbff;
    border-radius: 32px;
    padding: 32px;
    border: 1px solid #e9f0fc;
    box-shadow: 0 15px 30px -12px rgba(0,0,0,0.05);
  }

  .founder-name {
    font-weight: 700;
    font-size: 24px;
    color: #0a2540;
  }

  .founder-title {
    color: #3b71fe;
    margin: 8px 0 16px;
    font-weight: 500;
  }

  /* ----- STATS (responsive) ----- */
  .stats-row {
    display: flex;
    justify-content: space-between;
    background: linear-gradient(145deg, #0a2540, #123456);
    border-radius: 48px;
    padding: 48px 32px;
    margin: 80px 0;
    flex-wrap: wrap;
    gap: 30px;
    color: white;
    box-shadow: 0 25px 40px -15px #0a2540;
  }

  .stat-item {
    text-align: center;
    flex: 1 1 180px;
  }

  .stat-number {
    font-size: clamp(32px, 4vw, 44px);
    font-weight: 700;
    margin-bottom: 8px;
  }

  .stat-label {
    font-size: 16px;
    letter-spacing: 0.5px;
    opacity: 0.9;
  }

  /* ----- PARTNERS (responsive grid) ----- */
  .partners {
    margin: 80px 0;
  }

  .partner-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(170px, 1fr));
    gap: 24px;
    background: #F8FBFF;
    border-radius: 48px;
    padding: 48px 32px;
    border: 1px solid #eef2f6;
  }

  .partner-item {
    text-align: center;
    font-weight: 600;
    color: #1e293b;
    background: white;
    padding: 16px 8px;
    border-radius: 60px;
    box-shadow: 0 4px 10px -5px rgba(0,0,0,0.05);
    transition: 0.2s;
    font-size: 0.95rem;
  }

  .partner-item:hover {
    background: #3b71fe;
    color: white;
    transform: scale(1.02);
  }

  /* ----- CONTACT (flexible) ----- */
  .contact {
    scroll-margin-top: 30px;
  }

  .contact-container {
    display: flex;
    gap: 60px;
    flex-wrap: wrap;
    background: #f9fcff;
    border-radius: 56px;
    padding: 56px 32px;
    border: 1px solid #e9f0fc;
  }

  .contact-info {
    flex: 1;
    min-width: 280px;
  }

  .contact-info h3 {
    font-size: 28px;
    color: #0a2540;
    margin-bottom: 24px;
  }

  .contact-detail {
    display: flex;
    align-items: center;
    gap: 16px;
    margin-bottom: 24px;
    flex-wrap: wrap;
  }

  /* .contact-detail i {
    width: 48px;
    height: 48px;
    background: white;
    border-radius: 60px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #3b71fe;
    font-size: 24px;
    box-shadow: 0 10px 20px -5px rgba(0,0,0,0.05);
    flex-shrink: 0;
  } */

  /* hide local contact images, use font awesome */
  .contact-detail img {
    display: block;
    width:35px;
    height:35px;
  }

  .map-placeholder {
    flex: 1;
    background: linear-gradient(145deg, #d9e2ef, #cbd5e1);
    border-radius: 40px;
    min-height: 280px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #1e293b;
    font-size: 18px;
    font-weight: 500;
    text-align: center;
    padding: 20px;
  }

  /* ----- FOOTER (full width, no fixed offsets) ----- */
  footer {
    background: #2C1B3D;
    width: 100%;
    margin-top: 60px;
  }

  .footer {
    max-width: 1440px;
    margin: 0 auto;
    padding: 48px 32px 32px;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 40px;
    background: #2C1B3D;
  }

  .footer-col p {
    color: #94a3b8;
    max-width: 280px;
    margin-top: 20px;
  }

  .footer-links {
    display: flex;
    gap: 80px;
    flex-wrap: wrap;
  }

  .footer-links div h5 {
    font-size: 1rem;
    margin-bottom: 20px;
    color: white;
  }

  .footer-links div a {
    display: block;
    color: #cbd5e1;
    text-decoration: none;
    margin-bottom: 12px;
    font-size: 15px;
    transition: color 0.2s;
  }

  .footer-links div a:hover {
    color: #3b71fe;
  }

  .copyright {
    max-width: 1440px;
    margin: 0 auto;
    text-align: center;
    padding: 24px 32px;
    color: #94a3b8;
    font-size: 14px;
    border-top: 1px solid #3d2b50;
    background: #2C1B3D;
  }

  /* ----- BACK TO TOP ----- */
  #backToTop {
    position: fixed;
    bottom: 40px;
    right: 40px;
    width: 56px;
    height: 56px;
    background: #0a2540;
    color: white;
    border: none;
    border-radius: 50%;
    font-size: 24px;
    cursor: pointer;
    display: none;
    align-items: center;
    justify-content: center;
    box-shadow: 0 10px 25px rgba(0,0,0,0.2);
    transition: all 0.3s ease;
    z-index: 999;
  }

  #backToTop:hover {
    background: #3b71fe;
    transform: translateY(-5px);
  }

  /* hide any leftover images that we replaced with FA */
  .icon-part, .up, .contact-icon1, .next-icon {
    display: block;
  }

  /* ----- ADDITIONAL MEDIA QUERIES (fine-tuning) ----- */
  @media (max-width: 1024px) {
    .slide-content { max-width: 100%; }
    .partner-grid { padding: 40px 24px; }
  }

  @media (max-width: 768px) {
    body { padding-top: 80px; }
    .navbar { flex-direction: column; align-items: flex-start; }
    .nav-links { gap: 20px; }
    .hero { flex-direction: column; }
    .stats-row { flex-direction: column; align-items: center; padding: 40px 24px; }
    .carousel-control { width: 36px; height: 36px; font-size: 24px; }
    .prev { left: 8px; }
    .next { right: 8px; }
    .site-container { padding: 0 20px; }
    .header-container { padding: 0 20px; }
    .contact-container { padding: 32px 20px; }
    .carousel-slide { padding: 40px 24px; }
    .slide-content h3 { font-size: 28px; }
    #backToTop { bottom: 20px; right: 20px; width: 48px; height: 48px; font-size: 20px; }
    .footer-links { gap: 40px; }
  }

  @media (max-width: 480px) {
    .hero-content h1 { font-size: 36px; }
    .btn-large { width: 100%; text-align: center; }
    .logo-img { width: 40px; }
    .hero-image i { font-size: 120px; }
    .partner-grid { padding: 24px 16px; }
    .footer { flex-direction: column; }
    .footer-links { gap: 30px; }
    .copyright { padding: 20px; }
  }
  
  /* Additional responsiveness for very small screens */
  @media (max-width: 360px) {
    .hero-img {
      left: 0 !important; /* override inline left if needed, but it's inline style? actually it's in a separate rule, we'll reset */
    }
    .hero-img {
      left: 0; /* reset the left positioning */
      max-width: 100%;
    }
    .carousel-slide {
      padding: 30px 16px;
    }
    .slide-content h3 {
      font-size: 22px;
    }
    .slide-content p {
      font-size: 16px;
    }
    .footer-links {
      gap: 20px;
    }
    .contact-detail {
      gap: 8px;
    }
    .contact-detail span {
      font-size: 14px;
    }
    .stat-label {
      font-size: 14px;
    }
  }

  /* ensure all images scale */
  img {
    max-width: 100%;
    height: auto;
  }
  .map-container{
    flex:1;
    width:100%;
    min-height:280px;
    border-radius:40px;
    overflow:hidden;
    box-shadow:0 10px 30px rgba(0,0,0,0.1);
  }

  .map-container iframe{
    width:100%;
    height:100%;
    border:0;
    min-height:280px;
  }

  /* tablet */
  @media (max-width:768px){
    .map-container{
      min-height:250px;
    }
  }

  /* mobile */
  @media (max-width:480px){
    .map-container{
      min-height:220px;
    }
  }
  .partner-icon{
    width: 60px;
    height: 40px;
    position: relative;
    left: 62px;
  }
  /* Mission-Vision */
.Mission-Vision{
  scroll-margin-top: 30px;
}
.Mission-Vision-subhead{
  color: #64748b;
  font-size: 18px;
  margin-bottom: 20px;
  text-align: justify;
}
.hero-img{
  position: relative;
  left: 6px;
}
.Mission-Vision-heading{
  color: #0A2540;
  font-size: 18px;
  margin-bottom: 20px;
  text-align: justify;
  font-size: clamp(28px, 4vw, 30px);
}
.Mission-Vision-heading-sub{
  color: #0A2540;
  font-size: 18px;
  margin-bottom: 20px;
  text-align: justify;
  font-size: clamp(28px, 4vw, 6px);
}
</style>
