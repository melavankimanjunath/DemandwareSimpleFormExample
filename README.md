# DemandwareSimpleFormExample
This example shows you how to create Demandware simple form on front-end. Its includes pipeline creation 

This is a quick example on how to create a Demandware Form and how to use form validation:

First you need to create a Demandware Form object. This is done by creating a form definition, which is an xml file
that is placed in the forms directory in the cartridge.
```
<?xml version="1.0"?>
<form>
    <field formid="name" label="Name" type="string" mandatory="true" missing-error="You Name is Mandatory"/>
    <field formid="emailaddress" label="Email Address" type="string" mandatory="true" regexp="^[\w-\.]{1,}\@([\da-zA-Z-]{1,}\.){1,}[\da-zA-Z-]{2,3}$" value-error="Invalid Email Address"/>
    <field formid="message" label="Message" type="string" mandatory="true"/>
    <action formid="send" valid-form="true"/>
</form>
```
In the form definition you define the fields for the form. You provide formid, the form label, and the type. You can also specify if the field is mandatory, error conditions and messages.
Any form validation that you provide in the definition of the form will automatically be performed by the framework. For example all the fields above have the mandatory attribute set to
true. For the name field I have defined the message that will be provided if the form is submitted without a name value.
You can also provide a regular expression, as I did in the emailaddress field that the framework will use to validate the field. If the field does not meet regexp, the form will fail validation. The action formid is the name of the action when the form is submitted. This will be used in the pipeline to determine which path the form will continue to. For validation to be performed, you will need to set the valid-form=true. If not the framework will allow the form to continue with invalid form data. Note that I am using hard coded strings in the form definition file. The proper way to do this would have been defining a Demandware resource property file. This is the same as using a ResourceBundle in Java.
 
Once you have a form defined, you need to create an ISML template to render the form. You also need to create a pipeline so the template to be loaded into the system. For the example above, I created a quick pipeline. This pipeline has a Start Node, an Interaction Continue Node, which will load the template, form and handle the action, and an Interaction Node to render
the response data from the form.

Here is the template to load the form:
```
<isinclude template="util/modules">
<form action="${URLUtils.httpsContinue()}" method="post" id="${pdict.CurrentForms.simpleform.htmlName}">
<table  border="0">
    <tr>
        <isinputfield formfield="${pdict.CurrentForms.simpleform.name}" value="${pdict.CurrentForms.simpleform.name.htmlValue}" type="input">
    </tr>
    <tr>
        <isinputfield formfield="${pdict.CurrentForms.simpleform.emailaddress}" value="${pdict.CurrentForms.simpleform.emailaddress.htmlValue}" type="input">
    </tr>
    <tr>
        <isinputfield formfield="${pdict.CurrentForms.simpleform.message}" type="textarea" attribute1="rows" attribute2="cols" value1="6" value2="50">
    </tr>
</table>
    <input class="image imageright" type="image" src="${URLUtils.staticURL('/images/cont_bttn.gif')}" value="Send" name="${pdict.CurrentForms.simpleform.send.htmlName}" />
</form>
```

The fields are rendered using the isinputfield ISML tag. The isinputfield tag handles all the validation and rendering of any messages that are defined in the forms xml file.
Once the Send button is clicked, form validation is called. If the form passes the validation configured in the forms xml file, then the action will be set to send, and in the case of this pipeline,
another template will be rendered, showing the posted data from the form.
If form validation finds an issue, the action will be set to next. The next action will cause the form to be reloaded with any error messages defined rendered to the user. For example the email address was entered as foo@bar, this will cause the form validation to fail, and the next action will fired.
The Interaction Continue Node handles where the actions are routed to. So anytime you have a form, you will always render the forms template using the Interaction Continue Node pipelet.

This is just a quick overview of how to create a simple form using the Demandware Forms Framework, Demandware Templates and Pipelines.
