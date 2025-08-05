# Comprehensive Q&A: Monthly Linux Server Patching Process

---

## **General Overview**

1. **Q: What is the purpose of the monthly patching process for Linux servers?**  
   **A:** The patching process ensures that all Linux servers in Production, Test-Net, and Windward environments are updated with the latest core, security, and third-party software patches, reducing vulnerabilities and ensuring system reliability.

2. **Q: Which environments are included in the patching cycle?**  
   **A:**  
   - **Production (RR - Mahwah):** Active, user-facing environment in New Jersey.  
   - **Standby (WW - Windward):** Passive, backup environment in a separate data center.  
   - **Test-Net:** Replica of production used for testing patches before production rollout.

3. **Q: Why is Test-Net patched before production?**  
   **A:** To validate patches and ensure no issues occur before applying them to live production systems, minimizing risk.

---

## **Environment & Server Details**

4. **Q: What is the role of the RR (Production) environment?**  
   **A:** It handles all user jobs and logins, and is the main environment for Jenkins operations.

5. **Q: What is the purpose of the WW (Windward) environment?**  
   **A:** It serves as a standby cluster for disaster recovery, activated only if RR fails.

6. **Q: How are the Test-Net servers structured?**  
   **A:** Test-Net mirrors the production environment, with similar primary and secondary servers, enabling accurate testing.

7. **Q: What is the server naming convention for Mahwah (RR) and Windward (WW)?**  
   **A:**  
   - **Mahwah-RR:** e.g., OC-P: `njraritapp00a92`, M1-P: `njraritapp00a93`, etc.  
   - **Windward-WW:** e.g., OC-P: `gaalpitapp00765`, M1-P: `gaalpitapp007b9`, etc.

---

## **Patching Scope & Automation**

8. **Q: What does the monthly patching scope include?**  
   **A:** Linux core versions, third-party software, plugins, and especially Java (critical for Jenkins); Jenkins-specific components are excluded unless specified.

9. **Q: How is patching performed?**  
   **A:** Patching is automated using Ansible Tower (AWX) and AWS playbooks. Admins monitor the process and notify users of any disruptions.

10. **Q: Is manual intervention required during automated patching?**  
    **A:** Usually not, but admins must monitor progress and troubleshoot if automation fails.

---

## **Change Request Process**

11. **Q: What is the first step before making any infrastructure changes?**  
    **A:** Create a change request in the Service Management Console (SMC).

12. **Q: How many change requests are created each month for patching?**  
    **A:** Three: one for Test-Net, one for Mahwah-RR (Production), and one for Windward-WW (Standby).

13. **Q: Is it necessary to create a new change request from scratch each time?**  
    **A:** No, use an existing template and copy it, updating details as needed.

14. **Q: What is the parent change number and how is it used?**  
    **A:** It’s a reference from the patching calendar (e.g., ending in “97”) linking your change request to the master LICM change.

15. **Q: What tabs in SMC must be updated for a change request?**  
    **A:**  
    - **Planning Tab:** Implementer, verifier, and general details.  
    - **Schedule Tab:** Start/end times, with ample buffer (e.g., 13 hours).

16. **Q: Who should be listed as implementer and verifier?**  
    **A:** They must be different people; the implementer performs the patching, the verifier confirms success.

17. **Q: What is the CI (Configuration Items) section for?**  
    **A:** Listing all servers included in the patching activity.

18. **Q: What should you do if you cannot access SMC or a change request?**  
    **A:** Request access via your manager or the appropriate team (e.g., EPE under Doug).

---

## **Patching Execution & Validation**

19. **Q: How is the patching process initiated?**  
    **A:** Click the “Implement” button in SMC when ready, then run the patching via AWX jobs.

20. **Q: How is patching validated on each server?**  
    **A:** By running `cat /etc/ansible/facts.d/patchstatus.fact` and confirming the `patch_release` and `patch_date` match the expected values.

21. **Q: Who is responsible for vulnerability checks and patch selection?**  
    **A:** The LICM team, in collaboration with Red Hat; admins focus on patch validation, not package selection.

22. **Q: What happens if the patch release number does not match or validation fails?**  
    **A:** Contact the LICM team to resolve discrepancies before closing the change request.

---

## **Patching Schedule & Calendar**

23. **Q: How is the monthly patching schedule organized?**  
    **A:**  
    - **Week 1:** Notify users, create change requests.  
    - **Week 2:** Patch and validate Test-Net.  
    - **Week 3:** Patch standby (WW) and production (RR) servers.  
    - **Week 4:** Patch remaining production servers if needed.

24. **Q: Why are Fridays preferred for patching?**  
    **A:** Fewer jobs run, and the weekend allows time for troubleshooting if issues arise.

25. **Q: What is the typical timing for patching activities?**  
    **A:**  
    - **Test-Net:** Starts at 8:00 AM.  
    - **Production:** Starts at 7:00 AM.  
    - **Standby:** Around 8–9 AM.

---

## **Downtime & Impact**

26. **Q: When does downtime occur during patching?**  
    **A:** During server reboot and switch-over from primary to standby servers, typically 5–10 minutes.

27. **Q: What is affected during downtime?**  
    **A:** User access to Jenkins and related services; jobs may be temporarily disrupted.

---

## **Notifications**

28. **Q: How are users notified about patching activities?**  
    **A:**  
    - **Pre-patch:** Public email notifications via the LinuxPatchNotice list (subscribe via APRS).  
    - **Post-patch:** Automatic completion emails sent to ContactADID and relevant groups.

29. **Q: Who is responsible for patch notifications?**  
    **A:** The LICM team, but admins should also proactively notify users.

---

## **Closing Change Requests**

30. **Q: What is the process for closing a change request after patching?**  
    **A:**  
    - Update closure information for each child task (e.g., patch status, validation results).  
    - Save and close each task.  
    - Fill out closing information in the Planning Tab, mark as successful, and close the parent change request.

31. **Q: How many tickets are involved in closing a change request?**  
    **A:** Typically three: two child tickets (for tasks) and one parent ticket.

32. **Q: Can you close the change request directly after patching?**  
    **A:** No, you must complete all closure steps and validations before marking the change as closed.

---

## **Troubleshooting & Escalation**

33. **Q: What should you do if patching fails or issues arise?**  
    **A:**  
    - Troubleshoot as per process.  
    - If unresolved, create a new change request and escalate to the LICM team.

34. **Q: What if patching takes longer than planned?**  
    **A:** Allocate extra time in the schedule; if more time is needed, communicate and request an extension.

35. **Q: What challenges can occur with kernel or Java updates?**  
    **A:** Kernel updates may cause server boot failures; Java is critical for Jenkins. Any issues must be escalated to LICM, as only they have admin access.

---

## **Additional Notes**

36. **Q: Are Jenkins-specific patches included in the monthly patching?**  
    **A:** No, unless explicitly stated; focus is on Linux OS, Java, and third-party packages.

37. **Q: What should you do if you need access to patching tools or dashboards?**  
    **A:** Request access through your manager or the appropriate team; ensure you are on the correct distribution lists.

38. **Q: How does the patching process ensure all servers are up-to-date?**  
    **A:** By validating the patch release number on every server post-patch and ensuring it matches the scheduled release.
