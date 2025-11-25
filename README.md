# 🐍 DNSTwist Scanner
DNSTwist Enrichment & Reporting Helper

This Python script integrates the **`dnstwist`** tool with the **URLScan.io** API to automate the detection and analysis of potential typosquatting domains. It reads base domains from a CSV file, generates their variants (permutations), checks their DNS/WHOIS status, performs security scans via URLScan.io to collect reports and screenshots, and finally saves all results into a nicely formatted Excel spreadsheet.

Great for quickly reviewing potential typosquats / look-alike domains for a list of base domains.

---

## 🌟 Features

* **Automated Permutation Generation:** Uses the command-line tool `dnstwist` to generate a wide range of domain name variants (e.g., through typosquatting, bit-flipping, homographs).
* **Comprehensive DNS Validation:** Fetches **A**, **AAAA**, **NS**, and **MX** records for each permutation to quickly identify active infrastructure.
* **WHOIS Enrichment:** Records key WHOIS information (`created`, `updated`, `registrar`) and the full raw WHOIS text for detailed registration analysis.
* **URLScan.io Integration (Optional):**
    * Submits domain URLs to **URLScan.io** for dynamic analysis.
    * Automatically polls the API, waiting for the scan to complete.
    * Downloads screenshots and embeds them directly into the Excel file.
    * Supports custom scan visibility settings (`private`, `unlisted`, `public`).
* **Intelligent Rate Limiting:** Includes a soft rate-limiting mechanism to respect URLScan.io API usage limits.
* **Excel Reporting:** Compiles all collected data into a clean, easy-to-read, and well-formatted `.xlsx` file, complete with auto-sized columns and embedded screenshots.

---

## ⚙️ Requirements

The following tools and libraries are required to run the script:

1.  **Python 3**
2.  **`dnstwist`** (Command-line tool)
3.  **`whois`** (Command-line tool, often pre-installed on Linux/macOS)
4.  Python Libraries (install via `pip`):
    ```bash
    pip install requests dnspython openpyxl Pillow
    ```

---

## 🛠️ Configuration

Edit the `CONFIG` section at the top of the `DNSTwist.py` file to set your API key and options:

```python
# ---------------- CONFIG ----------------
# Define your input and output file names 👇
INPUT_CSV   = "twist.csv"
OUTPUT_XLSX = "dnstwist_results.xlsx"

# Put your API key here 👇
URLSCAN_API_KEY    = "<URL SCAN API KEY HERE>" # Required for URLScan.io functionality
URLSCAN_VISIBILITY = "private"   # can be "private", "unlisted", or "public"

# API / request safety limits
MAX_PER_MINUTE = 120
# -----------------------------------------
```

### Input File (`twist.csv`)

The script reads base domains from `twist.csv` in the current directory.

**Supported formats:**

The CSV file can have a header row with the column named `domain`, or simply be a list of domains without a header.

**Format 1: With Header**

```csv
domain
base-company.com
main-domain.net
```

**Format 2: Without Header**

```csv
base-company.com
main-domain.net
```

---

## 🚀 Usage

1.  Ensure you have met all the **Requirements** (Python, `dnstwist`, `whois`, and Python libraries).
2.  Configure your **URLSCAN\_API\_KEY** and output file names in the `CONFIG` section of `DNSTwist.py`.
3.  Create your input file named `twist.csv`.
4.  Run the script from your command line:

    ```bash
    python DNSTwist.py
    ```

Upon completion, the output file (`dnstwist_results.xlsx` by default) will be saved in the same directory. A final confirmation message will show the total number of rows saved.

---

## 📊 Output File Columns

The analysis compiles all data into a well-structured Excel spreadsheet. Each row represents a single domain permutation.

| Excel Header | Description |
| :--- | :--- |
| **input\_domain** | The original base domain provided in the CSV file. |
| **permutation** | The generated potential typosquatting domain variant. |
| **fuzzer** | The specific `dnstwist` method that generated this permutation (e.g., `bit-squatting`, `addition`). |
| **dns\_a** | Resolved IPv4 (A) records. |
| **dns\_aaaa** | Resolved IPv6 (AAAA) records. |
| **dns\_ns** | Resolved Name Server (NS) records. |
| **mx** | Resolved Mail Exchanger (MX) records. |
| **geoip** | Geographical location of the IP address (from `dnstwist`). |
| **whois\_created** | Domain creation date (from `dnstwist`). |
| **whois\_updated** | Domain last updated date (from `dnstwist`). |
| **whois\_registrar** | Domain registrar (from `dnstwist`). |
| **urlscan\_report** | Link to the full URLScan.io report for dynamic analysis. |
| **screenshot** | Website screenshot retrieved from URLScan.io (embedded as an image in the cell). |
| **whois\_full** | The complete, raw text output from the `whois` command. |

---

## ⚠️ Notes

* **API Usage:** The script includes a mechanism to pause execution if the URLScan.io request rate exceeds 120 requests per minute (`MAX_PER_MINUTE`).
* **Timeouts:** DNS lookups have short timeouts (3.0s lifetime, 2.0s timeout), while the full `whois` command has a longer timeout (25s).
* **Screenshot Size:** Embedded screenshots are scaled down to a maximum width of 320 pixels for better readability within the Excel report.
* **System Dependencies:** The `dnstwist` and `whois` command-line tools must be accessible in your system's `PATH`.
