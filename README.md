# `repo-review-stats` - tabulate project reviewers by activity level

This accumulates all repository postings — issues/PRs, comments, reviews, and review comments — then sorts these combined sets by contributor according to post count _and_ text volume.

_Why?_  All text comments are considered useful to _peer review_: as opposed to commits which are already tracked for each GitHub repo and have more traditional tools for performance analysis.

> [!NOTE]
> Therefore this simply counts all _writing_ on a repo, since all of this is considered useful *in a peer review context*: as opposed to the "software development" context which considers this material irrelevant.

Division into two scripts is intended to keep these reports deterministic & reproducible, to support well-informed project collaboration, reporting & audit:
* `rr-collect` - produces a single data file (text, space delimited) with 4 fields:
  * the UTC format date of every posting (sorted by this field)
  * the GitHub user who posted it
  * the number of raw Markdown characters ("size") in that posting
  * a deep link in the GitHub UI for that comment
* `rr-report` - taking collected data in this format in standard input, outputs to standard output (in Markdown format) two tabulations for each user:
  * first, ranked by total sizes of all postings
  * then, ranked by count of all postings

Reference data collections & reports are in [`/reports`](./reports) for the reference projects at the time they were indexed: along with fixed reports for data subsets of interest to a project.

## Usage

This is designed for reproducible results, so that:
* Multiple runs by different users will always produce the same `report.txt` from `rr-collect` (differing perhaps by any more recently posted comments at the end).
* Data collected for a reference project can re-used independently.

**Common prerequisites** to using any of the scripts by any method:
* a UNIX-like environment, in terms of shell & filename references.
* a recent version of `jq` (tested with `1.8` and [easily downloadable](https://github.com/jqlang/jq/releases/latest) if yours is older)

## Generating your own reports

**Prerequisites**:
1. [GitHub CLI > Installation](https://github.com/cli/cli#installation): confirm that `gh status` produces a meaningful result.
2. [`gh auth login`](https://cli.github.com/manual/gh_auth_login): confirm that `gh auth status` produces a meaningful result.

TODO the usual method of running both scripts in sequence

### Generating reports from shared data

**Prerequisites**: none

TODO bootstrapping report generation from an already uploaded `reviews.txt` file

## Verification of results

In addition to reproducible reports, postings of API data collection from GitHub can also be independently verified by checking for consistency with the larger intermediate files the `rr-collect` script generates.

This is encouraged, especially for reference projects.  For this purpose, the `rr-collect` script leaves the 4 intermediate files for each collected data type, as documented in the script:
* `slurp-*-*.json`
  * Each of these 4 files are designated "slurp JSON" because they are not syntactically correct JSON, but concatenated JSON objects which become a proper, parseable JSON array when combined by `jq --slurp`.
  * Originally posted GitHub comments are fully included here: so verifiers can search for an original posting, comment, review, or review comment by keyword without having to extract the URL with `Copy link` in the GitHub UI.
* `tmp-pr-numbers.txt`
  * an unformatted list of "issue" numbers which are actually PRs (since GitHub uses the same data structure for both).

This set of `slurp-` files, since it contains the raw comment data, is much larger than the `rr-collect` output and so it wouldn't generally be posted in a GitHub project.  However:
* We might still be interested in seeing this data while these scripts _themselves_ are being validated.
* In any case, these intermediate files might also be uploaded (e.g. in `raw-review-data`) with each set of reference data.

## Behavioural notes

For a detailed view of all "data types" of review commentary mentioned above, see the [`rr-collect` script](./rr-collect) inline comments.
* Each of these has a link to the correponding GitHub API endpoint which has a further explanation of that data structure.

Note: **commit comments** are not included in the above 4 "data types" because:
* they are extremely rare on this utility's [reference project](https://github.com/cardano-foundation/CIPs);
* they are relatively invisible in the GitHub UI and therefore don't directly lead to further review;
* they are less indicative _of peer review_ since they are made _after_ changes are made, rather than proposing further changes.

In addition to `reviews.txt` feeding our reporting script, it can also be used by other tools:
* import it into a spreadsheet (as CSV file, with "space" delimiter — i.e. the main reason why the report isn't also a JSON file)
* import into a relational database — the last (URL) is unique: more concisely, everything after the `#` character

### FAQ

#### Why `gh`?

i.e. _Why is this scripted on top of `gh` rather than constructing API calls more canonically using a Javascript-like language + [`octokit`](https://github.com/octokit/octokit.js) to use the GitHub API canonically?_

**Ease of setup**: GitHub users remain free to choose whichever authentication method suits them best: especially since different repositories have different security assumptions.
* It also normalises the problem of each user getting `gh` to work on _their own_ repository(ies) of reporting interest: without the complication of using either these scripts or testing with GitHub API calls.

**Consistency of setup** (at least as far as this repo is concerned): This code can focus on clearly written scripts and leave every other question about getting `gh` running to existing user documentation.

**Security**: Using `gh` as a platform equalises the vastly different OS-dependent procedures for security your GitHub token... a huge difference between Windows and Linux "certificate store" for instance.
* By contrast, using the raw GitHub API generally requires the token be [made available in cleartext](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10) at some point in the process.

**Simplicity of design**: Using `gh api` leads to a self-documenting script that anyone can understand: rather than a structure of JS code that only the author will ever understand.
* This is mainly because `gh api` handles [pagination of GitHub queries]() natively, and so a single shell command can produce most data sets in their entirety.

* Thanks to the `gh api` front end (already chosen for GitHub token management & cross-platform consistency) the data gather can be put into a half-dozen CLI commands: and therefore a script to execute these is far simpler than JS code.

**Distribution of API access**: it forces each user to use their own GitHub token rather than [potentially limited bandwidth](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10#about-authentication) from a shared token for a certain repository... or for *this* repository.
* As recommended by GitHub, [authenticated access to GitHub API provides the highest rate limit](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10#about-authentication) and is less likely to get stuck (or fail).

#### Why aren't discussions included?

At this time they're not common on this repo's [reference project](https://github.com/cardano-foundation/CIPs) and generally don't correpond to our deliverable of standards (they are rather about processes themselves, or "meta" to our repository).

Someday this may change: and discussions may also constitute peer review for other reference projects.  In either case it would be a good idea to include — or allow optional inclusion of — the comments available through the Discussions API endpoint.
