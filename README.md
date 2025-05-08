
```
import subprocess

def check_order_log(order_id, check_type, log_date):
    # Mapping search types to grep filters
    grep_filters = {
        "adv": r'PASS:.*ADV',
        "priceband": r'PASS:\[PriceBand',
        "sov": r'PASS:\[SOV',
        "pricelimit": r'PASS:\[SimpleAccess|PASS:\[PriceBand'
    }

    if check_type not in grep_filters:
        print("Invalid check type. Choose from: adv, priceband, sov, pricelimit")
        return

    # SSH command configuration
    user = "tpettp"
    host = "host"  # Replace with actual host
    remote_path = "/eqstore/scripts/ed/dzachari/my_py_scripts/splunk_tool"
    splunk_script = f"./splunk.py \"{order_id}\" | grep -E \"{grep_filters[check_type]}\""

    ssh_command = f'''
        ssh -q -o ConnectTimeout=3 {user}@{host} "
            . .DZ && \
            cd {remote_path} && \
            {splunk_script}
        "
    '''

    print(f"Running log check for Order ID: {order_id} on {log_date} for {check_type.upper()}...\n")
    try:
        result = subprocess.check_output(ssh_command, shell=True, text=True)
        print("----- Output -----")
        print(result if result else "No matching logs found.")
    except subprocess.CalledProcessError as e:
        print("Error during execution:")
        print(e.output)

# Example usage
if __name__ == "__main__":
    order_id = input("Enter Root Order ID: ").strip()
    check_type = input("Enter check type (adv, priceband, sov, pricelimit): ").strip().lower()
    log_date = input("Enter date (YYYY-MM-DD): ").strip()  # Optional use in expanded version
    check_order_log(order_id, check_type, log_date)


```
