# Installing PufferPanel (And DuckDNS. And Docker. And Portainer.) - 2 methods

Report issues if you find any!

## Table of Contents

1. [Prerequisites](#installing-prerequisites)
2. [Method 1: Using Ansible](#method-1-using-ansible)
3. [Method 2: Manual](#method-2-manual)
4. [Conclusion](#conclusion)

## Installing Prerequisites

1. Install Ubuntu Server from <https://ubuntu.com/download/server>
    - Select option 2
    - Burn it to a USB drive using [Rufus](https://rufus.ie/) if you're running Windows
    - Plug the USB into the PC and boot to it
    - Install
    - Deselect the LVM group option when selecting the hard drive
    - Enable OpenSSH Server
    - After it installs (let it install security updates), sign in and type "ip addr". Write down the IP address that starts with something like "192.168.1."
2. SSH Into the server
    - Install Windows Terminal on your PC. With this, you can right click inside the window and it will paste so you don't have to press Ctrl+V (Pre-installed on Windows 11, use this link on Windows 10: <https://www.microsoft.com/store/productId/9N0DX20HK701>)
    - On your pc, type in "ssh username@ip address" (Where username is the username you selected when installing and ip address is the ip address you obtained from the server)
    - When it asks about a fingerprint, type "y" and press enter
    - Put in your password and press enter

## Method 1: Using Ansible

1. Install Ansible
    - Ensure you have Python 3 installed. It should be installed by default on Ubuntu server, but make sure by running `python3 --version`.
    - Make sure `pip` is available
        - Run `python3 -m pip -V`. It should return something similar to `pip 21.0.1 from /usr/lib/python3.9/site-packages/pip (python 3.9)`, but it's fine if the version is different.
    - Install Ansible:
        `python3 -m pip install --user ansible`
    - Make sure the installation is running correctly by running `ansible --version`. If it doesn't work, try logging out and back in, then try again.
2. Install git
    - Run the command to install git for your OS (`sudo apt install git` on Ubuntu, `sudo dnf in git` on Fedora, `sudo pacman -S git` on Arch)
3. Clone the repository using git
    - Run the following:

    ```text
    git clone https://github.com/halfcyan/InstallPufferPanel
    cd InstallPufferPanel
    ```

4. Edit varaibles in `portainer.yml`
    - We're using DuckDNS as a Dymanic DNS provider, so you'll have to add your DuckDNS token to the `portainer.yml` file. You can get your token from <https://www.duckdns.org/domains>. Make an account if you don't have one, then add any subdomain you want. Add the token and subdomain to the respective `token` and `subdomain` variables at the top of the file. Additionally, add your username under the `username` variable.
5. Run the playbook
    - Run the following command:
        `ansible-playbook portainer.yml -i ./hosts --ask-pass  --ask-become-pass`

    - Enter your password if necessary
    - Once it finishes, open up a web browser and go to your server's IP address followed by :9443. This will open the Portainer dashboard. Create a username and password, then click local environment. This is how you will manage your Docker containers.
    - [Continue to the conclusion](#conclusion).

## Method 2: Manual

Use this method if you want to tweak anything within the installation

1. Now you have a few commands to run!
    - Install Java: `sudo apt install default-jdk`
    - Install PufferPanel:

        ```shell
        curl -s https://packagecloud.io/install/repositories/pufferpanel/pufferpanel/script.deb.sh | sudo bash
        sudo apt update
        sudo apt install pufferpanel
        sudo systemctl enable pufferpanel
        ```

2. Pufferpanel is now installed, but you still have quite a bit to do before it can be used outside your network.
    - Go to <http://www.duckdns.org/> and sign in
    - Make a domain with whatever you want
    - Click on install at the top
    - Select your domain at the bottom (linux cron is the right option and pre-selected)
    - Follow those steps
3. If you don't want to deal with all of that and want to make your server even more useful, you can install docker and portainer. This will make the DuckDNS setup way easier and there's a bunch of stuff you can use docker for!
    - Run this command (copy the whole thing):

        ```text
        sudo apt-get install \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
        ```

    - Run this command:
    curl -fsSL <https://download.docker.com/linux/ubuntu/gpg> | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    - Run this command:
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] <https://download.docker.com/linux/ubuntu> \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    - Run this command:
    sudo apt-get update
    - Run this command:
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    - Run this command:
    sudo usermod -aG docker $USER
    - At this point, you have installed Docker. Run "logout" and run the SSH command again to get back into the server.
4. Now to install Portainer!
    - This is gonna be a few commands again: \
    docker volume create portainer_data
    - Second command (copy the whole thing)

        ```text
        docker run -d -p 8000:8000 -p 9443:9443 --name portainer \ \
            --restart=always \ \
            -v /var/run/docker.sock:/var/run/docker.sock \ \
            -v portainer_data:/data \ \
            cr.portainer.io/portainer/portainer-ce:latest
        ```

    - You now have Portianer installed. Go to the server's ip address followed by :9443 and you can access it. Make a user and login
    - Select local docker install if the option pops up
    - Click on the "My account" button on the top right and change your theme to dark. This is essential.
5. DuckDNS Setup
    - I don't feel like explaining this one so I'm gonna just link a video: <https://www.youtube.com/watch?v=bVmUV1G5wpI>
6. [Continue to the conclusion](#conclusion).

## Conclusion

1. You're at the home stretch! Now you just have to modify your router settings. That will be fun, right?
    - I have no idea what type of router you have so I'll just tell you the goal.
        - Give the server a static local IP address
        - Forward port 8080 if you want to allow outside users to use the web panel
        - Forward port 25565 over both TCP and UDP
2. Home stretch! Now you just have to make a user for pufferpanel.
    - pufferpanel user add
        - Put in your email and password and stuff. Make sure to press "y" when it asks you if you want the user to be an admin.
3. Installing the server to the pufferpanel
    - If you setup that static IP properly earlier, you should remember what the IP address of the machine is. Go to that ip followed by :8080. Sign in using the pufferpanel user you created earlier.
    - Click the light bulb in the top right to stop the site from searing your eyes
    - Go to the templates tab. Add the templates for FTB Launcher, Fabric, MinecraftForge 1.17+, MinecraftForge 1.16 or older, Paper, Spigot, and Vanilla.
    - Go to the servers tab.
    - Click on the plus button
    - The following steps are for FTB University 1.16 as an example:
        - Select FTB Launcher - Minecraft
        - Do whatever server name, keep node as localnode, and environment as standard.
        - Press next on users
        - Turn on the EULA Agreement toggle
        - Set the memory to 16384 (that's 16GB)
        - Set the modpack ID to 90
        - Set the Modpack Version ID to 2087
4. If I didn't miss anything, you should be done now! Have fun.
