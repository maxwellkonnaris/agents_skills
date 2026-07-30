---
name: figuregeneration
description: Create, revise, and validate publication-ready scientific figures in R with ggplot2 and multi-panel layout tools. Use when generating or polishing analysis plots, reproducing an existing figure from updated data, assembling manuscript figures, improving typography or accessibility, preserving statistical content while redesigning presentation, exporting vector PDFs, or diagnosing clipping, overlap, weak contrast, crowded labels, legends, and panel balance.
---

# Scientific Figure Generation

Create figures that communicate the scientific result clearly at their final display size. Treat visual design as part of scientific reporting, not decoration.

## Protect scientific integrity

- Preserve the data, estimand, statistical results, uncertainty representation, labels, panel content, and scientific meaning unless the user explicitly requests a scientific change.
- Distinguish a visual revision from a new analysis. Do not silently change filtering, transformations, thresholds, summaries, models, or call rules.
- Preserve individual observations, distributions, intervals, and sample sizes when already supported by the source figure.
- Do not replace a statistical representation merely because another geometry looks cleaner.
- Label transformations and units explicitly. If an axis is truncated for readability, disclose it in the panel and mark out-of-range observations.
- Treat missing values as a data issue to diagnose. Do not render unexplained `NA` categories or silently discard observations.
- Keep color meaning consistent across panels. Preserve established project semantics unless the user requests a change.

## Protect existing artifacts

- Inspect the existing script, figure, plotting tables, and output path before editing.
- Check repository status before modifying tracked files.
- Preserve existing filenames and output structure unless a change is necessary.
- When asked for a second version, create a new script or a clearly isolated design branch and use a distinct output filename.
- Record the original figure checksum before rendering and verify it afterward when non-overwrite is required.
- Do not delete or overwrite prior figures unless the user explicitly asks.

## Work from the intended output

Before coding, establish:

1. the output medium: manuscript page, slide, poster, web, or supplement;
2. the final physical dimensions and expected reduction;
3. the number and relative importance of panels;
4. the principal comparison the reader should see first;
5. the variables and visual encodings that must remain consistent;
6. whether legends can be shared or direct labels are clearer.

Choose the canvas first. Scale text, points, lines, intervals, spacing, and panel letters relative to that canvas rather than using universal sizes.

## Use a centralized design system

Define one reusable style object and one reusable theme. Centralize:

- font family;
- base text size;
- axis-title, tick, legend, facet-title, annotation, and panel-letter sizes;
- major, secondary, reference, and highlighted line widths;
- point and interval sizes;
- plot and panel margins;
- panel spacing;
- gridline and axis styling;
- legend keys and spacing;
- discrete, sequential, and diverging palettes.

Use a clean sans-serif font available in the active R environment, preferably Arial, Helvetica, or Liberation Sans. Use the same family throughout.

Unless the user specifies otherwise, use these relative starting points:

- axis titles: 110–125% of base text;
- tick labels and legends: 85–100%;
- facet or panel titles: 115–135%;
- panel letters: 125–150%, bold;
- annotations: 85–100%;
- major data lines: 8–14% of base text size;
- secondary lines: 50–75% of the major line width;
- highlighted lines: 120–160% of the major line width.

Treat these as starting points. Respect explicit user overrides, including unusually large panel letters or labels.

## Design color accessibly

- Use a restrained colorblind-safe palette.
- Use dark charcoal instead of pure black where appropriate.
- Use neutral gray for reference information and numerical-zero categories.
- Use sequential palettes only for ordered magnitudes.
- Use balanced diverging palettes only when a meaningful midpoint exists.
- Reinforce color with shape, line type, position, or direct labels when categories must remain distinguishable without color.
- Avoid rainbow palettes, decorative gradients, excessive saturation, and red–green as the sole distinction.
- Check whether the hierarchy remains interpretable in grayscale.

When encoding positive and negative signs, follow the project or user convention exactly. Do not assume a universal red/blue direction.

## Make axes and labels legible

- Use concise sentence-case labels with units where applicable.
- Define abbreviations that a broad scientific reader may not know.
- Use approximately 4–7 major ticks on continuous axes unless the data require more.
- Remove minor ticks unless they provide useful resolution.
- Avoid unnecessary decimal places.
- Use scientifically appropriate transformations and limits.
- Prevent labels, points, intervals, and annotations from touching panel boundaries.
- Add controlled line breaks to long facet labels rather than shrinking all text.
- Preserve a common axis when direct comparisons require it.
- If different ranges are necessary, make that distinction visually explicit.

## Build a coherent multi-panel story

- Order panels according to the scientific argument: inputs or design, parameters or estimands, primary result, then consequences or diagnostics.
- Give more space to the panels that carry the primary result.
- Align plotting regions, axes, facet strips, panel letters, and legends precisely.
- Use consistent spacing between related panels and more separation between unrelated panels.
- Use shared legends only when mappings are identical and the shared placement reduces lookup.
- Keep legends with their panels when mappings differ or joint placement weakens comprehension.
- Prefer direct labels when they reduce repeated legend lookup.
- Use patchwork, cowplot, or gridExtra for assembly; use explicit layout areas for nonuniform designs.
- Avoid a global title or subtitle unless requested. Let panel content and the caption carry the scientific narrative.

## Implement reproducibly in R

- Generate new figure-analysis deliverables as R Markdown (`.Rmd`) by default.
  Create a standalone R script only when the user explicitly requests one or
  explicitly asks to modify an existing script.
- Slurm/HPC job-submission, launcher, and worker scripts remain valid when
  required for cluster execution.
- Use ggplot2 for plotting.
- Keep validated plotting-table construction separate from visual styling where practical.
- Read durable tables or result objects rather than copying values into plotting code.
- Validate expected row counts, keys, factor levels, ranges, and missingness before plotting.
- Set factor order explicitly when order carries scientific meaning.
- Use deterministic jitter or label placement when randomness affects rendering.
- Keep output dimensions and device explicit in `ggsave()`.
- Export PDF only by default. Do not create duplicate PNG, TIFF, or SVG files unless the user explicitly requests another format.
- Prefer `cairo_pdf` when available for embedded text and reliable font rendering.
- Keep the R Markdown document or explicitly requested script rerunnable and
  print concise validation counts and the written output path.

## Render, inspect, and revise

Never assume that valid ggplot code produced a good figure.

1. Render the figure at final intended dimensions.
2. Convert the PDF page to a temporary raster preview for inspection when needed; do not publish that preview as a duplicate deliverable.
3. Inspect the complete page and zoom into dense regions.
4. Check text readability, clipping, overlap, whitespace, panel balance, alignment, ticks, legends, contrast, and visual density.
5. Check accessibility through colorblind-safe mappings and grayscale redundancy.
6. Revise the figure at least once when the task requests active visual improvement.
7. Render and inspect the revision again.

Prefer enlarging the canvas, reallocating panel space, wrapping labels, or simplifying redundant legends over shrinking text until it becomes unreadable.

## Validate the final artifact

Run checks proportional to the figure:

- the plotting document or explicitly requested script parses and runs without errors;
- the expected PDF exists and is non-empty;
- the expected page count and dimensions are correct;
- the expected cohorts, samples, methods, or pairs are represented;
- no unexplained `NA` label is rendered;
- no important warning was hidden;
- the source and plotting-table dimensions remain unchanged by a visual-only revision;
- any protected original file retains its checksum;
- the final figure has been visually inspected after the last render.

Stop and report rather than guessing if the source figure cannot be linked to its data, labels are scientifically ambiguous, or improving readability would require removing scientific content.

## Report the result

Return:

1. a link to the revised R Markdown document or explicitly requested R script;
2. a link to the figure PDF;
3. a concise list of visual changes;
4. validation evidence;
5. any remaining visual limitation that cannot be fixed without changing scientific content;
6. the smallest command that reproduces the figure and its expected completion message.

State explicitly whether the original was preserved. State that no raster duplicate was created when PDF-only output applies.
