# Regression Testing Roport Tool
## Module lists
- diff_parser
    - According to the git link output the relative code changing   
- ast_analyzer
    - Deal with raw data which is the output of diff_parser, make it readable. 
- test_linker
    - Find the relative testing api
- llm_engine
    - use llm to give the user suggestion
- reporter
    - output a human frendily report

## GitDiffParser
 `GitDiffParser` is a Python tool for analyzing file-level differences between two commits in a GitHub repository. It automatically clones the repository, compares file versions between the specified commits, and outputs diffs for each changed file.

---

### 🚀 Features

- Clone any public GitHub repository (temporary or persistent).
- Show which files changed between two commits.
- View the diff content of each changed file.
- Load full file content from both the current and previous commits.

---

### 🧰 Requirements

- Python 3.6+
- Git must be installed and available in your system's PATH

---

### 📦 Installation

No installation needed. Just clone this repository and run the script directly:
### API
* init: clone the target repo
* get_changed_files: list the files which have been changed
* load_file: return the source code of the target commit
* load_file_from_previous_commit: return the source code of the base commit
* get_diff: return the git diff result between target and base commit
#### Easy way to call api
1. Inital the class with correct parameters
2. use get_changed_files to get the name list of modified files
3. load_file and load_file_from_previous_commit to load the specific function in the output of get_changed_files
4. use the both output of load_file and load_file_from_previous_commit as an input of ast_analyzer to analyze different
### Usage
```bash
python ./RegressionTest/diff_parser.py <repo_url> [--from COMMIT] [--to COMMIT] [--keep] 
```

#### Options

- `--from`: Base commit (default: `HEAD^`)
- `--to`: Target commit (default: `HEAD`)
- `--keep`: Keep the cloned repo (default: repo is deleted after diff)

#### Examples

```bash
python ./RegressionTest/diff_parser.py https://github.com/CoverIQ/CoverIQ-Test-Assistant

python ./RegressionTest/diff_parser.py https://github.com/CoverIQ/CoverIQ-Test-Assistant --from abc123 --to def456

python ./RegressionTest/diff_parser.py https://github.com/CoverIQ/CoverIQ-Test-Assistant --keep
```

## 🧠 AST Analyzer

This module performs static analysis on Python source code using the Abstract Syntax Tree (AST). It is primarily used to detect function-level changes across code versions and to build function-level call graphs for dependency tracking.

---

### ✨ Features

- **Function Extraction**: Extracts all top-level functions and their full source bodies.
- **Call Graph Construction**: Builds a call graph showing which functions call which.
- **Change Detection**: Compares two versions of a file and identifies added, removed, and modified functions.
- **Indirect Dependency Detection**: Finds functions that call modified ones (1-hop).

---

### 📚 Functions

#### `extract_functions_with_body(code: str) -> Dict[str, str]`

Parses the code and extracts each function's name and body.

#### `build_call_graph(code: str) -> Dict[str, Set[str]]`

Creates a dictionary mapping function names to the set of functions they call.

#### `find_callers(target_funcs: List[str], call_graph: Dict[str, Set[str]]) -> Set[str]`

Returns all functions that call any of the target functions (non-recursive).

#### `analyze_ast_diff(before_code: str, after_code: str) -> Dict[str, List[str]]`

Compares two versions of Python code and returns a dictionary with:
- `added`: Newly introduced functions
- `removed`: Deleted functions
- `modified`: Changed functions
- `indirect_dependents`: Functions that call modified ones

### 🔧 Example Usage

```python
from ast_analyzer import analyze_ast_diff

with open("before.py") as f1, open("after.py") as f2:
    before_code = f1.read()
    after_code = f2.read()

diff = analyze_ast_diff(before_code, after_code)
print(diff)