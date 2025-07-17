# Log Parser & Bot Detector

## Overview

This script fetches a web-server access log, parses it using a regular expression, detects and filters out bot traffic, and saves the cleaned data for analysis.

## Prerequisites

* Python 3.7+
* Internet access
* Python packages:

  * `pandas`
  * `requests`
  * (built-in) `re`

Install dependencies:

```bash
pip install pandas requests
```

## Installation

1. Clone or download this repository.
2. Ensure dependencies are installed.

## Usage

Run the script:

```bash
python parse_and_clean.py
```

It performs:

1. **Download** the log via the `url` variable.
2. **Inspect** and strip each line.
3. **Parse** fields: `ip`, `user`, `country_code`, `timestamp`, `method`, `url`, `protocol`, `status`, `size`, `referrer`, `user_agent`, `trailing_number`.
4. **Convert** `timestamp` to datetime and numeric fields (`status`, `size`, `trailing_number`).
5. **Detect bots** by checking for “bot” in `user_agent` or missing `referrer`.
6. **Split** traffic into human vs. bot.
7. **Filter out** bot requests and save the clean log to `cleaned_logs.csv`.
8. **Report** summary counts.

## Configuration

* **`url`**: Update to point to your own log file.
* **Regex**: Modify `log_pattern` for custom log formats.
* **Bot criteria**: Adjust the `is_bot` condition as needed.

## Log Format

Expected extended CLF with an extra `country_code` field:

```
<ip> <ident> <user> <country_code> [timestamp] "<method> <url> <protocol>" <status> <size> "<referrer>" "<user_agent>" <trailing_number?>
```

## Bot Detection Logic

* **Bot** if `user_agent` contains “bot” (case-insensitive) OR `referrer` is `-`.
* **Human** otherwise.

## Output

* **`cleaned_logs.csv`**: All non-bot requests.
* **`parsed_humans.csv`**, **`parsed_bots.csv`**: Optional splits before cleaning.

## License

This project is released under the IEUK 2025 Technology and Engineering License.
