# Soteria Pro security auditing

This Github action conducts security auditing on Solana smart contracts using the Soteria Premium tool.

Note: The action will send your source code to Soteria's server for analysis. By using this action, you certify that you agree to the [Terms of Use]() and the [Privacy Policies]() of Soteria.

## Input

### `soteria-token`

**Required.** The token provided by Soteria.

For those who have previously used the Soteria web app, the token is the same as the invitation code.

If you wish to get a token to use Soteria Pro, please email [contact@soteria.dev](contact@soteria.dev).

After acquiring the token, navigate to your repository, click Settings -> Secrets -> Actions -> New Repository Secret, Name the token as `SOTERIA_TOKEN` in the `Name` field, paste the token in the `Value` field and click `Add secret`. The token is now accessible in the workflow as `${{ secrets.SOTERIA_TOKEN }}`

Warning: **DO NOT** explicitly include your token in the workflow.

## Output

The output of the action is a file in the format of [Static Analysis Results Interchange Format (SARIF) Version 2.1.0](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html). It can be accessed by:

- A download link will be provided in the action log.

- A file named `soteria-report.sarif` will be generated in the workspace.

## Running the Action in GitHub CI
You can use this Action as part of your project by creating an Action as follows:
```
name: Soteria Pro Audit
     # update to match your branch names and requirements
on:
  push:
    branches: main
  pull_request:
    branches: main
jobs:
  audit:
    runs-on: ubuntu-20.04
    steps:
      - name: Check-out the repository
        uses: actions/checkout@v2
      - name: Soteria Pro Audit
        continue-on-error: false    # set to true if you don't want to fail jobs
        uses: soteria-bc/pro-action-pilot@v1
        with:
          soteria-token: ${{ secrets.SOTERIA_TOKEN }}
```
## Managing false positives
The tool may identify potential issues that you accept as they are to e.g. save compute cycles, or genuine false positives. Ignores can be configured by adding the below annotation to the line above the line you are wanting to ignore:
```
//#[soteria(ignore)]
```
