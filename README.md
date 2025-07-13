# Pip licenses usage in CI

This project shows how to intergrate [pip-licenses](https://github.com/raimon49/pip-licenses) in your CI pipeline to check the licenses of your Python dependencies.

## Usage

The command to check for problematic licenses is:

```bash
pip-licenses --fail-on="GPL;AGPL" --partial-match
```

Make sure to adjust the `--fail-on` option to include any licenses you want to flag as problematic.

If pip-licenses detects any packages with the specified licenses, it will exit with a non-zero status, which can be used to fail your CI job.

Here is an example of how you might use this in a CI pipeline configuration file (e.g., GitHub Actions):

```yaml
name: CI pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  check-licenses:
    name: Check licenses
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: .
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Check for problematic licenses
        run: |
          pip-licenses --fail-on="GPL;AGPL;LGPL;CDDL;EPL;CPAL;EUPL;OSL;SSPL;RPL;APS" --partial-match
          if [ $? -ne 0 ]; then
            echo "Problematic licenses found. Please review the licenses."
            pip-licenses --format=markdown
            exit 1
          fi
```

This configuration will run the license check on every push and pull request to the `main` branch. If any packages with the specified licenses are found, the job will fail, and a message will be printed to review the licenses.

## Acknowledgements

This project uses [pip-licenses](https://github.com/raimon49/pip-licenses) to check the licenses of Python dependencies.
