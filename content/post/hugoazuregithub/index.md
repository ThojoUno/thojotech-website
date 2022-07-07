---
title: "Use GitHub Actions workflow to deploy your Hugo static website in Azure Storage" # Title of the blog post.
date: 2022-07-07T10:17:57-07:00 # Date of post creation.
description: "How to deploy Hugo static web site to Azure blob storage" # Description used for search engine.
featured: true # Sets if post is a featured post, making it appear on the sidebar. A featured post won't be listed on the sidebar if it's the current page
draft: false # Sets whether to render this page. Draft of true will not be rendered.
toc: false # Controls if a table of contents should be generated for first-level links automatically.
# menu: main
usePageBundles: false # Set to true to group assets like images in the same folder as this post.
#featureImage: "/logos/azure_logo_794_new.png" # Sets featured image on blog post.
#featureImageAlt: 'Description of image' # Alternative text for featured image.
#featureImageCap: 'This is the featured image.' # Caption (optional).
thumbnail: "/logos/azure_logo_794_new.png" # Sets thumbnail image appearing inside card on homepage.
#shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
codeLineNumbers: false # Override global value for showing of line numbers within code block.
figurePositionShow: true # Override global value for showing the figure label.
showRelatedInArticle: false # Override global value for showing related posts in this series at the end of the content.
categories:
  - Azure
  - Hugo
tags:
  - Azure
  - Hugo
  - GitHub
---

This article is based on Microsoft’s documentation -- [Use GitHub Actions workflow to deploy your static website in Azure Storage ](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-static-site-github-actions?tabs=userlevel) --, but has been updated to reflect changes required to publish Hugo static sites to Azure blob storage/static web sites. Please see the article linked above for a list of prerequisites.

# Generate deployment credentials
Using Azure CLI
```
$azsubId="xxxxxxxxxxxxxxxxxxxxxx"
$azStrAcctResourceGroup="rg-westus2-website"
$azstorageacct="stgwestus2webstorageacct"
az ad sp create-for-rbac --name $azstorageacct --role contributor --scopes /subscriptions/$azsubId/resourceGroups/$azStrAcctResourceGroup --sdk-auth
```
The command will generate a JSON object, copy and save for later:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
  }
```
# Configure the GitHub secret
1. In GitHub, browse our repository.
2. Select **Settings > Secrets > New secret**.
3. Paste the entire JSON output captured earlier. For secret name, use AZURE_CREDENTIALS
When you configure the workflow file later, you use the secret name for the input **creds** of the Azure Login action, for example:
```
- uses: azure/login@v1
with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```
# Add your workflow
1. Go to Actions for your GitHub repository.
2. Select **Set up your workflow yourself**.
3. Delete everything from the workflow and replace with the following code block:
```
# This is a basic workflow to help you get started with Actions

name: Blob storage website CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Upload to blob storage
      uses: azure/CLI@v1
      with:
        inlineScript: |
            az storage blob upload-batch --account-name yourstaticwebsitestorageaccount --auth-mode key -d '$web' -s ./public --overwrite
    - name: Purge CDN endpoint
      uses: azure/CLI@v1
      with:
        inlineScript: |
           az cdn endpoint purge --content-paths  "/*" --profile-name "thojotechwebcdn" --name "thojotechwebcdn" --resource-group "rg-eastus-thojotech-01" --no-wait
   
# Azure logout
    - name: logout
      run: |
            az logout
      if: always()
```
In the example above, notice the changes required for publishing Hugo static content, which uses a  **public** folder to store the static content. That is the folder we need to copy to Azure Blob storage.

Now, when you commit changes to your GitHub static website repository, the public folder will be copied to your Azure static web site blob storage automagically.



