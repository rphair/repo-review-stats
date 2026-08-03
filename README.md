## `repo-review-stats` - produce lists of most active project reviewers

This accumulates all repository postings — issues, PRs, comments, and reviews.  All text comments are considered useful to peer review: as opposed to commits which are already tracked for each GitHub repo and have more traditional tools for performance analysis.

> [!NOTE]
> Therefore this simply counts all _writing_ on a repo, since all of this is considered useful *in a peer review context*.

Division into two scripts is intended to keep these reports deterministic & reproducible, to support well-informed project collaboration, reporting & audit:
* `rr-collect` - produces a single data file (text, space delimited) with 4 fields:
  * the UTC format date of a posting (sorted by this field)
  * the GitHub username
  * the number of raw Markdown characters ("size") in that posting
  * a deep link in the GitHub UI for that comment (this, or more practically everything after the `#`, can be used as a unique key)
* `rr-report` - taking collected data in this format in standard input, outputs to standard output two tabulations for each user:
  * ranked by total sizes of all postings
  * ranked by count of all postings

Reference data collections & reports are in [`/reports`](./reports) for the reference projects at the time they were indexed: along with fixed reports for data subsets of interest to a project.

### Usage

This is designed for reproducible results, so that:
* multiple runs by different users will always produce the same `report.txt` from `rr-collect` (with any more recently posted comments at the end of a file)
* data collected for a reference project can re-used independently.

Common prerequisites to using any of the scripts by any method:
* a UNIX-like environment, in terms of shell & filename references.
* a recent version of `jq` (tested with `1.8` and [easily downloadable](github.com/jqlang/jq/releases/latest) if yours is older)

#### Generating your own reports

Prerequisites:
1. [GitHub CLI > Installation](https://github.com/cli/cli#installation): confirm that `gh status` produces a meaningful result.
2. [`gh auth login`](https://cli.github.com/manual/gh_auth_login): confirm that `gh auth status` produces a meaningful result.

TODO the usual method of running both scripts in sequence

#### Generating reports from shared data

Prerequisites: none

TODO bootstrapping report generation from an already uploaded `reviews.txt` file

### Verification of results

In addition to reproducible reports, postings of API data collection from GitHub can also be independently verified by checking for consistency with the larger intermediate files the `rr-collect` script generates.

This is encouraged, especially for reference projects.  For this purpose, the `rr-collect` script leaves the 4 intermediate files for each collected data type, as documented in the script:
* `slurp-*-*.json`
  * each of these 4 files are designated "slurp JSON" because these are not syntactically correct JSON, but concatenated JSON objects which become a proper, parseable `json` array when combined by `jq --slurp`.
  * originally posted GitHub comments are fully included here, so verifiers can search for an original posting, comment, review, or review comment by keyword without having to extract the URL with `Copy link` in the GitHub UI.
* `tmp-pr-numbers.txt`
  * a list of "issue" numbers which are actually PRs (since GitHub uses the same data structure for both).

The first group of raw files, since it contains the raw comment data, is much larger than the `rr-collect` output and so it wouldn't generally be posted in the results directory.
* We might still be interested in seeing this data while the scripts _themselves_ are being validated: so the intermediate files might also be uploaded (e.g. in `raw-review-data`) along with the reference data.

### Behavioural notes & FAQ

For a detailed view of all "data types" of public commentary mentioned above, see the [`rr-collect` script](./rr-collect) inline comments.
* Each of these has a link to the correponding GitHub API endpoint which has a further explanation of that data structure.

Note: **commit comments** are not included in the above 4 "data types" because:
* they are extremely rare on this utility's [reference project](https://github.com/cardano-foundation/CIPs);
* they are relativly invisible in the GitHub UI and therefore don't directly lead to further review;
* they are less indicative _of peer review_ since they are made _after_ changes are made, rather than proposing further changes.

#### Why `gh`?

i.e. _Why is this scripted on top of `gh` rather than constructing API calls more canonically using a Javascript-like language + [`octokit`](https://github.com/octokit/octokit.js) to use the GitHub API canonically?

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
