# AI-Waste-Collection-Operations-Automation
AI-powered waste collection automation that turns customer requests into structured jobs, identifies an available driver by service area, sends driver notifications via Telegram, saves the job to Google Sheets, confirms the booking by email, and tracks customer confirmation status and date.

What if a waste collection request could go from customer message to driver assignment, database record, and customer confirmation — automatically?

## The Story

Waste collection businesses deal with a lot of small operational tasks that are easy to overlook but quickly add up.

A customer sends a request. Someone has to read it, understand what they need, identify the pickup location, determine when they want the collection, find an available driver, notify that driver, record the job, and then confirm the booking with the customer.

That's a lot of manual coordination for one request.

So I built a workflow that connects these steps into one automated process using AI, n8n, Google Sheets, Telegram, and Gmail.

This is that build.

## The Problem

A typical waste collection request can arrive as an unstructured message such as:

"I need 3 bags of general waste collected from my office tomorrow morning. The address is 14 Garki Road, Abuja. Please confirm if you can pick it up."

Before automation, someone would need to manually:

- Read and understand the request
- Identify the type of waste
- Extract the quantity
- Find the collection address
- Determine the requested pickup date
- Determine the preferred pickup time
- Identify the service area
- Find an available driver
- Notify the driver
- Create a job record
- Confirm the collection with the customer
- Update the job status

For a business handling many requests, these repetitive steps can consume valuable staff time and create opportunities for errors.

The goal of this workflow is to automate the coordination while keeping the process simple and traceable.

---

## The Solution

Here's what this automation does, step by step:

### 1. Trigger — Customer Request

A customer submits a waste collection request through a webhook.

The request contains basic customer information such as:

- Customer name
- Email
- Phone number
- Collection request

The webhook can be tested using a tool such as Postman.

### 2. Process — AI Information Extraction

The customer's natural-language request is passed to an n8n Information Extractor using Google Gemini.

The AI extracts structured information such as:

- Customer address
- Waste type
- Quantity
- Pickup date
- Preferred pickup time
- Service area

For example:

```text
Customer request:
"I need 3 bags of general waste collected from my office tomorrow morning..."

Extracted information:

Waste Type: General Waste
Quantity: 3
Pickup Date: 2026-08-13
Preferred Time: Morning
Address: 14 Garki Road, Abuja
Service Area: Garki

The workflow also converts relative dates such as "tomorrow" into an actual calendar date using the current date.

**### 3**. Driver Matching

The extracted service area is used to search the Drivers Google Sheet.

The workflow looks for a driver whose:

Service area matches the customer's service area
Availability is set to Yes

For example:

Service Area: Garki
Available: Yes

→ Driver: Musa Ibrahim
→ Driver ID: D001
→ Vehicle: Truck 01

This removes the need for someone to manually search through the driver list.

**### 4**. **Merge **— Combine Job and Driver Information

The customer request data and driver information are combined into one operational record.

The resulting record contains information such as:

Customer: John Doe
Address: 14 Garki Road, Abuja
Waste Type: General Waste
Quantity: 3
Pickup Date: 2026-08-13
Preferred Time: Morning

Driver: Musa Ibrahim
Vehicle: Truck 01
Service Area: Garki

**### 5.** **Create Job Details**

The workflow generates a unique Job ID.

Example:

WC-20260812231606

It also creates the initial operational status:

Status: Scheduled
Customer Confirmation: Pending

This gives every collection request a reference that can be tracked.

**### 6. Act — Driver Notification**

The assigned driver receives the collection details through Telegram.

The notification includes:

Job ID
Customer
Customer phone
Pickup address
Waste type
Quantity
Pickup date
Preferred time
Assigned driver
Vehicle

This means the driver receives the information without an operations staff member manually forwarding the request.

**### 7. Save Job to Database**

The scheduled collection is saved to a Google Sheets database called:

Waste Collection Jobs

The sheet tracks:

Job ID
Customer Name
Phone Number
Customer Address
Customer Email
Waste Type
Quantity
Pickup Date
Preferred Time
Driver
Status
Customer Confirmation
Confirmation Date

Google Sheets is used here as a simple database for the workflow.

**### 8. Customer Confirmation**

After the job is saved, Gmail sends a confirmation email to the customer.

The email contains the customer's:

Job ID
Pickup address
Waste type
Quantity
Pickup date
Preferred time
Assigned driver

This gives the customer a clear record of the scheduled collection.

**### 9. Update Confirmation Status**

After the confirmation email is sent, the Google Sheets record is updated.

The workflow changes:

Customer Confirmation:
Pending → SENT

It also records the confirmation date and time.

Example:

Customer Confirmation: SENT
Confirmation Date: 2026-08-12 23:56:43

This makes it possible to see whether the customer notification has already been sent.

💡 **Beginner note:** A webhook is simply a URL that allows another application to send information into your workflow. In this project, the webhook acts as the front door for a new waste collection request.

 **Tools Used**
Tool	Role in this workflow
n8n	Workflow orchestration and automation
Google Gemini	AI-powered information extraction
Google Sheets	Driver database and Waste Collection Jobs database
Telegram	Driver job notification
Gmail	Customer confirmation email
Postman	Testing the customer request webhook

 **Demo**
Workflow
Customer Request
       ↓
    Webhook
       ↓
  Edit Fields
       ↓
Information Extractor
       ↓
     Merge
       ↓
Find Available Driver
       ↓
     Merge
       ↓
Create Job Details
       ├──────────────→ Driver Notification
       │
       ↓
    Save Job
       ↓
Customer Confirmation
       ↓
 Update Job

**Example Request:**
{
  "customer_name": "John Doe",
  "email": "john@example.com",
  "phone": "08012345678",
  "request": "I need 3 bags of general waste collected from my office tomorrow morning. The address is 14 Garki Road, Abuja. Please confirm if you can pick it up."
}

**Example Result:**
Job ID: WC-20260812231606
Customer: John Doe
Waste Type: General Waste
Quantity: 3
Pickup Date: 2026-08-13
Preferred Time: Morning
Address: 14 Garki Road, Abuja

Driver: Musa Ibrahim
Vehicle: Truck 01
Status: Scheduled

Customer Confirmation: SENT

<img width="743" height="330" alt="image" src="https://github.com/user-attachments/assets/ec1796b0-f319-4f34-98ee-a86963ed6936" />


 **How to Set It Up**
1. Import or build the workflow

Import the n8n workflow into your n8n instance or recreate the nodes manually.

2. Connect your credentials

You will need credentials for:

Google Gemini
Google Sheets
Telegram
Gmail
3. Create the Google Sheets database

Create a workbook containing at least two sheets:

Waste Collection Jobs
Job ID
Customer Name
Phone Number
Customer Address
Customer Email
Waste Type
Quantity
Pickup Date
Preferred Time
Driver
Status
Customer Confirmation
Confirmation Date
Drivers
Driver ID
Driver Name
Driver Phone
Service Area
Available
Vehicle

Example driver:

D001
Musa Ibrahim
08011111111
Garki
Yes
Truck 01

4. Configure the webhook

Use the webhook URL generated by your n8n workflow.

Send a POST request with JSON similar to:

{
  "customer_name": "John Doe",
  "email": "john@example.com",
  "phone": "08012345678",
  "request": "I need 3 bags of general waste collected from my office tomorrow morning. The address is 14 Garki Road, Abuja."
}
5. Configure the AI Information Extractor

Connect Google Gemini to the Information Extractor node.

Configure the extractor to identify:

Address
Waste type
Quantity
Pickup date
Preferred time
Service area

For relative dates such as "tomorrow", the system prompt should provide the current date and instruct the model to return the actual date in YYYY-MM-DD format.

6. Configure driver matching

Use the extracted service area to filter the Drivers sheet.

The workflow should match:

service_area = customer's service area
AND
available = Yes
7. Configure notifications

Connect Telegram for driver notifications and Gmail for customer confirmations.

Replace the test Chat ID and email credentials with your own production credentials.

8. Test the workflow

Start with a simple request.

For example:

I need 3 bags of general waste collected from my office tomorrow morning. The address is 14 Garki Road, Abuja.

Verify that:

AI extracts the information correctly
The correct service area is identified
An available driver is found
The driver receives a Telegram notification
A new job is created in Google Sheets
The customer receives a Gmail confirmation
Customer Confirmation changes to SENT
Confirmation Date is recorded

⚠️ **Common mistakes**

Phone numbers

Store phone numbers as text/string values, not numbers.

Otherwise, a number such as:

08012345678

can become:

8012345678

because the leading zero is removed.

Merge fields

Be careful when combining customer and driver data.

Both records can contain a field called phone. If the fields are not named separately, one value can overwrite the other.

Use:

phone
driver_phone

instead.

Append Row during testing

Google Sheets Append Row creates a new row every time the workflow runs.

Repeatedly testing the complete workflow can therefore create duplicate job records.

For production, consider adding duplicate detection or an update/upsert pattern before appending new jobs.

What I Learned / Why It Matters
1. AI doesn't need to control the entire workflow.

The AI's job here is focused: understand an unstructured customer request and turn it into structured information.

The rest of the workflow uses predictable automation logic.

That makes the architecture simpler and easier to debug.

2. Structured data is what makes AI useful operationally.

The customer's message is messy and conversational.

The workflow turns it into fields that other systems can actually use:

Address
Waste Type
Quantity
Pickup Date
Preferred Time
Service Area

Once the information is structured, the workflow can make decisions and trigger actions.

3. Good automation is about orchestration.

The value isn't in using AI by itself.

It's in connecting:

Customer request
      ↓
Understanding
      ↓
Decision
      ↓
Action
      ↓
Database
      ↓
Customer communication

Each step has a clear responsibility.

4. Real-world workflows need tracking.

Sending an email isn't enough.

The business also needs to know:

Was the job created?
Which driver was assigned?
What is the job status?
Was the customer notified?
When was the confirmation sent?

That is why the workflow records both Customer Confirmation and Confirmation Date.

5. Simple tools can solve meaningful business problems.

This workflow uses n8n, Gemini, Google Sheets, Telegram, and Gmail.

No custom backend application was required to demonstrate the complete operational flow.

The important skill is understanding the business process and designing the automation around it.

**About This Project**

Built by Queen Ikwuji, founder of QueenEagleAI.

I build AI-powered workflow automations that help businesses reduce repetitive work, improve operational efficiency, and connect disconnected processes.

My focus is not simply adding AI to a workflow.

It's identifying where businesses are repeatedly reading, deciding, copying, notifying, and updating — and designing systems that can handle those processes more efficiently.

I build in public using tools such as n8n, AI models, APIs, databases, and no-code platforms.

📌 Follow the build-in-public journey on LinkedIn: https://www.linkedin.com/in/queenikwuji/ 

💬 Want a workflow like this for your business? Get in touch: mailto:queenikwuji@gmail.com

Got questions, ideas, or want to adapt this workflow to another industry? I'd love to see what you build with it.
