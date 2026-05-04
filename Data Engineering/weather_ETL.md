
- ## Key Learning

	- #### ISSUE 1 
		- ROOT_DIR = Path(__file__).resolve().parent.parent
			LOG_DIR = ROOT_DIR.joinpath("logs")
			
			LOG_DIR.mkdir(parents=True, exist_ok=True)
			log_file = LOG_DIR.joinpath("weather_pipeline.log")
		
			logging.basicConfig(
			    level=logging.INFO,
			    format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
			    filename=log_file,
			    filemode="a"
			)
	
		- my log files are generating in the src folder any idea why?

	- #### REASON
		- This is a classic Python logging gotcha! Your `pathlib` code in `main.py` is actually **100% correct**.

			- The reason the log file is showing up in your `src` folder comes down to a sneaky combination of how Python handles imports and the "First Come, First Served" rule of the logging library.
			
			##### The Culprit: "First Come, First Served"
			
			- `logging.basicConfig()` is designed to only execute **once**. As soon as any file calls it, the logging destination is locked in for the entire run. Any later calls to `basicConfig` are completely ignored by Python.
			- When you refactored `main.py`, you likely placed your `import` statements at the top of the file (which is standard practice):

```python
import logging
from pathlib import Path
from bronze.pincode_csv_data_extract import extract_data  # <-- Python jumps into this file first!
from silver.weather_api_util import weather_api_call

# Your pathlib logic is down here...
ROOT_DIR = Path(__file__).resolve().parent.parent 
```

			- When Python hits that first import line, it pauses `main.py` and runs everything inside `pincode_csv_data_extract.py`.

			- Because those child files still have this code inside them:

```python
logging.basicConfig(
    filename="weather_pipeline.log", # Hardcoded string!
    filemode="a"
)
```

...that child file "wins." It sets the global logging configuration before your `pathlib` code in `main.py` even gets a chance to run.

Because `filename="weather_pipeline.log"` is a relative string, Python simply drops the file into whatever folder your terminal is currently sitting in (your Current Working Directory). If you are running the script from inside the `src` folder, that is exactly where the log file gets created.

### The Fix

To allow your robust `main.py` configuration to take control, you need to strip the logging configuration out of the child files.

**Step 1:** Open `pincode_csv_data_extract.py`, `api_data_extract.py`, `weather_api_util.py`, and `aggregation.py`.

**Step 2:** Delete the `logging.basicConfig(...)` block completely from all of them.

The top of those child files should ONLY look like this:

```python
import logging
import pandas as pd # etc...

logger = logging.getLogger(__name__)

def your_function():
    ...
```

Once you delete those rogue configs, Python will safely pass over the imports, execute your `pathlib` logic in `main.py`, run `basicConfig` with your absolute path, and properly route your logs to the `Weather_ETL/logs/` directory!
