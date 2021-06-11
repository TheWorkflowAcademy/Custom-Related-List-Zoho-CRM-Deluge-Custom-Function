# Custom-Related-List-Zoho-CRM-Deluge-Custom-Function
Guide for creating custom Related Lists in Zoho CRM via Deluge custom function.

## Core Idea
With Related Lists, users are able to view associated records in Zoho CRM. Right out of the box, Zoho CRM provides users Related Lists by default. For example a Contact has associated Deals, Notes, Activites, Attachments. The cool thing is, you have the ability to add your own custom Related List. Zoho has made this programmable via Deluge, and that opens up a world of possibilities. Think about all the information you can have displayed as a Related List in CRM - and that is not limited to Zoho CRM (you can even pull information from other Zoho or third-party apps).

## Tutorial
To assist this tutorial, here's an example - I want to create a Related List that shows me other Contacts that are from the same event (I have a custom field called "Event Type" that I'll use to identify these Contacts).

### Add a Related List
* Click on any record in any module in CRM, and click on the "Add a Related List" link located at the bottom of the Related List tab. 
* Select "Functions" from the list of choices, and create a new function.
* Save the empty function first, and give your custom Related List a name.
  * I'm going to call it "Contacts from Same Event".

### How Zoho CRM Interprets Related List in Your Function
Zoho CRM requires the function to return a specific XML format for it to be readable as Related List.

This is the format for constructing Related List:

```xml
<record>
     <row no="0">
          <FL val="Name1">value1</FL>
          <FL val="Name2">value2</FL>
     </row>
     <row no="1">
          <FL val="Name1">value1</FL>
          <FL val="Name2">value2</FL>
      </row>
</record>
```

Error/Exception messages can be displayed in the custom related list by using the format below:

```xml
<error>
      <message>Your error message goes here !!!</message>
</error>
```

If you don't really understand, paste the Deluge script below into your function, and see what it does to your related list on your CRM record.
```javascript
resp = '<record><row no="0"><FL val="Name1">value1</FL><FL val="Name2">value2</FL></row><row no="1"><FL val="Name1">value1</FL><FL val="Name2">value2</FL></row></record>';
return resp;
```

### Write Your Function
Now that you understand how it works, let's begin writing the function (I will be using my example to demonstrate how this is done).
* You can access your function by either clicking Edit on your new Related List, or via Actions (under CRM settings) -> Functions.

#### Get the Event Type
```javascript
contact = zoho.crm.getRecordById("Contacts",contactid);
type = contact.get("Event_Type");
```

#### Search for other Contacts with the same Event Type
```javascript
relatedContacts = zoho.crm.searchRecords("Contacts","(Event_Type:equals:" + type + ")",1,10);
```
*Note: Set you can set the limit for the number of records here if you don't want your CRM to be flooded with too many line items. Here, I set max 10.*

#### Construct Related List with Full Name and Phone Number
* Set a condition to return the related list with the contact details if the search finds another Contact.
  * We validate with `.size() > 1` because if Event Type is not null, the function will return 1 (the Contact from which the function is being executed).
 * Then, set the counters to count the number of rows and build the XML header.
 * Iterate through the *relatedContacts* variable, filter out the record from which the function is being executed, and construct the XML string with the Full Name and the Phone Number of the related Contact(s).
* Add the XML footer to the string that you have built.
* Set an else condition to return a message if none was found in the error XML format.
* Return *responseXML* at the end of your function.

```javascript
if(relatedContacts.size() > 1)
{
	// Counter
	n = 0;
	responseXML = "<record>";
	for each  c in relatedContacts
	{
		if(c.get("id") != contactid)
		{
			contactRecord = zoho.crm.getRecordById("Contacts",c.get("id"));
			contactName = contactRecord.get("Full_Name");
			contactPhone = contactRecord.get("Phone");
			responseXML = responseXML + "<row no='" + n + "'><FL val='Customer Name'>" + contactName + "</FL><FL val='Phone'>" + contactPhone + "</FL></row>";
			n = n + 1;
		}
	}
	responseXML = responseXML + "</record>";
}
else
{
	// Return error message if no Contact is found
	responseXML = "<error>=><message>No other contact has this event type.</message></error>";
}
return responseXML;
```
