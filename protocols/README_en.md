## Details of Protocol Files

[日本語版はこちら](https://github.com/takamichi-lab/j-spaw2/blob/main/protocols/README.md)

This section describes the details of each protocol file placed in this directory and the experimental results when using them for evaluation.

　※Note: The EER reported in this repository has been updated from the values reported in the paper.


## List of Files
- `protocols/`
    - `ASV_eval.txt`: Trial list for ASV task (without spoofed speech)
    - `ASV_PA_eval_tDCF.txt`: List for calculating t-DCF (PA task)
    - `ASV_PA_eval.txt`: Trial list for ASV between bona fide and spoofed speech (7,600 genuine pairs, 30,000 impostor pairs, 640,000 pairs of genuine bona fide speech and spoofed speech)
    - `PA_eval.txt`: List for spoofing detection (800 bona fide files, 32,000 spoofed files) *
    - `metadata_PA.txt`: Metadata of spoofed speech for PA task (800 bona fide files, 32,000 spoofed files) *
    - `ASV_LA/`
        - `ASV_eval_LA_L{3,4,5}.txt`: Trial list for ASV (includes spoofed speech; 7,600 genuine pairs, 30,000 impostor pairs, 16,000 pairs of genuine bona fide speech and spoofed speech) *
    - `CM_LA/`
        - `metadata_LA_L{3,4,5}.txt` : Metadata of spoofed speech for LA task (800 bona fide files, 800 spoofed files)
    - `tDCF_LA/`
        - `asv/`
            - `L{3,4,5}_ASV.txt`: ASV score list for calculating t-DCF (LA)
        - `cm/`
            - `L{3,4,5}_CM.txt`: Spoofing detection score list for calculating t-DCF (LA)

  ※Note: `ASV_PA_eval.txt`, `PA_eval.txt`, and `metadata_PA.txt` are set to the Loud volume and Close distance (`p1_v1`) environment by default. Please change `p1` and `v1` as needed when using them.

## File Contents
`metadata_LA.txt` and `metadata_PA.txt` follow the metadata format of ASVspoof2021. Using these, you can evaluate EER (equal error rate) and t-DCF (tandem detection cost function) with `main.py` in the ASVspoof2021 eval-package. To evaluate EER and t-DCF for each environment in J-SpAW, replace the ASVspoof2021 `config.py` with the `protocols/config.py` included in this package. Furthermore, t-DCF (tandem detection cost function)—which is the result of ASV performed only on speech that bypassed the CM when concatenating ASV (speaker verification) and CM (spoofing detection)—was evaluated by modifying and using `evaluate_tDCF_asvspoof19.py` from ASVspoof2019. The meanings of the symbols used in the metadata and file details are described in [J-SPAW2/README_en.md](https://github.com/takamichi-lab/j-spaw2/blob/main/README_en.md).

## Experiments

### ASV (Evaluation with ECAPA-TDNN Model)
The pre-trained model can be downloaded [here](https://github.com/TaoRuijie/ECAPA-TDNN).
Modify `eval_list`, `eval_path`, etc., in `ECAPA-TDNN/trainECAPAModel.py`, and run the evaluation using the following command:
```sh
python trainECAPAModel.py --eval --initial_model exps/pretrain.model
```

#### Evaluation of ASV (without spoofed speech)
The EER result is 1.69%.

### CM
#### PA (LFCC-GMM)
Since the pre-trained model is not distributed, please refer to [this repository](https://github.com/asvspoof-challenge/2021) and create the model using the training scripts.

Please modify `dict_file`, `db_folder`, `eval_folder`, `eval_ndx`, `audio_ext`, etc., in `2021/PA/Baseline-LFCC-GMM/python/gmm_scoring_asvspoof21.py` as needed. Additionally, make `eval_folder` a list type so that it can reference both folders (the PA folder for spoofed speech and the ASV folder for bona fide speech), and evaluate using the following command:
```sh
python gmm_scoring_asvspoof21.py
```

#### PA (AASIST)
The pre-trained model can be downloaded [here](https://github.com/clovaai/aasist).
Modify `database_path` in `asvspoof5/Baseline-AASIST/config/AASIST_ASVspoof5.conf`, `dev_trial_path` in `asvspoof5/Baseline-AASIST/main.py`, etc., and evaluate using the following command:
```sh
python ./main.py --config ./config/AASIST_ASVspoof5.conf --eval
```

#### PA (wav2vec2.0+AASIST)
The pre-trained model can be downloaded [here](https://github.com/TakHemlata/SSL_Anti-spoofing).
Modify `file_eval.eval_set`, etc., in `SSL_Anti-spoofing/main_SSL_LA.py` as needed, and evaluate using the following command:
```sh
CUDA_VISIBLE_DEVICES=0 python main_SSL_LA.py --track=LA --is_eval --eval --model_path='model_path' --eval_output='output.txt'
```

#### LA (wav2vec2.0+AASIST)
The pre-trained model can be downloaded [here](https://github.com/TakHemlata/SSL_Anti-spoofing).
Modify `file_eval.eval_set`, etc., in `SSL_Anti-spoofing/main_SSL_LA.py` as needed, and evaluate using the following command:
```sh
CUDA_VISIBLE_DEVICES=0 python main_SSL_LA.py --track=LA --is_eval --eval --model_path='model_path' --eval_output='output.txt'
```

### Evaluation
Evaluation is performed using the text files that output the scores for each model. You can calculate EER using the [ASVspoof2021 eval-package](https://github.com/asvspoof-challenge/2021/tree/main/eval-package) and t-DCF using [ASVspoof2019](https://github.com/nesl/asvspoof2019).

#### EER (ASVspoof2021 eval-package)
Modify `2021/eval-package/keys/PA/CM/trial_metadata.txt`, etc., and evaluate using the following command:
```sh
python main.py --cm-score-file score.txt --track PA --subset eval 
```

#### t-DCF (ASVspoof2019)
Modify `asvspoof2019/evaluate_tDCF_asvspoof19.py` and evaluate.
In addition to modifying `cm_score_file`, `asv_score_file`, etc., change the ASV EER calculation so that it evaluates including spoof trials instead of the original implementation's setting which uses only non-target trials. Use differences in filename formats and rules for these changes. Also, modify the score file reading method and label determination processing appropriately according to the output format of the CM model used. Evaluate using the following command:
```sh
python evaluate_tDCF_asvspoof19.py
```

## Experimental Results

### UTMOS (LA)

|  | Recording Environment | CosyVoice2 | ElevenLabs | VALL-E X |
| :---: | :--- | :---: | :---: | :---: |
| **UTMOS** | Quiet indoor (E1) | 2.56 | 2.47 | 2.08 |
| | Indoor with air conditioning (E2) | 2.11 | 2.02 | 1.68 |
| | Indoor with music (E3) | 1.73 | 1.63 | 1.47 |
| | Outdoor (E4) | 2.90 | 2.68 | 2.17 |
| | All environments | 2.32 | 2.20 | 1.85 |

### Spoofing Detection

PA ※
| | | LFCC-GMM | AASIST | wav2vec2.0+AASIST |
| :--- | :--- | :---: | :---: | :---: |
| **EER (%)** | Loud volume, Close distance (p1_v1) | 34.50 | 40.40 | 2.65 |
| | Low volume, Close distance (p1_v2) | 54.75 | 35.12 | 3.62 |
| | Loud volume, Far distance (p2_v1) | 19.25 | 28.75 | 2.75 |
| | Low volume, Far distance (p2_v2) | 66.38 | 46.12 | 18.61 |
| **t-DCF** | Loud volume, Close distance (p1_v1) | 0.755 | 0.890 | 0.111 |
| | Low volume, Close distance (p1_v2) | 0.983 | 0.976 | 0.163 |
| | Loud volume, Far distance (p2_v1) | 0.682 | 0.939 | 0.097 |
| | Low volume, Far distance (p2_v2) | 0.975 | 1.000 | 0.615 |

※Note: The EER reported in this repository has been updated from the values reported in the paper. Please use these numbers when citing.

LA
| | Recording Environment | CosyVoice2 | ElevenLabs | VALL-E X |
| :--- | :--- | :---: | :---: | :---: |
| **EER (%)** | Quiet indoor (E1) | 9.87 | 6.66 | 3.19 |
| | Indoor with air conditioning (E2) | 9.57 | 6.18 | 2.86 |
| | Indoor with music (E3) | 8.30 | 4.88 | 2.52 |
| | Outdoor (E4) | 10.02 | 6.21 | 3.09 |
| | All environments | 13.98 | 8.78 | 3.67 |
| **t-DCF** | J-SPAW All environments | 0.45 | 0.99 | 0.41 |


### Evaluation of ASV (includes PA spoofed speech)

| Condition | EER (%) |
| -- | -- | 
| Loud volume, Far distance (p1_v1) | 23.95 |
| Loud volume, Far distance (p1_v2) | 20.54 |
| Low volume, Close distance (p2_v1) | 15.29 |
| Low volume, Close distance (p2_v2) | 9.80 |

### Evaluation of ASV (includes LA spoofed speech)


| Recording Environment | L3 | L4 | L5 |
| :--- | :---: | :---: | :---: |
| Quiet indoor (E1) | 15.56 | 32.00 | 10.50 |
| Indoor with air conditioning (E2) | 13.94 | 27.56 | 2.88 |
| Indoor with music (E3) | 12.94 | 27.56 | 7.00 |
| Outdoor (E4) | 15.56 | 31.50 | 3.50 |
| All environments | 14.75 | 29.00 | 6.75 |
