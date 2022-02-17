# bcc2telegraf
This integration sends ebpf-based bcc histogram metrics (https://github.com/iovisor/bcc)  to telegraf daemon (https://github.com/influxdata/telegraf)

Original bcc/table.py v.0.10 just printed histograms to stdout.

This extension instead creates a memory buffer full of text lines formatted to the requirements of telegraf line protocol and sends it by http to telegraf url .

You may create some function which extends tag1 with some other tags, based on the histogram bucket struct key described in ebpf C program
e.g. your_function returns "8.8.8.8,cloud=GCP" 
pass this function to this API: send_log2_hist (... bucket_fn=your_function...)
and telegraf line will contain "... tag1=8.8.8.8,cloud=GCP..."

Usage: 
1.set TELEGRAF_URL environment variable to your telegraf service 
2.copy a python-based bcc program
3.edit it, adding an
          import bcc2telegraf
    and replace instances of 
          dist.print_log2_hist(label...)
    with 
          dist.send_log2_hist(label...)
    or with 
          dist.send_log2_hist(label,bucket_fn=your_function...)
 4. maybe create a systemctl-managed service out of that program 
