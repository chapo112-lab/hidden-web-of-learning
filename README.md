# The Hidden Web of Learning (static site)

An interactive 3D web of statistically scored connections in the synthetic Kaggle dataset
“500k Student Performance and Behavior Dataset” by Waddah Ali (CC BY-NC-SA 4.0).
This derivative work is shared under the same CC BY-NC-SA 4.0 license, for non-commercial use only.

## Run locally
    cd /workspace/student-viz/site && python3 -m http.server 8765
    # open http://localhost:8765

## Files
- index.html, assets/app.js (bundled three.js + 3d-force-graph + app, ~1.4 MB), assets/style.css
- data/sample.json: 30k sampled rows (columnar, quantized), ~2.3 MB
- data/model.json: GBM importances and partial dependence, plus baseline associations
- data/connections.json: output of analysis/score_connections.py (scored, replicated findings)
Total is about 3.9 MB uncompressed, or about 1 MB gzipped. There is no build step needed to host; it is fully static.

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
