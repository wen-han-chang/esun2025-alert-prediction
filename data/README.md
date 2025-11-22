# 📁 Data Folder / 資料夾說明

This folder is reserved for the **raw CSV files** required by the
E.SUN Bank 2025 Alert Account Prediction competition.

本資料夾用於存放玉山銀行 2025 **Alert Account Prediction** 競賽所提供的
三份原始資料檔案。

---

## 📄 Required Files 必須放入的檔案

請將以下三個官方提供的 CSV 檔案放入此資料夾：

```
acct_transaction.csv
acct_alert.csv
acct_predict.csv
```

> 📌 **注意**：原始資料不會上傳到 GitHub，請使用者自行置入。

---

## ▶️ Run Pipeline 執行流程

當此資料夾已放入必要資料後，可直接於專案根目錄執行：

```bash
python main.py
```

程式會自動讀取本資料夾中的 CSV 並進行後續流程。
（特徵工程與模型流程說明請參考專案主 README。）

