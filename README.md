import socket
import threading
import tkinter as tk
from tkinter import scrolledtext

common_ports = {
    21: "FTP", 22: "SSH", 23: "Telnet", 25: "SMTP",
    53: "DNS", 80: "HTTP", 110: "POP3", 143: "IMAP",
    443: "HTTPS", 3306: "MySQL", 8080: "HTTP Proxy"
}

def scan(target, ports, output_box):
    output_box.insert(tk.END, f"Scanning {target}...\n")
    
    def scan_port(port):
        try:
            s = socket.socket()
            s.settimeout(1)
            s.connect((target, port))
            service = common_ports.get(port, "Unknown")
            output_box.insert(tk.END, f"[OPEN] Port {port} ({service})\n")
            s.close()
        except:
            pass

    for port in ports:
        threading.Thread(target=scan_port, args=(port,)).start()

def start_scan():
    target = target_entry.get()
    ports_input = ports_entry.get()

    if ports_input.strip() == "":
        ports = list(common_ports.keys())
    else:
        ports = [int(p.strip()) for p in ports_input.split(",")]

    output_box.delete(1.0, tk.END)
    scan(target, ports, output_box)

# GUI
window = tk.Tk()
window.title("Python Port Scanner")

tk.Label(window, text="Target:").pack()
target_entry = tk.Entry(window, width=40)
target_entry.pack()

tk.Label(window, text="Ports (comma-separated or blank):").pack()
ports_entry = tk.Entry(window, width=40)
ports_entry.pack()

tk.Button(window, text="Scan", command=start_scan).pack(pady=5)

output_box = scrolledtext.ScrolledText(window, width=60, height=20)
output_box.pack()

window.mainloop()
