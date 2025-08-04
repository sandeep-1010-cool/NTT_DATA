# **Conference Page: Infrastructure Patching of Linux Servers**

## **Objective**
This document provides a comprehensive overview of the monthly patching process for Linux servers across Production, Test-Net, and Windward environments. It covers the patching workflow, tools used, change request creation, validation, and closure processes.

---

## **Environment Overview**
### **1. Production Environment (RR - Mahwah)**
- **Active Environment:**  
  - Handles all user jobs and logins.  
  - Located in **New Jersey** data center.  
  - Contains primary and secondary servers (e.g., OC-P, M1-P, M1-S).  
  - All Jenkins jobs are replicated to ensure redundancy.

### **2. Dormant Environment (WW - Windward)**
- **Backup Environment:**  
  - Acts as a passive cluster for replication.  
  - Located in a separate data center.  
  - Used only when the active environment fails.

### **3. Test-Net Environment**
- **Testing Environment:**  
  - Mirrors the production environment for testing patches, plugins, and upgrades.  
  - Developers use it to validate changes before moving them to production.  
  - Contains primary and secondary servers similar to production (e.g., OC-P, M1-P, M1-S).

### **Server Naming Convention**
- **Mahwah-RR-Active (Production):**  
  - OC-P: `njraritapp00a92`  
  - M1-P: `njraritapp00a93`  
  - M1-S: `njraritapp00a94`  
  - M2-P: `njraritapp00a95`  
  - M3-P: `njraritapp00a96`  

- **Windward-WW-Dormant (Production):**  
  - OC-P: `gaalpitapp00765`  
  - M1-P: `gaalpitapp007b9`  
  - M1-S: `gaalpitapp007ba`  
  - M2-P: `gaalpitapp007bc`  
  - M3-P: `gaalpitapp007bd`  

- **Test-Net Servers:**  
  - Mahwah: Similar structure as Production.  
  - Windward: Similar structure as Production.

---

## **Patching Process**
### **Scope of Patching**
- Includes **Linux core versions**, **third-party software**, and **plugins**.  
- Excludes Jenkins-specific components unless explicitly stated.  
- Java patches are critical since Jenkins runs on Java.

### **Steps**
#### **1. Test-Net Patching**
- Patches are applied first to the test environment.  
- Validations are performed to ensure no issues arise.

#### **2. Production Patching**
- Standby servers are patched first.  
- Once verified, primary servers are patched.  
- Downtime occurs during switch-over (usually 5–10 minutes).

### **Automation**
- Patching is automated using **Ansible Tower** and **AWS** playbooks.  
- Admins monitor the process and notify Jenkins users of disruptions.

### **Validation and Vulnerability Checks**
- **Validation Process:**  
  - After patching, admins validate the **patch release number** on individual servers.  
  - Command: `cat /etc/ansible/facts.d/patchstatus.fact`.  
    - Displays the patch release number (e.g., `"patch_release": "20250701"`).  
    - Confirms all packages are included in the patch.

- **Vulnerability Checks:**  
  - LICMT team collaborates with Red Hat to ensure vulnerabilities are addressed.  
  - Admins do not perform individual version checks for third-party software.  
  - Focus remains on verifying patch release numbers.

---

## **Change Requests**
### **Structure**
- **Test-Net Environment:**  
  - Combined into **one change request**.  
- **Production Environment:**  
  - **Mahwah-RR-Active:** Includes active production servers.  
  - **Windward-WW-Dormant:** Includes dormant servers.  
  - Combined into **two change requests**.  
- **Total:** Three change requests per month.

### **Steps to Create a Change Request**
#### **1. Accessing the Change Request System**
- Change requests are created in **SMC** (Service Management Console).  
- Ensure you have access to the **SMPI Dashboard** or submit a request to your manager if access is denied.  
- If you are part of the **Enterprise Platform Engineering (EPE)** team, coordinate with your manager for necessary permissions.

#### **2. Creating a Change Request**
- **Parent Change Number:**  
  - Locate the parent change number from the patching calendar (e.g., ending with "97").  
  - Copy the parent change number and reference it in your new change request.

- **Using Templates:**  
  - Use an existing template to create a new change request.  
  - Navigate to the parent change request, click on "Copy Change," and duplicate the required details.

- **Tabs to Update:**  
  - **Planning Tab:**  
    - Fill in all relevant fields, including implementer details.  
    - Ensure the implementer and verifier are different individuals.  
  - **Schedule Tab:**  
    - Set the start and end dates for the patching process.  
    - Allocate sufficient time (e.g., 13 hours) to account for troubleshooting if issues arise.

#### **3. Implementing the Change**
- **Start Implementation:**  
  - Click on the "Implement" button when ready to begin patching.  
  - Execute the patching process using **AWX jobs** (automated via AWS).

- **Validation:**  
  - Verify the patch release number on the server using the following command:  
    ```
    cat /etc/ansible/facts.d/patchstatus.fact
    ```
  - Ensure the patch release number matches the patch date (e.g., `"patch_release": "20250701"` and `"patch_date": "2025-07-25"`).

#### **4. Closing the Change Request**
- **Closure Process:**  
  - After patching is successful, navigate to the **Change Task Node** tab.  
  - Update the closure information for each child task by typing relevant details (e.g., patching status, verification results).  
  - Click "Save" and then "Close" for each task.

- **Finalizing the Change Request:**  
  - Return to the **Planning Tab** and select "Closing Information."  
  - Fill out the form, mark the change as "Successful," and click "Close."  
  - Refresh the SMC dashboard to confirm the change request status as "Closed."

---

## **Monthly Patch Calendar (July 2025)**
### **Week-by-Week Breakdown**
- **Week 1:** Notification period.  
  - Alerts sent to users about upcoming patching.  
  - Change requests are created for Test-Net and Production environments.

- **Week 2:** Test-Net patching.  
  - Patches applied and validated in the test environment.

- **Week 3:** Production W3 patching.  
  - Standby servers patched first, followed by primary servers.  
  - Downtime occurs during switch-over.

- **Week 4:** Production W4 patching.  
  - Remaining production servers patched.  
  - Similar process as W3: Standby servers patched first, then primary servers.

### **General Insights**
- **Preferred Day:** Fridays (less user activity due to the weekend).  
- **Timing:**  
  - Test-Net patching: Typically starts at **8:00 AM**.  
  - Production patching: Begins at **7:00 AM**, with standby servers patched first.

- **Downtime:**  
  - Occurs during the switch-over from primary to standby servers.  
  - Duration: Typically **5–10 minutes**, but may vary based on server load and master activity.

---

## **Notifications**
### **Patch Notice Informational Emails**
- Public email distribution list for Linux Patch notifications.  
- Subscribe via APRS:  
  - Search for List Name = "LinuxPatchNotice".  
  - Add/remove yourself to the **UPS LinuxPatchNotice** distribution list.

### **System Patch Completed Emails**
- Notifications sent automatically after patching is completed.  
- Sent to ContactADID and relevant group members.

---

## **Important Links**
- **LEAP Patch Schedules:**  
  - [LEAP TestNet Scheduler](http://espinside.ms1907.com/leap/Scheduler/).  
  - [LEAP Production Scheduler](http://leap.inside.ups.com/leap/Scheduler/).

- **Documentation:**  
  - [Updating Linux Server/Application Information Fact File](http://devops-wiki.inside.ups.com/cmio/awx-patching-schedule/).

---

## **Summary**
- Monthly patching is automated and follows a structured schedule.  
- Validation of patch release numbers ensures all packages are applied correctly.  
- Notifications are sent to users before and after patching.  
- Change requests are created and tracked in SMC using predefined templates.
