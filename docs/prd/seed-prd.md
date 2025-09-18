# Seed PRD — Café Music System (MVP v0.1)

Status: Draft
Owner: You + Codex
Source: See `cafe-music-system-design.md`

## 1) Summary
An in-café music system where guests influence the now‑playing queue using their streaming tastes, with a Bitcoin-first layer enabling sats-based boosts and café loyalty. MVP targets one café, Spotify only, “Turn‑Taking” mode, and a simple Lightning integration.

## 2) Goals
- Deliver an enjoyable, smoothly transitioning shared playlist that reflects present guests.
- Demonstrate Bitcoin value-influence experiences (zap to lightly boost influence).
- Prove barista control: guardrails, veto/skip, simple presets.
- Pilot-ready in one café with minimal ops burden.

## 3) Non-Goals (MVP)
- Multi-café tenancy and advanced admin.
- Apple Music support, social feeds, or public playlist publishing.
- Full-blown ML embeddings; start with Spotify Audio Features.
- On-prem audio licensing management (use a compliant provider/account).

## 4) Personas
- Guest: scans QR, connects Spotify (read-only) or picks genres, adjusts Energy/Openness.
- Barista: oversees queue, sets guardrails/presets, skips when needed.
- Owner: reviews lightweight weekly summary (engagement, skips, dwell proxy).

## 5) Core Use Cases
1. Guest joins session and contributes taste signal.
2. System selects next track using weighted turn‑taking with transition smoothness.
3. Barista applies guardrails/presets and can veto/skip.
4. Guest sends a small sats boost to increase next‑turn weight (within fairness limits).

## 6) Functional Requirements (MVP)
- Session
  - Create/reset daily session; track active guests and presence decay.
  - Maintain 2+ track buffer and apply cooldowns (song/artist/time‑based).
- Guest
  - QR → PWA → Spotify OAuth (read scope) or quick genre pick.
  - Controls: Energy (0–1), Openness (0–1), Explicit filter toggle.
- Selection
  - Mode: Turn‑Taking with α weight for current guest (default 0.9), others at 0.05 each.
  - Rank candidates by (1) similarity to target vector, (2) transition smoothness (BPM/key/energy/valence), (3) novelty, (4) popularity cap.
- Barista Dashboard
  - Presets (morning/afternoon/evening), guardrails (genres/BPM/explicit), skip/veto.
  - See “influence attribution” for next 3 tracks.
- Bitcoin
  - Custodial Lightning for MVP (e.g., LNbits instance) with per-session guest balance.
  - Zap → temporary influence boost (bounded; no queue-jump beyond fairness).
- Streaming Output
  - Control a commercial/house Spotify account (e.g., Spotify Connect) for playback.

## 7) Non-Functional Requirements
- Availability: queue selection resilient to brief network hiccups; recover state on restart.
- Latency: selection < 800ms per pick; always keep ≥1 track buffered.
- Privacy: do not store personal identities beyond a pseudonymous session ID.
- Observability: basic logs + metrics for queue decisions and skips.

## 8) Success Metrics (Pilot)
- ≥ 8 guests join/day on average; ≥ 30% connect Spotify.
- Skip rate ≤ 12% per hour; average transition BPM delta < 15.
- ≥ 10 sats boosts/day; at least 50% barista satisfaction (manual feedback).

## 9) Constraints & Assumptions
- Licensing: use a compliant commercial playback account/provider.
- Spotify only (read features + control house device via Connect).
- Custodial Lightning for speed; export via LNURL‑withdraw.

## 10) Risks & Mitigations
- Licensing confusion → clearly separate “selection” from playback account.
- Harsh genre swings → enforce transition and genre-distance clamps.
- Payment friction → seed guests with small sats voucher at join.
- Abuse/spam boosts → rate limit and cap influence magnitude.

## 11) Out of Scope (MVP)
- Fedimint custody, Nostr identity, multi-room scheduling, artist tipping splits.

## 12) Acceptance Criteria (Sample)
- Guest joins, adjusts knobs, and within 2 turns hears an influenced track.
- Barista presets adjust queue profile within 3 tracks (observable in features).
- Boost increases the current guest’s turn weight (bounded) and decays in ≤ 10 min.
- Queue never repeats the same artist within 20 minutes; no explicit content if disabled.

## 13) Open Questions (for us to decide)
1. Which custodial Lightning path first (LNbits vs Breez SDK)?
2. Exact boost semantics (sat→weight mapping, caps, decay curve)?
3. Presence detection: ping interval and timeouts (Wi‑Fi vs web heartbeat)?
4. Barista device target (iPad PWA?) and minimal offline behavior?
5. Data retention window for per-track logs (7, 30, or 90 days)?
6. Are explicit lyrics always off by default for all hours?

## 14) Phases
- v0.1 Prototype: offline selection simulation + simple web UIs.
- v0.2 Pilot: one café, Spotify control + LNbits boosts, basic metrics.
- v0.3 Harden: stability, metrics, and consider one additional mode (Blend).

