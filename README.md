# MedicSync

MedicSync is a healthcare coordination platform that helps doctors manage patients and digital prescriptions in one place.

It combines:
- a **web application for medical staff** (dashboard, patient management, prescription workflow)
- a **secured API for patient mobile integrations** (profile, prescriptions, nearby pharmacies)

The project is built as a full-stack monolith with Laravel + Inertia + React, and runs locally with Docker.

## Why this project matters

MedicSync demonstrates practical delivery of a domain-focused product that includes:
- role-based access control (doctor/patient/pharmacist roles)
- secure API authentication with OAuth2 (Laravel Passport)
- real-world data modeling for prescriptions and medication schedules
- containerized developer workflow for reproducible local environments

## Core features

### Doctor web portal
- Authentication and account management
- Doctor dashboard with patient and prescription overview
- Patient lifecycle management (create, list, view, edit)
- Prescription creation with multiple medications and dosage intervals
- Prescription detail pages including generated QR code references

### Patient API
- Retrieve authenticated patient profile (`/api/me`)
- Update patient profile and details (`PUT /api/me`)
- List and view own prescriptions (`/api/prescriptions`, `/api/prescriptions/{id}`)
- List nearby pharmacies by geo-distance (`/api/pharmacies?lat=...&lng=...`)

## Technology stack

### Backend
- **PHP 8.2**
- **Laravel 11**
- **Laravel Passport** (OAuth2/password grant)
- **Spatie Laravel Permission** (RBAC)
- **MySQL**

### Frontend
- **React 18**
- **Inertia.js** (Laravel + React bridge)
- **Vite**
- **Tailwind CSS**
- Supporting libraries: `react-datepicker`, `react-icons`, `lucide-react`, `date-fns`, `qrcode.react`

### DevOps & tooling
- **Docker / Docker Compose**
- **Nginx + PHP-FPM**
- **phpMyAdmin**
- **PHPUnit** (backend tests)
- **ESLint + Prettier** (frontend/code style)
- **Makefile** shortcuts for common workflows

## High-level architecture

MedicSync follows a modular monolithic architecture:

1. **Presentation layer**
   - Inertia pages in `src/resources/js/Pages`
   - Role-protected web routes in `src/routes/web.php`

2. **Application layer**
   - HTTP controllers for web and API flows in `src/app/Http/Controllers`
   - Shared auth context via Inertia in `AppServiceProvider`

3. **Domain/data layer**
   - Eloquent models (`User`, `Prescription`, `Medication`, `QRCode`, `Pharmacy`, `PatientDetail`)
   - Relational schema managed through Laravel migrations

4. **Infrastructure layer**
   - Dockerized runtime (Nginx, PHP, MySQL, phpMyAdmin)
   - Environment-driven configuration via `.env`

## Data model highlights

- `users`: core identity table for all roles
- `patient_details`: medical profile extensions for patient users
- `prescriptions`: links doctors to patients with issue/expiry and notes
- `medications`: medication catalog
- `medication_prescription`: pivot table storing frequency and hour interval per medication
- `qr_codes`: unique code associated with each prescription
- Passport OAuth tables for API access tokens and clients

## Repository structure

- `/README.md` — project overview and onboarding
- `/docker-compose.yml` — local multi-container setup
- `/Dockerfile` — PHP runtime image build
- `/nginx/` — web server config
- `/src/` — Laravel application root
  - `app/` — controllers, models, providers
  - `resources/js/` — React + Inertia frontend
  - `routes/` — web and API routes
  - `database/` — migrations and seeders
  - `tests/` — unit and feature tests

## Getting started (local)

### Prerequisites
- Docker + Docker Compose
- GNU Make

### Quick start

From repository root:

```bash
make run-app-with-setup-db
```

This command:
- copies `.env.example` to `.env`
- builds and starts containers
- installs Composer and npm dependencies
- generates Laravel app key
- runs fresh migrations + seeders

### Access points
- Web app: `http://localhost:8001`
- phpMyAdmin: `http://localhost:8080`

## Useful commands

```bash
make run-app                # start containers
make kill-app               # stop containers
make flush-db-with-seeding  # reset DB and seed
make code-format-check      # prettier check
make code-format            # prettier write
make code-test              # php artisan test
```

## Security and access model

- Web area is protected with `auth` and role middleware (`role:medic` for doctor portal)
- API endpoints are protected with `auth:api` and role middleware (`role:patient`)
- Role management is handled with Spatie Permission
- OAuth2 token issuance is provided by Laravel Passport

## Testing and quality

- Backend tests: `php artisan test`
- Frontend formatting/linting scripts are available in `src/package.json`
- Dockerized commands in the `Makefile` standardize checks across machines

## Roadmap ideas

Potential next steps that would increase production readiness:
- stronger API versioning and documentation (OpenAPI)
- audit logging for medical operations
- stricter seed strategy for non-demo environments
- improved test coverage for domain workflows
- CI checks for lint/test gates on pull requests

## License

This project is currently provided without an explicit repository-level license file.
Add a license if you plan public reuse or third-party contributions.
