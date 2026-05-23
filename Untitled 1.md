Welcome to Cloud Shell! Type "help" to get started, or type "gemini" to try prompting with Gemini CLI.
Your Cloud Platform project in this session is set to angular-lambda-497201-t0.
Use `gcloud config set project [PROJECT_ID]` to change to a different project.
retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud org-policies reset iam.managed.disableServiceAccountApiKeyCreation \
     --project=angular-lambda-497201-t0
API [orgpolicy.googleapis.com] not enabled on project [angular-lambda-497201-t0]. Would you like to enable and retry (this will take a few minutes)? (y/N)?  y

Enabling service [orgpolicy.googleapis.com] on project [angular-lambda-497201-t0]...
Operation "operations/acat.p2-106787268148-b7778805-2a50-4d1d-90dd-90649ec90af0" finished successfully.
ERROR: (gcloud.org-policies.reset) [retryhu41@gmail.com] does not have permission to access projects instance [angular-lambda-497201-t0] (or it may not exist): Permission 'orgpolicy.policies.create' denied on resource '//cloudresourcemanager.googleapis.com/projects/angular-lambda-497201-t0' (or it may not exist). This command is authenticated as retryhu41@gmail.com which is the active account specified by the [core/account] property.
- '@type': type.googleapis.com/google.rpc.ErrorInfo
  domain: cloudresourcemanager.googleapis.com
  metadata:
    permission: orgpolicy.policies.create
    resource: projects/angular-lambda-497201-t0
  reason: IAM_PERMISSION_DENIED
retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud org-policies reset iam.managed.disableServiceAccountApiKeyCreation --project=angular-lambda-497201-t0
ERROR: (gcloud.org-policies.reset) [retryhu41@gmail.com] does not have permission to access projects instance [angular-lambda-497201-t0] (or it may not exist): Permission 'orgpolicy.policies.create' denied on resource '//cloudresourcemanager.googleapis.com/projects/angular-lambda-497201-t0' (or it may not exist). This command is authenticated as retryhu41@gmail.com which is the active account specified by the [core/account] property.
- '@type': type.googleapis.com/google.rpc.ErrorInfo
  domain: cloudresourcemanager.googleapis.com
  metadata:
    permission: orgpolicy.policies.create
    resource: projects/angular-lambda-497201-t0
  reason: IAM_PERMISSION_DENIED
retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud projects add-iam-policy-binding angular-lambda-497201-t0 \
  --member="user:retryhu41@gmail.com" \
  --role="roles/orgpolicy.policyAdmin"
ERROR: Policy modification failed. For a binding with condition, run "gcloud alpha iam policies lint-condition" to identify issues in condition.
ERROR: (gcloud.projects.add-iam-policy-binding) INVALID_ARGUMENT: Role roles/orgpolicy.policyAdmin is not supported for this resource.
retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud beta resource-manager org-policies reset iam.managed.disableServiceAccountApiKeyCreation \
  --project=angular-lambda-497201-t0
ERROR: (gcloud.beta.resource-manager.org-policies) Invalid choice: 'reset'.
Maybe you meant:
  gcloud org-policies
  gcloud resource-manager

To search the help text of gcloud commands, run:
  gcloud help -- SEARCH_TERMS
retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud resource-managerorg-policies disable-enforcement iam.managed.disableServiceAccountApiKeyCreation \
  --project=angular-lambda-497201-t0
ERROR: (gcloud) Invalid choice: 'resource-managerorg-policies'.
Maybe you meant:
  gcloud iam policies create
  gcloud iam policies delete
  gcloud iam policies get
  gcloud iam policies list
  gcloud iam policies update
  gcloud iam service-accounts disable
  gcloud resource-manager org-policies disable-enforce

To search the help text of gcloud commands, run:
  gcloud help -- SEARCH_TERMS
retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud resource-managerorg-policies delete iam.managed.disableServiceAccountApiKeyCreation \
  --project=angular-lambda-497201-t0
ERROR: (gcloud) Invalid choice: 'resource-managerorg-policies'.
Maybe you meant:
  gcloud iam policies delete
  gcloud iam policies create
  gcloud iam policies get
  gcloud iam policies list
  gcloud iam policies update

To search the help text of gcloud commands, run:
  gcloud help -- SEARCH_TERMS

retryhu41@cloudshell:~ (angular-lambda-497201-t0)$ gcloud resource-managerorg-policies allow iam.managed.disableServiceAccountApiKeyCreation \
  --project=angular-lambda-497201-t0
ERROR: (gcloud) Invalid choice: 'resource-managerorg-policies'.
Maybe you meant:
  gcloud iam policies create
  gcloud iam policies delete
  gcloud iam policies get
  gcloud iam policies list
  gcloud iam policies update
  gcloud resource-manager org-policies allow

To search the help text of gcloud commands, run:
  gcloud help -- SEARCH_TERMS