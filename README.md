# PRESS
**Platform Revenue Engine for Scaled Stories**

PRESS is a synthetic content agency - it replaces the $4.5K/mo retainer by producing high-volume, on-brand content across every platform through a 3-stage production pipeline with built-in quality control.

## What PRESS Does

- **Analyzes voice DNA** from sample content and injects it into every output - so content sounds like you, not like AI
- **Enforces 12 anti-slop rules** at the writer stage to kill clichés, filler phrases, and generic AI patterns before they ship
- Produces platform-native output for **LinkedIn, Twitter/X, Instagram, Email, and Blog** - each formatted for the platform's mechanics
- **Quality-scores** every output across 6 dimensions and flags anything below threshold for revision

## Architecture

Next.js App Router. Content production runs through a 3-stage pipeline in `src/app/api/generate/route.ts`:

1. **Strategist** - frames the angle, hook, and narrative structure using ICPs and proof bank from shared context
2. **Writer** - drafts the content using voice DNA + the 12 anti-slop rules
3. **Quality Controller** - scores 6 dimensions (clarity, hook strength, CTA, voice match, platform fit, proof density) and flags issues

Creator frameworks available (Hormozi, Brunson, Ferriss, Welsh, Koe) - selectable per generation.

Models: `gpt-4.1-mini` (strategist + writer), `gemini-2.5-flash` (quality controller).

## Key Files

| File | Purpose |
|------|---------|
| `src/app/api/generate/route.ts` | 3-stage pipeline: Strategist → Writer → Quality Controller |
| `src/lib/shared-context.ts` | Supabase context fetcher (voice rules, ICPs, proof bank) with 5-min cache |
| `src/app/page.tsx` | Main content brief input UI |
| `src/lib/anti-slop.ts` | 12-rule anti-slop rulebook injected at the writer stage |
| `src/lib/frameworks.ts` | Creator framework definitions (Hormozi, Brunson, Ferriss, Welsh, Koe) |
| `src/lib/platforms.ts` | Platform-specific format rules and constraints |

## Environment Variables

```env
OPENAI_API_KEY=                  # GPT-4.1-mini for strategist + writer stages
GOOGLE_GENERATIVE_AI_API_KEY=    # Gemini 2.5 Flash for quality control stage
NEXT_PUBLIC_SUPABASE_URL=        # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=   # Supabase anon key
```

## Development

```bash
npm install
npm run dev
```

## Deployment

Auto-deploys from `main` branch via Vercel.
Live: [content-factory-ochre.vercel.app](https://content-factory-ochre.vercel.app)

## Part of the ELIOS Suite

PRESS is one of five synthetic employees in the ELIOS product suite:
- **PAID** - Professional Analytics Intelligence Dashboard (Growth Diagnostic)
- **SAGE** - Strategic Automated Growth Editor (Content Strategy)
- **PRESS** - Platform Revenue Engine for Scaled Stories (Content Production)
- **SUITE** - Synthetic Unified Intelligence Team of Executives (Advisory Board)
- **RISE** - Revenue Intelligence Strategy Employee (Growth Analyst)

All five share a context layer via Supabase, so they know the same business data, ICPs, proof bank, and voice rules.
