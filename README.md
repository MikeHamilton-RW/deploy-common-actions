# Deploy Common Actions
Push a common Github Action yaml script from a centralized repository to many repositories. For use in a repo where your primary action is contained. For example, if you or your organization has 20 repos of Android applications, all of which build in a similar way, you can utilize one master yaml script, and deploy that script to each repo with this action.

If you have 30 repos, utilize this action 30 times in your yaml script to push out a common Github Action workflow. Helpful for those organizations working on a large number of similar applications.


## Action Inputs
- **GITHUB_TOKEN**: Required. Typically this will be `${{ secrets.GITHUB_TOKEN }}`.
- **USER**: Required. User or organization owning the repository where you are deploying the Github Action.
- **REPOSITORY**: Required. User and name of the repository to pull the release. Currently, you must have permissions to create and merge PRs in that repository. Future updates will include a flag to allow pulling from any public repository.
- **DEVELOPMENT_BRANCH**: Required. Set to the development branch of the repository you are pulling from, i.e., 'develop' or 'integration'.
- **COMMIT_MESSAGE**: Optional. Custom message for commit. Default message: Updating Github Action workflows.
- **GHA_DEPLOY_BRANCH_NAME**: Optional. Name of the branch to be created. Default branch name: update_gha_source.

## Action Outputs
- **NONE**


## Example
Here's an example of how you can utilize this action and a recommended github folder structure:

    .
    ├── .github/workflow/deploy.yml   # Your workflow to deploy the script, as shown in the example below.
    ├── my-common-workflow            # Your common workflow you wish to deploy. 
      └──  .github
        └── workflows
          └── action-to-deploy.yaml
    └── README.md
    
This example will deploy your common Github Action workflow when the event type "deploy_updated_workflow" is sent:

```yml
name: Deploy Github Action

on:
  repository_dispatch:
    types: [deploy_updated_workflow]

jobs:

  build:
  
    runs-on: ubuntu-latest
    
    steps:
    
    - name: Run the actions/checkout
      uses: actions/checkout@722adc6
      
    - name: Deploy GHA to WN-SDK-TEMPORARY
      uses: MikeHamilton-RW/deploy-common-actions@v1.0
      env:
        GITHUB_TOKEN: ${{ secrets.TOKEN }}
        USER: "my-user-name-or-organization"
        REPOSITORY: "my-repo"
        DEVELOPMENT_BRANCH: "develop"
        GHA_DEPLOYMENT_FOLDER: "gha-source"
        COMMIT_MESSAGE: "Deploy new Github Action"
        GHA_DEPLOY_BRANCH_NAME: "my_new_yaml_script_branch"
    
```

## Notes
- Your token must have full permissions to the receiving repo. 


## Trigger via repo dispatch
- You probably don't want to deploy your script each time you push this yaml script, so I highly reccomend only run on repository_dispatch:
```
curl -H "Accept: application/vnd.github.everest-preview+json" \
    -H "Authorization: token ${GITHUB_TOKEN}" \
    --request POST \
    --data '{"event_type": "deploy_updated_workflow"}' \
    https://api.github.com/repos/:owner/:repo/dispatches
```  
