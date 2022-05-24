# Sec3 Pro security auditing

This Github action conducts security auditing for Solana smart contracts using the Sec3 Premium (formerly Soteria) tool.

Note: The action will send your source code to Sec3's server for analysis. By using this action, you certify that you agree to the [Terms of Use](https://sec3.dev/terms) and the [Privacy Policies](https://sec3.dev/privacy-policy) of Sec3.

## Input

### `Sec3 Secret Token`

**Required.** The token provided by Sec3 to users with Build/Scale/Enterprise Plans.

The token can be found on the dashboard under the "Account" tab.

After acquiring the token, navigate to your repository, click Settings -> Secrets -> Actions -> New Repository Secret, Name the token as `SEC3_TOKEN` in the `Name` field, paste the token in the `Value` field and click `Add secret`. The token is now accessible in the workflow as `${{ secrets.SEC3_TOKEN }}`

Warning: **DO NOT** explicitly include your token in the workflow.

### `path`

**Optional.** The path to the program to be tested.

If omitted, the test will run against all the programs in the repository.

## Output

The output of the action is a file in the format of [Static Analysis Results Interchange Format (SARIF) Version 2.1.0](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html). It can be accessed by:

- A download link will be provided in the action log.

- A file named `sec3-report.sarif` will be generated in the workspace.

## Running the Action in GitHub CI
You can use this Action as part of your project by creating an Action as follows:
```
name: Sec3 Pro Audit
     # update to match your branch names and requirements
on:
  push:
    branches: main
  pull_request:
    branches: "*"
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - name: Check-out the repository
        uses: actions/checkout@v2
      - name: Sec3 Pro Audit
        continue-on-error: false    # set to true if you don't want to fail jobs
        uses: sec3dev/pro-action@v1
        with:
          sec3-token: ${{ secrets.SEC3_TOKEN }}
          path: programs/your_program
```
## Code scanning alerts integration
To integration with [Code scanning alerts in Github](https://docs.github.com/en/enterprise-server@3.4/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors/setting-up-code-scanning-for-a-repository), create an Action as follows:
```
name: Sec3 Pro Audit
     # update to match your branch names and requirements
on:
  push:
    branches: main
  pull_request:
    branches: "*"
jobs:
  audit:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - name: Check-out the repository
        uses: actions/checkout@v2
      - name: Sec3 Pro Audit
        continue-on-error: true    # set to true if you don't want to fail jobs
        uses: sec3dev/pro-action@v1
        with:
          sec3-token: ${{ secrets.SEC3_TOKEN }}
          path: programs/your_program
      - name: Upload Sarif Report
        uses: github/codeql-action/upload-sarif@v1
        with:
          sarif_file: sec3-report.sarif
```
## Managing false positives
The tool may identify potential issues that you accept as they are to e.g. save compute cycles, or genuine false positives. Ignores can be configured by adding the below annotation to the line above the line you are wanting to ignore:
```
//#[soteria(ignore)]
```

### Finer-grained annotations
Ignore missing signer check only:
```
//#[soteria(ignore_signer)]
```
Ignore missing unsafe transfer destination check only:
```
//#[soteria(ignore_destination)]
```
These annotations can also be combined:
```
//#[soteria(ignore_signer,ignore_destination)]
```
Or
```
//#[soteria(ignore_signer)]
//#[soteria(ignore_destination)]
```
