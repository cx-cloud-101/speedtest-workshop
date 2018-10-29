Installation Guide
==================

To participate in the workshop, you'll need the following tools and accounts for working with C#, Kotlin and Node on Azure and Google Cloud Platform.

General tools
-------------

### Git and GitHub
You'll need git installed and a GitHub account to complete both the Azure and GCP -part of the workshop.

1. Create a new GitHub account [here](https://github.com/join)
2. Download and install git following [these instructions](https://git-scm.com/downloads)
3. When git is installed, you should be able to [set your username](https://help.github.com/articles/setting-your-username-in-git/) and [commit email address](https://help.github.com/articles/setting-your-commit-email-address-in-git/). Use the same email that you registered at GitHub.

### Postman
You'll create at least one API, so it's nice to have a tool to test it. [Postman](https://www.getpostman.com/apps) is quite nice, and available on all platforms.

Azure and C#
------------
_To complete the Azure-part of the workshop, you'll need the following tools and accounts._

### .NET Core 2.1 SDK and VS Code
_If you already have .NET Core installed, you can check the version with `dotnet --version`._

1. Install the [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
2. If you don't already have a preferred editor for C# code, grab a copy of [VS Code](https://code.visualstudio.com/). You'll also want to get the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). Install it by pressing Ctrl+P, pasting `ext install ms-vscode.csharp` into the window and pressing enter.


### Azure free trial
If you don't have an active Azure account with available credits, the easiest option is to grab an [Azure free trial](https://azure.microsoft.com/nb-no/free) account. Just go through the registration process, verify your account, and at the end you should be able to visit the [Azure portal](https://portal.azure.com). You unfortunately have to register both a phone number and a debit or credit card, but Microsoft won't charge you if you don't upgrade the account.

_If you already have used the Azure free trial, then you'll have to register a new account with a new email. You can use the same phone number during the registration process._


Google Cloud Platform (GCP) and Kotlin
--------------------------------------
_To complete the GCP-part of the workshop, you'll need the following tools and accounts._

### Java Developer Kit, Gradle and IntelliJ IDEA

1. You'll need a recent version of the Java Developer Kit (JDK). [Oracle Java SE downloads](https://www.oracle.com/technetwork/java/javase/downloads/index.html) is usually a good place to start.
2. Gradle is used in the examples, so [install gradle as well](https://gradle.org/install/).
3. It you don't have a Java IDE and Spring Boot installed, you should download [IntelliJ IDEA Ultimate](https://www.jetbrains.com/idea/download/). It's not free, but it has a 30 day trial and includes Spring Boot.

### GCP Free trial

1. Activate your [free trial at GCP](https://console.cloud.google.com/freetrial)
2. Go through the registration process. At the end you should end up at [console.cloud.google.com](console.cloud.google.com/home/dashboard). You unfortunately have to register both a phone number and a debit or credit card, and Google will charge you if you exceed the spending limit of $300 (!), so it's important that you disable the project after finishing the workshop.

_If you already have used the GCP free trial, then you'll have to register a new account with a new email._

### Cloud SDK

1. Install the [Cloud SDK](https://cloud.google.com/sdk/)
2. During the installation process you'll initialize Cloud SDK with `gcloud init`. Remember to log on with the account that has the fri trial activated.

### Node v8 or later
_If you have node installed, you can check the version with `node --version`_

1. Install node from [nodejs.org](https://nodejs.org/en/download/)
