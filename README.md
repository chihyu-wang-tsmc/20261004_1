# 20261004_1

AI2 ARC 和 GSM8K 的原始題庫，從 HuggingFace 下載的 test split。

搭配程式碼 repo：[20261004](https://github.com/chihyu-wang-tsmc/20261004)。
把這兩個目錄放到那份程式碼的同一層，`gsm8k_routing.py` / `ai2arc_routing.py` 就讀得到。

## 檔案

| 檔案 | 題數 | 來源 | 欄位 |
| --- | --- | --- | --- |
| `gsm8k/gsm8k_test.jsonl` | 1319 | `openai/gsm8k` 的 main config | `id` `question` `solution` `answer` `steps` |
| `ai2arc/arc_challenge_test.jsonl` | 1172 | `allenai/ai2_arc` 的 ARC-Challenge | `id` `question` `options` `answer` |
| `ai2arc/arc_easy_test.jsonl` | 2376 | `allenai/ai2_arc` 的 ARC-Easy | 同上 |

這三個檔就是 `download_benchmarks.py` 的全部輸出：

```bash
python download_benchmarks.py --only gsm8k
python download_benchmarks.py --only ai2arc
```

**只收原始題庫。** 後續跑出來的東西（抽樣評測集、兩個 model 的逐題作答、彙整的
ground_truth、jevk5 的 model_route 答案）都不在這裡，要自己重跑：

```bash
python ai2arc_routing.py answer --model deepseek-flash
python ai2arc_routing.py answer --model gemini-3.7-flash
python gsm8k_routing.py  answer --model deepseek-flash
python gsm8k_routing.py  answer --model gemini-3.7-flash
```

## 這些題目拿來做什麼

GSM8K（小學數學文字題）和 AI2 ARC（科學選擇題）在這個專案裡是
**model_route 的實測 benchmark**：讓 fast 和 powerful 兩個 model 都答一遍再評分，
得出每題「能答對的最便宜 model」，用來檢驗 jevk5 的 `model_route` 判斷選得對不對。

在 `deep10.py` 的 `DATASETS` 裡它們屬於 `fast-powerful` 類別，同時也當成
安全 gate 的「難的無害題」—— 題目很專業但完全無害，是最嚴格的誤擋測試。

## 授權

兩個都是第三方資料集的衍生檔（下載後轉成 jsonl、重新編 id），
各自的授權條款請自行確認後再對外散布。
