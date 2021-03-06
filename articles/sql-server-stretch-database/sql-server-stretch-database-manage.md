<properties
	pageTitle="Stretch Database 的管理和疑難排解 | Microsoft Azure"
	description="了解如何針對 Stretch Database 進行管理和疑難排解"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# Stretch Database 的管理和疑難排解

若要針對 Stretch Database 進行管理和疑難排解，請使用本主題中描述的工具和方法。

## 管理本機資料

### <a name="LocalInfo"></a>取得已啟用 Stretch Database 功能之本機資料庫和資料表的相關資訊
開啟目錄檢視 **sys.databases** 和 **sys.tables** 以查看已啟用延展功能之 SQL Server 資料庫和資料表的相關資訊。如需詳細資訊，請參閱 [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) 和 [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)。

若要查看已啟用延展功能之資料表在 SQL Server 中使用多少空間，請執行下列陳述式。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## 管理資料移轉

### 查看套用至資料表的篩選函數
開啟目錄檢視 **sys.remote\_data\_archive\_tables** 並查看 **filter\_predicate** 資料行的值，以識別 Stretch Database 用來選取要移轉之資料列的函數。如果值為 Null，便代表整個資料表皆符合移轉資格。如需詳細資訊，請參閱 [sys.remote\_data\_archive\_tables (TRANSACT-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) 和[使用篩選函數來選取要移轉的資料列](sql-server-stretch-database-predicate-function.md)。

### <a name="Migration"></a>查看資料移轉狀態
在 SQL Server Management Studio 中，針對資料庫選取 [工作 | Stretch | 監視]，以在 Stretch Database 監視器中監視資料移轉。如需詳細資訊，請參閱[資料移轉的監視及疑難排解 (Stretch Database)](sql-server-stretch-database-monitor.md)。

或是開啟動態管理檢視 **sys.dm\_db\_rda\_migration\_status**，以查看已移轉的批次和資料列數量。

### <a name="Firewall"></a>資料移轉的疑難排解
如需疑難排解的建議，請參閱[資料移轉的監視及疑難排解 (Stretch Database)](sql-server-stretch-database-monitor.md)。

## 管理遠端資料

### <a name="RemoteInfo"></a>取得 Stretch Database 所使用的遠端資料庫和資料表相關資訊
開啟目錄檢視 **sys.remote\_data\_archive\_databases** 和 **sys.remote\_data\_archive\_tables**，以查看儲存已移轉資料的遠端資料庫和資料表相關資訊。如需詳細資訊，請參閱 [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) 和 [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

若要查看已啟用延展功能的資料表在 Azure 中使用了多少空間，請執行下列陳述式。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### 刪除已移轉的資料  
如果您想要刪除已移轉到 Azure 的資料，請依照 [sys.sp\_rda\_reconcile\_batch](https://msdn.microsoft.com/library/mt707768.aspx) 中所述的步驟操作。

## 管理資料表結構描述

### 請勿變更遠端資料表的結構描述
請勿變更和已設定 Stretch Database 的 SQL Server 資料表相關聯之遠端 Azure 資料表的結構描述。特別是，請勿修改資料欄的名稱或資料類型。Stretch Database 功能會做出各種有關遠端資料表的結構描述與 SQL Server 資料表的結構描述之間關係的假設。如果您變更遠端結構描述，Stretch Database 針對已變更資料表將會停止運作。

### 將資料表資料行重新同步化  
如果您不小心刪除了遠端資料表中的資料行，請執行 **sp\_rda\_reconcile\_columns**，將存在於已啟用延展功能的 SQL Server 資料表中但不存在於遠端資料表中的資料行，新增到遠端資料表中。如需詳細資訊，請參閱 [sys.sp\_rda\_reconcile\_columns](https://msdn.microsoft.com/library/mt707765.aspx)。

  > [!IMPORTANT] 當 **sp\_rda\_reconcile\_columns** 重新建立您不小心從遠端資料表中刪除的資料行時，並不會還原先前在已刪除資料行中的資料。

**sp\_rda\_reconcile\_columns** 不會從遠端資料表中，刪除存在於遠端資料表中但不存在於已啟用延展功能的 SQL Server 資料表中的資料行。如果遠端 Azure 資料表中有已不存在於已啟用延展功能之 SQL Server 資料表中的資料行，這些額外的資料行並不會導致 Stretch Database 無法正常運作。您可以視需要手動移除額外的資料行。

## 管理效能與成本  

### 針對查詢效能進行疑難排解
相較於尚未啟用 Stretch 的資料表，包含已啟用 Stretch 之資料表的查詢速度一定會更為緩慢。如果查詢效能大幅降低，請檢閱下列可能的問題。

-   您的 Azure 伺服器所在的地理區域是否和您的 SQL Server 不同？ 將您的 Azure 伺服器設定成位於和 SQL Server 相同的地理區域將可以減少網路延遲。

-   您的網路狀況可能已降級。請連絡您的網路系統管理員，以取得最新問題或中斷情形的資訊。

### 針對耗用資源的操作 (例如編製索引)，請提升 Azure 效能等級。
當您在針對 Stretch Database 設定的大型資料表上建立、重建或重組索引，並且預期在此期間於 Azure 中會有針對所移轉資料的大量查詢時，請考慮在此作業持續期間，提升對應之遠端 Azure 資料庫的效能等級。如需有關效能等級和定價的詳細資訊，請參閱 [SQL Server Stretch Database 定價](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

### 您無法暫停 Azure 上的 SQL Server Stretch Database 服務  
 請確定您選取適當的效能和定價等級。如果您針對需要大量資源的作業暫時提升效能等級，請在作業完成之後，將它還原到先前的等級。如需效能等級和定價的詳細資訊，請參閱 [SQL Server Stretch Database 定價](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

## 變更查詢的範圍  
 針對已啟用延展功能的資料表所進行的查詢預設既會傳回本機資料，也會傳回遠端資料。您可以針對所有使用者的所有查詢，或只針對系統管理員的單一查詢，變更查詢範圍。

### 變更所有使用者之所有查詢的查詢範圍  
 若要變更所有使用者的所有查詢範圍，請執行 **sys.sp\_rda\_set\_query\_mode** 預存程序。您可以將範圍縮減到只查詢本機資料、停用所有查詢，或是還原預設設定。如需詳細資訊，請參閱 [sys.sp\_rda\_set\_query\_mode](https://msdn.microsoft.com/library/mt703715.aspx)。

### <a name="queryHints"></a>變更系統管理員進行單一查詢的查詢範圍  
 若要變更 db\_owner 角色成員的單一查詢範圍，請在 SELECT 陳述式中新增 **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** 查詢提示。REMOTE\_DATA\_ARCHIVE\_OVERRIDE 查詢提示可以有下列值。
 -   **LOCAL\_ONLY**。只查詢本機資料。

 -   **REMOTE\_ONLY**。只查詢遠端資料。

 -   **STAGE\_ONLY**。只查詢 Stretch Database 暫存資料表中的資料，Stretch Database 會在此資料表中暫存符合移轉資格的資料列，並在移轉後依指定的期間保留已移轉的資料列。此查詢提示是查詢暫存資料表的唯一方法。

例如，下列查詢只會傳回本機結果。

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>進行系統管理更新和刪除  
 根據預設，您無法「更新」或「刪除」已啟用延展功能之資料表中符合移轉資格的資料列，或已移轉的資料列。當您必須修正問題時，db\_owner 角色的成員可以藉由在陳述式中新增 **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** 查詢提示來執行「更新」或「刪除」作業。REMOTE\_DATA\_ARCHIVE\_OVERRIDE 查詢提示可以有下列值。
 -   **LOCAL\_ONLY**。只更新或刪除本機資料。

 -   **REMOTE\_ONLY**。只更新或刪除遠端資料。

 -   **STAGE\_ONLY**。只更新或刪除 Stretch Database 暫存資料表中的資料，Stretch Database 會在此資料表中暫存符合移轉資格的資料列，並在移轉後依指定的期間保留已移轉的資料列。

## 另請參閱

[監視 Stretch Database](sql-server-stretch-database-monitor.md)

[備份已啟用延展功能的資料庫](sql-server-stretch-database-backup.md)

[還原已啟用延展功能的資料庫](sql-server-stretch-database-restore.md)

<!---HONumber=AcomDC_0629_2016-->