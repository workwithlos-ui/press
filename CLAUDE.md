# CLAUDE.md - PRESS

## Project Overview
PRESS (Platform Revenue Engine for Scaled Stories) is a high-volume content production engine. It ingests a brief, runs it through a 3-stage pipeline (Strategist → Writer → Quality Controller), and outputs platform-native content for LinkedIn, Twitter/X, Instagram, Email, and Blog. Voice DNA analysis ensures outputs match the creator's style. An anti-slop rulebook (12 rules) is enforced at the writer stage. It replaces a $4.5K/mo content agency.

## Tech Stack
- **Framework:** Next.js (App Router)
- **Language:** TypeScript
- **AI:** OpenAI (`gpt-4.1-mini`), Google Gemini (`gemini-2.5-flash`) via raw fetch
- **Context:** Supabase REST API
- **Styling:** Tailwind CSS

## Architecture

3-stage pipeline in `src/app/api/generate/route.ts`:

1. **Strategist** (`gpt-4.1-mini`) - Receives brief + ICPs + proof bank from shared context. Outputs: angle, hook type, narrative structure, proof point selection, platform-specific guidance.

2. **Writer** (`gpt-4.1-mini`) - Receives strategist output + voice DNA + anti-slop rulebook (12 rules) + creator framework (if selected). Produces full draft. The 12 anti-slop rules are injected directly into the system prompt.

3. **Quality Controller** (`gemini-2.5-flash`) - Receives final draft. Scores 6 dimensions (clarity, hook strength, CTA effectiveness, voice match, platform fit, proof density) on a 0-100 scale. Flags anything below 70 with specific revision notes.

Creator frameworks (Hormozi, Brunson, Ferriss, Welsh, Koe) modify the narrative structure at the writer stage - they are additive, not replacements for voice DNA.

## Key Files

| File | Description |
|------|-------------|
| `src/app/api/generate/route.ts` | **The pipeline.** All 3 stages, model routing, error handling. |
| `src/lib/shared-context.ts` | Supabase context fetcher - voice rules, ICPs, proof bank. 5-min cache + fallback. |
| `src/lib/anti-slop.ts` | 12 anti-slop rules injected at writer stage - edit here to update the rulebook |
| `src/lib/frameworks.ts` | Creator framework prompt modifiers - Hormozi, Brunson, Ferriss, Welsh, Koe |
| `src/lib/platforms.ts` | Platform format constraints (character limits, structure, CTA placement) |
| `src/app/page.tsx` | Content brief input UI - platform selector, framework picker, voice DNA input |

## Shared Context Layer
All ELIOS products read from a shared Supabase table (`shared_context`) containing:
- Business info (name, founder, priority, revenue target)
- Audiences (4 ICPs with pains, outcomes, objections, triggers)
- Proof bank (5 case studies with strength scores)
- Voice rules (tone, forbidden words, signature phrases)
- Platform specs, GRIP weights, offers, constraints

The shared context fetcher is in `lib/shared-context.ts`. It uses raw fetch to Supabase REST API with 5-minute caching and graceful fallback.

## Conventions
- No new dependencies without explicit approval
- TypeScript strict mode
- Raw fetch for all AI API calls - no OpenAI or Google SDKs
- Strategist and Writer use GPT; Quality Controller uses Gemini - do not swap these without testing quality impact
- The anti-slop rulebook in `anti-slop.ts` is the authoritative list - do not hardcode rules elsewhere
- Push to main for auto-deploy via Vercel

## Testing
1. Run `npm run dev`, submit a content brief for at least two different platforms
2. Confirm all 3 stages complete and the response includes: strategy, draft, and quality scores
3. Check quality scores are populated across all 6 dimensions
4. Test with a creator framework selected - verify the framework modifies tone/structure
5. Confirm Supabase context loads (check logs for cache hits vs. fresh fetches)
