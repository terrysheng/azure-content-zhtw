<properties
	pageTitle="Unity Roll a Ball 教學課程"
	description="建立傳統 Unity Roll a Ball 遊戲的步驟，此遊戲是所有 Mobile Engagement Unity 教學課程的必要條件"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>建立 Unity Roll a Ball 遊戲

本教學課程逐步引導您完成稍微修改過的 [Unity Roll a Ball 教學課程](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)的主要步驟。此範例遊戲由 app 使用者控制的球面「玩家」物件所組成，遊戲的目標是使用這些可收集的物件碰撞玩家物件來「收集」可收集的物件。這是假設使用者對 Unity 編輯器環境有基本的熟悉度。如果您遇到任何問題，應該參閱完整的教學課程。

### 設定遊戲
下列步驟來自 [Unity 教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. 開啟 **Unity Editor**，然後按一下 **New**。 
	
	![][51]
	
2. 提供 **Project name** 和 **Location**、選取 **3D**，然後按一下 **Create project**。
	
	![][52]

3. 使用名稱 **MiniGame**，將剛建立的預設場景當做新專案的一部分，儲存在 **Assets** 資料夾底下的新 **\_Scenes** 資料夾內︰
 	
	![][53]

4. 建立 **3D Object -> Plane** 做為遊戲場，然後將這個平面物件重新命名為 **Ground**

	![][1]

5. 重設此 **Ground** 物件的轉換元件，使其位於原點。

	![][3]

6. 從 **Ground** 物件的 **Gizmos 功能表**取消核取 **Show Grid**。

	![][4]

7. 將 **Ground** 物件的 **Scale** 元件更新為 [X = 2,Y = 1, Z = 2]。

	![][5]

8. 將新的 **3D Object -> Sphere** 加入至專案，並將此球形物件重新命名為 **Player**。

	![][6]

9. 選取 **Player** 物件，然後按一下類似於 Plane 物件的 **Reset Transform**。

10. 將玩家 Y 的 **Transform -> Position -> Y Coordinate** 元件更新為 0.5。

	![][7]

11. 在我們將建立材料以便為玩家著色所在的專案中，建立一個稱為 **Materials** 的新資料夾。

12. 在此資料夾中建立一個稱為 **Background** 的新 **Material**。

	![][8]

13. 更新材料的 **Albedo** 屬性，以更新材料的色彩。您可以選取 RGB 值 [0,32,64]。

	![][9]

14. 將此材料拖曳到場景檢視，以便將色彩套用至 **Ground** 物件。

	![][10]

17. 最後，為清楚起見，將 Directional Light 物件的 **Transform -> Rotation -> Y** 更新為 60。

	![][12]

### 移動玩家
下列步驟來自 [Unity 教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. 將 **RigidBody** 元件新增至 **Player** 物件。 

	![][13]

2. 在專案中建立一個稱為 **Scripts** 的新資料夾。

3. 按一下 **Add Component-> New Script -> C# Script**。將其命名為 **PlayerController**，然後按一下 **Create and Add**。這將會建立一個指令碼，並將其附加至 Player 物件。

	![][14]

5. 將此指令碼移到專案的 **Scripts** 資料夾底下。

6. 開啟要在您最愛的指令碼編輯器中編輯的指令碼、將指令碼更新為下列程式碼並加以儲存。

		using UnityEngine;
		using System.Collections;
		
		public class PlayerController : MonoBehaviour 
		{
			public float speed;
			private Rigidbody rb;
			void Start ()
			{
			    rb = GetComponent<Rigidbody>();
			}
			void FixedUpdate ()
			{
			    float moveHorizontal = Input.GetAxis ("Horizontal");
			    float moveVertical = Input.GetAxis ("Vertical");
				Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
				rb.AddForce (movement * speed);
			}
		}
	
8. 請注意，上述指令碼會使用 **Speed** 屬性。在 Unity Editor 中，將 speed 屬性更新為 10。

	![][15]

9. 按 Unity Editor 中的 **Play**。現在您應該能夠使用鍵盤控制球，而且球應該會旋轉並四處移動。

### 移動相機
下列步驟來自 [Unity 教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141)，而且會將 **Main Camera** 繫結至 **Player** 物件。

1. 將 **Transform.Position** 更新為 X = 0、Y = 10.5、Z =-10。  
2. 將 **Transform.Rotation** 更新為 X = 45、Y = 0、Z = 0。  

	![][16]

2. 將稱為 **CameraController** 的新指令碼加入至 **MainCamera**，並將其移動到 Scripts 資料夾底下。

	![][17]

3. 開啟指令碼進行編輯，並在其中加入下列程式碼︰

		using UnityEngine;
		using System.Collections;
		
		public class CameraController : MonoBehaviour {
		
		    public GameObject player;
		
		    private Vector3 offset;
		
		    void Start ()
		    {
		        offset = transform.position - player.transform.position;
		    }
		    
		    void LateUpdate ()
		    {
		        transform.position = player.transform.position + offset;
		    }
		}
	
5. 在 Unity 環境中，將 Player 變數拖曳到 Main Camera 物件的 Player 位置，使兩者相互關聯。

	![][18]

6. 現在如果您按 Unity Editor 中的 Play 並旋轉 Player Ball 物件，您將會看到 Camera 在該物件後面移動。

### 設定遊戲區域
下列步驟來自 [Unity 教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141)。我們將在 Ground 周圍建立 Walls，讓 Player Ball 物件不會在移動時掉出遊戲區域。

1. 按一下 **Create -> Create Empty -> Game Object**，並將它命名 **Walls**

	![][19]

2. 在此 Walls 物件底下，建立新的 **3D Object -> Cube**，並將它命名為 "West wall"。

	![][20]

3. 更新此 West Wall 物件的 **Transform -> Position** 和 **Transform -> Scale**。

	![][21]

4. 複製 West wall，以更新的轉換位置和比例建立 **East wall**。

	![][22]

5. 複製 East wall，以更新的轉換位置和比例建立 **North wall**。

	![][23]

6. 複製 North wall，以更新的轉換位置和比例建立 **South wall**。

	![][24]

### 建立可收集的物件
下列步驟來自 [Unity 教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141)。我們將會建立一些外觀吸引人的物件，這些物件會形成一組可收集的物件，Player Ball 物件必須透過碰撞這組可收集的物件來「收集」它們。

1. 建立新 **3D Cube** 物件，並將它命名為 Pickup。 

2. 調整 Pickup 物件的 **Transform -> Rotation** 和 **Transform -> Scale**。

	![][25]

3. 建立一個稱為 **Rotator** 的**新 C# 指令碼**，並將其附加至 Pickup 物件。請務必將指令碼放在 Scripts 資料夾底下。

	![][26]

4. 開啟此指令碼進行編輯，並將其更新如下：

		using UnityEngine;
		using System.Collections;
		
		public class Rotator : MonoBehaviour {
		
		    void Update () 
		    {
		        transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
		    }
		}

5. 現在按 Unity Editor 中的 Play 模式，您的 Pickup 物件應該就會在其軸上旋轉。

6. 建立一個稱為 **Prefabs** 的新資料夾

	![][27]

7. 拖曳 **Pickup** 物件，並將其放在 Prefabs 資料夾中。

	![][28]

8. 建立一個稱為 **Pickups** 的新 **Empty Game** 物件。將其位置重設為原點，然後將 Pickup 物件拖曳到此遊戲物件底下。

	![][29]

9. 複製 **Pickup** 物件，並藉由適當地更新 **Transform.Position** 的 X 和 Z 值，將其散佈在 **Player** 物件周圍的 **Ground** 物件上。

	![][30]

10. 建立一個稱為 **Pickup** 的**新材料**，並藉由更新 **Albedo** 屬性 (類似我們更新 Ground 物件的方式)，將其更新為紅色。

	![][31]

11. 將材料套用至全部 4 個 Pickup 物件。

	![][32]

### 收集 Pickup 物件
下列步驟來自 [Unity 教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141)。我們將更新 Player，使其能夠透過碰撞的方式，「收集」Pickup 物件。

1. 開啟已附加至 Player 物件的 **PlayerController** 指令碼進行編輯，並將其更新為：  

		using UnityEngine;
		using System.Collections;
		
		public class PlayerController : MonoBehaviour {
		
		    public float speed;
		
		    private Rigidbody rb;
		
		    void Start ()
		    {
		        rb = GetComponent<Rigidbody>();
		    }
		
		    void FixedUpdate ()
		    {
		        float moveHorizontal = Input.GetAxis ("Horizontal");
		        float moveVertical = Input.GetAxis ("Vertical");
		
		        Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
		
		        rb.AddForce (movement * speed);
		    }
		
		    void OnTriggerEnter(Collider other) 
		    {
		        if (other.gameObject.CompareTag ("Pick Up"))
		        {
		            other.gameObject.SetActive (false);
		        }
		    }
		}

2. 建立一個稱為 **Pick Up** 的新 **Tag** (它必須符合指令碼中的內容)

	![][33]
	
	![][34]

3. 將此 **Tag** 套用至 Prefab Pickup 物件。

	![][35]

4. 為 Prefab 物件啟用 **IsTrigger** 核取方塊。

	![][36]

5. 將 Rigid body 新增至 Pickup Prefab 物件。為將效能最佳化，我們將會更新我們用於 Dynamic Collider 的 Static Collider。

	![][37]
  
6. 最後，檢查 Prefab 物件的 **IsKinematic** 屬性。

	![][38]

7. 按 Unity Editor 中的 **Play**，您就能夠透過使用鍵盤按鍵當做方向輸入移動 Player 物件來玩這個 **Roll a Ball** 遊戲。

### 更新遊戲以便在行動裝置上玩遊戲
以上幾節就是 Unity 的基本教學課程。現在，我們將修改遊戲，使其成為行動裝置上的遊戲。請注意，到目前為止我們使用鍵盤輸入來測試遊戲。現在，我們將修改遊戲，讓我們可以使用手機的動作控制玩家，亦即，使用加速計做為輸入。

開啟 **PlayerController** 指令碼進行編輯，並更新 **FixedUpdate** 方法，以使用加速計的動作移動 Player 物件。

	    void FixedUpdate()
	    {
	        //float moveHorizontal = Input.GetAxis("Horizontal");
	        //float moveVertical = Input.GetAxis("Vertical");
	        //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
	        rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
	    }

本教學課程使用 Unity 建立基本遊戲的部分到此結束，您可以在您選擇要玩遊戲的裝置上部署此遊戲。

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

	
	
	
	
	
	
	
	
	
	
	
	

<!---HONumber=AcomDC_0330_2016-->