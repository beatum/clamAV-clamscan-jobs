# ClamAV Scheduled Scans (systemd)

Systemd service and timer units for running daily focused and weekly full-system ClamAV `clamscan` jobs.

## Contents

- `clamav-scan-daily.service`: Focused daily scan of common directories.
- `clamav-scan-daily.timer`: Runs the daily scan at 05:00.
- `clamav-scan-weekly.service`: Full weekly scan of `/` with exclusions.
- `clamav-scan-weekly.timer`: Runs the weekly scan Sundays at 03:00.

## Requirements

- `clamav` and `clamav-freshclam` installed.
- `clamscan` available at `/usr/bin/clamscan`.
- `freshclam` available at `/usr/bin/freshclam`.

## Install

Copy the unit files into systemd and enable the timers:

1. Copy units:
   - `sudo cp clamav-scan-*.service /etc/systemd/system/`
   - `sudo cp clamav-scan-*.timer /etc/systemd/system/`
2. Reload systemd:
   - `sudo systemctl daemon-reload`
3. Enable and start timers:
   - `sudo systemctl enable --now clamav-scan-daily.timer`
   - `sudo systemctl enable --now clamav-scan-weekly.timer`

## Logs

- Daily scan: `/var/log/clamav/daily-scan.log`
- Weekly scan: `/var/log/clamav/weekly-full-scan.log`

## Test

Run a manual scan and check status/logs:

- `sudo systemctl start clamd@scan.service`
- `sudo systemctl start clamav-scan-daily.service`
- `sudo systemctl status clamav-scan-daily.service`
- `sudo systemctl start clamav-scan-weekly.service`
- `sudo systemctl status clamav-scan-weekly.service`
- `sudo tail -n 50 /var/log/clamav/daily-scan.log`
- `sudo tail -n 50 /var/log/clamav/weekly-full-scan.log`
`
## Notes

- The daily scan targets `/home`, `/var/www`, and `/opt`.
- The weekly scan runs across `/` with exclusions for pseudo-filesystems and Flatpak data.
- Both services treat exit code 1 (virus found) as success to avoid failed units.

## Customize

- Adjust schedules in the `.timer` files (`OnCalendar`).
- Modify targets and exclusions in the `.service` files (`ExecStart`).
- If you want to limit full scans to a single filesystem, keep `--cross-fs=no` (default).
