# Workflow CICD for node applications in charge to deploy in S3 bucket service.

## Summary

This workflow allow you to build and deploy your application using S3 bucket, using the git flow.

![APLICATION GIT FLOW](https://github.com/Iberia-Ent/software-engineering--git-flow--template/assets/87304455/51d5f609-d814-4ce7-9f05-4af3fc4e0e43)

## Usage pipeline

Call directly from your workflow file like:

```
jobs:
	NODE:
	uses: Iberia-Ent/software-engineering--workflow--node--ci--cd-s3--git-flow/.github/workflows/ci-cd-node-s3.yml@v1.0
	secrets: inherit
```

## Set up repository properties

### Environment's configuration

You need to create the following environments in your repository:

- `Integration`: You need to create the **AWS_DEPLOYMENT_ROLE** secret and add the value that it should be the role for deploying the application in the integration environment.
- `Preproduction`: You need to create the **AWS_DEPLOYMENT_ROLE** secret and add the value that it should be the role for deploying the application in the preproduction environment. 
- `Production`: You need to create the **AWS_DEPLOYMENT_ROLE** secret and add the value that it should be the role for deploying the application in the production environment.

### Variables configuration

On your repository, go to Settings > Secrets and Variables and add the following variables (repository scope):

- `CI_LEVEL_CONFIG_FILE`: This variable is used to set level to read of the configuration file (ci-config.json file). Example: prod
- `CODEQL_LANGUAGES`: This variable is used to set the languages to analyze with CodeQL. Example: javascript
- `RUNNER_GROUP`: This variable is used to set your runner group. Example: my-group-runner
- `RUNNER_LABELS`: This variable is used to set the runner labels. Example: "node", 'node', ["label1","label2"], ['label1','label2']

### Secrets configuration

- Create the secret **AWS_RESOURCES_COMMON_ROLE** (in secret section but repository scope) and add the value of the role of that account where you want to read the artifacts or the ECR image in order to deploy your application.

> [!NOTE]
> Remember that you need to insert **AWS_DEPLOYMENT_ROLE** secret in the environment's configuration section.

### Branches rules

1. You will use the git flow, so you need to have the following branches:

	- main
	- develop
	- staging
	- feat/[feature name] (working branch)

2. Add some rules to main, develop and staging branch to make sure that you follow the flow and anyone can deploy in environments like preproduction or production.

   	- main:
		- Require a pull request before merging and check "Require approvals" option.
		- Require status checks to pass before merging and check "Require branches to be up to date before merging" option.
		- Do not allow bypassing the above settings.
		- Restrict who can push to matching branches: Add to the engineer, release management and technical lead product team.

	- develop and staging:
		- Require status checks to pass before merging
		- Require status checks to pass before merging and check "Require branches to be up to date before merging" option.
		- Do not allow bypassing the above settings.
		- Restrict who can push to matching branches: Add to the engineer, release management and technical lead product team.

## Setup pipeline

### Pipeline files hierarchy

You will use the github flow for building and uploading the distribution package.

You should have these files:

- package.json
- .github/config/ci-config.json
- .github/workflows/ci-cd-node-s3.yml
- .github/workflows/dependabot.yml

You can modify these files:

- package.json
- .github/config/ci-config.json

[Environment Name]: Integration, Preproduction or Production

### Set up ci-config.json file

#### Variables

| Variable                                       | Required | Type   |
| ---------------------------------------------- | -------- | ------ |
| AWS_ECR_IMAGE_BASE                        	 | yes      | string |
| AWS_ECR_REGISTRY                        		 | yes      | string |
| AWS_ECR_REPOSITORY_NAME                        | yes      | string |
| AWS_REGION                                     | yes      | string |
| AWS_RESOURCES_COMMON_ACCOUNT_ID                | yes      | string |
| AWS_RESOURCES_COMMON_DOMAIN                    | yes      | string |
| AWS_RESOURCES_COMMON_LIBRARY_REPOSITORY_FORMAT | yes      | string |
| AWS_RESOURCES_COMMON_LIBRARY_REPOSITORY_NAME   | yes      | string |
| NODE_VERSION                                   | yes      | string |
| S3_DEPLOY_BUCKET_REPOSITORY_NAME               | yes      | string |
| S3_PACKAGE_DISTRIBUTION_FORMAT                 | yes      | string |
| S3_RELEASE_BUCKET_REPOSITORY_NAME              | yes      | string |
| DEPLOYMENT_URL                                 | yes      | string |

> [!NOTE]
> **S3_RELEASE_BUCKET_REPOSITORY_NAME** is the name s3 bucket where the distribuibles files are uploaded in zip file.
> **S3_DEPLOY_BUCKET_REPOSITORY_NAME** is the name s3 bucket where the distribuibles files are uploaded and shared through cloudfront. 

For setting up the ci-config.json file with your custom properties of your project, go to your working feature branch and make the changes that you need using the above variables for example:

```
{
	"ci": {
		"AWS_ECR_IMAGE_BASE": "NA",
		"AWS_ECR_REGISTRY": "NA",
		"AWS_ECR_REPOSITORY_NAME": "NA",
		"AWS_REGION": "eu-west-1",
		"AWS_RESOURCES_COMMON_ACCOUNT_ID": "329066851743",
		"AWS_RESOURCES_COMMON_DOMAIN": "iberia",
		"AWS_RESOURCES_COMMON_LIBRARY_REPOSITORY_FORMAT": "npm",
		"AWS_RESOURCES_COMMON_LIBRARY_REPOSITORY_NAME": "npm-library-store",
		"NODE_VERSION": "18.x",
		"S3_DEPLOY_BUCKET_REPOSITORY_NAME": "NA",
		"S3_PACKAGE_DISTRIBUTION_FORMAT": "zip",
		"S3_RELEASE_BUCKET_REPOSITORY_NAME": "node-releases-store-operation-mro-mro"
	},
	"cd-int": {
		"AWS_REGION": "eu-west-1",
		"S3_RELEASE_BUCKET_REPOSITORY_NAME": "node-releases-store-integration-operation-mro-mro",
		"S3_PACKAGE_DISTRIBUTION_FORMAT": "zip",
		"S3_DEPLOY_BUCKET_REPOSITORY_NAME": "server-s3-poc",		
		"DEPLOYMENT_URL": "https://miweb-integration.com"
	},
	"cd-pre": {
		"AWS_REGION": "eu-west-1",
		"S3_RELEASE_BUCKET_REPOSITORY_NAME": "node-releases-store-preproduction-operation-mro-mro",
		"S3_PACKAGE_DISTRIBUTION_FORMAT": "zip",
		"S3_DEPLOY_BUCKET_REPOSITORY_NAME": "server-s3-poc",		
		"DEPLOYMENT_URL": "https://miweb-preproduction.com"
	},
	"cd-prod": {
		"AWS_REGION": "eu-west-1",
		"S3_RELEASE_BUCKET_REPOSITORY_NAME": "node-releases-store-prodution-operation-mro-mro",
		"S3_PACKAGE_DISTRIBUTION_FORMAT": "zip",
		"S3_DEPLOY_BUCKET_REPOSITORY_NAME": "server-s3-poc",		
		"DEPLOYMENT_URL": "https://miweb-production.com"
	}
}
```

### Build and upload the application

- Create a feature working branch following the naming convention. For example: feat/mybranch
- Add your changes or commit it into your working branch.
- Push your commits to remote repository.
- For building your code and see if pass the tests and security process, open a "Pull Request", select base branch: develop and compare branch: feat/mybranch.
- You have to wait that every status checks are sucessfully and when it's happen just confirm your changes and the merge request. Your code will be built, will execute all test and it will upload the package distribution to S3 bucket and also the image in the ECR service (if you have value in AWS_ECR_REPOSITORY_NAME variable in the ci-config.json). Finally, the pipeline in the CD job will deploy your image in a ECS cluster for integration environment.
- Go to the "Action" tab and see if it has finished sucessfully or not te pipeline execution.
- If you want to continue and deploy to preproduction environment, open a "Pull Request", select base branch: staging and compare branch: develop.  
- Go to the "Action" tab and see if it has finished sucessfully or not te pipeline execution. If it has finished sucessfully your code are deployed into Preproduction environment and check the tag created automatically because you should create the release from that tag when you want to deploy to production.
- For deploying to production go to "tags" tab and create the release of the tag created automatically in the previous step.
- Go to "Actions" tab and see the status of the execution of the pipeline, if it was sucessfully, your code are in production environment.
- Delete your custom branch. Example: feat/mybranch

> [!NOTE]
> If you have reviewers in any environment, remember that you have to review the deployment otherwise the pipeline will not be executed because it is waiting for the approval.

### Rollback the application
Find all process in the [rollback documentation](https://github.com/Iberia-Ent/software-engineering--git-flow--template#how-to-make-a-rollback---product-mode).


## Contributing

Briefly explains how your team members or others can contribute to the project

For the contribution and workflow guide, see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Maintainers

Contact information of the team member whose is responsible for the project, see [CONTRIBUTING.md](./CONTRIBUTING.md).