# CloudCompareによるLiDAR遺構モデルの補正手順

前提  
- LiDARスキャン：Scaniverseで取得した点群またはメッシュ  
- 基準座標：トータルステーション（TS）で測量した遺構点（上端・下端など）  
- TSデータ形式：X,Y,Z のCSVまたはTXT  
---
## 1. LiDARデータを書き出す
1. Scaniverseから点群またはメッシュをエクスポートする。  
2. 推奨形式：PLY（点群処理が軽く安定するため）。
---
## 2. TS測量点を準備する
1. TSで遺構の測点を取得する。  
   - 上端：3点程度  
   - 下端：3点程度  
   - 遺構外地表：2点程度  

2. 座標を以下形式のCSVまたはTXTに整理する。
X,Y,Z
34567.123,12345.456,15.234
34567.553,12345.832,15.211
34568.120,12346.110,15.010
---
## 3. CloudCompareにデータを読み込む
1. メニュー  
   `File -> Open`  
   を選択する。
2. LiDAR点群ファイル（PLY等）を読み込む。
3. TS測量点を読み込む。  
   メニュー  
   `File -> Open`  
   でCSV/TXTを選択する。
4. ASCII読み込み設定  
   - Column assignment  
     - X → X  
     - Y → Y  
     - Z → Z  
   - Separator  
     - comma または space  
   - Validate を押す。
---
## 4. 粗位置合わせ（手動移動）
1. LiDAR点群を選択する。
2. メニュー  
   `Edit -> Translate/Rotate`
3. 設定
   - Translation  
     - TS点群の近くへ移動するように調整
   - Rotation  
     - 必要に応じて回転
4. 目標  
   - TS点群と **1m以内程度** に位置を近づける。
---
## 5. 対応点登録（Point Pair Registration）
1. メニュー  
   `Tools -> Registration -> Align (point pairs picking)`  
   を選択する。
2. 設定
   - Aligned entity  
     - LiDAR点群
   - Reference entity  
     - TS点群
3. 操作
   - LiDAR点群の測点位置をクリック  
   - 対応するTS測点をクリック  
   - この対応点操作を **3〜6点** 行う。
4. 実行  
   - `Align` を押す。
---
## 6. ICP補正
1. メニュー  
   `Tools -> Registration -> ICP`
2. 設定
   - Reference cloud  
     - TS点群
   - Aligned cloud  
     - LiDAR点群
   - Adjust scale  
     - OFF
   - Random sampling limit  
     - ON  
     - 値：50000〜100000
   - Max iterations  
     - 50
   - Final overlap  
     - 0.8〜1.0
3. 実行  
   - `OK` を押す。
---
## 7. 誤差確認
1. ICP結果ウィンドウで  
   - RMS error を確認する。
2. 目安
| RMS誤差 | 評価 |
|---|---|
| 1cm以下 | 非常に良い |
| 1〜3cm | 実務上問題なし |
| 5cm以上 | 再調整 |
---
## 8. Z方向誤差の確認
1. メニュー  
   `Tools -> Distances -> Cloud/Cloud Dist`
2. 設定
   - Compared cloud  
     - LiDAR点群
   - Reference cloud  
     - TS点群
3. 実行  
   - `Compute`
4. 高さズレ分布を確認する。
---
## 9. メッシュへの変換適用（メッシュがある場合）
1. メッシュを選択する。
2. メニュー  
   `Edit -> Apply Transformation`
3. ICPで得られた変換を適用する。
---
## 10. 保存
1. メニュー  
   `File -> Save`
2. 推奨形式
   - PLY（点群）
   - OBJ（メッシュ）
---
## 推奨測点配置（TS）
- 遺構上端：3点  
- 遺構下端：3点  
- 遺構外地表：2点  
合計  
**8点程度**
理由  
- XY拘束  
- Z拘束  
を同時に確保できるため。
