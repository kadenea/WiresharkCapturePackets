# Wireshark for Beginners: Capture Packets

## Summary

This is a quick guided project that I have began through Coursera. The project is split into three main parts, the project overview, the hands on project, and the graded quiz that requires an 80% to pass the course. Within the project, I learned how to setup and use Wireshark on Ubuntu Linux to capture packets, save them, and use filters to dislay certain HTTP and HTTPS packets.

## In-Depth Expalaintion/Walkthrough

### Installing and Setting up Wireshark on Linux

To install the latest version of Wireshark on Ubuntu Linux, I used the add-apt-repository command within the terminal.
```
sudo add-apt-repository ppa:wireshark-dev/stable
```
When asked "Should non-superusers be able to capture packets?" select "Yes". This is because we do not going to run any software with sudo permissions as it makes some files more difficult to access and if you have access to everything there is a chance the software can corrupt the system.

We then added the user to the Wireshark group for packet capture capabilities through the terminal.
```
sudo usermod -aG wireshark $USER
```

### Capture and Save Packets on a Detected Network

First, I opened Wireshark and filtered to only show the wired interfaces. As I am looking to capture web traffic, I select the eth0 and start capturing packets. After a stop capturing, I select "Save this capture file", select the loaction, and name it.

![Screenshot 2024-10-28 191629](https://github.com/user-attachments/assets/6fdc93d9-f2bc-412f-a29a-db48ccbacd22)

### Using a Diplay Filter to Observe HTTPS Packets

In this step, started a fresh Wireshark packet catpure file, went to the computer's browser, entered duckduckgo.com into the URL, then stopped the packet capture and saved the file.

In oder to filter to only show HTTPS traffic, I used the display filter and typed in `tcp.port == 443`. This is because HTTPS uses the TCP protocol and used port 443. Then select "Apply display filter"

![Screenshot 2024-10-28 193805](https://github.com/user-attachments/assets/0a3a04de-291b-4315-833b-3546d44c9865)

We can then identify our activity by finding a "Client Hello" under the 'Info' column. We can confirm this is our activity by entering the destination IP address into the URL bar. This should bring us to the duckduckgo.com wedbite that we had visited confirming that is the destination IP address for that website. If we then open this packet we can see that it will show essentially everything there is to know about that packet. Information such as the source/destination IP, the source/destination ports, packet length, etc.

![Screenshot 2024-10-28 194741](https://github.com/user-attachments/assets/3f1c8c13-4258-4f79-a468-dbc424506f3c)

Afterwards, I used the same steps when visiting http://cygwin. However, instead of filtering to only show traffic over port 443 for HTTPS, I used the same tcp.port command in the display filter and filtered to only show traffic over port 80 since cygwin uses HTTP instead of HTTPS. This is because HTTP, unlike HTTPS, sends its traffic over port 80 and not 443.

### Visit a Webpage and Detect its IP Address Using a Display Filter

Here, we essentially did the same thing as the previous step. Started a new packet capture on Wireshark, opened our browser, searched google.com instead of duckduckgo.com, ended the packet capture, and filtered using the tcp.port 443 as google.com uses HTTPS. When we do this we can see that in this particular case, google.com has the destination IP address of 142.253.122.99. This can be entered into the address bar on the browser to show this to be true. 

Next, in order to just see the first step in the two way handshake between the server and the website, we can enter `tls.handshake.type == 1` into the display filter. This will just show us our "Client Hello" traffic. Additionally, if we would like to filter down to the second part of the two-way handshake, you can change that "1" into a "2". This will cause just the "Server Hello" traffic to appear.

![Screenshot 2024-10-29 224316](https://github.com/user-attachments/assets/9af36b1d-1e4b-4794-982c-2d44d083ebd4)


You can also use the display filter feature to just show a specific source or destination IP address. In this case, we used the google.com IP address. By using the command `ip.addr == 172.253.122.99` we can have Wireshark only show us traffic going to or from that specific IP address, in this case google.com's. If you would like to only see either the source or destination traffic from a specific IP address, you can use the `ip.src` command for the source or `ip.dst` for the destination.

![Screenshot 2024-10-29 224900](https://github.com/user-attachments/assets/c3a07d59-2e7e-4cf6-8c18-2b991b7e47b4)

### Locate All HTTPS Packets From a Capture Not Containing a Certain IP Address

For this step, we start by capturing packets through wireshark. Then, we enter our browser and enter the URL's google.com and duckduckgo.com. After this, we stop capturing packets and save our file. Once we are finshed, we began using the display filter. This time instead of just filtering through one command, we add the conditional statement "or" in between two separate commands in order to search for packets matching either of the filters we used. In this case, we filtered using `ip.addr == 142.251.167.103 or tls.handshake.type == 1`. 

We and also use an exclaimation point in front of any command. This means "not". Therefore the display filter will only show packets NOT pertaining to that command. We can also add the "and" condition in between filters. Instead of this filtering for packets pertaining to either filter, it instead filters to show packets pertaining to BOTH filters. In this case, we use both of these new functions and with the filter `!(ip.addr == 142.251.167.103) and (tcp.port == 443)`. This will filter to show any HTTPS traffic through port 443 that DOES NOT contain the IP address 142.251.167.103, which in this case is google.com. The parentheses that were added are to handle order of execution so the "!" function is only for the first filter.

![Screenshot 2024-11-01 124957](https://github.com/user-attachments/assets/e388aee3-2b53-4ab5-903b-d185f4fe3d55)
