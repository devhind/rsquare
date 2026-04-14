<script context="module">
  // This runs once at build time – optional, but we keep runtime loading
</script>

<script>
  import { onMount } from 'svelte';
  import { Country, State, City } from 'country-state-city';
  import intlTelInput from 'intl-tel-input';
  import 'intl-tel-input/build/css/intlTelInput.css';

  // ---------- FORM DATA ----------
  let form = {
    companyName: '',
    email: '',
    phone: '',
    country: '',
    state: '',
    city: '',
    district: '',
    taluka: '',
    zipcode: '',
    address: '',
    additionalDetails: '',
    fromDate: '',
    toDate: '',
    dueDate: '',
  };

  // ---------- LOCATION DATA ----------
  let countries = [];
  let states = [];
  let cities = [];

  // ---------- SEARCH TERMS ----------
  let countrySearch = '';
  let stateSearch = '';
  let citySearch = '';
  let districtSearch = '';
  let talukaSearch = '';

  // ---------- DROPDOWN OPEN STATES ----------
  let countryOpen = false;
  let stateOpen = false;
  let cityOpen = false;
  let districtOpen = false;
  let talukaOpen = false;

  // ---------- DISTRICT & TALUKA MAPPING (Gujarat) ----------
  const districtTaluka = {
    AHMADABAD: ['Bavla', 'Daskroi', 'Detroj-Rampura', 'Dhandhuka', 'Dholera', 'Dholka', 'Mandal', 'Sanand', 'Viramgam'],
    AMRELI: ['Amreli', 'Babra', 'Bagasara', 'Dhari', 'Jafrabad', 'Khambha', 'Kunkavav Vadia', 'Lathi', 'Lilia', 'Rajula', 'Savar Kundla'],
    ANAND: ['Anand', 'Anklav', 'Borsad', 'Khambhat', 'Petlad', 'Sojitra', 'Tarapur', 'Umreth'],
    ARAVALLI: ['Bayad', 'Bhiloda', 'Dhansura', 'Malpur', 'Meghraj', 'Modasa'],
    BANASKANTHA: ['Amirgadh', 'Bhabhar', 'Danta', 'Dantiwada', 'Deesa', 'Deodar', 'Dhanera', 'Kankrej', 'Lakhani', 'Palanpur', 'Suigam', 'Tharad', 'Vadgam', 'Vav'],
    BHARUCH: ['Amod', 'Anklesvar', 'Bharuch', 'Hansot', 'Jambusar', 'Jhagadia', 'Netrang', 'Vagra', 'Valia'],
    BHAVNAGAR: ['Bhavnagar', 'Gariadhar', 'Ghogha', 'Jesar', 'Mahuva', 'Palitana', 'Sihor', 'Talaja', 'Umrala', 'Vallabhipur'],
    BOTAD: ['Barwala', 'Botad', 'Gadhada', 'Ranpur'],
    CHHOTA_UDEPUR: ['Bodeli', 'Chhota Udaipur', 'Jetpur Pavi', 'Kavant', 'Nasvadi', 'Sankheda'],
    DAHOD: ['Dahod', 'Devgadbaria', 'Dhanpur', 'Fatepura', 'Garbada', 'Jhalod', 'Limkheda', 'Sanjeli', 'Singvad'],
    DANGS: ['Ahwa', 'Subir', 'Waghai'],
    DEVBHUMI_DWARKA: ['Bhanvad', 'Kalyanpur', 'Khambhalia', 'Okhamandal'],
    GANDHINAGAR: ['Dehgam', 'Gandhinagar', 'Kalol', 'Mansa'],
    GIR_SOMNATH: ['Kodinar', 'Patan-Veraval', 'Sutrapada', 'Talala', 'Una'],
    JAMNAGAR: ['Dhrol', 'Jamjodhpur', 'Jamnagar Rural', 'Jodiya', 'Kalavad', 'Lalpur'],
    JUNAGADH: ['Bhesan', 'Junagadh', 'Keshod', 'Malia', 'Manavadar', 'Mangrol', 'Mendarda', 'Vanthali', 'Visavadar'],
    KACHCHH: ['Abdasa', 'Anjar', 'Bhachau', 'Bhuj', 'Gandhidham', 'Lakhpat', 'Mandvi', 'Mundra', 'Nakhatrana', 'Rapar'],
    KHEDA: ['Galteshwar', 'Kapadvanj', 'Kathlal', 'Kheda', 'Mahudha', 'Matar', 'Mehmedabad', 'Nadiad', 'Thasra', 'Vaso'],
    MAHESANA: ['Becharaji', 'Jotana', 'Kadi', 'Kheralu', 'Mahesana', 'Satlasana', 'Unjha', 'Vadnagar', 'Vijapur', 'Visnagar'],
    MAHISAGAR: ['Kadana', 'Khanpur', 'Lunawada', 'Santrampur', 'Virpur'],
    MORBI: ['Halvad', 'Maliya Morbi', 'Morbi', 'Tankara', 'Wankaner'],
    NARMADA: ['Garudeshwar', 'Nandod', 'Sagbara', 'Tilakwada'],
    NAVSARI: ['Gandevi', 'Jalalpore', 'Khergam', 'Navsari', 'Vansda'],
    PANCHMAHALS: ['Godhra', 'Ghoghamba', 'Halol', 'Jambughoda', 'Kalol', 'Morwa (Hadaf)', 'Shehera'],
    PATAN: ['Chanasma', 'Harij', 'Patan', 'Radhanpur', 'Sami', 'Sankheswar', 'Santalpur', 'Saraswati', 'Sidhpur'],
    PORBANDAR: ['Kutiyana', 'Porbandar', 'Ranavav'],
    RAJKOT: ['Dhoraji', 'Gondal', 'Jamkandorna', 'Jasdan', 'Jetpur', 'Kotda Sangani', 'Lodhika', 'Paddhari', 'Rajkot', 'Upleta', 'Vinchhiya'],
    SABARKANTHA: ['Himatnagar', 'Idar', 'Khedbrahma', 'Poshina', 'Prantij', 'Talod', 'Vadali', 'Vijaynagar'],
    SURAT: ['Bardoli', 'Chorasi', 'Kamrej', 'Mandvi', 'Mahuva', 'Mangrol', 'Olpad', 'Palsana', 'Umarpada'],
    SURENDRANAGAR: ['Chuda', 'Dasada', 'Dhrangadhra', 'Lakhtar', 'Limbdi', 'Muli', 'Sayla', 'Thangadh', 'Wadhwan'],
    TAPI: ['Dolvan', 'Kukarmunda', 'Nizar', 'Songadh', 'Uchchhal', 'Valod', 'Vyara'],
    VADODARA: ['Dabhoi', 'Desar', 'Karjan', 'Padra', 'Savli', 'Sinor', 'Vadodara', 'Vaghodia'],
    VALSAD: ['Dharampur', 'Kaprada', 'Pardi', 'Umbergaon', 'Valsad', 'Vapi'],
  };

  let districts = Object.keys(districtTaluka);
  let talukas = [];

  // ---------- REACTIVE FILTERS ----------
  $: filteredCountries = countries.filter(c =>
    c.name.toLowerCase().includes(countrySearch.toLowerCase())
  );
  $: filteredStates = states.filter(s =>
    s.name.toLowerCase().includes(stateSearch.toLowerCase())
  );
  $: filteredCities = cities.filter(c =>
    c.name.toLowerCase().includes(citySearch.toLowerCase())
  );
  $: filteredDistricts = districts.filter(d =>
    d.toLowerCase().includes(districtSearch.toLowerCase())
  );
  $: filteredTalukas = talukas.filter(t =>
    t.toLowerCase().includes(talukaSearch.toLowerCase())
  );

  // ---------- HELPERS ----------
  $: isGujaratSelected = () => {
    const stateObj = states.find(s => s.isoCode === form.state);
    return stateObj?.name === 'Gujarat';
  };

  $: fullAddress = [
    form.address,
    form.city,
    form.district,
    form.taluka,
    states.find(s => s.isoCode === form.state)?.name,
    countries.find(c => c.isoCode === form.country)?.name,
    form.zipcode,
  ]
    .filter(Boolean)
    .join(', ');

  // ---------- DATE HELPER (dd/mm/yyyy) ----------
  function parseDateDDMMYYYY(dateStr) {
    if (!dateStr) return null;
    const parts = dateStr.split('/');
    if (parts.length !== 3) return null;
    const day = parseInt(parts[0], 10);
    const month = parseInt(parts[1], 10) - 1; // months 0-index
    const year = parseInt(parts[2], 10);
    if (isNaN(day) || isNaN(month) || isNaN(year)) return null;
    return new Date(year, month, day);
  }

  function formatDateDDMMYYYY(date) {
    if (!date) return '';
    const d = date.getDate();
    const m = date.getMonth() + 1;
    const y = date.getFullYear();
    return `${d.toString().padStart(2, '0')}/${m.toString().padStart(2, '0')}/${y}`;
  }

  // ---------- HANDLERS ----------
  function handleCountryChange() {
    if (!form.country) {
      states = [];
      return;
    }
    states = State.getStatesOfCountry(form.country);
    cities = [];
    form.state = '';
    form.city = '';
    stateSearch = '';
    citySearch = '';
    form.district = '';
    form.taluka = '';
    talukas = [];
  }

  function handleStateChange() {
    if (!form.country || !form.state) {
      cities = [];
      return;
    }
    cities = City.getCitiesOfState(form.country, form.state);
    form.city = '';
    citySearch = '';

    const stateName = states.find(s => s.isoCode === form.state)?.name;
    // Auto-select Ahmedabad if state is Gujarat and city exists
    if (stateName === 'Gujarat' && cities.length) {
      const ahmedabad = cities.find(c => c.name.toLowerCase() === 'ahmedabad');
      if (ahmedabad) form.city = ahmedabad.name;
    }

    // Reset district/taluka unless Gujarat
    if (stateName !== 'Gujarat') {
      form.district = '';
      form.taluka = '';
      talukas = [];
    } else {
      form.district = '';
      form.taluka = '';
      talukas = [];
    }
  }

  function handleDistrictChange() {
    talukas = districtTaluka[form.district] || [];
    form.taluka = '';
    talukaSearch = '';
  }

  // ---------- VALIDATION & SUBMIT ----------
  let errors = {};

  function validateForm() {
    errors = {};
    if (!form.companyName.trim()) errors.companyName = 'Company name required';
    if (!form.email.trim()) errors.email = 'Email required';
    if (!form.phone) errors.phone = 'Phone number required';

    if (!form.country) errors.country = 'Country required';
    if (!form.state) errors.state = 'State required';
    if (!form.city) errors.city = 'City required';

    // Date validations with dd/mm/yyyy
    const fromDateObj = parseDateDDMMYYYY(form.fromDate);
    const toDateObj = parseDateDDMMYYYY(form.toDate);
    const dueDateObj = parseDateDDMMYYYY(form.dueDate);

    if (form.fromDate && !fromDateObj) errors.fromDate = 'Invalid date (use dd/mm/yyyy)';
    if (form.toDate && !toDateObj) errors.toDate = 'Invalid date (use dd/mm/yyyy)';
    if (form.dueDate && !dueDateObj) errors.dueDate = 'Invalid date (use dd/mm/yyyy)';

    if (fromDateObj && toDateObj && toDateObj < fromDateObj) {
      errors.dateRange = 'To date cannot be before from date';
    }
    if (fromDateObj && dueDateObj && dueDateObj < fromDateObj) {
      errors.dueDateEarly = 'Due date cannot be earlier than from date';
    }

    return Object.keys(errors).length === 0;
  }

  function submitForm() {
    // Sync phone value from intl-tel-input
    if (iti && phoneInput) {
      form.phone = iti.getNumber();
    }
    if (!validateForm()) return;

    // Prepare display dates (already in dd/mm/yyyy)
    const fromDisplay = form.fromDate || '—';
    const toDisplay = form.toDate || '—';
    const dueDisplay = form.dueDate || '—';

    console.log('Form submitted', { ...form, fullAddress });
    alert(`Employer Registered Successfully!\n\nOffice Address: ${fullAddress}\n\nFrom: ${fromDisplay}  To: ${toDisplay}  Due: ${dueDisplay}`);
  }

  // ---------- INTl TEL INPUT ----------
  let phoneInput;
  let iti;

  // ---------- CLICK OUTSIDE ACTION ----------
  function clickOutside(node, callback) {
    const handleClick = (event) => {
      if (!node.contains(event.target)) callback();
    };
    document.addEventListener('click', handleClick, true);
    return {
      destroy() {
        document.removeEventListener('click', handleClick, true);
      },
    };
  }

  // ---------- ON MOUNT ----------
  onMount(() => {
    countries = Country.getAllCountries();
    districts = Object.keys(districtTaluka);

    // Set default country India
    const india = countries.find(c => c.name === 'India');
    if (india) {
      form.country = india.isoCode;
      handleCountryChange(); // loads states of India
    }

    // Initialize intl-tel-input with India as default
    if (phoneInput) {
      iti = intlTelInput(phoneInput, {
        initialCountry: 'in',
        separateDialCode: true,
        preferredCountries: ['in', 'us', 'gb'],
        utilsScript: 'https://cdn.jsdelivr.net/npm/intl-tel-input@18.2.1/build/js/utils.js',
      });
      phoneInput.addEventListener('countrychange', () => {
        form.phone = iti.getNumber();
      });
    }
  });
</script>

<div class="registration-container">
  <div class="form-card">
    <div class="form-header">
      <h1>Employer Registration</h1>
      <p>Fill in the details to register your company office</p>
    </div>

    <form on:submit|preventDefault={submitForm}>
      <!-- COMPANY DETAILS SECTION -->
      <div class="form-section">
        <h2>Company Details</h2>
        <div class="form-grid">
          <div class="input-group">
            <label for="companyName">Company Name <span class="required">*</span></label>
            <input
              id="companyName"
              type="text"
              bind:value={form.companyName}
              class={errors.companyName ? 'error' : ''}
              placeholder="e.g. Acme Inc."
            />
            {#if errors.companyName}
              <span class="error-message">{errors.companyName}</span>
            {/if}
          </div>

          <div class="input-group">
            <label for="email">Email <span class="required">*</span></label>
            <input
              id="email"
              type="email"
              bind:value={form.email}
              class={errors.email ? 'error' : ''}
              placeholder="contact@company.com"
            />
            {#if errors.email}
              <span class="error-message">{errors.email}</span>
            {/if}
          </div>

          <div class="input-group full-width">
            <label for="phone">Phone <span class="required">*</span> (Default India)</label>
            <input
              id="phone"
              type="tel"
              bind:this={phoneInput}
              placeholder="Enter phone number"
              class={errors.phone ? 'error' : ''}
            />
            {#if errors.phone}
              <span class="error-message">{errors.phone}</span>
            {/if}
          </div>
        </div>
      </div>

      <!-- COMPANY OFFICE SECTION -->
      <div class="form-section">
        <h2>Company Office</h2>
        <div class="form-grid">
          <!-- COUNTRY COMBOBOX -->
          <div class="input-group combobox" use:clickOutside={() => (countryOpen = false)}>
            <label for="country">Country <span class="required">*</span></label>
            <button
              type="button"
              class="combobox-button"
              class:error={errors.country}
              on:click={() => {
                countryOpen = !countryOpen;
                if (countryOpen) {
                  countrySearch = '';
                  setTimeout(() => document.querySelector('#country-search')?.focus(), 0);
                }
              }}
            >
              {countries.find(c => c.isoCode === form.country)?.name || 'Select Country'}
            </button>
            {#if countryOpen}
              <div class="combobox-dropdown">
                <input
                  id="country-search"
                  type="text"
                  placeholder="Search country..."
                  bind:value={countrySearch}
                  class="combobox-search"
                />
                <div class="combobox-options">
                  {#each filteredCountries as c}
                    <div
                      class="combobox-option"
                      class:selected={form.country === c.isoCode}
                      on:click={() => {
                        form.country = c.isoCode;
                        countryOpen = false;
                        handleCountryChange();
                      }}
                    >
                      {c.name}
                    </div>
                  {:else}
                    <div class="combobox-empty">No countries found</div>
                  {/each}
                </div>
              </div>
            {/if}
            {#if errors.country}
              <span class="error-message">{errors.country}</span>
            {/if}
          </div>

          <!-- STATE COMBOBOX -->
          <div
            class="input-group combobox"
            class:disabled={!form.country}
            use:clickOutside={() => (stateOpen = false)}
          >
            <label for="state">State <span class="required">*</span></label>
            <button
              type="button"
              class="combobox-button"
              disabled={!form.country}
              class:error={errors.state}
              on:click={() => {
                if (!form.country) return;
                stateOpen = !stateOpen;
                if (stateOpen) {
                  stateSearch = '';
                  setTimeout(() => document.querySelector('#state-search')?.focus(), 0);
                }
              }}
            >
              {states.find(s => s.isoCode === form.state)?.name || 'Select State'}
            </button>
            {#if stateOpen}
              <div class="combobox-dropdown">
                <input
                  id="state-search"
                  type="text"
                  placeholder="Search state..."
                  bind:value={stateSearch}
                  class="combobox-search"
                />
                <div class="combobox-options">
                  {#each filteredStates as s}
                    <div
                      class="combobox-option"
                      class:selected={form.state === s.isoCode}
                      on:click={() => {
                        form.state = s.isoCode;
                        stateOpen = false;
                        handleStateChange();
                      }}
                    >
                      {s.name}
                    </div>
                  {:else}
                    <div class="combobox-empty">No states found</div>
                  {/each}
                </div>
              </div>
            {/if}
            {#if errors.state}
              <span class="error-message">{errors.state}</span>
            {/if}
          </div>

          <!-- CITY COMBOBOX -->
          <div
            class="input-group combobox"
            class:disabled={!form.state}
            use:clickOutside={() => (cityOpen = false)}
          >
            <label for="city">City <span class="required">*</span></label>
            <button
              type="button"
              class="combobox-button"
              disabled={!form.state}
              class:error={errors.city}
              on:click={() => {
                if (!form.state) return;
                cityOpen = !cityOpen;
                if (cityOpen) {
                  citySearch = '';
                  setTimeout(() => document.querySelector('#city-search')?.focus(), 0);
                }
              }}
            >
              {form.city || 'Select City'}
            </button>
            {#if cityOpen}
              <div class="combobox-dropdown">
                <input
                  id="city-search"
                  type="text"
                  placeholder="Search city..."
                  bind:value={citySearch}
                  class="combobox-search"
                />
                <div class="combobox-options">
                  {#each filteredCities as c}
                    <div
                      class="combobox-option"
                      class:selected={form.city === c.name}
                      on:click={() => {
                        form.city = c.name;
                        cityOpen = false;
                      }}
                    >
                      {c.name}
                    </div>
                  {:else}
                    <div class="combobox-empty">No cities found</div>
                  {/each}
                </div>
              </div>
            {/if}
            {#if errors.city}
              <span class="error-message">{errors.city}</span>
            {/if}
          </div>

          <div class="input-group">
            <label for="zipcode">Zip Code</label>
            <input id="zipcode" type="text" bind:value={form.zipcode} placeholder="e.g. 380001" />
          </div>
        </div>

        <!-- DISTRICT & TALUKA (only for Gujarat) -->
        {#if isGujaratSelected()}
          <div class="form-grid">
            <div class="input-group combobox" use:clickOutside={() => (districtOpen = false)}>
              <label for="district">District</label>
              <button
                type="button"
                class="combobox-button"
                on:click={() => {
                  districtOpen = !districtOpen;
                  if (districtOpen) {
                    districtSearch = '';
                    setTimeout(() => document.querySelector('#district-search')?.focus(), 0);
                  }
                }}
              >
                {form.district || 'Select District'}
              </button>
              {#if districtOpen}
                <div class="combobox-dropdown">
                  <input
                    id="district-search"
                    type="text"
                    placeholder="Search district..."
                    bind:value={districtSearch}
                    class="combobox-search"
                  />
                  <div class="combobox-options">
                    {#each filteredDistricts as d}
                      <div
                        class="combobox-option"
                        class:selected={form.district === d}
                        on:click={() => {
                          form.district = d;
                          districtOpen = false;
                          handleDistrictChange();
                        }}
                      >
                        {d}
                      </div>
                    {:else}
                      <div class="combobox-empty">No districts found</div>
                    {/each}
                  </div>
                </div>
              {/if}
            </div>

            <div
              class="input-group combobox"
              class:disabled={!form.district}
              use:clickOutside={() => (talukaOpen = false)}
            >
              <label for="taluka">Taluka</label>
              <button
                type="button"
                class="combobox-button"
                disabled={!form.district}
                on:click={() => {
                  if (!form.district) return;
                  talukaOpen = !talukaOpen;
                  if (talukaOpen) {
                    talukaSearch = '';
                    setTimeout(() => document.querySelector('#taluka-search')?.focus(), 0);
                  }
                }}
              >
                {form.taluka || 'Select Taluka'}
              </button>
              {#if talukaOpen}
                <div class="combobox-dropdown">
                  <input
                    id="taluka-search"
                    type="text"
                    placeholder="Search taluka..."
                    bind:value={talukaSearch}
                    class="combobox-search"
                  />
                  <div class="combobox-options">
                    {#each filteredTalukas as t}
                      <div
                        class="combobox-option"
                        class:selected={form.taluka === t}
                        on:click={() => {
                          form.taluka = t;
                          talukaOpen = false;
                        }}
                      >
                        {t}
                      </div>
                    {:else}
                      <div class="combobox-empty">No talukas found</div>
                    {/each}
                  </div>
                </div>
              {/if}
            </div>
          </div>
        {/if}

        <!-- STREET ADDRESS -->
        <div class="input-group full-width">
          <label for="address">Street Address</label>
          <textarea id="address" rows="2" bind:value={form.address} placeholder="Building, street, etc."></textarea>
        </div>

        <!-- ADDRESS PREVIEW -->
        <div class="input-group full-width">
          <label>Generated Address</label>
          <div class="address-preview">{fullAddress || '—'}</div>
        </div>

        <!-- ADDITIONAL DETAILS -->
        <div class="input-group full-width">
          <label for="additionalDetails">Additional Details</label>
          <textarea
            id="additionalDetails"
            rows="2"
            bind:value={form.additionalDetails}
            placeholder="Any extra information (optional)"
          ></textarea>
        </div>
      </div>

      <!-- DATE SECTION with dd/mm/yyyy -->
     <div class="form-grid">

  <!-- FROM DATE -->
  <div class="input-group date-field">
    <label>From Date</label>

    <div class="date-wrapper">
      <input
        type="text"
        readonly
        value={form.fromDate}
        placeholder="dd/mm/yyyy"
      />

      <span
        class="calendar-icon"
        on:click={() => document.getElementById('fromPicker').showPicker()}
      >
        📅
      </span>

      <input
        id="fromPicker"
        type="date"
        class="hidden-date"
        on:change={(e) => {
          form.fromDate = formatDateDDMMYYYY(new Date(e.target.value));

          // OPTIONAL: auto-set min for To Date
          document.getElementById('toPicker').min = e.target.value;
        }}
      />
    </div>
  </div>

  <!-- TO / DUE DATE -->
  <div class="input-group date-field">
    <label>Due Date</label>

    <div class="date-wrapper">
      <input
        type="text"
        readonly
        value={form.toDate}
        placeholder="dd/mm/yyyy"
      />

      <span
        class="calendar-icon"
        on:click={() => document.getElementById('toPicker').showPicker()}
      >
        📅
      </span>

      <input
        id="toPicker"
        type="date"
        class="hidden-date"
        on:change={(e) =>
          form.toDate = formatDateDDMMYYYY(new Date(e.target.value))
        }
      />
    </div>
  </div>

</div>
      <button type="submit" class="submit-button">Register Employer</button>
    </form>
  </div>
</div>

<style>
  /* Same as before – no changes needed */
  * {
    box-sizing: border-box;
    margin: 0;
  }

  .registration-container {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #f8fafc;
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    padding: 2rem 1rem;
  }

  .form-card {
    background: white;
    border-radius: 24px;
    box-shadow: 0 20px 40px -10px rgba(0, 20, 30, 0.15), 0 1px 3px rgba(0, 0, 0, 0.05);
    width: 100%;
    max-width: 1000px;
    padding: 2.5rem;
    transition: box-shadow 0.2s ease;
  }

  .form-header {
    margin-bottom: 2rem;
    border-bottom: 1px solid #eef2f6;
    padding-bottom: 1.5rem;
  }

  .form-header h1 {
    font-size: 2rem;
    font-weight: 600;
    letter-spacing: -0.02em;
    color: #0a1e2f;
    margin: 0 0 0.25rem 0;
  }

  .form-header p {
    color: #5e6f88;
    font-size: 0.95rem;
    margin: 0;
  }

  .form-section {
    margin-bottom: 2.5rem;
  }

  .form-section h2 {
    font-size: 1.25rem;
    font-weight: 500;
    color: #1e2f44;
    margin: 0 0 1.25rem 0;
    padding-bottom: 0.5rem;
    border-bottom: 1px dashed #e2e8f0;
  }

  .form-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 1.5rem;
    margin-bottom: 1.5rem;
  }

  .input-group {
    display: flex;
    flex-direction: column;
    gap: 0.35rem;
  }

  .input-group.full-width {
    grid-column: 1 / -1;
  }

  .input-group.disabled {
    opacity: 0.6;
  }

  label {
    font-size: 0.9rem;
    font-weight: 500;
    color: #2d3f59;
    letter-spacing: 0.01em;
  }

  .required {
    color: #d14545;
    margin-left: 2px;
  }

  input,
  select,
  textarea,
  .combobox-button {
    width: 100%;
    padding: 0.75rem 1rem;
    font-size: 0.95rem;
    font-family: inherit;
    border: 1.5px solid #e0e7ed;
    border-radius: 12px;
    background: white;
    transition: all 0.15s ease;
    color: #1a2b3e;
  }

  .combobox-button {
    text-align: left;
    cursor: pointer;
    background: white;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .combobox-button::after {
    content: '▼';
    font-size: 0.7rem;
    color: #7e8b9c;
    margin-left: 8px;
  }

  .combobox-button:disabled {
    background-color: #f4f7fb;
    cursor: not-allowed;
    opacity: 0.7;
  }

  .combobox-button.error {
    border-color: #d14545;
    background-color: #fff8f8;
  }

  .combobox {
    position: relative;
  }

  .combobox-dropdown {
    position: absolute;
    top: calc(100% + 5px);
    left: 0;
    right: 0;
    background: white;
    border: 1.5px solid #e0e7ed;
    border-radius: 12px;
    box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
    z-index: 10;
    overflow: hidden;
  }

  .combobox-search {
    width: 100%;
    padding: 0.75rem 1rem;
    border: none;
    border-bottom: 1px solid #eef2f6;
    outline: none;
    font-size: 0.95rem;
  }

  .combobox-search:focus {
    box-shadow: inset 0 -2px 0 #0066cc;
  }

  .combobox-options {
    max-height: 200px;
    overflow-y: auto;
  }

  .combobox-option {
    padding: 0.75rem 1rem;
    cursor: pointer;
    transition: background 0.1s;
  }

  .combobox-option:hover {
    background: #f0f4fa;
  }

  .combobox-option.selected {
    background: #e6f0ff;
    font-weight: 500;
  }

  .combobox-empty {
    padding: 0.75rem 1rem;
    color: #7e8b9c;
    text-align: center;
  }

  .error-message {
    font-size: 0.8rem;
    color: #d14545;
    margin-top: 0.2rem;
  }

  .address-preview {
    padding: 1rem 1.25rem;
    background-color: #f8fafd;
    border-radius: 14px;
    border: 1px solid #e4eaf1;
    font-size: 0.95rem;
    line-height: 1.5;
    color: #1a2b3e;
    word-break: break-word;
  }

  .submit-button {
    width: 100%;
    padding: 1rem;
    background: linear-gradient(135deg, #6b0f9c 0%, #b91372 40%, #b91372 100%);
    color: white;
    border: none;
    border-radius: 40px;
    font-size: 1.1rem;
    font-weight: 600;
    letter-spacing: 0.4px;
    cursor: pointer;
    transition: all 0.35s ease;
    box-shadow: 0 10px 20px -10px rgba(185, 19, 114, 0.6);
    position: relative;
    overflow: hidden;
    margin-top: 1rem;
  }

  .submit-button:hover {
    transform: translateY(-3px) scale(1.01);
    background: linear-gradient(135deg, #7c18b5 0%, #d81b80 40%, #d81b80 100%);
    box-shadow: 0 18px 30px -12px rgba(185, 19, 114, 0.8);
  }

  .submit-button:active {
    transform: scale(0.97);
  }

  .submit-button::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(120deg, transparent, rgba(255, 255, 255, 0.4), transparent);
    transition: 0.6s;
  }

  .submit-button:hover::before {
    left: 100%;
  }

  .date-wrapper {
  position: relative;
}

.date-wrapper input[type="text"] {
  padding-right: 40px;
}

.calendar-icon {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  cursor: pointer;
  font-size: 18px;
}

.calendar-icon:hover {
  transform: translateY(-50%) scale(1.1);
}

.hidden-date {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

  /* intl-tel-input custom integration */
  :global(.iti) {
    width: 100%;
  }

  :global(.iti__flag-container) {
    border-radius: 12px 0 0 12px;
  }

  :global(.iti--separate-dial-code .iti__selected-flag) {
    background-color: #f4f7fb;
    border-radius: 12px 0 0 12px;
    border-right: 1px solid #e0e7ed;
  }

  :global(.iti__country-list) {
    border-radius: 12px;
    border: 1px solid #e2e8f0;
    box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
  }

  /* RESPONSIVE */
  @media (max-width: 768px) {
    .form-card {
      padding: 2rem;
    }
    .form-header h1 {
      font-size: 1.75rem;
    }
    .form-grid {
      gap: 1.25rem;
    }
  }

  @media (max-width: 640px) {
    .registration-container {
      padding: 1rem;
    }
    .form-card {
      padding: 1.5rem;
    }
    .form-header h1 {
      font-size: 1.5rem;
    }
    .form-grid {
      grid-template-columns: 1fr;
      gap: 1rem;
    }
    .combobox-dropdown {
      max-height: 250px;
    }
    .submit-button {
      padding: 0.85rem;
      font-size: 1rem;
    }
  }

  @media (max-width: 480px) {
    .form-card {
      padding: 1.25rem;
    }
    .form-header h1 {
      font-size: 1.35rem;
    }
  }
</style>