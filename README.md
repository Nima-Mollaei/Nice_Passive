# NicePassive 🕵️‍♂️

**NicePassive** is a passive reconnaissance tool designed for Bug Bounty hunters and security researchers. It automatically gathers URLs from multiple passive sources and produces **clean, useful, low-noise output** for efficient vulnerability hunting.

This tool is optimized for **Mass Hunting / Automation pipelines** and focuses on extracting only high‑value endpoints.

---

## ✨ Features

* Fully passive URL collection (no direct interaction with targets)
* Integrates:

  * `gau`
  * `waybackurls`
* Smart filtering of useless/static files (js, css, img, pdf, mp4, etc.)
* Duplicate removal
* Per-domain structured output
* Supports:

  * Single target
  * File input
  * stdin / pipe input

---

## 🧠 Design Philosophy

> High-volume passive input → smart filtering → small, high-value output

Instead of thousands of noisy URLs, NicePassive gives you a clean dataset ready for real hunting.

---

## ⚙️ Integrated Tools

### 1. gau (GetAllURLs)

Collects URLs from public sources:

* Wayback Machine
* Common Crawl
* AlienVault

### 2. waybackurls

Extracts historical URLs from the Wayback Machine

### 3. uro (URL Reducer & Optimizer)

> ⚠️ Important

`uro` **must only be used in Mass Hunting / Automation mode**.

### uro behavior:

* Normalizes similar URLs
* Removes structurally duplicate endpoints
* Optimizes output for automation

### Usage logic:

| Mode                   | uro        |
| ---------------------- | ---------- |
| Mass Hunt / Automation | ✅ Enabled  |
| Manual Hunt            | ❌ Disabled |
| Single Target          | ❌ Disabled |

**Result:**

* Manual Hunt → **No DUP Filtering**
* Automation → **Smart Deduplication**

Inside the code:

```python
# Delete uro if u work manual_hunt -> single_target
```

---

## 🔧 Customization & Extensibility

NicePassive is designed to be **easily extensible**.

### ➕ Add your own commands

You can add any additional tools or commands directly into the script:

```python
####################### Add Commands here############################
commands = [
    f"echo https://{domain}/ | tee {temp_file}",
    f"echo {domain} | waybackurls | sort -u | uro | tee -a {temp_file}",
    f"gau {domain} --threads 1 --subs | sort -u | uro | tee -a {temp_file}"
]
######################################################################
```

Just append your custom command to the `commands` list.

Examples:

* `katana`
* `hakrawler`
* `httpx`
* custom scripts
* internal tools

This makes NicePassive a **modular passive recon framework**, not just a single-purpose script.

---

## ⚠️ Manual Hunting Mode

If you are doing **manual hunting / single target testing**:

> ❗ You should remove `uro` from the commands.

### Reason:

`uro` performs aggressive normalization and deduplication which is ideal for automation, but it can hide:

* unique parameter structures
* logic flows
* valuable edge-case endpoints

### Recommendation:

| Mode          | uro        |
| ------------- | ---------- |
| Mass Hunting  | ✅ Enabled  |
| Manual Hunt   | ❌ Disabled |
| Single Target | ❌ Disabled |

Manual Hunt philosophy:

> Visibility > Automation

---

## 📦 Installation

```bash
# gau
sudo apt install golang -y
go install github.com/lc/gau/v2/cmd/gau@latest

# waybackurls
go install github.com/tomnomnom/waybackurls@latest

# uro
go install github.com/s0md3v/uro@latest
```

Add Go binaries to PATH:

```bash
export PATH=$PATH:~/go/bin
```

---

## 🚀 Usage

### Single Target

```bash
./nice_passive example.com
```

### Pipe Input

```bash
echo example.com | ./nice_passive
```

### Multiple Targets

```bash
cat domains.txt | ./nice_passive
```

or:

```bash
./nice_passive domains.txt
```

---

## 📁 Output

For each domain, a file is created:

```
example.com.passive
```

Output contains:

* Only valuable URLs
* No static files
* No noise
* No duplicates

---

## 🛠 Suitable For

* Endpoint discovery
* Parameter mining
* XSS hunting
* LFI / RFI
* SSRF
* IDOR
* Logic bugs

---

## ⚠️ Legal & Ethical Notice

This tool is intended **only** for:

* Bug Bounty
* Security research
* Authorized testing

Any illegal use is strictly the responsibility of the user.


