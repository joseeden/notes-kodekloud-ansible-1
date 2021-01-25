
#******************************************************************
# installing-gui-change-hostname.sh
#
# Since I'm using EC2 instances, I decided toc reate this script 
# which will change the hostname and install the GUI
#******************************************************************


#------------------------------------------------------------------------

# creating same user on all three linux machines and setting user as reboot

sudo useradd -m -G root eden
sudo passwd eden
#------------------------------------------------------------------------

# This is for Ubuntu setup

# This installs GUI
sudo apt update -y  
sudo apt upgrade -y
sudo sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo /etc/init.d/ssh restart

sudo apt install -y xrdp xfce4 xfce4-goodies tightvncserver
sudo echo xfce4-session > /home/ubuntu/.xsession  
sudo cp /home/ubuntu/.xsession /etc/skel
sudo sed -i '0,/-1/s//ask-1/' /etc/xrdp/xrdp.ini
sudo service xrdp restart

# Installs firefox browser
sudo apt-get install -y firefox


# This changes the hostname for master
sudo sed -i "s/.*/master/" /etc/hostname
sudo sed -i "s/localhost/master/" /etc/hosts
sudo hostname master

sudo /etc/init.d/ssh restart
sudo reboot


# This changes the hostname for host-ubuntu
sudo sed -i "s/.*/host-ubuntu/" /etc/hostname
sudo sed -i "s/localhost/host-ubuntu/" /etc/hosts
sudo hostname host-ubuntu

sudo /etc/init.d/ssh restart
sudo reboot

#------------------------------------------------------------------------

# This is for RHEL/CentOS setup

# This installs GUI
sudo yum update -y  
sudo yum upgrade -y
sudo sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# Installs firefox browser
sudo yum install -y firefox


# This changes the hostname
sudo hostnamectl set-hostname host-centos
sudo sed -i "s/localhost/host-centos/" /etc/hosts
sudo reboot