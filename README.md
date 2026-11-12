# Ethiopia Home Find

<p align="center">
  <strong>A rental discovery and property-listing platform built for Ethiopia.</strong>
</p>

<p align="center">
  <a href="https://ethiopiahomefind1.vercel.app">Live Demo</a> ·
  <a href="https://github.com/usamaawol/ethiopiahomefind1">Source Code</a>
</p>

---

## Overview

**Ethiopia Home Find** is a web application that helps people discover rental homes across Ethiopian cities and gives property owners a place to publish and manage their listings.

The platform is designed around a direct connection between renters and property owners. Visitors can browse approved properties without creating an account, while registered owners can submit properties for review and track their listing status. Administrators have a dedicated dashboard for moderation, listing management, and AI-assisted review.

The interface supports **English, Amharic, and Afaan Oromo**, with light and dark themes.

## Key Features

### For people looking for a home
- Browse approved rental listings.
- Explore properties by Ethiopian city.
- Search listings by title, city, area, or description.
- Filter available properties by city, monthly price range, and number of rooms.
- View property details such as location, monthly rent in ETB, room capacity, environment highlights, description, and photos.
- Browse public listings without registering.

### For property owners
- Create an account using email and password or Google sign-in.
- Submit a property with its title, city, neighbourhood, monthly rent, room count, maximum occupancy, environment features, description, phone number, and photos.
- Upload up to five property images through Cloudinary.
- Use AI assistance to improve a property description while retaining its original language (English, Amharic, or Afaan Oromo).
- Track submitted properties and their review status from **My Listings**.
- See pending, approved, and rejected submissions.

### For administrators
- Access a protected administration dashboard.
- Review incoming property submissions.
- Approve or reject listings.
- Remove listings and manage listing visibility.
- Monitor listing status and dashboard counts.
- Request an AI-generated quality review to help identify vague, incomplete, or potentially suspicious listing information.

### User experience
- Three interface languages: English, Amharic, and Afaan Oromo.
- Light and dark theme support.
- Responsive layout for desktop and mobile screens.
- Animated interface elements and reusable UI components.
- Web app manifest for an installable, app-like experience on supported devices.

## AI Assistance

The project includes two AI-assisted workflows powered by the Gemini API through a Supabase Edge Function:

1. **Description improvement** — helps owners rewrite property descriptions in a clearer, more professional style while keeping the input language.
2. **Administrator listing review** — provides a concise quality assessment and flags possible gaps or suspicious content to support human moderation.

AI output is an aid, not an automatic approval decision. Administrators remain responsible for reviewing and deciding whether a listing should be published.

## Technology Stack

| Area | Technologies |
|---|---|
| Frontend | React 18, TypeScript, Vite |
| Routing | React Router |
| Styling | Tailwind CSS, shadcn/ui, Radix UI |
| UI and animation | Lucide React, Framer Motion |
| Forms and validation | React Hook Form, Zod |
| Client-side data utilities | TanStack Query |
| Authentication | Firebase Authentication |
| Main application data | Cloud Firestore |
| Property image uploads | Cloudinary |
| AI proxy | Supabase Edge Functions |
| AI provider | Google Gemini API |
| Testing | Vitest, Testing Library |
| Code quality | ESLint |
| Deployment | Vercel |

**Architecture note:** Firebase Authentication and Cloud Firestore are used for the application's account and listing workflows. Supabase is included for the Gemini proxy Edge Function; it is not the primary listings database in the current implementation.

## Application Routes

| Route | Purpose |
|---|---|
| `/` | Home page with featured cities and sample property cards |
| `/browse` | Search and filter approved listings |
| `/cities` | Explore supported Ethiopian cities |
| `/add-listing` | Submit a property (owner account required) |
| `/my-listings` | View an owner's submitted properties |
| `/admin` | Listing moderation dashboard (administrator role required) |
| `/login` | Sign in |
| `/register` | Create an owner account |

## Getting Started

### Prerequisites

- Node.js 18 or later
- npm
- A Firebase project with Authentication and Cloud Firestore enabled
- A Cloudinary account with an unsigned image-upload preset
- A Supabase project (for the Gemini proxy function)
- A Google Gemini API key

### 1. Clone the repository

```bash
git clone https://github.com/usamaawol/ethiopiahomefind1.git
cd ethiopiahomefind1
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure Firebase

Create a Firebase project and enable:

- **Authentication → Email/Password**
- **Authentication → Google** (if Google sign-in is required)
- **Cloud Firestore**

Update the Firebase web configuration in `src/lib/firebase.ts` with the configuration for your own Firebase project.

The application stores user profiles in the `users` collection and property submissions in the `listings` collection. The registration flow creates owner accounts. To grant administrator access, assign the `admin` role to the appropriate user's Firestore profile through a trusted administrative process. Do not let ordinary users grant themselves administrator privileges.

Review and adapt the Firestore security rules for your deployment. The existing `FIREBASE_SETUP.md` file contains an example ruleset; verify that it matches the current application and your intended access policy before deploying it.

### 4. Configure Cloudinary image uploads

The upload helper is in `src/lib/cloudinary.ts`. Configure it with your Cloudinary cloud name and an **unsigned upload preset** named `house_rent`, or update the preset name in the helper to match your own configuration.

For production, restrict the unsigned preset with appropriate upload limits and allowed formats. Never put a Cloudinary API secret in frontend code.

### 5. Configure Supabase and the Gemini proxy

Create a `.env` file in the project root:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_PUBLISHABLE_KEY=your_supabase_publishable_key
```

The frontend calls the `gemini-proxy` Supabase Edge Function for AI-assisted description improvement and administrator reviews.

Set the Gemini API key as a Supabase Function secret, not as a frontend environment variable:

```bash
supabase secrets set GEMINI_API_KEY=your_gemini_api_key
supabase functions deploy gemini-proxy
```

Make sure the deployed function belongs to the same Supabase project configured in the frontend. The function currently calls the Gemini `gemini-2.0-flash` model.

### 6. Start the development server

```bash
npm run dev
```

Vite will print the local development URL in your terminal.

## Available Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start the Vite development server |
| `npm run build` | Create a production build |
| `npm run build:dev` | Create a development-mode build |
| `npm run preview` | Preview the production build locally |
| `npm run lint` | Run ESLint |
| `npm test` | Run the Vitest test suite once |
| `npm run test:watch` | Run tests in watch mode |

## Project Structure

```text
ethiopiahomefind1/
├── public/                     # Static assets and web app manifest
├── src/
│   ├── assets/                 # Local visual assets
│   ├── components/
│   │   ├── cards/              # City and property cards
│   │   ├── layout/             # Main layout and navigation
│   │   └── ui/                 # Reusable interface components
│   ├── contexts/               # Authentication, language, and theme state
│   ├── data/                   # City data and sample property data
│   ├── hooks/                  # Reusable React hooks
│   ├── integrations/supabase/  # Supabase client and generated types
│   ├── lib/                    # Firebase, Cloudinary, and utilities
│   ├── pages/                  # Route-level pages
│   └── test/                   # Test setup and example tests
├── supabase/
│   └── functions/
│       └── gemini-proxy/       # Server-side Gemini API proxy
├── FIREBASE_SETUP.md
├── package.json
└── vite.config.ts
```

## Data and Listing Workflow

1. A property owner creates an account and submits a listing.
2. The listing and its uploaded image URLs are saved to Cloud Firestore with a pending status.
3. An administrator reviews the submission and can optionally request an AI quality assessment.
4. The administrator approves or rejects the listing.
5. Approved listings are available in the public browsing experience; owners can follow their own submissions in **My Listings**.

The homepage also contains sample city and property data in `src/data/mockData.ts`. These sample cards and city counts should not be interpreted as live database statistics.

## Security and Deployment Notes

- Keep Gemini API keys and other server-side secrets in Supabase Function secrets.
- Configure Firestore security rules to enforce owner-only access to private listing data and administrator-only moderation actions.
- Do not rely only on hiding admin pages in the frontend; authorization must also be enforced by Firestore rules.
- Validate uploaded files and configure Cloudinary upload restrictions.
- Review the current Firestore rules before production. The checked-in example ruleset and application code should be kept consistent.
- Use environment-specific Firebase, Supabase, and Cloudinary configuration for local development and production.
- The repository includes a public `.env` file. Before using this project in production, remove environment files from version control, rotate any credentials that should not have been public, and keep only a sanitized `.env.example`. Public client configuration values are not substitutes for server-side secrets.

## Current Scope and Future Improvements

Potential next steps for the project include:

- Replacing remaining sample homepage data with live Firestore-backed data.
- Adding automated tests for authentication, listing submission, filtering, and moderation workflows.
- Improving listing expiry and availability handling.
- Adding stronger validation, reporting, and trust/safety workflows for property listings.
- Documenting and testing production Firestore security rules.

## Contributing

Contributions, suggestions, and bug reports are welcome.

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature`.
3. Commit your changes: `git commit -m "Add your feature"`.
4. Push your branch: `git push origin feature/your-feature`.
5. Open a pull request describing your changes.

## License

No license is currently specified in this repository. Unless a license is added, the default copyright restrictions apply.

---

<p align="center">
  Built to make rental discovery more accessible across Ethiopia.
</p>
