# Chapter 2 - Basic Malware Analysis

![](resources/Ch3/chapter3.002.jpeg)

C> By [ApexPredator](https://github.com/ApexPredator-InfoSec) | [Discord](http://discordapp.com/users/826227582202675210)

## Introduction
*Malware* has been around for as long as computers have been in common use. Any computer program that performs malicious activities is classified as malware. There are many types of malware ranging from sophisticated self-propagating worms, destructive logic bombs, ransomware, to harmless pranks. Everyone who regularly uses a computer will encounter malware at some point.

This chapter will cover the basics of analyzing malware on an infected computer. It is targeted towards beginners who are new to Digital Forensics and Incident Response (DFIR) and hobbyists. The goal of this chapter is to teach someone unfamiliar with the basic concepts of malware analysis some Tactics, Techniques, and Procedures (TTPs) used to confirm that a computer is infected with malware and how to begin extracting Indicators of Compromise (IOCs). It will cover the use of basic tools. We will not cover intermediate or advanced topics such as reverse engineering malware to discover its purpose or how it works in this chapter.

The chapter starts with an introduction to basic malware analysis. It then covers some free tools to use in basic malware analysis. The chapter culminates with a walkthrough of a canned analysis on a piece of malware. The walkthrough wraps up with recommendations on where to go next to progress to intermediate or advanced malware analysis.

I had numerous instances of friends and family asking me to figure out why their computer was acting weird long before moving into cybersecurity and receiving formal training on malware analysis. I have had other cybersecurity professionals ask why it is not a waste of time to learn to build Microsoft Office macro-based payloads when Microsoft is making it harder for users to run the malicious code inside to which I always respond with "Never underestimate the users desire and ability to download and run anything sent to them." People are going to download and execute malware at some point and if you are the IT expert they will ask you to figure out what happened.

One of my first instances of basic malware analysis was when I was in a situation that required using a computer shared by multiple people to access the internet. I erred on the paranoid side before using it to access any of my personal accounts and ran a network packet capture using Microsoft`s NetMon, which is a packet capture tool similar to Wireshark. I noticed from the packet capture that the machine was communicating with a Chinese domain which appeared unusual. I then conducted a quick Google search on the domain and found that it was associated with a piece of malware.

The site I found listed out additional IOCs which enabled me to check running processes to find that I had the malicious executable running. I was then able to kill the process with Task Manager. I was also able to review the registry with Regedit and delete the registry key that was created by the malware to establish persistence. I was then able to notify the other users of the machine that it had malware running on it that steals information such as account credentials. The machine was then reimaged to ensure all of the malware was removed and the machine was back to a known good state. Next, we will cover some of the basic tools that you can use to perform the same type of simple analysis.

## Basic Malware Analysis Tools

This section covers free tools that can be used for basic malware analysis to identify if a machine has been infected with malware. You can use these tools to extract IOCs to share with the community or to include in an Incident Response report in a professional setting. We will start with built in tools that you probably already know and discuss how to use them for basic malware analysis.

Task Manager is a built in Windows tool that allows you to view running processes. You can use it to view running processes and how much resources they are using. On Windows 10, right click the task bar and select Task Manager from the menu to launch the Task Manager. On Windows 11, click the Windows Start Menu icon and type Task Manager to search for the Task Manager app. You may then need to click the drop down arrow entitled `More details`.

![](resources/Ch3/chapter3.003.png)

You can use this tool to find suspicious processes running on the machine. More sophisticated Malware will attempt to blend in by using the names of common legitimate programs, however, if you have a specific process name from an IOC you can easily look to see if it is running. Each process also has an arrow you can click to expand to show child processes.

There are also `Startup` and `Services` tabs that allow you to review processes that are set to run on startup and the list of installed services. You can review the Startup tab to help identify simple persistence mechanism of malware to find applications that run on startup that are uncommon or should not be included. This same process can be done on the Services tab to find suspicious services installed on the machine. These tabs show you the same information that you would get by running Startup Apps or `services.msc` independently from Task Manager.

![](resources/Ch3/chapter3.004.png)

![](resources/Ch3/chapter3.005.png)

You can pull up the details for each service listed in the Services tab or from `services.msc`. It will list the Startup type which is either Manual, Automatic, or Disabled. The Automatic startup type services will start automatically when the computer boots up Windows. You can also find the path to the executable that the service runs and what user or context it runs under. These details are useful IOCs for malicious services installed by malware.

![](resources/Ch3/chapter3.006.png)

[Process Explorer](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer) (`procexp.exe` and `procexp64.exe`) from the Sysinternals Suite is another free tool that provides a greater level of detail than the built in Task Manager in Windows. It provides the same functionality to kill processes while providing additional details in the main window. You can submit hashes to VirusTotal through Process Explorer to help determine if a process is malicious.

![](resources/Ch3/chapter3.007.png)

Right clicking on the process and selecting `Check VirusTotal` will prompt you to accept submitting hashes of the suspected process to VirusTotal. After selecting yes on the prompt, the VirusTotal box on the image tab will contain a link to the VirusTotal results of the submitted hash. In this case, the legitimate Microsoft Print Spooler executable spoolsv.exe was submitted and resulted in 0 out of 73 Antivirus vendors detecting it as malicious.

![](resources/Ch3/chapter3.008.png)

Process Explorer also has a tab to review TCP/IP connections listing listening addresses and ports or outbound communications made by the process. This helps a malware analyst determine if the process is listening or receiving on any network ports. This can help find IOCs for Command and Control (C2) or even data exfiltration.

![](resources/Ch3/chapter3.009.png)

The Strings tab is another great feature that allows you to list the strings embedded in the binary just like the strings command in Linux. This is useful for finding IOCs and determining some of the capabilities of the malware. You may be able to find IPs or domain names that are coded into the application. Or you may find strings that point to dangerous Windows API calls that can hint at the executable being malicious. The Sysinternals Suite can be downloaded [here](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite).

![](resources/Ch3/chapter3.010.png)

System Informer, formerly Process Hacker,  is another great tool that performs similar functions to Task Manager and Process Explorer. It will provide you the same level or process details and group the processes in a parent/child process layout like Process Explorer. Right clicking a process in System Informer allows you to terminate a process just like in Task Manager and Process Explorer. Right clicking and selecting `Send to` provided an option to send the process executable or dll to VirusTotal similar to Process Explorer.

![](resources/Ch3/chapter3.011.png)

System Informer includes a Modules tab when right clicking and selecting properties on a process. This Modules tab lists all of the modules loaded and in use by the process. This is helpful for finding additional IOCs or identifying malicious dll files used by a suspicious process.

![](resources/Ch3/chapter3.012.png)

System Informer provides `Services` and `Network` tabs that offer similar functionality to the features covered under Task Manager and Process Explorer. A malware analyst can use the `Services` tab to search for suspicious services and review the details of the service. The `Network` tab can be used to map running processes to active network connections and listening ports. System Informer is available for download at https://github.com/winsiderss/systeminformer.

![](resources/Ch3/chapter3.013.png)

![](resources/Ch3/chapter3.014.png)

[Process Monitor](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon), or Procmon, is another tool included in the Sysinternals Suite that is useful for monitoring processes. Procmon goes beyond the process information provided by Task Manager, Process Explorer, or System Informer. It details every action taken by the process allowing in-depth analysis of suspicious or malicious processes. Procmon will quickly overload an analyst with data unless filters are used to filter out the noise. It enables an analyst to find IOCs and understand what actions the malware has taken on the system.

![](resources/Ch3/chapter3.015.png)

ProcDOT is useful for filtering and displaying the results from Procmon. ProcDOT allows an analyst to ingest the logs generated from a Procmon capture saved in a CSV file. The analyst can then select the desired process from the imported CSV file and ProcDOT will generate an interactive graph.

![](resources/Ch3/chapter3.016.png)

This effectively filters out the noise of unrelated processes giving the analyst an easy-to-follow graph that displays all actions conducted by the malware to include those of child processes spawned by the original process. It also allows to ingest packet captures to correlate with Procmon. ProcDOT can be downloaded [here](https://www.procdot.com/downloadprocdotbinaries.htm).

The [netstat](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/netstat) tool included in Windows is another useful tool. You can use it to list all listening ports and established connections. You can review the connections and listening ports with the command `netstat -ano`. This command includes the process ID of the process using that listed port to help you correlate a suspicious connection to a process.

![](resources/Ch3/chapter3.017.png)

The [tasklist](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tasklist) command can be used to list running process and their associated process ID from the command line. This can help you enumerate suspicious processes without needing to use a Graphical User Interface (GUI). It is helpful when used in conjunction with netstat to look up the process ID found with a suspicious network connection. The below screenshot lists that PID 4 listening on port 445 (RPC\SMB) on all interfaces (0.0.0.0) is the System process. In this case it is a legitimate process and listening port combination. The System process also always loads at PID for so if it were a PID other than 4 that would be unusual and a potential IOC.

![](resources/Ch3/chapter3.018.png)

Another way to do the same analysis is to use the [TCPView](https://docs.microsoft.com/en-us/sysinternals/downloads/tcpview) tool from Sysinternals Suite. The TCPView tool provides the same information received from `netstat -ano` and `tasklist /SVC` in a convenient and easy to read GUI. This allows you to quickly identify suspicious listening ports or connections and correlate them to the corresponding process. The remote address listed in TCPView and netstat is another useful IOC to include in your analysis.

![](resources/Ch3/chapter3.019.png)

Wireshark is a valuable tool to conduct more in-depth packet analysis. Wireshark enables a malware analyst to view all network traffic sent and received on the suspected machine. An analyst can filter the packets by IP, port, protocol, or many other options. Filtering by DNS protocol enables an analyst to find DNS queries to malicious sites used for Command and Control (C2) of malware. The domains found in the DNS queries are useful IOCs to determine if the machine is compromised.

Wireshark provides capabilities to conduct more advanced analysis of malware communication. It allows an analyst to identify C2 traffic hidden in protocols such as DNS. It also enables an analyst to extract data such as second stage binaries or infected text documents downloaded by the malware. Using a proxy in combination with Wireshark enables an analyst to export the certificate and keys used to encrypt Transport Layer Security (TLS) encrypted traffic to recover the plaintext data sent between malware and attacker-controlled servers.

![](resources/Ch3/chapter3.020.png)

The malware analysis walkthrough in this chapter will focus on using Wireshark to perform basic analysis tasks. This includes reviewing DNS queries to identify suspicious domain lookups and plaintext commands/passwords sent during malware communication. More advanced usage of Wireshark is out of scope of basic malware analysis and is saved for future writings on intermediate and advanced malware analysis. Wireshark can be downloaded [here](https://www.wireshark.org/). Microsoft's NetMon is an alternative to Wireshark, but is only available for download from [archive](https://www.microsoft.com/en-us/download/4865) and is no longer being developed.

Regedit is another useful tool built into Windows. Regedit gives the ability to view and edit the Windows registry. It can be used for basic malware analysis to search for persistence mechanism such as entries in `HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` or `HKEY\_CURRENT\_USER\Software\Microsoft\Windows\CurrentVersion\Run`. Applications listed in the run keys will auto start when a user logs into the machine and is sometimes used by malware to establish persistence.

![](resources/Ch3/chapter3.021.png)

Regshot is useful for determining what changes an application makes to the Windows registry when it is executed. Regshot allows an analyst to take a snapshot of the Windows registry before and after executing a suspicious application and generates a comparison of the two snapshots. This is useful when analyzing a suspicious application in a controlled lab setting. Regshot can be downloaded [here](https://github.com/Seabreg/Regshot). However, Regshot is no longer being actively maintained. NirSoft provides an alternative to Regshot that is capable of handling registry comparisons. NirSoft's RegistryChangesView can be found [here](https://www.nirsoft.net/utils/registry_changes_view.html). The malware analysis portion of this chapter will still use Regshot.

![](resources/Ch3/chapter3.022.png)

Certutil is another tool built into Windows that is useful for malware analysis. An analyst can use certutil to generate a hash of a file to compare it to a known malicious file hash. This can indicate if a file is malicious without having to execute it to investigate what it does. An analyst can use the hashes generated by certutil as IOCs once a suspicious file is determined to be malicious thru analysis.

![](resources/Ch3/chapter3.023.png)

[Certutil](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/certutil) is used in the above screenshot to generate the SHA1, MD5, and SHA256 hashes of `cmd.exe`. A malware analyst can compare these hashes to the hashes of the known legitimate versions of `cmd.exe` installed with Windows. The analyst can also submit these hashes to VirusTotal to see if it is a known malicious file.

An analyst can also use automated tools for analysis. Multiple tools mentioned already have features to upload files or hashes to VirusTotal. A suspicious file can be uploaded to [VirusTotal](https://www.virustotal.com/gui/home/upload). VirusTotal is an online system that will execute the file in a sandbox to attempt to determine if it is malicious or not. It will then provide file hashes and IOCs an analyst can use to identify the file. VirusTotal also shares uploaded files with Antivirus vendors to use for building detection signature.

![](resources/Ch3/chapter3.024.png)

[Antiscan.me](https://antiscan.me/) is another option an analyst can use to analyze a suspected file. Antiscan.me only checks uploaded files against 26 different Antivirus vendors. It also does not share the files with the Antivirus vendors. This makes it a good option if you are analyzing a file that you do not want to be shared with other organizations.

![](resources/Ch3/chapter3.025.png)

## Basic Malware Analysis Walkthrough
It is time to do a walkthrough of a sample malware analysis now that you are familiar with some of the tools used for malware analysis and their capabilities. The walkthrough will teach how to use some of the tools mentioned in this chapter. It will not use any tools not previously mentioned.

In this scenario a user has reported to you that their machine has been running slow and acting "weird". You have already conducted initial investigations by asking the user questions including: "When did the issues start?", "Did you download or install any new applications?" and "Did you click any links or open any documents from untrusted sources?" The user states that they did not install any application recently but did review a Microsoft Word document sent from a customer.

We start our analysis with opening TCPView from the Sysinternals Suite to determine if we can quickly find any unusual processes communicating to remote sites. In this simple scenario, we find that there is currently only one process, `python.exe`, communicating to a remote system. We flag this as suspicious since Python is not typically used in the manner for our fictitious network. We then make a note of the port and IP for potential IOCs.

![](resources/Ch3/chapter3.026.png)

We can verify this using the other tools covered early as well. `Netstat -ano` lists an established connection between our test machine and the simulated attacker machine with local IP/port 192.168.163.131:63590 and remote IP/port 192.168.163.128:8443 from the process with PID 6440. `Tasklist /SVC` lists that python.exe is running as PID 6440.

![](resources/Ch3/chapter3.027.png)

![](resources/Ch3/chapter3.028.png)

Process Explorer can also be used to verify the findings. Right clicking on `python.exe`, selecting Properties, and then selecting the TCP/IP tab lists the connection to 192.168.163.128:8443. System Informer provides another easy means to find the unusual connection and correlate it to the `python.exe` process by selecting the Network tab.

![](resources/Ch3/chapter3.029.png)

![](resources/Ch3/chapter3.030.png)

We have verified that there is unusual network traffic on the potentially compromised machine and need to dig deeper into the traffic. We then open up Wireshark to review a packet capture of the incident. We use the IP and port combination (`ip.addr == 192.168.163.128 and tcp.port == 8443`) to filter the traffic down to the currently interesting packets. The traffic is not encrypted which will allow us to extract plaintext communications.

![](resources/Ch3/chapter3.031.png)

We then right click on one of the packets and select follow TCP stream to pull up the conversation in a readable format. This confirms that this is a malicious process used to create a reverse shell to the attacker. We are able to identify commands sent by the attacker and the response from the infected machine.

![](resources/Ch3/chapter3.032.png)

The attacker ran a series of command to enumerate identifying information about the machine and what privileges the user account has. The attacker then attempts to establish persistence by creating a service named `NotBackDoor` to auto start the malware containing the reverse shell. This action failed leading the attacker to then attempt persistence by creating a run key in the system registry for the current user and was successful.

![](resources/Ch3/chapter3.033.png)

At this point we have verified that there is malware present on the system and it is actively being exploited by a remote attacker. We immediately take action to isolate the machine to cut off access from the attacker and protect the rest of the network. In this scenario we would just simply block the IP and port on the perimeter firewall and remove the infected machine from the network before continuing our analysis.

We then take steps to confirm the persistence measures taken by the attacker. We review the services in `services.msc` to verify that NotBackDoor service was not successfully created. Then we take a look to ensure no other unusual service exist. The NotBackDoor service name and the binPath option of `C:\Python27\python.exe C:\Windows\Tasks\bdw.py` are still noted as IOCs since the attacker did attempt to create the service and it could be present on other infected machines if access was granted.

![](resources/Ch3/chapter3.034.png)

Regedit is then used to verify the run key created after verifying that no malicious services exist. We do find a NotBackDoor key that points to `C:\Python27\python.exe C:\Windows\Tasks\bdw.py`. We make note of this as an IOC. We also note that `C:\Windows\Tasks\` is commonly used as a location to drop malware due to low privilege users being able to write to the location and is common excluded from protections such as application whitelisting since it is located under `C:\Windows`.

![](resources/Ch3/chapter3.035.png)

The next step for this scenario is to navigate to the `C:\Windows\Tasks\` folder to investigate the `bdw.py` file mentioned in the previous steps. The investigation finds that this is just a simple Python script to establish a reverse shell from the infected computer to the attacker's machine. We are able to determine that it contains the port number 8443 but it is pointing to a domain name of `maliciousdomain.cn` instead of IP.

![](resources/Ch3/chapter3.036.png)

We add this domain to the list of IOCs. We could have also identified the traffic associated with this domain if we had started this investigation by looking for suspicious DNS calls. The .cn root domain indicates this is a Chinese domain and if we are in a scenario where traffic to China is abnormal then this is a potential red flag.

![](resources/Ch3/chapter3.037.png)

We know that `bdw.py` is malicious and provided a remote attacker access to the infected machine, but we do not yet know how it got there. We see that the document the user stated they received from a new customer ends with the extension `.docm`. This informs us that the document contains macros which could be the initial infection vector (IIV). Analysis on this file needs to be done in an isolated lab to prevent any reinfection.

![](resources/Ch3/chapter3.038.png)

The document in this scenario contains only one line of text stating that it is a generic document for a malware analysis walkthrough. We could search for unique strings in the document that could be used for IOCs in a real-world scenario to help others determine if they have received the same document. The next step is to check the documents for macros.

Click View in the ribbon menu at the top of the document. Then select the Macros button and click the Edit button in the window that pops up. We find that this document does contain a simple macro that uses PowerShell to download `bdw.py` from `maliciousdomain.cn`. The macro then executes `bdw.py` to initiate the initial reverse shell connection. The macro contains the `AutoOpen` and `Document\_Open` subroutines to run the downloader when the document is opened. We have now verified that `Doc1.docm` is a downloader used to infect the system with a Python-based reverse shell. We add `Doc1.docm` to our list of IOCs.

![](resources/Ch3/chapter3.039.png)

We could have started our analysis with the `Doc1.docm` document that was mentioned by the user. This would have given us the info to track down the reverse shell that we had found by analyzing the network traffic and processes earlier. Running Wireshark while executing the macro helps us find the DNS calls to the `maliciousdomain.cn`. We can also extract the `bdw.py` script from the HTTP stream since it was download unencrypted via HTTP. This can be useful in instances were more advanced malware downloads another stager and then deletes the stager from the system after running its payload.

![](resources/Ch3/chapter3.037.png)

![](resources/Ch3/chapter3.040.png)

We can also use the built in `certutil.exe` tool to generate hashes for the malware files to use for IOCs. Run `certutil -hashfile Dco1.docm SHA256` to generate a SHA256 hash of the document. You can also generate an MD5 hash and generate the hashes for the `bdw.py`. These are useful IOCs for signature-based systems to detect the presence of the malware.

![](resources/Ch3/chapter3.041.png)

We can use Procmon and ProcDOT to verify that the malicious files did not spawn any additional processes that need to be investigated. The ProcDOT graph shows us that the `python.exe` process communicated over TCP to IP 192.168.163.128 and spawned a `cmd.exe` process. We can see the commands that were run in the `cmd.exe` process in the graph and verify that no additional files or processes were created.

![](resources/Ch3/chapter3.042.png)

We can verify if any other registry settings are changed by executing the Word document macro on our test machine. We use Regshot to take a snapshot before and after opening the document. We then open the comparison of the snapshots to review the changes. Start Regshot then click 1st shot and then shot.

![](resources/Ch3/chapter3.043.png)

We then open the malicious Word document. We execute the macro allowing it to download the bdw.py reverse shell from out attacker webserver and then add our persistence registry key under HKCU\Software\Microsoft\Windows\CurrentVersion\Run. Then we click 2^nd^ shot in Regshot and select shot. This takes the second snapshot and allows us to click the compare button to compare the snapshots.

![](resources/Ch3/chapter3.044.png)

This produces a `.txt` document listing all of the registry changes that occurred between the snapshots. It contains a lot of noise and can be tedious to sort through. We can verify that the persistence mechanism was added. We can find evidence that the user clicked the `Enable Content` button allowing the macro to run. This found by searching for `TrustRecords` to find an entry that lists the malicious document added to the `TrustRecords` key.

![](resources/Ch3/chapter3.045.png)

We can include automated analysis by uploading the document to VirusTotal to determine if it is detected as malicious by any of the Antivirus vendors. VirusTotal lists 30 out of 62 vendors detected the document as malicious with most of the detections flagging it as a downloader. This matches what we determined from our own analysis.

![](resources/Ch3/chapter3.046.png)

## Analysis Wrap-Up

We have now completed analyzing the system to verify that it is infected with malware. We determined what the malware does and we have extracted IOCs to implement in our defensive tools to detect future infection attempts. The machine will need to be reimaged before returning it to the user for use to ensure all malware has been eradicated. It is important to ensure a forensic image is taken before reimaging the system if evidence preservation is needed to legal cases or future investigations. To recap our IOCs:

- Downloader macro in document title `Doc1.docm`
- Unique string "This is a generic document for a malware analysis walkthrough" in `Doc1.docm`
- Second stage Python reverse shell named `bdw.py` stored in `C:\Windows\Tasks\`
- Service named NotBackDoor to auto start `bdw.py`
- `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\NotBackDoor` registry key to autorun `bdw.py`
- SHA256 hash of `Doc1.docm` - `6fa2281fb38be1ccf006ade3bf210772821159193e38c940af4cf54fa5aaae78`
- Md5 hash of Doc1.docm - `b85e666497ea8e8a44b87bda924c254e`
- SHA256 hash of `bdw.py` - `f24721812d8ad3b72bd24792875a527740e0261c67c03fe3481be642f8a4f980`
- Md5 hash of `bdw.py` - `34ca38da117d1bb4384141e44f5502de`
- `Bdw.py` downloaded from `maliciousdomain.cn`
- `Bdw.py` reverse shell to IP 192.168.163.128 (`maliciousdomain.cn`)
- `Bdw.py` reverse shell on port 8443

{pagebreak}

## Conclusion

This was a simple example of how to conduct basic malware analysis. The tools and techniques discussed in this scenario can be used in a real-world scenario to determine if a machine is infected with malware and extract some IOCs. The malicious files used for this scenario and a copy of the walkthrough can be found on my [GitHub](https://github.com/ApexPredator-InfoSec/Basic-Malware-Analysis). You will need a system with netcat to receive the reverse shell as well as [fakedns](https://github.com/SocialExploits/fakedns/blob/main/fakedns.py) to simulate a DNS server to direct the `maliciousdomain.cn` calls to your attacker machine.

More advanced malware will require additional tools and techniques. The techniques to reverse engineer malware to include decompiling, disassembling, and debugging is covered in courses such as [SANS FOR610 Reverse Engineering Malware](https://www.sans.org/cyber-security-courses/reverse-engineering-malware-malware-analysis-tools-techniques/). The FOR610 course is a good step up to the next level if you enjoyed this basic malware analysis. The course also teaches some techniques for deobfuscating code whereas this basic analysis only consisted of unobfuscated code.

Additional advanced topics to look into include techniques to recover encryption keys. Those techniques are useful to decrypt source code of encrypted malware or to help recover keys to decrypt files that have been encrypted by ransomware. Assembly language programming familiarity is needed for debugging and reverse engineering of malware. Basic knowledge of JavaScript is also useful for analyzing web-based malware.

You can also increase your skills by taking malware development course from [Sektor7](https://institute.sektor7.net/red-team-operator-malware-development-essentials). Learning to develop malware will help you better understand how to detect malware and will teach you additional techniques used by modern malware. SANS also offers the advanced FOR710 course for [Reverse-Engineering Malware: Advanced Code Analysis](https://www.sans.org/cyber-security-courses/reverse-engineering-malware-advanced-code-analysis/).

If you enjoyed this walkthrough and would like to check out more, you can check out my [GitHub](https://github.com/ApexPredator-InfoSec) for a walkthrough on performing white box code analysis of a vulnerable web application and coding a full chain exploit. I have solutions for various vulnerable web applications and binary exploitation challenges and will be adding a couple of binary exploitation and reverse engineering walkthroughs in the future. I can also add in intermediate malware analysis walkthroughs if there is enough interest.
