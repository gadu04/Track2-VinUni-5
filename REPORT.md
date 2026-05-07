# REPORT - MLOps Lab (AIInAction VinUni)

## 1) Cau hinh va ket qua buoc 1

- Mo hinh: `RandomForestClassifier`
- Tracking: MLflow local (`sqlite:///mlflow.db`)
- Du lieu huan luyen: `data/train_phase1.csv` (2998 mau)
- Du lieu danh gia: `data/eval.csv` (500 mau)

Da chay nhieu thuc nghiem voi cac bo sieu tham so khac nhau. Mot so lan chay tieu bieu:

1. `n_estimators=50`, `max_depth=3`, `min_samples_split=2`  
   -> `accuracy=0.5580`, `f1_score=0.5185`
2. `n_estimators=100`, `max_depth=5`, `min_samples_split=2`  
   -> `accuracy=0.5640`, `f1_score=0.5534`
3. `n_estimators=200`, `max_depth=10`, `min_samples_split=5`  
   -> `accuracy=0.6440`, `f1_score=0.6417`
4. `n_estimators=300`, `max_depth=None`, `min_samples_split=2`  
   -> `accuracy=0.6820`, `f1_score=0.6811` (tot nhat)

Bo sieu tham so duoc chon cho cac buoc tiep theo:

```yaml
n_estimators: 300
max_depth: null
min_samples_split: 2
```

Ly do chon:
- Dat `accuracy` va `f1_score` cao nhat trong cac lan thu nghiem.
- Mo hinh giu duoc su can bang giua kha nang tong quat va do on dinh tren tap eval.

## 2) Kho khan gap phai va cach xu ly

- **Loi moi truong:** ban dau `pytest` loi do thieu thu vien `mlflow`.
  - **Xu ly:** kich hoat dung moi truong va cai dat day du bang `pip install -r requirements.txt`.

- **Can tai lap ket qua:** ket qua co the dao dong neu random seed khong co dinh.
  - **Xu ly:** co dinh `random_state=42` trong `RandomForestClassifier`.

- **Dam bao CI/CD doc duoc ket qua train:**
  - **Xu ly:** luu metric vao `outputs/metrics.json` va model vao `models/model.pkl` sau moi lan train.

- **Gioi han cloud account (khong co billing):**
  - **Van de:** khong the tao GCS bucket, `dvc push/pull` va deploy VM bi chan boi loi billing.
  - **Xu ly tam thoi:** chuyen workflow sang local-only mode (generate data tren runner, train/eval binh thuong, mock deploy) de van bao dam quy trinh CI tu dong va tai lap duoc.

## 3) San pham da hoan thien trong repo

- Hoan thanh `src/train.py` (doc du lieu, train, log MLflow, luu metric/model).
- Hoan thanh `tests/test_train.py` (3 tests deu pass).
- Hoan thanh `src/serve.py` (`/health`, `/predict`, tai model tu cloud storage).
- Hoan thanh workflow `.github/workflows/mlops.yml` (local-only: Test -> Train -> Eval -> Mock Deploy).

## 4) Bang chung can chup man hinh de nop

- MLflow UI voi it nhat 3 runs.
- GitHub Actions pipeline (4 jobs mau xanh trong local-only mode).
- Metrics artifact (`outputs/metrics.json`) va model artifact (`models/model.pkl`) duoc luu tu job Train.
- Ghi chu han che cloud billing trong phan mo ta ket qua.
