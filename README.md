# HUB – Home Unified with Berry

![HUB](img/logo_medium.png)

A raspberry pi compose stack for home automation.

## Features

- Home Assistant - used for home automation
- SWAG (Nginx Proxy Manager) - used for exposing services to the internet
- Portainer - **Isolated** - used for managing docker containers
- Watchtower - used for auto-updating docker containers
- WG-Easy (Wireguard) - used for VPN access to the network
- Speedtest Tracker - used for tracking internet speed

## Notes

- This stack is targeted for a Raspberry Pi, but can be used in any other system and adapted easily if needed.
- This stack assumes that you have a domain and a dynamic DNS service to update your IP address.
- Some features are optional and can be removed if you don't need them.
    - Speedtest Tracker - if you don't want to track your internet speed
    - SWAG - if you don't want to expose services to the internet
    - WG-Easy - if you don't want to access your network remotely via VPN
    - Watchtower - if you don't want to auto-update your containers

## Installation

1. Create tailscale account [here](https://tailscale.com/).
2. Install tailscale on Raspberry Pi following the instructions [here](https://tailscale.com/kb/1347/installation).
3. Start tailscale with

    ```bash
    sudo tailscale up
    ```

4. Authenticate your device by following the link provided in the terminal.
5. Once authenticated, verify that your device is connected by running

    ```bash
    tailscale status
    ```
   
6. If you want to access your devices via the Raspberry Pi, enable subnet routing by running

    ```bash
    sudo tailscale set --advertise-routes=192.0.2.0/24
    ```
    (replace `192.0.2.0/24` with your local network subnet)

7. Fork the repository & clone it
8. Copy `portainer/docker-compose.yml` to your server

    ```bash
    scp portainer/docker-compose.yml user@server:/path/to/stack
    ```

9. Start the stack with

    ```bash
    docker-compose up -d
    ```

10. In order to expose home assistant to the Internet you will need a Tailscale funnel

    Use the configuration [tailscale.json](tailscale/tailscale.json) as a starting point.

    Create a folder `/docker/ts-homeassistant` on your server and copy the file there.
    
    ```bash
    ssh user@server
    su
    mkdir -p /docker/ts-homeassistant
    exit
    ```
    
    ```bash
    scp tailscale/tailscale.json user@server:/docker/ts-homeassistant/tailscale.json
    ```

11. Go to the Tailscale admin console, navigate to "DNS" and enable "HTTPS Certificates".

12. Go to the Tailscale admin console, navigate to "Settings". On left select "Keys" and create an Auth Key with

13. Login to Portainer and setup a stack pointing to `hub/docker-compose.yml`

14. Configure the following environment variables required for the stack & deploy it:

     ```
     STACK_TIMEZONE=Europe/Lisbon
     SPEEDTEST_TRACKER_APP_KEY=base64:YWJjZDEyMzQ=
     DOCKER_INTERNAL_SUBNET=123.0.0.0/24
     TAILSCALE_AUTH_KEY=tskey-1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
     HOME_ASSISTANT_HOSTNAME=homeassistant
     ```

## Troubleshooting

- If you have issues with the bluetooth service, check the [Bluetooth](docs/bluetooth.md) documentation.

## License

This project is released under [MIT License](LICENSE).
