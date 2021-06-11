# Custom-Related-List-Zoho-CRM-Deluge-Custom-Function
Guide for creating custom Related Lists in Zoho CRM via Deluge custom function.

## Core Idea
With Related Lists, users are able to view associated records in Zoho CRM. Right out of the box, Zoho CRM provides users Related Lists by default. For example a Contact has associated Deals, Notes, Activites, Attachments. The cool thing is, you have the ability to add your own custom Related List. Zoho has made this programmable via Deluge, and that opens up a world of possibilities. Think about all the information you can have displayed as a Related List in CRM - and that is not limited to Zoho CRM (you can even pull information from other Zoho or third-party apps).

## Tutorial
To assist this tutorial, I'm going to provide an example - I want to create a Related List that shows me other Contacts that are from the same event (I have a custom field called "Event Type" that I'll use to identify these Contacts).

### Add a Related List
* Click on any record in any module in CRM, and click on the "Add a Related List" link located at the bottom of the Related List tab. 
* Select "Functions" from the list of choices, and create a new function.
* Save the empty function first, and give your custom Related List a name.
  * I'm going to call it "Contacts from the same Event".

### How Zoho CRM Interprets Related List
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
Now that you understand how it works, let's begin writing the function.

