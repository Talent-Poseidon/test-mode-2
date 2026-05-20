# Event Storming: User Registration Feature

> **Purpose**: Sample event storming untuk test Mode 2 template regression.
> **Scope**: 1 epic — basic user registration flow.

---

## Context

Build a basic user registration page where new users can sign up using email + password. Successful registration redirects to dashboard. Failed registration shows error message.

---

## Actors

- **Visitor**: User yang belum punya akun, mengakses halaman /register.
- **System**: Backend yang validate + simpan user ke database.

---

## Domain Events

1. **VisitorOpenedRegisterPage** — User membuka `/register`
2. **RegistrationFormSubmitted** — User isi email + password lalu submit
3. **UserRegistered** — Sistem berhasil simpan user baru ke DB
4. **RegistrationFailed** — Validation gagal atau email sudah terdaftar
5. **UserRedirectedToDashboard** — Setelah registrasi sukses

---

## Commands

| Command | Trigger | Result Event |
|---|---|---|
| `OpenRegisterPage` | Visitor klik link "Register" | `VisitorOpenedRegisterPage` |
| `SubmitRegistration` | Visitor klik tombol Submit di form | `RegistrationFormSubmitted` |
| `ValidateAndCreateUser` | System receive submission | `UserRegistered` atau `RegistrationFailed` |
| `RedirectToDashboard` | UserRegistered fired | `UserRedirectedToDashboard` |

---

## Aggregates

### User
- **Properties**: id, email, passwordHash, createdAt
- **Invariants**:
  - email harus unique
  - password minimum 8 karakter
  - email harus valid format

---

## Policies / Business Rules

1. Email harus unique di sistem.
2. Password disimpan sebagai hash (bcrypt, salt rounds 10).
3. Setelah registrasi sukses, user otomatis login (session created).
4. Kalau registrasi gagal, form tetap retain email value (tapi NOT password).
5. Error message harus jelas (e.g., "Email sudah terdaftar", "Password minimum 8 karakter").

---

## Acceptance Criteria

- [ ] Halaman `/register` accessible tanpa login
- [ ] Form contains: email field, password field, submit button
- [ ] Email validation: format check (regex)
- [ ] Password validation: minimum 8 karakter
- [ ] Submit dengan email duplicate → error "Email sudah terdaftar"
- [ ] Submit dengan password < 8 char → error "Password minimum 8 karakter"
- [ ] Submit valid → user created di DB + session created + redirect to `/dashboard`
- [ ] Form retains email value setelah error (UX)
- [ ] Password TIDAK retained setelah error (security)
- [ ] Unit test untuk validation logic
- [ ] E2E test untuk happy path registrasi

---

## UI Spec (Hint)

### Route: `/register`

**Elements**:
- Heading: "Daftar Akun Baru"
- Email input (`data-testid="email-input"`)
- Password input (type="password", `data-testid="password-input"`)
- Submit button (`data-testid="submit-btn"`)
- Error message area (`data-testid="error-msg"`)
- Link "Sudah punya akun? Login" → `/login`

**Layout**: Center, max-width 400px, padding standard.

---

## Out of Scope (untuk test ini)

- Email verification / activation link
- Password reset flow
- Social login (Google/Facebook)
- 2FA
- Profile page
- Admin user management

