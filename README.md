# 📝 口罩辨識分類模型完整流程（Google Colab）

## 步驟 1：掛載 Google 雲端硬碟 + 變更執行階段(GPU)

說明：

<img width="437" height="179" alt="1" src="https://github.com/user-attachments/assets/33e5066c-e322-48e7-8da6-2c8aa4eef39d" />

- 將你的 Google 雲端硬碟掛載到 Colab，方便存取資料集與儲存模型。

- 執行後會跳出授權網址，登入後複製授權碼貼回即可。

- 掛載後，雲端硬碟路徑會出現在 /content/drive。

<img width="713" height="635" alt="2" src="https://github.com/user-attachments/assets/da628a43-ae88-4735-a66b-735eebe737bb" />

- Colab → Runtime（執行階段）→ Change runtime type → 選 GPU（建議訓練時開啟，使用完改回 None 以節省配額）。

## 步驟 2：設定工作目錄（可存在雲端硬碟中）

我們先建立一個資料夾，專門存放資料集與訓練內容。

<img width="582" height="332" alt="3" src="https://github.com/user-attachments/assets/c9c1c4e1-d06a-4e47-a869-b01da2c11eaa" />

## 步驟 3：下載口罩資料集（使用 GitHub 公開鏡像）

**這一步會直接從 GitHub 下載一個開源「戴口罩 / 未戴口罩」人臉資料集。
（來源：https://github.com/prajnasb/observations）**

<img width="800" height="280" alt="4" src="https://github.com/user-attachments/assets/6aaec59c-0fd2-4019-b1ee-c6d39809b3b2" />

## 步驟 4：確認資料是否下載成功

<img width="389" height="140" alt="5" src="https://github.com/user-attachments/assets/fa31ff57-944a-4258-b006-bf58df83c3db" />

## 步驟 5：分割資料集（訓練 / 測試）

之後要訓練模型，我們可以把它分成訓練與測試資料夾：

<img width="969" height="393" alt="6" src="https://github.com/user-attachments/assets/eb6928a4-09dc-41ef-8939-31bfd2589d1b" />

再確認一次目前路徑

<img width="686" height="276" alt="7" src="https://github.com/user-attachments/assets/4b8bfa7d-3866-4974-9c60-438ef5b66ab4" />

## 步驟 6：下載 YOLOv5 原始碼與安裝依賴

<img width="1591" height="651" alt="8" src="https://github.com/user-attachments/assets/553a8e45-3b04-460a-b437-d6116cb0f254" />

## 步驟 7：建立 YOLOv5 的資料設定檔

這個檔案會告訴 YOLOv5：

- 類別有哪些

- 訓練與驗證資料在哪裡

<img width="809" height="554" alt="9" src="https://github.com/user-attachments/assets/8a8b48d9-5fdd-4204-9a70-51cee498f390" />

## 步驟 8：設定專案路徑

說明：

- base_dir：存放專案所有檔案（模型、程式、資料集）的主要資料夾。

- data_dir：存放訓練與驗證資料集。

- 如果資料夾不存在，os.makedirs 會自動建立。

<img width="726" height="234" alt="10" src="https://github.com/user-attachments/assets/424f1f7d-bbab-4539-8a69-a6dc58d17fd6" />

## 步驟 9：匯入必要套件並建立資料載入器

說明：

- ImageFolder 自動將資料夾名稱作為標籤。
- train_loader / val_loader 用於批次讀取圖片，方便訓練與驗證。
- transform 將圖片轉成神經網路可用的張量（tensor），並做標準化。

<img width="936" height="684" alt="11" src="https://github.com/user-attachments/assets/e70d3ba5-7586-4edd-a2ce-2db8b5405afc" />

## 步驟 10：建立模型（使用 ResNet18）

說明：

- 使用 ResNet18 預訓練模型加速收斂。
- 修改最後一層為 2，對應兩個類別：with_mask 和 without_mask。
- 模型移到 GPU（如果可用）加速訓練。

<img width="1805" height="408" alt="12" src="https://github.com/user-attachments/assets/d7b2f90a-d9b9-4505-9a55-35f3d8cce6cf" />

## 步驟 11：設定損失函數與優化器

說明：

- CrossEntropyLoss 適用於多類別分類
- Adam 優化器學習率設定為 0.0001，可依需要調整。

<img width="641" height="84" alt="13" src="https://github.com/user-attachments/assets/86dadedf-3467-407c-8df3-036669d249b7" />

## 步驟 12：訓練模型

說明：

- 每個 epoch 先訓練再驗證。
- 每個 batch 更新模型參數。
- 驗證時計算正確率，觀察模型表現。

<img width="1536" height="741" alt="14" src="https://github.com/user-attachments/assets/8c5dc037-86f3-4d15-bf8b-17c7a436ca83" />

***輸出結果***

<img width="614" height="210" alt="15" src="https://github.com/user-attachments/assets/e78d20f1-70cd-439f-88c8-acfbddafc8eb" />

## 步驟 13：畫出訓練曲線

說明：
- 可以觀察損失隨 epoch 下降的情況，判斷模型是否收斂。
 
<img width="670" height="602" alt="16" src="https://github.com/user-attachments/assets/cb667203-891a-4ed0-9844-de10e4095312" />

## 步驟 14：測試模型（單張圖片預測）

<img width="714" height="374" alt="17" src="https://github.com/user-attachments/assets/a89b8f7f-5aa9-4334-85cb-9d0a94e459b3" />

***預測結果:***

<img width="326" height="437" alt="18" src="https://github.com/user-attachments/assets/b6cfeefa-ea6f-4ffa-8ca5-800b705b9a8c" />

## 步驟 15(非必要)：隨機顯示五張圖片預測結果

說明：

- 隨機挑五張驗證資料，依序預測並顯示圖片。
- 標題顯示「真實標籤」與「模型預測結果」。
- 一次可以快速檢視模型準確性。

<img width="626" height="699" alt="19" src="https://github.com/user-attachments/assets/d14f34fd-55bc-4b0c-b005-b865647ea140" />

***輸出結果:***
<img width="1651" height="490" alt="20" src="https://github.com/user-attachments/assets/bd484be8-8278-4760-ac73-a5d9046a9832" />

<img width="1632" height="498" alt="21" src="https://github.com/user-attachments/assets/b914d2d8-b1fe-422b-aefb-4d9dd3ccea5b" />

可以看到準確率很高!
