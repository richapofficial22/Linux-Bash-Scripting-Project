# Bash-SysAdmin-Toolkit


A collection of 5 production-style Bash scripts for Linux system administration where every script solves a real operational problem.


- Launched an EC2 instance (instanceproject2) with Ubuntu 
      <img width="1160" height="237" alt="Screenshot 2026-04-30 at 10 50 54 PM" src="https://github.com/user-attachments/assets/5eedcebd-0f19-4d0b-bc7a-4329c1d128fd" />


- Connected to ubuntu instance using internet
      <img width="710" height="657" alt="Screenshot 2026-04-30 at 10 51 49 PM" src="https://github.com/user-attachments/assets/d62f0e59-258d-473c-b5aa-d7b5ffca1f48" />

##

# Script 1 : diskmonitor.sh

## **What it does?**

📊 disk-monitor.sh

Scans all mounted partitions and alarms any above a configurable threshold (default 80%). 

 **What I learnt?**
   - How to use pipe |
   - Initialising value to a variable
   - Taking input directly to variales using read -r
   - Suffix removal
   - Also learnt about awk command and wrote an alternate script below. 

```
THRESHOLD=${1:-80}
df -h | awk -v thresh="$THRESHOLD"  ' 
NR>1 
  {
   gsub("%", " ", $5)  
   if($5 > thresh)
      print "ALERT @" $1 " with threshold value " $5 "%"
   else 
      print "All Normal" 
} 
```

## **Execution**

- Created separate directory script1, created script and previewed it
<img width="755" height="300" alt="Screenshot 2026-04-30 at 10 59 07 PM" src="https://github.com/user-attachments/assets/7aa10875-67a3-4946-9666-047cfc61936f" />

- diskmonitor.sh <img width="722" height="258" alt="Screenshot 2026-04-30 at 11 28 24 PM" src="https://github.com/user-attachments/assets/6fd7c8a8-a93a-4f64-822f-26ecb48c55c0" />

- Successful execution of script <img width="743" height="581" alt="Screenshot 2026-04-30 at 11 30 38 PM" src="https://github.com/user-attachments/assets/846c6ca0-926e-4ac2-a7a7-ab4ffdffd449" />


##

# Script 2 : systemhealth.sh

## **What it does?**

🖥️ systemhealth.sh 

Prints a one-page system report: hostname, uptime,RAM used/total, disk usage per partition, and top 5 CPU-consuming processes. Useful as a quick server diagnostics snapshot.
 
**What I learnt?**
   - How to use sort command 
   - for real time viewing of processes top & htop commands
     
## **Execution**

   - systemhealth.sh <img width="457" height="305" alt="Screenshot 2026-04-30 at 11 40 55 PM" src="https://github.com/user-attachments/assets/8b9623e5-4660-4fc9-96f5-3d0419381458" />
   
   - Successful execution of the script <img width="1031" height="659" alt="Screenshot 2026-04-30 at 11 41 48 PM" src="https://github.com/user-attachments/assets/496a9566-4bb5-4681-b65a-efe83bd3bfe1" />


## 
# Script 3 : servicewatchdog.sh

## **What it does?**

🔁 servicewatchdog.sh

Checks if a named Linux service is active. If not, it attempts an automatically restart it and logs the result with a timestamp to /var/log/watchdog.log. 

**What I learnt?**
   - How to create a logfile. Also that logfiles in var directory are only for the root user 
   - Used 'apt', updated the package, installed 'cups' in our package for the script.
   - set password for ubuntu user as starting any servive would require authentication of user.
   - learnt in brief about all the system commands. for eg. servive management, enable/disable service at boot, listing services, checking status etc.
 
 ## **Execution** 
 
 - Created directory script3 then wrote the servicewatchdog.sh script and previewed it after changing its permissions.
  <img width="557" height="243" alt="Screenshot 2026-05-01 at 1 26 53 AM" src="https://github.com/user-attachments/assets/be240666-7236-4a38-b551-6c5eefb60422" />
  
  ```
#!/bin/bash
LOGFILE="/home/ubuntu/watchdog.log"
SERVICE="cups.service"
STATUS=$(systemctl is-active "$SERVICE")
echo "Service: $SERVICE"
echo "$(date '+%Y-%m-%d %H:%M:%S') - cups status : $STATUS" | tee -a "$LOGFILE"
if [ "$STATUS" = "active" ]; then
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Service is running!" | tee -a "$LOGFILE"
else
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Service is stopped. Starting now..." | tee -a "$LOGFILE"
    sudo systemctl start "$SERVICE"
    echo "$(date '+%Y-%m-%d %H:%M:%S') - Service has now started" | tee -a "$LOGFILE"
fi
```

 - Successfully checked that service is active <img width="472" height="106" alt="Screenshot 2026-05-01 at 1 28 27 AM" src="https://github.com/user-attachments/assets/bb8e56c0-fdd4-456e-8a72-f93a2685150e" />
 
 - Stopping service as user to run script when service is inactive
   <img width="622" height="125" alt="Screenshot 2026-05-01 at 1 29 10 AM" src="https://github.com/user-attachments/assets/055b6e67-e55f-49cb-af0e-9623543f2b8a" />
 
 - Successfull execution of script when script is inactive
   <img width="609" height="222" alt="Screenshot 2026-05-01 at 1 29 47 AM" src="https://github.com/user-attachments/assets/36846105-9975-4a4f-b8cb-f7d79e3d7a37" />

 ##

 # Script 4 : logcleaner.sh
 
 ## **What it does?**

 🧹 log-cleaner.sh
 
 Finds all .log files in /var/log older than N days. Runs a dry-run first (prints the files), then prompts for confirmation before deletion.

  **What I learnt?**
    - The /var/log directory is owned by root user and changing owner from root to other user is not a good practive in linux as then multiple user can have the authority to have the same permissions and make changes in the directory where log files are stored. Hence, running this script as a root user.
    - Different ways to take user input. for eg. timeout prompt, hidden user input in case of passwords
    
 ## **Execution** 

   - Created a new directory as script4
     <img width="484" height="222" alt="Screenshot 2026-05-01 at 2 06 58 PM" src="https://github.com/user-attachments/assets/ca1f21fb-4af0-40e6-b997-4014fcd0c5ea" />
   
   - logcleaner.sh
     <img width="718" height="334" alt="Screenshot 2026-05-01 at 2 29 03 PM" src="https://github.com/user-attachments/assets/43a0e7e1-d2b5-4b35-b66d-e3a45b7ef890" />
   
   - Successful execution of logcleaner.sh
     <img width="583" height="398" alt="Screenshot 2026-05-01 at 2 31 20 PM" src="https://github.com/user-attachments/assets/e05c358d-dde1-4057-9467-a385e41e43ec" />

   ##
   
# Script 5 : backup.sh

## **What it does?**

💾 backup.sh

Creates a timestamped compressed archive (.tar.gz) of any specified directory. Confirms the output file size on completion.

 **What I learnt?**
  - learnt about a new command 'tar' and its various options. for eg. -c,-x,-v,-t,-f,-z,-j 
  - tar is used for archives and file transfers
  - By default,  tar  saves the archive in the current directory where we run the command.
  - A non-root user can not archive files that are owned by root user. Fix : run the script with sudo so that root user owned files can also be archived by any user.
  - OR we can exclude the root user owned directory/files from archiving. 
  

 ## **Execution** 
 
  - Creating script5 directory and previewing our script
    <img width="552" height="261" alt="Screenshot 2026-05-01 at 5 08 52 PM" src="https://github.com/user-attachments/assets/7cb7aa44-c688-453e-8115-b88196c373f5" />
  
  - backup.sh
    <img width="545" height="122" alt="Screenshot 2026-05-01 at 5 11 13 PM" src="https://github.com/user-attachments/assets/1dae17b9-3120-467d-b5ae-57fea367aeee" />
  
  - logcleaner.sh could not run as it was owned by root user <img width="739" height="276" alt="Screenshot 2026-05-01 at 5 15 57 PM" src="https://github.com/user-attachments/assets/c2d6556d-ff55-43b4-9fa5-beabfc4be507" />
  
  - Successfully executing script with sudo.
    <img width="741" height="431" alt="Screenshot 2026-05-01 at 5 31 40 PM" src="https://github.com/user-attachments/assets/d82ebcf8-a27f-481e-8353-0831d0a16cac" />

##





















