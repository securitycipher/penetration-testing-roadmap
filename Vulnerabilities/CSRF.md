# What is CSRF?
CSRF, which stands for Cross-Site Request Forgery, is a type of security vulnerability that exploits the trust a website has in a user's browser. It allows an attacker to perform actions on behalf of a user without their knowledge or consent.

## How Does CSRF Work?
- Authenticated User: The victim, usually an authenticated user, is tricked into visiting a malicious website or clicking on a specially crafted link.

- Automatic Requests: The malicious website or link contains code that automatically sends forged requests to a different website where the victim is authenticated. These requests can perform actions such as changing account settings, making purchases, or transferring funds.

- Trusted Session: Since the request originates from the victim's browser, the targeted website sees it as a legitimate request coming from the authenticated user.

## Example Scenario
Let's say you're logged into your online banking account. While still logged in, you visit a malicious website, perhaps disguised as a harmless link shared on a forum. Unbeknownst to you, this website contains hidden code that automatically submits a request to transfer funds from your bank account to the attacker's account.

## Impact of CSRF
- Unauthorized Transactions: Attackers can perform unauthorized actions on behalf of the victim, such as transferring funds, changing account settings, or deleting data.

- Data Theft: CSRF attacks can lead to the theft of sensitive information stored on the targeted website.

- Account Takeover: If the attacker gains control over the victim's account through CSRF, they can effectively take over the account and carry out malicious activities.

## Mitigating CSRF
- CSRF Tokens: Include unique tokens in each request that are validated by the server to ensure the request originated from a legitimate source.

- Same-Site Cookies: Set cookies to be sent only to the same origin, reducing the risk of CSRF attacks.

- Referrer Policy: Configure servers to check the referrer header of incoming requests to ensure they originated from trusted sources.

- Prompting User Action: Require users to confirm sensitive actions with additional authentication steps, such as entering a password or OTP.

## Conclusion
CSRF attacks exploit the trust between a user's browser and a website they are logged into. By understanding how CSRF works and implementing appropriate security measures, such as CSRF tokens and same-site cookies, web developers can help protect against this type of vulnerability. Regular security audits and updates are essential to maintaining a secure web environment.
