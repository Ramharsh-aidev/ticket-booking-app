# Ticket Booking System

![Python Version](https://img.shields.io/badge/python-3.12-blue.svg)
![Django Version](https://img.shields.io/badge/django-5.x-green.svg) <!-- Or 5.2 based on logs -->
![MySQL](https://img.shields.io/badge/mysql-8.0-%234479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)

## Project Overview

This project is a full-stack web application for managing a ticket booking system for shows and events. It's built using Python and the Django framework and adheres to specific constraints for demonstrating core web development concepts. Users can view available shows, book tickets, and review their booking history. A dedicated **custom-built admin panel** (explicitly avoiding the built-in Django Admin) allows administrators to manage shows and view all bookings.

The entire application environment, including the database, is containerized using Docker and Docker Compose for consistency across different machines and environments. A `Jenkinsfile` is included to define a basic Continuous Integration and Continuous Deployment (CI/CD) pipeline.

## Functional Requirements

*   **User Authentication:**
    *   User Registration (`/accounts/register/`)
    *   User Login (`/accounts/login/`)
    *   User Logout (`/accounts/logout/`)
*   **User Flow:**
    *   View a list of available shows (`/shows/`).
    *   View detailed information for a single show (`/shows/<pk>/`).
    *   Book tickets for a show (via the show detail page).
    *   View booking history (`/bookings/history/`).
    *   See a booking confirmation page (`/bookings/confirmation/`).
*   **Custom Admin Panel (Access at `/custom-admin/`):**
    *   Requires superuser login (create via `createsuperuser`).
    *   Admin Dashboard (`/custom-admin/`)
    *   View all shows (`/custom-admin/shows/`)
    *   Add new shows (`/custom-admin/shows/create/`)
    *   Edit existing show details (`/custom-admin/shows/<pk>/update/`)
    *   Delete shows (`/custom-admin/shows/<pk>/delete/`)
    *   View all bookings (`/custom-admin/bookings/`).
    *   **Note:** This panel is built using Django views and templates, *not* the standard `django.contrib.admin`.

## Key Constraints Followed

*   **Strictly Class-Based Views (CBV):** All views are implemented as Django Class-Based Views (`View`, `ListView`, `DetailView`, `TemplateView`). Function-Based Views (FBV) are explicitly avoided.
*   **Avoid Django Forms:** All HTML form data retrieval and validation is handled manually within the CBV `post` methods using `request.POST`.
*   **No Django Admin:** A completely separate custom admin panel is built from scratch using standard Django features (views, templates, permissions).
*   **MySQL Database:** The application is configured to use a MySQL database, managed via Docker Compose.
*   **Docker & Docker Compose:** Used for packaging the application and orchestrating services.
*   **Jenkinsfile:** Included for defining a basic CI/CD pipeline.

## Tech Stack

*   **Backend:** Python 3.12, Django 5.x
*   **Database:** MySQL 8.0 (containerized)
*   **Frontend:** HTML5, CSS3 (Basic styling provided in `static/css/style.css`)
*   **Server within Docker:** Django Development Server (`manage.py runserver 0.0.0.0:8000`) - *Note: For production deployment, replace this with a production-ready WSGI/ASGI server like Gunicorn or uWSGI.*
*   **Containerization:** Docker, Docker Compose (using modern `docker compose` syntax)
*   **DB Driver:** `mysqlclient`
*   **Environment Variables:** `python-dotenv`

## Screenshots & Demo GIF

**Homepage:**
![photo1](https://github.com/user-attachments/assets/a2a80f00-a63c-46d8-9797-a0eedcf11298)

**Cart:**
![image](https://github.com/user-attachments/assets/e9f669b9-f8d1-47b7-81b4-a83d32a61b27)

**Seat Select**
![HELLO](https://github.com/user-attachments/assets/8a5d746f-877b-4732-b5b4-58a0418fc70b)

**Booking Confirmation**
![part](https://github.com/user-attachments/assets/e8db9abf-9473-4b44-b72e-719f3a28fad5)

**Docker:**
![Screenshot 2025-04-25 164217](https://github.com/user-attachments/assets/f8e511f1-ed52-4b07-9e0e-c4fcbb942394)

![Screenshot 2025-04-25 152857](https://github.com/user-attachments/assets/32869968-0bce-4533-90b1-4b1ab6090e8e)



## Prerequisites

Before you begin, ensure you have the following installed on your system:

*   **Git:** For cloning the repository.
*   **Docker:** The containerization platform ([Get Docker](https://www.docker.com/products/docker-desktop/)).
*   **Docker Compose:** For managing multi-container Docker applications (included with Docker Desktop, invoked via `docker compose`).
*   *(Optional)* Python 3.12+ and Pip (if you wish to run outside Docker for local dev/debugging, though not recommended for this setup).

## Setup & Run Instructions (Recommended: Docker Compose)

The recommended way to set up and run this project is using Docker Compose.

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/Ramharsh-aidev/ticket-booking-app/
    cd ticket-booking-app
    ```

2.  **Configure Environment Variables:**
    Create a file named `.env` in the root of the project directory (the same location as `docker-compose.yml`). This file stores configuration and sensitive data. **Do NOT commit this file to Git.** Ensure it's listed in your `.gitignore`.

    ```env
    SECRET_KEY='your-secret-key-here-replace-this'
    DEBUG=True
    # Database settings for Docker Compose setup
    DATABASE_NAME='mydatabase'
    DATABASE_USER='myuser'
    DATABASE_PASSWORD='mypassword'
    DATABASE_HOST='db'
    DATABASE_PORT='3306'
    
    # DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1] yourdomain.com
    ```
    *   Replace the secret key with a strong, unique value.
    *   Fill in your desired database name, user, and password.

3.  **Build the Web Application Docker Image (Force Rebuild without Cache):**
    We've encountered caching issues during build. This command explicitly rebuilds the web image from scratch, ensuring all `apt-get` dependencies (like `netcat-openbsd`) are correctly installed.

    ```bash
    docker compose build --no-cache web
    # Or using older syntax: docker-compose build --no-cache web
    ```
    Wait for this command to complete successfully. Check the output for the `apt-get install` step to confirm `netcat-openbsd` is installed.

4.  **Start the Docker Containers:**
    Now that the web image is built, start both the `web` and `db` services. Use `--force-recreate` to ensure Docker uses the newly built image.

    ```bash
    docker compose up --force-recreate -d web db
    # Or using older syntax: docker-compose up --force-recreate -d web db
    ```
    *   `-d`: Run in detached mode.

    *   **Troubleshooting Port Conflicts (`listen tcp 0.0.0.0:<port>: bind: Only one usage...`):** If you encounter this, the indicated port on your host machine is in use. Edit `docker-compose.yml` and change the *host* port mapping (the first number in the `ports: - "HOST:CONTAINER"` line) for the conflicting service to an unused port (e.g., change `ports: - "3306:3306"` to `- "3307:3306"` for the database or `- "8000:8000"` to `- "8080:8000"` for the web app). Save the file and run the `docker compose up --force-recreate -d web db` command again.

5.  **Check Container Status:**
    Verify that both containers are running:
    ```bash
    docker compose ps
    # Or: docker-compose ps
    ```
    Look for `Up` status for both services. `(healthy)` indicates health checks are passing (DB should be healthy quickly, web might take a minute).

6.  **Access the Application:**
    Once the containers are running and healthy, open your web browser and go to:

    ```
    # http://localhost:8080/
    ```
    You can also use `http://127.0.0.1:8080/` or `http://127.0.0.1:8080/`.

## Custom Admin Panel

This project features a custom administration panel built using standard Django views and templates, completely separate from `django.contrib.admin`.

*   **Access URL:** `http://localhost:8000/custom-admin/` (or your mapped web port)
*   **Authentication:** Requires login using a user account that has `is_superuser` set to `True` (created via `python manage.py createsuperuser`).

## DevOps Notes

*   **Dockerfile:** Defines the build process for the web application's Docker image. It starts from `python:3.12-slim`, installs necessary system dependencies for `mysqlclient` (like `build-essential`, `pkg-config`, `libmariadb-dev-compat`, `libmariadb-dev`), includes `curl` for health checks and `netcat-openbsd` for `wait-for-it.sh`, installs Python dependencies from `requirements.txt`, copies the project code, and sets the startup command (`CMD`).
*   **docker-compose.yml:** Orchestrates the multi-container application. It defines two services: `web` (built from the `Dockerfile`) and `db` (using the standard `mysql:8.0` image). It configures volumes for data persistence, maps ports from containers to the host, sets up environment variables from the `.env` file, and defines health checks (`depends_on: db: condition: service_healthy` ensures the web app waits for the database).
*   **Jenkinsfile:** Provides a basic definition for a CI/CD pipeline in Jenkins. It includes stages for checking out code, building the Docker image (potentially via `docker-compose build`), a placeholder for running tests (e.g., `manage.py test` inside a container), and deploying the application via `docker compose up`. This file requires a Jenkins server configured with Docker access to be fully functional. You will need to replace `YOUR_GITHUB_REPO_URL` in the Jenkinsfile with the actual URL of your Git repository.

## Using the Jenkins

1.  In Jenkins, create a new **Pipeline** job.
2.  Configure the job to fetch the pipeline script from **SCM** (Git).
3.  Enter your repository URL (`<YOUR_GITHUB_REPO_URL>`).
4.  Specify the branch (e.g., `main`).
5.  Set the **Script Path** to `Jenkinsfile`.
6.  Configure **Build Triggers** (e.g., Poll SCM to trigger builds on commits) and **Pipeline Options** as needed.
7.  Configure **Credentials Binding** in the job settings to inject your required secret environment variables (matching the names expected by `docker-compose.yml`) into the build environment.
8.  Save the job and trigger a build ("Build Now").

## Project Structure

```
ticket_booking_system/
├── accounts/ # Django app for user authentication (views, urls, models - uses default User model)
│ └── migrations/ # Database migration files
├── shows/ # Django app for show/event management (models, views, urls)
│ └── migrations/ # Database migration files
├── bookings/ # Django app for ticket booking (models, views, urls)
│ └── migrations/ # Database migration files
├── custom_admin/ # Django app for the custom admin panel (views, urls)
│ └── migrations/ # Database migration files
├── ticket_booking_system/ # Main Django project settings (settings.py, urls.py, wsgi.py, asgi.py, init.py)
├── static/ # Project-wide static files (CSS, JS, images)
│ └── css/
│ └── style.css
├── templates/ # Project-wide templates (base.html, home.html, and app-specific subdirectories)
│ ├── accounts/
│ ├── bookings/
│ ├── custom_admin/
│ │ ├── bookings/
│ │ └── shows/
│ └── shows/
├── venv/ # Virtual environment (Ignored by .gitignore)
├── .env # Environment variables (MUST NOT be committed - check .gitignore)
├── .gitignore # Specifies intentionally untracked files for Git
├── Dockerfile # Instructions to build the Django application Docker image
├── docker-compose.yml # Defines services (web, db) for Docker Compose
├── Jenkinsfile # Declarative pipeline script for Jenkins CI/CD
├── manage.py # Django's command-line utility
├── requirements.txt # Python package dependencies
```

## Troubleshooting

*   **`docker compose up` fails with port conflict (e.g., 3306 or 8000):**
    *   Another process on your host machine is using the port Docker is trying to map.
    *   **Solution:** Edit `docker-compose.yml` and change the *host* port mapping (the first number in the `ports: - "HOST:CONTAINER"` line) for the conflicting service to an unused port (e.g., change `3306:3306` to `3307:3306` for DB, `8000:8000` to `8080:8000` for the Web app). Save the file and run `docker compose up --build -d` again.
*   **`docker compose ps` shows `web` container as `Up (unhealthy)` and browser shows `ERR_EMPTY_RESPONSE` or `ERR_CONNECTION_REFUSED` on `localhost:<web_port>`:**
    *   The Django server isn't starting correctly *inside* the container or the Docker health check is failing.
    *   **Solution:**
        1.  View container logs: `docker compose logs web`. Look for errors during startup.
        2.  If logs show `nc: command not found`, ensure `netcat-openbsd` is in your `Dockerfile`'s `apt-get install` line and force a rebuild: `docker compose build --no-cache web && docker compose up --force-recreate -d web db`.
        3.  If logs show database connection errors (`django.db.utils.OperationalError`), double-check `DATABASE_NAME`, `DATABASE_USER`, `DATABASE_PASSWORD` in `.env` match the `db` service's environment variables in `docker-compose.yml` exactly.
        4.  Run migrations or the server manually in foreground inside a temporary container to see errors directly: `docker compose run --rm web python manage.py migrate` or `docker compose run --rm web python manage.py runserver 0.0.0.0:8000`.
        5.  Could also be a firewall issue on your host blocking the exposed port (8000 or 8080).
*   **`docker compose build` fails during `apt-get install` with "Package '<package-name>' has no installation candidate":**
    *   The package name is wrong for the specific Debian version used (`python:3.12-slim` uses Debian Bookworm).
    *   **Solution:** The error message often suggests the correct alternative package names (e.g., for `libmysqlclient-dev`, use `libmariadb-dev-compat` and `libmariadb-dev`). Update the package names in the `Dockerfile` accordingly, then rebuild with `--no-cache`: `docker compose build --no-cache web`.
*   **`docker compose build` fails during `apt-get update` or `install` with network errors (e.g., 400 Bad Request):**
    *   Temporary network issue reaching Debian repositories.
    *   **Solution:** Simply try `docker compose up --build -d` again. If persistent, try cleaning Docker system/build cache (`docker system prune --force`, `docker builder prune --force`) then rebuild with `--no-cache`: `docker compose build --no-cache web`. If still persistent, investigate your host network, firewall, or proxy.
*   **`django.db.utils.OperationalError: (2005, "Unknown server host 'db'...")`:**
    *   Happens when running Django commands (`migrate`, `createsuperuser`) *locally* on your host machine, but `settings.py` is configured for the Docker network host `db`.
    *   **Solution:** Run these commands *inside* the Docker container using `docker compose exec web ...`. If you *must* run locally, temporarily change `DATABASE_HOST` in your *local* `.env` or `settings.py` to your local MySQL host (e.g., `127.0.0.1`) and ensure a local MySQL server is running.
*   **User-specific issues (Authentication, Booking):** Check Django logs (`docker compose logs web`) for Python tracebacks when performing actions on the website. Double-check template variable names and view logic (especially manual POST data handling).

---

