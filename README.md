
```
import subprocess
import sys
from datetime import datetime

def run_command(cmd):
    try:
        result = subprocess.run(cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
        if result.stdout.strip():
            print(result.stdout)
        else:
            print("[INFO] No output returned.")
            if result.stderr:
                print("[STDERR]", result.stderr)
    except Exception as e:
        print(f"[ERROR] Failed to run command: {cmd}")
        print(str(e))

def main():
    if len(sys.argv) != 3:
        print("Usage: python3 adv_checker.py <adv|priceband|sov|pricelimit> <YYYY-MM-DD>")
        sys.exit(1)

    search_type = sys.argv[1].lower()
    date_str = sys.argv[2]

    try:
        date = datetime.strptime(date_str, "%Y-%m-%d")
    except ValueError:
        print("Date must be in YYYY-MM-DD format.")
        sys.exit(1)

    early = date.strftime("%m/%d/%Y") + ":00:00:00"
    late = date.strftime("%m/%d/%Y") + ":23:00:00"
    order_id = "ABFD1"  # Replace or pass this dynamically if needed

    search_query = f'"{order_id} source IN (*SHERLOCK*) | reverse" -sst adv -early {early} -late {late'

    grep_map = {
        "adv": 'grep "PASS:.*ADV"',
        "priceband": 'grep "PASS:\\[PriceBand"',
        "sov": 'grep "PASS:\\[SOV"',
        "pricelimit": 'grep -E "PASS:\\[SimpleAccess|PASS:\\[PriceBand"',
    }

    if search_type not in grep_map:
        print("Invalid search type. Choose from: adv, priceband, sov, pricelimit.")
        sys.exit(1)

    full_cmd = f'./splunk.py {search_query}" | {grep_map[search_type]}'
    print(f"[DEBUG] Running command:\n{full_cmd}\n")
    run_command(full_cmd)

if __name__ == "__main__":
    main()



```
