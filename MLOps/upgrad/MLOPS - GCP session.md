
##### Enabling GCP services to the project

```bash
gcloud services enable compute.googleapis.com storage.googleapis.com artifactregistry.googleapis.com run.googleapis.com cloudbuild.googleapis.com iamcredentials.googleapis.com sts.googleapis.com logging.googleapis.com monitoring.googleapis.com
```


##### List the GCP Services having access to the project

```bash
gcloud services list --enabled --format="value(config.name)" | Select-String "compute|storage|artifactregistry|run|cloudbuild|iamcredentials|sts|logging|monitoring"
```

```bash
gcloud storage buckets describe gs://upgrad-0103-churn-mlops --format="value(name,location)"
```

```bash
gcloud iam service-accounts create github-actions-sa `
  --display-name="GitHub Actions CI/CD"
```

```bash
# Assign 4 roles
$SA_EMAIL = "github-actions-sa@upgrad-103.iam.gserviceaccount.com"
$roles = @(
  "roles/run.admin",
  "roles/artifactregistry.writer",
  "roles/storage.admin",
  "roles/iam.serviceAccountUser"
```

```bash



$PROJECT_ID     = "upgrad-103"
$PROJECT_NUMBER = gcloud projects describe $PROJECT_ID --format="value(projectNumber)"
$POOL_ID        = "github-pool"
$PROVIDER_ID    = "github-provider"
$SA_EMAIL       = "github-actions-sa@upgrad-103.iam.gserviceaccount.com"
$GITHUB_REPO    = "AdityaJyoti1/churn-mlops"

# Create identity pool
gcloud iam workload-identity-pools create $POOL_ID `
  --location=global `
  --display-name="GitHub Actions Pool"

# Create OIDC provider
gcloud iam workload-identity-pools providers create-oidc $PROVIDER_ID `
  --location=global `
  --workload-identity-pool=$POOL_ID `
  --display-name="GitHub Provider" `
  --attribute-mapping="google.subject=assertion.sub,attribute.repository=assertion.repository" `
  --attribute-condition="assertion.repository_owner == 'AdityaJyoti1'" `
  --issuer-uri="https://token.actions.githubusercontent.com"

# Bind repo to service account
$MEMBER = "principalSet://iam.googleapis.com/projects/$PROJECT_NUMBER/locations/global/workloadIdentityPools/$POOL_ID/attribute.repository/$GITHUB_REPO"
gcloud iam service-accounts add-iam-policy-binding $SA_EMAIL `
  --role="roles/iam.workloadIdentityUser" `
  --member=$MEMBER
  
```

