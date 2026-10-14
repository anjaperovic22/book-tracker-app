# BookSelf: Book Tracker App

A hybrid mobile app for keeping track of your reading: books you are reading now, books you plan to read, and books you have finished.

An **admin** curates a shared book catalogue, and each **user** builds a personal library from it, with reading status, ratings and private notes.

Built with **Ionic + Angular (TypeScript)** and **Firebase** (Authentication + Realtime Database). Firebase is accessed directly through its **REST APIs**, without the Firebase SDK.

> Team project for the *Mobile Computing* course at the Faculty of Organizational Sciences, University of Belgrade (2025/2026).
> **Authors:** Una Ilić & Anja Perović

## Features

**Users**
- Sign up and log in with e-mail and password (token-based authentication).
- Browse the global book catalogue, grouped by genre, and search it.
- Add books from the catalogue to a personal library.
- Track reading status: *Planned*, *Currently reading* or *Finished*.
- Rate books from 1 to 5 stars and add private notes to each book.
- See personal statistics: total books, average rating, most popular book.
- Edit their profile (username and avatar).

**Admin**
- Manage the global catalogue: add, edit and delete books.
- View the list of registered users and delete users.
- See system-wide statistics: number of users, catalogue size, most popular books.

The app uses **role-based UI**: the same tabs show different screens to admins and regular users.

## Tech stack

| Layer | Technologies |
|---|---|
| Mobile / UI | Ionic 8, Angular 20 (standalone components), TypeScript, Ionicons |
| Native packaging | Capacitor |
| Backend | Firebase Authentication (REST, Identity Toolkit API), Firebase Realtime Database (REST) |
| Tooling | Node.js, Angular CLI, Ionic CLI, ESLint, Karma/Jasmine |

## How it works

**Authentication.** The app calls the Firebase Identity Toolkit REST API directly:

- Sign up: `POST https://identitytoolkit.googleapis.com/v1/accounts:signUp`
- Log in: `POST https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword`

After login, the returned `idToken` is stored in `localStorage` and sent as the `?auth=` parameter with every Realtime Database request. The user's role (`user` / `admin`) is read from their profile and decides which screens they see.

**Data model (Firebase Realtime Database)**

```
root/
├── catalogue/
│   └── {bookId}/        title, author, genre, description, year, coverUrl
└── users/
    └── {userId}/
        ├── profile/     email, role ("admin" | "user"), username, avatarUrl
        └── books/
            └── {bookId}/  catalogueBookId, title, author, genre,
                           status ("planned" | "reading" | "finished"),
                           rating, notes, userId
```

The catalogue is shared, and each user's library lives under their own node. A user's book keeps a reference to the catalogue entry (`catalogueBookId`) plus that user's own status, rating and notes.

## Getting started

### Prerequisites
- Node.js 18+ and npm
- Ionic CLI: `npm install -g @ionic/cli`
- A Firebase project with **Email/Password** authentication and a **Realtime Database** enabled

### Install

```bash
git clone https://github.com/anjaperovic22/book-tracker-app.git
cd book-tracker-app
npm install
```

### Configure Firebase

Copy `src/environments/environment.example.ts` to `src/environments/environment.ts` and fill in your project's values:

```typescript
export const environment = {
  production: false,
  firebaseApiKey: 'YOUR_API_KEY',
  firebaseDatabaseUrl: 'YOUR_DATABASE_URL'
};
```

### Run

```bash
ionic serve
```

The app opens at `http://localhost:8100`.

To test on a phone on the same Wi-Fi network:

```bash
ionic serve --host=0.0.0.0
```

Then open the `External` address shown in the terminal on your phone.

### Admin account

To try the admin features, register a user and then set `role: "admin"` for that user in the Firebase console (`users/{userId}/profile/role`).

## Project structure

```
src/app/
├── auth/
│   ├── login/              Login page
│   └── register/           Registration page
├── tabs/                   Main tab layout and routes
├── tab1/                   Catalogue: browse & search books (admin: manage catalogue)
├── tab2/                   My books (admin: users)
├── tab3/                   My profile & stats (admin: system statistics)
├── services/
│   ├── auth.service.ts       Sign up, login, logout, role handling
│   ├── book.service.ts       User's personal library
│   └── catalogue.service.ts  Global catalogue
└── models/
    └── book.model.ts         Book interfaces
```

## Authors

- **Una Ilić**: [@unailic](https://github.com/unailic)
- **Anja Perović**: [@anjaperovic22](https://github.com/anjaperovic22)
