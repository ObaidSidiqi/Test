orders_file = "orders.txt"
logs_file = "logs.txt"
output_file = "sorted_logs.txt"

# Load order numbers
with open(orders_file) as f:
    order_ids = [line.strip() for line in f if line.strip()]

# Read all logs
with open(logs_file) as f:
    logs = f.readlines()

# Organize logs by order
sorted_logs = {order: [] for order in order_ids}
for line in logs:
    for order in order_ids:
        if order in line:
            sorted_logs[order].append(line)
            break  # Optional: stop after first match

# Write to output file
with open(output_file, "w") as f:
    for order in order_ids:
        f.write(f"===== {order} =====\n")
        if sorted_logs[order]:
            f.writelines(sorted_logs[order])
        else:
            f.write("No output found for this order.\n")
        f.write("\n")
