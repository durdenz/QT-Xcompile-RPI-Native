# Steps to get correct version of cmake:
1. Remove incorrect version of cmake:
`sudo apt remove cmake -y`
2. If you are using a minimal Ubuntu image or a Docker image, you may need to install the following packages:
```bash
sudo apt-get update
```
```bash
sudo apt-get install ca-certificates gpg wget
```
3. If the kitware-archive-keyring package has not been installed previously, manually obtain a copy of our signing key:
```bash
test -f /usr/share/doc/kitware-archive-keyring/copyright ||
wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null
```
4. Add the repository to your sources list and update.
```bash
echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main' | sudo tee /etc/apt/sources.list.d/kitware.list >/dev/null
```
```bash
sudo apt-get update
```
5. If the kitware-archive-keyring package has not been installed previously, remove the manually obtained signed key to make room for the package:
```bash
test -f /usr/share/doc/kitware-archive-keyring/copyright ||
sudo rm /usr/share/keyrings/kitware-archive-keyring.gpg
```
6. Install the kitware-archive-keyring package to ensure that your keyring stays up to date as we rotate our keys:
```bash
sudo apt-get install kitware-archive-keyring
```
7. Now install cmake:
```bash
sudo apt-get install cmake
```

# References:
1. https://apt.kitware.com/ <--This works-->
2. https://stackoverflow.com/questions/49859457/how-to-reinstall-the-latest-cmake-version
3. https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04
4. https://stackoverflow.com/questions/67541374/nvm-getting-permission-denied-with-nvm-install-command
5. 

