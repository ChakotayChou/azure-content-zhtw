<properties
	pageTitle="使用 Mahout 與以 Linux 為基礎的 HDInsight 產生推薦 | Microsoft Azure"
	description="了解如何搭配 Linux 架構的 HDInsight (Hadoop) 使用 Apache Mahout 機器學習庫來產生電影推薦。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="larryfr"/>

#在 HDInsight 上搭配使用 Apache Mahout 和以 Linux 為基礎的 Hadoop 來產生電影推薦

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

了解如何使用搭配 Azure HDInsight 的 [Apache Mahout](http://mahout.apache.org) 機器學習庫產生電影推薦。

Mahout 是 Apache Hadoop 的[機器學習][ml]庫。Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。在本文中，您將使用推薦引擎，其將根據朋友看過的電影來產生電影推薦。

> [AZURE.NOTE] 本文件中的步驟需要 HDInsight 叢集上有以 Linux 為基礎的 Hadoop。如需搭配使用 Mahout 與 Windows 叢集的資訊，請參閱[透過在 HDInsight 上將 Apache Mahout 與 Windows 架構的 Hadoop 搭配使用來產生電影推薦](hdinsight-mahout.md)

##必要條件

* HDInsight 叢集上以 Linux 為基礎的 Hadoop。如需有關建立此叢集的資訊，請參閱[開始在 HDInsight 中使用 Linux 架構的 Hadoop][getstarted]。

##Mahout 版本控制

如需關於 HDInsight 叢集隨附的 Mahout 版本詳細資訊，請參閱 [HDInsight 版本和 Hadoop 元件](hdinsight-component-versioning.md)。

> [AZURE.WARNING] 雖然可以將 Mahout 的不同版本上傳至 HDInsight 叢集，但只有 HDInsight 叢集隨附的元件可獲得完整支援，且 Microsoft 支援服務將助您釐清及解決與這些元件相關的問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。例如，有許多社群網站可以使用，像是：[HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/zh-TW/home?forum=hdinsight)、[http://stackoverflow.com](http://stackoverflow.com)。另外，Apache 專案在 [http://apache.org](http://apache.org) 上有專案網站，例如 [Hadoop](http://hadoop.apache.org/)、[Spark](http://spark.apache.org/)。

##<a name="recommendations"></a>了解推薦

Mahout 提供的其中一項功能是推薦引擎。這個引擎接受 `userID``itemId` 和 `prefValue` (使用者偏好的項目) 格式的資料。Mahout 接著可以執行共生分析判斷出：_偏好某項目的使用者同時也偏好其他這些項目_。接著 Mahout 會以偏好的類似項目判斷使用者，並以此做出推薦。

以下使用電影做一個很簡單的範例：

* __共生__：Joe、Alice 和 Bob 都喜歡_《星際大戰》_、_《帝國大反擊》_和_《絕地大反攻》_。Mahout 將判斷喜歡上述任何一部電影的使用者，也會喜歡另外兩部電影。

* __共生__：Bob 和 Alice 同時也喜歡_《威脅潛伏》_、_《複製人全面進攻》_和_《西斯大帝的復仇》_。Mahout 將判斷喜歡前三部電影的使用者，也會喜歡這三部電影。

* __相似性推薦__：因為 Joe 喜歡前三部電影，Mahout 會查看具有相似偏好的其他使用者所喜歡但 Joe 還沒看過 (喜歡/評價) 的電影。在此情況下，Mahout 將會推薦_《威脅潛伏》_、_《複製人全面進攻》_和_《西斯大帝的復仇》_。

###了解資料

[GroupLens 研究][movielens] 提供與 Mahout 相容之格式的電影評價資料，相當方便。您可在位於 `/HdiSamples/HdiSamples/MahoutMovieData` 的叢集預設儲存體取得這份資料。

有兩份檔案：`moviedb.txt` (影片相關資訊) 和 `user-ratings.txt`。分析期間使用的是 user-ratings.txt 檔案，moviedb.txt 則是在顯示分析結果時用來提供使用者易懂的文字資訊。

user-ratings.txt 內包含的資料具有 `userID`、`movieID`、`userRating` 和 `timestamp` 結構，可告訴我們每位使用者對於影片的評價為何。以下是資料範例：


    196	242	3	881250949
    186	302	3	891717742
    22	377	1	878887116
    244	51	2	880606923
    166	346	1	886397596

##執行分析

使用下列命令來執行推薦工作：

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] 此工作可能需要幾分鐘的時間才能完成，並可能執行多項 MapReduce 工作。

##檢視輸出

1. 工作完成後，使用以下命令來檢視所產生的輸出：

		hdfs dfs -text /example/data/mahoutout/part-r-00000

	輸出將如下所示：

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	第一欄是`userID`。 '[' 和 ']' 內含的值是 `movieId`:`recommendationScore`

2. 您可以使用輸出和 moviedb.txt，顯示更多使用者易懂的詳細資訊。首先，我們需要使用下列命令將檔案複製到本機上︰

		hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

	這會將輸出資料和影片資料檔一起複製到目前目錄中名為 **recommendations.txt** 的檔案上。

3. 使用下列命令來建立新的 Python 指令碼，該指令碼將會在建議輸出資料中查閱電影名稱：

		nano show_recommendations.py

	開啟編輯器時，請使用下列做為檔案的內容：

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

	按下 **CTRL-X**、**Y**，最後再按 **Enter** 鍵儲存資料。

3. 使用下列命令，讓檔案可以執行：

		chmod +x show_recommendations.py

4. 執行 Python 指令碼。以下假設您已在所有檔案的下載目錄中︰

		./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

	這要看看為使用者 ID 4 所產生的建議。

	* **user-ratings.txt** 檔案用來擷取使用者已評分的影片
	* **moviedb.txt** 檔案用來擷取影片名稱
	* **recommendations.txt** 用來擷取這位使用者的電影建議

	此命令的輸出會如下所示：

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
		Mimic (1997), rating=3
		Ulee's Gold (1997), rating=5
		Incognito (1997), rating=5
		One Flew Over the Cuckoo's Nest (1975), rating=4
		Event Horizon (1997), rating=4
		Client, The (1994), rating=3
		Liar Liar (1997), rating=5
		Scream (1996), rating=4
		Star Wars (1977), rating=5
		Wedding Singer, The (1998), rating=5
		Starship Troopers (1997), rating=4
		Air Force One (1997), rating=5
		Conspiracy Theory (1997), rating=3
		Contact (1997), rating=5
		Indiana Jones and the Last Crusade (1989), rating=3
		Desperate Measures (1998), rating=5
		Seven (Se7en) (1995), rating=4
		Cop Land (1997), rating=5
		Lost Highway (1997), rating=5
		Assignment, The (1997), rating=5
		Blues Brothers 2000 (1998), rating=5
		Spawn (1997), rating=2
		Wonderland (1997), rating=5
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##刪除暫存資料

Mahout 工作不會移除處理工作時所建立的暫存資料。範例工作中指定 `--tempDir` 參數將暫存檔隔離到特定路徑中以方便刪除。若要移除暫存檔案，請使用下列命令：

	hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] 如果您要再次執行該命令，您必須也刪除輸出目錄。使用以下命令刪除此目錄：
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## 後續步驟

您現在已了解如何使用 Mahout，請繼續探索在 HDInsight 上使用資料的其他方法：

* [搭配 HDInsight 使用 Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=AcomDC_0831_2016-->