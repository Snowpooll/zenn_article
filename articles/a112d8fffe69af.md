---
title: "現在地の天気情報の取得その2"
emoji: "☀️"
type: "tech" # tech: 技術記事
topics: ["天気", "Python", "OpenWeatherMap"]
published: true
publication_name: "singularity"
---

# 現在地の天気情報の取得その2

## Pythonで天気情報を取得する方法

以下はPythonを使って、郵便番号とAPIキーで天気情報を取得するコード例です。

```python
import requests
import json
from pprint import pprint

# OpenWeatherMap APIのURL
url = "https://api.openweathermap.org/data/2.5/weather?zip={zip_place}&units=metric&appid={API_key}"

# 郵便番号とAPIキーを指定
url = url.format(zip_place="郵便番号,JP", API_key="APIキー")

# APIからデータを取得
jsondata = requests.get(url).json()
pprint(jsondata)

# 天気情報を出力
print("天気：", jsondata["weather"][0]["main"])
print("天気詳細：", jsondata["weather"][0]["description"])
print("都市名：", jsondata["name"])
print("気温：", jsondata["main"]["temp"])
print("体感気温：", jsondata["main"]["feels_like"])
print("最低気温：", jsondata["main"]["temp_min"])
print("最高気温：", jsondata["main"]["temp_max"])
print("気圧：", jsondata["main"]["pressure"])
print("湿度：", jsondata["main"]["humidity"])
print("風速：", jsondata["wind"]["speed"])
print("風の方角：", jsondata["wind"]["deg"])
```

### 実行結果
```plaintext
{'base': 'stations',
 'clouds': {'all': 0},
 'cod': 200,
 'coord': {'lat': 緯度, 'lon': 経度},
 'main': {'feels_like': 4.12,
          'humidity': 60,
          'pressure': 1021,
          'temp': 6.65,
          'temp_max': 6.65,
          'temp_min': 6.65},
 'name': 'Kawai',
 'weather': [{'description': 'clear sky', 'main': 'Clear'}],
 'wind': {'deg': 288, 'speed': 3.58}}
天気： Clear
天気詳細： clear sky
都市名： Kawai
気温： 6.65
体感気温： 4.12
最低気温： 6.65
最高気温： 6.65
気圧： 1021
湿度： 60
風速： 3.58
風の方角： 288
```

## 日本語での天気情報取得

英語の天気情報を日本語に変換するには、以下の辞書を用いてマッピングします。

### 英語から日本語への天気翻訳辞書
```python
weather_translation = {
    "Clear": "晴れ",
    "Clouds": "曇り",
    "Rain": "雨",
    "Snow": "雪",
    "Thunderstorm": "雷雨",
    "Drizzle": "霧雨",
    "Mist": "霧",
    "Fog": "濃霧"
}

# APIから取得した天気を翻訳する
weather_english = jsondata["weather"][0]["main"]
weather_japanese = weather_translation.get(weather_english, "不明")  # 辞書にない場合は「不明」と表示

print("天気（日本語）：", weather_japanese)
```

これにより、APIから取得した天気情報を日本語で表示できます。

## 天気情報を詳細に取得する方法

緯度経度を使用して、一日の最高気温と最低気温を取得するには以下のコードを使用します。
```python
import requests
from datetime import datetime

# OpenWeather APIのURLとパラメータ
url = "https://api.openweathermap.org/data/2.5/onecall?lat={lat}&lon={lon}&exclude=hourly,minutely&units=metric&appid={API_key}"
latitude = "緯度"
longitude = "経度"
API_key = "YOUR_API_KEY"

# APIリクエスト
url = url.format(lat=latitude, lon=longitude, API_key=API_key)
response = requests.get(url)
jsondata = response.json()

# 今日の最高気温と最低気温
today = datetime.now().date()
for daily in jsondata["daily"]:
    date = datetime.fromtimestamp(daily["dt"]).date()
    if date == today:
        print("今日の最高気温：", daily["temp"]["max"], "度")
        print("今日の最低気温：", daily["temp"]["min"], "度")
        break
```

## 今後の展望

- 天気情報を音声に変換して通知
- 現在地の天気や移動予定地の天気を自動で取得・表示
