# Ethiopia Home Find

<p align="center">
  <strong>A rental discovery and property-listing platform for Ethiopia</strong>
</p>

<p align="center">
  Discover rental homes, publish properties, and manage listings through one platform.
</p>

<p align="center">
  <a href="https://ethiopiahomefind1.vercel.app">Live Demo</a> ·
  <a href="https://github.com/usamaawol/ethiopiahomefind1">Source Code</a>
</p>

---

## Overview

**Ethiopia Home Find** is a web application designed to make finding and listing rental homes more organized and accessible across Ethiopian cities. The application is branded as **HouseRent Connect** in parts of its interface.

It brings together three main workflows:

- **Home seekers** can browse approved rental listings and narrow results using city, price, and room filters.
- **Property owners** can create an account, submit a property with photos and contact details, and follow its review status.
- **Administrators** can review submissions, approve or reject listings, and manage listing visibility.

The interface supports English, Amharic, and Afaan Oromo, with light and dark appearance options. AI-assisted writing is also available to help improve property descriptions.

> **Implementation note:** The browse and owner-listing workflows use Cloud Firestore. The homepage's featured properties and city directory currently include local sample data. Sample cards and city counts should not be interpreted as live platform-wide statistics.

## Live Demo

**[Visit Ethiopia Home Find](https://ethiopiahomefind1.vercel.app)**

The live application relies on external Firebase, Cloudinary, and Supabase services. Some functionality may require those services to be configured and available.

## Features

### Property discovery

- Browse approved rental listings from Firestore.
- Search listings by title, area, and description.
- Filter by city, monthly rent range, and number of rooms.
- View property photos, city and neighbourhood, monthly price in ETB, room count, maximum occupancy, description, and environment tags.
- Reveal the contact phone number supplied by the property owner.
- Exclude listings marked as hidden or past their expiry date from browse results.

### City directory

- Explore a curated directory of Ethiopian cities.
- Display city names in English, Amharic, and Afaan Oromo.
- Navigate from a city to the browse page with that city selected.

The current city directory and its displayed listing counts are maintained in local data (src/data/mockData.ts).

### Property-owner experience

- Register with email and password.
- Sign in with email/password or Google.
- Submit a property with title, city, area, monthly price, room count, maximum occupancy, environment features, description, contact phone number, and images.
- Choose a listed city or enter a custom city.
- Upload up to five images; the form enforces a 5 MB per-image client-side limit.
- Choose a listing availability duration.
- Submit listings for administrator review.
- Track submissions in **My Listings**, grouped by All, Pending, Approved, and Rejected.
- View a rejection reason when one is supplied by an administrator.

### Administrator dashboard

- Review property submissions.
- Approve or reject pending listings.
- Manage listing visibility and remove listings.
- View listing status and dashboard summary information.
- Request an AI-assisted quality review to help identify incomplete descriptions or potentially suspicious content.

Administrator access is based on the user's role stored in Firestore. The public registration flow creates owner accounts; administrator roles must be assigned through a trusted process.

### AI-assisted writing and review

The project includes a Supabase Edge Function that proxies requests to Google's Gemini API.

- **Description improvement:** rewrites a property description to make it clearer and more professional while preserving its input language (English, Amharic, or Afaan Oromo).
- **Listing review assistance:** provides a concise quality assessment and flags possible gaps or suspicious information for an administrator to consider.

AI output is advisory. It does not replace human review or make the final approval decision.

### Language and appearance

- English, Amharic, and Afaan Oromo translations.
- Language preference persisted in browser local storage.
- Light and dark themes.
- Theme preference persisted in browser local storage.
- Responsive application layout with a collapsible sidebar and mobile navigation treatment.
- Animated interface elements and reusable UI components.

## Technology Stack

| Area | Technologies |
| --- | --- |
| Frontend | React 18, TypeScript |
| Build tool | Vite 5 |
| Routing | React Router |
| Styling and components | Tailwind CSS, shadcn/ui, Radix UI |
| UI animation | Framer Motion |
| Icons | Lucide React |
| Forms and validation | React Hook Form, Zod |
| Client-side data utilities | TanStack React Query |
| Authentication | Firebase Authentication |
| Application data | Cloud Firestore |
| Image hosting | Cloudinary |
| Server-side AI proxy | Supabase Edge Functions |
| AI provider | Google Gemini API |
| Testing | Vitest, Testing Library, jsdom |
| Code quality | ESLint |
| Deployment | Vercel |

**Architecture clarification:** Firebase Authentication and Cloud Firestore power the current user and property-listing workflows. Supabase is used for the Gemini proxy Edge Function. Although a generated Supabase client and database types are present in the repository, the current listing pages use Firebase rather than Supabase as their main data store.

## Application Routes

| Route | Description |
| --- | --- |
| / | Landing page, featured properties, and city highlights |
| /browse | Browse, search, and filter approved listings |
| /cities | Explore the city directory |
| /add-listing | Submit a property (owner account required) |
| /my-listings | Manage and track an owner's submissions |
| /admin | Listing moderation dashboard (admin role required) |
| /login | Sign in |
| /register | Create an owner account |

## Getting Started

### Prerequisites

- Node.js 18 or newer
- npm
- A Firebase project with Authentication and Cloud Firestore enabled
- A Cloudinary account with an unsigned image-upload preset
- A Supabase project for the AI Edge Function
- A Google Gemini API key

### 1. Clone the repository

\`\`\`bash
git clone https://github.com/usamaawol/ethiopiahomefind1.git
cd ethiopiahomefind1
\`\`\`

### 2. Install dependencies

\`\`\`bash
npm install
\`\`\`

### 3. Configure Firebase

The Firebase client is initialized in src/lib/firebase.ts.

1. Create a project in the [Firebase Console](https://console.firebase.google.com/).
2. Enable Email/Password authentication.
3. Enable Google authentication if you want to use Google sign-in.
4. Create a Cloud Firestore database.
5. Register a web app and replace the Firebase client configuration in src/lib/firebase.ts with your own project configuration.
6. Review the example rules in [FIREBASE_SETUP.md](./FIREBASE_SETUP.md), then adapt and test them against the current application before deploying.

The application uses these Firestore collections:

- users — user profile information and role.
- listings — property submissions, ownership, status, images, and expiry information.

New registrations are assigned the owner role. Create or promote administrator accounts only through a trusted administrative process. Never allow a public client to grant itself the admin role.

### 4. Configure Cloudinary

Image upload logic is located in src/lib/cloudinary.ts.

1. Create or select a Cloudinary account.
2. Create an **unsigned upload preset** in the Cloudinary dashboard.
3. Name the preset house_rent, which is the preset name currently used by the upload helper.
4. Confirm that the cloud name in src/lib/cloudinary.ts matches your Cloudinary account.

The form currently allows up to five images and checks for a maximum file size of 5 MB per image. For production, configure strict allowed formats, upload limits, and other restrictions in Cloudinary. Never expose a Cloudinary API secret in frontend code.

### 5. Configure Supabase and Gemini (optional)

Create a root-level .env file for the frontend Supabase settings:

\`\`\`env
VITE_SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=YOUR_SUPABASE_PUBLISHABLE_KEY
\`\`\`

The AI feature calls the gemini-proxy Edge Function on the configured Supabase project. The function reads the Gemini API key from a server-side secret.

Install and authenticate the Supabase CLI, then set the secret and deploy the function:

\`\`\`bash
supabase secrets set GEMINI_API_KEY=YOUR_GEMINI_API_KEY
supabase functions deploy gemini-proxy
\`\`\`

The function is located at supabase/functions/gemini-proxy/index.ts and currently requests the gemini-2.0-flash model.

**Important:** Do not place the Gemini API key in a VITE_ variable or commit it to the repository. Vite exposes variables prefixed with VITE_ to browser-side code.

### 6. Run the development server

\`\`\`bash
npm run dev
\`\`\`

Vite will display the local development URL in the terminal (typically http://localhost:5173).

## Available Scripts

| Command | Description |
| --- | --- |
| npm run dev | Start the local Vite development server |
| npm run build | Build the application for production |
| npm run build:dev | Build using development mode |
| npm run preview | Preview the production build locally |
| npm run lint | Run ESLint |
| npm test | Run the Vitest suite once |
| npm run test:watch | Run tests in watch mode |

## Project Structure

\`\`\`text
ethiopiahomefind1/
├── public/                      # Static public assets
├── src/
│   ├── assets/                  # Imported images and visual assets
│   ├── components/
│   │   ├── cards/               # Property and city cards
│   │   ├── layout/              # Shared layout and navigation
│   │   └── ui/                  # Reusable UI components
│   ├── contexts/                # Auth, language, and theme providers
│   ├── data/                    # City directory and sample property data
│   ├── hooks/                   # Reusable React hooks
│   ├── integrations/supabase/   # Generated Supabase client and types
│   ├── lib/                     # Firebase, Cloudinary, and utility modules
│   ├── pages/                   # Route-level pages
│   ├── test/                    # Test setup and example tests
│   ├── App.tsx                  # Providers and route definitions
│   └── main.tsx                 # Frontend entry point
├── supabase/
│   └── functions/
│       └── gemini-proxy/        # Gemini API proxy Edge Function
├── FIREBASE_SETUP.md            # Firestore rules and setup notes
├── package.json
├── tailwind.config.ts
├── vite.config.ts
└── vitest.config.ts
\`\`\`

## How the Listing Workflow Works

1. A property owner signs in and completes the listing form.
2. Selected property images are uploaded to Cloudinary.
3. The application creates a document in Firestore's listings collection with the owner's ID, image URLs, a pending status, and an expiry date.
4. An administrator reviews the submission and may use the AI review assistance.
5. The administrator approves or rejects the listing.
6. Approved, visible, unexpired listings are eligible to appear in the browse results.
7. The owner can track the submission in **My Listings**.

## Data and Project Notes

- The homepage's featured properties are currently read from local sample data in src/data/mockData.ts.
- The city directory and city listing counts are also local sample data.
- The browse page retrieves approved listings from Firestore.
- Owner submissions and the My Listings page use Firestore.
- Firebase Authentication handles email/password and Google sign-in.
- Cloudinary stores uploaded property images; Firestore records their resulting URLs.
- The AI description feature requires the deployed Supabase Edge Function and a valid Gemini secret.
- The generated Supabase client is included, but the current property-listing workflow is Firebase-based.
- The repository includes a Vitest setup and an example test; add broader tests for critical workflows as the project develops.

## Security and Production Checklist

Before using the application with real users and property data:

- Review and test Firestore security rules, including owner-level access and admin-only moderation.
- Ensure users cannot change their own role to admin.
- Enforce authorization in Firestore rules, not only by hiding pages in the frontend.
- Restrict Cloudinary unsigned uploads by file type, size, and preset settings.
- Keep Gemini credentials in Supabase secrets and never expose them in frontend code.
- Add appropriate validation, abuse prevention, and rate limiting to public and AI-powered endpoints.
- Decide how and when owner phone numbers should be exposed to visitors.
- Establish a real listing-verification process before describing properties as verified.
- Keep local environment files out of version control. A root-level .env file is currently present in this repository; inspect it for sensitive values, remove it from tracking if appropriate, rotate any exposed secrets, and provide a sanitized .env.example for setup guidance.

## Current Scope and Future Improvements

Possible next steps include:

- Replacing homepage sample properties with live Firestore-backed featured listings.
- Connecting city counts to real listing data.
- Expanding automated tests for authentication, property submission, filtering, and moderation.
- Strengthening listing validation, expiry handling, and trust-and-safety workflows.
- Documenting production Firestore rules and testing them with the Firebase Emulator Suite.
- Adding operational monitoring and clearer error handling for external service failures.

## Contributing

Contributions, suggestions, and bug reports are welcome.

1. Fork the repository.
2. Create a feature branch: git checkout -b feature/your-feature.
3. Make your changes and run the relevant checks:

   \`\`\`bash
   npm run lint
   npm test
   npm run build
   \`\`\`

4. Commit your work and push the branch.
5. Open a pull request with a clear description of the change.

## License

No license is currently specified in this repository. Until a license is added, the default copyright restrictions apply.

## Author

**Usama Awol** — Full-Stack Developer

- Portfolio: [usamaawol.pro.et](https://usamaawol.pro.et)
- GitHub: [@usamaawol](https://github.com/usamaawol)
- LinkedIn: [usama-awol](https://www.linkedin.com/in/usama-awol)

---

<p align="center">
  Built to make rental-property discovery more accessible across Ethiopia.
</p>
