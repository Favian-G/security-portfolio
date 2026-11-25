# Investigating a OAuth Consent-Phishing Attack

## Scenario
Someone got access into the Mad Hat Labs Azure Tenant. I was tasked to investigate a Five-Stage OAuth Consent Phishing Kill Chain.

## Environment
Live multi-user Azure training tenant, with Reader access

## The Investigation:
1. **ENTRY:** A user was phished and completed MFA, which resulted in there session token being stolen. Since MFA was satisfied the token carried an MFA-satisfied claim to bypass Conditional Access Policies. This same user was still a Owner Owner on a legacy connector app.

2. **ESCALATE** The threat actor was able to create a new client secret on the legacy app, with the owner rights the user had. That secret allowed the attacker to elevate there access to a highly privileged Service Principal. Which granting them access to API permissions so now they do not need to every login as a human. It is important to highlight that the attacker set the "Expiry date:" nearly a century out.
<img width="1676" height="721" alt="Screenshot 2026-11-24 at 11 05 12 AM" src="https://github.com/user-attachments/assets/ba0dbd10-f1dd-44aa-8f92-28f91a595be4" />

3. **PIVOT** The attacker wanted to keep access even if the secretes were rotated. So the attacker registered their own app and made sure to add the service principal to the legacy app's Owners list, to ensure they have access even if secrets get rotated. 
<img width="1674" height="716" alt="Screenshot 2026-11-24 at 11 07 28 AM" src="https://github.com/user-attachments/assets/6003d126-ba48-4e86-a64c-c8f11bbe3d36" />

4. **PERSIST** The attacker set a costume scope on the legacy application that Exposed an API blade. If their current client secret ever gets removed from the legacy app, the attacker can use the exposed API and the rogue app to launch a separate phishing campaign. The expose API also tells Entra ID that the application can act as a secured back end resource that other applications can call.
<img width="1677" height="773" alt="Screenshot 2026-11-24 at 11 09 34 AM" src="https://github.com/user-attachments/assets/ea266120-e4e1-441d-aa6d-496571a5877a" />

5. **LOOT** The only thing left for the attack to do with full control of the rouge app is to set up a way to collect tokens from real users in the tenant by manipulating the redirect URI that every sign in declares on the Authentication blade.
   
## Why this path exists:
You see when attackers try traditional phishing they run into compliance, location rules, and MFA. However when it comes to Consent phishing it takes a different approach, because the attacker is now authenticated on the victims trusted device. Now OAuth2PermissionGrant is not removed by a password reset and MFA is not in the way.

## What broke / what surprised me:
I was incredibly surprised that any standard user can register an app by default. Owning an app registration is an unlogged privilege path that Global Admins miss while conducting reviews. 

## Findings and recommendations:
The client token should defiantly be revoked. Also, the rogue service principal needs to be removed from Owners and the API scope should be deleted | Removing the OAuth2PermissionGrant in place is also important to ensure that the attacker does not retain access | The attackers redirect URI **NEEDS** to be removed to avoid any other access tokens being stolen | Auditing every app registration's Owners list should also be done to strengthen integrity | And one of the **MOST** important things is reconfigure default user app registration, after things are cleaned up and to then rotate credentials. 
