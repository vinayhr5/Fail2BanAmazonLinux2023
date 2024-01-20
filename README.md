Fail2Ban Installation Steps:-

**Before Proceeding with Fail2ban Installation;
 Validate below :- **

Setup the SSH system to allow password logins from sftp:

sudo nano /etc/ssh/sshd_config


Find the line for password authentication and set it to yes if you want to use password authentication. This should be off by default.

PasswordAuthentication yes


If using public key authentication ensure the following lines are not commented out.

PubkeyAuthentication yes

You will need to restart the sshd service:

sudo service sshd restart


**Steps to Install Fail2ban with Iptables in Amazon Linux 2023:-**

1. Install Iptables

# sudo dnf install iptables

To view your current 'firewall chains' you need to run the following command as root:

# sudo iptables -L

To Check Iptables Version

# iptables --version

iptables v1.8.8 (nf_tables)


2. Install Fail2ban -- sudo fail2ban-client version [1.1.0.dev1]

sudo dnf install git

sudo git clone https://github.com/fail2ban/fail2ban.git

cd fail2ban

sudo python3 setup.py install
----------------------------------------------------

sudo cp /home/ec2-user/fail2ban-1.0.2/build/fail2ban.service /etc/systemd/system/fail2ban.service  [Check location of fail2ban then copy]

Add:-

Environment="PYTHONPATH=/usr/local/lib/python3.9/site-packages"

sudo systemctl enable --now fail2ban

sudo systemctl start fail2ban

sudo systemctl status fail2ban


4. Install rsyslog

# Install Rocket-fast System for log processing (rsyslog):

sudo dnf install rsyslog


# Configure rsyslog:

Create a configuration file for rsyslog at /etc/rsyslog.d/sshd_logs.conf and add the following line:

sudo nano /etc/rsyslog.d/sshd_logs.conf

authpriv.*   /var/log/sshd.log


Restart rsyslog to apply the configuration changes:

sudo systemctl restart rsyslog


# Modify sshd_config:

Edit the SSH daemon configuration file (/etc/ssh/sshd_config) and add the following two lines:

sudo nano /etc/ssh/sshd_config

SyslogFacility AUTHPRIV

LogLevel INFO


sudo systemctl restart sshd
sudo systemctl restart rsyslog

# Test Logging:

Tail the new log file at /var/log/sshd.log and try logging in to generate logs:
sudo tail -f /var/log/sshd.log



5. Edit in Jail.local and restart fail2ban. [Other configurations in jail.local remains as previous]

sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local

sudo nano /etc/fail2ban/jail.local

[INCLUDES]

before = paths-fedora.conf

ensure backend is set to auto


sudo systemctl restart fail2ban
sudo systemctl status fail2ban
sudo fail2ban-client status sshd

**[WORKING]**
