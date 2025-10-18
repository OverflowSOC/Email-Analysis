# Email analysis

## Objective


Apply your theoretical understanding to examine an email's details without the need to open it in an email application, and conduct Open Source Intelligence (OSINT) to extract additional data.

### Skills Learned


-Conducted detailed email header analysis to identify phishing indicators and malicious activity.

-Interpreted SPF, DKIM, and DMARC results to assess email authenticity.

-Identified mismatches between From, Reply-To, and Return-Path fields to detect suspicious emails.

-Applied OSINT techniques to verify domains and sender information.

-Performed scoping and investigation planning to determine potential impact across an organization.

-Developed incident response procedures, including identifying recipients, reviewing SIEM logs, and recommending containment actions.

-Strengthened ability to analyze email content for phishing/social engineering tactics.

### Tools Used


-Email clients and header viewers – to inspect raw headers and email metadata. (notepad++)

-nslookup.io / DNS query tools – to validate domain SPF and mail server records.

-OSINT resources – to check domain reputation and historical spam reports.



## Steps

There are a few key fields we want to pay attention to. The first field is the “Received” field. In the beginning, we see a total of 7 “Received” fields.

<img width="519" height="488" alt="first email" src="https://github.com/user-attachments/assets/f4073d77-75a3-40d9-9931-f63b2c95925d" />

Starting from the top, the first “Received” field is the last mail server this email went to before it was delivered TO the recipient. Whereas the last “Received” field in the 7th position is the first mail server that received the email FROM the sender.

I began my analysis by identifying the mail server closest to the sender. In this scenario, the mail server closest to the sender was mail[.]yobow[.]cn.

<img width="516" height="127" alt="Capture d'écran 2025-08-28 162346" src="https://github.com/user-attachments/assets/88474fd0-74cb-42fc-953c-4772dd02ee78" />

The second field I noted was the “Authentication-Results” field. This contained the SPF, DKIM, and DMARC results, which I reviewed to determine whether the email passed any security checks. Checking this field was useful because any failures could quickly point to potential misuse.

<img width="516" height="68" alt="cc" src="https://github.com/user-attachments/assets/e8e75cb4-6e73-4549-9093-b4129016bfde" />

The third field I examined was the “Reply-To” field, which determines the address that appears if I press reply in the email. In this case, the field pointed to a Gmail account. Normally, the “Reply-To” and “From” addresses should match, so I flagged the mismatch as a potential indicator of suspicious activity.

<img width="517" height="23" alt="Capture d'écran 2025-08-28 162703" src="https://github.com/user-attachments/assets/67daab74-0e1b-4d6a-8add-9697f5b2a64c" />

The fourth field I reviewed was the “From” field. In this case, the email appeared to come from p[.]chambers with the display name Mrs. Janet Yellen. I noted a clear mismatch between the name, the email address, and the “Reply-To” field, which further indicated suspicious activity.

<img width="514" height="22" alt="ccs" src="https://github.com/user-attachments/assets/37da132f-3a6c-4984-9848-3d27ec8936fa" />

The fifth field I analyzed was the “Subject” field. In this scenario, the subject line was crafted to create a sense of urgency and importance, likely to entice the recipient into clicking on the email.

<img width="516" height="25" alt="subjet" src="https://github.com/user-attachments/assets/282c6f1b-32be-4123-8146-dc5d1c1fd0b6" />

The next field I reviewed was the “Date” field. While I kept in mind that this value could be spoofed, it still served as a useful reference point. In a real SOC environment, it would be especially valuable if both transaction logs and header logs were being ingested into the SIEM for correlation.

<img width="512" height="26" alt="date" src="https://github.com/user-attachments/assets/f0b5d7f5-95b6-4f86-8754-4b70a221a6f7" />

Next, I examined the “Content-Type” field. This field tells the mail server how to render the email’s content. Occasionally, a boundary value is included, but in this email, no boundary was provided. "https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types"
 to deepen my understanding of how different MIME types affect email rendering.
 
<img width="518" height="90" alt="Capture d'écran 2025-08-28 163303" src="https://github.com/user-attachments/assets/00cb2966-76ae-473c-bdfe-a236405be299" />

<img width="515" height="156" alt="Capture d'écran 2025-08-28 163331" src="https://github.com/user-attachments/assets/754dda8e-e600-43b8-ba1b-1f1029c92038" />

I also looked at fields prefixed with an X, known as X-Headers. These are optional fields added by applications or mail servers and are not present in every email. In this email, I noted the X-Mailer field, which had the value Microsoft Outlook Express, providing insight into the software used to send the message.

<img width="514" height="47" alt="Capture d'écran 2025-08-28 163445" src="https://github.com/user-attachments/assets/6e2a532a-7712-474d-9399-beb0f5615763" />

Next, I reviewed the “Message-ID” field. Although this field typically contains a unique identifier, I kept in mind that spoofing can occur. I noted this ID because it can be useful when tracking emails during an investigation, even if it isn’t immediately used in the analysis.

<img width="515" height="18" alt="Capture d'écran 2025-08-28 163510" src="https://github.com/user-attachments/assets/50045a6d-4216-4354-881f-4fe593a4faa4" />

Next, I examined the “To” field. In this scenario, it was set to Undisclosed recipients. While I couldn’t see the specific recipients, I made a note of this field since tracking recipient information can be important in email investigations.

<img width="475" height="46" alt="Capture d'écran 2025-08-28 163551" src="https://github.com/user-attachments/assets/95b8986c-369a-4527-80b3-6ee45af62dce" />

Another field I reviewed was the “Return-Path” field. This field primarily exists for troubleshooting, indicating where error messages should be sent if an email is rejected. I noted the value in this scenario, which was p.chambers. I kept in mind that the email listed here can differ from the sender’s address and is not necessarily an indicator of malicious activity.

<img width="517" height="40" alt="Capture d'écran 2025-08-28 163633" src="https://github.com/user-attachments/assets/61c81117-d760-4ee7-a6f6-4d4c2ca75f00" />

Finally, I analyzed the email body itself. In this example, the body was in HTML format (as indicated by the “Content-Type” field). The content claimed that 16 million USD was available to the recipient. Recognizing that offers that seem too good to be true usually are, I noted that the recipient was instructed to contact two “diplomatic agents”: Cynthia R. James at agentcynthiajamescontact01@gmail[.]com and John Williams at dr.philipmaxwell303@gmail[.]com. This information helped me identify potential phishing indicators.

<img width="510" height="367" alt="Capture d'écran 2025-08-28 163715" src="https://github.com/user-attachments/assets/79bf693e-134a-4be5-b1c0-e3e4859ae2eb" />

To recap, while analyzing this email, I focused on several key fields that are important for identifying suspicious activity:

Received – to determine the mail server closest to the sender

Authentication-Results – to check SPF, DKIM, and DMARC results

Reply-To – to verify if it matches the “From” address

From – to identify the sender and check for mismatches

Subject – to assess whether the content is designed to entice clicks

Date – to track when the email was sent (keeping in mind possible spoofing)

Content-Type – to understand how the email is rendered

Message-ID – to track the email during investigations

To – to see the recipients, if listed

Return-Path – for troubleshooting and error message routing

Body – to analyze the actual content for phishing or malicious indicators

Analysis
Perform OSINT on the closest mail server to the sender: mail[.]yobow[.]cn.

DomainTools: created back in 2014-07-18 and located in Beijing, China.

<img width="483" height="199" alt="Capture d'écran 2025-08-28 163813" src="https://github.com/user-attachments/assets/dd2d635c-06ef-4379-98d8-d934adc8028a" />

VirusTotal: looking at the details section, we see additional users reporting this domain sending what appears to be similar emails.

<img width="497" height="129" alt="Capture d'écran 2025-08-28 163854" src="https://github.com/user-attachments/assets/f4bf8769-935b-4a70-a5c5-dbdaacded78c" />

Check the sender domain: sasktel[.]net using both Whois from Domaintools and VirusTotal

DomainTools: created back in 2000-04-05 and located in Toronto, Ontario

<img width="527" height="449" alt="Capture d'écran 2025-08-28 163930" src="https://github.com/user-attachments/assets/d766de5b-14e1-4162-a4ae-5f6138e2c9ca" />

VirusTotal: no reports of malicious activity by vendors.

<img width="525" height="123" alt="Capture d'écran 2025-08-28 164005" src="https://github.com/user-attachments/assets/f06b6856-2d4e-4e17-9d70-c57188226a7d" />

Searching Google shows that this domain is owned by an internet service provider.

<img width="516" height="95" alt="Capture d'écran 2025-08-28 164024" src="https://github.com/user-attachments/assets/b45fbeb3-33df-4ba6-85f1-645f0759f03c" />

Look at the IP address: 183.56.179.169 on AbuseIPDB, it has been reported multiple times for Email Spam, which is a good indicator that this email is likely a spam email.

<img width="524" height="422" alt="Capture d'écran 2025-08-28 164054" src="https://github.com/user-attachments/assets/023114f1-912d-4751-935c-1b5c60080a4e" />

Check the IP using IPVoid. There are 3 services that have  reported this IP as of writing.

<img width="523" height="509" alt="Capture d'écran 2025-08-28 164118" src="https://github.com/user-attachments/assets/2c77e7bb-b3ed-4ab1-9ebc-968fc988b085" />

Next, I focused on the “Authentication-Results” field and observed that the SPF result was softfail. I noted that SPF can return four possible results:

    Pass – the IP is authorized to send mail, and the email is allowed through.

    Fail – the IP is not authorized, and the email is rejected.

    SoftFail (~) – the IP is not authorized, the email is allowed through but may be marked as spam.
  
    Neutral (?) – the IP neither passes nor fails, and the email is allowed through.
This analysis helped me understand that although the SPF softfail allowed the email through, it raised a potential red flag for further investigation.

<img width="517" height="68" alt="Capture d'écran 2025-08-28 164237" src="https://github.com/user-attachments/assets/6414ae15-58b7-42a8-b302-ed1c85fbfcc3" />

Since the Sender Policy Framework (SPF) was configured as softfail, the email was allowed through, although it was likely flagged as spam. I then focused on the “smtp.mailfrom” domain and observed that it pointed to sasktel[.]net, which matches the sender’s address, p.chambers@sasktel[.]net. To verify the legitimate mail servers for Sasktel, I used nslookup.io and queried the DNS records for sasktel.net. This helped me determine whether the sending server was authorized or potentially spoofed.

<img width="496" height="172" alt="Capture d'écran 2025-08-28 164337" src="https://github.com/user-attachments/assets/823278fa-1403-4401-9976-a7e02a9e3318" />

I then examined the TXT SPF records for sasktel.net, which specify which mail servers are authorized to send emails on behalf of the domain. I noticed that the sender’s IP address, 183.56.179.169, was not listed in the SPF record. As a result, the receiving mail server marked the email as softfail, confirming a potential mismatch between the sending server and the domain’s authorized mail servers.

<img width="528" height="239" alt="Capture d'écran 2025-08-28 164343" src="https://github.com/user-attachments/assets/51e532a4-c1c9-4903-a698-c803f70b2d93" />

Both DKIM and DMARC were set to none, meaning they were likely not setup.

<img width="517" height="62" alt="Capture d'écran 2025-08-28 164422" src="https://github.com/user-attachments/assets/3041450a-050d-4aed-b2ce-7d576118f5ae" />

# Lab Answers
1)   What is the status of SPF, what does it mean, and why did it occur?
Answer: Status of SPF: softfail, an IP that is not authorized to send mail will softfail and be marked as SPAM. This occurred because 183.56.179.169 (sender IP) is not authorized to send mail on behalf of the sasktel[.]net domain.

<img width="518" height="132" alt="Capture d'écran 2025-08-28 165548" src="https://github.com/user-attachments/assets/22331000-ebce-4fb1-9f68-4f77f9e6b3b0" />

2)   What is the sender’s IP address?
Answer: 183.56.179.169
<img width="517" height="66" alt="Capture d'écran 2025-08-28 165608" src="https://github.com/user-attachments/assets/57e9fcc4-ae53-4b06-accd-aeccf0458353" />

3)   Are DKIM and DMARC setup?
Answer: No, both DKIM & DMARC are set to none.

<img width="517" height="71" alt="Capture d'écran 2025-08-28 165631" src="https://github.com/user-attachments/assets/1cdd416e-bd8b-4b27-8599-a03afae0491e" />

4)   What is the domain name of the mail server closest to the sender? (include the subdomain)
Answer: mail[.]yobow[.]cn
<img width="519" height="40" alt="Capture d'écran 2025-08-28 165651" src="https://github.com/user-attachments/assets/8895d36d-9a29-4fbd-b763-7f2304ed3b5c" />

5)   What is the reported date of this email? (include date/time/time zone)
Answer: Wed, 6 Dec 2023 05:00:12 -0800

<img width="516" height="31" alt="Capture d'écran 2025-08-28 165712" src="https://github.com/user-attachments/assets/2f122492-d4bb-4091-ae94-24033437328e" />

6)   What is the email address of the sender and when was the domain created?
Answer: p.chambers@sasktel[.]net; created on 2000-04-05

<img width="517" height="148" alt="Capture d'écran 2025-08-28 165733" src="https://github.com/user-attachments/assets/e4c2c74f-43df-4d4a-8256-556c71dcc366" />

7)   If the recipient were to reply to this email, who would it be sent to?

Answer: agentcynthiajamescontact01@gmail[.]com

<img width="514" height="31" alt="Capture d'écran 2025-08-28 165750" src="https://github.com/user-attachments/assets/49ad44f1-7c1f-4530-95cc-a7a37d7cae0e" />

8)   What is the subject line of this email?
Answer: Attention Dear Beneficiary

<img width="516" height="25" alt="subjet" src="https://github.com/user-attachments/assets/51e93c59-6475-4751-be4e-74be0f57ff80" />

9)   If the email failed to deliver, who would receive the error message?
Answer: p.chambers@sasktel[.]net

<img width="516" height="39" alt="Capture d'écran 2025-08-28 165856" src="https://github.com/user-attachments/assets/f7476106-2fad-4d49-b7f9-5057e7a9f97b" />

11) How many “Content-Type” are there in this email and which format(s) are the mail server going to render this message as? (Example: base64? Html? Plain text?)
Answer: 1 content type, which is text/html, so the mail server would render this as html.

<img width="509" height="73" alt="Capture d'écran 2025-08-28 165915" src="https://github.com/user-attachments/assets/f8affd3e-0dca-4d72-9ec7-e58df3ddf18b" />

11) What is the “Message ID” for this email?
Answer: 20231206125957.6414E20EB5FD@mail.yobow[.]cn

<img width="513" height="23" alt="Capture d'écran 2025-08-28 165939" src="https://github.com/user-attachments/assets/6714e683-c34f-43a0-8656-aef976c38ab0" />

12) The recipient of this email is instructed to email two individuals, what are their names and email addresses?
Answer: First: Cynthia R. James - agentcynthiajamescontact01@gmail[.]com | Second: John Williams - dr.philipmaxwell303@gmail[.]com.

<img width="512" height="188" alt="Capture d'écran 2025-08-28 170012" src="https://github.com/user-attachments/assets/7ee0ffd7-8168-4eae-9a7f-9dcab3b78605" />

13) When was the root domain of the mail server created and which country is it located in?
Answer: Created 2014-07-18 and located in Beijing

<img width="483" height="199" alt="Capture d'écran 2025-08-28 170032" src="https://github.com/user-attachments/assets/f4a12ded-9d30-47e2-b93c-bd3d2d02222a" />

15) Has the root domain been involved in any other phishing related activity?
Answer: Yes, according to VirusTotal under the DETAILS tab. (As of writing, there are related results from 419scam[.]org)

<img width="491" height="129" alt="Capture d'écran 2025-08-28 170058" src="https://github.com/user-attachments/assets/6071e7d7-656f-4882-858c-b97daaec5139" />

15) Has the sender IP been reported for spam previously?
Answer: Yes, based on AbuseIPDB under Comments, others are reporting this IP for SPAM.

<img width="528" height="197" alt="Capture d'écran 2025-08-28 170201" src="https://github.com/user-attachments/assets/654edb48-b08b-42d4-be49-a5a9be46464b" />

Bonus question: Is the sender IP part of a VPN service?
Answer: No, based on ipinfo.io, this IP is not part of a VPN service.

<img width="517" height="312" alt="Capture d'écran 2025-08-28 170258" src="https://github.com/user-attachments/assets/39ff49ef-c35d-4496-b22a-666eb5ddf3ed" />


During my analysis, I observed several indicators that this email was suspicious:

The domain yobow[.]cn is known to send similar emails, and the sender IP has been reported for spam previously.

SPF was set to softfail because the sender IP was not authorized.

The “Reply-To” address differed from the “From” address, which is a red flag.

The “From” email’s display name and address did not match.

The email body attempted to entice the recipient into replying to receive 16 million USD, strongly suggesting a scam attempt.

# Extra Notes / Next Steps

I determined that the email is spam/not legitimate and should be deleted. I planned to perform a search across the organization to identify other recipients and check if anyone responded. If email transaction logs are ingested into the SIEM, I would review them; otherwise, I would search the email gateway for the subject “Attention Dear Beneficiary” from December.

Next, I would perform the following searches:

Emails to/from p.chambers@sasktel[.]net (the “From” address).

Emails to/from agentcynthiajamescontact01@gmail[.]com (the “Reply-To” address).

Emails to/from dr.philipmaxwell303@gmail[.]com (listed in the body).

Emails to/from the domain sasktel[.]net (note: this could generate many events; caution is advised for large domains).

I would then take responsive actions such as deleting the email and requesting anyone who replied to cease communications immediately.

# Lab Takeaways

This lab reinforced the importance of reviewing key fields when analyzing suspicious emails, as they can guide investigations and highlight indicators of compromise. I learned to use OSINT to gather additional context that may support my findings.

When planning next steps, I focused on scoping: considering the bigger picture, not just a single artifact. For example, I asked myself: Who else received this email? Did anyone reply? This approach helps ensure a thorough and actionable investigation.

# Key Fields to Review in Email Analysis

    Received
  
    Authentication-Results

    Reply-To

    From

    Subject

    Date

    Content-Type

    Message-ID

    To

    Return-Path

    Body
