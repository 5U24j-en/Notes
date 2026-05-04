

## Paths - pathlib

#### File Structure

- ![](../attachments/Screenshot%20from%202026-04-21%2017-57-03.png)

- #### `"__file__"`
	- The above variable  `__file__` is the path of _whatever file the code is currently written inside_
	- Created by Default


- #### main.py

	- Root DIR
	- ![](../attachments/Pasted%20image%2020260421175819.png)
	- ![](../attachments/Screenshot%20from%202026-04-21%2018-00-11.png)
	- @
		- ![](../attachments/Screenshot%20from%202026-04-21%2018-04-58.png)

```python
ROOT_DIR = Path(__file__).resolve().parent.parent.parent

WEATHER_DATA_DIR = ROOT_DIR / "data" / "bronze" / "weather"

def save_raw_weather(data: dict):

	timestamp = datetime.now(ZoneInfo("Asia/Kolkata")).strftime("%Y-%m-%d")
	file_path = WEATHER_DATA_DIR / f"weather_{timestamp}.json 
	WEATHER_DATA_DIR.mkdir(parents=True, exist_ok=True)
	
	with open(file_path, "w") as f:
		json.dump(data, f, indent=4)
	
	logger.info(f"Saved raw weather data to {file_path}")
```

**`WEATHER_DATA_DIR.mkdir(parents=True, exist_ok=True)`**  -> parents = True means that **IT WILL CREATE PARENT DIRECTORIES ALSO IF THE FILE DOES NOT EXIST**

- If **data/bronze/weather** - does not exist then it will create all the sub folder because **`parents=True`**
