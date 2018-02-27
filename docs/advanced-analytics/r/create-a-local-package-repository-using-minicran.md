---
title: "Create a local package repository using miniCRAN | Microsoft Docs"
ms.custom: ""
ms.date: "01/04/2018"
ms.reviewer: 
ms.suite: sql
ms.prod: machine-learning-services
ms.prod_service: machine-learning-services
ms.component: r
ms.technology: 
  
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 27f2a1ce-316f-4347-b206-8a1b9eebe90b
caps.latest.revision: 4
author: "jeannt"
ms.author: "jeannt"
manager: "cgronlund"
ms.workload: "Inactive"
---
# Create a local package repository using miniCRAN
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

There are two ways that you can prepare R packages for installation onto a server without internet access.

-   [Use the miniCRAN package to create a single local repository](#bkmk_miniCRAN)

    The [miniCRAN](https://cran.r-project.org/web/packages/miniCRAN/index.html) creates an internally consistent repository consisting of selected packages from CRAN-like repositories. The user specifies a set of desired packages, and miniCRAN recursively reads the dependency tree for these packages, and downloads only the listed packages and their dependencies.

    You can then move this local repository to the server, and proceed to install the packages without using the internet.

-   [Manually download and copy packages one by one](#bkmk_manual)

    You can find the list of dependent packages in the DESCRIPTION file for the downloaded package. 
    
    However, packages listed in **Imports** might have second-level dependencies. For this reason, we recommend use of the **miniCRAN** method.

> [!TIP]
> Did you know that you can use miniCRAN to prepare packages for use in Azure Machine Learning? For more information, see this blog: [Using miniCRAN in Azure ML, by Michele Usuelli](https://www.r-bloggers.com/using-minicran-in-azure-ml/) 

## Prepare packages using miniCRAN

The goal of creating a local package repository is to provide a single location that a server administrator or other users in the organization can use to install new R packages on a server that does not have internet access.

The [miniCRAN](https://cran.r-project.org/web/packages/miniCRAN/index.html) package for R was written by [Andre de Vries](http://blog.revolutionanalytics.com/2016/05/minicran-sql-server.html) to make it easier to create a consistent, managed set of R packages for an organization. 

There are many advantages to using miniCRAN to create the repository:

-   **Security**: Many R users are accustomed to downloading and installing new R packages at will, from CRAN or one of its mirror sites. However, for security reasons, production servers running [!INCLUDE[ssNoVersion_md](..\..\includes\ssnoversion-md.md)] typically do not have internet connectivity.

-   **Easier offline installation**: To install package to an offline server requires that you also download all package dependencies, Using miniCRAN makes it easier to get all dependencies in the correct format.

-   **Improved version management**: In a multiuser environment, there are good reasons to avoid unrestricted installation of multiple package versions on the server.

After creating the repository, you can modify it by adding new packages or upgrading the version of existing packages.

> [!NOTE]
> The miniCRAN package itself is dependent on 18 other CRAN packages, among which is the RCurl package, which has a system dependency on the curl-devel package. Similarly, package XML has a dependency on libxml2-devel. We recommend, therefore, that you build your local repository initially on a machine with full Internet access, so that you can easily satisfy all these dependencies. After created, you can move the repository to a different location.

### Step 1. Install the miniCRAN package

You begin by creating a miniCRAN repository to use as a source. You should create this repository on a computer that has internet access.

1.  Install the miniCRAN package and the required **igraph** package.

    ```R
    if(!require("miniCRAN")) install.packages("miniCRAN") if(!require("igraph"))
    install.packages("igraph") library(miniCRAN)
    ```

### Step 2. Define a package source: a CRAN mirror, or an MRAN snapshot

1. Specify a mirror site to use in getting packages.

    ```R
    CRAN_mirror \<- c(CRAN = "https://mran.microsoft.com/snapshot/2017-08-01")
    ```

2.  Type the name of a local folder in which to store the collected packages. 

    Be sure to create the folder in advance. An error is raised if the `local_repo` folder does not exist when you run the R code later.

    The folder should have a descriptive name. For example, avoid using "miniCRAN", and instead type something like "GeneticsPackages" or "TeamRPackages1.0.2".

    ```R
    local_repo <- "~/miniCRAN"
    ```

    The tilde expansion operator returns an environment variable, with results equivalent to `Sys.getenv("R_USER")`.

### Step 3. Add packages to the repository

1.  After miniCRAN is installed, create a list that specifies the additional packages you want to download.

    Do not add dependencies to this initial list; the **igraph** package used by miniCRAN generates the list of dependencies for you. For more information about how to use the generated dependency graph, see [Using miniCRAN to identify package
    dependencies](https://cran.r-project.org/web/packages/miniCRAN/vignettes/miniCRAN-dependency-graph.html).

    The following R script demonstrates how to get the target packages, "zoo"
    and "forecast".

    ```R
    pkgs_needed <- c("zoo", "forecast")
    ```
2. It is not required that you plot the dependency graph, but it can be informative.
    
    ```R
    plot(makeDepGraph(pkgs_needed))
    ```

3. Create the local repo. Be sure to change the R version if necessary

    ```R
    pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror)
    makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "win.binary", Rversion = "3.3")
    ```

    From this information, the miniCRAN package creates the folder structure that you need to copy the packages to the [!INCLUDE[ssNoVersion_md](..\..\includes\ssnoversion-md.md)] later.

4. At this point you should have a folder containing the packages you needed, and any additional packages that were required.

    You can run the following code to list the packages contained in the miniCRAN repository.

    ```R
    pdb <- as.data.frame(pkgAvail(local_repo, type = "win.binary", Rversion = "3.3"), stringsAsFactors = FALSE)
    head(pdb)
    pdb$Package
    pdb[, c("Package", "Version", "License")]
    ```

### Step 4. Use the repository to add R packages to the instance library

After you have created the repository and added the packages you need, you must move the package repository to the server computer, and ensure that the R packages are installed in the correct library for use from SQL Server.

Depending on the version of SQL Server, there are two options for adding new packages to the R library associated with the SQL Server instance:

- Install to the instance library using the miniCRAN repository and R tools.

- Upload packages to a SQL Server database and install using the CREATE EXTERNAL LIBRARY statement. This option requires SQL Server 2017. See [Install additional R packages on SQL Server](install-additional-r-packages-on-sql-server.md).

The following procedure describes how to install the packages using R tools.

1. Copy the folder containing the miniCRAN repository, in its entirety, to the server where you plan to install the packages.

2. Open an R command prompt using the R tool associated with the instance.

    - For SQL Server 2017, the default folder is `C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER/R_SERVICES/library`.

    - For SQL Server 2016, the default folder is `C:/Program Files/Microsoft SQL Server/MSSQL13.MSSQLSERVER/R_SERVICES/library`.

    - For a named instance, the default path would be something like: `<instance_path>.RTEST/R_SERVICES/library`.

    -  If you have installed SQL Server to a different drive, or made any other changes in the installation path, be sure to make those changes as well.

3.  Get the path for the instance library, and add it to the list of library paths.

    ```R
    .libPaths()[1]  
    lib \<- .libPaths()[1]
    ```

    On SQL Server, this command should return the path of the library associated with the instance, such as: "C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER/R_SERVICES/library "

4.  Specify the location on the server where you copied the mininCRAN repository in `server_repo`.

    In this example, we assume that you copied the repository to your user folder on the server.

    ```R
    R server_repo <- "C:\\Users\\MyUserName\\miniCRAN"
    ```

5.  Since you are working in a new R workspace on the server, you must also furnish the list of packages to install.

    ```R
    tspackages <- c("zoo", "forecast")
    ```

6.  Install the packages, providing the path to the local copy of the miniCRAN repo.

    ```R
    install.packages(tspackages, repos = file.path("file://", normalizePath(server_repo, winslash = "/")), lib = lib, type = "win.binary", dependencies = TRUE)
    ```

7.  From the instance library, you can view the installed packages using a command like the following:

    ```R
    installed.packages()
    ```

> [!NOTE] 
> In SQL Server, a server administrator must install packages from the miniCRAN repository into the default library used by the instance. 

## Manually download single packages

If you do not want to use miniCRAN, you can also manually download the packages you need, and their dependencies. To do this requires that you are either an administrator or sole owner of a server.

After downloading the packages, you install the R packages from the zipped file location.

1. Download the packages zip files, and save them in a local folder

2. Copy that folder to the [!INCLUDE[ssNoVersion_md](..\..\includes\ssnoversion-md.md)] computer.

3. Install the packages into the SQL Server instance library.

> [!NOTE]
> When you use R tools to install packages, they are installed for the instance as a whole. 
> 
> If you want to install the package into a database and share the package with users using database roles, you must upload the library using the CREATE EXTERNAL LIBRARY statement. See [Install additional R packages in SQL Server](install-additional-r-packages-on-sql-server.md)
