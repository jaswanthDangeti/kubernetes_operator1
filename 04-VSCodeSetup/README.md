## VSCode Setup for Operator Development

### Install VSCode

### Enable SSH Password Login for Root User
```
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config

sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config

service sshd restart
```

### Configure VSCode

#### 1. Open VSCode 
#### 2. Install SSH Remote Extension
#### 3. Close & ReOpen VSCode 
#### 4. Click on Left Buttom Connect [ >< ] Button to confiure SSH Profile

#### 5. Connect to Host -> root@172.31.0.100 -> Add to ssh_config 
#### 5. Click on Left Buttom Connect [ >< ] Button -> Connect to Host -> 172.31.0.100 -> Password -> Openfolder. 