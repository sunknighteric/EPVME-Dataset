# EPVME-Dataset
The five zip packages are all the malicious samples, totaling 49,136 EML files. This dataset is for research purpose.

## Dataset Description
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
| EPVME Dataset                  | 49,136    | 0       | 49,136  |   
| Total                          | 111,412   | 549,573 | 660,985 |  


## How we make the malicious email samples
We select and summarize six new types of malicious email attack scenarios that exploit vulnerabilities in email security authentication mechanisms (SPF and DMARC) and email UI rendering, and construct the EPVME dataset. It contains 37,283 malicious emails, and all email samples have email header and body information.

In the malicious samples we created, except for some specific fields where we inject the payload, other content, including *From*, *To*, *Subject*, and body text, is randomly selected from the existing samples. This is because we believe that the detection model should be able to identify malicious emails that use these attacks without being influenced by this irrelevant content. This allows the machine learning model to focus on static features related to the payload and thus achieve the identification of these new types of malicious emails. 

### Inconsistency between Mail From and From header attacks
For the Inconsistency between Mail From and From header attacks, the value of the *Mail From* field is randomly selected from the sender email addresses of malicious email samples, and the value of the *From* field is generated from the sender email addresses of all benign emails. For the Empty Mail From attack, we set the *Mail From* field to "<>". The value of the *From* field is generated from the sender email addresses of all benign emails, and the body is randomly selected from malicious samples.

<img width="326" alt="1679556723667" src="https://user-images.githubusercontent.com/32115816/227133907-d0a440ad-92fb-43b4-9729-483db18e6f29.png">

### Multiple From header attacks
We choose email addresses from both benign and malicious samples and set them as two values of *From*. Based on these four scenarios, we create malicious email samples equally.

<img width="319" alt="1679556835716" src="https://user-images.githubusercontent.com/32115816/227134285-de74317e-2f21-4b4d-bfba-9391f3ebae42.png">

### From Header Injection
We use unicode characters, utf-8 encoding, base64 encoding, and other injection methods to construct the *From* field, and the body part is selected from the malicious sample to construct an email sample with a legitimate recipient but malicious body content.

<img width="257" alt="1679556882412" src="https://user-images.githubusercontent.com/32115816/227134428-0a455d02-a28e-4976-a2ac-13d31e734b68.png">

### Subdomain Attacks
As with subdomain attacks, we use subdomains of legitimate sender domains (test.domain.com or security.domain.com) to construct the header information along with the body content of the malicious emails in the previously collected dataset to build the complete malicious email samples.

### XSS
We divide the payloads into two categories. One category is malicious code constructed from a single HTML statement. Since the former works in both the header and body of the email, we insert each specific statement equally into the *From*, *To*, *Subject*, and *Body* fields of the email body. For the three header fields, we add the payload above the sample "Subject: XSS test mail", "From: <test@attack.com>", "To: <victim@victim.com>". The other fields and content are filled in randomly from existing benign email samples or by using test samples. The other type is longer, the payload is more complex, and more techniques are used, such as encoding, which is only applicable to be placed in the body part of the email. We randomly select the content from the good emails to fill the headers of the emails and insert the payload in the body, and some emails will also add some body content from the benign emails after the payload.

### CMS Attacks

Cryptographic Message Syntax (CMS) is a common standard for signing and encrypting email that is used by S/MIME. The CMS and S/MIME standards define two types of signed messages: opaque signatures and split signatures. The main difference between the two types is that the former encrypts the text content to be signed along with the eContent, while the latter separates the content to be signed from the signed encrypted message when the *Content-Type* is set to *multipart/signed* and there is no eContent. If an attacker obtains a signed email, the attacker can construct arbitrary content in the body by removing the original content and adding an illegitimate eContent field to disguise the authentication of the email recipient. Due to a design flaw in S/MIME, an email may have multiple signatures or no signatures at all, so an attacker can use this vulnerability to construct spoofed emails. The CMS attack type of malicious email sample construction mainly modifies the signature encryption part of legitimately signed emails by adding more signer information, or simply removing all signature information, etc., which will eventually lead to abnormal base64 character lengths of signatures in these malicious email samples that use digital signatures.

### MIME Attacks

If an attacker already has at least one email and a corresponding valid signature from the impersonated entity based on MIME syntax rules, the attacker can manipulate the content of the email to deceive the recipient in a number of ways, including (1) Adding fake content and a large number of blank lines before the signed email so that the recipient notices only the fake content in the email and knows that the e-mail has been signed. (2) Using HTML/CSS tags to hide the legitimate body information of the signed email. (3) Using *cid.references* to link the unsigned malicious text to the signature section. (4) Marking the signed section as an attachment so that it can be hidden.

Next two figures are two typical examples of MIME attacks. The former uses a large number of line breaks to make the recipient see only the malicious text and thus ignore the benign signed email content. The latter uses HTML/CSS to hide the signed part so that the recipient cannot see it in the email client.

<img width="334" alt="1680070415967" src="https://user-images.githubusercontent.com/32115816/228442531-0626bce4-4873-4ef3-820a-d8b4c70283ce.png">

<img width="368" alt="1680070431972" src="https://user-images.githubusercontent.com/32115816/228442586-d3e3b9a7-818d-4992-a82a-8612373a4415.png">

### ID Attacks

This type of attack mainly exploits the difference between the information in the signature and the identity of the sender. However, many email receiving clients allow users to check the details of the signature, which may show signs of tampering, thus reducing the success rate of this type of attack. The main forms of this type of attack include: (1) The receiving server may not be able to verify that the sender and signer are the same, so the attacker can sign his own message and impersonate a legitimate recipient. (2) Based on (1), the sender field is constructed through a possible vulnerability in the UI display on the receiving end, so that the recipient can see the legitimate sender, but the actual sender is the attacker. (3) The SMTP protocol has vulnerabilities allowing multiple sender fields and possible inconsistencies in the *Mail From* and *From* fields. When the recipient of the mail checks the signature, it may traverse the sender, and after the attacker signs with his own information, the recipient's UI may display the legitimate sender. 

To summarize these three types of attacks, the core lies in the vulnerability of the SMTP protocol, which allows multiple *From* fields. By modifying the header information in the form of Multiple From Header, it is possible to make the sender information seen by the recipient and the person who actually signs the email inconsistent.
Next figure is an example of From Header Confusion. There are two *From* fields in the email, and some email clients display the sender information in the first *From* field. Because this signature uses the attacker's information, the email is marked as having a legitimate digital signature.

<img width="397" alt="1680070532386" src="https://user-images.githubusercontent.com/32115816/228442881-c43c4da1-17e3-48e4-8a64-281247ce080b.png">

## Appendix

Here are the full list of the static features that we extract from all email samples to realize the validation of EPVME dataset.

### Header Features

| Feature Name                         | Description | Domain |
|--------------------------------|-----------|-----------|
| Subject$\underline{ }$IsReply             | If the email is a reply to a previous email from the sender.           |{0,1} |
|Subject<u><u>文本</u></u>IsForwarded|If the email is forwarded from another account to the recipient.|{0,1} |
|Subject<u> </u>NumWords |Number of words in the subject line of the email. |N|
|Subject<u> </u>NumCharacters|Number of characters in the email's subject line.|N|
|Subject<u> </u>Richness|Richness of the subject line.|[0,1]|

