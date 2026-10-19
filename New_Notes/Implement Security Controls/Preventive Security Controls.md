Stop physically or logically unauthorized access. Operate before attack. For example, stop access to certain resource for unauthorized entities.

## Connect To Shared File
PC10 is joined to a domain controlled by Domain Controller (DC10) which is a windows server. The following resource should only be accessed by a domain and local admins.
![[Pasted image 20260224191547.png]]

## Change Permission For Shared Files in The Network
Steps:
	1- Domain Controller Server
	2- Server Manager
	3- File and Storage Srvices
	4- Shares
	5- Folder
	6- Right Click => Properties
	7- Permissions
	8- Custom Permissions
	9- Disable Inheritance (for the group not to inherit the permissions)
	10- Click the group => Remove
![[Pasted image 20260224192527.png]]

![[Pasted image 20260224192619.png]]

![[Pasted image 20260224192641.png]]
