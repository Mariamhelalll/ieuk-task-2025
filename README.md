# Log Parser & Bot Detector

## Overview

This Python script downloads a web-server access log from a GitHub repository, parses each entry using a regular expression, and separates the traffic into human vs. bot requests. The results are saved as two CSV files: `parsed_humans.csv` and `parsed_bots.csv`.

## Prerequisites

* Python 3.7+
* Internet access to fetch the raw log URL
* Required Python packages:

  * `pandas`
  * `requests`
  * (built-in) `re`

Install dependencies with:

```bash
pip install pandas requests
```

## Installation

1. Clone this repository or copy the script into your project folder.
2. Ensure dependencies are installed.

## Usage

Run the script directly:

```bash
python parse_and_detect.py
```

This will:

1. Download the log from the `url` defined at the top of the script.
2. Inspect and strip each line.
3. Parse each line into fields (`ip`, `user`, `country_code`, `timestamp`, `method`, `url`, `protocol`, `status`, `size`, `referrer`, `user_agent`, `trailing_number`).
4. Convert `timestamp` to datetime and numeric fields (`status`, `size`, `trailing_number`).
5. Detect bot traffic by checking for the substring “bot” in the User-Agent or missing referrer.
6. Write human requests to `parsed_humans.csv` and bot requests to `parsed_bots.csv`.
7. Print a summary of total, human, and bot request counts.

## Configuration

* **`url`**: Change the GitHub raw URL to point at your own log file.
* **Parsing regex**: Adjust the `log_pattern` if your logs differ in fields or order.
* **Bot detection**: Modify the `df['is_bot']` logic to apply custom heuristics (e.g., additional substrings).

## Log Format

The script expects lines in this extended format:

```
<ip> <ident> <user> <country_code> [<timestamp>] "<method> <url> <protocol>" <status> <size> "<referrer>" "<user_agent>" <optional trailing_number>
```

* **`<ip>`**: IPv4 address (e.g., `127.0.0.1`)
* **`<ident>`**: RFC 1413 identity of the client (`-` if unused)
* **`<user>`**: Authenticated user name (`-` if unused)
* **`<country_code>`**: Extra token inserted before timestamp
* **`[<timestamp>]`**: Apertured datetime with timezone (e.g. `[24/Oct/2007:12:42:35 -0400]`)
* **`"<method> <url> <protocol>"`**: HTTP request line
* **`<status>`**: HTTP status code
* **`<size>`**: Response size in bytes or `-`
* **`"<referrer>"`**, **`"<user_agent>"`**: Optional quoted fields
* **`<trailing_number>`**: Optional numeric suffix

## Regex Explanation

```python
log_pattern = re.compile(r"""
^
(?P<ip>\d{1,3}(?:\.\d{1,3}){3})\s+      # IP address
\S+\s+                                   # ident
(?P<user>\S+)\s+                        # user
(?P<country_code>\S+)\s+                # country_code
\[(?P<timestamp>[^\]]+)\]\s+           # timestamp
"(?P<method>\S+)\s+(?P<url>\S+)\s+(?P<protocol>[^\"]+)"\s+  # request
(?P<status>\d{3})\s+                    # status
(?P<size>\d+|-)\s+                      # size
"(?P<referrer>[^\"]*)"\s+             # referrer
"(?P<user_agent>[^\"]*)"\s*           # user_agent
(?P<trailing_number>\d+)?                # trailing_number
$                                         # end of line
""", re.VERBOSE)
```

Each named group becomes a DataFrame column.

## Traffic Classification

The script flags a request as **bot** if:

* The User-Agent contains the substring `bot` (case-insensitive), **OR**
* The referrer field is empty (`-`).

All other requests are considered **human**.

## Output

* `parsed_humans.csv`: All requests classified as human.
* `parsed_bots.csv`: All requests classified as bots.

## Contributing

Feel free to open issues or PRs to:

* Extend bot-detection heuristics
* Support different log formats
* Add rate-based filtering (requests/minute)

## License

This project is released under the IEUK 2025 Technology and Engineering License.
