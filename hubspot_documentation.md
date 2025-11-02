# HubSpot API Documentation

## Overview
HubSpot provides a comprehensive suite of APIs that allow developers to programmatically interact with data and business processes managed through its platform.

This document provides an overview and analysis of HubSpot’s REST APIs.  
It is intended for developers who want to integrate their applications with HubSpot.  

All API requests are made over HTTPS and require an **access token** for authentication.

---

##  Authentication

### Access Token
HubSpot uses **OAuth 2.0** for authentication.  
In this analysis, we use a **Private (Legacy) App Access Token**.

#### Steps to Generate Token
### Steps:

1. Sign in to your **HubSpot Developer Account**.
2. Create a **Test Account** under your developer portal.
3. In the test account, open the **Settings** panel.
4. From the left sidebar, select **Integrations → Legacy Apps**.
5. Create a new app and set it to **Private**.
6. Provide basic details:

   * App name
   * Description
   * Logo (optional)
7. Select **All Scopes** to allow full API access for testing.
8. Save the app — HubSpot will generate an **Access Token**.
9. Use this token in API requests for quick testing and documentation.

>  Keep your token secure. Do not share or expose it in public repositories.

#### Example Header
```bash
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json 
```
## Hubspot CRM Objects APIs

HubSpot’s **CRM (Customer Relationship Management)** system forms the core of its platform.  
It stores and organizes information about contacts, companies, deals, tickets, and other business entities.  

The **CRM Object APIs** allow developers to programmatically:
- Create, read, update, and delete CRM records  
- Define object properties  
- Manage associations between records (e.g., linking a contact to a company)  
- Search and filter records using queries

---

## CRM Object Model Overview
Each CRM record represents an instance of an **object**.  
- **Object:** A category (e.g., Contacts, Companies, Deals)  
- **Record:** A single entry (e.g., John Smith, XYZ Corp)  
- **Property:** A field or attribute (e.g., email, phone, dealstage)  
- **Association:** Relationship between records (e.g., Contact → Company)

---
## Common CRM Object Type IDs
| Object | Type ID | Description |
|---------|----------|-------------|
| Contacts | 0-1 | Information about an individual person |
| Companies | 0-2 | Information about a business or organization |
| Deals | 0-3 | Sales opportunities and transactions |
| Tickets | 0-5 | Customer service requests or issues |
| Custom Objects | 2-XXX | Developer-defined entities for custom data |

---

## CRM API | Contacts
In HubSpot, contacts store information about the individual people that interact with your business. The contacts endpoints allow you to create and manage contact records in your HubSpot account, as well as sync contact data between HubSpot and other systems.

### Create Contacts

The **Contacts API** allows you to add new contacts to your HubSpot CRM.  
You can create either a single contact or multiple contacts in a single request.

#### Required Fields

When creating a contact, at least one of the following properties is required:

| Property    | Type   | Description                                                              |
| ----------- | ------ | ------------------------------------------------------------------------ |
| `email`     | string | Contact’s email address (recommended as it acts as a unique identifier). |
| `firstname` | string | Contact’s first name.                                                    |
| `lastname`  | string | Contact’s last name.                                                 |

| Property         | Type        | Description                                                                                                                     |
| ---------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `lifecyclestage` | enumeration | Lifecycle stage (e.g., `subscriber`, `lead`, `customer`). Retrieve all options via `GET /crm/v3/properties/0-1/lifecyclestage`. |
| `phone`          | string      | Contact’s phone number.                                                                                                         |
| `mobilephone`    | string      | Contact’s mobile number.                                                                                                        |
| `fax`            | string      | Fax number.                                                                                                                     |
| `jobtitle`       | string      | Contact’s job title.                                                                                                            |
| `address`        | string      | Street address.                                                                                                                 |
| `city`           | string      | City.                                                                                                                           |
| `state`          | string      | State.                                                                                                                          |
| `country`        | string      | Country.                                                                                                                        |
| `zip`            | string      | Postal code.                                                                                                                    |
| `hs_timezone`    | enumeration | Time zone. Retrieve all available values via `GET /crm/v3/properties/0-1/hs_timezone`.                                          |


#### 1. Create a Single Contact
To create one contact, send a **POST** request to the following endpoint:

**Example: Create Contact**

**Endpoint:** 
POST https://api.hubapi.com/crm/v3/objects/contacts

**Headers:**

Authorization: Bearer YOUR_ACCESS_TOKEN

Content-Type: application/json

**Example Request Body:**
```json
{
  "properties": {
    "email": "example@hubspot.com",
    "firstname": "Jane",
    "lastname": "Doe"
  }
}
```

**Response Example**
```json
{
    "id": "309706358493",
    "properties": {
        "createdate": "2025-11-02T17:21:28.373Z",
        "email": "example@hubspot.com",
        "firstname": "Jane",
        "hs_all_contact_vids": "309706358493",
        "hs_associated_target_accounts": "0",
        "hs_currently_enrolled_in_prospecting_agent": "false",
        "hs_email_domain": "hubspot.com",
        "hs_full_name_or_email": "Jane Doe",
        "hs_is_contact": "true",
        "hs_is_unworked": "true",
        "hs_lifecyclestage_lead_date": "1762104088373",
        "hs_marketable_status": "false",
        "hs_marketable_until_renewal": "false",
        "hs_membership_has_accessed_private_content": "0",
        "hs_object_id": "309706358493",
        "hs_object_source": "INTEGRATION",
        "hs_object_source_id": "23237921",
        "hs_object_source_label": "INTEGRATION",
        "hs_pipeline": "contacts-lifecycle-pipeline",
        "hs_prospecting_agent_actively_enrolled_count": "0",
        "hs_prospecting_agent_total_enrolled_count": "0",
        "hs_registered_member": "0",
        "hs_sequences_actively_enrolled_count": "0",
        "lastmodifieddate": "2025-11-02T17:21:28.373Z",
        "lastname": "Doe",
        "lifecyclestage": "lead",
        "num_notes": "0"
    },
    "createdAt": "2025-11-02T17:21:28.373Z",
    "updatedAt": "2025-11-02T17:21:28.373Z",
    "archived": false,
    "url": "https://app-na2.hubspot.com/contacts/244265456/record/0-1/309706358493"
}
```
**Status Code Returned: 201**

#### Create Contacts with Associations

When creating a contact, you can also associate it with existing records or activities by including an associations object.

| Parameter | Description                                                                            |
| --------- | -------------------------------------------------------------------------------------- |
| `to`      | Specifies the record or activity to associate (identified by its unique `id`).         |
| `types`   | Defines the association type, including `associationCategory` and `associationTypeId`. |

For example, to associate a new contact with an existing company and email, your request would look like the following:

```json 
{
  "properties": {
    "email": "example@hubspot.com",
    "firstname": "Jane",
    "lastname": "Doe",
    "phone": "+18884827768",
    "company": "HubSpot",
    "website": "hubspot.com",
    "lifecyclestage": "marketingqualifiedlead"
  },
  "associations": [
    {
      "to": {
        "id": 123456
      },
      "types": [
        {
          "associationCategory": "HUBSPOT_DEFINED",
          "associationTypeId": 279
        }
      ]
    },
    {
      "to": {
        "id": 556677
      },
      "types": [
        {
          "associationCategory": "HUBSPOT_DEFINED",
          "associationTypeId": 197
        }
      ]
    }
  ]
} 
```

### come here after creating company for association 

#### 3. Retrieve All Contacts
Retrieves all contact records from HubSpot. You can specify which properties, associations, and historical values to include using query parameters.

**Query Parameters**
| Name                    | Type     | Default | Description                                                                          |
| ----------------------- | -------- | ------- | ------------------------------------------------------------------------------------ |
| `limit`                 | integer  | 10      | The maximum number of results per page.                                              |
| `after`                 | string   | —       | A paging cursor token used to fetch the next page of results.                        |
| `properties`            | string[] | —       | Comma-separated list of contact properties to include in the response.               |
| `propertiesWithHistory` | string[] | —       | Comma-separated list of properties with their historical values.                     |
| `associations`          | string[] | —       | Comma-separated list of associated object types to include (e.g., companies, deals). |
| `archived`              | boolean  | false   | Whether to return only archived contacts.                                            |

**Endpoint:**
GET https://api.hubapi.com/crm/v3/objects/contacts

**Headers:**

Authorization: Bearer YOUR_ACCESS_TOKEN

Content-Type: application/json

**Response Example**
```json
{
    "results": [
        {
            "id": "309549136624",
            "properties": {
                "createdate": "2025-11-02T14:36:21.770Z",
                "email": "emailmaria@hubspot.com",
                "firstname": "Maria",
                "hs_object_id": "309549136624",
                "lastmodifieddate": "2025-11-02T16:36:34.874Z",
                "lastname": "Johnson (Sample Contact)"
            },
            "createdAt": "2025-11-02T14:36:21.770Z",
            "updatedAt": "2025-11-02T16:36:34.874Z",
            "archived": false,
            "url": "https://app-na2.hubspot.com/contacts/244265456/record/0-1/309549136624"
        }
    ],
    "paging": {
        "next": {
            "after": "309549136625",
            "link": "https://api.hubapi.com/crm/objects/v3/contacts?limit=1&after=309549136625"
        }
    }
}
```
**Status Code Returned : 200**

#### Retrieve Contact by ID
Retrieve a contact by its ID (`contactId`) or by a unique property (`idProperty`). You can specify what is returned using the properties query parameter.

**Endpoint:**
Get https://api.hubapi.com/crm/v3/objects/contacts/{contactId}

**Headers:**

Authorization: Bearer YOUR_ACCESS_TOKEN

Content-Type: application/json

**Response Example**
```json
{
    "id": "309549136624",
    "properties": {
        "createdate": "2025-11-02T14:36:21.770Z",
        "email": "emailmaria@hubspot.com",
        "firstname": "Maria",
        "hs_object_id": "309549136624",
        "lastmodifieddate": "2025-11-02T16:36:34.874Z",
        "lastname": "Johnson (Sample Contact)"
    },
    "createdAt": "2025-11-02T14:36:21.770Z",
    "updatedAt": "2025-11-02T16:36:34.874Z",
    "archived": false,
    "url": "https://app-na2.hubspot.com/contacts/244265456/record/0-1/309549136624"
}
```
**Status Code Returned : 200**