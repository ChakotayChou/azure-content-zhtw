<properties
   pageTitle="適用於 Azure SQL 資料倉儲的驗證 | Microsoft Azure"
   description="適用於 Azure SQL 資料倉儲的 Azure Active Directory (AAD) 與 SQL Server 驗證。"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/15/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# 適用於 Azure SQL 資料倉儲的驗證

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-connect-overview.md)
- [驗證](sql-data-warehouse-authentication.md)
- [驅動程式](sql-data-warehouse-connection-strings.md)

若要連線到 SQL 資料倉儲，您必須傳入安全性認證進行驗證用途。建立連線時，會設定特定的連線設定，以做為建立查詢工作階段的一部分。

如需安全性以及如何啟用您資料倉儲連線的詳細資訊，請參閱[保護 SQL 資料倉儲中的資料庫][]。

## SQL 驗證
若要連線到 SQL 資料倉儲，您必須提供下列資訊：

- 完整伺服器名稱
- 指定 SQL 驗證
- 使用者名稱
- 密碼
- 預設資料庫 (選擇性)

根據預設，您的連線會連線至主要資料庫，而非您的使用者資料庫。若要連線到您的使用者資料庫，您可以選擇執行下列兩個動作之一：

- 使用伺服器註冊 SSDT、SSMS 或應用程式連接字串中的 SQL Server 物件總管時，請指定預設資料庫。例如，藉由包含 ODBC 連線的 InitialCatalog 參數。
- 在 SSDT 中建立工作階段之前先反白顯示使用者資料庫。

> [AZURE.NOTE] 如需使用 SSDT 連線到 SQL 資料倉儲的指引，請參閱[使用 Visual Studio 查詢][]文章。

也請務必注意，變更連線的資料庫時，Transact-SQL 陳述式 **USE <您的資料庫>** 不受支援


## Azure Active Directory (AAD) 驗證

[Azure Active Directory][What is Azure Active Directory] 驗證是在 Azure Active Directory (Azure AD) 中使用身分識別連線到 Microsoft Azure SQL 資料倉儲的機制。您可以使用 Azure Active Directory 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。中央識別碼管理提供單一位置以管理 SQL 資料倉儲使用者並簡化權限管理。

### 優點

包括以下優點：

- 它提供 SQL Server 驗證的替代方案。
- 協助停止跨資料庫伺服器使用過多的使用者身分識別。
- 允許在單一位置的密碼替換
- 客戶可以管理使用外部 (AAD) 群組的資料庫權限。
- 它可以藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
- Azure Active Directory 驗證會使用自主資料庫使用者，在資料庫層級驗證身分。
- Azure Active Directory 針對連線到 SQL 資料倉儲的應用程式支援權杖型驗證。
- 設定 Azure Active Directory 驗證時，SQL Server Management Studio 會透過 Active Directory 通用驗證支援 Multi-Factor Authentication。如需 Multi-Factor Authentication 的說明，請參閱[適用於 Azure AD MFA 與 SQL Database 和 SQL 資料倉儲的 SSMS 支援](../sql-database/sql-database-ssms-mfa-authentication.md)。


### 組態步驟

設定步驟包括以下設定和使用 Azure Active Directory 驗證的程序。

1. 建立和填入 Azure Active Directory
2. 選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更
3. 建立 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員。
4. 設定用戶端電腦
5. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者
6. 使用 Azure AD 身分識別連接到您的資料倉儲。

Azure Active Directory 使用者目前不會顯示在 SSDT 物件總管中。解決方法是在 [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中檢視使用者。
  
### 尋找詳細資料
- 完成詳細的步驟。針對 Azure SQL Database 及針對 Azure SQL 資料倉儲設定並使用 Azure Active Directory 驗證的詳細步驟幾乎完全相同。請依照[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](../sql-database/sql-database-aad-authentication.md)主題中的詳細步驟操作。
- 建立自訂資料庫角色，並加入使用者至角色。然後授與角色細微的權限。如需詳細資訊，請參閱[資料庫引擎權限使用者入門](https://msdn.microsoft.com/library/mt667986.aspx)。

## 後續步驟

若要透過 Visual Studio 和其他應用程式開始查詢您的資料倉儲，請參閱[使用 Visual Studio 查詢][]。

<!-- Article references -->
[保護 SQL 資料倉儲中的資料庫]: ./sql-data-warehouse-overview-manage-security.md
[使用 Visual Studio 查詢]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md

<!---HONumber=AcomDC_0817_2016-->