Fix for https.server AttributeError in Python 3.12+

This repository contains a patched version of the https.server module. The original module relied on the ssl.wrap_socket() function, which was deprecated in Python 3.7 and officially removed in Python 3.12.
The Issue

When running the original module on Python 3.12, you will encounter the following error: AttributeError: module 'ssl' has no attribute 'wrap_socket'

This occurs because Python 3.12 requires the use of ssl.SSLContext to handle TLS/SSL connections, as the legacy top-level wrapper is no longer supported for security reasons.
The Fix

The ThreadingHTTPSServer class has been updated to use the modern SSLContext API.
Before (Legacy/Broken)
Python

self.socket = ssl.wrap_socket(
    self.socket, 
    server_side=True, 
    certfile=self.cert_path
)

After (Python 3.12 Compatible)
Python

# Initialize a modern server-side SSL context
context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
context.load_cert_chain(certfile=self.cert_path)

# Wrap the socket using the context
self.socket = context.wrap_socket(
    self.socket, 
    server_side=True
)

Installation & Usage

    Clone the fix:
    Bash

git clone https://github.com/yourusername/https-server-fix.git
cd https-server-fix

Run the server: Place your cert.pem in the directory and run:
Bash

    python3 -m https.server --cert cert.pem

Key Benefits of the Update

    Security: SSLContext(ssl.PROTOCOL_TLS_SERVER) automatically enables secure defaults, such as disabling insecure protocols (SSLv2/v3).

    Future-Proof: Adheres to the PEP 543 standard for TLS in Python.

    Compatibility: Works on Python 3.10, 3.11, and 3.12+.
