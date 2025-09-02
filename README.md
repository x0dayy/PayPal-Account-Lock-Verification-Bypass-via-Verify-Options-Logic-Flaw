<img src="https://raw.githubusercontent.com/Tarikul-Islam-Anik/Animated-Fluent-Emojis/master/Emojis/Symbols/Warning.png" alt="Warning" width="40" height="40" /> PayPal ATO via Verification Workflow Manipulation

CVE-PENDING | ZERO-DAY | UNPATCHED

A critical logic flaw in PayPal's account recovery "Verify Options" page allows an attacker to manipulate the verification process. By exploiting inconsistent state handling, an attacker with access to a single verification method (e.g., email) can systematically remove other obstacles (e.g., SMS) to achieve a full Account Takeover (ATO).

    ⚠️ Disclaimer: This document details an unpatched, active vulnerability. This information is published for educational, defensive, and responsible disclosure purposes only. Testing this on accounts you do not own is illegal and unethical. The author assumes no liability for misuse.

  #Mechanism of Failure

The vulnerability is a Broken Authentication flaw caused by Improper State and Access Control in the multi-step verification process. The system fails to maintain a secure, immutable context of the verification intent once initiated.

The core failure is that the https://www.paypal.com/webapps/accountrecovery/verify_options endpoint allows users to:

    Re-trigger the sending of codes to any method an arbitrary number of times.

    Change the selected verification method without re-validating the initial security context or user identity.

    Does not properly lock the account or introduce rate-limiting after multiple failed attempts across different methods, creating a window for manipulation.

This creates a race condition where an attacker can "spam" verification methods they don't control to invalidate or remove them from the workflow, forcing the system to rely on the method they possess.

#PoC : https://streamable.com/d759xu

It Sometimes Gives Full Access And Sometimes It Just Fully Lock Down The Account For The User And Make It A Lot Of Hussle To Unlock It Again For The Victim. So For Red Teamer's Its Win Win.

Steps to Reproduce:

    Initiate Lock: Trigger PayPal's security lock on the victim's account via repeated verification step methods and knock off all options u dont have access to then leave the one u have access to as last and use it.

    Intercept: Access the victim's email to find the notification from PayPal. Click the link to begin the "verification" process. You will land on /verify_options.

    Force the Path: After several failures on the SMS method, the system may:

        a.) Temporarily disable the SMS option, leaving only the Email option available.

        b.) Present the Email option as the only viable path forward.

    Exploit: Select the "Email" option. A new code is sent to the compromised inbox. Submit this code to complete verification.

    Account Takeover: Upon successful code submission, you are granted full access to reset the account password, security questions, and linked devices.

Note: Outcome is not guaranteed. Excessive attempts may lead to a full, permanent account lock (Denial-of-Service), as noted in the limitations.


I Already Reported It But They Seem Like They Doesn't Care About It And Didnt Get A Penny Of It. 

Thier Hackerone Response:

Hey [x0dayy],
Thank you for your report!
After review, there doesn’t seem to be any significant security impact as a result of the behavior you are describing.
Account lockout is a feature to prevent active brute forcing of credentials.
As a result, we will be closing this report as informative. If you are able to leverage this into a practical exploitation scenario, we will be happy to reevaluate this report.
This will not have any impact on your Signal or Reputation score. We appreciate your effort and look forward to seeing more reports from you in the future.
Kind regards,
-----------------------------------------------------------------------------------------------

❓ FAQ
<details> <summary><b>Is this vulnerability fixed?</b></summary> <br> <strong>No.</strong> As of the last update to this document, this vulnerability remains unpatched and functional on a portion of PayPal accounts. </details><details> <summary><b>What should I do if my account is vulnerable?</b></summary> <br> Ensure you have a strong, unique password for your email account. Enable Two-Factor Authentication (2FA) on your email and PayPal account (using an Authenticator app, as SMS can be intercepted). </details><details> <summary><b>Why publish an unpatched vulnerability?</b></summary> <br> This is published under the principles of <strong>Responsible Disclosure</strong>. After attempted contact and a reasonable timeframe without a patch, public disclosure pressures the vendor to prioritize a fix and allows the community to defend themselves. The PoC is described theoretically to prevent easy weaponization. </details>
