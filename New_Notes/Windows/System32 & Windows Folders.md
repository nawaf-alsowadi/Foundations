# C:\Windows
C:\Windows folder is the root folder of the windows (equivalent to /root in Linux) and its folders like [[#C /Windows/System32]] contains **all OS files for the Windows + built-in apps executables + dll files + environment variables like %windir% (C:\WINDOWS) & %NUMBER_OF_PROCESSORS% (16 in my case) ...etc.** (doesn't have to reside in C drive, can reside in any folder). 
![[Pasted image 20260121232531.png]]

According to [Microsoft](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7.1) , " _Environment variables store information about the operating system environment. This information includes details such as the operating system path, the number of processors used by the operating system, and the location of temporary folders_ ".

Also, core folders are:
- SysWOW64
- WinSxS
- Temp
- Logs
# C:/Windows/System32

One of the folders called System32. 
![[Pasted image 20260113232944.png]]

This folder specifically store:
- **files of the OS
- **DLL files** like:
	- kernel32.dll
	- ntdll.dll
- **built in programs exe files** like:
	- cmd.exe
	- powershell.exe
	- lsass.exe
	- servicess.exe
- **drivers folder** (C:\Windows\System32\drivers) which has system files to communicate with the hardware
- **Windows Registry files** C:\Windows\System32\Config)

When we install application, the executable will be in /programs folder while its dll will be in the /System32.

In order to modify this folder, it requires Admin or trusted installer privileges and it is protected by Windows Resource Protection (WRP)
 _Note: System32 folder is important [even on 64-bit versions of Windows](https://www.howtogeek.com/326509/whats-the-difference-between-the-system32-and-syswow64-folders-in-windows/), where it still contains important system libraries and executables in 64-bit form._
 