---
title: "Notes from the field - Microsoft Office 365 Migration" # Title of the blog post.
date: 2022-07-05T09:38:55-07:00 # Date of post creation.
description: "Lessions learned when migrating from Microsoft Office 2010 and Exchange on-prem to Office 365 in the cloud" # Description used for search engine.
featured: true # Sets if post is a featured post, making it appear on the sidebar. A featured post won't be listed on the sidebar if it's the current page
draft: false # Sets whether to render this page. Draft of true will not be rendered.
toc: false # Controls if a table of contents should be generated for first-level links automatically.
# menu: main
usePageBundles: false # Set to true to group assets like images in the same folder as this post.
#featureImage: "/images/path/file.jpg" # Sets featured image on blog post.
featureImageAlt: 'Description of image' # Alternative text for featured image.
featureImageCap: 'This is the featured image.' # Caption (optional).
#thumbnail: "/images/path/thumbnail.png" # Sets thumbnail image appearing inside card on homepage.
#shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
codeLineNumbers: false # Override global value for showing of line numbers within code block.
figurePositionShow: true # Override global value for showing the figure label.
showRelatedInArticle: false # Override global value for showing related posts in this series at the end of the content.
categories:
  - "Notes from the field"
tags:
  - Office365
  - Box
  - OneDrive
  - Microsoft
---

**My role**: Technical Advisor
**Company type**: Large Hospitality & Gaming

On a recent Microsoft Office365/Box to OneDrive migration project, I was asked to put together a lessons-learned for the project. Listed below are some of those lessons. The Microsoft Office 365 upgrade project had in scope upgrading users from Office 2010 to O365.

  
- Make sure you have a good sampling of users in the pilot. Office workers, executives, etc.
  
- Inventory is everything.
  
- Really need good configuration management to help determine where computers reside. *--COMPANY--* network team does not keep up to date records of what subnets exist at the properties. Workday, their employee management/organizational tool was not always accurate in determining where a user resides. Users moved around a lot and paperwork was slow to follow them.
  
- UDA, (user device affinity) in Microsoft System Center Configuration Manager, is challenging in multiuser environments. In some cases, 5-10 users could share one computer, or 30-40 users could share 20 or more computers
  
- Don’t wait until the project is inflight to begin compatibility testing
  
- If you are deploying to multiple locations/properties, you need a point person(s) and PM per property. The Senior PM will be overwhelmed with scheduling if they are trying to handle it for all properties, you need multiple PMs assigned
  
- What we found out is *--COMPANY--* runs their business on PSTs. Due to the limited mailbox size allocated to users 500MB or less, users save ALL their important mail to PST’s. Microsoft BROKE the functionality for PST’s beginning with Outlook 2013. Breaking changes include the inability to reference PST’s on Network drives over 4GB in size and inability to SEARCH PST’s located on network drives. Other than moving the PST data to the O365 Online Archive, still do not have a good answer how different departments should be storing information from emails. If you have a short email retention policy, where do you store this data, which might include contracts, agreements, etc, that should persist beyond most email retention policies.
  
- Legal considerations might prevent you from migrating users email from onprem to O365, make sure you understand what your limitations are prior to the project beginning and understanding how these situations should be handled. More than likely, you will need additional documentation and "evidence" regarding fidelity of email migrations (Exchange on-premises to Exchange Online/O365), and file migrations (Box to OneDrive).
  
- Shared calendars, mailboxes and other resources present a challenge when it comes to migration from on-prem to O365. You cannot have split homes for shared resources. If you have multiple people sharing resources, they must be cutover at the same time, or those left behind On-prem will not be able to manage those resources.
  
- Third party SSO really complicates everything. Introduces problems in syncing licensing.

- Make sure you have a good mobile device management strategy. *--COMPANY--* uses OKTA but has no way to deploy to iPhones or Android devices, email cutover was made more difficult due to having to provide resources to help users change configurations on their phones.
  
- Make sure other projects like desktop refresh do not coexist at the same time. In many cases, hardware refresh would complete a week or less before we upgraded a property and it wrecked UDA and we were unable to target users with confidence.
   
# Box to OneDrive Migration
  
- Make sure you identify users who may have legal restrictions. *--COMPANY--* users on legal hold, I had to document and submit a report on all the files they owned and that they were copied to OneDrive without any modifications.

- You should probably archive ALL data from the source in case retrieval is required. We had quite a few cases of users leaving the company, but their files on Box were still shared. We did not move users unless they were active in the company.

- Picking a good tool like SkySync allowed the project to move faster. 

- OneDrive had more file length and path limitations that Box, so reports had to be run to determine where these files resided and moved manually.

- OneDrive is slower than Box, if you need faster access, make sure you negotiate this with Microsoft ahead of time, or use Azure blob storage for things like digital media
