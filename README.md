# dir-diary

## Overview

`dir-diary` is a Python library and CLI tool that generates a concise, natural language pseudocode summary of your entire project folder. This summary can serve as an intermediate step in development, documentation writing, or even for embedding your repo for search-and-retrieval or Q&A. The tool leverages GPT-3.5 Turbo to summarize a decent-sized project for just 2-4 cents. Future plans include a Github Action for seamless integration into automated cloud deployment workflows.

## Installation

If you install `dir-diary` via `pip`, the CLI tool should be automatically added to your system PATH. If you encounter issues or have installed it manually, you may need to add it to your PATH manually.

### PyPi Installation

```bash
pip install dir-diary
```

### Clone from GitHub

```bash
git clone https://github.com/Promptly-Technologies-LLC/dir-diary.git
cd dir-diary
pip install .
```

## Usage

### Setting an OpenAI API Key

Setting environment variables for local command-line usage can be done in several ways, depending on your operating system and shell. Here are some methods:

#### 1. Exporting in Shell

You can set an environment variable in your shell session. This will set the variable for the duration of the shell session, making it available to `dir-diary` in the same session. The exact command differs depending on which shell you use.

In Bash, use the `export` command:

```bash
export OPENAI_API_KEY=your_api_key_here
```

In Windows Command Prompt, you can use the `set` command:

```cmd
set OPENAI_API_KEY=your_api_key_here
```

In Windows PowerShell, you can use the `$env:` prefix:

```powershell
$env:OPENAI_API_KEY="your_api_key_here"
```

#### Using an `.env` File

You can also place your environment variables in a `.env` file in the same directory as the repo to be summarized, with the following text:

```
OPENAI_API_KEY=your_api_key_here
```

Make sure to replace `your_api_key_here` with your actual API key. 

When you run your script, `load_dotenv()` will load these variables into the environment.

#### Making Environment Variables Permanent

If you find yourself needing to set this environment variable frequently, you may want to add the export command to your shell profile script (e.g., `.bashrc`, `.zshrc` for Unix-like systems) or set it as a system-wide environment variable through system settings.

#### Using an option flag

You can also pass your API key as an option flag to the CLI tool (`--api_key="your_openai_api_key"`) or Python API (`api_key="your_openai_api_key"`). For security reasons, this is not recommended, as it will expose your API key in your shell history or command-line history.

### Command-Line Interface

To summarize a project folder, use the `summarize` command in your shell from the folder you want to summarize. The CLI tool will automatically map the project folder, classify files by their role in the project, and generate a pseudocode summary of all project files with the roles you specify for inclusion in the summary. By default, the tool will create a `docs` folder if one does not already exist, and then will create `project_map.json` and `pseudocode.md` files in that folder. The default paths for these files can be adjusted using the `--pseudocode_file` and `--project_map_file` options.

The `project_map.json` file contains a structured representation of the project folder file structure, and the `pseudocode.md` file contains the pseudocode summary of the project as generated by the specified OpenAI model. By default, `dir-diary` uses the GPT-3.5 Turbo model with 8k context length and will fall back to GPT-3.5 Turbo 16k if necessary. The tool currently only supports OpenAI's chat models.

Note that the syntax for setting option flags differs slightly depending on your shell. Bash uses an equal sign, while Windows Powershell uses a space, as in the examples below.

#### Bash/Zsh

```bash
summarize --startpath="./my_project" --api_key="your_openai_api_key"
```

#### PowerShell

```powershell
summarize --startpath ".\my_project" --api_key "your_openai_api_key"
```

#### Option Flags

Option Flags
- `--startpath`: Specifies the path to the project folder you want to summarize. Defaults to the current directory.
- `--pseudocode_file`: Sets the output path for the generated pseudocode summary. Defaults to docs/pseudocode.md.
- `--project_map_file`: Sets the output path for the project map file. Defaults to docs/project_map.json.
- `--include`: Specifies the types of files to include in the summary based on their roles. Accepts multiple values as a comma-separated list enclosed in square brackets. Valid options are: "source", "configuration", "build or deployment", "documentation", "testing", "database", "utility scripts", "assets or data", and "specialized". Defaults to ["source", "utility scripts"].
- `--api_key`: Your OpenAI API key. An API key is required for the tool to function, but the option flag is not required if you've set the API key as an environment variable.
- `--model_name`: Specifies the OpenAI model to use for generating the summary. Defaults to "gpt-3.5-turbo". Valid options are: "gpt-3.5-turbo", "gpt-3.5-turbo-0301", "gpt-3.5-turbo-0613", "gpt-3.5-turbo-16k", "gpt-3.5-turbo-16k-0613", "gpt-4", "gpt-4-0314", "gpt-4-0613".
- `--long_context_fallback`: Specifies the fallback OpenAI model to use when the context is too long for the primary model. Defaults to "gpt-3.5-turbo-16k". Valid options are: "gpt-3.5-turbo-16k", "gpt-3.5-turbo-16k-0613".
- `--temperature`: Sets the "temperature" for the OpenAI model, affecting the randomness of the output. Defaults to 0.

### Python API

Inside a Python script, you can summarize your project folder with the `summarize_project_folder` function, which takes the same arguments as the CLI tool. 

#### Example Usage

To use the tool from a Python script, import and invoke as follows:

```python
from dir-diary import summarize_project_folder

summarize_project_folder(
    startpath="./my_project",
    pseudocode_file="./my_project/docs/pseudocode.md",
    project_map_file="./my_project/docs/project_map.json",
    include=["source", "documentation"],
    api_key="your_api_key_here",
    model_name="gpt-4",
    long_context_fallback="gpt-4-0314",
    temperature=0.7
```

This will generate a pseudocode summary of the "my_project" folder, save it to "./my_project/docs/pseudocode.md", and also create a project map in "./my_project/docs/project_map.json".

#### Parameters

- **`startpath` (Union[str, PathLike], default: ".")**:
  The starting path of the project folder you want to summarize. You can provide this as either a string or a `PathLike` object. The default is the current directory (".").
- **`pseudocode_file` (Union[str, PathLike], default: "docs/pseudocode.md")**:
  The path where the generated pseudocode summary will be saved. The default is "docs/pseudocode.md".
- **`project_map_file` (Union[str, PathLike], default: "docs/project_map.json")**:
  The path where the project map file will be saved. The default is "docs/project_map.json".
- **`include` (list[Literal], default: ["source", "utility scripts"])**:
  A list specifying which types of files to include in the summary. Valid options are: "source", "configuration", "build or deployment", "documentation", "testing", "database", "utility scripts", "assets or data", and "specialized". The default is to include "source" and "utility scripts".
- **`api_key` (str, default: None)**:
  The API key for the language model. If not provided, the function will use the default API key if available.
- **`model_name` (Literal, default: "gpt-3.5-turbo")**:
  The name of the language model to use for generating the summary. Valid options are: "gpt-3.5-turbo", "gpt-3.5-turbo-0301", "gpt-3.5-turbo-0613", "gpt-3.5-turbo-16k", "gpt-3.5-turbo-16k-0613", "gpt-4", "gpt-4-0314", "gpt-4-0613".
- **`long_context_fallback` (Literal, default: "gpt-3.5-turbo-16k")**:
  The name of the fallback language model to use when the primary model's token limit is exceeded. Valid options are the same as for `model_name`. Valid options are: "gpt-3.5-turbo-16k", "gpt-3.5-turbo-16k-0613".
- **`temperature` (float, default: 0)**:
  The temperature setting for the language model, affecting the randomness of the output. A value of 0 makes the output deterministic, while higher values (up to 1) make it more random.

#### Tools for Working with the Pseudocode Summary File

The Python API also exposes the `read_pseudocode_file` function and the `ModulePseudocode` class, which can be used to read and parse the pseudocode summary file. The `ModulePseudocode` class is a pydantic model that can be used to validate the JSON file and access its contents. It has the attributes `path`, `modified`, and `content`, which correspond to the path of the module, the last modified timestamp of the module, and the pseudocode summary of the module, respectively. The `read_pseudocode_file` function returns a list of `ModulePseudocode` objects, one for each module in the project folder.

```python
from dir-diary import read_pseudocode_file, ModulePseudocode

pseudocode: list[ModulePseudocode] = read_pseudocode_file("./docs/pseudocode.md")
```

## Contributing

We welcome contributions! Feel free to submit pull requests for new features, improvements, or bug fixes. Please make sure to follow best practices and include unit tests using the pytest framework.

For any issues, please [create an issue on GitHub](https://github.com/Promptly-Technologies-LLC/dir-diary/issues).

## Modules and Key Functions

- `summarize.py`: Orchestrates the summarization of the entire project folder.
- `cli.py`: Defines the command-line interface for the tool.
- `mapper.py`: Maps the project folder.
- `chatbot.py`: Initializes and queries the chatbot model.
- `classifier.py`: Classifies files' roles by querying the chatbot.
- `summarizer.py`: Summarizes individual files by querying the chatbot.
- `file_handler.py`: Handles reading and writing of pseudocode files.
  
For a more detailed understanding, please refer to the source code, inline comments, and, most importantly, [docs\pseudocode.md](docs\pseudocode.md)!

## To-do

- [ ] Add project-level tech stack summarization
- [ ] Add module-level generation of usage instructions
- [ ] Rename to project-summarizer?
- [ ] Add support for LLMs other than OpenAI's
