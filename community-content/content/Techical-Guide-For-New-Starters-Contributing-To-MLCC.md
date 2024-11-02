---
title: Technical Guide for New Starters Contributing to Microsoft Learn Community Content (MLCC) #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: A Comprehensive Guide to Setting Up Your Environment and Contributing Effectively #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 11/02/2024 #Required; mm/dd/yyyy format.
contributor-type: community
---


**Principal author**: [George Chrysovalantis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/)

---

# Technical Guide for New Starters Contributing to Microsoft Learn Community Content (MLCC)

## Overview

The "Technical Guide for New Starters Contributing to Microsoft Learn Community Content (MLCC)" provides essential instructions for beginners on creating and contributing technical content to MLCC. It covers submission guidelines, content formatting, best practices, and tools to help new contributors successfully share knowledge on the Microsoft Learn platform.

## How to install visual studio code

Visual Studio Code (VS Code) is a powerful and lightweight code editor from Microsoft.
Click the link below to read the steps to install it:

[Install Visual Studio Code](/contribute/content/get-started-setup-tools?pivots=windows-os-pivot-selection)

## Install VS code extensions

VS Code extensions enhance the functionality of the editor. To install extensions:

- Open VS Code and click on the Extensions :::image type="icon" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/VS-Extensions.png" border="false"::: on the Activity Bar on the side of the window.
- Search for the extensions you need:
  - [Learn Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack)
  - [Learn-images](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-images)
  - [Learn-markdown](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-markdown)
  - [Learn-validation](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-build)
  - [Learn-yaml](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-yaml)
- Click the Install button next to the extension to add it.

Reference link: [Install Learn Authoring Pack](/contribute/content/get-started-setup-tools?pivots=windows-os-pivot-selection)

## Setup a GitHub account

GitHub is the platform for hosting and collaborating on MLCC articles. To set up an account:

- [Create a new MS account ](https://support.microsoft.com/en-us/account-billing/how-to-create-a-new-microsoft-account-a84675c3-3e9e-17cf-2911-3d56b15c0aaf)or use an existing.
- Go to the GitHub website: [https://github.com/](https://github.com/)
- Click on the Sign up for GitHub button.
- Follow the prompts to create a new account. Make sure to verify your email address.
- Once your account is set up, customize your profile and explore the features GitHub offers.

## Setup a Local Git Repository

A local repository allows you to manage your code changes locally before pushing them to GitHub.
Here’s how to set it up:

- [Overview](/contribute/content/get-started-setup-local)
- [Determine the repository](/contribute/content/get-started-setup-local)
- [Fork the repository](/contribute/content/get-started-setup-local)
- [Clone the repository](/contribute/content/get-started-setup-local)
  - [Clone from Visual Studio Code](/contribute/content/get-started-setup-local)
- [Set up remotes](/contribute/content/get-started-setup-local)

## Make major changes to Microsoft Learn Documentation

Contributing to Microsoft Learn requires adherence to certain guidelines:

- [Prerequisites](/contribute/content/how-to-write-major-edits?tabs=terminal)
- [Create and check out your working branch](/contribute/content/how-to-write-major-edits?tabs=terminal)
- [Find the source Markdown file](/contribute/content/how-to-write-major-edits?tabs=terminal)
- [Commit and push your changes](/contribute/content/how-to-write-major-edits?tabs=terminal)
- [Make your next change](/contribute/content/how-to-write-major-edits?tabs=terminal)

## Create a Pull Request (PR) in GitHub - Contributor Guide

A pull request allows you to post your changes to a repository. The changes may concern a new article or amendments to an existing article. To create one follow the steps below:

- [Prerequisites](/contribute/content/create-pull-request)
- [Open a Pull Request (PR)](/contribute/content/create-pull-request)

## Process a Pull Request - Contributor Guide

Processing a pull request involves reviewing and merging it.
Here’s how:

[Pull Request Validation](/contribute/content/process-pull-request)
[Review and address feedback](/contribute/content/process-pull-request)
[Sign-off and comment automation](/contribute/content/process-pull-request)
[Publishing](/contribute/content/process-pull-request)

## Write a New Article

Writing a new article includes several steps:

- Choose a relevant and engaging topic such as: ML & AI, Azure, Identity & Security,  Dev, DevOps & IaC, Power Platform
- Write a draft following the outline you prepared.
- Edit and revise your draft to ensure clarity and correctness.
- Format the article according to the guidelines provided below:
  - [Prerequisites and assumptions](/contribute/content/how-to-write-docs-auth-pack)
  - [How to use the Learn Markdown extension](/contribute/content/how-to-write-docs-auth-pack)
  - [How to generate a main redirect file](/contribute/content/how-to-write-docs-auth-pack)
  - [How to assign keyboard shortcuts](/contribute/content/how-to-write-docs-auth-pack)
  - [How to show the legacy "Gauntlet" toolbar](/contribute/content/how-to-write-docs-auth-pack)
  - [How to use Learn templates](/contribute/content/how-to-write-docs-auth-pack)
    - [To apply a template in VS Code](/contribute/content/how-to-write-docs-auth-pack)
    - [To add your GitHub ID and/or Microsoft alias to your VS Code settings](/contribute/content/how-to-write-docs-auth-pack)
    - [To set author and/or ms.author](/contribute/content/how-to-write-docs-auth-pack)
    - [To make a new template available in VS Code](/contribute/content/how-to-write-docs-auth-pack)
- [Demo several features](/contribute/content/how-to-write-docs-auth-pack)

## Microsoft writing style guide

- Accessibility Guidelines and Requirements
  - [Writing for all abilities](/style-guide/accessibility/writing-all-abilities)
  - [Colors and patterns in text, graphics, and design](/style-guide/accessibility/colors-patterns-text-graphics-design)
  - [Graphics, design, and media](/style-guide/accessibility/graphics-design-media)
- Writing for all abilities
  - [Put the person first](/style-guide/accessibility/writing-all-abilities)
  - [Write brief, meaningful, and focused text](/style-guide/accessibility/writing-all-abilities)
  - [Use content structure and location to communicate](/style-guide/accessibility/writing-all-abilities)
  - [Document alternate input methods](/style-guide/accessibility/writing-all-abilities)
- [Colors and patterns in text, graphics, and design](/style-guide/accessibility/colors-patterns-text-graphics-design)
- [Graphics, Design, and Media](/style-guide/accessibility/graphics-design-media)
- [Acronyms](/style-guide/acronyms)
- [Structural and technical considerations](/style-guide/chatbots-virtual-agents/structural-technical-considerations)
- [Content planning](/style-guide/content-planning)
- Developer content
  - [Reference documentation](/style-guide/developer-content/reference-documentation)
  - [Code examples](/style-guide/developer-content/code-examples)
  - [Formatting developer text elements](/style-guide/developer-content/formatting-developer-text-elements)
- Scannable content
  - [Headings](/style-guide/scannable-content/headings)
  - [Lists](/style-guide/scannable-content/lists)
  - [Pull quotes](/style-guide/scannable-content/pull-quotes)
  - [Sidebars](/style-guide/scannable-content/sidebars)
  - [Tables](/style-guide/scannable-content/tables)
- Search and writing
  - [Keywords](/style-guide/search-writing)
  - [Links and backlinks](/style-guide/search-writing)
- [Text formatting](/style-guide/text-formatting/)

## Prepare Writing

Before starting to work with GitHub you must create a local repository and fork (copy) the repository files.

The steps to achieve it are:

### Step 1. Create local repository

Create the local repository folder to your local storage drive., i.e. C:\Microsoft Community Content\

Fork the repository to create a copy to your local drive

Navigate to [https://github.com/MicrosoftDocs/community-content](https://github.com/MicrosoftDocs/community-content), select **<> Code** and press the :::image type="icon" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/copy-icon.png" border="false":::  to copy the URL.

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/github-clone-url.png" alt-text="GitCloneURL":::

Open the **TERMINAL** on the VS Code.

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/github-terminal.png" alt-text="GitHub-Terminal-Image":::

Navigate to the directory where you want to fork the MS Learn repository files.

Type, git clone [https://github.com/MicrosoftDocs/community-content.git](https://github.com/MicrosoftDocs/community-content.git)

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/git-clone-command.png" alt-text="Git-Clone-Command-Image":::

### Step 2. Create the branch

There are two reasons to use a branch:

- It helps many people to work on the same project at the same time.
- Work on the same project in various ways

A Git project can have multiple branches. Each of these branches is a standalone version for this project.

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/GitHub-Main-Branch.png" alt-text="GitHub-MainBranch-Image":::

#### Create New branch

Open the VS code and in the **Command Palette** (Ctrl + Shift + P)

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/New-Branch.png" alt-text="New-GitHub-Branch-Image":::

#### Write a new article

Follow these steps when you begin each new article:

Switch to main branch

```bash
git checkout main

```

Update the local repository with changes from the remote repository that is called “upstream”.

```bash
git pull upstream main 

```

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/git-pull-upsteream-main.png" alt-text="git-pull-upstream-main-image":::

> [!NOTE]
> At this point, you can start writing your article locally on the VS code environment.

On the left-hand side menu, on the Source Control icon:::image type="icon" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/source-control.png" border="false":::  you will find the changes they’ve been made on the local repository.

Save the changes to the local repository, type a comment and choose **Commit & Push**

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/Commit-and-push.png" alt-text="Commit-And-Push-Image":::

In the next step go to [https://github.com/](https://github.com/). From the top right corner menu select your account and then click on “*Your repositories*”.

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/Git-Hub-Repositories.png" alt-text="Git-Hub-Repositories-Image":::

> [!IMPORTANT]
> Please make sure to update the "**toc.yml**", as well

Choose the Forked community-content repository

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/Git-Hub-Forked-Repository.png" alt-text="Git-Hub-Forked-Repo-Image":::

On the top menu tap on Pull requests

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/Git-Hub-Pull-Requests.png" alt-text="Git-Hub-Pull-Requests-Image":::

Your pull request compares your topic branch with the branch in the upstream repository as the base branch.

Then press the **New pull request** button

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/New-Pull-Request-Button.png" alt-text="New-Pull-Request-Button-Image":::

> [!NOTE]
> Before the editor can see the pull request to fulfill it, there should be no errors.

If everything in the article is correct, it will be approved by the MS Learn team and in the queue for publishing.

:::image type="content" source="media/Techical-Guide-For-New-Starters-Contributing-To-MLCC/Git-Hub-Pull-Request-Checks.png" alt-text="Git-Hub-Pull-Requests-Checks-image":::

## Summarize

This technical guide provides essential guidelines for the new Microsoft Learn Community Content (MLCC) contributors. It outlines the contribution process, best practices, and resources for creating new content, ensuring a streamlined onboarding experience, and encouraging a higly collaborative learning community at Microsoft platform.
