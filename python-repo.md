# Python project setup

## Virtual Env
1. Install `virtualenv` if it's not installed:
    ```sh
    pip install virtualenv
    ```

2. Create venv directory:
    ```sh
    virtualenv --upgrade-embed-wheels # makes it easier to manage python versions
    virtualenv --python 3.8 venv
    ```

3. Activate the virtual environment:
    ```sh
    source venv/bin/activate # venv\Scripts\activate on Windows
    ```

The next sections assume to be invoked within this activated environment.

## Linter
1. Install `flake8`:
    ```sh
    pip install flake8
    ```

2. Create its config `.flake8` with this base structure:
    ```ini
    [flake8]
    ignore =
        # Some template strings can easily go over the limit
        E501
    exclude =
        .git,
        __pycache__,
        venv
    max-complexity = 14
    ```

3. Update dependency list:
    ```sh
    pip freeze > requirements.txt
    ```

## Formatter
1. Install `autopep8`:
    ```sh
    pip install --upgrade autopep8
    ```

2. Add its config to `.flake8` file:
    ```ini
    [pycodestyle]
    max_line_length = 120
    ignore =
        E501
    ```

3. Update dependency list:
    ```sh
    pip freeze > requirements.txt
    ```

## Pre-commit hooks
1. Install `pre-commit`:
    ```sh
    pip install pre-commit
    ```

2. Create its config `.pre-commit-config.yaml` and fill it with this preset:
    ```yml
    repos:
    -   repo: https://github.com/pre-commit/pre-commit-hooks
        rev: 38b88246ccc552bffaaf54259d064beeee434539  # frozen: v4.0.1
        hooks:
        -   id: trailing-whitespace
        -   id: end-of-file-fixer
        -   id: check-yaml
        -   id: check-added-large-files
    -   repo: https://github.com/pycqa/flake8
        rev: 'cbeb4c9c4137cff1568659fcc48e8b85cddd0c8d'  # frozen: 4.0.1
        hooks:
        -   id: flake8
    -   repo: https://github.com/pre-commit/mirrors-autopep8
        rev: '7d14f78422aef2153a90e33373d2515bcc99038d'  # frozen: v1.5.7
        hooks:
        -   id: autopep8
    ```

3. Update hooks in the config:
    ```sh
    pre-commit autoupdate --freeze
    ```

4. Install hook scripts and activate them:
    ```sh
    pre-commit install --install-hooks --overwrite
    ```

5. Update dependency list:
    ```sh
    pip freeze > requirements.txt
    ```

Now the repo is ready to accept commits and will fail them upon rule violations.

## IDE-specific setup
### VSCode
1. Create `.vscode` folder amd populate it with these files:
    - `settings.json`
    - `extensions.json`
    - `launch.json`

2. Content of `settings.json`:
    ```json
    {
      "editor.wordWrap": "bounded",
      "editor.wordWrapColumn": 120,
      "editor.rulers": [
          {
              "column": 65,
              "color": "orange"
          },
          {
              "column": 120,
              "color": "red"
          },
      ],
      "[json]": {
        "editor.tabSize": 2,
      },
      "[jsonc]": {
        "editor.tabSize": 2,
      },
      "[python]": {
        "editor.tabSize": 4,
      },
      "python.linting.enabled": true,
      "python.linting.flake8Enabled": true,
      "python.linting.flake8Args": [
          "--verbose"
      ],
      "python.formatting.autopep8Args": [],
    }
    ```

3. Content of `extensions.json`:
    ```json
    {
      "recommendations": [
        "ms-python.python",
        "ms-python.vscode-pylance",
        "ms-toolsai.jupyter",
        "rioj7.command-variable"
      ]
    }
    ```

4. Install recommended extensions.

5. Content of `launch.json`:
    ```json
    {
      // Use IntelliSense to learn about possible attributes.
      // Hover to view descriptions of existing attributes.
      // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
      "version": "0.2.0",
      "configurations": [
        {
          "name": "Python: Module",
          "type": "python",
          "request": "launch",
          "module": "${command:extension.commandvariable.file.relativeDirDots}.${fileBasenameNoExtension}"
        },
        {
          "name": "Python: Current File",
          "type": "python",
          "request": "launch",
          "program": "${file}",
          "console": "integratedTerminal",
          "env": {
              "PYTHONPATH": "${workspaceFolder}"
          },
        }
      ]
    }
    ```

6. Create `setup.py` with this content:
    ```python
    from setuptools import setup, find_packages

    setup(
        name='Python repo',
        packages=find_packages(),
    )

    ```

7. Install this package within virtual environment:
    ```sh
    py -m pip install -e .
    ```
