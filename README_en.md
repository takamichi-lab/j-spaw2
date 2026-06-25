# J-SPAW2: A Japanese Speech Corpus for Analyzing Recording Conditions in Replay Attacks
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

### ASV Task (ASV_PA_eval.txt)
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

### PA Task (metadata_PA.txt)
```sh
F001 F001_R1_E2_M3_s1_r1_e1_m1_AA R1 M3 E2 r1 m1 s1 e1 p1 v1 spoof notrim eval
```
* `F001`: Speaker ID or target speaker ID
* `F001_R1_E2_M3_s1_r1_e1_m1_AA`: Trial ID
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
* `spoof`: Correct label
    * `bonafide`: Bona fide speech
    * `spoof`: Spoofed speech
* `notrim`: Whether non-speech segments are trimmed
    * `notrim`: No trimming (only `notrim` is used in this database)
* `eval`: Subset type
    * `eval`: Evaluation data (only `eval` is used in this database)

## Naming Format for Speech Files
### ASV Task
```sh
{spkr_id}_{room_id}_{env_id}_{mic_id}_{sent_id}.wav
```
* `{spkr_id}`: Speaker ID (40 speakers: F001--F019, M001--M021)
* `{room_id}`: Recording room ID (for bona fide speech, R1--R4)
* `{env_id}`: Recording environment ID (for bona fide speech, E1--E4)
* `{mic_id}`: Recording microphone ID (for bona fide speech, M1--M3)
* `{sent_id}`: Sentence ID (50 sentences: AA--BX)


### LA Task
```sh
{spkr_id}_{room_id}_{env_id}_{LA_method_id}_{sent_id}.wav
```
* `{spkr_id}`: Speaker ID (40 speakers: F001-F019, M001-M021)
* `{room_id}`: Recording room ID (for bona fide speech, R1--R4)
* `{env_id}`: Recording environment ID (for bona fide speech, E1--E4)
* `{LA_method_id}`: Speech synthesis method (5 types: L3--L5)
* `{sent_id}`: Sentence ID (5 sentences: BT--BX)

### PA Task
```sh
{spkr_id}_{room_id}_{env_id}_{mic_id}_{loudspeaker_id}_{room_id_replay}_{env_id_replay}_{mic_id_replay}_{pos_id_replay}_{vol_id_replay}_{sent_id}.wav
```
* Bona fide speech recording of the target speaker
    * `{spkr_id}`: Speaker ID (40 speakers: F001--F019, M001--M021)
    * `{room_id}`: Recording room ID (for bona fide speech, R1--R4)
    * `{env_id}`: Recording environment ID (for bona fide speech, E1--E4)
    * `{mic_id}`: Recording microphone ID (for bona fide speech, M1--M3)
* Spoofed speech recording by the attacker
    * `{loudspeaker_id}`: Playback loudspeaker ID (for re-recording, 4 types: s1--s4)
    * `{room_id_replay}`: Recording room ID (for re-recording, only r1)
    * `{env_id_replay}`: Recording environment ID (for re-recording, 3 types: e1--e3)
    * `{mic_id_replay}`: Recording microphone ID (for re-recording, 2 types: m1--m2)
    * `{pos_id_replay}`: Playback distance ID (for re-recording, 2 types: p1--p2)
  * `{vol_id_replay}`: Playback volume ID (for re-recording, 2 types: v1--v2)
  * `{sent_id}`: Sentence ID (25 sentences: AA--AY)


## Common Details Across Tasks
* Recording room ID: Dimensions (length (m) x width (m) x height (m))
    * `R1`: 4.4 x 7.4 x 2.5 
    * `r1`: 11.0 x 8.0 x 2.6
    * `R2,r2`: Outdoor 1
    * `R3,r3`: 10.8 x 2.0 x 2.8
    * `R4,r4`: Outdoor 2
* Recording microphone ID:
    * `M1,m1`: Pixel3
    * `M2,m2`: iPhone8 
    * `M3,m3`: iPad mini (5th generation)
* Recording environment ID:
    * `E1,e1`: Quiet indoor
    * `E2,e2`: Indoor with air conditioning
    * `E3,e3`: Indoor with music
    * `E4`: Outdoor
* Playback loudspeaker ID:
    * `s1`: Bose Soundlink Micro Bluetooth Speaker Bundle
    * `s2`: iPad mini (5th generation)
    * `s3`: MacBook Pro (13-inch, M2, 2022)
    * `s4`: Sony SRS-ZR7
* Playback distance ID:
  * `p1`: Close distance (approx. 10 cm)
  * `p2`: Far distance (approx. 1 m)
* Playback volume ID:
  * `v1`: Loud volume
  * `v2`: Low volume

## License
- For non-commercial use only


## Authors
- Suzuka Horie (Tokyo Metropolitan University)
- Sawato Furubayashi (Tokyo Metropolitan University)
- Shinnosuke Takamichi (Keio University)
- Sayaka Shiota (Tokyo Metropolitan University)

## Papers
- Suzuka Horie, Shinnosuke Takamichi, Sayaka Shiota, "J-SPAW2: A Japanese Speech Corpus for Analyzing Recording Conditions in Replay Attacks", SPEASIP, 2026
- Sawato Furubayashi, Shinnosuke Takamichi, Sayaka Shiota, "Spoofing attacks using deepfake speech synthesized from non-consensual recording", SPEASIP, 2026

  ※ The PA (Playback Attack) component of J-SPAW2 is primarily reported by Horie et al. (2026), and the LA (Synthetic Speech Attack) component by Furubayashi et al. (2026).

## References
[1] Z. Du, Y. Wang, Q. Chen, X. Shi, X. Lv, T. Zhao, Z. Gao, Y. Yang, C. Gao, H. Wang, et al., “CosyVoice 2: Scalable Streaming Speech Synthesis with Large Language Models,” in Proc. CoRR, 2024.

[2] https://elevenlabs.io//.

[3] Ziqiang Zhang, Long Zhou, et al, ”speak foreign languages with your own voice: Cross-lingual neural codec language modeling,” arXiv:2303.03936(2023)
