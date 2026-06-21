<img width="250" height="250" alt="Image" src="https://github.com/user-attachments/assets/214e4074-fb2b-4fa1-9db7-3b183ae0351f" />

# ByteTheTaxman

Automatically recover sales tax from email receipts and create Quicken-ready split transactions for year-end tax reporting.  License terms are still to be decided, and currently looking for contributors or sponsors to help cover trademark cost and a Claude Pro plan to help speed development along.  I am also seeking to join the Quicken Affiliate Program, but until then:

**This project is not affiliated with or endorsed by Quicken.**

## Overview

ByteTheTaxman helps taxpayers track deductible sales tax purchases by automatically matching financial transactions with emailed receipts.

The program reads a Quicken QIF export, searches Gmail (or local `.eml` files) for matching receipts, extracts sales tax amounts, and generates a new QIF file containing properly split transactions that can be imported back into Quicken.

The goal is to eliminate the tedious manual process of locating receipts, calculating sales tax, and entering split transactions one-by-one.

---

## Features

### Receipt Discovery

* Reads transactions from a Quicken QIF file.
* Searches Gmail through IMAP using vendor-specific matching rules.
* Supports offline processing of saved `.eml` email files.
* Matches receipts by:

  * Transaction amount
  * Transaction date
  * Vendor-specific search criteria

### Sales Tax Extraction

* Extracts sales tax from:

  * HTML email receipts
  * PDF invoice attachments
* Vendor-specific parsers improve accuracy.
* Only counts receipts that contain a valid sales tax amount.

### Receipt Archiving

For each matched receipt:

* Saves the original email as an `.eml` file.
* Generates a cleaned PDF version suitable for printing or record retention.
* Removes:

  * Product photos
  * Tracking images
  * Promotional content
  * Unnecessary email clutter

### Quicken Integration

Creates a new QIF file that:

* Changes transaction memo from:

```
Need receipt
```

to:

```
EmailMatch
```

* Splits transactions into:

```
Original Purchase Category
Tax:Sales Tax Paid
```

Example:

| Category           | Amount |
| ------------------ | ------ |
| Household          | $87.50 |
| Tax:Sales Tax Paid | $7.50  |

This allows sales tax to be tracked separately throughout the year.

### Extensible Vendor Framework

New merchants can be added without modifying the main program.

Each vendor module can provide:

* Receipt identification rules
* Email search terms
* Sales tax extraction logic
* Charged amount extraction
* PDF cleanup rules

Simply add a new:

```
vendors/vendor_<merchant>.py
```

file.

---

## Typical Workflow

1. Export transactions from Quicken as a QIF file.
2. Mark transactions needing receipts with the memo:

```
Need receipt
```

3. Run Byte The Taxman.
4. The program searches for matching receipts.
5. Matching receipts are archived.
6. Sales tax is extracted.
7. A new QIF file is generated with split transactions.
8. Import the generated QIF file back into Quicken.

---

## Example

Input transaction:

```
Date: 2025-05-15
Payee: Amazon
Amount: -107.50
Memo: Need receipt
Category: Household
```

Receipt found:

```
Subtotal: $99.95
Sales Tax: $7.55
Total: $107.50
```

Generated split transaction:

```
Household           -99.95
Tax:Sales Tax Paid   -7.55
```

---

## Requirements

### Python

* Python 3.9+

### Libraries

* beautifulsoup4
* reportlab
* pdfplumber
* lxml (recommended)

Install dependencies:

```bash
pip install beautifulsoup4 reportlab pdfplumber lxml
```

---

## Gmail Setup

Byte The Taxman accesses Gmail using IMAP.

Requirements:

1. Enable 2-Step Verification on your Google account.
2. Create a Gmail App Password.
3. Use the App Password when prompted by the application.

The program does **not** use OAuth and does **not** store credentials.

---

## Usage

### Gmail Search Mode

```bash
python find_missing_receipts.py transactions.qif
```

### Debug Mode

```bash
python find_missing_receipts.py transactions.qif --debug
```

### Local Email Archive Mode

```bash
python find_missing_receipts.py transactions.qif --eml receipts/*.eml
```

---

## Output

### Receipt Archive Folder

```
receipts/
```

Contains:

* Original `.eml` files
* Cleaned PDF copies
* PDF invoice attachments

### Generated QIF

```
matched_receipts.qif
```

Import this file into Quicken to update transactions with sales tax splits.

---

## Project Status

This project is actively evolving as additional vendors and receipt formats are added.

Current design emphasizes:

* Accuracy over speed
* Human-reviewable output
* Preservation of original receipt evidence
* Easy extension for additional merchants

---

## Disclaimer

This software is provided for recordkeeping and tax-documentation purposes only.

Users are responsible for verifying extracted sales tax amounts and ensuring compliance with all federal, state, and local tax regulations. Consult a qualified tax professional regarding the deductibility of sales taxes in your jurisdiction.
