A web server is a software that listens for incoming connections and uses [[HTTPS]] protocol to deliver the content. 

The most widely used softwares for web servers are:
- Apache
- Nginx
- NodeJS
- IIS

 A Web server delivers files from what's called its **root directory**, which is defined in the software settings. For example, **Nginx** and **Apache** share the same default location of **/var/www/html in Linux** operating systems, and **IIS** uses **C:\inetpub\wwwroot for the Windows** operating systems. For example, if you requested the file [http://www.example.com/picture.jpg](http://www.example.com/picture.jpg), it would send the file /var/www/html/picture.jpg from its local hard drive.

### Virtual Hosts
One web servers can host multiple different domain names of websites. In order to achieve this, we use virtual hosts. First, when the web server receive HTTP request, it checks the hostname in the [[HTTPS#Requests & Response]]] header, then accordingly foreward the request to the right virtual host.If no match was found, the default browser will be displayed (depending on the conf). 

Virtual Hosts can have their root directory mapped to different locations on the hard drive. For example, [one.com](http://one.com/) being mapped to /var/www/website_one/, and [two.com](http://two.com/) being mapped to /var/www/website_two/ and accordingly, the service will be delivered.

*Note: there's no limit to the number of different websites you can host on a web server.*


