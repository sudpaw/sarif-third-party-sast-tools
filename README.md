# Terraform Security Scanning with tfsec

## Overview
[`tfsec`](https://github.com/aquasecurity/tfsec) is a static analysis tool that scans Terraform code for security vulnerabilities and best practice violations.

This repository uses `tfsec` along with GitHub Actions to automatically scan Terraform configurations and upload security findings to GitHub Security Code Scanning.

## Setup
To integrate `tfsec` into your GitHub workflow, follow these steps:

## How to start this course

<!-- For start course, run in JavaScript:
'https://github.com/new?' + new URLSearchParams({
  template_owner: 'TBD-organization',
  template_name: 'TBD-course-name',
  owner: '@me',
  name: 'TBD-organization-TBD-course-name',
  description: 'My clone repository',
  visibility: 'public',
}).toString()
-->

[![start-course](https://user-images.githubusercontent.com/1221423/235727646-4a590299-ffe5-480d-8cd5-8194ea184546.svg)](https://github.com/new?template_name=sarif-third-party-sast-tools&template_owner=danf22&name=sarif-third-party-sast-tools&description=GitHub+Skills:+Introduction+to+sarif&visibility=public)

### 1. Add a GitHub Actions Workflow
Create a file `.github/workflows/tfsec.yml` with the following content:

```yaml
name: Terraform Security Scan

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  tfsec:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Run tfsec with SARIF output
        uses: aquasecurity/tfsec-sarif-action@v0.1.4
        with:
          sarif_file: tfsec.sarif

      - name: Upload SARIF report to GitHub
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: tfsec.sarif
```

### 2. Enable GitHub Code Scanning
Add this Terraform file with tha name [`main.tf`]
```yaml
resource "aws_security_group_rule" "my-rule" {
    type        = "ingress"
    cidr_blocks = ["0.0.0.0/0"]
}

resource "aws_alb_listener" "my-valid-alb-listener"{
    port     = "80"
    protocol = "HTTPS"
}

resource "aws_alb_listener" "my-wrong-alb-listener"{
    port     = "80"
    protocol = "HTTP"
}

resource "aws_db_security_group" "my-group" {

}

variable "enableEncryption" {
    default = false
}

resource "azurerm_managed_disk" "source" {
    encryption_settings {
        enabled = var.enableEncryption
    }
}
```
### 3.  Code Scanning Tab
1. Click on the **Security** tab.
3. Under **Code scanning**, 
4. Review all your security issues.

## Running tfsec Locally
If you want to run `tfsec` locally before pushing changes, install it with:

```sh
brew install tfsec  # macOS (Homebrew)
# or
curl -s https://raw.githubusercontent.com/aquasecurity/tfsec/main/scripts/install_linux.sh | bash  # Linux
# or
scoop install tfsec  # Windows (Scoop)
```

Then scan your Terraform code:

```sh
tfsec .
```

## Interpreting Results
- **High/Medium/Low Issues**: `tfsec` categorizes findings by severity.
- **Fixing Issues**: Follow recommendations provided in the scan output.
- **Suppressing False Positives**: Use `#tfsec:ignore:<rule-id>` comments in your Terraform files.

## References
- [tfsec Documentation](https://aquasecurity.github.io/tfsec/)
- [GitHub Advanced Security](https://docs.github.com/en/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors)


