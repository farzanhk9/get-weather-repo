import os
import requests
import datetime
import random

# ====== SETTINGS ======
CITY = "London"  # Change to your city
API_KEY = "your_openweathermap_api_key"  # Get from openweathermap.org (free account)
# ======================

# Weather-based commit messages
sunny_msgs = ["Sunny day vibes ☀️☀️", "Bright and beautiful outside 🌞🌞", "Enjoying the sunshine 🌼"]
rainy_msgs = ["Rainy mood 🌧", "Raindrops keep falling 💧", "Cozy day indoors ☕"]
cloudy_msgs = ["Cloudy skies ahead ☁️", "A bit gloomy but okay 🌫", "Waiting for the sun to come out 🌥"]
other_msgs = ["Weather is changing 🌍", "Interesting day outside 🌈", "Another day, another vibe 🌎"]

def get_weather():
    """Fetch current weather from OpenWeatherMap."""
    url = f"https://api.openweathermap.org/data/2.5/weather?q={CITY}&appid={API_KEY}&units=metric"
    try:
        response = requests.get(url)
        data = response.json()
        weather = data["weather"][0]["main"].lower()
        temp = data["main"]["temp"]
        return weather, temp
    except:
        return "unknown", None

def pick_message(weather):
    """Pick a random commit message based on the weather."""
    if "sun" in weather or "clear" in weather:
        return random.choice(sunny_msgs)
    elif "rain" in weather:
        return random.choice(rainy_msgs)
    elif "cloud" in weather:
        return random.choice(cloudy_msgs)
    else:
        return random.choice(other_msgs)

# Get weather data
weather, temp = get_weather()
msg = pick_message(weather)

# Append to weather log
with open("daily_weather.txt", "a", encoding="utf-8") as f:
    f.write(f"{datetime.datetime.now()}: Weather in {CITY} - {weather}, Temp: {temp}°C\n")

# Commit and push
os.system("git add daily_weather.txt")
os.system(f'git commit -m "{msg}"')
os.system("git push")

print(f"✅ Commit pushed with message: {msg}")
