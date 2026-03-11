## 🔹 1. What Happens If Client Secret Is Exposed?

If exposed:

- Attacker can generate access tokens.
    
- Full API access to Salesforce data.
    
- Data theft / deletion.
    
- Automation misuse.
    
- Compliance violation.
    

Mitigation:

- Regenerate secret immediately.
    
- Rotate credentials.
    
- Revoke connected app sessions.
    
- Use IP restrictions.
    

---

## 🔹 2. How to Handle Token Expiry

Access Token:

- Short-lived (session based)
    

Refresh Token:

- Used to generate new access token.
    

Flow:

1. Access token expires.
    
2. Use refresh token.
    
3. Get new access token without re-login.
    

Best Practice:

- Always implement refresh token flow.
    
- Never ask user to login repeatedly.


![[Pasted image 20260228205615.png]]

# 1️⃣ Possible Security Risks in API Integration


## 1. Client Secret Exposure

- Attacker can generate access tokens.
    
- Full API access depending on scopes.
    
- Data theft, deletion, or manipulation.
    

## 2. Access Token Leakage

- Tokens intercepted from logs, browser, or network.
    
- Session hijacking possible.
    

## 3. Hardcoded Credentials

- Exposed in source code or GitHub.
    
- Difficult to rotate.
    
- High breach risk.
    

## 4. Using HTTP Instead of HTTPS

- Tokens can be intercepted (MITM attack).
    
- Credentials exposed in plaintext.
    

## 5. Over-Permissioned OAuth Scopes

- App gets more access than needed.
    
- Violates least-privilege principle.
    

## 6. No Token Expiry Handling

- Application breaks after expiration.
    
- Users forced to re-authenticate repeatedly.
    

## 7. No IP Restrictions

- Any attacker from anywhere can attempt access.

# 2️⃣ How to Prevent Unauthorized Access

## ✔ Use OAuth 2.0 Properly

- Never use basic authentication.
    
- Always use token-based access.
    

## ✔ Apply Least Privilege

- Grant only required OAuth scopes.
    
- Avoid full-access permissions.
    

## ✔ Secure Secret Storage

- Use environment variables.
    
- Use secret manager (e.g., Azure Key Vault).
    
- Never store secrets in frontend code.
    

## ✔ Enable IP Restrictions

- Restrict Connected App to trusted IP ranges.
    

## ✔ Monitor API Usage

- Enable audit logs.
    
- Track suspicious API calls.
    

## ✔ Rotate Secrets Periodically

- Regenerate Client Secret if compromised.
    
- Revoke unused tokens.