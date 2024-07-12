![alt text](https://github.com/ecocity-coder/store/blob/main/2024-07-06_22-33-06.png)
<p><strong>Аналитическое исследование продаж, возвратов и обращений за ремонтом нагревателей табака</strong></p>
from google.colab import files


uploaded = files.upload()

import pandas as pd
file_path = '/content/TEST_TASK_REPLACEMENT_UPDATE 1.xlsx'

df = pd.read_excel(file_path)
print(df.head())

print("Типы данных в колонках:")
print(df.dtypes)

if sum(df.duplicated()) == 0:
    print("Дубликатов не обнаружено")
else:
    print("Обнаружены дубликаты")

if sum(df.isnull().values.ravel()) == 0:
    print("Пропусков не обнаружено")
else:
    print("Обнаружены пропуски")

missing_values = df.isnull()
print(missing_values.sum())
print(df[missing_values.any(axis=1)])

grouped = df.groupby(df['дата_обращения'].dt.month).size()
result = grouped[(grouped.index >= 1) & (grouped.index <= 3)]
print(result)
total_count = result.loc[[1, 2, 3]].sum()
print(f"Суммарное количество обращений за январь, февраль, март: {total_count}")

count = df[(df['тип'] == 'Kit') & (df['решение'] == 'WarrantyReplacement')].shape[0]
print(count)


import matplotlib.pyplot as plt
df['неделя'] = df['дата_обращения'].dt.isocalendar().week
df_warranty = df[df['решение'] == 'WarrantyReplacement']
weekly_counts = df_warranty.groupby(['неделя', 'тип']).size().unstack(fill_value=0)
weekly_counts.plot(kind='bar', stacked=True, figsize=(12, 6))
plt.title('Количество обращений с решением "WarrantyReplacement" по типам и неделям')
plt.xlabel('Неделя')
plt.ylabel('Количество обращений')
plt.legend(title='Тип')
plt.show()

print(df['решение'].unique())

df['дата_обращения'] = pd.to_datetime(df['дата_обращения'], errors='coerce')
print(df['дата_обращения'].min(), df['дата_обращения'].max())

df_filtered = df[(df['дата_обращения'] >= '2022-01-01') & (df['дата_обращения'] <= '2022-03-31')]

print(f'Количество записей с января по март: {df_filtered.shape[0]}')

df_warranty = df_filtered[df_filtered['решение'].str.contains('WarrantyReplacement', na=False)]

unique_records = df_warranty['код_клиента'].nunique()

print(f'Количество уникальных записей, запрашивающих WarrantyReplacement с января по март: {unique_records}')

df['дата_обращения'] = pd.to_datetime(df['дата_обращения'])
df_february = df[(df['дата_обращения'] >= '2022-02-01') & (df['дата_обращения'] <= '2022-02-28')]
df_warranty_february = df_february[df_february['решение'] == 'WarrantyReplacement']
print (df_warranty_february)
total_warranty_replacements = df_warranty_february.shape[0]
unique_clients = df_warranty_february['код_клиента'].nunique()
average_replacements_per_client = total_warranty_replacements / unique_clients if unique_clients else 0

print(f'Среднее количество решений "WarrantyReplacement" на одного клиента в феврале: {average_replacements_per_client}')



color_counts = df['цвет'].value_counts()
print(color_counts)
platform_counts = df['платформа'].value_counts()
print(platform_counts)


df_jan_to_mar = df[(df['дата_обращения'] >= '2022-01-01') & (df['дата_обращения'] <= '2022-03-31')]
average_device_age = df_jan_to_mar['возраст_устройства'].mean()
print(f'Средний возраст устройств за январь-март 2022 года: {average_device_age}')


warranty_df = df[df['решение'] == 'WarrantyReplacement']
warranty_by_city = warranty_df.groupby('город').size().reset_index(name='количество')
warranty_by_city_sorted = warranty_by_city.sort_values('количество', ascending=False)
print(warranty_by_city_sorted)


warranty_df = df[df['решение'] == 'WarrantyReplacement']
warranty_by_city = warranty_df.groupby('город').size()
warranty_by_city = warranty_by_city[warranty_by_city > 1000]
warranty_by_city.plot(kind='bar')

plt.title('Распределение решений WarrantyReplacement по городам с >1000 записей')
plt.xlabel('Город')
plt.ylabel('Количество решений')

plt.xticks(rotation=45, ha='right')

plt.tight_layout()
plt.show()


warranty_df = df[df['решение'] == 'WarrantyReplacement']
warranty_with_delivery = warranty_df[warranty_df['тип_доставки'].notna()]
count_warranty_with_delivery = warranty_with_delivery.shape[0]
print(f"Количество замен с доставкой: {count_warranty_with_delivery}")


warranty_df = df[df['решение'] == 'WarrantyReplacement']
total_warranty = warranty_df.shape[0]
warranty_with_delivery = warranty_df[warranty_df['тип_доставки'].notna()]
count_warranty_with_delivery = warranty_with_delivery.shape[0]
percentage_with_delivery = (count_warranty_with_delivery / total_warranty) * 100

print(f"Процент змен с доставкой: {percentage_with_delivery:.2f}%")


total_decisions = df['решение'].count()
warranty_decisions = df[df['решение'] == 'WarrantyReplacement']['решение'].count()
percentage_warranty = (warranty_decisions / total_decisions) * 100

print(f"99,46: {percentage_warranty:.2f}%")



filtered_df = df[(df['дата_обращения'] >= '2022-01-01') & (df['дата_обращения'] <= '2022-03-31')]
channel_counts = filtered_df['канал'].value_counts()
channel_ratio = channel_counts / channel_counts.sum()

print(channel_ratio)


nps_counts = df['nps'].value_counts()
nps_percentage = (nps_counts / nps_counts.sum()) * 100
nps_percentage_df = pd.DataFrame({'NPS': nps_percentage.index, 'Процент': nps_percentage.values})

print(nps_percentage_df)

