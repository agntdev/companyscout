# Business Research Assistant — Bot specification

**Archetype:** custom

**Voice:** professional and concise — write every user-facing message, button label, error, and empty state in this voice.

A Telegram bot that provides lawful, public business contact and registration details for companies. It returns verified company information including name, phone, email, website, LinkedIn, registration status, and links to public sources. Designed for sales, business development, compliance, and entrepreneurial use.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- Sales reps
- Business development professionals
- Compliance analysts
- Entrepreneurs

## Success criteria

- Users can retrieve accurate public company information with /search command
- Users can view sources and save company records
- Rate limiting prevents abuse while allowing productive usage

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Open the main menu
- **/search** (command, actor: user, command: /search) — Search for company information by name, phone, address, or registration number
- **/help** (command, actor: user, command: /help) — Show help and usage instructions
- **/recent** (command, actor: user, command: /recent) — View the last 10 searches performed by the user
- **View sources** (button, actor: user, callback: view_sources) — Show links to public registries where information was found
- **Save to list** (button, actor: user, callback: save_to_list) — Save the current company record to the user's saved list
- **New search** (button, actor: user, callback: new_search) — Start a new company search

## Flows

### Company search
_Trigger:_ /search

1. User enters /search command
2. Bot requests search query input
3. User provides search term (name/phone/address/registration number)
4. Bot performs search and returns company card with key fields
5. User interacts with buttons to view sources, save, or search again

_Data touched:_ Company record, Search query

### Recent searches
_Trigger:_ /recent

1. User enters /recent command
2. Bot displays list of last 10 searches with timestamps
3. User can select a previous search to view details again

_Data touched:_ Search query

### Save company record
_Trigger:_ save_to_list

1. User clicks 'Save to list' button
2. Bot confirms save and displays saved list count
3. User can view saved records at any time

_Data touched:_ Company record

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **Company record** _(retention: persistent)_ — Public business information including name, registration details, contact info, and source links
  - fields: Company name, Registration number, Jurisdiction, Status, Website, Phone, Email, Address, LinkedIn, Source links, Confidence score
- **Search query** _(retention: persistent)_ — User's search input and associated results
  - fields: Search term, Timestamp, Company ID
- **User saved list** _(retention: persistent)_ — Up to 100 saved company records per user
  - fields: Company records, Timestamp

## Integrations

- **Telegram** (required) — Bot API messaging
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Configure rate limits (default 60 searches/hour)
- Set maximum saved records per user (default 100)
- Enable/disable specific data sources

## Notifications

- Rate limit warnings when approaching daily limits
- Success/failure notifications for search operations

## Permissions & privacy

- Only public business information is retrieved and stored
- No personal data is collected or processed
- User search history is private and not shared

## Edge cases

- No results found for a search query
- Multiple companies match a search term
- User exceeds rate limits
- User tries to save more than 100 records

## Required tests

- Verify /search command returns accurate company cards with source links
- Test rate limiting prevents abuse
- Validate saved records are persisted and accessible

## Assumptions

- Public business data is available through official registries and directories
- Users will only search for legitimate business purposes
- Rate limits are sufficient to prevent abuse while allowing productive use
