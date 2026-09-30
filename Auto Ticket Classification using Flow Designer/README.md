# 🎫 Auto Ticket Classification using Flow Designer

## 📌 Project Overview

**Auto Ticket Classification using Flow Designer** is a ServiceNow-based IT service management automation project developed as part of the **Naan Mudhalvan Skill Development Program**.

The project automates the classification of School IT Helpdesk tickets using **ServiceNow Flow Designer**. When a new ticket is created, the system analyzes the ticket's short description and automatically assigns the appropriate **Category** and **Subcategory** based on predefined keywords.

The solution is designed as a **no-code automation**, reducing manual ticket classification and providing immediate email confirmation to the ticket caller.

---

## 🎯 Problem Statement

School IT helpdesks receive different types of support requests from students and teachers, including:

* Wi-Fi and network problems
* Projector and hardware issues
* Password and account access problems
* Slow or hanging computers

Traditionally, IT staff manually review each request and select the appropriate category and subcategory. This can increase the time required for ticket triage and may result in inconsistent classification.

This project addresses the problem by automating the classification process using ServiceNow Flow Designer.

---

## 🚀 Objectives

The main objectives of this project are:

* Automatically classify IT tickets when they are created.
* Reduce manual effort for IT support staff.
* Automatically assign Category and Subcategory.
* Maintain a dependency between Category and Subcategory.
* Send an automated confirmation email to the caller.
* Store ticket information in a structured custom table.
* Provide a maintainable and scalable no-code solution.
* Package the configuration using a ServiceNow Update Set.

The project requirements specify automatic classification, dependent choices, email notification, structured ticket storage, and maintainability as key requirements.

---

## 🛠️ Technologies Used

| Technology                     | Purpose                                |
| ------------------------------ | -------------------------------------- |
| ServiceNow                     | ITSM platform                          |
| Flow Designer                  | Ticket classification automation       |
| Data Dictionary                | Category/Subcategory dependency        |
| ServiceNow Notification Engine | Automated email notification           |
| Update Sets                    | Deployment and configuration migration |
| XML                            | Update Set export                      |

---

## 🏗️ System Architecture

The project uses the following workflow:

```text
Student / Teacher
       │
       ▼
Create IT Support Ticket
       │
       ▼
Incident WorkFlow Table
       │
       ▼
Flow Designer Trigger
       │
       ▼
Keyword Classification
       │
       ├── Wi-Fi / Network
       │        └── Network → Wi-Fi
       │
       ├── Projector / Hardware
       │        └── Hardware → Projector
       │
       ├── Password / Login
       │        └── Access → Forgot Password
       │
       └── Slow / Performance
                └── Performance → Slow Computer
       │
       ▼
Update Ticket
       │
       ▼
Send Confirmation Email
       │
       ▼
Caller Receives Notification
```

---

## 🗃️ Custom Table

A custom ServiceNow table named **Incident WorkFlow** is used to store the ticket records.

**Table name:**

```text
u_incident_workflow
```

The table uses an auto-generated incident number with the `INC` prefix.

### Main Fields

| Field             | Type        | Description                                      |
| ----------------- | ----------- | ------------------------------------------------ |
| Number            | Auto Number | Automatically generated ticket number            |
| Caller            | Reference   | References `sys_user`                            |
| Category          | Choice      | Network, Hardware, Access, Performance           |
| Subcategory       | Choice      | Wi-Fi, Projector, Forgot Password, Slow Computer |
| Short Description | String      | Short issue description                          |
| Description       | String      | Detailed issue description                       |
| State             | Choice      | New, In Progress, On Hold, Resolved, Closed      |
| Assignment Group  | Reference   | IT support group                                 |
| Assigned to       | Reference   | Assigned technician                              |

The project documentation defines these fields and their corresponding types.

---

## 🔗 Category and Subcategory Dependency

The **Subcategory** field is configured as a dependent field of **Category**.

This prevents unrelated subcategories from being selected for a category.

### Classification Mapping

| Category    | Subcategory     |
| ----------- | --------------- |
| Network     | Wi-Fi           |
| Hardware    | Projector       |
| Access      | Forgot Password |
| Performance | Slow Computer   |

The dependency is implemented through the ServiceNow Data Dictionary.

---

## ⚙️ Flow Designer Automation

### Flow Name

```text
Auto Classify School IT Tickets
```

### Trigger

```text
Trigger: Record Created
Table: Incident WorkFlow
Condition: Category is Empty
```

The flow starts when a new `Incident WorkFlow` record is created and the Category field is empty.

### Classification Logic

| Condition / Keyword                | Category    | Subcategory     |
| ---------------------------------- | ----------- | --------------- |
| Wi-Fi / Network                    | Network     | Wi-Fi           |
| Projector / Hardware               | Hardware    | Projector       |
| Forgot Password / Password         | Access      | Forgot Password |
| Slow Computer / Slow / Performance | Performance | Slow Computer   |

The Flow Designer uses sequential `If` / `Else If` logic to update the ticket fields automatically.

---

## 📧 Automated Email Notification

After classification, the flow sends an automated email to the caller.

### Email Configuration

```text
Action: Send Email
Recipient: Caller → Email
Subject: Your IT Support Ticket [Number] has been logged & categorized
Format: HTML Rich Text
```

The email includes information such as:

* Ticket Number
* Issue Summary
* Assigned Category
* Assigned Subcategory
* Current Status

The email action dynamically retrieves the caller's email through the `sys_user` relationship.

---

## 🧪 Testing

The project was tested using different ticket scenarios.

### Test Cases

| Test Case | Input                              | Expected Category | Expected Subcategory | Result |
| --------- | ---------------------------------- | ----------------- | -------------------- | ------ |
| TC-01     | WiFi not working in library        | Network           | Wi-Fi                | PASS   |
| TC-02     | Projector not turning on in Hall B | Hardware          | Projector            | PASS   |
| TC-03     | Student forgot password for portal | Access            | Forgot Password      | PASS   |
| TC-04     | Lab computer is slow and freezing  | Performance       | Slow Computer        | PASS   |
| TC-05     | General student query              | Unclassified      | Null                 | PASS   |

The project testing documentation records these scenarios and their results.
Email delivery was also verified through **System Logs → Emails** and the Preview Email option.

---

## 🔐 Security and Access Control

The project includes ServiceNow ACL configuration for the custom table.

The documented access model includes:

* Create access for authenticated users/end users.
* Write access restricted to ITIL users after classification.
* Read access for the ticket caller and assigned fulfillers.
* Auto-number validation to prevent duplicate ticket numbers.

---

## 📦 Update Set

All major ServiceNow configuration changes are maintained using a dedicated Update Set:

```text
Project Update Set
```

The Update Set contains configuration such as:

* Custom table
* Custom fields
* Choice values
* Dependent field configuration
* Flow Designer configuration
* Other project customizations

The completed Update Set can be exported as an XML file for version control and deployment to another ServiceNow instance.

### Update Set Export

```text
System Update Sets
        ↓
Local Update Sets
        ↓
Project Update Set
        ↓
State: Complete
        ↓
Export to XML
```

---

## 📁 Repository Structure

A recommended GitHub repository structure is:

```text
Auto-Ticket-Classification-Flow-Designer/
│
├── README.md
│
├── update-set/
│   └── sys_remote_update_set.xml
│
├── documentation/
│   ├── Phase-1-Requirements.pdf
│   ├── Phase-2-Data-Model.pdf
│   ├── Phase-3-Choice-Dependency.pdf
│   ├── Phase-4-Flow-Designer.pdf
│   ├── Phase-5-Email-Notification.pdf
│   ├── Phase-6-Testing.pdf
│   ├── Phase-7-Deployment.pdf
│   └── Phase-8-Conclusion.pdf
│
└── screenshots/
    ├── custom-table.png
    ├── form-design.png
    ├── dependent-fields.png
    ├── flow-designer.png
    ├── email-notification.png
    └── te
```
