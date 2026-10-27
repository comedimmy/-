## 第一頁
大家好，我是吳易軒。今天我要向大家報告的主題是「即時道路損害偵測：方法與資料集的全面性文獻綜述」。這份報告主要回顧近年在車載與邊緣端運算中，電腦視覺如何應用於道路缺陷辨識的最新技術發展。
Good morning, everyone. My name is Yi-Xuan Wu. Today, I will be presenting on the topic: "Real-Time Road Damage Detection: A Comprehensive Survey of Methodologies and Datasets." In this presentation, we will walk through the recent advancements in computer vision applied to road defect detection, especially in vehicular and edge-computing environments.

## 第二頁
今天的報告主要分為七個部分。首先，我會介紹這項研究的背景與動機，以及目前遭遇的核心挑戰；接著會檢視主流的道路損害資料集及其局限性；隨後，我們會深入比較 One-stage 與 Two-stage 的模型架構方法；第四與第五部分會探討 YOLO 演進史與注意力機制的引入；最後，我會統整關鍵的研究發現，並提出未來的研究展望與結論。
Here is the outline of my presentation today.

## 第三頁
首先看到背景與動機。道路表面缺陷，像是坑洞或各類裂縫，不僅容易損壞車輛，更是導致重大交通事故的元凶。例如在馬來西亞，光是 2019 年就有超過 36 萬起相關交通事故。
隨著先進駕駛輔助系統（也就是 ADAS）的普及，即時辨識這些路面危險，對於提供及時預警甚至觸發自動煞車來說至關重要。然而，目前的技術缺口在於：我們該如何在複雜變化的天候光影下，準確辨識微小且不規則的路面損壞，同時維持車載系統所必需的「即時推論速度」。
so Let's look at the background and motivation first. Road defects are major contributors to vehicle wear-and-tear and severe traffic accidents. For instance, in Malaysia alone, over 364K accidents were recorded in 2019.
And then As Advanced Driver-Assistance Systems—or ADAS—become mainstream, detecting these roadway hazards in real-time is crucial for enabling early driver warnings and automated emergency braking. However, the key research gap remains: how can we accurately detect small, irregular road damages under dynamic lighting conditions, while maintaining the high inference speed required for real-time safety?

## 第四頁
具體來說，即時道路損害偵測面臨三大核心難題：
第一是「精準度與速度的權衡」。車載 ADAS 必須具備高影格率（也就是高 FPS）來處理連續影像，而且推論速度不能以犧牲偵測準確率為代價。
第二是「環境變異」。例如陽光直射、樹蔭陰影，或是夜間雨天等極端氣候，都會嚴重干擾特徵提取，降低模型的信心度。
第三是「小物件偵測」。遠處的坑洞或細小的橫縱向裂縫，在影像中往往只佔極少數像素，很容易淹沒在複雜粗糙的路面背景紋理中。
Specifically, real-time road damage detection faces three fundamental challenges:
First, Accuracy versus Speed. Real-time ADAS requires high-FPS video processing without compromising the precision and reliability of the detection.
Second, Environmental Variance. Harsh lighting shifts, tree shadows, rain, or nighttime conditions drastically lower the model's confidence.
Third, Small Object Detection. Subtle cracks and distant potholes occupy very few pixels, making them easily lost in noisy asphalt textures and complex road backgrounds.

## 第五頁
為了訓練與評估模型，學界公開了多個基準資料集，但它們各自存在明顯的局限。
最具代表性的是 RDD 系列。從 2018 年的 9,000 多張影像，擴展到 2020 年跨三國的 2 萬多張，再到 2022 年跨六國的 4 萬 7 千多張。雖然資料規模不斷擴大，但「資料類別嚴重不平衡」依舊存在，特別是危險度最高的坑洞照片數量遠少於裂縫，且資料大多偏向晴天白天的場景。
另外還有無人機視角的 UAV-PDD2023，但它缺乏車載視角；而斯洛伐克等區域性資料集則通常只涵蓋單一損害類別。
值得一提的是，目前只有 RDD 系列對人臉和車牌進行了模糊去識別化，其他多數資料集都還缺乏隱私保護措施。
To train and benchmark these models, several public datasets have been developed, though each comes with certain limitations.
The most prominent is the Road Damage Dataset (RDD) series. It evolved from RDD2018 with around 9,000 images, to RDD2020 with 21,000 images across three countries, and RDD2022 featuring over 47,000 images across six countries. Despite this growth, severe class imbalance remains a hurdle—particularly, hazardous potholes are heavily underrepresented compared to cracks. Furthermore, there is a clear daylight bias.
Other datasets like UAV-PDD2023 offer high-resolution drone views but lack car-dashboard angles, while regional sets like Slovakia Potholes focus strictly on a single defect type.
Lastly, it is worth noting that only the RDD benchmark provides privacy protection by blurring faces and license plates, which is absent in most other collections.

## 第六頁
了解資料集之後，我們需要合適的量化指標來衡量模型的好壞。
第一個關鍵指標是 F1-Score。如同前面提到的，路面資料集存在嚴重的正負樣本不平衡，正常柏油路面佔了絕大多數，而缺陷只佔極小部分。如果我們一味拉高信心度閾值，精準度（Precision）雖然會提升，但往往會漏抓許多真正的損壞，導致召回率（Recall）大幅下降。因此，F1-Score 作為 Precision 和 Recall 的調和平均數，能提供一個客觀、不受資料傾斜干擾的綜合評估。
第二個是 mAP（平均精確率），它用來全面評估模型在所有損害類別下的整體偵測與定位能力。
第三個則是 FPS（每秒傳輸幀數），這代表推論速度，是檢驗演算法是否能在車載或邊緣端硬體上實現「即時偵測」的最直接標準。
Moving on to Slide 6, we examine the core evaluation metrics used to assess model performance.
The first key metric is the F1-Score. As mentioned earlier, road damage datasets suffer from severe class imbalance—normal road surfaces vastly outnumber defect areas. Setting a high confidence threshold might boost Precision, but it causes the model to miss subtle defects, lowering Recall. The F1-Score acts as the harmonic mean between the two, offering an unbiased and balanced evaluation.
Next is mAP (Mean Average Precision), which serves as the gold standard for measuring comprehensive detection accuracy across all defect categories.
Finally, we have FPS (Frames Per Second), which measures inference latency to ensure the architecture can run in true real-time on vehicular edge devices.

## 第七頁
接著進入方法學架構的比較。在深度學習物件偵測中，主要可分為兩大流派：One-stage 與 Two-stage。
先看最右邊的 Two-stage 架構（以 Faster R-CNN 為代表）。它會先透過一個區域候選網路（RPN）在特徵圖上生成可能的候選框，接著再進行精細的特徵萃取與分類回歸。這種兩階段分工讓它的邊界框定位非常精準，但多階段計算也導致運算負擔極大。
相比之下，左邊的 One-stage 架構（如 SSD 與 YOLO）則是回歸型模型。它不需要額外的候選框生成步驟，而是在單次前向傳播中，直接從整張影像同時預測出邊界框位置、置信度以及類別機率。
下方的整理表格清楚點出兩者的核心差異：Two-stage 精準度高但推論過慢，通常無法用於即時車載；而 One-stage 具備推論快速、輕量化的特性，更適合即時系統部署。
Now, let's examine the methodology comparisons on Slide 7. In modern object detection, models are broadly divided into two paradigms: one-stage and two-stage detectors.
On the right, we have Two-Stage Detectors, exemplified by Faster R-CNN. It utilizes a Region Proposal Network (RPN) to suggest potential bounding boxes first, followed by feature pooling and refinement. While this separation grants exceptional localization accuracy, it creates a heavy computational bottleneck.
In contrast, One-Stage Detectors—such as SSD and YOLO on the left—treat detection as a straightforward regression problem. They skip the proposal generation and predict bounding box coordinates, confidence scores, and class probabilities simultaneously in a single forward pass.
As summarized in the bottom table, while two-stage models excel in precision, their complexity makes them impractical for real-time safety. One-stage models offer the lightweight efficiency necessary for vehicular deployment.

## 第八頁
在了解架構概念後，第 8 頁我們直接看這兩大流派在標準公開資料集上的實證數據。
請大家看到左下方 Pascal VOC 2007 的數據表：Faster R-CNN 的 mAP 確實達到 73% 到 76% 的高水準，但它的推論速度只有 5 到 7 FPS，這在高速行駛的車輛上是完全無法接受的。
反觀 YOLO 系列，YOLOv2 在相近甚至更好的準確率下，推論速度能飆升到 40 到 91 FPS，完全超越即時門檻。
再看到右邊 COCO 資料集的對比，SSD 雖然速度不錯，但對小物件特徵的捕捉較弱；而 YOLOv3 則展現了極佳的多尺度特徵平衡。因此本篇 Survey 的核心結論之一就是：YOLO 系列在精準度與即時性之間取得最理想的折衷平衡，是車載 ADAS 的首選架構。
Turning to Slide 8, here are the empirical benchmarks supporting this comparison.
If you look at the Pascal VOC 2007 table on the left, Faster R-CNN achieves impressive mAP scores between 73% and 76%, but its inference speed hovers at merely 5 to 7 FPS. This creates an intolerable latency for moving vehicles.
In stark contrast, YOLOv2 achieves comparable detection accuracy while surging to speeds between 40 and 91 FPS, comfortably exceeding real-time requirements.
Looking at the COCO benchmark on the right, while SSD performs decently, it struggles with fine-grained small objects, whereas YOLOv3 balances multi-scale features much more effectively. Thus, the empirical takeaway is clear: the YOLO family delivers the optimal trade-off between speed and accuracy for ADAS deployment.

## 第九頁
第 9 頁我們深入看一下 YOLO 是如何具體定位路面損害的。
YOLO 將輸入影像劃分成網格單元（Grid cells）。當道路缺陷的中心點落在某個網格內時，該網格就負責預測這個目標。
請看右側的標籤向量結構：$P_c$ 代表該格是否包含物件（0 代表純路面，1 代表有缺陷）；接著是邊界框的中心座標 $(B_x, B_y)$ 以及長寬尺寸 $(B_w, B_h)$；最後則是四種標準損害類別的 One-hot 標籤：D00 代表縱向裂縫、D10 代表橫向裂縫、D20 代表龜裂（或稱地圖狀裂縫），D40 則是坑洞。
從早期依賴預設 Anchor Boxes 到近年導入 Anchor-Free 與路徑聚合網路（PAN），YOLO 不斷強化多尺度特徵融合能力，即使在 200 FPS 以上的超高速推論下，依然能精確鎖定這些微小的損壞。
On Slide 9, we take a closer look at how YOLO interprets road damage through its output tensor.
YOLO divides the input image into a grid of cells. If the center of a road defect falls into a specific grid, that cell is responsible for detecting it.
As shown in the label structure on the right: $P_c$ indicates object existence—0 for normal asphalt and 1 for damage. This is followed by the normalized center coordinates $(B_x, B_y)$ and bounding box dimensions $(B_w, B_h)$. Finally, we have the class probabilities corresponding to standard road damage definitions: D00 for longitudinal cracks, D10 for transverse cracks, D20 for alligator cracking, and D40 for potholes.
By evolving from rigid anchor boxes to anchor-free heads and Path Aggregation Networks (PAN), modern YOLO architectures excel at capturing multi-scale defects while maintaining processing speeds well above 200 FPS. 

## 第十頁
來到第 10 頁，我們綜觀 YOLO 家族從早期到最新架構的技術演進與效能躍升。
從早期 YOLOv3 採用 Darknet-53，mAP 僅有約 33% 且只有 20 FPS，到中期的 YOLOv5 與 YOLOv8，透過引入動態 Anchor、Mosaic 資料增強、C2F 以及 SPPF 特徵金字塔模組，mAP 突破了 50%，推論速度更大幅攀升至數百 FPS。
到了近期的 YOLOv10、v11 與 v12，架構進一步導入空間與通道解耦、C3k2/C2PSA 區塊，以及 RepVGG 式的結構重參數化與自適應標籤分配。可以看到 YOLOv12m 在僅約 20M 的參數量下，達到了 52.5% 的 mAP 與超過 200 FPS 的表現，為低延遲的邊緣端車載偵測奠定了扎實基礎。
Moving to Slide 10, we observe the evolutionary leap across the YOLO family architectures.
Starting from YOLOv3 with Darknet-53, which yielded an mAP of 33% at 20 FPS, intermediate models like YOLOv5 and YOLOv8 introduced dynamic anchors, Mosaic data augmentation, C2F modules, and SPPF pooling layers, pushing mAP past 50% while scaling frame rates to hundreds of FPS.
In recent releases—namely YOLOv10, v11, and v12—the designs leverage rank-guided spatial-channel decoupling, C3k2 and C2PSA blocks, and RepVGG-style re-parameterization with adaptive label assignment. As a result, YOLOv12m achieves an mAP of 52.5% at 206 FPS with only 20.2 million parameters, providing a robust baseline for low-latency automotive edge deployment.

## 第十一頁
儘管 YOLO 速度飛快，但前面提過，道路瑕疵通常極微小，容易被背景路面紋理淹沒。為了解決這個瓶頸，近年研究大量結合了「注意力機制」。
常見的兩種模組包括：
第一是 CBAM（卷積塊注意力模組），它沿著通道與空間兩個維度依序推導注意力圖，能主動強化關鍵缺陷特徵。
第二是 Coordinate Attention（座標注意力），它將空間資訊分解為水平與垂直兩個一維特徵進行追蹤，在幾乎不增加運算負擔的前提下保留精確的空間位置資訊。
這類注意力機制的整體效益在於能有效抑制粗糙瀝青的背景噪聲，讓原本輕巧的 One-stage 偵測器在細小裂縫上的檢出率大幅提升。
Turning to Slide 11, we address the challenge of subtle, small defect identification by integrating Attention Mechanisms.
Two widely adopted modules in recent literature are:
First, CBAM (Convolutional Block Attention Module), which sequentially infers attention maps along channel and spatial dimensions to emphasize critical defect regions.
Second, Coordinate Attention (CA), which captures direction-aware and position-sensitive information across horizontal and vertical axes without introducing significant computational overhead.
The overall impact is that these modules suppress complex asphalt background noise, empowering lightweight one-stage detectors to capture tiny cracks with high precision.

## 第十二頁
這些架構改良在真實道路場景中表現如何？請看第 12 頁 CRDDC 2022 國際競賽的實際評測結果。
傳統上，精準度最高的是龐大的整合模型（Ensemble），例如 Faster R-CNN 搭配 Swin Transformer 與 Deformable ROI，它的 F1-score 達到 77%，但這種多模型集成運算極其龐大，根本無法在車載端即時運行。
然而，預訓練的 YOLOv5x 達到了 74.3% 的 F1-score，而引入座標注意力模組的 YOLOv7 + CA 更是達到了 74.1%。
這項數據直接證明：單一 YOLO 模型只要整合輕量注意力機制，就能在維持 ADAS 所需的即時高影格率下，達到逼近多模型整合架構的精準度。
On Slide 12, we validate these improvements using official competition results from the Crowdsensing-based Road Damage Detection Challenge (CRDDC 2022).
Top accuracy was achieved by complex Ensemble methods—such as combining Faster R-CNN with Swin Transformer and Deformable ROI—yielding an F1-score of 77%. However, such heavy ensembles are computationally prohibitive for in-vehicle execution.
In contrast, pre-trained YOLOv5x achieved a 74.3% F1-score, while YOLOv7 integrated with Coordinate Attention achieved 74.1%.
This demonstrates that a single YOLO variant augmented with lightweight attention can achieve near-ensemble precision while strictly preserving the real-time frame rates required by ADAS.

## 第十三頁
最後來到第 13 頁的總結與未來展望。
左半邊是本文獻綜述的核心結論：
第一是架構選型定論（Architecture Verdict）——整合輕量注意力機制的單階段 YOLO 系列，在偵測精準度與推論速度間取得了最佳平衡，是目前車載 ADAS 最實用的選擇。
第二是資料集現況（Dataset Status）——雖然 RDD 推動了領域發展，但嚴重的類別不平衡（特別是坑洞影像稀缺）以及缺乏夜間與雨天標註，仍是當前演算法落地的關鍵瓶頸。   針對這些瓶頸，右半邊提出了三大未來研究方向：
首先是「環境強健性」，可透過天候模擬資料擴增技術、HDR 影像以及紅外線相機來克服極端光照與天候惡化。
其次是「多感測器融合」，將單純的光學相機與光達（LiDAR）、毫米波雷達及熱感應器結合，建立具備三維空間感知的防護網。
最後是「損傷嚴重度分級」，研究不應僅停留在二元偵測，更需邁向嚴重程度的精細評估，才能進一步支援動態車輛避障與養護通報排程。
Finally, Slide 13 presents our Conclusion and Future Directions.
On the left, we conclude our survey findings:
First, regarding our Architecture Verdict, single-stage YOLO models integrated with lightweight attention mechanisms offer the optimal trade-off between localization precision and high-speed throughput for advancing ADAS.
Second, on Dataset Status, while benchmarks like RDD have accelerated progress, critical limitations remain, specifically severe class imbalance (the scarcity of potholes) and a shortage of adverse-weather annotations.   To address these gaps, three future directions are outlined on the right:
First, Environmental Robustness, which can be tackled through weather-simulation data augmentation, HDR imaging, and infrared vision.
Second, Multi-Sensor Fusion, combining optical cameras with LiDAR, radar, and thermal sensors to achieve resilient 3D spatial awareness.
Third, transitioning toward Damage Severity Classification, moving beyond binary detection into multi-level risk ranking to inform dynamic vehicle avoidance maneuvers and prioritized road maintenance.   That concludes my presentation. 
