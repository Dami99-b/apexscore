# ApexScore

A credit risk assessment platform built for financial institutions. ApexScore combines behavioral analytics, traditional financial data, and intelligent recommendations to evaluate loan applicants in real-time.

![ApexScore](https://img.shields.io/badge/Status-Production-green) ![React](https://img.shields.io/badge/React-18.3-blue) ![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue) ![FastAPI](https://img.shields.io/badge/FastAPI-Backend-teal)

---

## Overview

ApexScore addresses a core challenge in lending: how do you accurately assess creditworthiness when traditional credit bureau data is incomplete or unavailable? The platform ingests multiple data signals—device fingerprints, SIM registration status, geolocation patterns, network metadata, and loan repayment history—then synthesizes them into a single, actionable risk score.

Financial analysts search applicants by email, receive instant risk profiles, and can export detailed PDF reports for compliance and record-keeping.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                           Frontend                                   │
│                    React + Vite + TypeScript                        │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│  │   Landing    │  │  Dashboard   │  │    Admin     │              │
│  │    Pages     │  │   Search     │  │   Settings   │              │
│  └──────────────┘  └──────────────┘  └──────────────┘              │
│                              │                                       │
└──────────────────────────────┼───────────────────────────────────────┘
                               │
              ┌────────────────┴────────────────┐
              │                                 │
              ▼                                 ▼
┌──────────────────────────┐    ┌──────────────────────────────────┐
│     Cloud Backend        │    │      FastAPI Risk Engine         │
│   (PostgreSQL + Auth)    │    │   apexscore.onrender.com         │
│                          │    │                                  │
│  • Authentication        │    │  • Applicant Data Generation     │
│  • User Profiles         │    │  • BSI Score Calculation         │
│  • Search History        │    │  • Recommendation Engine         │
│  • Edge Functions        │    │  • Credit Report Simulation      │
│  • Row-Level Security    │    │  • Financial Profile Analysis    │
│                          │    │                                  │
└──────────────────────────┘    └──────────────────────────────────┘
```

---

## Core Features

### Applicant Search & Risk Assessment
- Search loan applicants by email address
- Real-time risk scoring with color-coded indicators (Low/Medium/High)
- Full-screen responsive applicant detail view across all devices
- Intelligent lending recommendations with reasoning

### Behavioral Stability Index (BSI)
The BSI measures non-traditional creditworthiness signals:
- **Location Consistency** – How stable is the applicant's declared location vs. actual IP geolocation?
- **Device Stability** – Single device usage vs. frequent device switching
- **SIM Stability** – SIM card changes and registration verification
- **IP Region Match** – Does network location match declared address?
- **Travel Frequency** – Unusual mobility patterns that may indicate fraud

### Traditional Financial Data (TFD)
- Outstanding debt tracking across institutions
- Complete loan history with repayment status
- Bank account verification
- Multi-currency support (NGN, USD, GBP)

### Financial Profile & Credit Reports
- Monthly income with verification source
- Detailed expenditure breakdown (Housing, Transportation, Utilities, etc.)
- Debt-to-income ratio calculation
- Credit bureau reports (Experian, Equifax, TransUnion, etc.)
- Credit score and rating assessment

### Recommendations
Each applicant receives an intelligent lending recommendation:
- Decision (Approve / Approve with Conditions / High Risk)
- Recommended and maximum loan amounts
- Interest rate range based on risk
- Repayment period suggestions
- Detailed reasoning and conditions

### Search History
- Persistent search history per user (stored in database)
- Quick access to previous applicant lookups
- Delete individual records or clear all history

### PDF Export
- Export individual applicant reports
- Batch export multiple applicants
- Professional formatting with risk indicators
- Timestamped for compliance

### Admin Settings
- Configurable risk rules and thresholds
- Custom banking form templates with field management
- Global compliance policies
- Open Banking document request simulation
- Region-filtered financial institution selection
- Request history with re-download capability

### Chat Interface
- Email-based applicant search via chat
- Persistent message history
- Full-screen dialog on all device sizes
- Clear conversation option

---

## Tech Stack

### Frontend
| Technology | Purpose |
|------------|---------|
| React 18 | UI framework with hooks |
| Vite | Build tool and dev server |
| TypeScript | Type safety throughout |
| Tailwind CSS | Utility-first styling |
| shadcn/ui | Component library (Radix primitives) |
| React Router v6 | Client-side routing |
| TanStack Query | Server state management |
| Lucide React | Icon system |
| jsPDF | Client-side PDF generation |
| React Helmet Async | SEO meta management |
| Sonner | Toast notifications |

### Cloud Backend
| Component | Purpose |
|-----------|---------|
| PostgreSQL | User profiles, search history |
| Row-Level Security | Data isolation per user |
| Edge Functions | Password recovery, extensibility |
| Authentication | Email/password + Google OAuth |

### Risk Engine (FastAPI)
| Endpoint | Purpose |
|----------|---------|
| `GET /api/search?email=` | Search applicant by email |
| `GET /api/applicant/{id}` | Get applicant by ID |
| `GET /api/applicants` | List all applicants |
| `GET /api/stats` | Platform statistics |
| `GET /api/applicant/{id}/financial-profile` | Financial data |
| `GET /api/applicant/{id}/credit-report` | Credit bureau data |
| `GET /api/applicant/{id}/full-report` | Complete applicant report |

The FastAPI backend runs on [apexscore.onrender.com](https://apexscore.onrender.com) and handles:
- Synthetic applicant data generation for testing
- BSI and ApexScore calculation algorithms
- Recommendation engine
- Multi-country support (Nigeria, USA, UK)

---

## Database Schema

### profiles
Stores user information after signup:
```sql
- id: UUID
- user_id: UUID (links to auth.users)
- email: TEXT
- first_name: TEXT
- last_name: TEXT
- work_email: TEXT
- institution: TEXT
- department: TEXT
- position: TEXT
- avatar_url: TEXT
- security_question: TEXT
- security_answer: TEXT
```

### search_history
Tracks every applicant search per user:
```sql
- id: UUID
- user_id: UUID
- applicant_email: TEXT
- applicant_name: TEXT
- apex_score: INTEGER
- risk_level: TEXT
- applicant_data: JSONB (full applicant object)
- searched_at: TIMESTAMP
```

Both tables have RLS policies ensuring users can only access their own records.

---

## Project Structure

```
src/
├── assets/              # Static images (logo, hero background)
├── components/
│   ├── dashboard/       # ApplicantDetail, ScoreExplanation
│   ├── landing/         # Hero, Features, HowItWorks, CTA, Footer
│   └── ui/              # shadcn components
├── hooks/
│   ├── useAuth.tsx      # Authentication context
│   ├── useChatMessages.tsx  # Chat persistence
│   ├── useSearchHistory.tsx # Search history CRUD
│   └── use-mobile.tsx   # Responsive breakpoints
├── integrations/
│   └── supabase/        # Database client + types
├── lib/
│   ├── api.ts           # FastAPI client + types
│   ├── financialInstitutions.ts  # Global bank database
│   ├── openBanking.ts   # Open Banking utilities
│   ├── pdfExport.ts     # PDF generation
│   ├── riskCalculator.ts # Score breakdown algorithms
│   └── utils.ts         # cn() helper
├── pages/
│   ├── Index.tsx        # Landing page
│   ├── Auth.tsx         # Login/Signup forms
│   ├── AuthCallback.tsx # OAuth redirect handler
│   ├── Home.tsx         # Logged-in home with chat
│   ├── Dashboard.tsx    # Search + history + applicant view
│   ├── Profile.tsx      # User settings
│   ├── AdminSettings.tsx # Risk rules, forms, requests
│   └── NotFound.tsx     # 404 page
└── index.css            # Tailwind + custom design tokens

supabase/
├── config.toml          # Database configuration
└── functions/
    └── security-password-recovery/  # Password reset edge function
```

---

## Design System

ApexScore uses a custom fintech-inspired theme with teal/cyan accents:

### Colors (HSL)
- **Primary**: `173 80% 35%` (teal)
- **Accent**: `180 100% 40%` (cyan)
- **Success**: `142 76% 36%` (green)
- **Warning**: `38 92% 50%` (amber)
- **Destructive**: `0 72% 51%` (red)

### Typography
- **Headlines**: Space Grotesk (400–700)
- **Body**: Inter (300–800)

### Custom Components
- `.glass-card` – Frosted glass effect for cards
- `.gradient-text` – Primary gradient on text
- `.glow-effect` – Animated border glow on hover
- `.shimmer` – Loading skeleton animation

Dark mode is fully supported with automatic system detection.

---

## Running Locally

```bash
# Clone the repository
git clone <repo-url>
cd apexscore

# Install dependencies
npm install

# Start development server
npm run dev
```

The app runs at `http://localhost:8080`.

### Environment Variables

Required environment variables:
- `VITE_SUPABASE_URL` – Database connection URL
- `VITE_SUPABASE_PUBLISHABLE_KEY` – Public API key
- `VITE_SUPABASE_PROJECT_ID` – Project identifier

---

## Routes

| Path | Description | Auth Required |
|------|-------------|---------------|
| `/` | Landing page with features and CTA | No |
| `/auth` | Login and signup forms | No |
| `/auth/callback` | OAuth redirect handler | No |
| `/home` | Logged-in home with quick actions | Yes |
| `/dashboard` | Search applicants, view history | Yes |
| `/profile` | Edit user settings | Yes |
| `/admin` | Configure risk rules and forms | Yes |

---

## FastAPI Backend Details

The backend generates realistic synthetic data for demonstration. Key algorithms:

### ApexScore Calculation
```
ApexScore = (BSI Average × 0.6) + (TFD Score × 0.4)
```

Where:
- **BSI Average** = (Location + Device + SIM) / 3
- **TFD Score** starts at 60 and adjusts based on repayment history, defaults, and outstanding debt

### Risk Levels
- **Low**: ApexScore ≥ 75
- **Medium**: ApexScore 50–74
- **High**: ApexScore < 50

### Supported Countries
Each country has localized data:
- **Nigeria** (+234): GTBank, Access Bank, Opay, Moniepoint, etc.
- **USA** (+1): Chase, Bank of America, Wells Fargo
- **UK** (+44): Barclays, HSBC, Monzo, Revolut

### Loan Statuses
- Paid On Time
- Paid Early
- Paid Late
- Defaulted
- Restructured
- Active

---

## Authentication

ApexScore supports two authentication methods:

1. **Email/Password** – Standard signup with institutional details (work email, department, position)
2. **Google OAuth** – One-click sign-in with automatic profile creation

Security questions are implemented as an account recovery mechanism for email-based accounts.

---

## Deployment

The frontend is deployed on Vercel with SPA routing configured in `vercel.json`:
```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

The FastAPI backend is deployed on Render at `apexscore.onrender.com`.

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -m 'Add new feature'`)
4. Push to branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## License

Proprietary – All rights reserved.

---

## Support

For issues or questions, open a GitHub issue or contact the development team.
