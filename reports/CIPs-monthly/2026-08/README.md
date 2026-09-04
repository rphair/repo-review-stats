## CIPs repo review stats - monthly - August 2026

To verify results, ensure [`gh` is installed](https://github.com/cli/cli#installation) and then run any command below.

### [CIP editors only](reviews-editors.md)

```bash
curl -s \
  https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | awk '$2 ~ /^(rphair|Ryun1|perturbing)$/' \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report) \
  > reviews-editors.md
```

| ***user*** | ***review SIZE*** | ***% total 162738*** |
| --- | --- | --- |
| rphair | 157011 | 96.480% |
| perturbing | 4602 | 2.827% |
| Ryun1 | 1125 | .691% |

| ***user*** | ***review COUNT*** | ***% total 326*** |
| --- | --- | --- |
| rphair | 264 | 80.981% |
| Ryun1 | 32 | 9.815% |
| perturbing | 30 | 9.202% |

### [All users except CIP editors](reviews-non-eds.md)

```bash
curl -s \
  https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | awk '$2 !~ /^(rphair|Ryun1|perturbing)$/' \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report) \
  > reviews-non-eds.md
```
| ***user*** | ***review SIZE*** | ***% total 160782*** |
| --- | --- | --- |
| paweljakubas | 16331 | 10.157% |
| mariusgeorgescu | 14485 | 9.009% |
| colll78 | 14166 | 8.810% |
| styamanda | 13088 | 8.140% |
| SeungheonOh | 11477 | 7.138% |
| kingslavcho | 10006 | 6.223% |
| yHSJ | 7459 | 4.639% |
| kwxm | 7388 | 4.595% |
| sierkov | 6844 | 4.256% |
| papag00se | 4766 | 2.964% |
| nemo83 | 4571 | 2.842% |
| zliu41 | 4480 | 2.786% |
| WhatisRT | 3988 | 2.480% |
| fallen-icarus | 3184 | 1.980% |
| michaelpj | 3177 | 1.975% |
| ch1bo | 3151 | 1.959% |
| skoniog | 2980 | 1.853% |
| polinavino | 2798 | 1.740% |
| willjgould | 2769 | 1.722% |
| Kammerlo | 2344 | 1.457% |
| lehins | 2263 | 1.407% |
| adavault-ops | 2117 | 1.316% |
| wadler | 2069 | 1.286% |
| ana-pantilie | 1755 | 1.091% |
| aniketd | 1451 | .902% |
| Scitz0 | 1264 | .786% |
| thenic95 | 1185 | .737% |
| Quantumplation | 961 | .597% |
| david-a-clark | 943 | .586% |
| coot | 923 | .574% |
| paluh | 918 | .570% |
| carlostome | 869 | .540% |
| Unisay | 815 | .506% |
| AndrewWestberg | 801 | .498% |
| Cerkoryn | 733 | .455% |
| HinsonSIDAN | 414 | .257% |
| nazrhom | 392 | .243% |
| waalge | 368 | .228% |
| kenerik | 268 | .166% |
| MadOrkestra | 265 | .164% |
| Kronoshus | 187 | .116% |
| keyan-m | 148 | .092% |
| thaddeusdiamond | 107 | .066% |
| jasagredo | 104 | .064% |
| hjeljeli32 | 10 | .006% |

| ***user*** | ***review COUNT*** | ***% total 295*** |
| --- | --- | --- |
| kwxm | 45 | 15.254% |
| colll78 | 34 | 11.525% |
| paweljakubas | 27 | 9.152% |
| zliu41 | 26 | 8.813% |
| michaelpj | 25 | 8.474% |
| SeungheonOh | 19 | 6.440% |
| lehins | 15 | 5.084% |
| kingslavcho | 12 | 4.067% |
| fallen-icarus | 7 | 2.372% |
| carlostome | 7 | 2.372% |
| aniketd | 7 | 2.372% |
| coot | 6 | 2.033% |
| Quantumplation | 5 | 1.694% |
| mariusgeorgescu | 5 | 1.694% |
| styamanda | 4 | 1.355% |
| polinavino | 4 | 1.355% |
| nemo83 | 4 | 1.355% |
| Kammerlo | 4 | 1.355% |
| AndrewWestberg | 4 | 1.355% |
| yHSJ | 3 | 1.016% |
| WhatisRT | 3 | 1.016% |
| willjgould | 2 | .677% |
| wadler | 2 | .677% |
| keyan-m | 2 | .677% |
| HinsonSIDAN | 2 | .677% |
| ch1bo | 2 | .677% |
| waalge | 1 | .338% |
| Unisay | 1 | .338% |
| thenic95 | 1 | .338% |
| thaddeusdiamond | 1 | .338% |
| skoniog | 1 | .338% |
| sierkov | 1 | .338% |
| Scitz0 | 1 | .338% |
| papag00se | 1 | .338% |
| paluh | 1 | .338% |
| nazrhom | 1 | .338% |
| MadOrkestra | 1 | .338% |
| Kronoshus | 1 | .338% |
| kenerik | 1 | .338% |
| jasagredo | 1 | .338% |
| hjeljeli32 | 1 | .338% |
| david-a-clark | 1 | .338% |
| Cerkoryn | 1 | .338% |
| ana-pantilie | 1 | .338% |
| adavault-ops | 1 | .338% |

### [All users](reviews.md)

```bash
curl -s \
  https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/reports/CIPs-monthly/reviews.txt \
  | grep ^2026-08 \
  | bash <(curl -s https://raw.githubusercontent.com/rphair/repo-review-stats/refs/heads/main/rr-report) \
  > reviews.md
```

(see link for full listing)
