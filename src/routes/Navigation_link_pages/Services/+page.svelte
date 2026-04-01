<script>
  import { onMount } from 'svelte';
  import { fly } from 'svelte/transition';

  let expanded = {};
  let showBackToTop = false;

  // Helper to strip HTML tags and truncate
  function stripHtmlAndTruncate(html, maxLength = 300) {
    const plain = html.replace(/<[^>]*>/g, '');
    if (plain.length <= maxLength) return plain;
    return plain.slice(0, maxLength).trim() + '...';
  }

  // All service sections with precomputed previews
  const sections = [
    {
      id: "hr-advisory",
      title: "HR Advisory Services",
      content: `<p>R Square HR Services (RSHRS) provides comprehensive human capital management solutions, offering expertise in organisational structure design, strategic workforce planning, HR policy optimization, and the creation of competitive compensation and rewards strategies.<br><br>While providing all these services we also write Manuals for the services and carry out hand holding process during implementation.<br><br>In parallel, our business growth and transformation services cover sales strategy and enablement, digital transformation, supply chain management, and project management. Our strategies are tailor-made for customers to align with the culture, vision and mission unique to them. Each solution is customised to align with the client’s unique culture, vision, and mission, driving sustainable growth and long-term organisational success.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "psychometric",
      title: "Psychometric Test Services",
      content: `<p><strong>Employability Skill Assessment:</strong><br>Skill assessment is important in nurturing the lowest segment of the pyramid of the organization. Our services for skill assessment help in talent identification and development.<br><br><strong>Managerial and Leadership Skills/Entrepreneurial Assessment:</strong><br>We conduct assessment of managerial and leadership. The assessments are used constructively to add value to an organization in the form of coaching and development.<br><br><strong>Competency Mapping:</strong><br>Developing of HR process and competency Mapping: We also expertise in developing and aligning HR systems and processes for organizations in the MSME sector.<br><br><strong>Performance Evaluation:</strong><br>Performance Evaluation assessment for the suitability on the current position and growth path.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "permanent-recruitment",
      title: "Permanent Recruitment Services",
      content: `<p>R SQUARE HR Services is basically an effort to create value in talent management domains and business consulting services. We specialize in talent acquisition, deployment & outsourcing, corporate learning & development to name a few. We also cater to the organization consulting space primarily servicing the industry through business process improvement, managerial outsourcing and other organizational development interventions. We are connected to companies in India helping them source best solutions to their intellectual & resourcing needs for both their onsite / offshore requirements.<br><br>Team R SQUARE HR is a pool of well qualified and experienced professionals in their respective areas, which enables them to serve key consulting needs across all corporate disciplines & promote quality service delivery through its people, affiliations & associates in India & Overseas.<br><br>The main and only concern for R SQUARE HR is Human Resources. Today the company has its remarkable presence in various industries like Tourism, Education, Information Technology, Pharmaceuticals, Engineering, etc.<br><br>Further accentuating our presence in the enterprise restructuring area we now deliver critical competitive advantage packages to small and medium enterprises for business process improvements and organizational excellence.<br><br>We understand your hiring philosophy and are perfectly placed to undertake all your recruitment and staffing needs. We understand the role context and advertise in online and offline channels to attract the best talent. We undertake recruitment outsourcing services, which equips us to understand clearly challenges faced by the employers.<br><br>Job seekers are increasingly relying on smartphones and mobile devices to conduct job searches. Ensuring that job posts and accompanying images are mobile optimized is essential for the success of hiring strategies. With the prevalence of social media, HR recruitment agencies like us understand the benefits of enhancing candidate experience. A positive experience enhances employer brand image immediately and immensely. An HR recruitment agency needs to maintain a healthy pipeline of candidates to meet hiring demands on a daily basis. A focused approach to develop a passive candidate pool is vital. Engaging with passive job seekers and unsuccessful candidates helps in not only enlarging the talent pool to work with, but also helps in strengthening employer reputation. With an aim to reign in infrastructural costs, a number of companies are opting to allow remote working employees. Manpower recruiting companies have to develop specific strategies to tap into the target candidate pool.<br><br><strong>TALENT ACQUISITION:</strong><br>R SQUARE HR strives to make sure our services are dedicated to support HR solution. Our in-house research and recruiting capabilities are extensive. We follow the best systems & processes in the recruitment industry, and so have the ability to serve our clients the relevant & quality profiles with a minimum lead-time.<br><br>We are committed for the accurate search & selection of professionals with the help of highly trained search team, widely spread network as well as existing database of skilled and experienced professionals.<br><br>We specialize in Executive Search/ Middle management/ Board Member & CXO Search. In addition, we have expertise in Mass Hiring or Team Hiring within a stipulated time and we take these assignments on a specific & reasonable term unlike general requisitions.<br><br>For an excellent output in the client side we follow a robust internal process which is time tested and which intertwines the recruiters, leads and directors. We concentrate on research methods, client confidentiality, documentation, strict timelines, reporting and follow up process, constant service feedback.<br><br>Our team includes experienced consultants, who work across the key industry segments and functions to deliver the executive search service. Whether you are a startup, a mid-market firm looking to expand your Board, or a Fortune 500 company working on your succession plan, we stand by to partner with you.<br><br>• R SQUARE Differentiation: Unlike our generalist competitors, we choose to specialize - in industries and applications that are highly information-intensive and such increasingly challenging areas as HR consulting and executive search along with our specialization in IT recruitment.<br>• Presence across Industries & Locations: Our clients are spread across India in different segments including Tourism, Education, Information Technology, Pharmaceuticals, Engineering, Manufacturing, Services and Retail etc.<br>• Leadership: They see R SQUARE HR as a leader too - often as the biggest "specialist" company, more often as leading consulting thinking and setting the standard in business values and service delivery.<br>• Secret of our success: We are sure that with our experience and expertise in the field of recruitment & training, we together with the client can accomplish optimum productivity commitment of client’s employees to achieve the goal of client organization.<br><br><strong>R SQUARE’s Executive Search:</strong><br>At R SQUARE HR, we invest in understanding the context of the client, the organization’s structure, the role to be filled and its context. This understanding is captured in a document and forms the basis of the next few steps in the search process, followed by the premier staffing companies in India.<br><br>Based on the brief prepared earlier in the process, we create a plan to identify the possible matches. We review this plan with the client and co-create it. Then, we plan the communication with the potential candidates. We get into the next step, talent attraction, followed by talent assessment.<br><br>We present our assessment of the short-listed candidates to the client and discuss further to draw up meeting schedules. With debriefs and reference checks happening simultaneously, we arrive at the selects. Offers are worked out in collaboration with the client. Throughout the process, we anchor the relationship with the candidates and help them set the ‘right expectations’ about the process, the role, the support systems and the offer.<br><br><strong>R SQUARE HR Advantages</strong><br>Process<br>• Robust assignment tracking Process.<br>• Special 2-member team working on each assignment.<br>• Use of data sciences and analytics to match roles with candidates.<br>Research<br>• Strong research team with exceptional market intelligence to give you the best service among the recruitment agencies in India.<br>• Internal Knowledge Management Framework.<br>Depth<br>• Consultants professionally qualified with experience in the respective domains for providing you a detailed and customized experience.<br>• Proven expertise of having recruited top management hires.<br><br><strong>SELECTION SERVICE:</strong><br>At R SQUARE HR Services, we understand the importance of filling up a vacancy whether it’s a new or a replacement position. Realizing fully well that speed and selection of the right profiles are of the essence, our experienced recruiters are specially trained to understand the context in which the candidate has to perform; this in tandem with our unique algorithm used in the matching process ensures that we find the best matches in the quickest possible time.<br><br>We believe that context is very important in recruitment apart from the job description, the resume and the competencies that a candidate has. Our consultants are trained to understand the context of an organization and that of the role in the organisation’s structure. These contexts are unique and need to be understood. Similarly, a candidate has a context apart from the competencies that he/she possesses. We not only match the job description of the role with the competencies of a candidate but also assess if the context of the organization and the role match with the competencies of the candidate. Thus the match ensures finding the best candidate for the job while also improving the retention rate. We are aspiring to be one of the premier staffing companies in India.<br><br><strong>Advantages of R SQUARE HR</strong><br>• Expertise in attracting and finding the best candidates using advanced data sciences.<br>• Expert consultants – that helps you reduce your hiring time and cost with their rich industry experience.<br>• Unsuccessful candidates are added to a database to aid a referral process<br>• Ability to identify individuals who technically and culturally match a client’s requirements with the advanced use of algorithms.<br>• Proven methodology for notifying unsuccessful candidates while protecting the client brand.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "rpo",
      title: "Recruitment Process Outsourcing (RPO)",
      content: `<p>R SQUARE HR understands the significance of Onboarding a set of people as per a recruitment plan and what it takes to make it happen. A number of activities have to happen in harmony with one another and continuous monitoring is required to make sure that the harmony is maintained, an aspect we emphasize on; being a pioneer among the staffing companies in India.<br><br><strong>Scope of Work</strong><br>• Understanding the organization, the structure, the context of each role holder and the requirements.<br>• Designing a plan for talent attraction, candidate assessment and candidate communication.<br>• Advertising the jobs on a variety of media.<br>• Sourcing candidates through multiple channels: interest check and validate suitability.<br>• Managing vendors and internal referrals, if any.<br>• Receiving and screening applications sourced through all the channels – R SQUARE, internal database, external sources and referrals.<br>• Capturing details of the applicants in a format that is trackable over a period of time.<br>• Coordinating interviews and any other steps in the selection process with shortlisted candidates.<br>• Working with client teams for the issuance of offer letters to successful candidates.<br>• Negotiating offers with the candidates.<br>• Engaging offered candidates till their successful joining.<br><br><strong>R SQUARE HR Advantages</strong><br>• Matches your compliance requirements fully without taking any short-cuts unlike some other staffing companies in India.<br>• A team of expert consultants who have the experience in designing and executing some of the largest RPO programs in India will make sure that the client has a good plan and executes it rigorously.<br>• R SQUARE HR Team follows standardization and process rigours to ensure that the outcomes are up to your satisfaction levels and that best practices are put in to ensure that.<br>• Our client’s brand gets strengthened by the experience that the R SQUARE HR Team delivers to the applicants.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "contract-staffing",
      title: "Contract Staffing (Workforce Service on Outsourcing basis)",
      content: `<p><strong>R SQUARE’s Flex Staffing Services Process:</strong><br>R SQUARE HR provides Flex staff with varied skill-sets on its payroll across multiple locations. We call them our Deputies (Flex Staff). They work at our client organizations for a specified period of time across different functions and levels. We do end-to-end employee life cycle management with an option to be hired on to the client payroll, offering complete flexibility. Our technology enabled transparent work process brings in proven efficiency in deputee engagement and reduces employee Query Resolution Time massively.<br><br>R SQUARE HR is sensitive to the fact that in addition to Flex Staffing, organizations also need value added services not just limited to employee engagement, performance management, talent pipeline management etc. In such cases, we bring in a dedicated team for them; they operate out of the customer’s office/ location and do everything for that client – for the line managers, HR team, finance team, legal team and all the temp staff of R SQUARE HR working there; they bring in higher efficiency in staffing and a significant increase in productivity.<br><br><strong>R SQUARE HR Advantages</strong><br>• Rigorous management of compliance and risks<br>• Domain experts carry out payroll and all compliance matters to ensure that there are no errors and all risks are managed appropriately.<br>• Our client focus ensures that we align with the specific requirements of each client around their internal processes.<br>• Enhanced Employee Productivity<br>• Right selection of candidates using our expertise and unique matching algorithm.<br>• Transparent work processes using technology that leads to a huge reduction in queries from employees and line managers.<br>• Quick resolution of queries, if any, and thus, preventing the wastage of your precious time.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "nats",
      title: "National Apprenticeship Training Scheme (NATS)",
      content: `<p>R Square’s National Apprenticeship Training Scheme (NATS) service offers comprehensive, end-to-end management of apprenticeship programs, helping organisations seamlessly comply with the Government of India’s initiative to upskill young professionals. We handle every aspect of the process, from sourcing and mobilising apprentices to coordinating training, conducting assessments, and facilitating final certification. Our expertise ensures that both apprentices and organisations benefit from structured skill development, while adhering to all government apprenticeship guidelines and statutory requirements.<br><br>With RSHRS’s NATS services, organisations can implement apprenticeship programs effortlessly. We not only ensure compliance but also help companies maximise government incentives, providing a cost-effective way to build a skilled workforce. Our extensive network enables us to place apprentices from diverse regions into meaningful roles, offering young professionals valuable industry-aligned skills to launch their careers, while ensuring businesses benefit from a trained, capable talent pipeline.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "naps",
      title: "National Apprenticeship Promotion Scheme (NAPS)",
      content: `<p>National Apprenticeship Promotion Scheme (NAPS) is a scheme launched by the Government of India on 19th August 2016 to provide financial support to establishments undertaking the Apprenticeship training.<br><br>Apprenticeship training is a course of training in an industry or establishment, under a contract of Apprenticeship which consists of:<br>1. Provides an ‘learn while earn’ opportunity to the apprentice<br>2. Tackle skill shortages and develop a talent pipeline for future needs<br><br>All establishments having work force (regular and contract employees) of 30 or more are mandated to undertake Apprenticeship Programs in a range from 2.5% -15% of its workforce (including direct contractual employees) every year.<br><br><strong>Basic Training:</strong><br>Basic training consists of theoretical and practical / lab instruction segment of every Apprenticeship Program related to a particular trade post for which on-the-Job-Training is imparted to the Apprentice. Basic training is an essential component of Apprenticeship training for those who have not undergone any institutional training / skill training before taking up On-the-Job-Training / practical training. Basic Training is imparted to fresher apprentices for acquiring a reasonable ability to handle Instruments / Machineries / Equipment independently prior to being moved to Shop Floor / Work Area for practical / On-Job training.<br><br><strong>On-the-Job-Training (OJT):</strong><br>OJT is practical training imparted at the workplace premises of an establishment.<br><br><strong>Trades / Courses establishments under the Apprenticeship act:</strong><br>There are two categories of Trades defined under the Apprenticeship act 1961.<br>Designated Trades: Those notified by the Government are referred to as “Designated Trades”<br>Optional Trades: The other trades which are not included in the notified list of the Designated Trades but opted as a Trade / Course to be run under the Apprentices Act by an establishment are referred to as “Optional Trades”.<br><br><strong>Categories covered under the program:</strong><br>• ITI Pass Outs<br>• All Pass Outs from the NSQF aligned courses including PMKVY / DDUGKY etc.<br>• Dual-Learning Mode from it is<br>• Fresh apprentices</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "functional-hr",
      title: "Functional HR Services on Outsourcing basis",
      content: `<p>We provide all HR Services under one roof. So, small companies do not require HR Department to carry out HR processes. We carry out all the tasks on outsourcing basis and thus making companies’ tension free from manpower issues and we provide strength for their core business operations.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "payroll",
      title: "Payroll Outsourcing",
      content: `<p>RSHRS offers a comprehensive payroll management solution that integrates technology and streamlines processes. Our services cover leaves and attendance management, verification of employee investments and claims, payroll calculation, and the generation of pay slips and statutory reports. Additionally, employees can access relevant employment details through a self-service portal, simplifying HR administration for businesses.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "legal-compliance",
      title: "Legal Compliance",
      content: `<p>We also provide labour law compliance services, including remitting statutory dues, filing returns, maintaining statutory registers, and liaising with government offices to address complex queries and disputes.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "third-party-auditor",
      title: "Third Party Auditor for HR Services",
      content: `<p>Number of organizations engages manpower agencies for their manpower requirement in offices, housekeeping, security services etc. These agencies/ contractors sometimes fail in maintaining legal compliance and unfortunately organization who has engaged them are held responsible being a principal employer for the mistakes carried out by the contractor. We provide service as Third Party Auditor for the HR services and legal compliances.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "background-verification",
      title: "Employment Background Verification",
      content: `<p><strong>Employment check</strong> is a process to validate an applicant's previous employment details including length of employment, position, and rehire eligibility. Frequently, information supplied by job applicants is either false or inflated. Having false information, one can make an incorrect decision for the applicant's position or salary.<br><br><strong>Education check:</strong><br>Education check is an easy and cost effective way to determine the validity and accuracy of an applicant's educational history. Education check includes the degree received, dates of attendance, graduation date, and any special recognition or awards. Frequently, information supplied by the applicant is either false or inflated. Having false information, one can make an incorrect decision for the business.<br><br><strong>Reference check:</strong><br>Reference check for a sensitive or confidential position, often a reference check is the best way to determine what an applicant is really like. If the job you are seeking to fill is sensitive or confidential in nature, often a personal reference check helps to ensure the employment references are valid in today's high-risk competitive marketplace. For middle and upper level management, this search is vital in determining the true character of an applicant.<br><br>More and more companies are now checking job references, along with other background checks.<br><br><strong>Credential check:</strong><br>Is your business highly dependent on the credentials of the individual? If so, are all the certificates and credentials accurate? Whether your business is involved in technological services to a Multi-National or public services like hospitals or civil contracts, the credentials of the individual and current competency are what make your relationship strong with your customers. Hiring a new resource is a major investment in both your business and the individual's future.<br><br>State-of-the-art technology system helps our researchers to verify relevant education, training, professional state licensure and certification credentials. We expedite the process in gathering, formatting and reporting the information supplied by the applicant and information we have gathered by concerned agencies or institutions.<br><br><strong>Address Check:</strong><br>Find out why the address check is very important during your business process. Problems arising from incorrect or missing address information are usually not obvious at first. You might encounter a surprise when the address does not exist or addressee never lived there or when your correspondence about the loan installment payment notice is undeliverable and returned.<br><br><strong>Identity Check:</strong><br>Identity check is a process to find out if they are who they say they are. Negligent hiring may lead to a lawsuit or corporate culture breach. The most common scam, called "identity theft", occurs when the profile applies for a house loan, credit card or bank account with another person's name and address. The false identity misuses the resources under the true identity's name. A bad decision can wreak havoc on a company's budget and reputation as well as ruin the career of the hiring official.<br><br><strong>Criminal Check:</strong><br>Criminal check is a process to reveal any past convictions or criminal activities by the applicant. A search of government records is conducted at the appropriate level. Jurisdictions searched are determined by the applicant's address, nationality, government intelligence, or as designated by the client. These searches include those associated with the subject's current and previous addresses. Reported information includes the location, case number, date filed, charges, conviction date, and disposition.<br><br>Criminal checks help exclude people with criminal records who would be a crime risk. Organizations must conduct a criminal check to ensure other employees are safe and feel safe at their work environment. Our cutting edge technology system helps our researchers to expedite the process of gathering, analyzing, formatting and reporting the information supplied by the candidate and the information verified by the government authorities.<br><br><strong>Drug Check:</strong><br>Drug check – 5 Panel: Indicates the presence of illegal or prescription drugs from laboratory results. We partner with an independent company to provide complete drug screening services. The 5-Panel drug screen includes testing for amphetamines, cocaine, marijuana, opiates, and phencyclidine (PCP).<br><br>Drug check – 10 Panel: Indicates the presence of illegal or prescription drugs from laboratory results. We partner with an independent company to provide complete drug screening services. The 10-Panel drug screen includes testing for amphetamines, barbiturates, benzodiazepines, cocaine, marijuana, methadone, methaqualone, opiates, phencyclidine (PCP), and propoxyphene.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "business-reforms",
      title: "Advisory Services on Business Reforms & Improvement",
      content: `<p>As the Business world tries to steer the present challenges, many of the management practices will undergo changes in the days ahead. The biggest challenge today is to achieve the strategic intent of the business, provide organizational scalability & sustained growth through optimized resources-manpower and material. We need to make our work force more engaged, enjoy what they do and draw motivation from their achievement. Performance parameters need to more align to key outcome.<br><br>Our output needs to meet higher level quality. We should set objective without draining our self or burning out our talent pool. We need to understand that it’s not quantum but quality of our focus on the on the key areas that will give us the desired results. Quantum can in the short run give us a level of output though higher than the previous level and in line with our business target but not near the possible potential and perhaps on target but with a cost. Thus, many a times the approach is not sustainable.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "training",
      title: "Training & Development",
      content: `<p>Someone has beautifully said: What happens if we train our people and they leave? ….Wrong question! According to Zig Ziglar, you should be asking what happens if we don’t train our people, and they stay?<br><br>R SQUARE HR employs a suite of interventions to enhance & develop general and professional skills of all our client’s employees which aim for betterment of basic knowledge and competency skills. All programs are delivered by well-qualified and renowned training professionals having rich experience in organizational learning and development. We offer various business improvement packages which are based on diagnostics studies of client model and business environment. Our learning and development processes are exhaustive in nature which takes care of all skill gaps, future learning and implementing change management at our client side.<br><br>The entire Learning & Development delivery model performed at 5 levels across the organizational hierarchy with specific modes at each level.<br><br>Education and Learning Process covers a set of critical methodologies like Self Reflection, Action Therapy, Dramatics, and Case Studies & Role Plays to increase the retention level at the trainees end.<br><br>R SQUARE HR undertakes a range of modules in learning & development across levels, out of those, few of the key modules are:<br><br><strong>Techno Management Modules:</strong><br>• Quality & Process Compliance<br>• Preventative Maintenance<br>• Cost Effectiveness<br>• Supply Chain & Logistics / Inventory Management<br>• Production Planning & Control (PPC)<br>• Six Sigma Applications<br>• Business Excellence<br><br><strong>Executive Coaching Modules:</strong><br>• Leadership Development<br>• Train the Trainer (TTT)<br>• Change Management<br>• Project Management<br>• Managerial Effectiveness Programme<br>• Coaching & Mentoring<br>• Vision & Mission Exercise for Senior Management Group<br>• Thinking Strategy<br><br><strong>Soft Skills / Behavioural Modules:</strong><br>• Magic of Teams<br>• Creating Right Attitude<br>• Motivation<br>• Managing Stress<br>• Problem Solving & Decision Making Skills<br>• Creativity and Out of Box Thinking<br>• Effective Time Management<br>• Communications<br>• Interpersonal Skills<br>• Presentation Skills<br>• CRM<br>• Business Etiquettes<br>• Transactional Analysis<br>• Self-Awareness</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "rti",
      title: "Training on RTI and Legal Services under RTI",
      content: `<p>After the implementation of Right to Information Act, all the Government, Semi Government and such other organisations needs to adopt certain changes in their day today working style. It requires a balanced approach between speed of working and protection of the law. R Square provides specific insight to the employees and officers working in such organizations. We train officers as well as general public to get the best out of RTI. Interested organizations may approach R Square for their specific needs.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "posh",
      title: "Training on Prevention of Sexual Harassment (PoSH) Act",
      content: `<p>The Sexual Harassment of Women at Workplace (Prevention, Prohibition and Redressal) Act, 2013, commonly known as POSH (Prevention of Sexual Harassment) Act is a legislative act in India that seeks to protect women from sexual harassment at their place of work. It was passed by the Lok Sabha (the lower house of the Indian Parliament) on 3 September 2012, by the Rajya Sabha (the upper house) on 26 February 2013, and got the assent of the President on 23 April 2013. The Act came into force on 9 December 2013. This statute superseded the Vishaka Guidelines for Prevention of Sexual Harassment (POSH) introduced by the Supreme Court (SC) of India. It was reported by the International Labour Organization that very few Indian employers were compliant to this statute. Most Indian employers have not implemented the law despite the legal requirement that any workplace with more than 10 employees need to implement it. According to a FICCI-EY November 2015 report, 36% of Indian companies and 25% among MNCs are not compliant with the Sexual Harassment Act, 2013.The government has threatened to take stern action against employers who fail to comply with this law.<br><br>A report by the Indian Express in May 2023 finds that half of India's sports federations are yet to create an "Internal Complaints Committee" as mandated by this law.<br><br>Sometimes, companies desire to implement but needs support. We extend our helping hand to such organizations by providing them necessary guidance and training.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "labour-codes",
      title: "Training on New Labour Codes",
      content: `<p>India’s labour law landscape is undergoing one of the biggest transformations in decades. The new labour codes for HR professionals and businesses are expected to reshape how organizations manage wages, working conditions, social security, and industrial relations. The upcoming changes require HR leaders and business owners to prepare themselves for understanding compliance requirements which will come into effect when the country reaches implementation date.<br><br>The Government of India has consolidated 29 existing labour laws into four new labour codes to simplify compliance and create a more transparent and structured system for employers and employees. The new system consists of four main parts which are the Code on Wages, Industrial Relations Code, Occupational Safety, Health and Working Conditions Code, and the Social Security Code.<br><br>Organizations need to conduct alignment checks between their HR policies and payroll systems and compliance frameworks because the new regulations will be implemented soon.<br><br><strong>Why HR Professionals Must Prepare Now?</strong><br>The new labour codes will introduce several important changes for businesses across industries. HR teams will need to review and update multiple aspects of workforce management, including:<br>• Revised wage definitions impacting salary structures<br>• Changes in working hours and overtime rules<br>• Updated PF and social security contributions<br><br>The new labour codes will bring in some significant changes to businesses across various industries. Some of the key changes that will require attention and updates to various facets of employee management will include:<br>• New wage definitions<br>• Changes to employee work hours<br>• New PF and social security contributions<br>• New regulations pertaining to contract employees<br>• New regulations around contract labour and gig workers<br>• Revised compliance and documentation requirements<br><br>Preparing early will help organizations avoid last-minute compliance challenges and ensure smooth implementation once the codes come into effect.<br><br><strong>Labour Law Training:</strong><br>To help professionals stay ahead of these changes, R Square provides a new labour codes 2026 training designed specifically for HR professionals, compliance managers, and business leaders.<br><br>This labour law training will provide practical insights into the upcoming implementation of the labour codes and how organizations can prepare effectively.<br><br>Participants will gain a clear understanding of:<br>• The structure and scope of the four new labour codes<br>• Key changes in wages, compliance, and employee benefits<br>• The impact of labour codes on HR policies and payroll<br>• Steps organizations must take before implementation<br>• Practical guidance for HR teams and management<br><br>All participants will receive a Certificate of Appreciation, recognizing their participation and learning. More importantly, attendees will gain practical knowledge before the implementation of the new labour codes, helping them confidently prepare their organizations for the upcoming transition.<br><br><strong>Stay Ahead of Labour Law Changes in India:</strong><br>As India prepares for the rollout of the new labour codes, knowledge and preparation will be essential for HR professionals and businesses. Our expert-led training provides a valuable opportunity to understand the changes, clarify doubts, and ensure your organization is ready for the future of labour compliance.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "tourism",
      title: "Tourism Specific Solutions & Services",
      content: `<p>R Square has excellent experience in working with the Tourist centric organizations. Supported projects in the tribal areas and hence developed expertise on local employment. R Square being a company having versatile background, can can provide excellent IT and Non-IT solutions for Tourism Projects.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "csr",
      title: "Supporting CSR Activity",
      content: `<p>The allied social activities of our founder encourages us to support CSR activities and if any organization wants services of R Square to strength their CSR activity and wants appropriate recognition of the company in the respective sector, they should approach R Square.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "collaborations",
      title: "Collaborating with Various Institutions",
      content: `<p>We have signed MoU’s with renowned institutions, colleges and Universities for various educational and placement programs.<br><br>The list of institutes with whom we are working in various capacities & role are as follows:<br>• IIM Udaipur<br>• Silver Oak University, Ahmedabad (all institutes working under the University)<br>• Sardarkrushinagar Dantiwada Agricultural University, Dantivada, Gujarat.<br>• Society For Creation of Opportunities Through Proficiency In English – SCOPE<br>• Knowledge Consortium of Gujarat<br>• Hk School of Foreign Languages & Hkumars Education Institute, Ahmedabad<br>• All the Government Colleges & Schools of Gujarat<br>• Gujarat Disaster Management Institute, Gandhinagar.<br>• The M S University of Baroda, Vadodara<br>• Nirma University, Ahmedabad<br>• Gujarat University, Ahmedabad<br>• GLS University, Ahmedabad<br>• Ahmedabad Management Association, Ahmedabad<br>• Saradar Patel Institute of Public Administration, Ahmedabad<br>• Computer Society of India, Ahmedabad<br>• Sardar Patel Institute of Economic & Social Research, Ahmedabad<br>• Prakshal IT Academy, Ahmedabad<br>• Sardar Patel College of Pharmacy<br>• Sardar Patel College of Engineering<br>• Sardar Patel College of Administration & Management<br>• Sardar Patel College of Education<br>• Sardar Patel College of Applied Science<br>• Sardar Patel College of Commerce<br>• Balaji College of Paramedical<br>• Balaji College of Law<br>• Balaji College of Physiotherapy<br>• Balaji College of Nursing - Approved by Guj.Nursing Council<br>• Tirupati School of Nursing - Approved by Guj.Nursing Council<br>• Tirupati Industrial Training Institute, Vadodara approved by the Government of Gujarat<br>• My Shanen School (GSEB) , Vadodara<br>• Sharda Mandir English Medium School (GSEB) , Vadodara<br>• Gujarat Refinery School (GSEB) , Vadodara<br>• Atmiya Vidyalay, Vadodara<br>• Shanen School (CBSE) , Vadodara<br>• My Shanen Pre-School, Vadodara<br>• Sarvamangal School, Vadodara</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "cultural",
      title: "Cultural Activities (Musical programs, films, Gujarati serials, drama etc.)",
      content: `<p>R Square is an HR company having founders with the versatile background. With numerous contacts R Square is sometimes requested to provide artists from different areas like music, theater, serials and films. During providing such service R Square in association with extra ordinary personalities from this industry initiated new venture of supporting Cultural Activities at Schools, Colleges and in general.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "it-consultancy",
      title: "IT Consultancy & Solutions",
      content: `<p>The founder of R Square, Mr. S N Rao is having more than 45 years of experience in the development and implementation of IT solutions at various organisations. R Square also gets assignments for providing IT manpower to various IT companies. So, combination of consultation in IT, development of ERP/MIS and successful implementation by providing appropriate training of change management etc. put together lead R Square in the field of IT also.</p>`,
      preview: "",
      hasMore: false
    },
    {
      id: "clients",
      title: "Some of the Client’s and Services",
      content: `<p>R SQUARE has handled a number of clients by providing various HR Services like preparation of HR Policy manual, recruitment, staffing, training, payroll, overall monitoring and supervision of the staff deployed etc.<br><br>Mr. S N Rao has prepared HR Policy Manuals for<br>• IIM Ahmedabad,<br>• IIM Udaipur,<br>• Gujarat Ecology Commission,<br>• India International Boolean Exchange (IFSC) Ltd. and<br>• Sahaj Solar Limited.<br><br>Mr. S N Rao has also provided training, recruitment & staffing services to<br>• IIM Ahmedabad,<br>• Tourism Corporation of Gujarat Limited,<br>• Statue of Unity Area Development and Tourism Governance Authority,<br>• Gujarat Council of Science City,<br>• Forest Department of Gujarat,<br>• Children Nutrition Park,<br>• Human Development and Research Foundation,<br>• Voltbek Home Appliances Ltd.,<br>• Tenneco Automotive (India) Pvt. Ltd. etc.<br><br>S N Rao has trained and provided 132 Tourist Guides to the Tourism Corporation of Gujarat Limited (TCGL) for the Statue of Unity and other 7 district locations of Gujarat. These Tourist Guides are performing their duty since July-2019 to till date. The Tourist Guides coming from the tribal background and trained by us performed so well that our Honorable Prime Minister Shri Narendra Modi ji praised their performance in “Man ki baat” and other public programs. We also provided Aquatic Educators to the Gujarat Council of Science City, Ahmedabad.<br><br>We have organized and conducted training programs on<br>• Tourist Guides,<br>• Housekeeping,<br>• Security,<br>• Spoken English,<br>• International Languages<br>• MIS<br>• MS-Office<br>• HR Management<br>• Communication Skils<br>• Pradhan Mantri Kaushal Vikas Yojna (PMKVY),<br>• Pradhan Mantri Dakshta Aur Kushalta Sampann Hitgrahi (PM-DAKSH) Yojana,<br>• Deen Dayal Upadhyaya Gramin Kaushalya Yojana (DDU-GKY),<br>• Deen Dayal Antyodaya Yojana - National Urban Livelihood Mission (DAY-NULM),<br>• Electronics System Design and Manufacturing (ESDM) Training Programs etc.</p>`,
      preview: "",
      hasMore: false
    }
  ];

  // Precompute previews and hasMore flags (safe for SSR)
  for (const section of sections) {
    const plain = section.content.replace(/<[^>]*>/g, '');
    section.preview = plain.length > 300 ? plain.slice(0, 300).trim() + '...' : plain;
    section.hasMore = plain.length > 300;
  }

  function toggleSection(id) {
    expanded[id] = !expanded[id];
    expanded = { ...expanded };
  }

  // Navigation cards
  const services = [
    { id: "hr-advisory", title: "HR Advisory", icon: "📊" },
    { id: "psychometric", title: "Psychometric Tests", icon: "🎯" },
    { id: "permanent-recruitment", title: "Permanent Recruitment", icon: "🔍" },
    { id: "rpo", title: "Recruitment Process Outsourcing", icon: "🔄" },
    { id: "contract-staffing", title: "Contract Staffing", icon: "👥" },
    { id: "nats", title: "NATS", icon: "📚" },
    { id: "naps", title: "NAPS", icon: "🎓" },
    { id: "functional-hr", title: "Functional HR Outsourcing", icon: "⚙️" },
    { id: "payroll", title: "Payroll Outsourcing", icon: "💰" },
    { id: "legal-compliance", title: "Legal Compliance", icon: "⚖️" },
    { id: "third-party-auditor", title: "Third Party Auditor", icon: "🔍" },
    { id: "background-verification", title: "Background Verification", icon: "✅" },
    { id: "business-reforms", title: "Business Reforms Advisory", icon: "💡" },
    { id: "training", title: "Training & Development", icon: "🎓" },
    { id: "rti", title: "RTI Training", icon: "📜" },
    { id: "posh", title: "POSH Training", icon: "⚖️" },
    { id: "labour-codes", title: "New Labour Codes Training", icon: "📋" },
    { id: "tourism", title: "Tourism Solutions", icon: "🏞️" },
    { id: "csr", title: "CSR Support", icon: "🤝" },
    { id: "collaborations", title: "Institutional Collaborations", icon: "🏛️" },
    { id: "cultural", title: "Cultural Activities", icon: "🎭" },
    { id: "it-consultancy", title: "IT Consultancy", icon: "💻" },
    { id: "clients", title: "Our Clients", icon: "🏢" }
  ];

  function scrollToSection(id) {
    const element = document.getElementById(id);
    if (element) {
      element.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
  }

  onMount(() => {
    // Back to top button visibility
    window.addEventListener('scroll', () => {
      showBackToTop = window.scrollY > 500;
    });
  });
</script>

<main class="page">
  <!-- Animated Background Blobs -->
  <div class="blob blob-1"></div>
  <div class="blob blob-2"></div>

  <!-- Hero -->
  <div class="hero">
    <div class="hero-badge">R SQUARE HR SERVICES</div>
    <h1>Comprehensive <span class="accent">HR & Business Solutions</span></h1>
    <p>End-to-end human capital management, compliance, training, and advisory services tailored to drive excellence.</p>
  </div>

  <!-- Service Navigation Cards -->
  <div class="cards">
    {#each services as s, i}
      <button
        class="card"
        on:click={() => scrollToSection(s.id)}
        aria-label={s.title}
        in:fly={{ y: 20, duration: 400, delay: i * 40 }}
      >
        <div class="icon">{s.icon}</div>
        <h3>{s.title}</h3>
      </button>
    {/each}
  </div>

  <!-- All Sections with Read More -->
  <div class="sections-container">
    {#each sections as section}
      <section id={section.id} class="section">
        <h2>{section.title}</h2>
        <div class="section-content">
          {#if expanded[section.id]}
            {@html section.content}
          {:else}
            <p class="preview">{section.preview}</p>
          {/if}
        </div>
        {#if section.hasMore}
          <button class="read-more-btn" on:click={() => toggleSection(section.id)}>
            {expanded[section.id] ? 'Show less' : 'Read more'}
          </button>
        {/if}
      </section>
    {/each}
  </div>
</main>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700&display=swap');

  :root {
    --primary: #2c6e9e;
    --primary-dark: #1f587e;
    --accent: #d97a4a;
    --gray-bg: #f9fafb;
    --card-bg: #ffffff;
    --border-light: #eef2f6;
    --text-dark: #1a2c3e;
    --text-muted: #5a6e7e;
    --shadow-sm: 0 4px 12px rgba(0, 0, 0, 0.02);
    --shadow-md: 0 12px 28px rgba(0, 0, 0, 0.04);
    --transition: all 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
  }

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  body {
    background: var(--gray-bg);
    font-family: 'Inter', system-ui, -apple-system, sans-serif;
    color: var(--text-dark);
    line-height: 1.5;
    overflow-x: hidden;
  }

  .page {
    max-width: 1200px;
    margin: 0 auto;
    padding: 40px 24px 60px;
    position: relative;
    z-index: 2;
  }

  /* Animated Background Blobs */
  .blob {
    position: fixed;
    border-radius: 50%;
    filter: blur(60px);
    z-index: 0;
    pointer-events: none;
    opacity: 0.4;
    animation: float 20s infinite ease-in-out;
  }
  .blob-1 {
    width: 400px;
    height: 400px;
    background: radial-gradient(circle, rgba(44,110,158,0.3) 0%, rgba(217,122,74,0.1) 100%);
    top: -100px;
    left: -100px;
  }
  .blob-2 {
    width: 500px;
    height: 500px;
    background: radial-gradient(circle, rgba(217,122,74,0.2) 0%, rgba(44,110,158,0.05) 100%);
    bottom: -150px;
    right: -150px;
    animation-duration: 25s;
    animation-direction: reverse;
  }
  @keyframes float {
    0%, 100% { transform: translate(0, 0) rotate(0deg); }
    50% { transform: translate(30px, -30px) rotate(5deg); }
  }

  /* Hero */
  .hero {
    text-align: center;
    margin-bottom: 48px;
    padding: 20px 0 30px;
    position: relative;
  }
  .hero-badge {
    display: inline-block;
    background: rgba(44,110,158,0.08);
    color: var(--primary);
    font-size: 0.8rem;
    font-weight: 600;
    padding: 5px 14px;
    border-radius: 40px;
    margin-bottom: 20px;
    letter-spacing: 0.3px;
    backdrop-filter: blur(4px);
  }
  .hero h1 {
    font-size: 2.8rem;
    font-weight: 700;
    margin-bottom: 12px;
    letter-spacing: -0.02em;
    line-height: 1.2;
  }
  .accent {
    background: linear-gradient(135deg, var(--primary), var(--accent));
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
  }
  .hero p {
    font-size: 1.1rem;
    color: var(--text-muted);
    max-width: 650px;
    margin: 0 auto;
  }

  /* Cards */
  .cards {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    gap: 18px;
    margin-bottom: 60px;
  }
  .card {
  background: #ffffff;
  border: 1px solid #eef2f6;
  border-radius: 28px;

  padding: 20px 14px;
  text-align: center;

  cursor: pointer;

  position: relative;
  overflow: hidden;

  transition: all 0.4s cubic-bezier(0.2, 0.9, 0.4, 1);
  box-shadow: 0 8px 18px -8px rgba(0,0,0,0.05);

  backdrop-filter: blur(4px);
}
.card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;

  width: 5px;
  height: 0%;
  background: linear-gradient(135deg, #0066ff 0%, #0044cc 40%, #0066ff 100%);
  transition: height 0.4s ease;
}
  .card:hover {
  transform: translateY(-10px) scale(1.03);
  border-color: rgba(44,110,158,0.3);
  box-shadow: 0 25px 40px -12px rgba(0,0,0,0.15);
}

.card:hover::before {
  height: 100%;
}
 .icon {
  font-size: 1.9rem;
  margin-bottom: 10px;

  width: 60px;
  height: 60px;
  border-radius: 18px;

  background: #f5f7fa;

  display: flex;
  align-items: center;
  justify-content: center;

  margin-left: auto;
  margin-right: auto;

  transition: all 0.3s ease;
}

.card:hover .icon {
  background: linear-gradient(135deg, #2c6e9e, #d97a4a);
  color: white;
  transform: scale(1.15) rotate(6deg);
}
  .card:hover .icon {
    transform: scale(1.1);
  }
 .card h3 {
  font-size: 0.95rem;
  font-weight: 600;
  color: #1a2c3e;
  margin: 0;
}

  /* Sections */
  .sections-container {
    display: flex;
    flex-direction: column;
    gap: 48px;
  }
  .section {
    background: var(--card-bg);
    border-radius: 32px;
    padding: 36px 40px;
    border: 1px solid var(--border-light);
    box-shadow: var(--shadow-sm);
    transition: var(--transition);
    scroll-margin-top: 100px;
  }
  .section:hover {
    box-shadow: var(--shadow-md);
    border-color: rgba(44,110,158,0.2);
  }
  .section h2 {
    font-size: 1.8rem;
    font-weight: 700;
    margin-top: 0;
    margin-bottom: 24px;
    padding-bottom: 12px;
    border-bottom: 2px solid rgba(44,110,158,0.2);
    color: var(--text-dark);
    position: relative;
  }
  .section h2::after {
    content: '';
    position: absolute;
    bottom: -2px;
    left: 0;
    width: 60px;
    height: 2px;
    background: linear-gradient(90deg, var(--primary), var(--accent));
  }
  .section-content {
    color: #2c3f4e;
    line-height: 1.7;
  }
  .section-content p {
    margin-bottom: 1em;
  }
  .section-content p:last-child {
    margin-bottom: 0;
  }
  .section-content strong {
    color: var(--primary);
    font-weight: 600;
  }
  .preview {
    color: #2c3f4e;
    line-height: 1.7;
  }
  .read-more-btn {
    background: transparent;
    color: var(--primary);
    border: 1px solid var(--border-light);
    padding: 8px 20px;
    border-radius: 40px;
    font-size: 0.85rem;
    font-weight: 500;
    cursor: pointer;
    margin-top: 20px;
    transition: var(--transition);
    font-family: inherit;
  }
  .read-more-btn:hover {
    background: rgba(44,110,158,0.05);
    border-color: var(--primary);
  }

  /* Footer */
  .footer {
    margin-top: 64px;
    text-align: center;
    padding: 24px 0 16px;
    border-top: 1px solid var(--border-light);
    font-size: 0.85rem;
    color: var(--text-muted);
  }

  /* Back to Top Button */
  .back-to-top {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    background: var(--primary);
    color: white;
    border: none;
    width: 48px;
    height: 48px;
    border-radius: 50%;
    font-size: 1.5rem;
    cursor: pointer;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    transition: var(--transition);
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .back-to-top:hover {
    background: var(--primary-dark);
    transform: scale(1.05);
  }

  /* Responsive */
  @media (max-width: 768px) {
    .page {
      padding: 24px 16px 40px;
    }
    .hero h1 {
      font-size: 2rem;
    }
    .cards {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
      gap: 12px;
    }
    .section {
      padding: 24px 20px;
    }
    .section h2 {
      font-size: 1.5rem;
    }
    .back-to-top {
      width: 40px;
      height: 40px;
      font-size: 1.2rem;
      bottom: 1rem;
      right: 1rem;
    }
  }
</style>