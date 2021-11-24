# Installing PufferPanel (And DuckDNS. And Docker. And Portainer.)
1. Install Ubuntu Server from https://ubuntu.com/download/server
    - Select option 2
    - Burn it to a USB drive using Rufus (https://rufus.ie/)
    - Plug the USB into the PC and boot to it
    - Install
    - Deselect the LVM group option when selecting the hard drive
    - Enable OpenSSH Server
    - After it installs (let it install security updates), sign in and type "ip addr". Write down the IP address that starts with something like "192.168.1."
2. SSH Into the server
    - Install Windows Terminal on your PC. With this, you can right click inside the window and it will paste so you don't have to press Ctrl+V (Pre-installed on Windows 11, use this link on Windows 10: https://www.microsoft.com/store/productId/9N0DX20HK701)
    - On your pc, type in "ssh username@ip address" (Where username is the username you selected when installing and ip address is the ip address you obtained from the server)
    - When it asks about a fingerprint, type "y" and press enter
    - Put in your password and press enter
3. Now you have a few commands to run! (This is when it gets more technical)
    - sudo apt install default-jdk
        - You'll have to put in your password again. You might have to press "y" and hit enter when it says it will take however much storage space.
    - curl -s https://packagecloud.io/install/repositories/pufferpanel/pufferpanel/script.deb.sh | sudo bash
    - sudo apt update
    - sudo apt-get install pufferpanel
    - sudo systemctl enable pufferpanel
4. Pufferpanel is now installed, but you still have quite a bit to do before it can be used outside your network.
    - Go to http://www.duckdns.org/ and sign in
    - Make a domain with whatever you want
    - Click on install at the top
    - Select your domain at the bottom (linux cron is the right option and pre-selected)
    - Follow those steps
5. If you don't want to deal with all of that and want to make your server even more useful, you can install docker and portainer. This will make the DuckDNS setup way easier and there's a bunch of stuff you can use docker for!
    - Run this command (copy the whole thing):
    sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
    - Run this command:
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    - Run this command:
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    - Run this command:
    sudo apt-get update
    - Run this command:
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    - Run this command:
    sudo usermod -aG docker $USER
    - At this point, you have installed Docker. Run "logout" and run the SSH command again to get back into the server. 
6. Now to install Portainer!
    - This is gonna be a few commands again:
    docker volume create portainer_data
    - Second command (copy the whole thing)
    docker run -d -p 8000:8000 -p 943:9443 --name portainer \
        --restart=always \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v portainer_data:/data
        cr.portainer.io/portainer/portainer-ce:2.9.3
    - You now have Portianer installed. Go to the server's ip address followed by :9443 and you can access it. Make a user and login
7. DuckDNS Setup
    - I don't feel like explaining this one so I'm gonna just link a video: https://www.youtube.com/watch?v=bVmUV1G5wpI
8. You're at the home stretch! Now you just have to modify your router settings. That will be fun, right?
    - I have no idea what type of router you have so... I'll just tell you the goal.
        - Give the server a static local IP address
        - Forward ports 9443, 8080, and 22
        - Forward port 25565 over both TCP and UDP
9. Home stretch! Now you just have to make a user for pufferpanel.
    - pufferpanel user add
        - Put in your email and password and stuff. Make sure to press "y" when it asks you if you want the user to be an admin.
10. If I didn't miss anything, you should be done now! Have fun.
