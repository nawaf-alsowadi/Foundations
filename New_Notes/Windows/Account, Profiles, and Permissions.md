
## Accounts & Profiles
Two type of accounts exist: Admin (**can make changes to the system**) & Standard User. Currently I'm Admin since I can add users
![[Pasted image 20260203193622.png]]
If user is created, the type of the account can be changed
![[Pasted image 20260203193901.png]]

When the user is created, a profile will be created under C:\Users\ andeach one of them will have the same folders initially. For example, all have:
- Desktop
- Documents
- Downloads
- Music
- Pictures

We can manage the users and their assigned groups by
![[Pasted image 20260203194423.png]]

Then we can see all the groups 
![[Pasted image 20260203194623.png]]

and users
![[Pasted image 20260203194831.png]]

*Note: In Windows, the user inherits the permissions of the group and can be assigned to multiple groups.*

## Permissions
When we log into our personnel machines, we login as Adminstrator. Before, when we do so, every program the admin runs, will have the full the admin privileges. Imagine what malware can do if we by mistake installed them :).

To prevent this, Microsoft introduced **User Account Control** (UAC) which allow the admin to log in with limited permissions and ask for approval every time a  admin action is required.

*Note: This started with the short lived Windows Vista and continued to the next versions*

> **Note:** By default, User Account Control (UAC) does not prompt for elevation when using the built-in local Administrator account, because it runs with full administrative privileges, unlike other administrator accounts which require approval for admin actions.
> ![[Pasted image 20260203201819.png]]


If we right click on a folder/file => properties => security: we can see the users and groups and their permissions on the folder/file.
![[Pasted image 20260203202418.png]]

If we are logged in as standard user in the same local machine, and we try to open a program on other local machine:
- We get a **shield icon** on that program![[Pasted image 20260203204016.png]]
- **Built-in Admin Credentials** to download it in the standard user profile![[Pasted image 20260203204032.png]]