---
title: "Install new Python packages on SQL Server | Microsoft Docs"
ms.date: "01/04/2018"
ms.reviewer: 
ms.suite: sql
ms.prod: machine-learning-services
ms.prod_service: machine-learning-services
ms.component: python
ms.technology: 
  - "r-services"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 21456462-e58a-44c3-9d3a-68b4263575d7
caps.latest.revision: 1
author: "jeannt"
ms.author: "jeannt"
manager: "cgronlund"
ms.workload: "On Demand"
---
# Install new Python packages on SQL Server
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

This article describes how to install new Python packages on an instance of SQL Server 2017.

It also describes how to list packages that are installed in the current environment.

## Prerequisites

The process for installing new packages is much like that in a standard Python environment. However, some additional steps are required if the server does not have an internet connection.

+ You must have installed Machine Learning Services (In-Database) with the Python language option. For instructions, see [Set up Python Machine Learning Services](setup-python-machine-learning-services.md).

+ For each server instance, you must install a separate copy of the package. Packages cannot be shared across instances.

+ Determine whether the package you intend to use will work with Python 3.5 and in the Windows environment. 

    In general, there are few limitations on the packages that you can import and use in the SQL Server environment. Possible problems include packages that use networking functionality that is blocked on the server or by the firewall, or packages with dependencies that cannot be installed on a Windows computer.

+ Administrative access to the server is required to install packages.

## Add a new Python package

For this example, we assume that you want to install a new package directly on the SQL Server computer.

The package installed in this example is [CNTK](https://docs.microsoft.com/cognitive-toolkit/), a framework for deep learning from Microsoft that supports customization, training, and sharing of different types of neural networks.

> [!TIP]
> Need help configuring your Python tools? See these blogs:
> 
> [Getting Started with Python Web Services using Machine Learning Server](https://blogs.msdn.microsoft.com/mlserver/2017/12/13/getting-started-with-python-web-services-using-machine-learning-server/)
> 
> [David Crook: Microsoft Cognitive Toolkit + VS Code](http://dacrook.com/cntk-vs-code-awesome/)

### Step 1. Download the Windows version of the Python package

+ If you are installing Python packages on a server with no internet access, you must download the WHL file to a different computer and then copy it to the server.

    For example, on a separate computer, you can download the WHL file from this site [https://cntk.ai/PythonWheel/CPU-Only](https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl), and then copy the file `cntk-2.1-cp35-cp35m-win_amd64.whl` to a local folder on the SQL Server computer.

+ SQL Server 2017 uses Python 3.5. Be sure to get the Windows version of the package, and a version that is compatible with Python 3.5.

This page contains downloads for multiple platforms and for multiple Python versions: [Set up CNTK](https://docs.microsoft.com/cognitive-toolkit/Setup-CNTK-on-your-machine)

### Step 2. Open a Python command prompt

Locate the default Python library location used by SQL Server. If you have installed multiple instances, be sure to locate the PYTHON_SERVICE folder for the instance where you want to add the package.

For example, if Machine Learning Services has been installed using defaults, and machine learning is enabled on the default instance, the path would be as follows:

    `C:\Program Files\Microsoft SQL  Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES`

Open the Python command prompt associated with the instance.

> [!TIP]
> We recommend that you set up a Python environment specific to Machine Learning Server or SQL Server.

### Step 3. Install the package using pip

+ If you are accustomed to using the Python command line, use PIP.exe to install new packages. You can find the **pip** installer in the `Scripts` subfolder. 

    If you get an error that **pip** is not recognized as an internal or external command, you can add the path of the Python executable and the Python scripts folder to the PATH variable in Windows.

    The full path of the **Scripts** folder in a default installation is as follows:

    `C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\Scripts`

+ If you are using Visual Studio 2017, or Visual Studio 2015 with the Python extensions, you can run `pip install` from the **Python Environments** window. Click **Packages**, and in the text box, provide the name or location of the package to install. You don't need to type `pip install`; it is filled in for you automatically. 

    - If the computer has Internet access, provide the name of the package, or the URL of a specific package and version. For example, to install the version of CNTK that is supported for Windows and Python 3.5, you coudl specify the download URL: `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl`

    - If the computer does not have internet access, you should have downloaded the WHL file in advance. Then, specify the local file path and name. For example, paste the following path and file to install the WHL file downloaded from the site: 
    `"C:\Downloads\CNTK\cntk-2.1-cp35-cp35m-win_amd64.whl"`

You might be prompted to elevate permissions to complete the install.

As installation progresses, you can see status messages in the command prompt window:

```python
pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
Collecting cntk==2.1 from https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
  Downloading https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl (34.1MB)
    100% |################################| 34.1MB 13kB/s
...
Installing collected packages: cntk
Successfully installed cntk-2.1
```



### Step 4. Load the package or its functions as part of your script

When installation is complete, you can immediately begin using the package as described in the next step.

For examples of deep learning using CNTK, see these tutorials: [Python API for CNTK](https://cntk.ai/pythondocs/tutorials.html)

To use functions from the package in your script, insert the standard `import <package_name>` statement in the initial lines of the script:

```python
import numpy as np
import cntk as cntk
cntk._version_
```

##  How to view installed packages using conda

There are different ways that you can get a list of installed packages. For example, you can view the installed packages in the **Python Environments** windows of Visual Studio.

If you are using the Python command line, you can use the **conda** package manager, which is included with the Anaconda Python environment added by SQL Server setup.

To view Python packages that have been installed in the current environment, run this command from the command prompt windows:

```python
conda list
```

For more information about **conda** and how you can use it to create and manage multiple Python environments, see [Managing environments with conda](https://conda.io/docs/user-guide/tasks/manage-environments.html).
