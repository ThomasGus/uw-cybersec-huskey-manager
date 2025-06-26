# Week 2 | Man in the Middle | Updated 2025 Version for Phone/Laptop Attack

Networking plays a pivotal role in the field of cybersecurity, serving as both a critical component and a potential vulnerability. The interconnected nature of modern computer systems underscores the importance of robust networking practices to ensure the integrity, confidentiality, and availability of sensitive information. Cybersecurity professionals heavily rely on network architecture to implement effective defense mechanisms, such as firewalls, intrusion detection and prevention systems, and virtual private networks, to safeguard against unauthorized access and malicious activities. A well-designed and secure network infrastructure enables efficient monitoring and timely response to potential threats, contributing to the overall resilience of an organization's cybersecurity posture. Collaboration and information sharing within the cybersecurity community also heavily depend on effective networking, as it facilitates the dissemination of threat intelligence and the implementation of collective defense strategies. In essence, the strength of cybersecurity measures is intricately linked to the robustness of networking protocols and practices.

## Part 1: Phone Setup
1. **Connect Both Devices to the Same Wi-Fi**: 
To perform an ARP spoofing attack, both your computer and phone must be connected to the same local network. Ensure both of your devices are connected to the same Wi-Fi network. If you’re using a mobile hotspot, make sure your laptop is connected to the hotspot created by your phone.

2. **Get Your Phone’s IP Address**: You will need your phone’s IP address in order to spoof it.

	**iPhone:** Go to Settings > Wi-Fi
Tap the ⓘ icon of the connected Wi-Fi network
Scroll down for the IP Address

	**Android:** Go to Settings > Network & Internet > Wi-Fi
Tap the connected network
Look for IP address under advanced settings
Write down your phone’s IP address.

3. **Get Your Computer’s IP Address**: Open your terminal or command prompt and run the following command:
	
 	**Windows:** ipconfig Look under ‘Wireless LAN adapter Wi-Fi for IPv4 address

	**MacOS:** ipconfig getifaddr en0

## Part 2: Begin ARP spoofing

For this lab, we will be exploring what information an adversary can extract from your web application in it's current, unencrypted state. In order for you and your group to view each other's network traffic, you will need to perform a [man-in-the-middle](https://www.imperva.com/learn/application-security/man-in-the-middle-attack-mitm/) (MITM) attack. This will allow the victim's network traffic to route through your machine before being sent to the destination network. To do this we will utilize ARP spoofing. Use one of the following tools depending on your OS to perform the attack:



- [Windows ARP Spoofing tool](https://github.com/alandau/arpspoof)

- [Mac ARP Spoofing tool (bettercap)](https://www.bettercap.org/installation/)



You will also need to know your victim's IP address. Normally, this can be done using other tools such as netcat, however for the sake of ease and time, we can just look at our secondary device to find out it's IP address. Follow the instructions below to perform our man-in-the-middle attack!

### Windows ARP Spoofing:

Once you have `arpspoof.exe` downloaded, we can start using it immediately

1. cd into the directory where you saved the tool:
    - `cd \path\to\tool\`

2. Once in the appropriate directory, we will run the tool directly from the command line. We must also enter the victim's IP address, and the target IP address.
    - `.\arpspoof.exe <victim IP>`

3. Let's say that my victim's IP address was `192.168.0.101`, I would enter the following command:
    - `.\arpspoof.exe 192.168.0.101`

    And I should get the following output:

    ```
    PS C:\Users\mvere\Downloads> .\arpspoof.exe 192.168.0.101
    Resolving victim and target...
    Redirecting 192.168.0.101 (02:1A:4F:8E:7B:9C) ---> 192.168.0.1 (AC:D1:23:45:67:89)
	and in the other direction
    Press Ctrl+C to stop
    ```
    
4. Once you have finished the lab and want to end the ARP spoof, you can enter `Ctrl+C` to stop the program from running.

### Mac ARP Spoofing:

We can utilize bettercap to perform our ARP spoof on macOS. In order to download bettercap, we will need to install Homebrew.

Homebrew is a package manager for macOS, and allows us to install packages and software directly from the terminal.

1. To install Homebrew, enter the following command into your terminal:
    `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

2. Once Homebrew is installed, we can use it to install bettercap. Enter the following command in your terminal:
    `brew install bettercap`

4. Enter the following command to perform your ARP spoof against your victim!
    `sudo bettercap -eval "set arp.spoof.targets <victim IP>; arp.spoof on"`

5. Once you have finished the lab and want to end the ARP spoof, you can enter `Ctrl+C` to stop the program from running.


## Part 3: Wireshark

Now that we have performed our man-in-the-middle attack, we can view all of our victim's network traffic. To do this we will be utilizing [Wireshark](https://www.wireshark.org/), a network traffic analysis tool. Wireshark can allow anyone to view the packets moving across our network. This can pose a serious issue when a web application is not properly utilizing cryptography, allowing any script kiddie to easily view our sensitive data.



### Instructions:

1. Download Wireshark [here](https://www.wireshark.org/).

2. Once Wireshark is installed, open the application and select the appropriate network interface.

3. Wireshark will now quickly populate with all network packets being sent to and from your victim's computer. While you are monitoring their traffic, have the victim log into their insecure web application. Once they have done so, click the red button at the top of Wireshark to stop recording their network traffic.



    ![Stop wireshark](/lab-writeup-imgs/stop_wireshark.png)



3. In the text field at the top of the application, you will see "Apply a display filter", this is where you can set filters to easily sort through your traffic.

    ![Highlighted filter bar](/lab-writeup-imgs/wireshark_filter_bar.png)



    The easiest way to begin filtering through traffic is to select any packet in wireshark, right-click the attribute of interest, and Apply as Filter. We can change the value of that filter later, but this way we can ensure we have proper syntax for filtering in wireshark. Below is an example of us applying the source address as our filter:



    ![Applying source filter](/lab-writeup-imgs/apply_src_filter.png)

4. We can now edit the filter to match the source IP address with the victim we were ARP spoofing:

    ![Editing filter](/lab-writeup-imgs/edit_filter.png)

5. Now that we are filtering to see traffic from our victim's IP address, let's also sort the packets by protocol. We know that the victim connected to the unencrypted site of HTTP, so we can look for packets that used that protocol:

    ![Sorting by protocol](/lab-writeup-imgs/sort_protocol.png)

6. Look through these packets and see what information you can gather. Below we can see a packet using the POST header to login. Upon further inspection we can see the victim's login credentials in clear text!

    ![Login credentials](/lab-writeup-imgs/login_credentials.png)

## For Credit

Congratulations on performing your first man-in-the-middle attack. This means that you are officially an iSchool script kiddie (you can update your LinkedIn accordingly :) ). For this lab write up, please use the template found on the canvas page following the rubric listed for the assignment. At the top of the writeup include the names of the people you worked with to complete this lab.
