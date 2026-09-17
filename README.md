# Student Skill & Project Management System

A full-stack CRUD application that keeps one register of students, the skills
they have and the project each of them is building. Staff can add, search,
edit and delete records, and the dashboard turns the register into live
statistics.

- Frontend: React 18 + Vite (plain JavaScript, plain CSS, no UI library)
- Backend: Django 5 + Django REST Framework
- Database: SQLite (created automatically, no server to install)
- Communication: REST API over JSON, testable with Postman

---

## 1. Folder structure

```
student-skill-project-management/
├── README.md
├── .gitignore
├── postman_collection.json
├── backend/
│   ├── .gitignore
│   ├── manage.py
│   ├── requirements.txt
│   ├── config/
│   │   ├── __init__.py
│   │   ├── asgi.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   └── records/
│       ├── __init__.py
│       ├── admin.py
│       ├── apps.py
│       ├── models.py
│       ├── serializers.py
│       ├── tests.py
│       ├── urls.py
│       ├── views.py
│       └── migrations/
│           ├── __init__.py
│           └── 0001_initial.py
└── frontend/
    ├── .env
    ├── .gitignore
    ├── index.html
    ├── package.json
    ├── vite.config.js
    └── src/
        ├── App.jsx
        ├── api.js
        ├── main.jsx
        ├── styles.css
        └── components/
            ├── ConfirmDialog.jsx
            ├── Dashboard.jsx
            ├── FilterBar.jsx
            ├── Header.jsx
            ├── RecordForm.jsx
            ├── RecordList.jsx
            └── Toast.jsx
```

### What each backend file does

| File | Purpose |
| --- | --- |
| `manage.py` | Command line entry point for every Django command |
| `config/settings.py` | Installed apps, database, CORS, REST Framework settings |
| `config/urls.py` | Sends `/admin/` to the admin site and `/api/` to the records app |
| `records/models.py` | The `StudentProject` table definition |
| `records/serializers.py` | Converts objects to JSON and validates incoming JSON |
| `records/views.py` | CRUD viewset, dashboard statistics, dropdown options |
| `records/urls.py` | Router that builds the `/api/records/` endpoints |
| `records/admin.py` | Django admin list, filters and search |
| `records/tests.py` | Automated test cases |

### What each frontend file does

| File | Purpose |
| --- | --- |
| `src/main.jsx` | Mounts the React application into `index.html` |
| `src/App.jsx` | Holds the data, the filters and every action handler |
| `src/api.js` | One wrapper around `fetch` used by the whole application |
| `src/components/Header.jsx` | Title bar and section switcher |
| `src/components/Dashboard.jsx` | Statistics, breakdown bars, most common skills |
| `src/components/FilterBar.jsx` | Search box and the four filter dropdowns |
| `src/components/RecordList.jsx` | Record cards, empty state, edit and delete buttons |
| `src/components/RecordForm.jsx` | Add and edit form with client-side validation |
| `src/components/ConfirmDialog.jsx` | Delete confirmation window |
| `src/components/Toast.jsx` | Success and error messages |
| `src/styles.css` | All styling, including the responsive rules |

---

## 2. Requirements

- Python 3.10 or newer
- Node.js 18 or newer (Node 20 recommended) and npm
- Git

Check what you have:

```bash
python --version
node -v
npm -v
```

---

## 3. Backend setup and run

Open a terminal in the project folder.

**Windows (PowerShell or Command Prompt)**

```bash
cd backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

**macOS or Linux**

```bash
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

The API now runs at `http://127.0.0.1:8000/api/` and the admin site at
`http://127.0.0.1:8000/admin/`.

Leave this terminal running.

---

## 4. Frontend setup and run

Open a **second** terminal.

```bash
cd frontend
npm install
npm run dev
```

The application opens at `http://localhost:5173/`.

The API address is read from `frontend/.env`:

```
VITE_API_BASE_URL=http://127.0.0.1:8000/api
```

Change that line only if you run Django on a different port. Restart
`npm run dev` after editing `.env`, because Vite reads it at start up.

---

## 5. Running the tests

With the virtual environment active:

```bash
cd backend
python manage.py test
```

The suite covers the model, every CRUD endpoint, the validation rules, the
search and filter parameters and the statistics endpoint. Tests use a
temporary database, so your own records are never touched.

---

## 6. API documentation

Base address: `http://127.0.0.1:8000/api`

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/records/` | List records, with optional search and filters |
| POST | `/records/` | Create a record |
| GET | `/records/{id}/` | Read one record |
| PUT | `/records/{id}/` | Replace a record (send every field) |
| PATCH | `/records/{id}/` | Update some fields of a record |
| DELETE | `/records/{id}/` | Delete a record |
| GET | `/stats/` | Dashboard statistics |
| GET | `/options/` | Values for the dropdown menus |

### Query parameters for `GET /records/`

| Parameter | Example | Meaning |
| --- | --- | --- |
| `search` | `?search=python` | Matches name, register number, email, skills, technology, project title |
| `department` | `?department=CSE` | Exact department code |
| `year` | `?year=3` | 1, 2, 3 or 4 |
| `skill_level` | `?skill_level=Advanced` | Beginner, Intermediate or Advanced |
| `project_status` | `?project_status=Completed` | Planned, In Progress, Completed or On Hold |
| `skill` | `?skill=react` | Records that contain this skill |
| `ordering` | `?ordering=-created_at` | Sort by name, year, project_title, created_at, updated_at |

Parameters can be combined: `/api/records/?department=CSE&year=3&search=react`

### Request body for POST and PUT

```json
{
  "name": "Anitha Raman",
  "register_number": "20CSE1001",
  "email": "anitha.raman@example.edu",
  "department": "CSE",
  "year": 3,
  "skills": "Python, Django, SQL",
  "skill_level": "Intermediate",
  "project_title": "Campus Placement Portal",
  "project_description": "A portal that lets students apply for campus drives and track results.",
  "technology": "Django, React, SQLite",
  "project_status": "In Progress",
  "github_link": "https://github.com/anitha/placement-portal"
}
```

### Successful response (201 Created)

The response repeats the saved data and adds read-only helper fields:

```json
{
  "id": 1,
  "name": "Anitha Raman",
  "register_number": "20CSE1001",
  "email": "anitha.raman@example.edu",
  "department": "CSE",
  "department_display": "Computer Science and Engineering",
  "year": 3,
  "year_display": "Third year",
  "skills": "Python, Django, SQL",
  "skill_level": "Intermediate",
  "project_title": "Campus Placement Portal",
  "project_description": "A portal that lets students apply for campus drives and track results.",
  "technology": "Django, React, SQLite",
  "project_status": "In Progress",
  "github_link": "https://github.com/anitha/placement-portal",
  "skill_list": ["Python", "Django", "SQL"],
  "technology_list": ["Django", "React", "SQLite"],
  "created_at": "2026-02-10T09:15:22.184Z",
  "updated_at": "2026-02-10T09:15:22.184Z"
}
```

### Error response (400 Bad Request)

```json
{
  "register_number": ["student project record with this register number already exists."],
  "project_description": ["Project description must contain at least 20 characters."]
}
```

The frontend reads this object and prints each message under the matching
input box.

### Field reference

| Field | Type | Rules |
| --- | --- | --- |
| `name` | text | At least 3 characters, letters, spaces and dots |
| `register_number` | text | 6 to 20 letters or digits, unique, stored in capitals |
| `email` | text | Valid email, unique, stored in small letters |
| `department` | choice | CSE, IT, AIDS, ECE, EEE, MECH, CIVIL, MBA |
| `year` | number | 1 to 4 |
| `skills` | text | Comma separated, 1 to 15 items |
| `skill_level` | choice | Beginner, Intermediate, Advanced |
| `project_title` | text | At least 5 characters |
| `project_description` | text | At least 20 characters |
| `technology` | text | Comma separated, at least 1 item |
| `project_status` | choice | Planned, In Progress, Completed, On Hold |
| `github_link` | text | Optional, must contain `github.com/`, required when the status is Completed |

### Testing with Postman

1. Start the Django server.
2. Import `postman_collection.json` (Import → File).
3. Run **Create record** first; the other requests use `{{base_url}}` and a
   record id you can change in the address bar.
4. For POST, PUT and PATCH, choose Body → raw → JSON.

You can also open `http://127.0.0.1:8000/api/records/` in a browser to use the
browsable API that REST Framework provides.

---

## 7. Django admin

```bash
python manage.py createsuperuser
```

Then sign in at `http://127.0.0.1:8000/admin/`. The record list shows the name,
register number, department, year, skill level, project and status, with
filters on the right and a search box on top.

---

## 8. Pushing to GitHub

```bash
git init
git add .
git commit -m "Student Skill and Project Management System"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repository>.git
git push -u origin main
```

`db.sqlite3`, `venv/`, `node_modules/` and the local secret key file are
already listed in `.gitignore`, so they stay off GitHub.

---

## 9. How the parts fit together

1. The browser loads the React application from Vite on port 5173.
2. React calls the Django API on port 8000 using `fetch`.
3. Django allows that call because port 5173 is listed in `CORS_ALLOWED_ORIGINS`.
4. The router sends `/api/records/` to `StudentProjectViewSet`.
5. The serializer validates the JSON and the model saves it into SQLite.
6. Django replies with JSON; React updates the screen and shows a message.

---

## 10. Common problems and fixes

| Problem | Cause | Fix |
| --- | --- | --- |
| "Cannot reach the server" in the browser | Django is not running | Start `python manage.py runserver` in the backend terminal |
| CORS error in the browser console | Frontend runs on a different port | Add that address to `CORS_ALLOWED_ORIGINS` in `settings.py` |
| `no such table: records_studentproject` | Migrations not applied | Run `python manage.py migrate` |
| `ModuleNotFoundError: No module named 'rest_framework'` | Virtual environment not active | Activate `venv`, then `pip install -r requirements.txt` |
| Port 8000 already in use | Another server is running | `python manage.py runserver 8001` and update `.env` |
| Changes to `.env` have no effect | Vite reads it at start up | Stop and start `npm run dev` again |

---

## 11. Viva questions and answers

**1. What does CRUD mean in this project?**
Create, Read, Update and Delete. Those four actions map to POST, GET, PUT or
PATCH, and DELETE on `/api/records/`.

**2. Why is this called a full-stack project?**
It has a frontend (React), a backend (Django REST Framework) and a database
(SQLite), and the three layers talk to each other.

**3. What is a REST API?**
A way for two programs to talk over HTTP, where each address stands for a
resource and the HTTP method says what to do with it. The data travels as JSON.

**4. What does Django REST Framework add to Django?**
Serializers, viewsets, routers, a browsable API and ready-made validation and
error responses, so we do not write JSON handling by hand.

**5. What is a serializer?**
A class that converts model objects into JSON for the response, and validates
and converts incoming JSON into model fields for the request.

**6. What is the difference between `ModelViewSet` and a normal view?**
`ModelViewSet` gives list, create, retrieve, update, partial update and destroy
in one class. A normal view handles one request style at a time.

**7. What does the router do?**
`DefaultRouter` builds the URLs for the viewset automatically, so
`/records/` and `/records/{id}/` exist without writing each path.

**8. What is a migration?**
A file that describes a change to the database structure. `makemigrations`
writes it and `migrate` applies it to SQLite.

**9. Why SQLite?**
It is a single file database that needs no separate server, which makes it a
good fit for a college project. The same code can move to PostgreSQL by
changing the `DATABASES` setting.

**10. What is CORS and why is it needed here?**
The browser blocks requests from one origin to another by default. The frontend
is on port 5173 and the API on port 8000, so `django-cors-headers` adds the
headers that allow it. The middleware must sit above `CommonMiddleware`.

**11. Where is validation done, and why in two places?**
In `RecordForm.jsx` for fast feedback while typing, and in
`serializers.py` because the server must never trust the browser. Postman can
skip the frontend entirely, so the server check is the real one.

**12. Give an example of a business rule in the code.**
A project cannot be marked Completed unless a GitHub link is present. It is
checked in the serializer `validate` method and in the form.

**13. How does search work?**
`SearchFilter` from REST Framework reads the `search` parameter and matches it
against the fields listed in `search_fields` using a case-insensitive
contains query.

**14. How do the filters work?**
`get_queryset` reads `department`, `year`, `skill_level` and `project_status`
from the query string and narrows the queryset for each one that is present.

**15. How are the dashboard numbers calculated?**
The `/api/stats/` view groups records with `values(...).annotate(Count(...))`
and counts skills with `collections.Counter`, then returns one JSON object.

**16. What are `useState` and `useEffect`?**
`useState` stores values that change and re-renders the component when they
change. `useEffect` runs code after rendering, for example the first API call.

**17. Why is `useCallback` used in `App.jsx`?**
`loadData` is listed in a `useEffect` dependency array. `useCallback` keeps the
same function between renders unless the filters change, which stops an endless
request loop.

**18. Why is there a 300 millisecond timer around `loadData`?**
It is debouncing. The app waits until typing pauses, so one request goes out
instead of one per keystroke.

**19. How are errors shown to the user?**
`api.js` throws an `ApiError` that carries the per-field messages. The form
puts each message under its input, and a toast shows the summary.

**20. Why is the secret key not written inside `settings.py`?**
Secrets must not reach GitHub. The key is read from the `DJANGO_SECRET_KEY`
environment variable, and on a development machine it is generated once into
`.dev_secret_key`, which `.gitignore` excludes.

**21. What is the purpose of `.gitignore`?**
It keeps generated and private files out of the repository: `venv/`,
`node_modules/`, `db.sqlite3`, `__pycache__/` and the local secret key.

**22. What is `requirements.txt` and `package.json`?**
Lists of dependencies with versions. They let anyone rebuild the same
environment with `pip install -r requirements.txt` and `npm install`.

**23. How do the Django tests run without touching your data?**
Django creates a separate test database, runs each test inside a transaction
and deletes the database at the end.

**24. What is the difference between PUT and PATCH?**
PUT replaces the whole record, so every field must be sent. PATCH updates only
the fields you send.

**25. What would you improve next?**
Authentication with tokens, pagination for large lists, file upload for project
reports, and deployment with PostgreSQL and a production web server.
