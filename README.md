# Activity Tracker

A Windows desktop activity-tracking web application built with Django. Users can create an account, start or stop tracking, review active window titles, and retrieve screenshots saved during a tracking session.

> **Privacy and security:** Tracking records the foreground window title and captures the entire desktop. Use this application only on a device and account you are authorized to monitor, and make sure anyone using the device knows tracking is enabled. The current project is a development prototype: tracking endpoints are not individually protected, and the Django settings contain development-only security configuration. Do not expose it to the public internet or use it for production without addressing those limitations.

## Features

- Signup and login endpoints with bcrypt password hashing and JWT access/refresh token generation.
- Start/stop controls for foreground-window tracking.
- Records a changed active window title approximately every three seconds.
- Captures up to five screenshots per tracking session, about ten seconds apart, and stores them in MongoDB.
- Endpoints to retrieve tracked window titles and request screenshot images.

## Tech stack

- Python 3.10+
- Django 5.1 and Django REST Framework
- MongoDB with PyMongo
- bcrypt and JWT
- `psutil` and Windows APIs (`pywin32`) for foreground-window information
- Pillow for desktop screenshots
- HTML, CSS, and JavaScript templates

## Requirements

- Windows
- Python 3.10 or newer
- MongoDB running locally on `mongodb://localhost:27017/`

The application currently connects to the `MynewDB` database and its `user` collection. The connection is configured in `activitytracking/Home/models.py`.

## Setup

Open PowerShell in the repository folder and run:

```powershell
py -3 -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
pip install Pillow pywin32
```

The additional install command supplies Pillow and `pywin32`, which are imported by the application but are not listed in the current `requirements.txt`.

Start MongoDB, then launch Django:

```powershell
cd activitytracking
python manage.py runserver
```

Open [http://127.0.0.1:8000/](http://127.0.0.1:8000/) in a browser. Keep the server running while using the app.

## Main routes

| Route | Method | Purpose |
|---|---|---|
| `/` | GET | Front page |
| `/register/` | GET | Signup page |
| `/signup/` | POST | Create an account |
| `/login/` | POST | Authenticate and issue tokens |
| `/logout/` | POST | Clear the session access token |
| `/dashboard/` | GET | Dashboard page |
| `/activity/` | GET | Activity controls and tracked window titles |
| `/start_tracking/` | POST | Start collecting window titles and screenshots |
| `/stop_tracking/` | POST | Stop tracking |
| `/get_applications/` | GET | Return recorded window titles |
| `/get_my_images/` | POST | Return screenshot images; expects a JSON `id` |

## Project structure

```text
Activity_Tracker/
├── requirements.txt
├── README.md
└── activitytracking/
    ├── manage.py
    ├── activitytracking/       # Django project settings and URL configuration
    └── Home/                   # Views, MongoDB access, authentication, and templates
```

## Notes

- Screenshots are stored as Base64-encoded PNG data in the user's MongoDB document.
- Screenshot retrieval currently compares the supplied user ID as a string with MongoDB's `_id` value; because captured data is keyed by an `ObjectId`, image retrieval may need an ID conversion fix.
- The tracking implementation uses Windows-specific APIs, so the activity-monitoring features are intended for Windows.
- The project uses development settings, including `DEBUG = True` and an in-code Django secret key. Configure secure environment-based settings before deployment.