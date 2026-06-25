# J-SPAW2: A Japanese Speech Corpus for Analyzing Recording Conditions inReplay Attacks
[日本語版はこちら](https://github.com/takamichi-lab/j-spaw2/blob/main/README.md)

The J-SpAW2 (pronounced j-spou2) corpus is designed for speaker verification and anti-spoofing verification, containing both bona fide speech and spoofing attacks.

## Download speech files
[Link](https://tmpuc.box.com/s/nwlq5wwposnbwx0oxp6bwvjxi003kenr) (zip, 9.0 GB)

## Contents
This repository provides a set of speech files, lists for evaluating speaker verification (ASV), and various protocols including lists and metadata for evaluating logical attack (LA) and physical attack (PA) tasks in spoofing detection. The contents are as follows:

- `jspaw2/`：Directory of speech files (download from the above URL and use them)
    - `ASV/*.wav`：Speaker verification (ASV) <!-- ([J-SPAW](https://github.com/takamichi-lab/j-spaw)と同じ音声ファイル) -->
    - `LA/L3/*.wav`：なりすまし音声検出 LA タスク(CosyVoice2[1]によるなりすまし音声)
    - `LA/L4/*.wav`：なりすまし音声検出 LA タスク(ElevenLabs[2]によるなりすまし音声)
    - `LA/L5/*.wav`：なりすまし音声検出 LA タスク(VALL-E X[3]によるなりすまし音声)
    - `PA/*.wav`：なりすまし音声検出 PA タスク
