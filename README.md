# Template Injection in Documents
Template injection a technique is used in both the offensive as well as defensive fields, a few examples are: 
1. [Delivering Malicious payloads](http://blog.redxorblue.com/2018/07/executing-macros-from-docx-with-remote.html)
2. Being notified of a potential breach via [Canary Tokens](https://canarytokens.org/generate#)

In short, template injection takes advantage of Microsoft Office's ability to reach out to a file in your local file system or on a domain to download a template to be used in a document.

But if you're on the other end of a template injection as in you're dealing with a potentially rigged document, whether that be to grab your IP or you're a red teamer & are attempting to be 100% sure a document won't notify a company of a potential intrusion, it's useful to be able to detect if the document will phone home.

# Detection & Removal
We will be using Canary Token's auto generated .docx documents from [here](https://canarytokens.org/generate#) which will send an email if it is opened with Microsoft Word.

Here is an example of the email that gets sent when the document is opened:

![](/imgs/img.png)

Since all Microsoft Office documents are all .zip files, we unzip the .docx file & navigate to the newly created directory & into _rels_ (%USERPROFILE%\word\_rels) & we find a **footer2.xml.rels** document:

![](/imgs/img2.png)

Opening this file, we see the domain http://canarytokens.com/traffic/about/tags/<canry-id>/index.html, we can safely assume this is the part of the document that triggers the canary to phone home.

![](/imgs/img3.png)

**NOTE:** This is not the only URL path used for phoning home with Canary generated documents, it seems that the URL path changes. Here is another document's URL path as an example:

![](/imgs/img8.png)

If we examine a regular document generated by Microsoft Word, we see that in the same directory, the **footer.xml.rels** file that we saw in the Canary Token generated document doesn't exist, this indicates that this file has no purposes (besides calling home). So we delete it.

To recreate the .docx file is quite simply:
1. Create a dedicated directory before unzipping the .docx file (this is optional, it simply makes rezipping simpler)
2. Select all the files that were created by unzipping, right-click, 7-Zip, then create archive:

![](/imgs/img4.png)

3. Select "zip" for the **Archive Format** section & leave everything else to the default:

![](/imgs/img5.png)

4. Rename the newly created .zip file with a .docx extension & open it. You'll likely get an error like this, simply click yes:

![](/imgs/img6.png)

Boom! Your document is recreated & is stripped of its ability to phone home:

![](/imgs/img7.png)

It is also possible to generate Microsoft Office documents that aren't supposed to contain macros (EX: .docx files) & force them to contain macros via template injection. More info on this [here](http://blog.redxorblue.com/2018/07/executing-macros-from-docx-with-remote.html). This method has also been used in actual attacks as seen [here](https://www.elastic.co/blog/advanced-techniques-used-in-malaysian-focused-apt-campaign).

I am not too sure if template injection can be used to directly download an .exe file though, I have tried but failed. If anyone has any way to achieve this, I'd love to hear it!
