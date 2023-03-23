# EPVME-Dataset
The five zip packages are all the malicious samples, totaling 37,283 EML files.

#Dataset Description
Here are reference links of other open-source datasets we used in our work.

Enron Email Dataset "http://www.cs.cmu.edu/~./enron/"

Nazario phishingcorpus "https://monkey.org/~jose/phishing/"

Spam Assassin open-source corpus "https://spamassassin.apache.org/old/publiccorpus/"

TREC 2007 "https://plg.uwaterloo.ca/~gvcormac/treccorpus07/"

Wooyun Email XSS Dataset "https://github.com/WhiteRabbitc/Wooyun-Email-XSS-Dataset/tree/master/malious-sample"

Email Dataset Sample Amount:
| Source                         | Malicious | Benign  | Total   |
|--------------------------------|-----------|---------|---------|
| Enron email corpus             | 0         | 517,401 | 517,401 | 
| Nazario phishing corpus        | 9,510     | 0       | 9,510   | 
| Trec07                         | 50,199    | 25,220  | 75,419  | 
| Spam Assassin                  | 2,399     | 6,952   | 9,351   | 
| Wooyun XSS Dataset             | 168       | 0       | 168     |  
| EPVME Dataset                  | 37,283    | 0       | 37,283  |   
| Total                          | 99,559    | 549,573 | 649,132 |  


#How we make the malicious email samples
We select and summarize six new types of malicious email attack scenarios that exploit vulnerabilities in email security authentication mechanisms (SPF and DMARC) and email UI rendering, and construct the EPVME dataset. It contains 37,283 malicious emails, and all email samples have email header and body information.

In the malicious samples we created, except for some specific fields where we inject the payload, other content, including *From*, *To*, *Subject*, and body text, is randomly selected from the existing samples. This is because we believe that the detection model should be able to identify malicious emails that use these attacks without being influenced by this irrelevant content. This allows the machine learning model to focus on static features related to the payload and thus achieve the identification of these new types of malicious emails. 

##Inconsistency between Mail From and From header attacks
For the Inconsistency between Mail From and From header attacks, the value of the *Mail From* field is randomly selected from the sender email addresses of malicious email samples, and the value of the *From* field is generated from the sender email addresses of all benign emails. For the Empty Mail From attack, we set the *Mail From* field to "<>". The value of the *From* field is generated from the sender email addresses of all benign emails, and the body is randomly selected from malicious samples.

<img width="326" alt="1679556723667" src="https://user-images.githubusercontent.com/32115816/227133907-d0a440ad-92fb-43b4-9729-483db18e6f29.png">

##Multiple From header attacks
We choose email addresses from both benign and malicious samples and set them as two values of *From*. Based on these four scenarios, we create malicious email samples equally.

<img width="319" alt="1679556835716" src="https://user-images.githubusercontent.com/32115816/227134285-de74317e-2f21-4b4d-bfba-9391f3ebae42.png">

##From Header Injection
We use unicode characters, utf-8 encoding, base64 encoding, and other injection methods to construct the *From* field, and the body part is selected from the malicious sample to construct an email sample with a legitimate recipient but malicious body content.

<img width="257" alt="1679556882412" src="https://user-images.githubusercontent.com/32115816/227134428-0a455d02-a28e-4976-a2ac-13d31e734b68.png">

##Subdomain Attacks
As with subdomain attacks, we use subdomains of legitimate sender domains (test.domain.com or security.domain.com) to construct the header information along with the body content of the malicious emails in the previously collected dataset to build the complete malicious email samples.

##XSS
We divide the payloads into two categories. One category is malicious code constructed from a single HTML statement. Since the former works in both the header and body of the email, we insert each specific statement equally into the *From*, *To*, *Subject*, and *Body* fields of the email body. For the three header fields, we add the payload above the sample "Subject: XSS test mail", "From: <test@attack.com>", "To: <victim@victim.com>". The other fields and content are filled in randomly from existing benign email samples or by using test samples. The other type is longer, the payload is more complex, and more techniques are used, such as encoding, which is only applicable to be placed in the body part of the email. We randomly select the content from the good emails to fill the headers of the emails and insert the payload in the body, and some emails will also add some body content from the benign emails after the payload.

#New Static Features Proposed
Based on the way we create the new samples, we propose several new features for mailicious email detection.

| Name            | Description                                      | Domain |
|-----------------|--------------------------------------------------|--------|
| To_HasHTML      |  If *to* field contains HTML tags.                 |  {0,1} |
| From_HasHTML    |  If *from* field contains HTML tags.               |  {0,1} |
| Subject_HasHTML |  If *subject* field contains HTML tags.            |  {0,1} |
| From_Subdomain  |  If *from* field contains subdomain email address. |  {0,1} |
