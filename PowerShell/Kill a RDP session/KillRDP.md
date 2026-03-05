# Kill a RDP session

Especially when using CyberArk for access to servers via RDP, these sessions can timeout and enter in a disconnected state. These disconnected sessions needs to be logged off before the specific user can log on to the server again.

To list all RDP session on the server
```powershell
PS C:\Windows\system32> quser
 USERNAME              SESSIONNAME        ID STATE   IDLE TIME  LOGON TIME
>SomeUser              console             1 Active      none   8/16/2015 5:29 PM
```

If the session has state “Disc”, then they are disconnected and can be removed. The session with a “>” in front of the username is your own session, so don’t remove that !!

To remove a RDP session:
```powershell
PS C:\Windows\system32> logoff <sessionID> 
```
