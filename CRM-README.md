Feature,Design Detail,Rationale

Customer Data Source,"Hybrid: Stores Name & Email locally, but relies on SSO Microservice for the true source of user identity (user_id, login credentials).","Performance & Audit: Stores essential communication data locally for speed, but ensures the core authentication data is isolated in SSO."
Core Data Fields,"Owned by CRM: Lead Status, Sales Pipeline Stage, Last Contact Date, Support Ticket History, Customer Lifetime Value (CLV), Sales Rep Assignment ID, Lead Score.",This data is critical for sales forecasting and is only owned by the CRM.
Data Consistency,"CRM Listens to SSO: The CRM will subscribe to an event (e.g., UserEmailUpdated) from the SSO Microservice to keep its local email records consistent.",Ensures data integrity across services without tight synchronous coupling.
B. Sales and Lead Management 4. Lead Capture API Proposal: POST /v1/leads/capture

Action: When a marketing form is submitted via the CMS Microservice, the data is sent to this endpoint. The CRM creates the new lead record and initiates the Lead Scoring process.

Robust Pipeline Stages A robust sales cycle often has 5-7 key stages for clarity:
Lead (Initial Contact): Raw contact information has been collected.

Qualified (Needs Assessment): Sales rep has verified the lead meets basic criteria.

Proposal Sent: Pricing/service agreement has been delivered to the prospect.

Negotiation/Commitment: Final terms are being discussed; prospect intends to buy.

Closed/Won: Transaction complete (Triggers Project Handoff).

Closed/Lost: Lead has been deactivated or rejected.

Activity Logging API Proposal: POST /v1/activity/log
Action: Internal staff (Sales Reps, Support) use the CRM UI, which sends structured data (User ID, Activity Type: 'Call', 'Email', 'Meeting', Notes) to this endpoint. The CRM creates an immutable record on the customer's timeline.

C. Collaboration and Entitlements 7. Data Filtering (Authorization) Authorization is handled through Role-Based Access Control (RBAC) enforced by the CRM's backend logic.

Logic: When a user requests data, the CRM decodes the JWT Token (from SSO) to get the Sales Rep Assignment ID (if they are a standard user) or the SLYYFOXX_ADMIN role.

Enforcement:

Standard User: The CRM query is filtered to WHERE lead.assigned_to_id = current_user.id.

Admin: The filter is removed, allowing a global view.

Project Handoff The most critical point where the sale becomes a task.
Proposal: CRM Emits Event: SaleClosedWonV1

Action: When the Sales Rep clicks "Closed/Won," the CRM saves the status and emits a single, asynchronous event containing the customer_id and the sale_details.

Consumer: The Workflow Orchestrator listens to this event and uses it to automatically initiate the "Client Onboarding" process in the Project Management Microservice.

This blueprint ensures the CRM remains focused on the customer relationship while cleanly integrating the outcome of its work (new sales) into the internal operational systems (Project Management).
