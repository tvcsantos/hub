# Install Bluez

1. Download and install latest package

2. Check that it's been installed correctly

   `$ sudo systemctl status bluetooth`

3. Open `bluetooth.service` for edit with

   `$ sudo nano /lib/systemd/system/bluetooth.service`

4. Add `--experimental` at the end of the following line

   `ExecStart=/usr/local/libexec/bluetooth/bluetoothd --experimental`

5. Start bluetooth service

   `sudo systemctl start bluetooth`

6. Check that it's been started with the `--experimental` option

   `$ sudo systemctl status bluetooth` (if it isn't, restart the RPi)

7. Run `bluetoothctl`

   `$ sudo bluetoothctl`
    
   > ℹ️ If weird things happen (e.g. console not responding to keyboard, etc...)
   > execute `bluetoothd` try again and check if the error is gone.

8. Now inside `bluetoothctl`

   ```
   [bluetooth]# list  
   # lists the bluetooth interfaces

   [bluetooth]# show B8:27:EB:14:7E:35  
   # shows the B8:27:EB:14:7E:35 interface configuration

   [bluetooth]# power on
   # powers the interface if it isn't yet

   [bluetooth]# help
   # displays the help

   [bluetooth]# scan on
   # starts a scan of the surrounding bluetooth devices

   [bluetooth]# connect C0:28:8D:45:3D:79
   # connects to the C0:28:8D:45:3D:79 device
   # it can show a path for every characteristic : the dbus path
   ```
   
   > ℹ️ dbus is a "distributed communication bus". It allows processes
   > to "talk" to each other : it's a "generic" bus that anyone can send
   > datas to and also receive datas.
   >
   > Bluez creates a unique path in dbus for every service, characteristics,
   > descriptors, etc. You can interact with those objects on the dbus path.

To connect to a device with `bluetoothctl` follow this example:

```bash
bluetoothctl
devices
scan on
pair 34:88:5D:51:5A:95 # (34:88:5D:51:5A:95 is my device code,replace it with yours)
trust 34:88:5D:51:5A:95
connect 34:88:5D:51:5A:95
```

Original source: <https://bluez-cheat-sheet.readthedocs.io/en/latest/>
