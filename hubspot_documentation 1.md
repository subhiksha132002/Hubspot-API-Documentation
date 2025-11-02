# HubSpot API Documentation

## 1. Introduction to HubSpot

HubSpot is a Customer Relationship Management (CRM) platform that helps businesses manage contacts, companies, deals, and tickets. The HubSpot API allows applications to connect with HubSpot data — for example, to fetch contacts, create deals, or update records.

Using the API requires **authentication** (to verify the app’s identity) and **authorization** (to define what the app can access). HubSpot offers two ways to connect: the older **Legacy App** method and the newer **OAuth 2.0 Project** method.

---

## 2. Legacy App Setup (For Testing Purposes)

This method is ideal for internal testing and API documentation because it provides a non-expiring access token.

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
8. Ignore the **Webhook** section for testing API Endpoints.
9. Save the app — HubSpot will generate an **Access Token**.
10. Use this token in API requests for quick testing and documentation.

**Example usage:**

```http
GET https://api.hubapi.com/crm/v3/objects/contacts
Authorization: Bearer <access_token>
```

---

## 3. New Platform (Project + OAuth 2.0 Flow)

HubSpot’s newer developer platform uses **Projects** with **OAuth 2.0** for secure and scalable integrations.

### Steps:

1. Log in to your **HubSpot Developer Account**.
2. Create a **New App (Project)**.
3. Note down the following credentials from the app’s settings:

   * **Client ID**
   * **Client Secret**
4. Add one or more **Redirect URIs** — the URLs to which HubSpot will redirect users after authorization.
5. Choose the required **Scopes** (permissions), for example:

   * `crm.objects.contacts.read`
   * `crm.objects.deals.write`
6. Choose the **Distribution Type**:

   * *Private* → For internal/testing purposes
   * *Public* → For multiple HubSpot accounts or marketplace distribution
7. Save the app configuration.

---

### OAuth 2.0 Authentication Flow

1. **Generate an Authorization URL:**
   Construct the URL using your app credentials:

   ```text
   https://app.hubspot.com/oauth/authorize
   ?client_id=<CLIENT_ID>
   &redirect_uri=<REDIRECT_URI>
   &scope=<SCOPES>
   &state=<STATE>
   ```

2. **User Authorization:**
   The user logs in to HubSpot, reviews permissions, and grants access.

3. **Receive Authorization Code:**
   HubSpot redirects to the specified redirect URI with a temporary `code`.

4. **Exchange Code for Tokens:**
   Send a POST request:

   ```http
   POST https://api.hubapi.com/oauth/v1/token
   Content-Type: application/x-www-form-urlencoded

   grant_type=authorization_code&client_id=<CLIENT_ID>&client_secret=<CLIENT_SECRET>&redirect_uri=<REDIRECT_URI>&code=<CODE>
   ```

5. **Receive Tokens:**

   * `access_token`: short-lived
   * `refresh_token`: long-lived

6. **Use Access Token:**
   Include the access token in your API requests:

   ```http
   Authorization: Bearer <access_token>
   ```

7. **Refresh Token:**
   When the access token expires, request a new one using the refresh token:

   ```http
   POST https://api.hubapi.com/oauth/v1/token

   grant_type=refresh_token&client_id=<CLIENT_ID>&client_secret=<CLIENT_SECRET>&refresh_token=<REFRESH_TOKEN>
   ```

---

### Comparison

| Feature          | Legacy App               | OAuth Project               |
| ---------------- | ------------------------ | --------------------------- |
| Token Expiry     | Does not expire          | Expires, refreshable        |
| Security         | Basic                    | Stronger, industry standard |
| Setup Complexity | Simple                   | Moderate                    |
| Best For         | Testing / Single Account | Production / Multi-Account  |

---
