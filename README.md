<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fail2Ban Installation on Amazon Linux 2023</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            line-height: 1.6;
            color: #333;
        }

        code {
            background-color: #f4f4f4;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-family: 'Courier New', Courier, monospace;
            padding: 4px;
            display: inline-block;
        }

        pre {
            background-color: #f4f4f4;
            border: 1px solid #ddd;
            border-radius: 4px;
            padding: 10px;
            overflow: auto;
        }

        h2 {
            color: #333;
            border-bottom: 2px solid #ddd;
            padding-bottom: 5px;
        }

        h3 {
            color: #444;
        }

        .prerequisite {
            color: #1e74b5;
        }

        .installation {
            color: #6c757d;
        }

        .configuration {
            color: #007bff;
        }

        .verification {
            color: #28a745;
        }

        .note {
            color: #ffc107;
        }
    </style>
</head>

<body>

    <h2>Fail2Ban Installation on Amazon Linux 2023 with Iptables and rsyslog</h2>

    <h3 class="prerequisite">Prerequisites</h3>

    <p class="note">Before proceeding with Fail2ban installation, ensure the following configurations:</p>

    <pre>
<code class="installation">1. <span class="configuration">SSH Configuration:</span>
   - Open the SSH configuration file:
     <span class="verification">sudo nano /etc/ssh/sshd_config</span>
   - Set password authentication to 'yes':
     <span class="verification">PasswordAuthentication yes</span>
   - If using public key authentication, ensure the following lines are not commented out:
     <span class="verification">PubkeyAuthentication yes</span>
   - Restart the SSH service:
     <span class="verification">sudo service sshd restart</span>

</code></pre>

    <h3 class="installation">Installation Steps</h3>

    <pre>
<code class="configuration">2. <span class="installation">Install Iptables:</span>
   <span class="verification">sudo dnf install iptables
   sudo iptables -L   # View current firewall chains
   iptables --version  # Check Iptables version</span>

3. <span class="installation">Install Fail2ban:</span>
   <span class="verification">sudo dnf install git
   sudo git clone https://github.com/fail2ban/fail2ban.git
   cd fail2ban
   sudo python3 setup.py install</span>

4. <span class="configuration">Configure Fail2ban Service:</span>
   <span class="verification">sudo cp /home/ec2-user/fail2ban-1.0.2/build/fail2ban.service /etc/systemd/system/fail2ban.service
   sudo nano /etc/systemd/system/fail2ban.service</span>
   Add the following line:
   <span class="verification">Environment="PYTHONPATH=/usr/local/lib/python3.9/site-packages"</span>
   <span class="verification">sudo systemctl enable --now fail2ban
   sudo systemctl start fail2ban
   sudo systemctl status fail2ban</span>

</code></pre>

    <pre>
<code class="configuration">5. <span class="installation">Install rsyslog:</span>
   <span class="verification">sudo dnf install rsyslog</span>

6. <span class="configuration">Configure rsyslog:</span>
   <span class="verification">sudo nano /etc/rsyslog.d/sshd_logs.conf</span>
   Add the line:
   <span class="verification">authpriv.*   /var/log/sshd.log</span>
   Restart rsyslog:
   <span class="verification">sudo systemctl restart rsyslog</span>

7. <span class="configuration">Modify sshd_config:</span>
   <span class="verification">sudo nano /etc/ssh/sshd_config</span>
   Add the lines:
   <span class="verification">SyslogFacility AUTHPRIV
   LogLevel INFO</span>
   Restart SSH and rsyslog:
   <span class="verification">sudo systemctl restart sshd
   sudo systemctl restart rsyslog</span>

8. <span class="configuration">Test Logging:</span>
   <span class="verification">sudo tail -f /var/log/sshd.log</span>

9. <span class="configuration">Edit Jail.local and Restart Fail2ban:</span>
   <span class="verification">sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
   sudo nano /etc/fail2ban/jail.local</span>
   Ensure backend is set to auto:
   <span class="verification">[INCLUDES]
   before = paths-fedora.conf</span>
   Restart Fail2ban:
   <span class="verification">sudo systemctl restart fail2ban
   sudo systemctl status fail2ban
   sudo fail2ban-client status sshd</span>

</code></pre>

    <h3 class="verification">Working Status</h3>

    <p class="note">Fail2ban should now be configured and actively monitoring for SSH login attempts based on the defined rules. Verify the status to ensure it is working correctly.</p>

    <p class="note"><strong>Note:</strong> Customize the README according to your specific needs and configurations.</p>

</body>

</html>
