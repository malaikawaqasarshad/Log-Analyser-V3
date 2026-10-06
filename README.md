# Log-Analyser-V3
This is version 3 of the log analyser.
A Python-based security log analyser designed to identify failed login activity and potential brute-force attacks.
Log Analyser V3 reads authentication logs, processes login events, tracks failed attempts by IP address and username, and analyses the timing of failed login attempts to identify potentially suspicious activity.

## Features

* Counts successful login attempts
* Counts failed login attempts
* Tracks failed login attempts by IP address
* Tracks failed login attempts by username
* Extracts and processes timestamps from log entries
* Stores individual failed login events
* Compares failed login events based on their timestamps
* Detects potential brute-force activity
* Generates a structured security report in the console

## How It Works

The analyser reads the log file line by line and processes each login event.

For each entry, the timestamp is extracted and converted into a Python `datetime` object.

The program then checks whether the event contains either:

```text
LOGIN_SUCCESS
```

or:

```text
LOGIN_FAILED
```

Successful login events are counted.

For failed login events, the analyser extracts the username and IP address and stores the event for further analysis.

## Failed Login Analysis

The analyser keeps separate counters for failed login attempts by:

### IP Address

This shows how many failed login attempts have originated from each IP address.

Example:

```text
Failed Logins by IP
------------------------------
192.0.2.15 : 3
192.0.2.30 : 2
```

### Username

This shows how many failed login attempts have occurred against each username.

Example:

```text
Failed Logins by Username
------------------------------
admin : 3
alex : 2
```

This can help identify accounts that are receiving repeated failed authentication attempts.

## Brute-Force Detection

V3 includes time-based detection for potential brute-force activity.

The analyser compares failed login events from the same IP address and calculates the time difference between them.

An IP address is flagged when the analyser finds **3 or more failed login attempts within 60 seconds**.

The detection rule can be represented as:

```text
Same IP address
      +
3 or more failed login attempts
      +
Attempts occurring within 60 seconds
      =
Potential brute-force activity
```

When suspicious activity is detected, the IP address is added to a set of suspicious IPs and reported once.

## Example Output

```text
==============================
       SECURITY LOG REPORT
==============================

Login Summary
------------------------------
Successful logins: 2
Failed logins: 5

Failed Logins by IP
------------------------------
192.0.2.15 : 3
192.0.2.30 : 2

Failed Logins by Username
------------------------------
admin : 3
alex : 2

Potential Brute-Force Activity
------------------------------
Potential brute-force activity: 192.0.2.15
```

## Log Format

The analyser expects log entries containing a timestamp, login event, username, and IP address.

For example:

```text
2026-10-06 14:32:10 LOGIN_FAILED user=admin ip=192.0.2.15
2026-10-06 14:32:25 LOGIN_FAILED user=admin ip=192.0.2.15
2026-10-06 14:32:48 LOGIN_FAILED user=admin ip=192.0.2.15
```

The timestamp is expected in the following format:

```text
YYYY-MM-DD HH:MM:SS
```

The current parser also relies on the username and IP address appearing in the expected positions within each log entry.

## Requirements

* Python 3
* A security log file
* Python's built-in `datetime` module

No external Python packages are required.

## Usage

Place the log file in the project directory and run the Python program.

For example:

```bash
python log_analyser.py
```

The analyser will process the log file and display the security report directly in the console.

## Project Structure

```text
log-analyser/
│
├── log_analyser.py
├── securityloginsV2.txt
└── README.md
```

If the project is being run as a Jupyter Notebook, the analyser can also be executed directly from the notebook.

## Version 3

Version 3 focuses on adding more detailed analysis of authentication events.

The main functionality includes:

* Timestamp parsing using `datetime`
* Storage of failed login events
* Failed login tracking by IP
* Failed login tracking by username
* Time-based failed login analysis
* Potential brute-force detection
* Suspicious IP tracking

The project currently uses a simple rule-based approach to identify potentially suspicious behaviour.

## Detection Example

Consider the following events:

```text
14:32:10 LOGIN_FAILED user=admin ip=192.0.2.15
14:32:25 LOGIN_FAILED user=admin ip=192.0.2.15
14:32:48 LOGIN_FAILED user=admin ip=192.0.2.15
```

All three events originate from the same IP address and occur within 60 seconds.

The analyser therefore reports:

```text
Potential brute-force activity: 192.0.2.15
```

This does not prove that the IP address is malicious. It indicates that the activity matches the detection rule and may require further investigation.

## Limitations

This project is intended as a learning and development project rather than a production security monitoring system.

Current limitations include:

* The log format must follow the expected structure
* The brute-force threshold is currently fixed at 3 failed attempts
* The detection window is currently fixed at 60 seconds
* The analyser processes a static log file rather than monitoring logs in real time
* Detection is based on IP address and timing rather than more advanced behavioural analysis
* The report is currently displayed in the console

## Future Improvements

Possible future improvements include:

* Configurable detection thresholds
* Configurable time windows
* Real-time log monitoring
* CSV or JSON report generation
* More detailed alerts
* Detection of multiple usernames being targeted from one IP
* Detection of one username being targeted from multiple IP addresses
* Graphs and visualisations
* Command-line arguments for selecting log files
* Improved handling of invalid or unexpected log entries
* More advanced anomaly detection

## Disclaimer

This project is intended for educational purposes and for analysing logs that you are authorised to access.

The brute-force detection feature identifies potentially suspicious activity based on predefined rules. It should not be treated as definitive proof of malicious activity.

## License

This project is licensed under the MIT License.
