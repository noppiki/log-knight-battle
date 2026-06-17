# Log Knight Battle — Improvement Log

Running log of quality improvements. The hourly quality loop appends one line per run.
Read this before each run to avoid repeating work; pick the highest-impact thing NOT yet done.

## Current state (baseline as of 2026-06-17)
- Core: 2-player seated frying-pan brawler on a log; rigid-pendulum tap-to-swing combat; balance = LIFE (0 → fall off → lose); physical knockback can ring-out; "流れパン" recoil-hit is an INTENTIONAL gray-zone mechanic (do not gate hits on tap).
- Specials: 4 (Mega Smash / Counter Stance / Dash Tackle / Anchor), chosen on a SELECT screen; SP gauge fills via combat; special key P1=F, P2=/. Specials shake hard (addBigShake) + big onomatopoeia.
- Timer: 99s round; time-up decided by higher balance; tie → draw replay. Best-of-3.
- Modes: 1P vs CPU (reactive AI), 2P LOCAL, ONLINE (Cloudflare Worker+DO relay, host-authoritative; wss://lkb-relay.noppiki.workers.dev/r/<code>).
- Art: generated pixel-art ASSETS in assets/ (bg.jpg Nordic dusk; knight_red/blue sprites; title_crest; 4 special icons). Knights drawn as sprites rotated about the seat; pan-tip hit geometry derived from sprite anchors. Do NOT revert to procedural.
- Juice: hit-stop, screen shake (normal toned down ~60%, specials strong), sparks, floaters, WebAudio SFX.
- Deployed to GitHub Pages: https://noppiki.github.io/log-knight-battle/

## Known limitations / candidate improvement areas
- No netcode prediction/interpolation (guest sees RTT input lag); no reconnect.
- AI difficulty is a reasoned medium, not tuned against real play.
- Live 2-browser online play not yet verified in this env (agent-browser can't drive rAF).
- Possible polish: round-intro/victory presentation, sound design depth, control remapping, mobile/touch, colorblind cues, performance audit, more special VFX variety.

## Standing policy (producer directive 2026-06-17)
**Prefer generated image assets over procedural Canvas drawing.** Any visual element still drawn procedurally (HUD gauges/frames/pips, the log, card frames, banner ribbons, particles, onomatopoeia, icons, the speaker indicator, the title controls strip, etc.) is a candidate to replace with a generated pixel-art asset that fits the established Nordic dusk aesthetic. Procedurally-driven MOTION is fine — but the pixels it draws should be sprites where reasonable. Reject AI-slop; commit to the world.

Still procedural (replace candidates, roughly in priority order):
- HUD frame, balance/SP gauges, win pips, timer slab, "TIME" label
- Speaker / mute indicator
- The log (cylinder + bark + end-grain rings)
- SELECT card frames + cursor outlines
- Round-over / Match-over banner backgrounds + restart button
- Hit-spark particles + onomatopoeia floaters (consider a sprite-based stamp system)
- Mode-selector slabs on the title

## Run log
- 2026-06-17 — Online host wait-for-opponent no longer times out (watchdog now only guards reaching the relay; connect timeout 8s→12s). Decluttered the title screen (removed the redundant two-column controls panel; rebalanced spacing; prompt → "PRESS ENTER TO START").
- 2026-06-17 — Audio: procedural Nordic-dusk BGM (A1/A2 drone with LFO-swept lowpass + A-minor-pentatonic triangle bell arpeggio through Schroeder feedback-delay reverb, ~75 BPM, sparse with rare octave-up shimmer accents every 8-16s); global M-key mute toggle (single `globalGain` node mutes BGM + SFX together, 0.05s linear ramp; persists across reloads under `localStorage["lkb_muted"]`; gated against the JOIN room-code input); procedural top-right speaker indicator with red diagonal slash when muted; BGM lazily starts on first user gesture (autoplay-safe) and tears down its scheduler on tab hide / restarts on show to avoid frozen-clock event bursts.
- 2026-06-17 — Replaced procedural log drawing (cylinder bands + bark grooves + end-grain ellipse rings) with generated Nordic-style WebP sprite (assets/log.webp, 1800×400, transparent background); registered in ASSET_SRCS so loading screen counts it as a pip; drawLog() sprite-first with full procedural fallback if image not yet loaded; knight seat coordinate logTopY and TUNE.logThickness rectangle unchanged so combat geometry is unaffected; drone-and-bell BGM and HUD intact.
