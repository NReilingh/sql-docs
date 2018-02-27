---
title: "Release Notes (ODBC Driver for SQL Server) | Microsoft Docs"
ms.custom: ""
ms.date: "02/14/2018"
ms.prod: "sql-non-specified"
ms.prod_service: "drivers"
ms.service: ""
ms.component: "odbc"
ms.reviewer: ""
ms.suite: "sql"
ms.technology: 
  - "drivers"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: b8459ed8-625e-4d8b-891c-e7e78c9977cc
caps.latest.revision: 7
author: "MightyPen"
ms.author: "genemi"
manager: "jhubbard"
---
# Release Notes
[!INCLUDE[Driver_ODBC_Download](../../../includes/driver_odbc_download.md)]

  Release Notes for Microsoft ODBC Driver for SQL Server on Windows.  

## What's New in the [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 17 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion_md.md)] on Windows

**Features Added**:

Always Encrypted support for BCP API

New connection string attribute UseFMTOnly causes driver to use legacy metadata in special cases requiring temp tables.

Support for Azure SQL Managed Instance (Extended Private Preview). 
> [!NOTE]
> There are a number of differences when using Managed Instance:
> -   FILESTREAM is not supported 
> -   Local filesystem access is not supported, but required for things like tracefiles 
> -   Create UDT from local path is not supported 
> -   Windows Integrated Authentication is not supported 
> -   DTC is not supported 
> -   'sa' account is not present (default account is called 'cloudSA')
> -   TDS token ERROR (0xAA) returns incorrect server name
> -   Special characters in database name are not supported 
> -   ALTER DATABASE [dbname1] MODIFY NAME = [dbname2] is not supported
> -   The error messages are always shown in English, regardless of language settings (same as Azure) 
  

## What's New in the [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 13.1 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion_md.md)] on Windows  
 ODBC Driver 13.1 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion_md.md)] adds support for [Always Encrypted](../../../connect/odbc/using-always-encrypted-with-the-odbc-driver.md) and [Azure Active Directory](../../../connect/odbc/using-azure-active-directory.md) when used in conjunction with Microsoft SQL Server 2016.  Corresponding connection pooling keywords/attributes are described in [Driver Aware Connection Pooling in the ODBC Driver for SQL Server](../../../connect/odbc/windows/driver-aware-connection-pooling-in-the-odbc-driver-for-sql-server.md).

 ## What's New in the [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 13 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion_md.md)] on Windows  
 ODBC Driver 13 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion_md.md)] includes previous functionality from ODBC Driver 11 for SQL Server and adds support for Microsoft SQL Server 2016.

## What's New in the [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 11 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion_md.md)] on Windows  
 ODBC Driver 11 for SQL Server contains new [features](./features-of-the-microsoft-odbc-driver-for-sql-server-on-windows.md) as well as all the features that shipped with ODBC in SQL Server 2012 Native Client.  
