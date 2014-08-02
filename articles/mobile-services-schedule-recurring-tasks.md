<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

在行動服務中為週期性工作排程
============================

本主題將示範如何在管理入口網站中使用工作排程器功能，以根據您所定義的排程來，從而定義要執行的伺服器指令碼。在此情況下，指令碼會定期向遠端服務 (在此案例中為 Twitter) 查詢，並將結果儲存在新資料表中。可排定的其他一些定期工作包括：

-   封存老舊或重複的資料記錄。
-   要求或儲存外部資料，例如推文、RSS 項目和位置資訊。
-   處理或調整儲存影像的大小。

本教學課程將逐步引導您完成下列步驟，以瞭解如何使用工作排程器，來建立向 Twitter 要求推文資料並在新的 Updates 資料表中儲存推文的排定工作：

-   [註冊以取得 Twitter 存取權與儲存認證](#get-oauth-credentials)
-   [建立新的 Updates 資料表](#create-table)
-   [建立新的排定工作](#add-job)

註冊以取得 Twitter v1.1 API 的存取權與儲存認證
----------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

建立新的 Updates 資料表
-----------------------

接下來，您必須建立要儲存推文的新資料表。

1.  在管理入口網站中，按一下行動服務的 **[資料]** 索引標籤，然後按一下 **[建立]**。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png)

   	這將顯示 [建立新資料表] 對話方塊。

2.  在 **[資料表名稱]** 中輸入 *Updates*，然後按一下核取按鈕。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png)

  	這會建立新的儲存資料表 **Updates**。

建立新的排定工作
----------------

現在，您可以建立可存取 Twitter 並將推文資料儲存於全新 Updates 資料表中的排定工作。

1.  按一下 **[排程器]** 索引標籤，然後按一下 **[建立]**。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png)

    >[WACOM.NOTE]當您在*免費*層中執行行動服務時，您一次只能執行一個排定工作。在付費層中，您一次可以執行多達十個排定工作。

2.  在排程器對話方塊的 **[工作名稱]** 中輸入 *getUpdates*、設定排程間隔和單位，然後按一下核取按鈕。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png)

   	這會建立名為 [getUpdates] 的新工作。

3.  按一下您剛建立的新工作，然後按一下 **[指令碼]** 索引標籤。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png) 

4.  使用下列程式碼取代 **getUpdates** 預留位置功能：

		var updatesTable = tables.getTable('Updates');
		var request = require('request');
		var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

		// 取得服務組態模組。
		var config = require('mobileservice-config');
            
		// 取得儲存的 Twitter 取用者金鑰和密碼。 
		var consumerKey = config.twitterConsumerKey,
			consumerSecret = config.twitterConsumerSecret
		// 從應用程式設定取得 Twitter 存取權杖。    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
			accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
            
		function getUpdates() {   
			// 檢查上次執行工作時儲存的最後推文
			// 並要求 Twitter 只提供最新的推文
			appendLastTweetId(
				twitterUrl, 
				function twitterUrlReady(url){            
					// 使用 OAuth 認證建立新的要求。
					request.get({
						url:url,                
						oauth: {
							consumer_key:consumerKey,
							consumer_secret:consumerSecret,
							token:accessToken,
							token_secret:accessTokenSecret
						}},
						function (error, response, body) {
						if (!error && response.statusCode == 200) {
							var results = JSON.parse(body).statuses;
							if(results){
								console.log('Fetched ' + results.length + ' new results from Twitter');                       
								results.forEach(function (tweet){
									if(!filterOutTweet(tweet)){
										var update = {
											twitterId:tweet.id,
											text:tweet.text,
											author:tweet.user.screen_name,
											date:tweet.created_at
										};
										updatesTable.insert(update);
									}
								});
							}            
						} else { 
							console.error('Could not contact Twitter');
						}
					});
            
				});
		}
		// 找出已儲存的最大 (最新) 推文 ID
		// (如果已儲存的話)，並要求 Twitter 只傳回最
		// 新的推文
		function appendLastTweetId(url, callback){
			updatesTable
			.orderByDescending('twitterId')
			.read({ success:function readUpdates(updates){
				if(updates.length){
					callback(url + '&since_id=' + (updates[0].twitterId + 1));           
				} else {
					callback(url);
				}
			}});
		}

		function filterOutTweet(tweet){
			// 移除回推和回覆
			return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
		}


   	此指令碼會使用儲存的認證來呼叫 Twitter 查詢 API，以要求包含雜湊標記 `#mobileservices` 的最新推文。在重複的推文和回覆被儲存於資料表之前，系統會先將它們從結果中移除。

    > [WACOM.NOTE]本範例會假設每次排定執行期間，資料表只會插入幾個資料列。倘若一個迴圈會在免費層上執行插入許多資料列，則您可能會沒有足夠的連線。在此情況下，您應該以批次方式執行插入。如需詳細資訊，請參閱[作法：執行大量插入](/en-us/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts)。

5.  按一下 **[執行一次]** 以測試指令碼。

  	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png)

   	當工作在排程器中維持停用狀態時，這麼做會儲存並執行工作。

1.  按 [上一步] 按鈕，再依序按一下 **[資料]**、**[Updates]** 資料表、**[瀏覽]**，並驗證 Twitter 資料是否已插入資料表中。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png)

2.  按 [上一步] 按鈕，再按一下 **[排程器]**、選取 **[getUpdates]**，然後按一下 **[啟用]**。

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png)

   	這會讓工作在指定的排程上執行，在此案例中為每個小時。

恭喜！您已順利地在行動服務上建立新的排定工作。系統會依排程執行此工作，直到您停用或修改此工作為止。

後續步驟
--------

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>深入了解註冊與使用伺服器指令碼。
