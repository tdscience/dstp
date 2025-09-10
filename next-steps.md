# Next Steps to Improve the Course Website

The Quarto website for "Data Science for Transport Planning" has been successfully created with minimal placeholder content and rendered to the `docs/` directory. The site is functional, but here are suggestions to enhance it:

## 1. Add Images and Assets
- Create an `images/` directory and copy essential images from `../course/images/`, such as `icon.png` for favicon and a logo (e.g., update `tsrtr.png` to a DSTP-specific logo).
- Uncomment the `favicon` and `logo` lines in `_quarto.yml` once assets are added to avoid broken references.

## 2. Expand Placeholder Content
- Fill in session pages (s1.qmd to s8.qmd) with actual teaching materials adapted from `../course/` (e.g., s1.qmd: content on importing data using stats19, osmextract for Leeds).
- Update examples: adapt `collisions.qmd` and `peoplemap.qmd` for Leeds UK using local data (e.g., stats19 for West Yorkshire).
- Enhance `materials.qmd` with links to datasets, code repositories, and readings from dstp.qmd schedule.

## 3. Integrate Schedule Links
- Add hyperlinks in `schedule.qmd` to corresponding session pages, e.g., `[Finding, importing and cleaning transport datasets](s1.qmd)`.
- Ensure session titles in s*.qmd match schedule exactly for consistency.

## 4. Test and Refine Prerequisites
- Verify the R and Python test code in `prerequisites.qmd` runs without errors for Leeds (e.g., use `execute_command` to test `Rscript -e "source('prerequisites.R')"` if extracted).
- Add screenshots or outputs for the test plots (e.g., Leeds road network visualization).

## 5. Add Supporting Directories and Files
- Copy `slides/` from `../course/` and adapt for DSTP topics (e.g., day1.qmd on introduction to data science).
- Copy `data/` with Leeds-specific files (e.g., leeds_study_area.geojson generated from prerequisites).
- Add `references.bib` copied from `../course/` and integrate citations in pages.
- Copy `_extensions/` from `../course/` for custom Quarto features like bsicons.

## 6. Deployment and CI/CD
- Commit changes and push to GitHub to trigger `publish.yml` for automatic deployment to gh-pages.
- Test the live site at `https://tdscience.github.io/dstp/` and fix any broken links.

## 7. Additional Improvements
- Add a forum link in sidebar if discussions are enabled on GitHub.
- Include alignment with learning outcomes from dstp.qmd in index.qmd.
- Add navigation to examples section and ensure mobile responsiveness.
- Review for accessibility and add alt text to future images.

These steps will transform the minimal site into a comprehensive course resource.