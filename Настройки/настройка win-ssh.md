**windows(powershell)**:
```sh
ssh-keygen -t rsa -b 2048 -f $env:USERPROFILE\.ssh\id_rsa
$publicKey = Get-Content -Path "$env:USERPROFILE\.ssh\id_rsa.pub"
$sshCommand = "echo $publicKey >> .ssh/authorized_keys"
ssh user@remote_host "$sshCommand"
```