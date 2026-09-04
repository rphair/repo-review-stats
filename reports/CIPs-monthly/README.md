## CIPs repo review stats - monthly

To save space on GitHub — because the huge `reviews.txt` file (27K+ text lines and counting) is seen by `git` as mostly the same from month to month (only adding the new month + changed lines when any older comments are edited) — this file will live _here in this directory_ and the monthly subdirectories will use this same pathname every time as input data.

Therefore, a report for any month can be extracted (or any of the reports here verified) by running a command like this, as explained in [README > Generating reports from shared data](https://github.com/rphair/repo-review-stats#generating-reports-from-shared-data):

```bash
curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report)
```

with:
* the `grep` argument updated for the target month
* (optional) a line in the middle to filter for or against the current list of [CIP editors](https://github.com/cardano-foundation/CIPs#editors) (also demonstrated at the README link above).

For additional verification, the intermediate files (`slurp*` and `tmp*`) generated in each latest build of `reviews.txt` are also included & updated monthly here.
* The `slurp*` files are also huge but can be practically included here since these also will only be updated incrementally by `git`.
* Auditors and other interested parties can verify that the full list of raw review data matches GitHub _and_ that each of these review items is included in the `reviews.txt` file.
* If any comments are missing in the results, please file an [issue in this repo](https://github.com/rphair/repo-review-stats/issues) with the full URLs of any missing comments.
