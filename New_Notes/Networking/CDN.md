Content Delivery Network (CDN) is a geographically distributed group of servers that work together to **provide fast delivery** of Internet content such as websites resources ([[HTML]], [[JavaScript]], CSS, images, ...etc.).

![[Pasted image 20250819184634.jpg]]

Steps:
- CDN copies the pages of a website to a network of servers that are spread out at geographically different locations
- Each server cache the contents of the website
- When a user requests a webpage that is part of a content delivery network, the CDN will redirect the request
	- from the originating site’s server
	- to a server in the CDN that is closest to the user
- Server deliver the cached content.
- In case the content is not cached, CDN will communicate with the originating server to deliver

Same is used for any internet content (e.g., sharing files). 