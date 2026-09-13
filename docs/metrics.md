# Metrics

For system metrics use `glances`. For that install it with:

```bash
sudo apt install glances
sudo mkdir -p /usr/lib/python3/dist-packages/glances/outputs/static/public
```

Then run it as a service with:

```bash
sudo nano /etc/systemd/system/glances.service
```

Add the following content to the file:

```text
[Unit]
Description=Glances in web server mode
After=network.target

[Service]
ExecStart=/usr/bin/glances -w
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Save and exit the file. Then enable and start the service with:

```bash
sudo systemctl enable glances
sudo systemctl start glances
```
