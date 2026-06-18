# Client Lead Management CRM System (Dark Mode Edition)

A clean, minimal, and fully functional CRM web application built with **Python Flask** and **JSON-based storage**, styled with a sleek **dark mode theme**. Designed for business owners or admins to manage incoming leads from a website contact form through a secure admin panel.

---

## 1. Project Title
**Client Lead Management CRM System**

---

## 2. Aim
To build a lightweight, easy-to-deploy CRM system that allows a business admin to capture, track, update, and manage client leads without needing a database server.

---

## 3. Abstract
This project is a full-stack web application that simulates a real-world Client Relationship Management (CRM) tool. Leads are captured via a public contact form and stored as JSON. An admin panel provides a dashboard to view all leads, search and filter them, update their pipeline status, add follow-up notes, and view analytics charts — all secured behind session-based authentication.

---

## 4. Introduction
Many small businesses struggle with tracking leads that come in through their website. This CRM system solves that by providing:
- A public-facing contact form (simulating a website enquiry form)
- A secure admin panel to manage and track those leads
- A simple pipeline with actionable statuses
- Analytics to understand lead sources and conversion

---

## 5. Problem Statement
Small business owners and solo admins often lose track of incoming leads, rely on spreadsheets, or don't have the budget for enterprise CRM tools. This system provides a free, self-hosted, zero-database solution.

---

## 6. Objectives
- Capture leads through a public contact form
- Store leads in JSON without a database
- Provide a secure admin login
- Allow status updates across a 4-stage pipeline
- Allow adding timestamped follow-up notes per lead
- Provide search & filter functionality
- Display visual analytics using Chart.js

---

## 7. Technologies Used

| Layer        | Technology                    |
|--------------|-------------------------------|
| Backend      | Python 3.10+, Flask 3.x       |
| Frontend     | HTML5, CSS3, JavaScript (ES6) |
| Storage      | JSON files (no database)      |
| Auth         | Flask sessions + Werkzeug     |
| Charts       | Chart.js 4.x                  |
| Icons        | Font Awesome 6                |
| Fonts        | Google Fonts — Inter          |

---

## 8. Features

### Public
- Contact form with: Name, Email, Phone, Company, Source, Message
- Client-side and server-side validation
- Submission success confirmation

### Admin Panel
- Secure login with hashed passwords (Werkzeug)
- Dashboard with 5 summary stat cards
- Full leads table with colour-coded status badges
- Real-time search (name, email, company) and status filter
- Lead detail view with all contact info + message
- Inline status update (AJAX — no page reload)
- Follow-up notes timeline per lead (AJAX — no page reload)
- Lead deletion with confirmation modal
- Analytics page: Bar chart, Doughnut chart, Line chart
- Conversion rate calculation
- Responsive layout (desktop, tablet, mobile)
- Animated number counters on stats
- Toast notifications for all actions

---

## 9. Folder Structure

```
crm/
├── app.py                    # Flask application & all routes
├── requirements.txt          # Python dependencies
├── README.md                 # This file
│
├── data/
│   ├── leads.json            # All lead records
│   ├── users.json            # Admin credentials (hashed)
│   └── notes.json            # Follow-up notes per lead
│
├── templates/
│   ├── index.html            # Public contact form
│   ├── login.html            # Admin login page
│   ├── dashboard.html        # Main leads dashboard
│   ├── lead_detail.html      # Individual lead view + notes
│   └── analytics.html        # Charts & statistics
│
└── static/
    ├── css/
    │   └── style.css         # All custom CSS
    └── js/
        └── script.js         # All custom JavaScript
```

---

## 10. Data Structures Used

### `leads.json` — Array of lead objects
```json
{
  "id":         "lead_abc12345",
  "name":       "Rahul Sharma",
  "email":      "rahul@example.com",
  "phone":      "+91 98765 43210",
  "company":    "TechSolutions Pvt Ltd",
  "message":    "Interested in your services.",
  "source":     "Website",
  "status":     "Contacted",
  "created_at": "2025-06-01T09:30:00"
}
```

### `notes.json` — Array of note objects
```json
{
  "id":        "note_xyz789",
  "lead_id":   "lead_abc12345",
  "note":      "Called the client. Scheduled demo.",
  "timestamp": "2025-06-02T10:00:00"
}
```

### `users.json` — Array of admin user objects
```json
{
  "username": "admin",
  "password": "<werkzeug_hashed_password>",
  "name":     "Admin User",
  "email":    "admin@clientlead.com"
}
```

**Data structures used internally:**
- Python `list` — stores all leads/notes/users from JSON
- Python `dict` — individual record objects
- Python `defaultdict` — grouping leads by day for timeline chart
- String manipulation — date slicing for chart labels
- List comprehensions — filtering and searching leads

---

## 11. System Architecture

```
Browser (User)
     │
     ▼
Public Contact Form (index.html)
     │  POST /submit-lead
     ▼
Flask Backend (app.py)
     │  Writes to leads.json
     ▼
JSON File Storage (data/)

Browser (Admin)
     │
     ▼
Login Page (login.html)
     │  POST /login → Session set
     ▼
Dashboard (dashboard.html) ◄── GET /api/leads (AJAX search)
     │
     ├─► Lead Detail (lead_detail.html)
     │       ├── POST /lead/<id>/update-status (AJAX)
     │       └── POST /lead/<id>/add-note (AJAX)
     │
     └─► Analytics (analytics.html)
```

---

## 12. Working Flow

1. A website visitor fills in the public contact form
2. Flask saves the lead to `leads.json` with status "Contacted"
3. Admin logs into the secure panel at `/login`
4. Dashboard shows all leads with stats and colour-coded statuses
5. Admin searches/filters leads by name, email, or status
6. Admin clicks "View" to open the lead detail page
7. Admin updates the pipeline status (Contacted → Qualified, etc.)
8. Admin adds timestamped follow-up notes
9. Analytics page shows charts of lead distribution and trends

---

## 13. Algorithm Explanation

### Authentication
- On first run, `init_admin()` hashes the default password using Werkzeug's `pbkdf2:sha256`
- Login compares submitted password to hash using `check_password_hash()`
- Successful login sets `session["admin"]`; all admin routes check this session key

### Lead Filtering (client-side)
- JavaScript reads all `<tr class="lead-row">` elements from the rendered table
- On each keypress, it matches row text against the search query string
- Status filter matches the `data-status` attribute of each row
- Rows not matching either condition are hidden with `display:none`

### Analytics Grouping
- Python reads all leads and groups by `created_at[:10]` (YYYY-MM-DD)
- Uses `defaultdict(int)` to count leads per day
- Sorts the day keys and takes the last 14 for the timeline chart
- Conversion rate = (Closed/Won count / total) × 100

---

## 14. Security Features

- Passwords hashed with `werkzeug.security.generate_password_hash` (pbkdf2:sha256)
- Session-based auth using Flask's signed cookie sessions
- All admin routes decorated with `@login_required`
- Server-side input validation before writing to JSON
- HTML auto-escaping via Jinja2 prevents XSS
- Delete actions require POST method (not GET)
- CSRF not implemented (add Flask-WTF for production use)

---

## 15. Advantages

- Zero external database dependencies — works anywhere Python runs
- Single-file backend — easy to understand and extend
- Fast to deploy — `pip install` + `python app.py`
- Clean, professional UI suitable for demos and submissions
- AJAX updates avoid full page reloads for a smoother UX

---

## 16. Limitations

- JSON files are not suitable for high-concurrency production use
- No CSRF protection (suitable for academic/demo use)
- No multi-admin support (single admin account)
- No email notifications for new leads
- No pagination on large lead datasets

---

## 17. Future Scope

- Replace JSON with SQLite or PostgreSQL for scalability
- Add email notifications when new leads arrive
- Add CSV export for all leads
- Add multi-user admin support with role management
- Add a Kanban board view for the pipeline
- Add lead assignment and ownership tracking
- Integrate with email/WhatsApp APIs for follow-ups
- Add two-factor authentication

---

## 18. Conclusion

This CRM system demonstrates a complete, working full-stack web application using Flask without a traditional database. It covers all core CRM functions — lead capture, tracking, status management, notes, and analytics — in a clean, professional interface suitable for real business use and academic mini-project submission.

---

## 19. How to Run the Project

### Prerequisites
- Python 3.9 or higher
- pip

### Steps

```bash
# 1. Clone or download the project
cd crm

# 2. (Optional) Create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the application
python app.py

# 5. Open in browser
#    Public contact form : http://127.0.0.1:5000/
#    Admin login         : http://127.0.0.1:5000/login
#
#    Default credentials:
#      Username : admin
#      Password : admin123
```

> **Note:** On first run, `init_admin()` automatically hashes the admin password in `users.json`. You do not need to do this manually.
