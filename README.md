# Настроение треков: кластеризация и рекомендации

Прототип рекомендаций по настроению на готовых аудио-признаках Spotify
(`tempo`, `energy`, `valence`, `danceability`, `loudness`), ~278 тыс. треков.

**Чего здесь нет:** анализа сырого аудио (признаки взяты из датасета готовыми) и бэкенда. Это исследовательский ноутбук.

## Как это работает
1. Признаки стандартизуются, KMeans (k=5) делит треки на группы по настроению.
2. Для трека из датасета рекомендации — ближайшие к нему треки **внутри его группы** (евклидово расстояние).
3. Для нового трека группа определяется через `kmeans.predict`, дальше то же ранжирование.

## Как проверить (около 2 минут)
Откройте `mood_clustering.ipynb`, затем Run All. Полный датасет в репозиторий не входит,
поэтому ноутбук автоматически берёт `data/sample_5k.csv` (5000 треков).
Результаты на полном датасете приведены ниже.

## Результаты (полный датасет)
Оценка: для 2000 случайных треков доля из 10 рекомендаций с той же **реальной** эмоцией из датасета (precision@10).

| Метод | precision@10 |
|---|---|
| Случайные из всей базы | 0.282 |
| Случайные из того же кластера | 0.488 |
| Ближайшие по всей базе (без кластеров) | 0.679 |
| Ближайшие внутри кластера (метод проекта) | 0.681 |


Соответствие кластеров реальным эмоциям: ARI = 0.247, NMI = 0.289. Silhouette (k=5) = 0.213.

## Примеры рекомендаций
Случайные исходные треки, не отобранные вручную (ссылки на Spotify):

| Исходный трек | Группа | Рекомендации |
|---|---|---|
| [трек](https://open.spotify.com/track/3dvtiG0XQkcfHQpr3Ghhp0) | энергичная | [1](https://open.spotify.com/track/7gK2rxrKGFnGEuXzMzKLxh) · [2](https://open.spotify.com/track/1USzBdoY919UEQheqzAON5) · [3](https://open.spotify.com/track/2f4IuijXLxYOeBncS60GUD) · [4](https://open.spotify.com/track/29j8O18EZi6i6Kdx3WZQD4) · [5](https://open.spotify.com/track/4sEvOnVoC7dEKRlT42u7K4) |
| [трек](https://open.spotify.com/track/7aj5HCI33VjFYBrCh5G4NN) | спокойная | [1](https://open.spotify.com/track/40jZFJBVYMzxdEpMptacUM) · [2](https://open.spotify.com/track/62OtnUrgvOg1wQdavqcZgU) · [3](https://open.spotify.com/track/2xOBiq7NMvSBqlDQjVvOek) · [4](https://open.spotify.com/track/4bzPm515kjjaTDLfGo5TR9) · [5](https://open.spotify.com/track/04Tv9Yle3gJFfoAaLTfkl2) |
| [трек](https://open.spotify.com/track/23we50Qc5172rJ54fgfRJP) | спокойная | [1](https://open.spotify.com/track/0Q4ubt0dvVtMcAYwAul0Nl) · [2](https://open.spotify.com/track/5AMdoidwOqaN0aiJrlu2BG) · [3](https://open.spotify.com/track/7o3EmAAZuhyCv4LwRtnNcB) · [4](https://open.spotify.com/track/0nx50eCOunPVQCaqebc48H) · [5](https://open.spotify.com/track/6gFYbE7E8zNnkhh4TEUypo) |
| [трек](https://open.spotify.com/track/2Ps6d1ld4mxZFKy9ZFOldo) | радостная | [1](https://open.spotify.com/track/2ie4YdZgMJVEaz4irkf5yw) · [2](https://open.spotify.com/track/2oIZcX26Krbmb3svEfSjLB) · [3](https://open.spotify.com/track/5o1TaTsFtDddxLa2BKw29y) · [4](https://open.spotify.com/track/4Ee6ZsyVepVsvObgYwzgH1) · [5](https://open.spotify.com/track/2tEhiPAdwtAmsBAsKbc4tO) |
| [трек](https://open.spotify.com/track/3eoFORgglclGm5uwLomyuv) | радостная | [1](https://open.spotify.com/track/40SHZwS95ZadjL52BjkdJr) · [2](https://open.spotify.com/track/0zqBZqm5czQ3A4EoSdKFHj) · [3](https://open.spotify.com/track/7eKiuT8bMkmVhwiCJ03OKC) · [4](https://open.spotify.com/track/4XUF8jkphrADXyOqL4lffA) · [5](https://open.spotify.com/track/0qOvlEzD7xU35GLFT8DF1t) |

## Ограничения
- Кластеры сильно перекрываются в пространстве признаков (см. `mood_clusters.png`): пять признаков не разделяют настроения чётко.
- RandomForest в ноутбуке обучен на метках KMeans по тем же признакам, поэтому его метрики показывают точность воспроизведения кластеров, а не качество определения настроения.
- Данные содержат выбросы (`tempo = 0`, `loudness` до −60), они не отфильтрованы.
- Оценка использует метки датасета как эталон; предпочтения реальных пользователей не измерялись.

## Данные
Данные: https://github.com/orzanai/Moodify/blob/main/Datasets/278k_labelled_uri.csv
