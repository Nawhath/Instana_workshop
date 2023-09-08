# Access VM using Window PowerShell with ssh key


1. Download the ssh keys from the Box into your local Download folder.

<picture>
  <img alt="image" src="./assets/images/winDownload.png">
</picture>

2. Check IP Address for the "Manage-From" host.

<picture>
  <img alt="image" src="./assets/images/manageFromIP.png">
</picture>

3. Launch PowerShell

<picture>
  <img alt="image" src="./assets/images/lunchPowerShell.png">
</picture>

4. Enter ssh command as follow:

```sh
ssh -i "C:\Users\<username>\Downloads\Manage-From-Server-ssh-key_ID0.pem" -p 2223 itzuser@52.118.165.240
```
<picture>
  <img alt="image" src="./assets/images/winSSH.png">
</picture>

