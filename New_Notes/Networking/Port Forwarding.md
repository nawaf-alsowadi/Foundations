Used to expose services to internet. Any access to any service in the internet need the port number while trying to accessing it.
https://www.google.com
is actually
https://www.google.com:443
bur hidden using the browser 
so if the website is hosted on non-standard port we must specify the port number.
https://www.google.com:8080

Th example below show that only the two devices can access the web services since **they can reach the private IP address**
![[326ef12878c2f669ad2374dba3635a44.svg]]
However, in order to expose the service to the internet, we will have to use the public IP and apply port forwarding.
![[eb63570eb9f31d26ebd8207ec08058bc.svg]]
This is configured in the router level as the following example:
![[Pasted image 20250714180756.png]]

If we have multiple web servers/we want to enable ssh to multiple devices:
- Use different external ports (internal port will remain same):
	- My.Public.IP:8080 web1
	- My.Public.IP:8081 web2
- Use Reverse Proxy (from outside to inside, proxy from inside to outside) to rout to the specific host based on the query.
	- `site1.example.com` → `192.168.1.10:80`
	- `site2.example.com` → `192.168.1.11:80`
```
server {
    listen 80;
    server_name site1.example.com;
    location / {
        proxy_pass http://192.168.1.10;
    }
}
server {
    listen 80;
    server_name site2.example.com;
    location / {
        proxy_pass http://192.168.1.11;
    }
}

```