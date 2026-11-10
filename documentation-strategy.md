# Vaultline Documentation Strategy and Content Audit

*Sample documentation by Kumar Gaurav. Vaultline is a fictional product, and all figures in this document are illustrative data created for portfolio purposes. The full inventory is in [content-inventory.csv](content-inventory.csv).*

| Field | Value |
| --- | --- |
| Prepared for | Head of Developer Experience |
| Audit date | November 2026 |
| Scope | 24 pages in the Vaultline developer documentation |
| Product version | Key Service API 2.4 |

## Summary

The Vaultline documentation helps developers who follow the main path, but it fails them at the edges. The guides for the main task (envelope encryption) are strong. The pages around them, such as migration, error handling and troubleshooting, are out of date, hard to find, or have no owner.

**What I found**

- **Findability is the weakest quality.** It scores 2.5 out of 5, against 3.1 for accuracy and completeness and 3.4 for clarity. Developers can't reliably reach the good content.
- **Half of the documentation is stale.** 13 of 24 pages (54%) haven't been updated in more than 12 months. Those pages receive 37% of all monthly views.
- **One in five pages has no owner.** 5 pages (21%) have nobody responsible for them, so nobody fixes them.
- **The highest-traffic pages are not the best pages.** The REST v2 API reference is the most-viewed page (6,240 views a month) and scores only 3.5 out of 5. It is missing endpoints that now exist.

**What I recommend**

1. Fix the five highest-impact pages first (see [Priorities](#priorities)). They have the best return per hour of writing.
2. Retire or merge four pages that add confusion and no value.
3. Assign an owner to every page, and add a review date to each one.
4. Generate the API reference from the OpenAPI spec, so it cannot drift from the product.
5. Adopt a single style guide and a small set of page templates (see sample 4 in this portfolio).

## Method

I audited every page against four qualities, each scored from 1 (poor) to 5 (excellent).

| Quality | A page scores 5 when... | A page scores 1 when... |
| --- | --- | --- |
| Accuracy | Every step, value and screenshot matches the current product. | Instructions fail or describe features that no longer exist. |
| Completeness | A reader can finish the task without leaving the page or asking for help. | Key steps, parameters or error cases are missing. |
| Findability | The page ranks in search, appears in navigation and has clear links to and from related pages. | The page is only reachable by direct link or has a misleading title. |
| Clarity | The page has one purpose, a logical structure, and plain language. | The page mixes several purposes or has unexplained jargon. |

For each page I also recorded its content type (tutorial, how-to, concept, reference, troubleshooting, FAQ or release notes), audience, owner, last update, and monthly views.

I gave each page a score (the average of the four qualities) and an action:

| Action | Meaning |
| --- | --- |
| Keep | The page is in good shape. Review it on its normal date. |
| Update | The structure is sound, but facts, examples or links need fixing. |
| Rewrite | The page has a structural problem, so patching it won't help. |
| Merge | The page overlaps with another, and one should absorb the other. |
| Retire | The page is obsolete. Redirect it, then remove it. |

**Limits of this audit.** I scored pages by reading them and checking them against the product. I did not run usability tests, and view counts show traffic, not whether readers succeeded. Treat the scores as a way to rank work, not as measurements.

## Findings

### Overall scores

| Quality | Average score (out of 5) |
| --- | --- |
| Accuracy | 3.1 |
| Completeness | 3.1 |
| Findability | 2.5 |
| Clarity | 3.4 |

### Recommended actions

| Action | Pages |
| --- | --- |
| Keep | 6 |
| Update | 10 |
| Rewrite | 4 |
| Merge | 2 |
| Retire | 2 |

### Problems that appear repeatedly

1. **No clear path from the first tutorial to real work.** "Getting started" ends without pointing to the migration guide, the SDK quickstarts or the error reference. Developers finish the tutorial and then search on their own.
2. **The v1 documentation still competes with v2.** The old API reference (D04) and a legacy status page (D23) still appear in search. They describe behaviour that no longer exists, and they contradict the current guides.
3. **Error content is spread across three pages.** The error code table (D12), the decrypt troubleshooting page (D13) and the rate-limit page (D14) each explain part of the problem. A developer who gets a 409 error has to find all three.
4. **SDK quickstarts have drifted apart.** The Python quickstart was updated in May 2026. The Java and Node.js quickstarts were last updated in early 2025 and don't match the current API. The Java quickstart scores only 2 out of 5 for accuracy.
5. **Unowned pages decay.** Every page with no owner scored 2.5 or lower. No page with a named owner was last updated more than 24 months ago.

## Priorities

I ranked pages by **impact**, calculated as monthly views multiplied by the gap between the page's score and a perfect 5. A busy page with a few problems can outrank a quiet page with many.

| Rank | Page | Monthly views | Score | Action | Why it matters |
| --- | --- | --- | --- | --- | --- |
| 1 | D03 API reference (REST v2) | 6,240 | 3.5 | Rewrite | The most-viewed page. Missing the rotate, enable and disable endpoints. Generate it from the OpenAPI spec. |
| 2 | D12 Error codes | 3,380 | 3.0 | Update | Hard to find. Does not say what to do for each error. |
| 3 | D06 Authentication and API tokens | 3,150 | 3.0 | Update | Does not link to the permissions reference (D18), the page that explains most 403 errors. |
| 4 | D05 Migrate from v1 to v2 | 2,260 | 2.5 | Rewrite | The v1 endpoint is scheduled for removal. This page has to work before that date. |
| 5 | D13 Troubleshooting decrypt errors | 2,150 | 2.5 | Rewrite | The top cause of support tickets. Merge it with the error content. |

Pages D01, D17 and D07 follow in the full ranking. D01 receives many views but scores well, so it needs a small change (links to next steps) rather than a rewrite.

## Strategy

### Structure the content by purpose

Every page should do one job. I recommend four content types, following the Diátaxis framework, plus release notes:

| Type | Reader's goal | Example |
| --- | --- | --- |
| Tutorial | Learn by doing a guided exercise | Getting started with Vaultline |
| How-to | Complete a specific task | Rotate a master key |
| Reference | Look up a fact | API reference, error codes |
| Concept | Understand how something works | Envelope encryption, permissions |

The audit found pages that mix these types. For example, the key rotation page (D07) explains the concept, gives a procedure and lists API parameters. Splitting such pages makes each one shorter and easier to maintain.

### Make the reference trustworthy

Generate the API reference from the OpenAPI spec, and run the build in CI so a spec change updates the docs. This removes the largest source of inaccuracy. Sample 5 in this portfolio demonstrates this approach.

### Fix findability

- Add a "Next steps" section to every tutorial and how-to.
- Rename pages with the words developers search for. For example, change "Rotation policy FAQ" to "How often should I rotate keys?" and merge it into the rotation how-to.
- Add redirects from every retired page to its replacement.
- Review the top 20 search queries each quarter and check that the right page ranks first.

### Govern the content

| Practice | Detail |
| --- | --- |
| Ownership | Every page has a named owner (a team is acceptable). Pages with no owner go to the docs team by default. |
| Review dates | Every page has a "last reviewed" date and a review interval: 6 months for tutorials, 12 months for concepts. |
| Release checklist | Each product release includes a docs task: update affected pages, add release notes and check links. |
| Style | All pages follow one style guide and one template for their content type. |

## Roadmap

| Phase | Timing | Work | Outcome |
| --- | --- | --- | --- |
| 1. Stop the harm | Weeks 1 to 4 | Retire D04 and D23 with redirects. Assign owners to the five unowned pages. Add a v1 deprecation banner. | Search no longer shows contradictory v1 content. |
| 2. Fix the top five | Weeks 3 to 8 | Rewrite D03 (generated from OpenAPI), D05 and D13. Update D12 and D06. | The highest-impact pages are accurate and complete. |
| 3. Clear the backlog | Weeks 9 to 16 | Rewrite D17. Update the SDK quickstarts, key rotation, audit logging and the other "Update" pages. Merge D08 and D21. | No page is more than 12 months old. |
| 4. Make it stick | Weeks 12 to 20 | Adopt the style guide and templates. Add the release checklist. Start quarterly reviews. | New content stays accurate without another audit. |

## How we will measure success

| Measure | Today | Target (6 months) | Source |
| --- | --- | --- | --- |
| Pages updated in the last 12 months | 46% | 90% | Content inventory |
| Pages with a named owner | 79% | 100% | Content inventory |
| Average findability score | 2.5 | 3.5 | Re-audit |
| Support tickets about documented errors | Baseline needed | 20% fewer | Support system |
| "Was this page helpful?" yes rate | Not collected | 75% or higher | Feedback widget |

Two of these measures have no baseline today. The first task in phase 4 is to add a feedback widget and agree how to count the support tickets, so that later reports show real change.

## Risks and assumptions

- **Subject matter experts have limited time.** The plan relies on engineers reviewing drafts. I recommend agreeing a review turnaround (two working days) with each team before phase 2 starts.
- **Page views may overstate importance.** A page with few views, such as the HSM setup guide (D15), may matter greatly to the few readers who need it. I weighted by views, but I would not retire a low-traffic page without checking who uses it.
- **The v1 removal date is fixed.** The migration guide (D05) has to be ready well before 31 March 2027, so phase 2 can't slip.

## Next steps

1. Review this strategy with the Head of Developer Experience and agree the phase 1 scope.
2. Name owners for the five unowned pages.
3. Confirm the OpenAPI spec is complete enough to generate the reference.
