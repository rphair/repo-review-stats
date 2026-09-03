## CIPs repo review stats - monthly

To save space on GitHub — because the huge `reviews.txt` file (27K+ text lines and counting) is mostly the same from month to month (only adding the new month + changed lines when any older comments are edited) — this file will live _here in this directory_ and the monthly subdirectories will use it as input data.

In other words: a monthly report can be extracted (or any of the reports here verified) by running a command like this, as explained in [README > Generating reports from shared data](https://github.com/rphair/repo-review-stats#generating-reports-from-shared-data):

```bash
curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-2026-08-02T20.31.51Z/reviews.txt \
  | grep ^2026-08 \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report)
```

with:
* `grep` value updated to the current month
* an added line in the middle to filter in, or filter out, a list of GitHub users (also explained at the link above).
