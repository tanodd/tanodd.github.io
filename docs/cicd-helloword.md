## Github CICD
### Github Workflow
### Path of directory
```bash
.github
|-workflows
|-   name_of_cicd_file.yml
```
### Structure
```
name: Hello CI/CD

on:
  push:
    paths:
      - 'app/**'  # Run only when files in the 'app' folder change

jobs:
  say-hello:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print a message
        run: echo "Hello, CI/CD! 🚀 - Only runs when files in 'app/' change."
```
