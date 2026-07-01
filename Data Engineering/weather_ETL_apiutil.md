```python
import requests
import os
import time
import pandas as pd
from datetime import datetime
from zoneinfo import ZoneInfo
import logging
from tqdm import tqdm

logger = logging.getLogger(__name__)

API_KEY = ""
API_ENDPOINT = "https://api.openweathermap.org/data/2.5/weather"

def weather_api_call(df: pd.DataFrame) -> dict:

	weather_api_json = {}
	print("Fetching weather data from API...")
	logger.info("Weather API extraction started")

	with requests.Session() as session:
	
		for row in tqdm(df.itertuples(index=False), total=len(df), desc="Processing Pincodes"):
			#itertuples is faster than itterows
			if pd.isna(row.latitude) or pd.isna(row.longitude):
			logger.warning(f"Skipping pincode {row.pincode}: Missing latitude/longitude")
			continue

			try:
				#Payload
				geo_payload = {
					"lat": row.latitude,
					"lon": row.longitude,
					"units": "metric",
					"appid": API_KEY
				}
				# Implementing Logging
				logger.info(f"Calling API for pincode {row.pincode}")
		
				# OpenWeather API Call
				weather_api_response = session.get(
					API_ENDPOINT,	
					params = geo_payload,
					timeout=5	
					)
				
				weather_api_response.raise_for_status()
				# Result will have the API response in dictionary Form
				result=weather_api_response.json()
				# Extract the time of the weather details from the API call
				timestamp = result.get("dt")
				
				if not timestamp:
					raise ValueError("Missing 'dt' in response")
					
				# Convert it into IST
				ist_time = datetime.fromtimestamp(timestamp, ZoneInfo("Asia/Kolkata"))
				#Updatating Main JSON
				weather_api_json[row.pincode] = {
					"api_call_timestamp": datetime.now(
					ZoneInfo("Asia/Kolkata")	
					).isoformat(),
					"weather_data_time": ist_time.isoformat(),
					"response": result	
				}
		
				# Log the API call was success
				logger.info(f"Success for pincode {row.pincode}")
				#Rate Limiting
				time.sleep(1)
	
			except requests.exceptions.RequestException as e:
				logger.error(f"API error for pincode {row.pincode}: {e}")
			 
			except ValueError as e:
				logger.error(f"Data issue for pincode {row.pincode}: {e}")
			
			except Exception as e:
				logger.exception(f"Unexpected error for pincode {row.pincode}")

	logger.info(f"Weather API extraction completed.")
	logger.info(f"Total successful records: {len(weather_api_json)}")

	return weather_api_json
```

## NEW

```python
import requests
import os
import time
import pandas as pd
from datetime import datetime
from zoneinfo import ZoneInfo
import logging
from tqdm import tqdm
import concurrent.futures


logger = logging.getLogger(__name__)

API_KEY = ""
API_ENDPOINT = "https://api.openweathermap.org/data/2.5/weather"


def api_call(pincode, latitude, longitude):
    with requests.Session() as session:
        try:
            #Payload
            geo_payload = {
                "lat": latitude,
                "lon": longitude,
                "units": "metric",
                "appid": API_KEY
            }
            # OpenWeather API Call
            weather_api_response = session.get(
                API_ENDPOINT, 
                params = geo_payload,
                timeout=5
            ) 
            weather_api_response.raise_for_status()

            # Result will have the API response in dictionary Form
            result=weather_api_response.json()

            # Extract the time of the weather details from the API call
            timestamp = result.get("dt")
            if not timestamp:
                raise ValueError("Missing 'dt' in response")
            
            # Convert it into IST
            ist_time = datetime.fromtimestamp(timestamp, ZoneInfo("Asia/Kolkata"))

            #Updatating Main JSON
            temp = {
                "pincode": pincode,
                "api_call_timestamp": datetime.now(
                    ZoneInfo("Asia/Kolkata")
                ).isoformat(),
                "weather_data_time": ist_time.isoformat(),
                "response": result
            }

            # Log the API call was success
            logger.info(f"Success for pincode {pincode}")
            #Rate Limiting
            time.sleep(1)
            return(temp)

        except requests.exceptions.RequestException as e:
                logger.error(f"API error for pincode {pincode}: {e}")

        except Exception as e:
            logger.exception(f"Unexpected error for pincode {pincode}")


def weather_api_call(df: pd.DataFrame) -> list[dict]:

    weather_api_json = []
    print("Fetching weather data from API...")
    logger.info("Weather API extraction started")

    with concurrent.futures.ThreadPoolExecutor(10) as executor:

        results = executor.map(
                    api_call,
                    df["pincode"],
                    df["latitude"],
                    df["longitude"]
                )      
        try:
            weather_api_json = [
                result
                for result in tqdm(
                    results,
                    total=len(df),
                    desc="Fetching Weather Data"
                )
                if result is not None]
        
        except Exception as e:
            logger.exception(f"Worker thread failed: {e}")

    return weather_api_json
```

