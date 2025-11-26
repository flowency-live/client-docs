# Requirements Document

## Introduction - Jason test again

The Events Management & Client Portal system transforms NinerOps from a reactive booking tool into a proactive sales engine. The system manages events (motorsports races, TV productions, music tours, esports tournaments) as top-level entities that contain multiple client participations, each with their own bookings. It enables L9 staff to conduct targeted outreach campaigns and provides clients with a self-service portal to configure quotes, reuse previous configurations, and manage their event participation.

This feature addresses the need to:
- Organize bookings by real-world events rather than treating them as isolated transactions
- Proactively reach out to clients 3-6 months before events with targeted campaigns
- Allow clients to self-serve quote requests through a secure portal
- Enable configuration reusability across similar events (same client, same event type)
- Track event lifecycle from discovery through booking confirmation
- Provide unified visibility for L9 staff across all clients at an event while maintaining client privacy

## Glossary

- **Event_System**: The NinerOps Events Management & Client Portal system
- **Event**: A real-world occurrence (F1 race, TV production, music festival) that serves as a container for client participations
- **Client_Participation**: A specific client's involvement in an event, containing their quote requests and bookings
- **Event_Calendar**: The master calendar view showing all discovered and created events across all industries
- **Campaign**: A targeted outreach effort to specific clients about an upcoming event
- **Magic_Link**: A secure, time-limited URL that grants client access to their event participation without login
- **Configuration**: The complete set of booking details (vehicles, drivers, dates, locations) for a client's event participation
- **L9_Staff**: Locker 9 internal users (Admin, Operations, Sales staff)
- **Client_User**: External client company users accessing the portal
- **Event_Type**: The industry category of an event (F1, Formula E, WEC, TV Production, Music, Esports, etc.)
- **Event_Status**: The lifecycle stage of an event (Draft, Targeting, Outreach Sent, Quote Requested, Quotes Sent, Quote Accepted, Bookings Confirmed, Not Targeting)
- **Client_Portal**: The external-facing interface where clients view events and configure quotes
- **Outreach_Method**: Communication channel for campaigns (Email, SMS, WhatsApp)
- **Heat_Map**: Visual representation of booking density across time periods to identify coordination challenges
- **Event_Coordinator**: An L9_Staff member assigned to manage on-site operations for an event
- **Staff_Participation**: An L9_Staff member's logistics record for an event, including their travel dates and service requirements

## Requirements

### Requirement 1: Event Management Core

**User Story:** As L9 staff, I want to create and manage events as top-level entities, so that I can organize all client activities around real-world occurrences.

#### Acceptance Criteria

1. WHEN L9_Staff creates an event, THE Event_System SHALL capture event name, Event_Type, start date, end date, location, venue, and Event_Status
2. WHEN L9_Staff views the Event_Calendar, THE Event_System SHALL display all events with visual indicators for Event_Status and Event_Type
3. WHEN L9_Staff filters the Event_Calendar by Event_Type, THE Event_System SHALL display only events matching the selected types
4. WHEN L9_Staff marks an event as "Not Targeting", THE Event_System SHALL exclude the event from campaign suggestions and visually distinguish it in the Event_Calendar
5. WHEN L9_Staff edits an event, THE Event_System SHALL update the event details and maintain audit history of changes

### Requirement 2: Multi-Client Event Participation

**User Story:** As L9 staff, I want to manage multiple clients within a single event, so that I can track all business activity for that event while keeping client data private.

#### Acceptance Criteria

1. WHEN L9_Staff adds a client to an event, THE Event_System SHALL create a Client_Participation entity linked to both the event and the client company
2. WHEN L9_Staff views an event, THE Event_System SHALL display all Client_Participation records with status indicators and booking counts
3. WHEN Client_User accesses their Client_Participation, THE Event_System SHALL display only their own data and SHALL NOT display other clients' information
4. WHEN L9_Staff views the unified event dashboard, THE Event_System SHALL aggregate statistics across all Client_Participation records for that event
5. WHEN multiple Client_Participation records exist for an event, THE Event_System SHALL maintain complete data isolation between clients

### Requirement 3: Client Industry Segmentation

**User Story:** As L9 staff, I want to tag clients with specific event type interests, so that I can target the right clients for each event campaign.

#### Acceptance Criteria

1. WHEN L9_Staff edits a client company, THE Event_System SHALL provide multi-select options for Event_Type interests
2. WHEN L9_Staff creates a campaign for an event, THE Event_System SHALL suggest clients whose Event_Type interests match the event's Event_Type
3. WHEN L9_Staff views a client profile, THE Event_System SHALL display all assigned Event_Type interests
4. WHEN a client has multiple Event_Type interests, THE Event_System SHALL allow independent configurations for each Event_Type
5. WHERE a client company has Event_Type interests defined, THE Event_System SHALL prioritize them in campaign targeting suggestions

### Requirement 4: Proactive Campaign Management

**User Story:** As L9 staff, I want to create targeted outreach campaigns for events, so that I can proactively engage clients 3-6 months before events occur.

#### Acceptance Criteria

1. WHEN L9_Staff creates a campaign for an event, THE Event_System SHALL allow selection of target clients and Outreach_Method (Email, SMS, WhatsApp)
2. WHEN L9_Staff configures campaign timing, THE Event_System SHALL allow setting lead time between 3 and 6 months before the event date
3. WHEN L9_Staff sends a campaign message, THE Event_System SHALL generate unique Magic_Link URLs for each targeted client
4. WHEN L9_Staff sends a campaign, THE Event_System SHALL track message delivery status and update Event_Status to "Outreach Sent"
5. WHEN L9_Staff views campaign analytics, THE Event_System SHALL display open rates, click rates, and response rates per Outreach_Method

### Requirement 5: Historical Client Intelligence

**User Story:** As L9 staff, I want to see which clients attended previous similar events, so that I can prioritize outreach to proven customers.

#### Acceptance Criteria

1. WHEN L9_Staff creates a campaign for an event, THE Event_System SHALL identify clients who participated in the same Event_Type in previous years
2. WHEN L9_Staff views campaign suggestions, THE Event_System SHALL flag clients with previous participation and display their historical booking value
3. WHEN L9_Staff views a client's event history, THE Event_System SHALL display all past Client_Participation records grouped by Event_Type
4. WHEN a client participated in a previous event at the same location, THE Event_System SHALL highlight this in campaign targeting
5. WHERE a client has historical participation data, THE Event_System SHALL calculate average booking value per Event_Type

### Requirement 6: Client Portal Authentication

**User Story:** As a client user, I want to securely access my event participations through both magic links and traditional login, so that I can quickly respond to campaigns or return to manage my bookings.

#### Acceptance Criteria

1. WHEN Client_User clicks a Magic_Link from a campaign, THE Event_System SHALL authenticate the user and display their specific Client_Participation without requiring login credentials
2. WHEN Client_User accesses the Client_Portal via login, THE Event_System SHALL authenticate using email and password and display all their active Client_Participation records
3. WHEN a Magic_Link is generated, THE Event_System SHALL set an expiration time of 30 days from creation
4. WHEN Client_User attempts to use an expired Magic_Link, THE Event_System SHALL prompt for login credentials
5. WHEN Client_User logs in successfully, THE Event_System SHALL maintain session state across multiple Event_System interactions

### Requirement 7: Quote Configuration Interface

**User Story:** As a client user, I want to configure my service requirements for an event, so that I can request a customized quote from L9.

#### Acceptance Criteria

1. WHEN Client_User accesses their Client_Participation, THE Event_System SHALL provide a configuration interface for vehicle types, quantities, driver names, pickup locations, dropoff locations, dates, and special requests
2. WHEN Client_User adds a vehicle booking to their configuration, THE Event_System SHALL require either a driver name or lead passenger name
3. WHEN Client_User submits a configuration, THE Event_System SHALL validate all required fields and update Event_Status to "Quote Requested"
4. WHEN Client_User submits a configuration, THE Event_System SHALL notify L9_Staff via the Event_System notification system
5. WHEN Client_User saves a partial configuration, THE Event_System SHALL persist the data and allow resumption at a later time

### Requirement 8: Configuration Reusability

**User Story:** As a client user, I want to reuse my configuration from previous events of the same type, so that I can quickly create quote requests without re-entering driver names and service details.

#### Acceptance Criteria

1. WHEN Client_User has previous Client_Participation records for the same Event_Type, THE Event_System SHALL offer to copy configuration from those events
2. WHEN Client_User selects a previous configuration to reuse, THE Event_System SHALL copy vehicle types, quantities, driver names, and special requests while excluding dates and locations
3. WHEN Client_User reuses a configuration, THE Event_System SHALL allow modification of all copied fields before submission
4. WHEN Client_User views reusable configurations, THE Event_System SHALL display only configurations from events matching the current Event_Type
5. WHERE multiple previous configurations exist for the same Event_Type, THE Event_System SHALL display them sorted by most recent event date

### Requirement 9: L9 Staff Quote Management

**User Story:** As L9 staff, I want to receive client configuration requests and create quotes, so that I can provide pricing and convert requests to bookings.

#### Acceptance Criteria

1. WHEN a Client_User submits a configuration, THE Event_System SHALL create a quote request record visible to L9_Staff
2. WHEN L9_Staff views a quote request, THE Event_System SHALL display all configuration details including vehicle types, quantities, driver names, dates, locations, and special requests
3. WHEN L9_Staff creates a quote, THE Event_System SHALL allow entry of pricing for each vehicle/service item and update Event_Status to "Quotes Sent"
4. WHEN L9_Staff sends a quote to Client_User, THE Event_System SHALL deliver via the client's preferred Outreach_Method and provide a Magic_Link to view the quote
5. WHEN L9_Staff adjusts quote pricing, THE Event_System SHALL maintain version history of all quote iterations

### Requirement 10: Quote Acceptance and Booking Conversion

**User Story:** As a client user, I want to review and accept quotes, so that I can confirm my booking for the event.

#### Acceptance Criteria

1. WHEN Client_User views a quote, THE Event_System SHALL display all line items with pricing and total cost
2. WHEN Client_User accepts a quote, THE Event_System SHALL update Event_Status to "Quote Accepted" and notify L9_Staff
3. WHEN L9_Staff receives quote acceptance, THE Event_System SHALL provide a workflow to convert quote line items into bookings in the existing booking pipeline
4. WHEN bookings are created from a quote, THE Event_System SHALL link each booking to the Client_Participation and update Event_Status to "Bookings Confirmed"
5. WHEN Client_User accepts a quote, THE Event_System SHALL generate a confirmation message delivered via the client's preferred Outreach_Method

### Requirement 11: Event Calendar Discovery

**User Story:** As L9 staff, I want to import events from public calendars, so that I can quickly populate the Event_Calendar with known motorsports and entertainment events.

#### Acceptance Criteria

1. WHEN L9_Staff initiates calendar import, THE Event_System SHALL provide options to import from predefined Event_Type sources (F1, Formula E, WEC, WRC, E1, Extreme E)
2. WHEN L9_Staff selects an Event_Type source, THE Event_System SHALL retrieve the current season calendar and display events for review
3. WHEN L9_Staff reviews imported events, THE Event_System SHALL allow selection of which events to add to the Event_Calendar
4. WHEN L9_Staff imports events, THE Event_System SHALL create event records with Event_Status set to "Draft"
5. WHERE imported events conflict with existing events, THE Event_System SHALL flag duplicates and allow merge or skip actions

### Requirement 12: Manual Event Creation

**User Story:** As L9 staff, I want to manually create events for private productions and ad-hoc requests, so that I can manage all event types in a unified system.

#### Acceptance Criteria

1. WHEN L9_Staff creates a manual event, THE Event_System SHALL provide a form for event name, Event_Type, dates, location, venue, and notes
2. WHEN L9_Staff creates an event from an inbound enquiry, THE Event_System SHALL allow association with the originating client company
3. WHEN L9_Staff creates a private event, THE Event_System SHALL allow marking it as non-public to exclude from automated discovery
4. WHEN Client_User creates an event request via the Client_Portal, THE Event_System SHALL create a draft event and notify L9_Staff for review
5. WHEN L9_Staff approves a client-created event, THE Event_System SHALL update Event_Status to "Targeting" and allow campaign creation

### Requirement 13: Unified Event Dashboard

**User Story:** As L9 staff, I want to see aggregated statistics for each event, so that I can understand total business activity and resource requirements.

#### Acceptance Criteria

1. WHEN L9_Staff views an event dashboard, THE Event_System SHALL display total number of Client_Participation records, total bookings, and total revenue
2. WHEN L9_Staff views an event dashboard, THE Event_System SHALL display Event_Status breakdown showing how many clients are in each stage
3. WHEN L9_Staff views an event dashboard, THE Event_System SHALL display a timeline of all bookings across all clients for that event
4. WHEN L9_Staff views an event dashboard, THE Event_System SHALL provide a Heat_Map visualization showing booking density by date and time
5. WHERE multiple clients have overlapping booking times, THE Event_System SHALL highlight potential coordination challenges in the Heat_Map

### Requirement 14: Campaign Tracking and Reminders

**User Story:** As L9 staff, I want to track campaign engagement and send reminders, so that I can maximize client response rates.

#### Acceptance Criteria

1. WHEN L9_Staff sends a campaign message, THE Event_System SHALL track delivery status, open events, and click events per client
2. WHEN L9_Staff configures a campaign, THE Event_System SHALL allow setting automated reminder schedules (e.g., 2 weeks after initial outreach)
3. WHEN a reminder is due, THE Event_System SHALL notify L9_Staff to review and manually send the reminder
4. WHEN L9_Staff views campaign analytics, THE Event_System SHALL display engagement metrics per client and per Outreach_Method
5. WHERE a client has not responded within the configured timeframe, THE Event_System SHALL flag them for follow-up action

### Requirement 15: Client Portal Dashboard

**User Story:** As a client user, I want to view all my active and upcoming events in one place, so that I can manage my relationship with L9 efficiently.

#### Acceptance Criteria

1. WHEN Client_User logs into the Client_Portal, THE Event_System SHALL display all Client_Participation records sorted by event date
2. WHEN Client_User views their dashboard, THE Event_System SHALL display Event_Status for each Client_Participation with visual indicators
3. WHEN Client_User selects a Client_Participation, THE Event_System SHALL navigate to the detailed configuration and quote interface
4. WHEN Client_User has pending quote requests, THE Event_System SHALL highlight them prominently on the dashboard
5. WHERE Client_User has upcoming events within 30 days, THE Event_System SHALL display them in a priority section

### Requirement 16: Multi-Channel Communication Delivery

**User Story:** As L9 staff, I want to send campaigns via email, SMS, and WhatsApp, so that I can reach clients through their preferred channels.

#### Acceptance Criteria

1. WHEN L9_Staff creates a campaign, THE Event_System SHALL allow selection of one or more Outreach_Method options
2. WHEN L9_Staff sends an email campaign, THE Event_System SHALL use the configured email service (AWS SES) and include the Magic_Link
3. WHEN L9_Staff sends an SMS campaign, THE Event_System SHALL use the configured SMS service (AWS Pinpoint) and include a shortened Magic_Link
4. WHEN L9_Staff sends a WhatsApp campaign, THE Event_System SHALL integrate with WhatsApp Business API and include the Magic_Link
5. WHEN a campaign is sent via multiple Outreach_Method channels, THE Event_System SHALL track engagement separately for each channel

### Requirement 17: Event Conflict Detection

**User Story:** As L9 staff, I want to see when multiple events occur on the same dates, so that I can plan resource allocation and prioritize targeting.

#### Acceptance Criteria

1. WHEN L9_Staff views the Event_Calendar, THE Event_System SHALL visually indicate dates with multiple events
2. WHEN L9_Staff creates or edits an event, THE Event_System SHALL display warnings if other events exist on overlapping dates
3. WHEN L9_Staff views event conflicts, THE Event_System SHALL display all conflicting events with their Event_Type and location
4. WHEN L9_Staff filters the Event_Calendar by date range, THE Event_System SHALL highlight periods with high event density
5. WHERE events of the same Event_Type occur on the same dates, THE Event_System SHALL flag them as potential targeting conflicts

### Requirement 18: Booking Context Integration

**User Story:** As L9 staff, I want bookings created from event quotes to maintain their event context, so that I can track which bookings belong to which events.

#### Acceptance Criteria

1. WHEN L9_Staff converts a quote to bookings, THE Event_System SHALL link each booking record to the originating Client_Participation
2. WHEN L9_Staff views a booking in the existing booking pipeline, THE Event_System SHALL display the associated event name and client
3. WHEN L9_Staff views a Client_Participation, THE Event_System SHALL display all linked bookings with their current status
4. WHEN a booking status changes in the booking pipeline, THE Event_System SHALL reflect the update in the Client_Participation view
5. WHERE all bookings for a Client_Participation reach "Paid" status, THE Event_System SHALL update Event_Status to "Bookings Confirmed"

### Requirement 19: Event Analytics and Reporting

**User Story:** As L9 staff, I want to analyze event performance and client engagement, so that I can optimize future campaigns and resource allocation.

#### Acceptance Criteria

1. WHEN L9_Staff views event analytics, THE Event_System SHALL display conversion rates from outreach to quote request to booking confirmation
2. WHEN L9_Staff views event analytics, THE Event_System SHALL display total revenue per event and per Event_Type
3. WHEN L9_Staff views event analytics, THE Event_System SHALL provide year-over-year comparison for recurring events
4. WHEN L9_Staff views client analytics, THE Event_System SHALL display engagement rates per Outreach_Method and per Event_Type
5. WHERE historical data exists for an Event_Type, THE Event_System SHALL calculate average booking value and client participation rates

### Requirement 20: Client Self-Service Event Creation

**User Story:** As a client user, I want to request quotes for new events directly through the portal, so that I can initiate bookings without waiting for L9 outreach.

#### Acceptance Criteria

1. WHEN Client_User accesses the Client_Portal, THE Event_System SHALL provide an option to create a new event request
2. WHEN Client_User creates an event request, THE Event_System SHALL capture event name, dates, location, and initial service requirements
3. WHEN Client_User submits an event request, THE Event_System SHALL create a draft event and Client_Participation record and notify L9_Staff
4. WHEN L9_Staff reviews a client-created event request, THE Event_System SHALL allow approval, modification, or rejection with feedback
5. WHEN L9_Staff approves a client-created event, THE Event_System SHALL notify Client_User and allow them to proceed with full configuration

### Requirement 21: L9 Staff Event Participation

**User Story:** As L9 staff, I want to assign event coordinators to events and manage their travel requirements, so that I can track internal team logistics and service needs.

#### Acceptance Criteria

1. WHEN L9_Staff assigns an event coordinator to an event, THE Event_System SHALL create a staff participation record linked to the event and the assigned L9_Staff member
2. WHEN L9_Staff configures coordinator logistics, THE Event_System SHALL capture arrival date, departure date, vehicle requirements, transfer services, and accommodation notes
3. WHEN coordinator dates differ from client dates, THE Event_System SHALL allow independent date configuration for the staff participation
4. WHEN L9_Staff views an event dashboard, THE Event_System SHALL display assigned coordinators with their logistics status
5. WHERE a coordinator requires vehicles or transfers, THE Event_System SHALL allow creation of internal bookings linked to the staff participation record
