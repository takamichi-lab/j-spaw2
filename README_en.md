# J-SPAW2: A Japanese Speech Corpus for Analyzing Recording Conditions inReplay Attacks
[日本語版はこちら](https://github.com/takamichi-lab/j-spaw2/blob/main/README.md)

The J-SpAW2 (pronounced j-spou2) corpus is designed for speaker verification and anti-spoofing verification, containing both bona fide speech and spoofing attacks.

## Download speech files
[Link](https://tmpuc.box.com/s/nwlq5wwposnbwx0oxp6bwvjxi003kenr) (zip, 9.0 GB)

## Contents
This repository provides a set of speech files, lists for evaluating speaker verification (ASV), and various protocols including lists and metadata for evaluating logical attack (LA) and physical attack (PA) tasks in spoofing detection. The contents are as follows:

- `jspaw2/`：Directory of speech files (download from the above URL and use them)
    - `ASV/*.wav`：Speaker verification (ASV) <!-- (The same audio file as [J-SPAW](https://github.com/takamichi-lab/j-spaw)) -->
    - `LA/L3/*.wav`：Spoofing detection (LA task) (Spoofed speech by CosyVoice2 [1])
    - `LA/L4/*.wav`：Spoofing detection (LA task) (Spoofed speech by ElevenLabs [2])
    - `LA/L5/*.wav`：Spoofing detection (LA task) (Spoofed speech by VALL-E X [3])
    - `PA/*.wav`：Spoofing detection (PA task)
- `protocols/`
    - `ASV_eval.txt`：Trial list for ASV task (7,600 genuine pairs, 30,000 impostor pairs)
    - `PA_eval.txt`：List for spoofing detection (800 bona fide files, 32,000 spoofed files)
    - `ASV_PA_eval.txt`：Trial list for ASV between bona fide and spoofed speech (7,600 genuine pairs, 30,000 impostor pairs, 640,000 pairs of genuine bona fide speech and spoofed speech)
    - `metadata_PA.txt`：Metadata of spoofed speech for PA task (800 bona fide files, 32,000 spoofed files)
    - `ASV_LA/`
        - `ASV_eval_LA_L{3,4,5}.txt`: Trial list for ASV (includes spoofed speech; 7,600 genuine pairs, 30,000 impostor pairs, 16,000 pairs of genuine bona fide speech and spoofed speech)※
    - `CM_LA/`
        - `metadata_LA_L{3,4,5}.txt` : Metadata of spoofed speech for LA task (800 bona fide files, 800 spoofed files)
    - `tDCF_LA/`
        - `asv/`
            - `L{3,4,5}_ASV,txt`: ASV score list for calculating tDCF (LA)
        - `cm/`
            - `L{3,4,5}_CM,txt`: Spoofing detection score list for calculating tDCF (LA)
    - `config.py`：Configuration file to apply the ASVspoof2021 eval-package to this database
    - `README_protocols`：[https://github.com/takamichi-lab/j-spaw2/protocols/README.md](https://github.com/takamichi-lab/j-spaw2/blob/main/protocols/README.md)

 ## Metadata
`metadata_LA.txt` and `metadata_PA.txt` follow the metadata format of [ASVspoof2021](https://www.asvspoof.org/index2021.html)．Using these, you can evaluate EER (equal error rate) and [t-DCF](https://arxiv.org/abs/1804.09618) (tandem detection cost function) with the ASVspoof2021  [eval-package](https://github.com/asvspoof-challenge/2021/tree/main/eval-package) To evaluate EER and t-DCF for each environment in J-SpAW, replace the ASVspoof2021 [config.py](https://github.com/asvspoof-challenge/2021/blob/main/eval-package/config.py) with the one included in this package. The meanings of the symbols used in the metadata are as follows:

### ASV Task (ASV_eval.txt)
<!-- （No changes from J-SPAW） -->
```sh
1 F001_R1_E2_M2_BT.wav F001_R1_E2_M2_BU.wav
```
* `1`：Correct label
    * `1`：Genuine pair (speakers of two speech are the same)
    * `0`：Impostor pair (speakers of two speech are NOT the same)
* `F001_R1_E2_M2_BT.wav`：Enrollment speech
* `F001_R1_E2_M2_BU.wav`：Test speech

### ASV タスク (ASV_PA_eval.txt)
```sh
F001 F001_R1_E2_M3_s1_r1_e1_m1_AA R1 M3 E2 r1 m1 s1 e1 p1 v1 spoof notrim eval
```
* `F001`：Speaker ID or target speaker ID
* `F001_R1_E2_M3_s1_r1_e1_m1_p1_v1_AA`：Trial ID
* Bona fide speech recording of the target speaker
  * `R1 - R4`: Recording room ID
  * `M1 - M3`: Recording microphone ID
  * `E1 - E4`: Recording environment ID
* Spoofed speech recording by the attacker
  * `r1 - r4`: Recording room ID
  * `m1 - m3`: Recording microphone ID
  * `s1 - s4`: Playback loudspeaker ID
  * `e1 - e4`: Recording environment ID
  * `p1 - p2`: Playback distance ID
  * `v1 - v2`: Playback volume ID

### LA Task (metadata_LA.txt)
```sh
F001 F001_R1_E2_L1_BT _  E2 L1 _ _ _ _ spoof notrim eval
```
* `F001`：Speaker ID or target speaker ID
* F001_R1_E2_L1_BT: Trial ID
* `_`: Adjustment to match the number of columns in ASVspoof2021 metadata (please adjust according to the input of each system)
* `E2`: Recording environment of the bona fide speech used to generate the spoofed speech  
  * `E1`: Quiet indoor
  * `E2`: Indoor with air conditioning
  * `E3`: Indoor with background music
  * `E4`: Outdoor
* `L1`: Speech synthesis method
  * `L3`: CosyVoice2
  * `L4`: ElevenLabs
  * `L5`: VALL-E X
* `spoof`: Correct label
  * `bonafide`: Bona fide speech
  * `spoof`: Spoofed speech  
* `trim`, `notrim`: Whether non-speech segments are trimmed
  * `notrim`: No trimming
  * `trim`: Trimmed (Speech activity detection by Silero-VAD [4])  
* `eval`: Subset type
  * `eval`: Evaluation data (only eval is used in this database)

### PAタスク (metadata_PA.txt)
```sh
F001 F001_R1_E2_M3_s1_r1_e1_m1_AA R1 M3 E2 r1 m1 s1 e1 p1 v1 spoof notrim eval
```
* `F001`：話者IDまたはなりすまし対象話者ID
* `F001_R1_E2_M3_s1_r1_e1_m1_AA`：トライアルID
* なりすまし対象話者の実発話収録
  * `R1 - R4`：収録場所ID
  * `M1 - M3`：収録機器ID
  * `E1 - E4`：収録環境ID
* 攻撃者によるなりすまし音声収録
  * `r1 - r4`：収録場所ID
  * `m1 - m3`：収録機器ID
  * `s1 - s4`：再生機器ID
  * `e1 - e4`：収録環境ID
  * `p1 - p2`: 再生距離ID
  * `v1 - v2`: 再生音量ID
* `spoof`：正解ラベル
    * `bonafide`：実発話
    * `spoof`：なりすまし音声
* `notrim`：非発話区間のトリミングの有無
    * `notrim`：トリミングなし(本データベースではnotrimのみ)
* `eval`：サブセットの種類
    * `eval`：評価データ(本データベースではevalのみ)

## 音声ファイルの命名規則
### ASV タスク
```sh
{spkr_id}_{room_id}_{env_id}_{mic_id}_{sent_id}.wav
```
* `{spkr_id}`：話者ID (F001--F019, M001--M021 の 40 話者)
* `{room_id}`：収録場所ID (実発話収録, R1--R4 の 4 場所)
* `{env_id}`：収録環境ID (実発話収録, E1--E4 の 4 環境)
* `{mic_id}`：収録機器ID (実発話収録, M1--M3 の 3 種類)
* `{sent_id}`：発話テキストID (AA--BX の 50 文)

### LA タスク
```sh
{spkr_id}_{room_id}_{env_id}_{LA_method_id}_{sent_id}.wav
```
* `{spkr_id}`：話者ID (F001-F019, M001-M021 の 40 話者)
* `{room_id}`：収録場所ID (実発話収録, R1--R4 の 4 場所)
* `{env_id}`：収録環境ID (実発話収録, E1--E4 の 4 環境)
* `{LA_method_id}`：音声合成手法 (L3--L5 の 5 種類)
* `{sent_id}`：発話テキストID (BT--BX の 5 文) 

### PA タスク
```sh
{spkr_id}_{room_id}_{env_id}_{mic_id}_{loudspeaker_id}_{room_id_replay}_{env_id_replay}_{mic_id_replay}_{pos_id_replay}_{vol_id_replay}_{sent_id}.wav
```
* なりすまし対象話者の実発話収録
    * `{spkr_id}`：話者ID (F001--F019, M001--M021 の 40 話者)
    * `{room_id}`：収録場所ID (実発話収録, R1--R4 の 4 場所)
    * `{env_id}`：収録環境ID (実発話収録, E1--E4 の 4 環境)
    * `{mic_id}`：収録機器ID (実発話収録, M1--M3 の 3 種類)
* 攻撃者によるなりすまし音声収録
    * `{loudspeaker_id}`：再生機器ID (再収録, s1--s4 の 4 種類)
    * `{room_id_replay}`：収録場所ID (再収録, r1 のみ)
    * `{env_id_replay}`：収録環境ID (再収録, e1--e3 の 3 種類)
    * `{mic_id_replay}`：収録機器ID (再収録, m1--m2 の 2 種類)
    * `{pos_id_replay}`: 再生距離ID (再収録, p1--p2 の 2 種類)
  * `{vol_id_replay}`: 再生音量ID (再収録, v1--v2 の 2 種類)
  * `{sent_id}`: Sentence ID (25 sentences: AA--AY)
    * `{sent_id}`：発話テキストID (AA--AY の 25 文)

## タスク共通の詳細
* 収録場所ID：縦 (m) x 横 (m) x 高さ (m)
    * `R1`：4.4 x 7.4 x 2.5 
    * `r1`：11.0 x 8.0 x 2.6
    * `R2,r2`：屋外1
    * `R3,r3`：10.8 x 2.0 x 2.8
    * `R4,r4`：屋外2
* 収録機器ID：
    * `M1,m1`：Pixel3
    * `M2,m2`：iPhone8 
    * `M3,m3`：iPad mini (第5世代)
* 収録環境ID：
    * `E1,e1`：静かな室内
    * `E2,e2`：空調が動作している室内
    * `E3,e3`：音楽が流れている室内
    * `E4`：屋外
* 再生機器ID：
    * `s1`：Bose Soundlink Micro Bluetooth Speaker Bundle
    * `s2`：iPad mini (第5世代)
    * `s3`：MacBook Pro (13インチ, M2, 2022)
    * `s4`：Sony SRS-ZR7
* 再生距離ID:
  * `p1`:近距離 (約 10 cm)
  * `p2`:遠距離 (約 1m)
* 再生音量ID:
  * `v1`:音量大
  * `v2`:音量小

## ライセンス
- 非商用利用に限る
