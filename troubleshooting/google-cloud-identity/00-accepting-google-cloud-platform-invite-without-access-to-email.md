# Accepting Google Cloud Platform invite without access to email

I have recently created a Google Cloud Identity account, due to wanting an organisation within Google Cloud Platform (GCP). As there is two ways to acquire an organisation, either through Google Cloud Identity or Google Workspace. I opt-ed for Cloud Identity as there is a free version available.

After following the [official instructions provided](https://docs.cloud.google.com/identity/docs/how-to/set-up-cloud-identity-admin#sign-up-free) by Google to acquire a Cloud Identity admin account. I re-used one of my domains' `intuitive-judgments.com` purchased from Cloudflare.

## Existing Google Cloud Platform project

As with everyone else, I only had a personal Gmail account and did not look into any other options available. Because I wanted to have a uniform project within GCP and could not select an organisation without completing the perquisite mentioned above.

With an existing GCP project, which I wanted to migrate across to an organisation, I needed to login to **both** my personal and cloud identity admin account within GCP. As per the following steps:

1. Go to **Google Cloud Console**
2. Switch resource selector to your **Project**
3. Open **IAM & Admin → IAM**
4. Click **Grant Access**
5. Add your Gmail account (e.g. `k@intuitive-judgments.com`)
6. Assign a role (for example): Owner

After inviting my new account and switching profiles within GCP, I was unable to view the invite sent. When looking with my projects console with my other account, I could see the following 'alert' against the invite sent.

> Invitation sent. Pending acceptance.

What I did not know, was although I had a Cloud Identity, it did not come with Gmail and was only provided with Workspace. I did not want to pay for additional Google services, considering I was on the 'free' version of Cloud Identity.

## Accepting invite via a URL

As I was unable to access the email associated with the account, I had to find an alternative route to accept the invite via the GCP console. Luckily for myself, with a quick search online, I was able to find a [Stackoverflow with a solution](https://stackoverflow.com/a/63542757/25098588) that worked for myself.

URL:

```bash
https://console.cloud.google.com/invitation?project=[your-project-id]&account=[the-account-email-invited]&memberEmail=[the-account-email-invited]
```

Replace the values `[your-project-id]`and `[the-account-email-invited]`,  mentioned in the post with my information.

E.g

```bash
https://console.cloud.google.com/invitation?project=playground-438414&account=k@intuitive-judgments.com&memberEmail=k@intuitive-judgments.com
```

Accessing the updated URL, allowed me to accept the invite via the UI.

---
#google #gcp #cloud-identity
