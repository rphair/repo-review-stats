## CIPs repo review stats - monthly - August 2026

### [CIP editors only](reviews-editors.md)

```bash
curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | awk '$2 ~ /^(rphair|Ryun1|perturbing)$/' \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report) \
  > reviews-editors.md
```

### [All users except CIP editors](reviews-non-eds.md)

```bash
curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | awk '$2 !~ /^(rphair|Ryun1|perturbing)$/' \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report) \
  > reviews-non-eds.md
```

### [All users](reviews.md)

```bash
curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report) \
  > reviews.md
```

(see link for full listing)
