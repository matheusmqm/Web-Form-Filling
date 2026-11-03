# Web Form Filling

An RPA bot in Python that reads responses from an Excel spreadsheet and submits them, one by one, to a Google Forms employee satisfaction survey. Built with [BotCity Web](https://documentation.botcity.dev/tutorials/python-automations/web/) (Selenium) and integrable with BotCity Maestro.

## How It Works

1. Reads the `Satisfaction.xlsx` spreadsheet with pandas.
2. Opens the form in Chrome.
3. For each row in the spreadsheet, fills in the fields, submits the response, and clicks "Submit another response".
4. Closes the browser and reports the result to Maestro (when connected).

### Form Fields

| Spreadsheet Column    | Form Field            | Type                |
|-----------------------|-----------------------|---------------------|
| `Employee Name`       | Employee name         | Text                |
| `Years of Service`    | Years of Service      | Text                |
| `Department`          | Department            | Dropdown            |
| `Satisfaction Rating` | Employee satisfaction | Scale (options)     |

## Requirements

- Python 3.8+
- Google Chrome and a [ChromeDriver](https://googlechromelabs.github.io/chrome-for-testing/) compatible with your version
- Windows (the project's default paths are Windows paths)

## Installation

```bash
git clone <repository-url>
cd Web-Form-Filling
python -m venv .venv
.venv\Scripts\activate
pip install --upgrade -r requirements.txt
```

## Configuration

The values below are hardcoded in [bot.py](bot.py) and should be adjusted to your environment:

| Item                  | Default                                           | Where to change               |
|-----------------------|---------------------------------------------------|-------------------------------|
| ChromeDriver path     | `C:\chromedriver-win64\chromedriver.exe`          | `bot.driver_path`             |
| Input spreadsheet     | `C:\Projects\Satisfaction.xlsx`                   | `pd.read_excel(...)`          |
| Form URL              | Sample Google Form                                | `bot.browse(...)`             |
| Headless mode         | `False` (visible browser)                         | `bot.headless`                |

### Spreadsheet Format

The spreadsheet must have exactly these columns:

| Employee Name | Years of Service | Department | Satisfaction Rating |
|---------------|------------------|------------|---------------------|
| Maria Silva   | 5                | Sales      | 4                   |

The `Department` and `Satisfaction Rating` values must exactly match the options available in the form.

> **Note:** the selectors and button texts ("Submit", "Submit another response") assume a form with a **Portuguese** interface. If your form is in another language, adjust the XPaths in [bot.py](bot.py).

## Usage

```bash
python bot.py
```

When run locally, the bot works without Maestro: `BotMaestroSDK.RAISE_NOT_CONNECTED = False` prevents errors when there is no connection.

## Packaging for BotCity Maestro

Generates `WebFormFilling.zip`, ready to upload to Maestro:

```bash
# Windows (PowerShell)
./build.ps1

# Windows (cmd)
build.bat

# Linux / macOS
./build.sh
```

## Project Structure

```
.
├── bot.py                 # Main bot logic
├── requirements.txt       # Dependencies
├── WebFormFilling.botproj # BotCity project metadata
├── build.bat | .ps1 | .sh # Packaging scripts
└── resources/             # Bot resources (images)
```

## Technologies

- [BotCity Web Framework](https://documentation.botcity.dev/)
- [BotCity Maestro SDK](https://documentation.botcity.dev/maestro/)
- [pandas](https://pandas.pydata.org/) and [openpyxl](https://openpyxl.readthedocs.io/)

## Troubleshooting

- **`ModuleNotFoundError: No module named 'botcity'`**: the interpreter in use is not the one where the dependencies were installed. Reinstall with `pip install --upgrade -r requirements.txt` in the correct interpreter.
- **Element not found**: the Google Forms layout changed or the language is different; review the XPaths.
- **Driver version error**: the ChromeDriver version must be compatible with the installed Chrome version.
