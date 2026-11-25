# TASK for Claude Code: finish my GitHub profile "all together"

Read CLAUDE.md first. Then do the steps IN ORDER. After each step: run the check, commit locally,
and report in 2-3 sentences. Don't continue past a failed check; tell me what failed.

## Step 1: Verify the README as it is
- Check README.md renders clean as GitHub-flavored Markdown (valid HTML tags, no broken relative
  links, all badge URLs well formed). Fix only real problems.
- The top of README has a commented-out block for `assets/luminette-loop.gif`. Leave it commented
  until Step 3 gives a real file.
Check: list any issues found/fixed; otherwise say "clean".

## Step 2: Snake
- grid-snake.svg is already recolored (snake ivory, cells oxblood -> crimson, dark card behind).
  Validate it is well-formed XML and still animates (CSS keyframes present, no broken var(--...)).
- .github/workflows/snake.yml generates a fresh palette-colored snake.svg into branch `output`.
  Review it for correctness (permissions, action versions, URL-encoded colors). Don't change the
  colors. Keep the static grid-snake.svg as a fallback.
- Add to README, below the static snake, nothing yet. I'll switch the image source to the
  `output` branch URL myself after the workflow has run once. Write that one-line instruction
  into a short SETUP.md (how to run the workflow manually: Actions tab -> "Generate snake" ->
  Run workflow; then the raw URL to use).
Check: XML validates; workflow YAML parses; SETUP.md exists.

## Step 3: Build the ASCII loop (the big one: do it in sub-steps, stop after each)
Goal: assets/luminette-loop.gif, a seamless looping photo-to-ASCII animation, my visual style:
dense, photographic ASCII on black. Subject built from mixed heavy glyphs (@ S ? # % & + * :),
bright where the photo is bright, fading into near-black with sparse faint "dust" characters
(+ . : ') in dim crimson/grey. Strong contrast, deep blacks. Letters in lit areas slowly swap
between near-equivalent glyphs.
Sequence (loop ~20 s): LUMINETTE (name) -> Aether (spaceship/galaxy) -> BXP (butterfly) ->
Game Studio + creativity -> back to the name. Letters visibly flow from each scene into the next.

Inputs: put my photos in refs/ (ship.jpg, butterfly.jpg, game.jpg). If one is missing, stop and
ask me; don't download substitutes. The name scene needs no photo.

Pipeline (Python 3 + NumPy + Pillow; pygame-ce only if needed):
 a. atlas.py: pre-render each candidate glyph (monospace bold) to a small bitmap (e.g. 8x16).
 b. pipeline.py: image -> grayscale -> percentile contrast stretch -> gamma ~0.8 -> mild unsharp
    -> push background toward black -> sample to a letter grid (~200 cols, cell aspect ~1:2) ->
    choose each glyph by SHAPE MATCHING against the local patch (brightness + gradient
    direction), not by a plain brightness ramp -> color by brightness via the palette gradient
    (near-black -> oxblood -> maroon -> mauve -> blush -> ivory; crimson only for dust/accents).
 c. dust.py: deterministic sparse dim crimson/grey characters clustered by low-frequency noise,
    drifting slowly.
 d. Name scene: render "LUMINETTE" in a bold font to a mask, fill with fractal noise + soft
    upper-left lighting so it looks as photographic as the others, then same pipeline.
 e. morph.py: resample each scene's cell list to equal N (weighted by brightness), match cells
    between scenes (sort by angle around centroid, then radius), ease with smoothstep, add
    curl-noise swirl at mid-transition, glyphs scramble briefly then settle, brightness dips mid-way.
 f. scene.py + render.py: timeline hold ~3 s / morph ~2 s per scene, last morph returns to the
    name; slow 2-3% zoom/drift while holding. CLI flags: --fast, --scene NAME, --t SECONDS,
    --clip, --full, --gif.
Sub-steps (stop and report after each, one preview image max per sub-step, `--fast` only):
 3a atlas + pipeline on butterfly.jpg   3b tune until it matches the look above (max 3 tries)
 3c dust   3d name scene   3e ship + game scenes   3f ONE transition (name -> ship) as a short clip
 3g all transitions + full timeline   3h final export.
Final export: assets/luminette-loop.gif (<= 10 MB, ~800 px wide, 12-15 fps, seamless: check
last vs first frame numerically), plus loop.mp4 (full quality) and 4 stills in out/ (not in README).
Check: report file size, dimensions, frame count, total cell count, only palette colors used.

## Step 4: Wire it into the README
- Uncomment the loop block at the top of README.md (it already points at assets/luminette-loop.gif).
- Confirm the file path exists and size <= 10 MB.
Check: `git status` clean except intended files.

## Step 5: Final pass
- Run a last sanity check on README.md, grid-snake.svg, workflow, assets.
- Show me `git log --oneline` and the list of files to push. Don't push.
