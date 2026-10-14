# Style guides for agents

Every guide an agent needs to write code here, copied from its source so it can
be read offline. Read this file first; open a guide only for the language you
are writing.

## Hierarchy

When two rules disagree, the higher one wins. Say so in your reply when it
happens.

1.  **The user's direct instruction** for the task at hand.
2.  **The project's own rules**: its `AGENTS.md` or `CLAUDE.md`, and the
    project's linter config (`ruff.toml`, `.sqlfluff`, `tsconfig.json`).
3.  **Google style guides** for the language (folder `google/`).
4.  **dbt Labs how-we-style** for dbt, SQL, YAML and Jinja (folder `dbt/`).
5.  **Astro styling guide** for styles in Astro components (folder `astro/`).
6.  **The code around it**: naming, comment density and idiom.

## Which guide for which file

File                           | Guide
------------------------------ | --------------------------------------------
`.go`                          | `google/go/guide.md`, then `decisions.md`, then `best-practices.md`
`.ts`, `.tsx`                  | `google/tsguide.html`
`.py`, marimo notebooks        | `google/pyguide.md` (`google/pylintrc` is its config)
`.sh`                          | `google/shellguide.md`
`.md`                          | `google/docguide/style.md`
dbt models, `.sql`             | `dbt/2-how-we-style-our-sql.md`, `dbt/1-how-we-style-our-dbt-models.md`
dbt `.yml`                     | `dbt/5-how-we-style-our-yaml.md`
Jinja in dbt                   | `dbt/4-how-we-style-our-jinja.md`
dbt project layout             | `dbt/0-how-we-style-our-dbt-projects.md`
`.astro` styles, CSS           | `astro/styling.mdx`

Python in a dbt project follows Google, not `dbt/3-how-we-style-our-python.md`,
because Google ranks higher.

## How to apply it

1.  Pick the language by the project's rule (Go by default, Python when it is
    clearly the better tool, TypeScript only for browser code).
2.  Read the matching guide above before writing the first line.
3.  Write the test first, see it fail, then write the least code that passes.
4.  Run the linter and formatter and fix every finding:
    -   Go: `gofmt -l .` prints nothing; `go vet ./...` passes.
    -   Python: `ruff check` and `ruff format --check` pass.
    -   SQL and dbt: `sqlfluff lint` passes.
    -   TypeScript: `tsc --noEmit` with `strict` passes.
5.  Run the full test suite.
6.  Report which guide you followed and any rule where the hierarchy decided.

## Sources and licences

Folder    | Source                                                    | Licence
--------- | --------------------------------------------------------- | ----------
`google/` | https://github.com/google/styleguide (branch `gh-pages`)  | CC BY 3.0
`dbt/`    | https://github.com/dbt-labs/docs.getdbt.com, `website/docs/best-practices/how-we-style` | Apache 2.0
`astro/`  | https://github.com/withastro/docs, `src/content/docs/en/guides/styling.mdx` | MIT

Each folder keeps its source's licence file. The files are unchanged copies;
to refresh them, download them again from the same paths.
