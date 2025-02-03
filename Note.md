## allow ping, ubuntu issue
sudo setcap cap_net_raw+ep /bin/ping
OR
sudo setcap 'cap_net_admin,cap_net_raw+ep' $(which ping)
OR
sudo sysctl -w net.ipv4.ping_group_range="0 1000"



## Install requirement dependencies and install docker
sudo apt install apt-transport-https ca-certificates curl software-properties-common util-linux open-iscsi nfs-common binfmt-support qemu-user-static iptables nmap -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

wget -qO- https://get.docker.com/ | sh

sudo usermod -aG docker $USER


