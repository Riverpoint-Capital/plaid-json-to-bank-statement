# Plaid Asset Report to Bank Statement Converter

A simple HTML-based tool that converts Plaid Asset Report JSON data into traditional bank statement format, helping operations teams transition from manual bank statement review to automated Plaid data processing.

## Purpose

This tool serves as a **short-term bridge solution** for loan application scrubbing teams who are transitioning to accept financial data via Plaid. Since teams are accustomed to reviewing traditional bank statements, this converter restructures Plaid Asset Report data into familiar bank statement formats.

## How to Run Locally

### Quick Start

1. **Clone or download this repository** to your local machine

2. **Open the HTML file** in any modern web browser:
   - Double-click `json_Statement_Generator_v1.html`, or
   - Right-click and select "Open with" → Your preferred browser, or
   - Drag the HTML file into a browser window

3. **That's it!** No installation or build process required.

### Requirements

- Any modern web browser (Chrome, Firefox, Safari, Edge)
- No server or dependencies needed - everything runs client-side
- Internet connection required (for loading CDN libraries: jsPDF and html2canvas)

## How to Use

1. **Load your Plaid Asset Report JSON**:
   - Drag and drop the JSON file onto the upload area, or
   - Click the upload area to select a file

2. **View statements by month**:
   - The tool automatically organizes data into monthly tabs
   - Shows up to 4 complete months + current Month-to-Date (MTD) if available
   - Click tabs to switch between months

3. **Download PDF statements**:
   - Click "Download [Month] Statement (PDF)" button for any month
   - Generates a formatted PDF matching traditional bank statements

## How It Works

### Core Functionality

#### 1. Input Processing
- Accepts Plaid Asset Report JSON via drag-and-drop or file selection
- Validates JSON structure expecting `{report: {items: [...]}}`
- Extracts financial data from the hierarchical Plaid structure

#### 2. Data Organization
- **Date Range Analysis**: Scans all transactions and historical balances to find actual date range
- **Month Segmentation**: 
  - Identifies complete months vs Month-to-Date (MTD)
  - Displays last 4 complete months + current MTD if available
  - Handles months based on actual data presence

#### 3. Statement Generation
Each account gets formatted as a traditional bank statement with:
- **Header Section**: Institution name, account details, statement period
- **Account Summary**: Beginning/ending balances from `historical_balances`
- **Deposits Section**: Transactions with negative amounts (Plaid convention)
- **Withdrawals Section**: Transactions with positive amounts
- **Daily Balance Summary**: From `historical_balances` array

#### 4. Key Data Mappings

| Plaid Field | Bank Statement Display |
|------------|----------------------|
| `report.items[].institution_name` | Bank name |
| `accounts[].mask` | Account number (last 4) |
| `accounts[].subtype/type` | Account type |
| `transactions.amount < 0` | Deposits (credits) |
| `transactions.amount > 0` | Withdrawals (debits) |
| `historical_balances` | Daily ending balances |

#### 5. PDF Export
- Uses html2canvas + jsPDF libraries (loaded from CDN)
- Generates multi-page PDFs for each month
- Maintains traditional bank statement formatting

### Important Design Choices

- **No Calculations**: Displays data exactly as provided by Plaid
- **String-based Date Handling**: Uses direct string comparison to avoid timezone issues
- **MTD Detection**: Automatically identifies if newest month is current month for MTD labeling
- **Tab-based UI**: Each month gets its own tab for easy navigation

## Expected Plaid Asset Report Structure

The tool expects standard Plaid Asset Report format:

```json
{
  "report": {
    "asset_report_id": "...",
    "date_generated": "...",
    "items": [
      {
        "institution_name": "Bank Name",
        "accounts": [
          {
            "account_id": "...",
            "name": "Account Name",
            "mask": "1234",
            "type": "depository",
            "subtype": "checking",
            "balances": {
              "current": 1000.00,
              "available": 900.00
            },
            "transactions": [
              {
                "date": "2025-08-01",
                "amount": 50.00,
                "name": "Transaction description"
              }
            ],
            "historical_balances": [
              {
                "date": "2025-08-01",
                "current": 1000.00
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## Features

- ✅ Drag-and-drop file upload
- ✅ Automatic month detection and organization
- ✅ Month-to-Date (MTD) identification
- ✅ Multiple account support
- ✅ PDF export for each month
- ✅ Traditional bank statement formatting
- ✅ No server required - runs entirely in browser
- ✅ Preserves exact Plaid data (no calculations)

## Limitations

- Displays data exactly as provided by Plaid (no balance recalculations)
- Shows maximum of 4 complete months + 1 MTD
- Requires internet connection for CDN libraries
- PDF generation may take a moment for large datasets

## Sample Files

The repository includes three test JSON files for demonstration:
- `test1 plaid_asset_report_2025-08-22.json`
- `test2 plaid_asset_report_2025-08-22.json`
- `test3 plaid_asset_report_2025-08-25.json`

## Security Note

All processing happens locally in your browser. No data is sent to external servers except for loading the JavaScript libraries from CDN.

## Support

This is a short-term operational tool. For questions about:
- **Plaid Asset Reports**: See [Plaid Assets Documentation](https://plaid.com/docs/assets/)
- **Tool usage**: Contact your operations team lead

## License

Internal use only - Operations Team Tool