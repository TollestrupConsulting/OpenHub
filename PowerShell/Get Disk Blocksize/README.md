# Get Disk blocksize

To get blocksize on all disks on a windows device, following PowerShell command is useful:

```powershell
Get-CimInstance -ClassName Win32_Volume | Select-Object Label, DriveLetter, BlockSize | Format-Table -AutoSize
```
