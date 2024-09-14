# Heartbreaker 💔
## Scenario:

Delicate situation alert! The customer has just been alerted about concerning reports indicating a potential breach of their database, with information allegedly being circulated on the darknet market. As the Incident Responder, it's your responsibility to get to the bottom of it. Your task is to conduct an investigation into an email received by one of their employees, comprehending the implications, and uncovering any possible connections to the data breach. Focus on examining the artifacts provided by the customer to identify significant events that have occurred on the victim's workstation.


## Task 1
The victim received an email from an unidentified sender. What email address was used for the suspicious email?
Looking at the file we've extracted and navigating to the users folder, we need to search for an email client or application, which in this case would be Outlook, I used OST Systool program
![image](https://github.com/user-attachments/assets/9092c127-8e78-4f86-bc8a-73389da4c468)

 - Ans:ImSecretlyYours@proton.me

## Task 2
It appears there's a link within the email. Can you provide the complete URL where the malicious binary file was hosted?

Still in that email we could see a link to the attachment for download, you can view the HTML source code to be able to see the embeeded link of the attachment. You will at this point probably have to switch to a linux system as it will be easier to analyze the ost file using the pffexport tool.

In the terminal run the command following :  pffexport -f all -m all /ost/path/client.ost . This will extract the conversations and you can now navigate, view the messages page source 

![image](https://github.com/user-attachments/assets/156352bc-e157-4f1a-9c96-34e5714c8124)

 - Ans: http://44.206.187.144:9000/Superstar_MemberCard.tiff.exe

## Task 3
The threat actor managed to identify the victim's AWS credentials. From which file type did the threat actor extract these credentials?

 - Ans: .ost

## Task 4
Provide the actual IAM credentials of the victim found within the artifacts.

So using systools I went throught the different email folders and found something interesting in the users Draft folder
![image](https://github.com/user-attachments/assets/12b2c5b3-3dfd-460a-b568-2657f1c62323) We could see the Access key ID and secret access key

In Kali we could still just view the html file: ![image](https://github.com/user-attachments/assets/514d6c00-bef4-47bf-8c2f-df038c48f7a0)

 - Ans: AKIA52GPOBQCK73P2PXL:OFqG/yLZYaudty0Rma6arxVuHFTGQuM6St8SWySj

## Task 5
When (UTC) was the malicious binary activated on the victim's workstation?
We will take a look at the prefetch directory. Prefetch is a Windows feature that was introduced in Windows XP. It is a component of the Windows operating system that is designed to improve the performance of the system by preloading frequently used applications and data into memory. When an application is launched, the Prefetcher service creates a prefetch file, which is a small file that contains information about the application's loading behavior. This file is used to optimize the loading of the application on subsequent launches. The Prefetcher service monitors the system's activity and identifies frequently used applications and data. It then creates a prefetch file for each of these applications, which contains information about the files and registry keys that are loaded during the application's startup process. When the application is launched again, the Prefetcher service uses the prefetch file to preload the necessary files and registry keys into memory, reducing the time it takes for the application to start. Prefetch files are stored in the C:\Windows\Prefetch directory and have a .pf extension. They are specific to each application and are updated each time the application is launched.

So after learning a lil bit about htat we just need to navigate to the directory and we'll see something like this: ![image](https://github.com/user-attachments/assets/1e3177fe-2f83-49b3-923a-b20346873d0a)
Notice that was the malicious binary file we found earlier. We will be using a tool called [PECmd](https://ericzimmerman.github.io/#!index.md). 
Runnig the .\PECmd.exe -f <path_to_file> we had the following results :
![image](https://github.com/user-attachments/assets/8c26ec76-75e6-4760-bbd7-753c2338a5ff)

 - Ans: 2024-03-13 10:45:02


## Task 6

Following the download and execution of the binary file, the victim attempted to search for specific keywords on the internet. What were those keywords?

Search the different browsing historys of the browsers the user uses(check AppData both local and Romaing ).  In this case it was Firefox and Edge:![image](https://github.com/user-attachments/assets/959161a5-f9c8-49ef-9b2c-c1c1323f3924)

Using a browsing history search tool i could see the user interacted with Firefox too: ![image](https://github.com/user-attachments/assets/cd77c867-1fed-4476-a60f-921f9fd49c10)
- Went over to the FireFox history directory and loaded up the file, searched for the keywords the victim might have attempted and got this(btw used the SQlite studio view tool) :![image](https://github.com/user-attachments/assets/98940099-a240-4c27-9da0-558ee4112792)
  - Ans :superstar cafe membership

## Task 7
At what time (UTC) did the binary successfully send an identical malicious email from the victim's machine to all the contacts?
Head back to our kali machine and investigate the ost file we exported :![image](https://github.com/user-attachments/assets/3e146b8b-1f79-4414-86b8-41738d3078fc)
 - Ans: 2024-03-13 10:47:51 (UTC so subtract 8 hrs )


## Task 8
How many recipients were targeted by the distribution of the said email excluding the victim's email account?
Count the number of users the mail was cc to
 - Ans: 58

## Task 9
Which legitimate program was utilized to obtain details regarding the domain controller?
Well this took me a while and had to learn(cries in hours of research) how to use a new tool Chainsaw. Chainsaw provides a powerful ‘first-response’ capability to quickly identify threats within Windows forensic artefacts such as Event Logs and the MFT file. Taking a look at the C directory of the file we extracted we could see some interesting files $LOG, $MFT etc : ![image](https://github.com/user-attachments/assets/2e67e645-7332-4a54-b655-2535f70041de)

Searching through we do see other likely candidates but this is what we were looking for: ![image](https://github.com/user-attachments/assets/8e86fb00-a083-44d2-a233-06586ed1d243)

 - Ans: nltest.exe

Note: Nltest is a native Microsoft command-line tool that administrators often use to enumerate domain controllers (DC) and determine trust status between domains, to name a few important features. so yeah we are on track

## Task 10
Specify the domain (including sub-domain if applicable) that was used to download the tool for exfiltration.

![image](https://github.com/user-attachments/assets/bc9c0736-1976-4b67-a58c-783f5015faed)

- Ans: us.softradar.com

## Task 11
The threat actor attempted to conceal the tool to elude suspicion. Can you specify the name of the folder used to store and hide the file transfer program?

![image](https://github.com/user-attachments/assets/3c240bdf-cf47-4380-8409-dbd3b7bf9bf3)
 - Ans: HelpDesk-Tools

## Task 12
Under which MITRE ATT&CK technique does the action described in question #11 fall?
From the investigations we've done and also from the hints in the answer box , we clearly see its a masquerading attack as the attacker manipulated the malicious binary to make it look like a legitimate object .

 - Ans: Masquerading

## Task 13
Can you determine the minimum number of files that were compressed before they were extracted?

Ngl had to look up a write up for this one, and I did come to understand the thought process behind the execution. Let me summarize a lil. Assumming we cated out our results from using chainsaw now focusing soley on the eventlogs;

**Excluding Specific File Types (e.g., .exe, .ps1, .tiff)** which are files that  might be common artifacts dropped by malware or tools used during the attack (e.g., executable payloads, scripts). Removing them from consideration helps in focusing on files that may contain more critical information (e.g., documents, logs, configuration files) rather than simply the tools used by the attacker.

**Excluding the HelpTools directory** since you identified that this directory was used by the attacker to place tools, excluding it from analysis prevents noise from obscuring the more critical interactions elsewhere on the system and also excluding Zip Files

This is because since the  attacker used specific directory  as part of their toolkit, by focusing only on files they interacted with that might contain valuable data, you limit your analysis to items with higher forensic importance.

So in total went through the 129 hits and sorted them out focusing on the TargetFilename filed to determine what type of data it was and the importance.

 - Ans: 26

## Task 14

To exfiltrate data from the victim's workstation, the binary executed a command. Can you provide the complete command used for this action?

We will need to have a look at the HelpDesk-Tools directories and we had a hint : "Include all quotation marks"
![image](https://github.com/user-attachments/assets/bb128f50-4fcf-4378-a1d0-e68bc440dfbf)

  - Ans: "C:\Users\Public\HelpDesk-Tools\WinSCP.com" /script="C:\Users\Public\HelpDesk-Tools\maintenanceScript.txt"

And thats it. Hope you had fun going through it and learning new stuff. I definetly will explore the Chainsaw tool more 😅


![chainsaw](https://github.com/user-attachments/assets/1918f51d-2495-4644-8b32-81d3f6a2c04e)







