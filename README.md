## Context

This project was part of a university capstone with Intel, focused on measuring and analyzing the environmental impact of CI/CD workflows. 

The system uses [Eco-CI](https://github.com/green-coding-solutions/eco-ci-energy-estimation) to collect energy data from GitHub Actions, stores it in a database for analysis and visualization. This implementation used a forked version of Eco-CI from the capstone project.

## My Contribution

I wrote `integrate_ecoci.py`, a Python script that automates Eco-CI integration across GitHub Actions workflows.

It scans `.github/workflows/`, checks whether Eco-CI is already present, and inserts the required measurement steps into each job while preserving YAML formatting with `ruamel.yaml`.

Implementation (team repository): https://github.com/DaxLynch/ecocli-utility

## Script Output

Example output when running the integration script:

![Script output](./assets/ecoci-integrated.png)

The script inserts Eco-CI steps where missing and skips jobs where they are already present.

## Example Transformation

### Before:
```
name: CI

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest
```

### After running `integrate_ecoci.py`:
```
name: CI

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Start Energy Measurement
        uses: DaxLynch/eco-ci-energy-estimation@main
        with:
          task: start-measurement
          api-endpoint-add: ${{ secrets.ECOCI_API }}

      - uses: actions/checkout@v4

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest

      - name: Test Energy Measurement
        uses: DaxLynch/eco-ci-energy-estimation@main
        with:
          task: get-measurement
          label: 'test'

      - name: Show Energy Results
        uses: DaxLynch/eco-ci-energy-estimation@main
        with:
          task: display-results
```
