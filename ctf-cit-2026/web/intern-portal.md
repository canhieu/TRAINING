# Intern Portal

## ANALYZE

<figure><img src="../../.gitbook/assets/image (767).png" alt=""><figcaption></figcaption></figure>

For the next challenge, we can identify that the target is an internal portal belonging to some company.

First, we interact with the application as a normal user in order to understand its intended functionality and overall workflow.

<figure><img src="../../.gitbook/assets/image (768).png" alt=""><figcaption></figcaption></figure>

At this stage, we observe that the web application exposes two main features worth paying attention to:

* Create reports
* View reports

<figure><img src="../../.gitbook/assets/image (769).png" alt=""><figcaption></figcaption></figure>



Next, I used Burp Suite's crawl functionality to gather more information about the attack surface.

<figure><img src="../../.gitbook/assets/image (774).png" alt=""><figcaption></figcaption></figure>



### hypothesis

Here, we identified an interesting API endpoint:

`GET /report?id=13532`

At this point, we can formulate a hypothesis: what happens if we modify the value of the `id` parameter?

Possible outcomes include:

* IDOR
* SQL injection

### verify

To validate this hypothesis, we can use Burp Suite Intruder to test different values for the `id` parameter.

<figure><img src="../../.gitbook/assets/image (771).png" alt=""><figcaption></figcaption></figure>

The result shows that an attacker is able to access and read reports that do not belong to their own account.

Therefore, this confirms the presence of an IDOR vulnerability.

⇒ Confirm IDOR  <[https://portswigger.net/web-security/access-control/idor](https://portswigger.net/web-security/access-control/idor)>



## EXPLOIT

As a final step, I added one more configuration in Burp Suite Intruder: `Grep - Match` to match a format flag

<figure><img src="../../.gitbook/assets/image (772).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (773).png" alt=""><figcaption></figcaption></figure>

## FLAG

```
CIT{Acc355_C0ntr0l_M@tt3rs!}
```
