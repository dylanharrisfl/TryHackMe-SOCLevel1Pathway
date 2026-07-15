____________________

# Phishing Analysis

___________________

## Key Takeaways:

#### Lab Still in Progress

__________________

## Phishing Analysis Fundamentals

___________________

Social engineering is natural one of the most common threats facing organization. This is because humans are naturally going to be vulnerabilities. Even with the right policies and tools in place, mistakes can still be made. That is why phishing emails are so effective. They are seen so often because they are a very simply executed method of gaining a foothold into a network.

There are a number of protocols used for handling mail during its travels to its destination:

1. A user sends out an email using SMTP
2. The user's mail server queries DNS servers to locate the recipient domain's mail server
3. Once located, the email is delivered (not mentioned here, but typically goes through things like SPF, DKIM, DMARC, etc. checks) and accepted by the recipient server
4. The recipient opens their mailbox, which then syncs with the mail server
5. Email is downloaded via POP3 or synced via IMAP protocols respectively so the user can read said mail

#### Email Headers

An email contains two parts:
1. Email headers - All metadata related to the message
2. Email body - The actual contents of the email

Looking at a sample email provided within a mail application we see a few fields:

<img width="536" height="128" alt="image" src="https://github.com/user-attachments/assets/db6e8405-6b5d-4a3d-854c-f082a7c31944" />

- From - What email address the email originated from
- To - Who received the email
- Reply to - The email where replies are sent to
- Subject - Subject line of the email

If we then open the message headers, we reveal a lot more in depth information regarding this email:

<img width="944" height="824" alt="image" src="https://github.com/user-attachments/assets/dad3bdfa-c8a9-46ad-a6cc-68db6ec4e574" />

For example the originating servers, the protocols used, the return path, SPF/DKIM/DMARC checks, etc.



