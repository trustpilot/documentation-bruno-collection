# documentation-bruno-collection
A Bruno collection for testing our [public APIs](https://developers.trustpilot.com), owned by the [API team](https://trustpilot-production.atlassian.net/wiki/spaces/DPT/pages/1445560325/API+TEAM).

Public calls are based on public information that any consumer or business can find on Trustpilot without the use of an API.

Private calls are not accessible to everyone. They're only accessible to businesses with a Trustpilot business profile.

## Prerequisites
- [Bruno](https://www.usebruno.com/) client installed on your machine
- A Trustpilot API key and secret (obtain from Trustpilot Business, see article on [how to use Trustpilot APIs](https://support.trustpilot.com/hc/en-us/articles/207309867-How-to-use-Trustpilot-APIs))

## Setup
1. Clone this repository (or download as zip and unzip)
2. Launch Bruno client
3. Open the collection within Bruno client
4. Update the environment variables:
- `businessUnitId` - unique identifier of the business unit
- `baseUrl` - base URL for Trustpilot API environment (default is https://api.trustpilot.com/v1/)
- `invitationsBaseUrl` - base URL for Trustpilot invitations API environment (default is https://invitations-api.trustpilot.com/v1/)
- `username` - Trustpilot business username
- `password` - Trustpilot business password
- `apikey` - api key for the business application
- `apisecret` - api secret for the (above) business application

## Make API request
1. Select endpoint
2. Update any path and query parameters
3. Execute request

## Authentication
The Bruno collection has been configured to handle token authentication for you. On your first request (and prior to any following request), a script will fetch a valid token based on the environment variables you've configured, and populate the request accordingly. When a token expires, the script will fetch a new token automatically before the next request.

## Secrets
Secrets are not to be shared with anyone. Do not untick the 'Secret' checkbox when configuring environment variables - this will ensure your secret value(s) are not commit to the collection's files.

# TODO
- Update default baseUrl and invitationsBaseUrl so that they're not pointing to TP staging but to prod