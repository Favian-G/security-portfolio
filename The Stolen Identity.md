# Investigating a OAuth Consent-Phishing Attack

## Scenario
Someone got access into the Mad Hat Labs Azure Tenant. I was tasked to investigate a Five-Stage OAuth consent-phishing Kill Chain.

## Environment
Live multi-user Azure training tenant, with Reader access

## The Investigation:
1. **ENTRY:** A user was phished and completed MFA, which resulted in there session token being stolen. Since MFA was satisfied the token carried an MFA-satisfied claim to bypass Conditional Access Policies. This same user was still a Owner Owner on a legacy connector app.

2. **ESCALATE** The threat actor was able to create a new client secret on the legacy app, with the owner rights the user had. That secret allowed the attacker to elevate there access to a highly privileged Service Principal. Which granting them access to API permissions so now they do not need to every login as a human. It is important to highlight that the attacker set the "Expiry date:" nearly a century out.

3. **PIVOT** The attacker wanted to keep access even if the secretes were rotated. So the attacker registered their own app and made sure to add the service principal to the legacy app's Owners list, to ensure they have access even if secrets get rotated. 

4. **PERSIST** The attacker set a costume scope on the legacy application that Exposed an API blade. If their current client secret ever gets removed from the legacy app, the attacker can use the exposed API and the rogue app to launch a separate phishing campaign. The expose API also tells Entra ID that the application can act as a secured backend resource that other applications can call.

5. **LOOT** The only thing left for the attack to do with full control of the rouge app is to set up a way to collect tokens from real users in the tenant by manipulating the redirect URI that every sign in declares on the Authentication blade.
   
## Why this path exists:
You see when attackers try traditional phishing they run into compliance, location rules, and MFA. However when it comes to Consent phishing it takes a different approach because the attacker is now authenticated on the victims trusted device. Now OAuth2PermissionGrant is not removed by a password reset and MFA is not in the way.

## Findings and recommendations

## What I learned
