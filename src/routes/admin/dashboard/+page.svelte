<script>
  // Sample hiring requests with submission timestamps
  let requests = [
    {
      id: 1,
      company: "Acme Corp",
      contact: "John Doe",
      jobTitle: "Senior Software Engineer",
      department: "Engineering",
      type: "Full-time",
      positions: 2,
      status: "Pending",
      submittedAt: "2025-03-09T09:15:00Z",
    },
    {
      id: 2,
      company: "TechNova",
      contact: "Alice Smith",
      jobTitle: "UI Designer",
      department: "Design",
      type: "Contract",
      positions: 1,
      status: "Pending",
      submittedAt: "2025-03-11T08:30:00Z",
    },
    {
      id: 3,
      company: "InnovateLabs",
      contact: "Bob Johnson",
      jobTitle: "Product Manager",
      department: "Product",
      type: "Full-time",
      positions: 1,
      status: "Approved",
      submittedAt: "2025-03-10T14:20:00Z",
    },
    {
      id: 4,
      company: "Cloudify",
      contact: "Eva Green",
      jobTitle: "DevOps Engineer",
      department: "Engineering",
      type: "Full-time",
      positions: 2,
      status: "Pending",
      submittedAt: "2025-03-11T10:05:00Z",
    },
    {
      id: 5,
      company: "DesignStudio",
      contact: "Mark Tan",
      jobTitle: "UX Researcher",
      department: "Design",
      type: "Contract",
      positions: 1,
      status: "Rejected",
      submittedAt: "2025-03-08T16:20:00Z",
    },
  ];

  // --- Pagination state ---
  let currentPage = 1;
  let pageSize = 3; // can be 2, 3, 5

  // Computed: total pages and paginated requests
  $: totalPages = Math.ceil(requests.length / pageSize);
  $: paginatedRequests = requests.slice(
    (currentPage - 1) * pageSize,
    currentPage * pageSize
  );

  // Adjust current page if it goes out of bounds (e.g., after delete)
  $: if (currentPage > totalPages && totalPages > 0) {
    currentPage = totalPages;
  } else if (totalPages === 0) {
    currentPage = 1;
  }

  // --- Notification bell state ---
  let dropdownOpen = false;
  let lastViewed = null;

  function timeAgo(dateString) {
    const date = new Date(dateString);
    const now = new Date();
    const seconds = Math.floor((now - date) / 1000);
    const minutes = Math.floor(seconds / 60);
    const hours = Math.floor(minutes / 60);
    const days = Math.floor(hours / 24);

    if (days > 0) return `${days} day${days > 1 ? "s" : ""} ago`;
    if (hours > 0) return `${hours} hour${hours > 1 ? "s" : ""} ago`;
    if (minutes > 0) return `${minutes} minute${minutes > 1 ? "s" : ""} ago`;
    return "just now";
  }

  $: pendingRequests = requests
    .filter((r) => r.status === "Pending")
    .sort((a, b) => new Date(b.submittedAt) - new Date(a.submittedAt));

  $: unreadCount = lastViewed
    ? pendingRequests.filter((r) => new Date(r.submittedAt) > lastViewed).length
    : pendingRequests.length;

  function toggleDropdown() {
    dropdownOpen = !dropdownOpen;
    if (dropdownOpen) {
      lastViewed = Date.now();
    }
  }

  // --- Request actions ---
  function approveRequest(id) {
    requests = requests.map((r) =>
      r.id === id ? { ...r, status: "Approved" } : r
    );
  }

  function rejectRequest(id) {
    requests = requests.map((r) =>
      r.id === id ? { ...r, status: "Rejected" } : r
    );
  }

  function editRequest(req) {
    alert(`Edit feature coming soon for: ${req.jobTitle}`);
  }

  // Simulate a new incoming request (for demo)
  function addMockRequest() {
    const newId = Math.max(...requests.map((r) => r.id)) + 1;
    const newRequest = {
      id: newId,
      company: `Startup ${newId}`,
      contact: "New Contact",
      jobTitle: "New Position",
      department: "Engineering",
      type: "Full-time",
      positions: 1,
      status: "Pending",
      submittedAt: new Date().toISOString(),
    };
    requests = [...requests, newRequest];
    // Optionally go to last page to see the new request
    currentPage = totalPages; // will auto-recalc
  }
</script>

<section class="dashboard">
  <!-- Header with title, pending summary, and notification bell -->
  <div class="dashboard-header">
    <div class="title-section">
      <h1>Admin Dashboard</h1>
      <p class="subtitle">Manage hiring requests from companies</p>
    </div>

    <div class="header-actions">
      <div class="pending-summary">
        <span class="pending-count">{pendingRequests.length}</span>
        <span>pending</span>
      </div>

      <div class="bell-wrapper">
        <button class="bell-icon" on:click={toggleDropdown}>
          🔔
          {#if unreadCount > 0}
            <span class="blue-dot"></span>
          {/if}
        </button>

        {#if dropdownOpen}
          <div class="notification-dropdown">
            <div class="dropdown-header">
              <strong>Notifications</strong>
              <span class="new-badge">{unreadCount} new</span>
            </div>
            <ul class="dropdown-list">
              {#if pendingRequests.length === 0}
                <li class="empty-item">✨ No pending requests</li>
              {:else}
                {#each pendingRequests as notif}
                  <li class="notification-item">
                    <div class="notif-company">{notif.company}</div>
                    <div class="notif-details">
                      {notif.jobTitle} · {notif.department}
                    </div>
                    <div class="notif-time">{timeAgo(notif.submittedAt)}</div>
                  </li>
                {/each}
              {/if}
            </ul>
            <div class="dropdown-footer">
              <button class="simulate-btn" on:click={addMockRequest}>
                + Simulate new request
              </button>
            </div>
          </div>
        {/if}
      </div>
    </div>
  </div>

  <!-- Main table with pagination -->
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>Company</th>
          <th>Contact</th>
          <th>Job Title</th>
          <th>Department</th>
          <th>Type</th>
          <th>Positions</th>
          <th>Status</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        {#each paginatedRequests as req}
          <tr>
            <td>{req.company}</td>
            <td>{req.contact}</td>
            <td>{req.jobTitle}</td>
            <td>{req.department}</td>
            <td>{req.type}</td>
            <td>{req.positions}</td>
            <td>
              <span class="status {req.status.toLowerCase()}">
                {req.status}
              </span>
            </td>
            <td class="actions">
              <button class="edit" on:click={() => editRequest(req)}>
                Edit
              </button>
              <button class="approve" on:click={() => approveRequest(req.id)}>
                Approve
              </button>
              <button class="reject" on:click={() => rejectRequest(req.id)}>
                Reject
              </button>
            </td>
          </tr>
        {/each}
      </tbody>
    </table>

    <!-- Pagination controls -->
    <div class="pagination">
      <div class="rows-per-page">
        <label for="pageSize">Rows per page:</label>
        <select
          id="pageSize"
          bind:value={pageSize}
          on:change={() => (currentPage = 1)}
        >
          <option value={2}>2</option>
          <option value={3}>3</option>
          <option value={5}>5</option>
        </select>
      </div>

      <div class="page-controls">
        <button
          class="page-btn"
          on:click={() => (currentPage = currentPage - 1)}
          disabled={currentPage === 1}
        >
          ‹ Prev
        </button>
        <span class="page-info">
          Page {currentPage} of {totalPages || 1}
        </span>
        <button
          class="page-btn"
          on:click={() => (currentPage = currentPage + 1)}
          disabled={currentPage === totalPages || totalPages === 0}
        >
          Next ›
        </button>
      </div>
    </div>
  </div>

  <!-- Optional inline simulate button -->
  <div class="simulate-row">
    <button class="simulate-btn" on:click={addMockRequest}>
      + Add mock request (tests blue dot & pagination)
    </button>
  </div>
</section>

<style>
  /* Professional soft color palette, elegant spacing */
  .dashboard {
    padding: 2rem 2.5rem;
    background: #f8fafd;
    min-height: 100vh;
    font-family:
      system-ui,
      -apple-system,
      'Segoe UI',
      Roboto,
      'Helvetica Neue',
      sans-serif;
  }

  .dashboard-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 2rem;
    flex-wrap: wrap;
    gap: 1rem;
  }

  .title-section h1 {
    font-size: 2rem;
    font-weight: 500;
    color: #1e293b;
    margin: 0 0 0.25rem 0;
    letter-spacing: -0.02em;
  }

  .subtitle {
    color: #64748b;
    font-size: 1rem;
    margin: 0;
    font-weight: 400;
  }

  .header-actions {
    display: flex;
    align-items: center;
    gap: 1.5rem;
  }

  .pending-summary {
    background: #e8edf5;
    padding: 0.5rem 1.2rem;
    border-radius: 2rem;
    font-size: 0.95rem;
    color: #2c3e50;
    display: flex;
    align-items: center;
    gap: 0.4rem;
    border: 1px solid #d0dbe8;
  }
  .pending-count {
    font-weight: 700;
    color: #0a2942;
    background: white;
    padding: 0.1rem 0.6rem;
    border-radius: 1rem;
    font-size: 0.9rem;
  }

  .bell-wrapper {
    position: relative;
  }

  .bell-icon {
    background: white;
    border: 1px solid #e2e8f0;
    font-size: 1.6rem;
    padding: 0.5rem 0.7rem;
    border-radius: 3rem;
    cursor: pointer;
    line-height: 1;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.02);
    transition: all 0.15s;
    position: relative;
  }
  .bell-icon:hover {
    background: #f1f5f9;
    border-color: #cbd5e1;
  }

  .blue-dot {
    position: absolute;
    top: 0.5rem;
    right: 0.6rem;
    width: 0.75rem;
    height: 0.75rem;
    background: #3b82f6;
    border-radius: 50%;
    border: 2px solid white;
  }

  .notification-dropdown {
    position: absolute;
    top: calc(100% + 0.75rem);
    right: 0;
    width: 360px;
    background: white;
    border-radius: 1.25rem;
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.05), 0 5px 15px rgba(0, 0, 0, 0.03);
    border: 1px solid #edf2f7;
    overflow: hidden;
    z-index: 50;
  }

  .dropdown-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 1.25rem;
    background: #fafcff;
    border-bottom: 1px solid #edf2f7;
    font-size: 0.95rem;
  }
  .dropdown-header strong {
    color: #1e293b;
  }
  .new-badge {
    background: #e6edf8;
    color: #2563eb;
    padding: 0.2rem 0.7rem;
    border-radius: 2rem;
    font-size: 0.8rem;
    font-weight: 500;
  }

  .dropdown-list {
    list-style: none;
    margin: 0;
    padding: 0;
    max-height: 320px;
    overflow-y: auto;
  }

  .notification-item {
    padding: 1rem 1.25rem;
    border-bottom: 1px solid #f0f4fa;
    transition: background 0.1s;
  }
  .notification-item:hover {
    background: #fafcff;
  }
  .notif-company {
    font-weight: 600;
    color: #0f172a;
    margin-bottom: 0.2rem;
  }
  .notif-details {
    font-size: 0.9rem;
    color: #475569;
    margin-bottom: 0.25rem;
  }
  .notif-time {
    font-size: 0.8rem;
    color: #7c8ba0;
    background: #f0f4fa;
    display: inline-block;
    padding: 0.2rem 0.8rem;
    border-radius: 2rem;
  }

  .empty-item {
    padding: 2rem;
    text-align: center;
    color: #7c8ba0;
    font-style: italic;
  }

  .dropdown-footer {
    padding: 0.75rem 1rem;
    background: #f9fbfd;
    border-top: 1px solid #edf2f7;
    text-align: center;
  }

  .simulate-btn {
    background: #eef2f6;
    border: 1px solid #dce3ec;
    color: #2d3c53;
    font-size: 0.85rem;
    padding: 0.4rem 1rem;
    border-radius: 2rem;
    cursor: pointer;
    font-weight: 500;
    transition: all 0.15s;
  }
  .simulate-btn:hover {
    background: #e2e8f0;
    border-color: #b9c4d2;
  }
  .simulate-row {
    margin-top: 1.5rem;
    display: flex;
    justify-content: flex-end;
  }
  .simulate-row .simulate-btn {
    background: white;
  }

  /* Table container with both scrolls */
  .table-container {
    background: #ffffff;
    border-radius: 1.25rem;
    padding: 1.5rem;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.02);
    border: 1px solid #edf2f7;
    overflow-x: auto;
    max-height: 60vh;        /* vertical scroll when content exceeds */
    overflow-y: auto;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.95rem;
    color: #1e293b;
  }

  th {
    text-align: left;
    padding: 0.9rem 1rem;
    background: #f9fbfd;
    color: #475569;
    font-weight: 500;
    font-size: 0.9rem;
    letter-spacing: 0.02em;
    border-bottom: 1px solid #e9eef3;
    position: sticky;
    top: 0;
    background: #f9fbfd;
    z-index: 10;
  }

  td {
    padding: 1rem;
    border-bottom: 1px solid #f0f4f9;
    vertical-align: middle;
  }

  tr:hover td {
    background-color: #fafcff;
  }

  .status {
    display: inline-block;
    padding: 0.25rem 0.85rem;
    border-radius: 2rem;
    font-size: 0.85rem;
    font-weight: 500;
    min-width: 80px;
    text-align: center;
  }
  .status.pending {
    background: #fef9e7;
    color: #b6560b;
    border: 1px solid #ffedc0;
  }
  .status.approved {
    background: #e3f7ec;
    color: #0a724a;
    border: 1px solid #b9e6cd;
  }
  .status.rejected {
    background: #fee9e7;
    color: #b33a3a;
    border: 1px solid #fbc5c2;
  }

  .actions {
    display: flex;
    gap: 0.6rem;
    flex-wrap: wrap;
  }

  button {
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 2rem;
    font-size: 0.85rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.15s;
    border: 1px solid transparent;
  }

  .edit {
    background: #f1f4f9;
    color: #334155;
    border-color: #dde3ea;
  }
  .edit:hover {
    background: #e6eaef;
    border-color: #cbd5e1;
  }

  .approve {
    background: #dff0e6;
    color: #1f7042;
    border-color: #b9e0cb;
  }
  .approve:hover {
    background: #d0e9db;
    border-color: #96cfb2;
  }

  .reject {
    background: #fce9e8;
    color: #b34141;
    border-color: #f5c0bc;
  }
  .reject:hover {
    background: #fadcd9;
    border-color: #f0a8a2;
  }

  /* Pagination styling */
  .pagination {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1.5rem 0.5rem 0.5rem 0.5rem;
    flex-wrap: wrap;
    gap: 1rem;
    background: white;
    border-top: 1px solid #edf2f7;
    margin-top: 1rem;
  }

  .rows-per-page {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.9rem;
    color: #475569;
  }

  .rows-per-page select {
    padding: 0.3rem 1.5rem 0.3rem 0.8rem;
    border-radius: 2rem;
    border: 1px solid #d0dbe8;
    background: white;
    font-size: 0.9rem;
    color: #1e293b;
    cursor: pointer;
    appearance: none;
    background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='16' height='16' viewBox='0 0 24 24' fill='none' stroke='%23475569' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'><polyline points='6 9 12 15 18 9'/></svg>");
    background-repeat: no-repeat;
    background-position: right 0.5rem center;
    background-size: 1rem;
  }

  .page-controls {
    display: flex;
    align-items: center;
    gap: 0.8rem;
  }

  .page-btn {
    background: #f1f4f9;
    border: 1px solid #dde3ea;
    color: #2d3c53;
    padding: 0.4rem 1rem;
    border-radius: 2rem;
    font-size: 0.9rem;
  }
  .page-btn:disabled {
    opacity: 0.4;
    cursor: not-allowed;
  }
  .page-info {
    font-size: 0.95rem;
    color: #475569;
  }

  @media (max-width: 700px) {
    .dashboard {
      padding: 1.5rem;
    }
    .dashboard-header {
      flex-direction: column;
      align-items: stretch;
    }
    .header-actions {
      justify-content: flex-end;
    }
    .notification-dropdown {
      width: 300px;
      right: -20px;
    }
    .pagination {
      flex-direction: column;
      align-items: flex-start;
    }
  }
</style>