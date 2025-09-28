### Purpose
Simulate failed logins and confirm how Windows records them, to practice detecting brute-force or credential attacks.

### Environment
- macOS host running VirtualBox  
- Windows 11 ARM64 virtual machine

### Steps Taken
- Built a Windows 11 VM in VirtualBox and created a secondary local user.
- Attempted several logins with incorrect passwords to generate audit failures.
- Opened Event Viewer and reviewed **Security** logs, filtering on **Event ID 4625** to isolate the failed logon events.
- Examined the event details (username, logon type, failure reason) for each attempt.

### Key Findings
- Each failed login creates a Security log entry with **Event ID 4625**.
- Event details reveal the target account, logon type, and error codes that can help detect brute-force activity.

<img width="895" height="751" alt="Failed login attempts (event id 4625)" src="https://github.com/user-attachments/assets/7e07b075-11b5-4d4c-b704-cc735650d73d" />
<img width="895" height="751" alt="Event properties, general" src="https://github.com/user-attachments/assets/77739bcb-bcbc-47f8-ad17-41a5e888ecba" />
<img width="895" height="751" alt="Event properties, details" src="https://github.com/user-attachments/assets/95afe646-7a7c-494a-834d-7b0c79709cd6" />
