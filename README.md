# ProjectHelper Update Between Projects

## Description

This action allows you to update a project with the information from another project. It can be used to copy information between projects.

## Calling the action

This workflow will update the destination project with the information from the source project.

It will copy all the custom fields of source project to the custom fields of the destination project that match in name, or the custom fields that match the field name prefixed by the slug if the slug is provided. There for the custom fields matching will be:

| Source Project Field Name | Destination Project Field Name | slug |
|---------------------------|--------------------------------|------|
| field1                    | field1                         |      |
| field2                    | slug_field2                    | slug |

Create the custom fields in the destination project with the same name as the source project, or with the name prefixed by the slug to have them populated.

## Workflow Example

```yaml
name: Update Project Due Date
on:
  # Run manually
  workflow_dispatch:

    # run at 5am every morning
  schedule:
    - cron: '0 5 * * *' 
jobs:
  ProjectUpdate:
    name: Update Project Between Projects
    runs-on: ubuntu-latest

    env:
      GH_TOKEN:                       ${{ secrets.GH_PAT }}
      BTW_SOURCE_OWNER:               ${{ vars.BTW_SOURCE_OWNER }}
      BTW_DESTINATION_OWNER:          ${{ vars.BTW_DESTINATION_OWNER }}
      BTW_SOURCE_PROJECT_NUMBER:      ${{ vars.BTW_SOURCE_PROJECT_NUMBER }}
      BTW_DESTINATION_PROJECT_NUMBER: ${{ vars.BTW_DESTINATION_PROJECT_NUMBER }}
      BTW_FIELD_SLUG:                 ${{ vars.BTW_FIELD_SLUG }}

    steps:

      # Install ProjectHelper module
      - name: Powershell Module Setup
        uses: rulasg/psmodule-setup-action@v2
        with:
          Name: ProjectHelper
          AllowPreReleaseVersions: true

        # ProjectHelper Update Status on Due Date run
      - name: Update Project Between Projects
        id: updated-run
        uses: rulasg/projecthelper-action-update-betweenprojects@v1
        with:
          TOKEN:                    ${{ env.GH_TOKEN }}
          SourceOwner:              ${{ env.BTW_SOURCE_OWNER }}
          SourceProjectNumber:      ${{ env.BTW_SOURCE_PROJECT_NUMBER }}
          DestinationOwner:         ${{ env.BTW_DESTINATION_OWNER }}
          DestinationProjectNumber: ${{ env.BTW_DESTINATION_PROJECT_NUMBER }}
          FieldSlug:                ${{ env.BTW_FIELD_SLUG }}

```

## Using repo variables

You can use the following variables in your workflow to allow chainging the target projects from repo configurations.
Use the following script to set variables and secrets. Change the --body values with yours

```bash
# Set GitHub Variables
gh variable set BTW_SOURVER_OWNER --body "github"
gh variable set BTW_DESTINATION_OWNER --body "github"
gh variable set BTW_SOURCE_PROJECT_NUMBER --body "20521"
gh variable set BTW_DESTINATION_PROJECT_NUMBER --body "9279"
gh variable set BTW_FIELD_SLUG --body "oa_"

# Set GitHub Secrets (prompting securely)
echo "Enter GH_PAT secret:"
gh secret set GH_PAT --body "$(read -s GH_PAT && echo $GH_PAT)"

echo "All variables and secrets have been set successfully."
```