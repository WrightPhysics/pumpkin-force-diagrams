# pumpkin-force-diagrams

Interactive simulation for Richard Wright's Advanced Physics class, built from the worksheet "4.5 Exploration: A Pumpkin and the 3 Diagrams." Students look at the pumpkin in different situations and connect the written description, sketch, interaction diagram (ID), free body diagram (FBD) and force vector addition diagram (VAD).

It started as a copy of `physics-force-vad-solve` (see that repo's CLAUDE.md). The arrow style, label placer, angle annotations, highlighting and Hide/Show panels are the same code. Keep the two consistent when fixing shared code.

## How the project works

- Everything is in **one file: `index.html`** (inline CSS and JS, no build step, no dependencies besides Google Fonts).
- Deployed like the other sims: GitHub Pages from `WrightPhysics/pumpkin-force-diagrams`, with a card on the wrightphysics.org homepage (`WrightPhysics/wrightphysics.github.io`). Live at https://wrightphysics.org/pumpkin-force-diagrams/ (repo `WrightPhysics/pumpkin-force-diagrams`).
- After any JS change, syntax-check the `<script>` block (`node --check`, or load the page and check the console), then check the page in a browser.

## Working with Richard

- He is a physics teacher and former consulting engineer. He gives feedback through screenshots; expect visual, precise requests.
- **Use his wording exactly** when he specifies text. Don't paraphrase it.
- Make the change asked for and nothing else; mention side effects briefly.
- Commit and push only when he asks.

## Theme (St. Andrew's + Halloween, permanent)

- Navy header band (`.site-head`) with an orange bottom border, "St. Andrew's Advanced Physics" eyebrow in steel blue, and the title in **Teko** (a school brand font, loaded from Google Fonts) with a small pumpkin icon and a few bats in the corner. Ghost icons sit on the "Hidden" placeholders.
- School colors were sampled from the athletics logo sheet (no official hex list was found): navy #111921, royal #1A47BC, steel #789EC4. They are used only on page chrome (header, picker, Show/Hide and Start over buttons, panel-title bars, focus outlines) and never inside the diagrams, because royal/steel blue is close to the gravity force color (`--f0`).
- **No logo for now**: Richard has not confirmed permission to use the Highlander athletics marks on the public site. The logos (and Teko, Lato, Spectral, a Highlander font) are in `../SAS Branding`. If permission comes, the round head-mark icon (`Logos/Web (For Digital Use)/Athletics/Color/StAndrews-Icon-Circle.png`, resized to 192 px) was mocked up at the left of the header.
- Light mode: steel-tinted page; dark mode: near-black header and navy panels.

## Panels

- Layout, top to bottom: title, scenario picker (← arrow, dropdown, → arrow; arrows disable at the ends) plus the hill toggle on #9, then a two-column grid (`.layout`, `grid-template-areas`) that matches the paper worksheet:
  - Written Description (full width)
  - ID | Sketch
  - FBD | VAD
  - Below ~900 px it becomes one column: Written Description, Sketch, ID, FBD, VAD.
- The **Angle Annotations** bar sits below all of that.
- Every representation panel, the VAD included, has a Hide/Show button with a placeholder asking students to build it.
- **On page load and whenever students move to a different scenario, every panel is shown** (`resetReps`, `SHOWN_AT_START`); students can hide any of them. Switching Smooth/Rough on #9 keeps whatever is showing. (Earlier the ID, FBD and VAD started hidden; Richard changed the default to show all.)
- The VAD panel header has a "VAD reference (PDF)" link (opens `vad-reference.pdf` in a new tab), a one-page student handout on building a VAD from an FBD. Its source is Richard's Claude Docs doc; the PDF is a separately built copy, so rebuild and replace the file when the doc changes.
- VAD arrows have **no dot at the tail** (Richard's request; dots belong only on the FBD).
- The VAD status line (`#msg`) only updates on pointerup, never during a drag, and keeps a `min-height` sized for its longest message. Otherwise its line count changes as arrows snap/unsnap, the grid below shifts under the pointer, and the arrow flickers between snapped and unsnapped (Richard hit this).
- The VAD starts with an instruction line (`.vad-intro`, Richard's exact wording) above its controls.
- The VAD's "Show lines of action" and "Start over" controls are inside its body, so they hide with it.

## This activity is qualitative

- **No numerical force values anywhere**: no masses, no newtons, no magnitudes on chips or VAD labels, no grid scale. Angles of directions (30° hills, rope angles) are fine.
- In the VAD, **the first force a student brings over is the reference (the "anchor", `st.anchor`)**: it gets length `REF_LEN` (4 grid squares), the middle of the arrow sits at the center of the grid (`centerAnchor`), and it is fully locked (no moving, no stretching; a hint explains). A small muted "start" label sits just behind its tail. Every other force, F_gPE included, starts at `START_LEN` in one of the `SLOTS` around the anchor and can be moved and stretched along its direction. Chains never move the anchor (`downstream` stops at it). If the anchor is removed (chip or double-tap), the earliest-placed remaining force becomes the new anchor and is re-centered at length 4. (Before this, F_gPE always had the fixed length; Richard changed it.) The chain is built **forward from the start**: a moved arrow may snap its tail onto a head, or its head onto any tail except the start's. Only an arrow already in the chain that runs from the anchor (`chainFromAnchor`) may land its head on the start, by stretching. Otherwise students could attach a force backwards onto the start, and "start" would no longer mark the chain's real beginning.
- **Do not show ΣF on the VAD**: no dashed gap segment, no ΣF labels, no closure ring. Students should connect the gap to the motion map themselves. The only feedback is the status line, exact wording:
  - ends where it started: "When these force vectors are added head to tail, the diagram ends where it started, so the **result of all the forces exerted on the pumpkin** is "**nothing**.""
  - doesn't: "When these force vectors are added head to tail, the diagram doesn't end where it started, so the **result of all the forces exerted on the pumpkin** is **NOT** "**nothing**.""
  - The bold phrases are Richard's request (rendered with `<strong>`).
  - “nothing.” is **green** (`--good`) when it ends where it started; **NOT** “nothing.” is **red** (`--bad`) when it doesn't.
- Students answer "Balanced or Unbalanced? How do you know?" on paper, not in the sim.

## Scenarios

Dropdown options read **"Scenario N: <worksheet sentence>"** (HTML like `<em>` is stripped for the options). 1–10 use the worksheet sentences; the extra examples Richard chose are numbered **11a–11d** (the `num` field) because they stand in for the worksheet's make-your-own #11:

1. Motionless (level ground) 2. Sliding at constant velocity (smooth ground; the description deliberately does not say why friction is negligible, so students reason it out) 3. Slowing down on a rough surface 4. Sliding down a smooth hill 5. At rest on a rough hill 6. Hanging from a rope 7. Hanging by two ropes (left 30° west of north, right 30° north of east) 8. Free fall 9. At rest on a hill against a block, with a **Smooth hill / Rough hill** toggle (`vars`) 10. Pulled up over a pulley at constant velocity by the Headless Horseman 11a. Sliding up a smooth hill, slowing down 11b. Falling at constant velocity with air resistance 11c. Held at rest against a rough wall by the Horseman's push 11d. Pulled across rough ground by a rope at 30°, speeding up

The Written Description is exactly the scenario sentence from the paper worksheet (the scenario `name` plus a period, with *constant* italic as on the paper); 11a–11d use their names in the same style. No extra details or hints (angles, friction, who is pushing): those come from the sketch, and students reason out friction from "smooth"/"rough" and the motion. The key line ("P = pumpkin, …") stays below it for the Type-On-By letters.

## Notation

- Type-On-By subscripts in the current style: `F_gPE` = gravitational force on Pumpkin by Earth. Drag (air resistance) is **F_D**, e.g. `F_DPA` (A = air); its ID interaction is **compression**.
- The bottom of the FBD panel lists every force in Richard's format, e.g. "F_TPR = **T**ension Force Exerted ON the **P**umpkin BY the **R**ope" (`renderForceKey`). The letters of the type, the object it acts ON and the object it is exerted BY are bold; "ON" and "BY" are capitalized. Type names come from `TYPE_NAME`; object names come from the scenario's `key` string, title-cased. It hides with the FBD.
- P = pumpkin in every scenario. H = hill in hill scenarios and Headless Horseman in Horseman scenarios (they never appear together). W = pulley, C = ceiling.

## Sketches

- **No Earth (E) label on the sketches** (Earth still appears in the IDs and keys).
- Etkina style: simple line drawings, objects labeled "Name (L)", the pumpkin drawn by `pumpkin()` (orange fill, ribs, green stem).
- Red dashed system boundary (#C62828, stroke-width 3.5, dash 8 5). Where the pumpkin touches a surface, that edge of the boundary runs along the surface (`tiltBox` for hills; the wall in #13; the block face in #9).
- Smooth vs rough surfaces are labeled with a muted note ("smooth", "rough surface").
- Motion maps (`mmap`): light green `--vel` velocity arrows with dots on top. At rest: "v = 0, Δv⃗ = 0". Constant velocity: equal arrows and "Δv⃗ = 0". Changing velocity: arrows grow or shrink and a separate dark green `--dv` Δv⃗ arrow (`dvArrow`) drawn with a filled TikZ **-stealth** tip (`stealthArrow`) so it reads differently from the open-barbed velocity arrows.
- Motion map placement: on level ground, centered horizontally in the sketch. On an incline (#4, #11a), parallel to the slope, centered on the incline, just above the system boundary, with the pumpkin also centered on the incline (`slopePt` gives points along/out from a slope). The at-rest label on the rough hill (#5) is rotated to run parallel to the incline.
- A push is drawn perfectly horizontal (#11c: the hand is at shoulder height, level with the pumpkin's center).
- Ropes are drawn with `rope(runs)` (straight runs): dark outline, tan core and short diagonal twist marks (style "B", Richard's pick). Hanging ropes (#6, #7, #10) are tied to the pumpkin's stem with a wrapped hitch (`stemKnot`/`knot`: two rope wraps around the stem's middle). In #11d the rope loops around the pumpkin's middle (`ropeCurve`, front half visible) and is knotted (`ropeKnot`) on the right side to the run going up at 30° to the Horseman; the 30° reference starts at the knot and the pumpkin label sits above the boundary. In #7 the left rope is natural tan and the right rope is dark brown (`rope(runs, true)`) so the two ropes look different from each other. Ropes stay taut and straight. On the pulley (#10) the rope runs sit just inside the rim (in the groove) and the wrap around the wheel is not drawn: the wheel is drawn on top, so the rope disappears into the groove.
- The Headless Horseman is the `person()` stick figure with `headless = true` (collar line, no head).
- After layout changes, view every sketch (including both #9 variants) and check desktop and phone widths.

## Interaction diagrams, FBD, VAD, labels, arrows

Same rules as `physics-force-vad-solve`, except the ID evidence words are **field, compression, extension, shear** ("extension", never "stretch"): curved double-headed interactions, crossing interactions colored by their force, negligible interactions left out, `wright vector` arrowheads at `HEAD_SCALE = 0.6`, collision-aware labels (`makePlacer`), direction-locked VAD arrows that snap head-to-tail.

ID addition: scenarios with a rope (#6, #7, #10, #11d) show a note under the ID explaining there is no Earth–rope field interaction because ropes are treated as "massless" for now (`#rope-note`, shown when any ID object's name contains "rope").

FBD addition: forces that share a direction (#9 rough: F_NPB and F_fPH) must **not overlap**. They are drawn side by side, parallel, each tail inside the dot (offset `SIDE` px either side of center), with labels on their own sides. This matters to Richard.
