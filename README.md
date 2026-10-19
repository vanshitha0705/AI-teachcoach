# TeachCoach

AI-powered teaching assistant and professional-development coaching platform for educators. TeachCoach helps teachers plan lessons, reflect on classroom practice, close pedagogical gaps, and build communication skills — backed by an ML pipeline that turns reflection text into actionable coaching recommendations.

## Features

| Page | Route | What it does |
|---|---|---|
| Landing | `/landing` | Standalone marketing page |
| Dashboard | `/`, `/dashboard` | Habit streak, weekly digest, today's schedule, key-basics cue, quick 2-minute reflection with mood analysis, anonymous student feedback, speaking cue, 13-week activity heatmap |
| Planner | `/planner` | Lesson scheduling and topic prep |
| Student Hub | `/students` | Class announcements/broadcasts, student inquiries, anonymous feedback vault |
| Communication Skills | `/skills` | Voice/delivery and questioning guides plus an interactive response simulator (poor → masterful ratings) |
| Pathways | `/pathways` | Self-paced PD courses: Active Learning, Differentiated Instruction, Inquiry-Based Learning, Classroom Management |
| Reflections | `/reflections` | Daily journal with prompts and tags |
| Coach | `/coach` | AI mentor with tips and analysis |
| Goals | `/goals` | Habit and pedagogy goals with progress tracking |
| Profile / Settings | `/settings` | Educator profile and app settings |

Additional UI: sidebar navigation, topbar, click-through profile drawer, and an onboarding questionnaire modal.

## Tech Stack

- **React 19** + **Vite 6**
- **Tailwind CSS** — custom pastel design system in `tailwind.config.js`
- **react-router-dom 7** — routing
- **framer-motion** — animations
- **recharts** — charts
- **lucide-react** (icons), **clsx** / **tailwind-merge**
- **axios** — HTTP client, ready for a future backend (not yet wired up)

## Getting Started

Requires Node.js 18+.

```bash
# Install dependencies
npm install

# Start the dev server
npm run dev

# Production build
npm run build

# Preview the production build
npm run preview
```

> **Note:** the repo currently has no root `index.html` (only `dist/index.html` exists), so `npm run dev` will not start until one is added. Either re-run `npm run build`, or add a standard Vite `index.html` at the project root referencing `/src/main.jsx`.

## Project Structure

```
├── src/
│   ├── pages/                 # Route-level pages (10 routes)
│   ├── components/
│   │   ├── layout/            # Sidebar, Topbar, ProgressRing
│   │   ├── profile/           # ProfileDrawer
│   │   └── auth/              # EducatorOnboardingModal
│   ├── data/mockData.js       # Mock educator profile, schedule, feedback, etc.
│   ├── hooks/                 # useMoodAnalysis (lexicon-based reflection mood scoring)
│   ├── styles/index.css       # Tailwind entry
│   ├── App.jsx                # Routing and app shell
│   └── main.jsx               # Entry point
├── ml/                        # Python ML pipeline (see below)
│   ├── run_pipeline.py        # Runs all 3 models in sequence
│   ├── model1..3_*.py
│   ├── data/                  # Input CSVs and generated outputs
│   ├── requirements.txt
│   └── report.md              # Analytics snapshot of pipeline outputs
├── dist/                      # Build output
├── TeachCoach_Project_Report.docx
├── tailwind.config.js
└── vite.config.js
```

## ML Pipeline

Three Python models analyze teacher reflection and engagement data to generate coaching insights. All scripts live in `ml/` and write results to `ml/data/`.

| Model | Script | Purpose | Outputs |
|---|---|---|---|
| 1 — BERTopic | `model1_bertopic.py` | Detects pedagogical gaps from reflection text | `reflection_with_topics.csv`, `teacher_gap_profiles.csv`, `gap_distribution.png` |
| 2 — Collaborative Filtering | `model2_collaborative_filtering.py` | Recommends PD pathways based on similar teachers | `pathway_recommendations.csv`, `pathway_analysis.png` |
| 3 — XGBoost | `model3_xgboost.py` | Predicts teacher churn risk | `churn_predictions.csv`, `churn_analysis.png` |

Input datasets (`teacher_reflections.csv`, `teacher_pathways.csv`, `teacher_churn.csv`) are also stored in `ml/data/`.

### Running the pipeline

Requires Python 3.9+.

```bash
# Install dependencies
pip install -r ml/requirements.txt

# Run all 3 models in sequence
python ml/run_pipeline.py
```

Each model can also be run individually, e.g. `python ml/model1_bertopic.py`.

### ML data report

See **[`ml/report.md`](ml/report.md)** for a full admin/analytics snapshot of the latest pipeline outputs: gap distribution, pathway recommendations/engagement, churn-risk breakdown, and key insights (200 teachers, 200 reflections, 400 pathway interactions, 300 churn-scored records).

## Data & State

- The educator profile, goals, focus areas, schedule, reflections, and feedback come from **mock data** (`src/data/mockData.js`) — there is no backend API yet.
- Profile edits are persisted to `localStorage` under the `teachcoach_profile` key.
- Reflection mood analysis is a client-side keyword lexicon (`src/hooks/useMoodAnalysis.js`), not an ML model.

## Notes

- The root `index.html` is missing — see the warning under [Getting Started](#getting-started).
- `package.json` still carries the placeholder project name `satellite-collision-risk-assessment`; the app itself is TeachCoach.
