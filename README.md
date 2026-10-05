# CTRL-is-hers

Kaggle müsabiqəsi (`ctrl-is-hers-final`) üçün hazırlanmış **ikili təsnifat (binary classification)** layihəsi. Məqsəd: məqalə xüsusiyyətlərinə əsasən `target` (0/1) dəyişənini proqnozlaşdırmaqdır. Qiymətləndirmə metrikası **ROC-AUC**-dir.

## Repozitoriya

| Fayl | Təsvir |
|---|---|
| [`projectwithmarkdown.ipynb`](projectwithmarkdown.ipynb) | Əsas notebook: EDA, preprocessing, modellər, submission və izahlı markdown xanalar |
| `README.md` | Layihə haqqında qısa məlumat |

## Data

Data `kagglehub` vasitəsilə yüklənir. Müsabiqə qovluğunda 4 fayl var: `train.csv`, `test.csv`, `sample_submission.csv`, `metaData.csv`.

| Göstərici | Dəyər |
|---|---|
| Train ölçüsü | 31 715 sətir × 49 sütun |
| Test ölçüsü | 7 929 sətir |
| Çatışmayan dəyər | yoxdur |
| Dublikat | yoxdur |
| Unikal `id` | 31 715 (hər sətir unikaldır) |
| Tarix aralığı | 2013-01-07 — 2014-08-28 |
| Kateqorik sütunlar | `weekday`, `channel` |
| Ədədi sütunlar | 44 |

**Hədəf paylanması:**

| target | Say | Faiz |
|---|---|---|
| 1 | 17 357 | ≈54.7% |
| 0 | 14 358 | ≈45.3% |

Siniflər nisbətən balanslıdır.

## Metodologiya

1. `id`, `publish_date` və `target` sütunları feature-lərdən çıxarılır.
2. Preprocessing (`ColumnTransformer`):
   - ədədi sütunlar → `StandardScaler`
   - kateqorik sütunlar → `OneHotEncoder(handle_unknown='ignore')`
3. Üç model `Pipeline` daxilində öyrədilir və müqayisə olunur:
   - **Logistic Regression** — baseline (`max_iter=1000`)
   - **Random Forest** — `n_estimators=300`, `max_depth=10`, `min_samples_leaf=5`
   - **LightGBM** — `n_estimators=300`, `learning_rate=0.05`, `num_leaves=31`
4. Metriklər: Accuracy, Precision, Recall, F1, ROC-AUC (train datası üzərində hesablanıb).
5. Final model bütün train datası ilə yenidən öyrədilir və `test.csv` üçün proqnoz verilir.

## Nəticələr

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Logistic Regression | 0.6536 | 0.67 | 0.72 | 0.69 | 0.7049 |
| Random Forest | 0.7487 | 0.7403 | 0.8330 | 0.7839 | 0.8326 |
| **LightGBM** | **0.7737** | **0.7778** | 0.8211 | **0.7989** | **0.8629** |

*Logistic Regression üçün Precision, Recall və F1 sinif 1 üzrədir.*

**LightGBM confusion matrix:**

| | Proqnoz 0 | Proqnoz 1 |
|---|---|---|
| **Həqiqi 0** | 10 286 | 4 072 |
| **Həqiqi 1** | 3 105 | 14 252 |

**Əsas müşahidələr:**
- Logistic Regression-dan ağac əsaslı modellərə keçid ROC-AUC-ni 0.705-dən 0.833-ə qaldırır.
- LightGBM ən yüksək nəticəni göstərir (ROC-AUC 0.8629).
- Hər üç model sinif 1-i sinif 0-dan daha yaxşı tapır.

## Submission faylları

| Fayl | Model | Sinif 0 | Sinif 1 |
|---|---|---|---|
| `submission.csv` | Random Forest | 3 696 | 4 233 |
| `submission_logistic.csv` | Logistic Regression | 3 929 | 4 000 |

Hər fayl 7 929 sətirdən ibarətdir (`id`, `target`).

## İşə salma

Notebook Kaggle mühiti üçün yazılıb (`/kaggle/input`, `/kaggle/working`). Lokal işlətmək üçün:

```bash
git clone https://github.com/Elvira-Babayeva/CTRL-is-hers.git
cd CTRL-is-hers
pip install pandas numpy matplotlib seaborn scikit-learn lightgbm kagglehub
jupyter notebook projectwithmarkdown.ipynb
```

Datanı `kagglehub` ilə endirmək üçün Kaggle hesabı və müsabiqə qaydalarının qəbul edilməsi lazımdır.




## Müəllif

[Elvira Babayeva](https://github.com/Elvira-Babayeva)
