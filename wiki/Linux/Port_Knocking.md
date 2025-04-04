# **Port knocking**

Port knocking is a simple concept, it’s like you invite friends at home, you tell them, when you arrive, you ring twice *short*, then once *long* on the bell, so I that can recognize you then open the door, otherwise it’s a false alarm.

![Untitled](Port%20knocking%205390b3be3d874d80a2188bc567f888a3/Untitled.png)

![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FBOHa9o1IO8VF4oJWZDmo%2Fuploads%2FtfM4mZT79dTvA0ckEx6t%2Fimage.png?alt=media&token=2ae3bf3e-1dda-4707-b9a4-c734ab5bd983)

From this step, we have two methods, first one, passing by **IPtables**, second one using **Knockd** package (simple, which I recommend). I’ll go through both of’em.

## **Knockd**

**Install & Configure Knockd**

```bash
sudo apt install knockd
```

Then editing the file `knockd.conf`

```powershell
sudo nano /etc/knockd.conf
```

Here in our example, by “knocking” (send SYN packet) on the ports : 7777, 8888 then 9999 the port of SSH is gonna open. Same goes for closing it.

![Untitled](Port%20knocking%205390b3be3d874d80a2188bc567f888a3/Untitled%201.png)

![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FBOHa9o1IO8VF4oJWZDmo%2Fuploads%2FuJQTTjTmmtMfc0PUVcBn%2Fimage.png?alt=media&token=ae5d1cb7-a216-4707-86a0-2194ac4f5e8e)

Next step, we edit the “**/etc/default/knockd**”

```bash
sudo nano /etc/default/knockd
```

Look for the line : “START_KNOCKD”, uncomment it, then set the value to **1**

As for line “KNOCKD_OPTS” we set its value the active interface in our machine. We could use **`ifconfig`** or **`tcpdump -D`**.

We can now, start, enable and check the status of **knockd**

```bash
sudo systemctl start knockd && sudo systemctl enable knockd && sudo systemctl status knockd
```

Lets jump to our client machine to try to connect to the server via SSH. We’ll get a connection timeout error!

![Untitled](Port%20knocking%205390b3be3d874d80a2188bc567f888a3/Untitled%202.png)

**Configure the knock client**

```bash
sudo apt install knockd
```

```bash
knock -v <Server IP Address> <Sequence>
```

![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FBOHa9o1IO8VF4oJWZDmo%2Fuploads%2FQjZI6FiP76mczu1ZQqnU%2Fimage.png?alt=media&token=61142529-c015-41d8-8c91-6fefc40a4c68)

![Untitled](Port%20knocking%205390b3be3d874d80a2188bc567f888a3/Untitled%203.png)

As you can see, after knocking on the correct ports, the SSH service is available to our IP!

# **References**

[Sicurezza/PortKnocking - Wiki di ubuntu-it](https://wiki.ubuntu-it.org/Sicurezza/PortKnocking)
[Port knockingMedium](https://medium.com/@reotmani/port-knocking-dbe6d8aaeb9)
