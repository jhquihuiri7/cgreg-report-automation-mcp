# Modular Command Platform (MCP)

## Overview / Introduction

This project is a Modular Command Platform (MCP) that provides a set of tools to perform various tasks, from fetching weather data to generating monthly reports. The platform is designed to be extensible, allowing new tools to be added easily.

## Features

- **Modular Design:** Easily add new tools to the platform.
- **Asynchronous Operations:** Utilizes asyncio for non-blocking I/O operations.
- **Weather Information:** Fetch weather data from the NWS API.
- **File System Operations:** Duplicate folders and rename files.
- **Document Generation:** Generate monthly reports in DOCX format.
- **Text Extraction:** Extract text from PDF files.
- **AI Prompt Generation:** Create structured prompts for AI agents.

## Installation & Setup

### System requirements
- Python 3.10 or higher installed.
- You must use the Python MCP SDK 1.2.0 or higher.

### Set up your environment
First, let’s install uv and set up our Python project and environment:

**MacOS/Linux**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```
Make sure to restart your terminal afterwards to ensure that the `uv` command gets picked up.

Now, let’s create and set up our project:

**MacOS/Linux**
```bash
# Create a new directory for our project
uv init weather
cd weather

# Create virtual environment and activate it
uv venv
source .venv/bin/activate

# Install dependencies
uv add mcp[cli] httpx

# Create our server file
touch weather.py
```

**Windows**
```powershell
# Create a new directory for our project
uv init weather
cd weather

# Create virtual environment and activate it
uv venv
.venv\Scripts\activate

# Install dependencies
uv add mcp[cli] httpx

# Create our server file
new-item weather.py
```

## Usage Guide

To start the MCP server, run the following command in your terminal:

```bash
python main.py
```

The server will start, and you can interact with the tools through the MCP interface.

## MCP / Tools Documentation

### `get_weather_data`

Fetches weather data for a given location using the NWS API.

**Arguments:**

- `location` (str): The latitude and longitude for which to fetch weather data (e.g., "38.8894,-77.0352").

**Returns:**

- `Any`: The weather data in JSON format.

**Example:**

```python
import asyncio
from mcp.client import MCPClient

async def main():
    async with MCPClient("weather") as mcp:
        weather_data = await mcp.get_weather_data(location="38.8894,-77.0352")
        print(weather_data)

if __name__ == "__main__":
    asyncio.run(main())
```

### `duplicate_folder`

Duplicates a folder with a new folder name.

**Arguments:**

- `source_folder` (str): The path to the folder to duplicate.
- `new_folder` (str): The path for the new duplicated folder.

**Returns:**

- `Any`: A message indicating success or error.

**Example:**

```python
import asyncio
from mcp.client import MCPClient

async def main():
    async with MCPClient("weather") as mcp:
        result = await mcp.duplicate_folder(source_folder="my_folder", new_folder="my_folder_copy")
        print(result)

if __name__ == "__main__":
    asyncio.run(main())
```

### `change_month_in_name`

Renames all files in a folder by replacing the month in their names with the current month (uppercase, Spanish).

**Arguments:**

- `folder_path` (str): The path to the folder containing the files.

**Returns:**

- `Any`: A list of messages indicating success or error for each file.

**Example:**

```python
import asyncio
from mcp.client import MCPClient

async def main():
    async with MCPClient("weather") as mcp:
        result = await mcp.change_month_in_name(folder_path="my_reports")
        print(result)

if __name__ == "__main__":
    asyncio.run(main())
```

### `generate_monthly_report`

Abre un archivo DOC o DOCX, reemplaza los marcadores con texto o tabla.

**Arguments:**

- `report_filepath` (str): Ruta del archivo .doc o .docx.
- `month` (str): Mes a insertar.
- `activities` (str): Texto de actividades.
- `conclusiones` (str): Texto de conclusiones.
- `recommendations` (str): Texto de recomendaciones.
- `title_activities` (list): Lista de dicts con "actividad" y "mes".
- `description_activities` (list): Lista de dicts con "actividad", "descripcion" y "verifcador".

**Returns:**

- `Any`: Mensaje de éxito o error.

**Example:**

```python
import asyncio
from mcp.client import MCPClient

async def main():
    async with MCPClient("weather") as mcp:
        title_activities = [
            {"actividad": "Entrega de informe técnico a la Subsecretaría", "mes": "Marzo"},
            {"actividad": "Capacitación sobre Registro Social", "mes": "Marzo"}
        ]
        description_activities = [
            {"actividad": "Entrega de informe técnico a la Subsecretaría", "descripcion":"Se convoca a la Fundación Naveducando a una reunión...", "verificador": "Oficio Nro. CGREG-DDFPES-2025-0014-OF"},
            {"actividad": "Capacitación sobre Registro Social", "descripcion": "Se convoca a la Comisión Técnica y a ESPOLTECH-EP...", "verificador": "Memorando Nro. CGREG-DDFPES-2025-0319-M"}
        ]
        result = await mcp.generate_monthly_report(
            report_filepath="report_template.docx",
            month="Marzo",
            activities="...",
            conclusiones="...",
            recommendations="...",
            title_activities=title_activities,
            description_activities=description_activities
        )
        print(result)

if __name__ == "__main__":
    asyncio.run(main())
```

### `extract_text_from_pdfs`

Extracts all text from PDF files in the given folder using pdf-plumber.

**Arguments:**

- `folder_path` (str): The path to the folder containing PDF files.

**Returns:**

- `Any`: A dictionary with filenames as keys and extracted text as values, or an error message.

**Example:**

```python
import asyncio
from mcp.client import MCPClient

async def main():
    async with MCPClient("weather") as mcp:
        extracted_text = await mcp.extract_text_from_pdfs(folder_path="pdf_documents")
        print(extracted_text)

if __name__ == "__main__":
    asyncio.run(main())
```

### `generate_monthly_report_prompt`

Genera un prompt estructurado para que un agente de IA lea documentos PDF desde una carpeta y genere un informe mensual con base en dichos documentos.

**Arguments:**

- `report_path` (str): Ruta del archivo donde se generará el informe mensual.
- `folder_path` (str): Carpeta donde se encuentran los archivos PDF a resumir.

**Returns:**

- `str`: Un prompt con instrucciones detalladas para el agente.

**Example:**

```python
from mcp.client import MCPClient

def main():
    mcp = MCPClient("weather")
    prompt = mcp.generate_monthly_report_prompt(report_path="monthly_report.docx", folder_path="pdf_documents")
    print(prompt)

if __name__ == "__main__":
    main()
```

## Examples

Here's an example of how you might use multiple tools together to generate a monthly report:

1. **Extract text from PDFs:** Use `extract_text_from_pdfs` to get the content of your monthly activity documents.
2. **Generate a prompt for an AI:** Use `generate_monthly_report_prompt` to create a structured prompt for an AI to summarize the extracted text and generate the report content.
3. **Generate the report:** Use the output from the AI to call `generate_monthly_report` and create the final DOCX report.

## Contribution Guidelines

We welcome contributions to the Modular Command Platform! Please follow these steps to contribute:

1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Make your changes and commit them with a clear message.
4. Push your changes to your fork.
5. Create a pull request to the main repository.

## License

This project is licensed under the MIT License. See the `LICENSE` file for more details.
