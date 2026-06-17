# Azure DevOps YAML Structure

## 1. YAML Hierarchy

Azure DevOps YAML follows a hierarchical structure:

Pipeline
├── Trigger
├── Pool
├── Parameters
├── Variables
└── Stages
└── Jobs
└── Steps
├── Script
├── PowerShell
├── Bash
└── Tasks

---

## 2. Simplest Pipeline

```yaml
trigger: none

pool:
  name: Default

steps:
- script: echo Hello World
```

Azure DevOps automatically creates:

```text
Stage (implicit)
└── Job (implicit)
    └── Step
```

---

## 3. Pipeline With Jobs

```yaml
trigger: none

pool:
  name: Default

jobs:
- job: Test

  steps:
  - script: echo Running tests
```

Azure DevOps automatically creates:

```text
Stage (implicit)
└── Job: Test
```

---

## 4. Pipeline With Stages

```yaml
trigger: none

pool:
  name: Default

stages:

- stage: Build

  jobs:

  - job: BuildJob

    steps:

    - script: echo Building

- stage: Test

  jobs:

  - job: TestJob

    steps:

    - script: echo Testing
```

---

## 5. Parameters vs Variables

### Parameters

Compile-time values.

```yaml
parameters:
- name: environment
  type: string
  default: dev
```

Usage:

```yaml
${{ parameters.environment }}
```

Characteristics:

* Evaluated before pipeline execution
* Cannot be modified during runtime
* Used frequently in templates

---

### Variables

Runtime values.

```yaml
variables:
  environment: dev
```

Usage:

```yaml
$(environment)
```

Characteristics:

* Evaluated on the agent during execution
* Can be modified during runtime
* Used for configuration values

---

## 6. Conditions

### Run only if previous step/job succeeds

```yaml
condition: succeeded()
```

### Run only if previous step/job fails

```yaml
condition: failed()
```

### Run regardless of result

```yaml
condition: always()
```

### Run only if cancelled

```yaml
condition: canceled()
```

---

## 7. Dependencies

### Job Dependency

```yaml
- job: Apply
  dependsOn: Plan
```

Apply waits for Plan to finish.

---

### Multiple Dependencies

```yaml
- job: Deploy
  dependsOn:
  - UnitTests
  - SecurityScan
```

Deploy waits for both jobs.

---

## 8. Templates

### Step Template

Template:

```yaml
steps:

- script: terraform validate
```

Usage:

```yaml
steps:

- template: templates/terraform-validate.yml
```

---

### Job Template

Template:

```yaml
jobs:

- job: TerraformPlan

  steps:

  - script: terraform plan
```

Usage:

```yaml
jobs:

- template: templates/terraform-plan-job.yml
```

---

## 9. Pipeline Artifacts

Publish a file:

```yaml
- publish: terraform/tfplan
  artifact: terraform-plan
```

Download artifact:

```yaml
- download: current
  artifact: terraform-plan
```

Purpose:

* Transfer files between jobs
* Transfer files between stages
* Store build outputs

---

## 10. Variable Groups

Azure DevOps:

Pipelines → Library → Variable Groups

Example:

terraform-lab

```text
location = westeurope
resourceGroupName = rg-devops-lab
azureSubscription = sc-azure-platform-lab
```

Usage:

```yaml
variables:
- group: terraform-lab
```

Reference:

```yaml
$(location)
$(resourceGroupName)
$(azureSubscription)
```

---

## 11. Service Connections

Used by tasks such as AzureCLI@2.

Example:

```yaml
- task: AzureCLI@2
  inputs:
    azureSubscription: 'sc-azure-platform-lab'
```

Important:

azureSubscription refers to the Service Connection name, not the Azure Subscription name.

---

## 12. Enterprise Terraform Flow

```text
Stage: Plan
    │
    ├── terraform init
    ├── terraform plan -out=tfplan
    └── Publish Artifact
            │
            ▼
        Approval
            │
            ▼
Stage: Apply
    │
    ├── Download Artifact
    └── terraform apply tfplan
```

Benefits:

* Reproducible deployments
* Approval workflow
* Audit trail
* Separation of planning and deployment
