# charge_threshold
Set a charge threshold for asus laptop in ubuntu
This only works if your computer hardware support it, go to MyASUS app to check this feature 

# check the battery name 
the following command will return your battery name, mine is BAT0
ls /sys/class/power_supply

# create a systemd service to set the battery charge stop threshold on boot
Before creating the systemd service, check if your laptop actually has charge_control_end_threshold in /sys/class/power_supply/BAT* (without it, this won't work):
ls /sys/class/power_supply/BAT*/charge_control_end_threshold
continue if it returns the path to charge_control_end_threshold
now create a file called battery-charge-threshold.service in /etc/systemd/system by the following command
sudo editor /etc/systemd/system/battery-charge-threshold.service
in the file, paste the following script

[Unit]
Description=Set the battery charge threshold
After=multi-user.target
StartLimitBurst=0

[Service]
Type=oneshot
Restart=on-failure
ExecStart=/bin/bash -c 'echo CHARGE_STOP_THRESHOLD > /sys/class/power_supply/BATTERY_NAME/charge_control_end_threshold'

[Install]
WantedBy=multi-user.target

Here, change BATTERY_NAME with the name of the battery (mine is BAT0), and CHARGE_STOP_THRESHOLD with the battery charge stop threshold you want

Remember that some systems' battery can only be set to some specific value as 60, 80, 100

# Enable and start the battery-charge-threshold systemd service
sudo systemctl enable battery-charge-threshold.service
sudo systemctl start battery-charge-threshold.service

# If you want to change the battery charge stop threshold level
edit the /etc/systemd/system/battery-charge-threshold.service file, change the number 
restart the systemd service using the following commands:

sudo systemctl daemon-reload
sudo systemctl restart battery-charge-threshold.service

# Verify that the battery charge stop threshold is working
cat /sys/class/power_supply/BATTERY_NAME/status





