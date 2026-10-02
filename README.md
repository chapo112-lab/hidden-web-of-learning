# The Hidden Web of Learning (static site)

An interactive 3D web of statistically scored connections in the synthetic Kaggle dataset
“500k Student Performance and Behavior Dataset” by Waddah Ali (CC BY-NC-SA 4.0).
This derivative work is shared under the same CC BY-NC-SA 4.0 license, for non-commercial use only.

## Run locally
    cd /workspace/student-viz/site && python3 -m http.server 8765
    # open http://localhost:8765

## Files
- index.html, assets/app.js (bundled three.js + 3d-force-graph + app, ~1.4 MB), assets/style.css. Source lives in /workspace/student-viz/web/src (outside this repo)
- data/sample.json: 30k sampled rows (columnar, quantized), ~2.3 MB
- data/model.json: GBM importances and partial dependence, plus baseline associations
- data/connections.json: output of analysis/score_connections.py (scored, replicated findings)
Total is about 3.9 MB uncompressed, or about 1 MB gzipped. There is no build step needed to host; it is fully static.

## Design (editorial redesign, Oct 2026)
- Nodes: physically shaded spheres (MeshPhysicalMaterial with clearcoat, sheen and a fresnel rim) lit by a RoomEnvironment/PMREM map, a camera-following key light and a cool rim light. Bloom is low and only catches highlights. Size = predictive importance; colour = factor group (muted, Okabe–Ito-derived, colour-blind safe; gold is the one accent).
- Links: custom tapered tubes whose colour blends from source to target group. Width and opacity encode strength. Arched = non-linear, dashed = negative linear, dotted = spokes to multi-factor findings. Light pulses travel only along strong links (>= 0.28).
- Motion: spring-scaled nodes, eased fades on hover/toggle/filter (nodes and links fade out before they are removed), damped orbit, slow idle rotation. prefers-reduced-motion turns off rotation, pulses, springs and camera tweens.
- Atmosphere: gradient backdrop, depth fog, faint floor grid with contact shadows, vignette and film grain.
- Type and UI: Source Serif 4 + Inter (Google Fonts), masthead with kicker, deck and byline, tabbed control panel with custom switches and sliders, magazine-style hover callouts with leader lines, and a detail card that lists findings in plain English with effect sizes.

## Rebuild
    cd /workspace/student-viz && . .venv/bin/activate
    python analysis/score_connections.py "data/Student Performance and Behaviour.csv" --target Final_Exam_Score --exclude-higher Midterm_Mark --out analysis/connections.json
    python analysis/precompute_site.py && cp analysis/connections.json site/data/
    cd web && npx esbuild src/app.js --bundle --minify --format=iife --target=es2020 --outfile=../site/assets/app.js

## Deploy (not done; each option needs your own account)
- **Netlify**: drag the `site/` folder onto https://app.netlify.com/drop, or run `npx netlify-cli deploy --dir site --prod`.
- **Vercel**: `cd site && npx vercel --prod` (framework preset: Other, no build command, output directory `.`).
- **GitHub Pages**: push the contents of `site/` to a repo (or a `docs/` folder), then enable Settings → Pages → Deploy from branch.
- **Cloudflare Pages**: `npx wrangler pages deploy site`.
All asset paths are relative, so the site works from a sub-path such as username.github.io/repo/.
