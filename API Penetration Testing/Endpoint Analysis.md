**MITM**
Capture web-application behaviour by proxing traffic with '_mitmweb_'.
```bash
mitmweb #proxy all egress traffic on port 8080 and show captured traffic on web-gui on localhost:8081
```

Same kind of capture can be done with #postman with foxy-proxy swinging traffic to postman.

At the end we will be able to download the file of the whole capture.
```bash
sudo mitmproxy2swagger -i /path/capture-file -o output-file.yml -p  http://localhost -f flow --example
```

The _output-file.yml_ can be used in https://editor.swagger.io to build a ressemble of API documentation to clearly visualize and reverse-engineer the API to build an attack. It is important to have tried every possible aspect to of the webapp during the capture to gather the most out of it.

