## プロトコルファイルの詳細
本ディレクトリに置かれている各プロトコルファイルの内容の詳細及び，それらを用いて実験した際の実験結果を記載しています．

 ※本リポジトリで報告しているEERは，論文で報告した値から更新されています．


## ファイル一覧
- `protocols/`
    - ASV_eval.txt: 話者照合のためのトライアルリスト(なりすまし音声無し)
    - `ASV_LA/`
        - ASV_eval_LA_L{3,4,5}.txt: 話者照合のためのトライアルリスト(なりすまし音声あり，本人同士：7,600ペア, 他人同士：30,000ペア, 本人同士の実発話となりすまし音声：16,000ペア)※
    - `tDCF_LA/`
        - `asv/`
            - L{3,4,5}_ASV,txt: tDCFを求めるための話者照合スコアリスト(LA)
        - `cm/`
            - L{3,4,5}_CM,txt: tDCFを求めるためのなりすまし音声検出スコアリスト(LA)
    - ASV_PA_eval_tDCF.txt: tDCFを求めるためのリスト(PA)
    - ASV_PA_eval.txt: 実発話となりすまし音声の話者照合のためのトライアルリスト(本人同士：7,600ペア, 他人同士：30,000ペア, 本人同士の実発話となりすまし音声：640,000ペア)
    - PA_eval.txt: なりすまし音声検出のためのリスト(実発話：800ファイル, なりすまし音声：32,000ファイル)※
    - `CM_LA/`
        - metadata_LA_L{3,4,5}.txt: LAタスク用のなりすまし音声のメタデータ(実発話：800ファイル, なりすまし音声：800ファイル)
    - metadata_PA.txt: PAタスク用のなりすまし音声のメタデータ(実発話：800ファイル, なりすまし音声：32,000ファイル)※

  ※ASV_PA_eval.txt，PA_eval.txt，metadata_PA.txtは音量大・近距離（p1_v1）の環境になっているため，p1とv1を変更しながら使用してください

## ファイル内容
metadata_LA.txt, metadata_PA.txt は ASVspoof2021 におけるメタデータの書き方を参考にしています．これらを利用することで, ASVspoof2021 の eval-packageのmain.pyで EER (equal error rate)とt-DCF (tandem detection cost function)の評価を実施できます．その際, J-SpAW の各環境ごとに EER, t-DCF の評価を行いたい場合は ASVspoof2021 eval-package の config.py を, 本パッケージに含まれている protocols/config.pyと差し替えてください．また，ASV(話者照合)とCM(なりすまし音声検出)を連結した結果，CMを突破した音声のみで話者照合をした結果であるt-DCF (tandem detection cost function)はASVspoof2019のevaluate_tDCF_asvspoof19.pyを変更しながら使用して評価を実施しました．メタデータで使われている各記号の意味やファイルの詳細は[J-SPAW2/README.md](https://github.com/takamichi-lab/j-spaw2/blob/main/README.md)に記載されています．

## 実験

### ASV (ECAPA-TDNNモデルで評価)
事前学習済みモデルは[ここ](https://github.com/TaoRuijie/ECAPA-TDNN)からダウンロードできます．
ECAPA-TDNN/trainECAPAModel.pyのeval_list,eval_path等を変更し，以下のコマンドを用いて評価を行います．
```sh
python trainECAPAModel.py --eval --initial_model exps/pretrain.model
```

#### ASV (なりすまし音声無し)の評価
EERの結果は1.69%です．

### CM　
#### PA (LFCC-GMM)
事前学習済みモデルは[ここ](https://github.com/asvspoof-challenge/2021)からダウンロードできます．

2021/PA/Baseline-LFCC-GMM/python/gmm_scoring_asvspoof21.pyのdict_file,db_folder,eval_folder,eval_ndx,audio_ext等を変更しながら使用してください．また，eval_foldeをリスト型にするなどして，2つのフォルダ（なりすまし音声用のPAのフォルダと実発話用のASVのフォルダ）をどちらも参照できるようにし，以下のコマンドを用いて評価を行います．
```sh
python gmm_scoring_asvspoof21.py
```

#### PA (AASIST)
事前学習済みモデルは[ここ](https://github.com/clovaai/aasist)からダウンロードできます．
asvspoof5/Baseline-AASIST/config/AASIST_ASVspoof5.confのdatabase_path, asvspoof5/Baseline-AASIST/main.pyのdev_trial_path等を変更し，以下のコマンドを用いて評価を行います．

```sh
python ./main.py --config ./config/AASIST_ASVspoof5.conf --eval
```

#### PA (wav2vec2.0+AASIST)
事前学習済みモデルは[ここ](https://github.com/TakHemlata/SSL_Anti-spoofing)からダウンロードできます．
(SSL_Anti-spoofing/main_SSL_LA.pyのfile_eval.eval_set等を変更しながら使用し，以下のコマンドを用いて評価を行います．
```sh
CUDA_VISIBLE_DEVICES=0 python main_SSL_LA.py --track=LA --is_eval --eval --model_path='model_path' --eval_output='output.txt'
```
#### LA (wav2vec2.0+AASIST)
事前学習済みモデルは[ここ](https://github.com/TakHemlata/SSL_Anti-spoofing)からダウンロードできます．
(SSL_Anti-spoofing/main_SSL_LA.pyのfile_eval.eval_set等を変更しながら使用し，以下のコマンドを用いて評価を行います．
```sh
CUDA_VISIBLE_DEVICES=0 python main_SSL_LA.py --track=LA --is_eval --eval --model_path='model_path' --eval_output='output.txt'
```

### 評価
それぞれのモデルでスコアを出したテキストファイルを使用して評価を行います．[ASVspoof2021のeval-package](https://github.com/asvspoof-challenge/2021/tree/main/eval-package)を使用してEER，[ASVspoof2019](https://github.com/asvspoof-challenge/2021/tree/main/eval-package)を使用してtDCFを出すことができます．

#### EER (ASVspoof2021のeval-package)
2021/eval-package/keys/PA/CM/trial_metadata.txt等を変更し，以下のコマンドを用いて評価を行います．
```sh
python main.py --cm-score-file score.txt --track PA --subset eval 
```

#### tDCF (ASVspoof2019)
asvspoof2019/evaluate_tDCF_asvspoof19.pyのcm_score_file,asv_score_file等を変更し，以下のコマンドを用いて評価を行います．
```sh
python evaluate_tDCF_asvspoof19.py
```

### 実験結果

##なりすまし音声検出

PA※
| | | LFCC-GMM | AASIST | wav2vec2.0+AASIST |
| :--- | :--- | :---: | :---: | :---: |
| **EER (%)** | 音量大・近距離 (p1_v1) | 34.50 | 40.40 | 2.65 |
| | 音量小・近距離 (p1_v2) | 54.75 | 35.12 | 3.62 |
| | 音量大・遠距離 (p2_v1) | 19.25 | 28.75 | 2.75 |
| | 音量小・遠距離 (p2_v2) | 66.38 | 46.12 | 18.61 |
| **t-DCF** | 音量大・近距離 (p1_v1) | 0.755 | 0.890 | 0.111 |
| | 音量小・近距離 (p1_v2) | 0.983 | 0.976 | 0.163 |
| | 音量大・遠距離 (p2_v1) | 0.682 | 0.939 | 0.097 |
| | 音量小・遠距離 (p2_v2) | 0.975 | 1.000 | 0.615 |

※本リポジトリで報告しているEERは，論文で報告した値から更新されています．引用する場合にはこちらの数値を使用してください．

LA
| 収録環境 | CosyVoice2 | ElevenLabs | VALL-E X |
| **EER (%)** | :--- | :---: | :---: | :---: |
| | 静かな室内 (E1) | 9.87 | 6.66 | 3.19 |
| | 空調のある室内 (E2) | 9.57 | 6.18 | 2.86 |
| | 音楽のある室内 (E3) | 8.30 | 4.88 | 2.52 |
| | 静かな屋外 (E4) | 10.02 | 6.21 | 3.09 |
| | 全環境 | 13.98 | 8.78 | 3.67 |
|  **t-DCF** | |　| |
| |J-SPAW 全環境 | 0.45 | 0.99 | 0.41 |


## 話者照合 (LAによるなりすまし音声を含む)の評価


| 収録環境 | L3 | L4 | L5 |
| :--- | :---: | :---: | :---: |
| 静かな室内 (E1) | 15.56 | 32.00 | 10.50 |
| 空調のある室内 (E2) | 13.94 | 27.56 | 2.88 |
| 音楽のある室内 (E3) | 12.94 | 27.56 | 7.00 |
| 静かな屋外 (E4) | 15.56 | 31.50 | 3.50 |
| 全環境 | 14.75 | 29.00 | 6.75 |


## 話者照合 (PAによるなりすまし音声を含む)の評価

| 条件 | EER（%） |
| -- | -- | 
| 音量大, 遠距離 （p1_v1） | 23.95% |
| 音量大, 遠距離 （p1_v2） | 20.54% |
| 音量小, 近距離 （p2_v1） | 15.29% |
| 音量小, 近距離 （p2_v2） | 9.80% |
