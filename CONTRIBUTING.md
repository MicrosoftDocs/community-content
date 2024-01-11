# Contributing

This project welcomes contributions and suggestions. Most contributions require you to
agree to a Contributor License Agreement (CLA) declaring that you have the right to,
and actually do, grant us the rights to use your contribution. For details, visit
https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need
to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the
instructions provided by the bot. You will only need to do this once across all repositories using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/)
or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Allowed content

* Quality, helpful, technical content that adds value (for example, solving a problem, sharing valuable tips or processes, or reducing time to implement a solution).
* Content that uses your own voice and style.
* Content that *occasionally* quotes or refers to other sources or webpages, but you must cite your sources.

## Prohibited content

* Marketing or advertising.
* Licensing or pricing information.
* Content generated exclusively by AI. If AI assisted in writing the content, this must be explicitly mentioned by adding 'AI-contribution' as a value to the 'content_well_notification' metadata attribute. Adding this value will add a disclaimer at the top of the article.
* Harmful content, such as processes that may leave a system or process in a stuck state or that clearly violate laws or terms and conditions.
* Content that doesn't belong to you. Plagiarism is strictly prohibited.
* Low-effort articles, such as having a single link to another site, a single video, or an article with only links in it.
* Articles where the primary purpose is self-promotion.
* News and announcements.

## Get started contributing

1. [Set up your GitHub account](https://learn.microsoft.com/contribute/content/#create-a-github-account) and [install authoring tools](https://learn.microsoft.com/contribute/content/get-started-setup-tools) such as VS Code.
1. Fork this repository to create a copy in your GitHub account:
    * Open [MicrosoftDocs/community-content](https://github.com/MicrosoftDocs/community-content) (this repository).
    * Select **Fork** --> The URL of your fork is now `https://github.com/<YOUR_GITHUB_ACCOUNT>/community-content/`.
1. Clone the repository to have it locally available:
    * Select **Code**.
    * Copy the URL (it is `https://github.com/<YOUR_GITHUB_ACCOUNT>/community-content.git`).
      ![clone repo](community-content/media/contribution/clone-url.png)
    * Open the terminal in VS Code.
    * Navigate to a directory where you want to clone the repository.
    * Type `git clone <COPIED URL HERE>`.
1. [Set up remotes](https://learn.microsoft.com/en-us/contribute/content/get-started-setup-local#set-up-remotes) to establish a connection between your local repository and the original repository.
1. [Create a working branch](https://learn.microsoft.com/contribute/content/how-to-write-major-edits?tabs=terminal#create-and-check-out-your-working-branch) and write your article! Here are some resources to refer to, if you need them:
    * [Templates folder](https://github.com/MicrosoftDocs/community-content/tree/main/community-content/templates)
    * [Choose a template](https://github.com/MicrosoftDocs/community-content/blob/main/community-content/templates/content-type-comparison.md)
    * [Markdown reference](https://learn.microsoft.com/contribute/content/markdown-reference)
    * [Add code to articles](https://learn.microsoft.com/contribute/content/code-in-docs)
    * [Format text](https://learn.microsoft.com/contribute/content/text-formatting-guidelines)
1. [Commit and push changes](https://learn.microsoft.com/contribute/content/how-to-write-major-edits?tabs=terminal#commit-and-push-your-changes) to your fork.
1. If you need to [make more changes](https://learn.microsoft.com/contribute/content/how-to-write-major-edits?tabs=terminal#make-your-next-change), no problem! Keep iterating as needed.
1. Open a pull request:
    1. Navigate to your fork of this repository. (It will most likely be in the format of https://github.com/<YOUR_GITHUB_ACCOUNT>/community-content).
    1. Select **Pull requests**.
    1. Select **New pull request**.
       ![pull request tab](community-content/media/contribution/pull-request-tab.png)
    1. Ensure that the base repository is **MicrosoftDocs/community-content** and the **base** drop-down menu is **main**.
    1. Ensure that the head repository is your fork (**<YOUR_GITHUB_ACCOUNT>/community-content**) and the **base** drop-down menu is the branch with your proposed content.
       ![pr-request](community-content/media/contribution/pull-request-request.png)
    1. Select **Compare & pull request**.
    1. Give your PR a descriptive title and a description of what your content represents (for example, "new Azure Defender article with images").
    1. If you want to see what the article will look like, switch to the **Preview** view.
    1. Select **Create pull request**.
    1. The system will validate the PR against the build rules and output any technical corrections that need to be made. You **must** resolve any **warnings** or **errors**. If needed, you can commit more files and changes in your fork, and the PR will track any commits that you make.

## File structure

* **Article files ending in .md** belong in the [content](https://github.com/MicrosoftDocs/community-content/tree/main/community-content/content) folder.
* **Image files, such as .png or .jpg** belong in the [content/media](https://github.com/MicrosoftDocs/community-content/tree/main/community-content/content/media) folder, in a sub-folder where the images are used. For example, if your article is named **my-article.md**, you would create a folder within the media folder called **my-article**, and all your images would be stored in the **media/my-article** folder.
* Make sure to add your article as an entry to the top of the [TOC.yml](https://github.com/MicrosoftDocs/community-content/blob/main/community-content/content/TOC.yml) file, following the Markdown syntax of all the previous entries.

## Microsoft Learn contributor guide

Another resource to reference when contributing is the [Microsoft Learn contributor guide](https://learn.microsoft.com/contribute/content). This is our most comprehensive guide for contributing. The guide includes more detailed setup information, the full contribution workflow, style and voice guidelines, and other writing resources. Check it out!
