# Dentsu WhatsApp Gateway  
**User Guide – August 2025**  
_Published 28/08/2025_

## Table of Contents
1. [Document Overview](#1-document-overview)
2. [UI Access](#2-ui-access)
3. [The Platform UI](#3-the-platform-ui)
    - [Template Management](#31-template-management)
    - [Create/Edit template](#32-createedit-template)
    - [Delete template](#33-delete-template)
    - [Journeys / Chatbots](#34-journeys--chatbots)
        - [Rules](#341-rules)
        - [Example Rule](#342-example-rule)
    - [Conversations Portal – Live Chat UI](#35-conversations-portal--live-chat-ui)
    - [Campaign Management](#36-campaign-management)
        - [Campaign List](#361-campaign-list)
        - [Create Campaign](#362-create-campaign)
        - [Campaign Setup](#363-campaign-setup)
        - [Campaign Summary](#364-campaign-summary)
    - [Billing](#37-billing)
4. [WhatsApp Flows](#4-whatsapp-flows)
5. [Reporting](#5-reporting)

---

## 1. Document Overview
This guide describes how to access and use the Dentsu WhatsApp platform. It assumes users have basic knowledge of WhatsApp for business, template types, and guidelines.

## 2. UI Access
Access is via the Dentsu secure portal, requiring login with two-factor authentication (Okta App or code). Request access through your account manager. Once authorized, you’ll receive an email to set up your account.

**Access URL:** [https://myapps.dentsu.com/](https://myapps.dentsu.com/)

## 3. The Platform UI
The home page allows navigation of platform modules via the left menu or central dashboard.

### 3.1 Template Management
Create, edit, and delete WhatsApp templates. Templates list includes name, language, status, and category. A search bar and language filter are provided.  
> **Note:** Templates can also be managed in the Meta Console.

### 3.2 Create/Edit template
> **Note:** Templates may only be edited once every 24 hours!

All templates are submitted to Meta for approval. If the type is incorrectly defined, Meta may automatically change it. Typically, this is from Utility to Marketing.

- **Name:** Unique name; ID auto-generated
- **Language:** Select language; create new variant for each template
- **Category:** Marketing, Utility, Authentication
- **Header Type:** Text or Image (upload sample for approval if image)
- **Body Text:** Main content, can include up to 3 parameters (more via Meta console)
- **Parameters/Sample Values:** For each parameter, provide name and sample value
- **Buttons:** Custom, Quick Reply, Visit Website, Complete Flow, Copy Offer Code

> **Note:** Limitations exist on URL tracking depending on region and business HQ. Contact your account team if unsure.

Click publish to save and submit for approval.

### 3.3 Delete template
Delete a template from the platform and Meta console.  
> **Note:** This action cannot be undone!

### 3.4 Journeys / Chatbots
Non-technical users can create/manage templates. Journeys require editing a JSON configuration file, managed by a technical user via S3. Key components:

#### 3.4.1 Rules
- **Trigger:** Type of event (text, button, interactive), associated template, allowedTime constraints
- **checkType:** How values are compared (equals, contains)
- **values:** Array of values to match
- **caseCheck:** Case sensitivity (Y/N)
- **action:** Actions to take (template, db, MediaCard)
- **defaultTemplate:** Default response if no rules match

#### 3.4.2 Example Rule

```json
{
  "trigger": {
    "type": ["button"],
    "template": "clr_indoor_outdoor"
  },
  "checkType": "equals",
  "values": ["Interior"],
  "caseCheck": "N",
  "action": [
    {
      "type": "template",
      "templateName": "clr_room_v2",
      "headerParameters": [
        {
          "type": "image",
          "image": {
            "link": "https://example.com/image.jpg"
          }
        }
      ],
      "bodyParameters": [],
      "flowParameters": ["button"]
    },
    {
      "type": "db",
      "databaseType": "dynamodb",
      "itemStructure": {
        "mobile_number": "{{from_number}}",
        "optout_datetime": "{{current_datetime}}"
      },
      "tableName": "{{table.Optout}}"
    },
    {
      "type": "db",
      "databaseType": "mysql",
      "itemStructure": {
        "user_id": "{{user_id}}",
        "action_time": "{{current_datetime}}"
      },
      "tableName": "user_actions"
    }
  ]
}
```

**This rule triggers when:** a button in the `clr_indoor_outdoor` template is clicked and value is `Interior`.

**If matched, it:**
1. Sends the `clr_room_v2` template with an image.
2. Writes an opt-out entry into the DynamoDB table.
3. Records the action in a MySQL table `user_actions`.

### 3.5 Conversations Portal – Live Chat UI
The Live Chat UI lets users talk directly to customers via WhatsApp. All interactions, including pictures and documents, are shown. Messages can only be sent within 24 hours of a customer's last message (Meta Policy: Customer Service Window).

- **Left pane:** List of conversations; filter/search by number or agent
- **Middle pane:** Chat window; reply and upload documents
- **Right pane:** Customer details and agent assignment

New Agents can be added via the User Access request.

### 3.6 Campaign Management
Send WhatsApp messages to customers in bulk (Marketing or Utility templates). Currently supports one template per campaign.

#### 3.6.1 Campaign List
Lists all campaigns, allows for editing details and setup.

#### 3.6.2 Create Campaign
- Campaign Name: Unique name
- Description
- Template Name: Select from approved templates
- Template Language: Auto-populated
- Template Parameters: Auto-populated if defined in the template

#### 3.6.3 Campaign Setup
- CampaignID: Auto generated
- Campaign Name
- File Name and Header
- Upload: Button for customer file
- Run: Now or scheduled date/time

#### 3.6.4 Campaign Summary
View performance statistics after campaign run.

### 3.7 Billing
Download billing info from Meta (amount spent by period/message type/source).  
> **Note:** Billing is managed directly by Dentsu – no action needed.

## 4. WhatsApp Flows
A visual editor is in development for drag-and-drop Flow creation. Currently, Flows are created using the Meta Console (JSON style editor, for technical users).

**References:**
- [Meta Flow Components Reference](https://developers.facebook.com/docs/whatsapp/flows/reference/components/)
- [Meta Flow Playground](https://developers.facebook.com/docs/whatsapp/flows/playground)
- [Meta Flows Changelog](https://developers.facebook.com/docs/whatsapp/flows/changelogs/)

## 5. Reporting
Reporting data can be made available at any location and time interval (currently TBC).

---

**END OF DOCUMENT**
