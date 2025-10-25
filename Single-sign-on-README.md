Overview and Responsibility The SSO Microservice is the core Authentication and Identity Provider for the entire slyyfoxxmedia.com ecosystem. It is powered by Amazon Cognito and is critical for enforcing security across all 18 microservices.
Subdomain: sso.slyyfoxxmedia.com

Status: Phase 1: Security and Core Data Foundation

Single Responsibility: To verify user identity, issue secure access tokens (JWTs), and manage the core user profile.

Feature,Design Decision,Notes Primary Identifier,Username,Must be unique and enforced by the Cognito User Pool. Core Attributes Stored,"Username, First Name, Last Name, Phone Number, slyyfoxx_id (internal ID).",These attributes are stored in Cognito and are available via API Lookup. Authentication Flow,Hybrid,"Uses a Custom UI built by the SSO service, with Cognito as the secure backend/identity provider." Password Policy,"Minimum 8 characters, strong complexity.",Enforced by the Cognito User Pool configuration. MFA,Optional,Multi-Factor Authentication is offered to users but is not mandatory for initial access.

Role Name (Cognito Group),User Type,Access Scope SLYYFOXX_ADMIN,"Internal Employees (PM Admins, HR)",Highest Authority. Full access to Admin and Project Management APIs. MERCHANT,External Sellers/Vendors,Access to merchant-specific endpoints on E-commerce and Marketplace for product management. CUSTOMER,All registered end-users (Paid and Free).,"Accesses Marketplace, Cart, Social, and Blog user interfaces."

Claim Name,Value Source,Dependent Microservices subscription_tier,"free, trial, pro, elite (Updated by Subscriptions Microservice).","CMS, Analytics, Subscriptions (enforces premium feature limits)." has_fantasy_access,Boolean flag (True/False).,Social-Tech (to display the Fantasy Sports feature).

his service exposes two critical internal API endpoints for the system's infrastructure and other microservices.

3.1. Token Validation API (For the API Gateway) This endpoint is called on every request to a protected API to confirm the user's identity.

Endpoint: POST /v1/sso/validate-token

Request Body: Includes the user's access token (JWT).

Response: If valid, returns the user's user_id, username, and all Role/Tier Claims.

3.2. User Lookup API (For Data Retrieval) This internal API is used by other backend services when they need non-sensitive, static user data.

Endpoint: GET /v1/sso/user/{user_id}

Action: CRM or PM calls this endpoint to get the user's name/phone number.

Authorization: Access is restricted to internal microservices only (via secure API key, not a public token).

3.3. Admin Management Authority The SLYYFOXX_ADMIN role is the sole authority allowed to call the SSO's internal APIs to manage or create new user accounts (e.g., adding a new internal employee).

About
Users are authenticated and given a unique ID

Resources
 Readme
 Activity
Stars
 0 stars
Watchers
 0 watching
Forks
 0 forks
Releases
No releases published
Create a new release
Packages
No packages published
Publish your first package
Footer
