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
3. Open the collection within Bruno
4. Update the environment variables:
- `businessUnitId` - unique identifier of the business unit
- `baseUrl` - base URL for Trustpilot API environment (default is https://api.trustpilot.com/v1/)
- `invitationsBaseUrl` - base URL for Trustpilot invitations API environment (default is https://invitations-api.trustpilot.com/v1/)
- `username` - Trustpilot business username
- `password` - Trustpilot business password
- `apikey` - api key for a business application
- `apisecret` - api secret for the above business application
<br>
<br>
REMOVE BELOW AFTER SCRIPTING IMPLEMENTATION
<br>
<br>
3. Generate an access token. Go [here](https://developers.trustpilot.com/authentication) and review the guide. 
3. Set up your Bruno secret variables (see [Secret Variables](https://docs.usebruno.com/secrets-management/secret-variables)).
4. You will first need to run the `Get Access Token` request to get an `access_token`. Then you will need to add this token to you secret variables (under `token`).

## Running Tests

Execute the requests
