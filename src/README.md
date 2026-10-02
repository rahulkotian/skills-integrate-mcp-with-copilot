# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Sign up for activities
- Teacher-only signup and unregister controls

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   python app.py
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Sign up for an activity                                             |

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.

## Teacher Access

Activity listings and participant names are public. Signing up and unregistering require a teacher login. Copy `teacher_credentials.example.json` to `teacher_credentials.json` to configure teachers. The local credential file is ignored by Git, and no one can sign in until a teacher is configured. Passwords are stored as salted PBKDF2-HMAC-SHA256 hashes, not plaintext.

Generate a salt and password hash with Python, then add the printed object to the `teachers` array in `teacher_credentials.json`:

```sh
python - <<'PY'
import getpass
import hashlib
import json
import secrets

username = input("Teacher username: ")
password = getpass.getpass("Teacher password: ")
salt = secrets.token_hex(16)
password_hash = hashlib.pbkdf2_hmac(
   "sha256", password.encode(), bytes.fromhex(salt), 600_000
).hex()
print(json.dumps({"username": username, "salt": salt, "password_hash": password_hash}, indent=2))
PY
```

Do not commit populated credentials. The browser keeps the teacher login only until the page is closed or the teacher logs out. Use HTTPS when deploying outside a trusted local environment.
