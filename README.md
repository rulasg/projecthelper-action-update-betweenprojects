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


## Example

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
      GH_TOKEN: ${{ secrets.GH_PAT }}
      DUE_OWNER: ${{ vars.DUE_OWNER }}
      DUE_PROJECT_NUMBER: ${{vars.DUE_PROJECT_NUMBER}}
      DUE_FIELD_NAME: ${{ vars.DUE_FIELD_NAME }}
      DUE_STATUS: ${{ vars.DUE_STATUS }}
      

    steps:

    # Install ProjectHelper module
      - name: Powershell Module Setup
        uses: rulasg/psmodule-setup-action@v2
        with:
          Name: ProjectHelper
          AllowPreReleaseVersions: true

      # ProjectHelper Update Status on Due Date run
      - name: Update Project Due Dates
        id: updated-due
        uses: rulasg/update-ProjectItemsStatusOnDueDate@v1
        with:
          TOKEN: ${{ env.GH_TOKEN }}
          ProjectOwner: ${{ env.DUE_OWNER }}
          ProjectNumber: ${{ env.DUE_PROJECT_NUMBER }}
          DueDateFieldName: ${{ env.DUE_FIELD_NAME }}
          Status: ${{ env.DUE_STATUS }}

```
