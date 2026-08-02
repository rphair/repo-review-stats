## Project review snapshots, subsets, and sample reports

Subfolders here are named `<repo-name>` + `-` + `UTCdate`: with date formatted as on last line of collected data = the date of the repo's last posting (approximately the current date).

> [!IMPORTANT]
> In these subfolder (subdirectory) names, the `:` character appearing in the UTC standard formatted dates in the collected data file has been replaced by `.` to avoid causing problems on Windows filesystems.

Contents: outputs from `rr-collect` (or subsets, for certain date ranges and/or users) are named `*.txt`, paired with the similarly named reports they produce named `*.md`.

Long reports (output from `rr-report`) might be further split into 2 files for clarity, since the SIZE and COUNT tables appear in the same stream: e.g. similarly named file pairs `*-size.md` and `*-count.md`.

