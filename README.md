# `repo-review-stats` - tabulate project reviewers by activity level

This accumulates all repository postings — issues/PRs, comments, reviews, and review comments — then sorts these combined sets by contributor according to text volume _and_ post count.

_Why?_  All text comments are considered useful to _peer review_: as opposed to commits which are already tracked for each GitHub repo and have more traditional tools for performance analysis.

> [!NOTE]
> Therefore this simply counts all _writing_ on a repo, since all of this is considered useful *in a peer review context*: as opposed to the "software development" context which considers this material irrelevant.

Division into two scripts is intended to keep these reports deterministic & reproducible; to support cooperative project reporting, audit, and division of labour:
* `rr-collect` - produces a single data file (text, space delimited) with 4 fields:
  * the UTC format date of every posting (sorted by this field)
  * the GitHub user who posted it
  * the number of raw Markdown characters ("size") in that posting
  * the unique deep link in the GitHub UI for that posting
* `rr-report` - taking collected data in this format in standard input, outputs to standard output (in Markdown format) two tabulations for each user:
  * first, ranked by total size of all postings
  * then, ranked by count of all postings

Reference data collections & reports are in [`/reports`](./reports) for the reference projects at the time they were indexed: along with fixed reports for data subsets of interest to a project.

## Usage

This is designed for reproducible results, so that:
* Multiple runs by different users will always produce the same `reviews.txt` from `rr-collect` (differing perhaps by any more recently posted comments at the end).
* Data collected for a reference project can re-used by different users.

**Common prerequisites** to using any of the scripts by any method:
* a UNIX-like environment, in terms of shell & filename references
* a recent version of `jq` (these scripts tested with `1.8`: [easily downloadable](https://github.com/jqlang/jq/releases/latest) if yours is older)

### Generating your own reports

**Prerequisites**:
1. [GitHub CLI > Installation](https://github.com/cli/cli#installation): confirm that `gh status` produces a meaningful result.
2. [`gh auth login`](https://cli.github.com/manual/gh_auth_login): confirm that `gh auth status` shows an accessible GitHub token.

Recommended steps:
* Get local copies of the scripts `rr-collect` and `rr-report` and ensure they are executable and in your search path.
* Update the `rr-collect` script `owner` and `repo` variables if using a repo different than [our reference repository](https://github.com/cardano-foundation/CIPs).
* Create an empty directory and `cd` into it.
* Run `rr-collect`:
  * The shortest tasks run first, are displayed as they are executed, and will cause the script to terminate upon any error (generally a GitHub API failure).
  * All the waiting time is for the GitHub API to return from repeated (paginated) calls: so these times are proportional to the size of the repository * the size of that data structure.
  * Without any Internet delays the total collection time for our reference repository is currently (mid-2026) _14 minutes_.
* Filter the output file `reviews.txt` however needed for your targeted results:
  * The file is in chronological order: so you can target a time span by deleting lines from the beginning and/or end.
  * You can also select on the second field (the GitHub username) with `grep` or `grep -v` to report on a list of users ***or*** everyone _other than_ that list of users.
* Run `rr-report` on the target file and collect standard output:
```
rr-report < reviews.txt > reviews.md
```
The Markdown result will have 2 tables of reviewers sorted by review SIZE and then review COUNT:
* For long tables, it will probably help to split this file (exactly down the middle; since the set of users is the same) into those two files: one for each table.
* See the [initial test data](https://github.com/rphair/repo-review-stats/tree/main/reports/CIPs-2026-07-29T14.13.59Z) for some examples of how to prepare data sets: with input & output data paired together so the results can be readily verified.

### Generating reports from shared data

**Prerequisites**: none

**TODO** (by end of day **2026-08-04**) bootstrapping report generation from an already uploaded `reviews.txt` file

## Verification of results

In addition to reproducible reports, postings of API data collection from GitHub can also be independently verified by checking for consistency with the larger intermediate files the `rr-collect` script generates.   This can be especially useful when the results are considered unlikely or otherwise disputable.

For this purpose, the `rr-collect` script leaves behind its intermediately generated files, as documented in the script:
* `slurp-*-*.json`
  * Each of these 4 raw API result files are designated "slurp JSON" — they are not syntactically correct JSON, but concatenated JSON objects which become a proper, parseable JSON array when combined by `jq --slurp`.
  * Originally posted GitHub comments are fully included here: to facilitate testing by search for an original posting, comment, review, or review comment by keyword without having to extract its URL with `Copy link` in the GitHub UI.
* `tmp-pr-numbers.txt`
  * an unformatted list of all "issue" numbers which are actually PRs (since GitHub uses the same data structure for both).

The set of `slurp-` files, since it contains the raw comment data, is much larger than the `rr-collect` output and so it wouldn't generally be posted in a GitHub repo.  However:
* We might still be interested in seeing this data while these scripts _themselves_ are being validated.
* In this case, these intermediate files should also be uploaded (e.g. in `raw-review-data`) with each set of reference data.

## Behavioural notes

For a detailed view of all "data types" of review commentary mentioned above, see the [`rr-collect` script](./rr-collect) inline comments.
* Each of these has a link to the correponding [GitHub REST API endpoint](https://docs.github.com/en/rest?apiVersion=2026-03-10) which has a further explanation of that data structure.

Note: _commit **comments**_ are not included in the above 4 "data types" because:
* they are extremely rare on this utility's [reference project](https://github.com/cardano-foundation/CIPs);
* they are relatively invisible in the GitHub UI and therefore don't directly lead to further review;
* they are less indicative _of peer review_ since they are made _after_ changes are made, rather than proposing further changes.

In addition to `reviews.txt` feeding our reporting script, it can also be used by other tools as:
* import into a spreadsheet (as CSV file, with "space" delimiter — the main reason why the report isn't also a JSON file);
* import into a relational database — the last column (URL) is unique enough to be a primary key: more concisely, everything after the `#` character.

### FAQ

#### Why `gh`?

i.e. _Why is this scripted on top of [GitHub CLI](https://github.com/cli/cli#github-cli) rather than constructing API calls using a Javascript-like language + [`octokit`](https://github.com/octokit/octokit.js) to use the GitHub API more canonically?_

**Ease of setup**: GitHub users remain free to choose whichever [authentication method](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10) suits them best: especially since different repositories have different security assumptions.
* It also normalises the problem of each user getting `gh` to work on _their own_ repository of reporting interest: without the complication of having to test their access through either these scripts or other GitHub API calls.

**Consistency of setup** (at least as far as this repo is concerned): This code can focus on clearly written scripts and leave every other question about getting `gh` running to existing user documentation.

**Security**: Using `gh` as a platform equalises the vastly different OS-dependent procedures for securely storing & recalling your GitHub token... a huge difference between Windows and Linux "certificate store" for instance.
* By contrast, using the raw GitHub API generally requires the token be [made available in cleartext](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10) at some point in the process.

**Simplicity of design**: Using `gh api` leads to a self-documenting script that anyone can understand: rather than a structure of JS code that only its author would ever understand.
* This is mainly because `gh api` handles [pagination of GitHub queries](https://docs.github.com/en/rest/using-the-rest-api/using-pagination-in-the-rest-api?apiVersion=2026-03-10) natively: allowing a single shell command to produce most data sets in their entirety.
* Thanks to the `gh api` front end (already chosen for GitHub token management & cross-platform consistency) all data gathering can be done with a half-dozen CLI commands: and therefore a script to execute these is far simpler than JS code.

**Distribution of API access**: Each user must use their own (or their project's) GitHub token, which avoids [potentially limited bandwidth](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10#about-authentication) they would have from a publicly shared token that one might have on a typical "scraper" repository... or would have by running these scripts through GitHub workflow automation, which can impose very strict bandwidth limitations.
* As recommended by GitHub, [authenticated access to GitHub API provides the highest rate limit](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2026-03-10#about-authentication) and is less likely to get stuck (or fail).

#### Why aren't Discussions included?

At this time [GitHub Discussions](https://docs.github.com/en/discussions) are [not common on this repo's reference project](https://github.com/cardano-foundation/CIPs/discussions) and generally don't correpond to our standards deliverable: rather, they are mainly about processes themselves, or "meta" to our repository.

Someday this may change, or discussions might also constitute peer review for other reference projects.  In either case then it would be an improvement to include — or allow optional inclusion of — the comments available through the Discussions API endpoint.

#### Why can't we optimise repeated runs of the script by incremental update?

Because the nature of review means that we will never know when a comment that's relevant today can be applied to an issue or PR that was considered relevant a long time ago.

Therefore, targeting anything but the entire duration of the repository will, in general, orphan comments from their review context _or_ the other way around.

For a simple example: think of a 5-year-old issue that may remain open until some contingency is fixed.  In this case, we never would want to penalise a reviewer or contributor that was able to supply the missing information to the resolution of a long-standing problem.

Practically speaking: if summaries are to be obtained for monthly activity, as already recommended above you would have to `rr-collect` the entire repository (or use an already posted collection) and then filter out each month that you were interested in.
