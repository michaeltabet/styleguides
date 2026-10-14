# Rules for every agent

These rules apply to every agent that works for Michael: in /srv/basci, in the
marimo notebooks, and behind the claude-api proxy. The folder holding this file
is /srv/basci/styleguides on vm1 and /rules inside the claude-api container;
it is also https://github.com/michaeltabet/styleguides.

There are two kinds of rule. **Fixed rules** hold everywhere and are strict.
**Project choices** are set per project in its own `AGENTS.md`; when a project
says nothing, the default given here applies. All of it holds until Michael
changes or removes a rule.

## Hierarchy

When two rules disagree, the higher one wins. Say so when it happens.

1.  Michael's direct instruction for the task at hand.
2.  The fixed rules in this file.
3.  The project's own `AGENTS.md` (its project choices) and linter config.
4.  Google style guides for the language (`google/`).
5.  dbt Labs how-we-style for dbt, SQL, YAML and Jinja (`dbt/`).
6.  Astro styling guide for styles in Astro components (`astro/`).
7.  The code around it: naming, comment density and idiom.

## Fixed rules

### The human owns the code

-   Michael must understand every line that ships. If he cannot explain the
    code, you failed, however well it runs.
-   You do not make decisions. For every choice that matters, set out the
    options with their trade-offs, recommend one, and let Michael decide.
-   Vibe coding is allowed; the result must still be production quality.
-   Choose the simplest solution that scales. Never the most complex one.

### How work runs

-   Work in Scrum: a backlog, sprints, and a review at the end of each sprint.
-   Every piece of work has a software design document (SDD) and an
    acceptance criteria sheet before any code is written.
-   Follow the plan. If the plan is wrong or blocked, stop and say so; do not
    improvise around it.

### Order of work

Every piece of work follows this flow. Each step is reviewed by Michael before
the next one starts.

1.  **Research.** Read the primary sources and see what already exists. Prove
    every claim with evidence: docs, benchmarks or running code. Pick the best
    simple, scalable solution.
2.  **Pyramid.** Write the answer as a pyramid (see Writing): the one
    point at the top, the situation, complication and question that lead to
    it, and the grouped points that support it.
3.  **Data model.** An ERD, always.
4.  **UML.** At least the use case, sequence and activity diagrams, plus any
    the project adds (see project choices).
5.  **SDD and acceptance criteria.**
6.  **Test logic** in plain words (see Tests).
7.  **TDD.** Test, code, lint, tidy, one behaviour at a time.
8.  **Review** by the reviewer agent, then by Michael.

Diagrams are code: Mermaid or PlantUML, committed beside the SDD.

### Writing: the pyramid

Every document, SDD, research note, review and report is a pyramid. These are
hard rules; a document that breaks one is rewritten before anyone reads it.

1.  The document supports exactly one point, stated at the top.
2.  The introduction is situation, complication, question. The complication
    must raise exactly the question the top point answers.
3.  The introduction holds only what the reader already accepts as true.
4.  Every point is a full sentence that raises a question in the reader's
    mind; the level below answers it. "Why?" is answered by reasons, "How?"
    by steps.
5.  Every grouping holds ideas of one kind, named by one plural noun.
6.  Every grouping is mutually exclusive and collectively exhaustive.
7.  Every grouping is inductive or deductive, never both. The key line is
    inductive unless a deduction is unavoidable.
8.  An inductive grouping is ordered by time, structure or degree; a
    deductive one by argument: statement, comment, therefore.
9.  A grouping has at least two points, at most five if inductive and at most
    four if deductive.
10. There are middle levels between the top and the details. At every level
    ask whether one point is done in order to achieve another, and group
    them if so.
11. No blank assertions. A summary point states the effect of the actions
    below it or the insight they share, never a label such as "three issues".
12. If the introduction and key line do not make the whole argument clear in
    30 seconds, rewrite.

Build top-down from the question when you know it. When you do not, list the
points, group them bottom-up, then check the result top-down against these
rules.

### Choosing tools

-   Default to foundation-backed open source under the Apache License 2.0:
    projects of the Apache Software Foundation, the CNCF or the Linux
    Foundation. Anything else needs a stated reason.

### Everything as code

-   Infrastructure, config, schema, dashboards and diagrams live in git and
    deploy with Ansible. Nothing is changed by hand.
-   `stack` holds only the software. Code lives in its own package.
-   One database, but no shared database package: each package owns its own
    SQL (tables, migrations) and its own Superset dashboards.

### Secrets

-   Bitwarden is the secrets manager. Never change it, its setup or its
    access.
-   Never store a secret anywhere else: not in code, config, `.env` files,
    notebooks, logs, commits or chat. Read secrets from Bitwarden at runtime.

### Language

1.  **Go is the default for production code**: every service, worker, API
    and tool.
2.  **Python is the default for notebook code**, and notebooks are always
    marimo. Python is also used for an ML model, a dbt or Airflow DAG, or a
    library that exists only in Python; say why in the commit.
3.  **TypeScript only for code that must run in the browser.**
4.  **SQL** for schema, migrations and dbt models.

Data projects follow /srv/stack/marimo/STANDARD.md: Kedro projects, explored
and presented in marimo.

### Tests: TDD with the linter in the loop

1.  Write the test logic first, in plain words: one line per behaviour, as
    given / when / then. Michael reviews it before any test code exists.
2.  Write the test. Run it and see it fail for the right reason.
3.  Write the least code that makes it pass.
4.  Run the linter and formatter for the language (see Checks) and fix every
    finding.
5.  Tidy, then run the full suite and the linters again.

No production code without a failing test first. Do not mock modules; give
the code a real seam and test through it. Go tests are table-driven in
`_test.go` beside the code; Python tests are pytest in
`tests/.../test_<module>.py`. dbt models test their primary key with `unique`
and `not_null` in the model's YAML.

### Code

-   Minimal code that is easy to read. No slop: no filler, no dead code, no
    TODOs, no comments that repeat the code, no placeholder or faked data.
-   Build only what the task asks for (YAGNI). One package per piece of code;
    no copies.
-   Debug by finding the root cause before changing anything.
-   Never say work is done, fixed or passing without showing the command and
    its output.
-   TypeScript also passes the anti-slop Oxlint rules in `anti-slop/`: parse
    input at the boundary, never widen a known type to `unknown`, `any` or
    `object`, and justify every type assertion with a `SAFETY:` comment.

### Work that needs data

1.  Import the data.
2.  Make sure it is complete: show it raw, and check row counts, the date
    range and missing values before using it.
3.  Design the model that will run on it.
4.  Run it and do the work.

### Git

Commit the way a careful human would.

-   First line 50 characters or less, in the imperative mood ("Add feature",
    not "Added feature").
-   A blank line, then the body: why the change was made, not only what
    changed.
-   One logical change per commit.
-   No slop: no AI or tool trailers, no emoji, no "This commit...", no
    restating the diff, no secrets.

## Project choices

Each project sets these in its own `AGENTS.md`. The default applies when it
does not.

Choice           | Options                            | Default
---------------- | ---------------------------------- | -----------------------
Agents           | one, two or three                  | two: one coder, one reviewer
Design diagrams  | C4, state, class, deployment       | ERD + use case, sequence, activity only
Test coverage    | a line-coverage floor              | every acceptance criterion has a test
Sprint length    | in weeks                           | one week

The reviewer checks the coder's work against the SDD, the acceptance criteria
and this file, and nothing merges until the reviewer passes it.

## Style guides

Read the guide for the language before writing the first line.

File                    | Guide
----------------------- | --------------------------------------------------
`.go`                   | `google/go/guide.md`, `decisions.md`, `best-practices.md`
`.ts`, `.tsx`           | `google/tsguide.html`
`.py`, marimo notebooks | `google/pyguide.md` (`google/pylintrc` is its config)
`.sh`                   | `google/shellguide.md`
`.md`                   | `google/docguide/style.md`
dbt models, `.sql`      | `dbt/2-how-we-style-our-sql.md`, `dbt/1-how-we-style-our-dbt-models.md`
dbt `.yml`              | `dbt/5-how-we-style-our-yaml.md`
Jinja in dbt            | `dbt/4-how-we-style-our-jinja.md`
dbt project layout      | `dbt/0-how-we-style-our-dbt-projects.md`
`.astro` styles, CSS    | `astro/styling.mdx`

Python in a dbt project follows Google, not
`dbt/3-how-we-style-our-python.md`, because Google ranks higher.

Short form of the rules most often missed:

-   Python: 80 columns, docstrings on every public function, snake_case.
-   Markdown: wrap at 80, ATX headings, one H1, fenced code with a language.
-   SQL: lowercase, 4-space indent, trailing commas, explicit `as`,
    `inner join` not `join`, import CTEs first, models end with
    `select * from <final cte>`. YAML indents 2 spaces.

## Checks

Every one must pass, inside the TDD loop and before every commit.

-   Go: `gofmt -l .` prints nothing; `go vet ./...` and `go test ./...` pass.
-   Python: `ruff check`, `ruff format --check` and `pytest` pass.
-   SQL and dbt: `sqlfluff lint` passes.
-   TypeScript: `tsc --noEmit` with `strict`, and `oxlint` with the anti-slop
    rules, pass.

## Sources and licences

Folder       | Source                                                   | Licence
------------ | -------------------------------------------------------- | ----------
`google/`    | https://github.com/google/styleguide, branch `gh-pages`  | CC BY 3.0
`dbt/`       | https://github.com/dbt-labs/docs.getdbt.com, `website/docs/best-practices/how-we-style` | Apache 2.0
`astro/`     | https://github.com/withastro/docs, `src/content/docs/en/guides/styling.mdx` | MIT
`anti-slop/` | https://github.com/dmmulroy/anti-slop, `src/` at 13909c9 | MIT

The working method (design before code, TDD, root cause before fix, evidence
before done, coder and reviewer) follows https://github.com/obra/superpowers
(MIT).

Each folder keeps its source's licence file. The files are unchanged copies;
to refresh them, download them again from the same paths.
