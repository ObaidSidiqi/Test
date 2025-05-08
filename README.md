```
import subprocess

def check_order_log(order_id: str, date: str, search_type: str) -> str:
    """
    Check order logs using the splunk.py command format for a specific order ID and date.

    Args:
        order_id (str): The root order ID to search for.
        date (str): Date in format YYYY-MM-DD
        search_type (str): One of 'adv', 'priceband', 'sov', or 'pricelimit'

    Returns:
        str: Result of the grep search or error message.
    """
    search_type = search_type.lower()

    early = f"{date}:00:00:00"
    late = f"{date}:23:00:00"

    # Base command template
    base_cmd = f'./splunk.py "{order_id} source IN (*SHERLOCK*)" | reverse --sst adv -early {early} -late {late}'

    grep_patterns = {
        "adv": "grep ADV",
        "priceband": "grep PriceBand",
        "sov": "grep SOV",
        "pricelimit": "grep -E 'SimpleAccessLimit|PriceBand'"
    }

    if search_type not in grep_patterns:
        raise ValueError(f"Unsupported search type: {search_type}")

    full_command = f'{base_cmd} | {grep_patterns[search_type]}'

    try:
        output = subprocess.check_output(full_command, shell=True, cwd="/eqstore/scripts/ed/dzachari/my_py_scripts/splunk_tool", text=True)
        return output.strip()
    except subprocess.CalledProcessError as e:
        return f"No matching logs found or an error occurred:\n{e.output.strip()}"

```
