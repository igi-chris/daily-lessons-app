# Daily Lessons App

This project is a Vue.js application built with Vite and deployed to GitHub Pages.

## 🚀 Deployment Instructions

To update the deployment (i.e., publish new changes to your GitHub Pages site), follow these steps:

### 1. Make Your Changes
Edit your code as needed. Commit and push your changes to the `main` branch:

```sh
git add .
git commit -m "Describe your changes"
git push origin main
```

### 2. Build and Deploy
Run the following command to build the project and deploy it to GitHub Pages:

```sh
npm run deploy
```

This will:
- Build the project into the `dist` folder
- Publish the contents of `dist` to the `gh-pages` branch using the `gh-pages` package

### 3. View Your Site
After deployment, your site will be available at:

[https://igi-chris.github.io/daily-lessons-app/](https://igi-chris.github.io/daily-lessons-app/)

---

## 🛠️ Project Setup

If you haven't already, install dependencies:

```sh
npm install
```

## 📦 Scripts

- `npm run dev` — Start the development server
- `npm run build` — Build the app for production
- `npm run preview` — Preview the production build locally
- `npm run deploy` — Build and deploy to GitHub Pages

## ⚙️ Configuration

The `vite.config.js` file is set with the correct `base` for GitHub Pages:

```js
base: '/daily-lessons-app/',
```

---

## ℹ️ Notes
- Make sure all your changes are committed before deploying.
- The deployment uses the `gh-pages` branch; do not edit this branch manually.
- If you change the repository name, update the `base` in `vite.config.js` accordingly.
