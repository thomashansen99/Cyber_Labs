# Day 1 Basic Capture  
  
Filters used:  
dns  
tcp.port==80  
tcp.port==443  
  
DNS Query - My laptop (192.168.0.82) -> local DNS server/router (192.168.0.1)  
Asking for the IP address of [wikipedia.org](http://wikipedia.org) (UDP port 53)  
  
TLS ServerHello - [example.com](http://example.com) -> my laptop (TCP 443)  
The server responds to start the HTTPS handshake and negotiate encryption  
  
TLS ClientHello - my laptop -> 198.35.26.112 ([upload.wikipedia.org](http://upload.wikipedia.org))  
Start of the HTTPS handshake. My laptop tells the server it wants to connect securely to [upload.wikipedia.org](http://upload.wikipedia.org) (shown in the SNI)  
