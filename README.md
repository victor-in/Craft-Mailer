Craft-Mailer
============

Send Emails from the CP to custom recipients, specific users or whole usergroups. 

**Note:** This plugin is currently in public beta.

**Supports:**

- Sending mails in batches.
- Using Twig inside the mails body.
- File-attachments


##Install:

1. Move the `mailer` directory into the `craft/plugins/` directory.
2. Go to `Settings -> Plugins` and enable 'Mailer'.
3. Adjust the settings if you need to, and make sure Crafts Email settings are correct.


##Overview:

![Overview of the plugins CP section](/overview_1.jpg)

![Overview of the plugins CP section](/overview_2.jpg)



##Services (API):

You can make use of the plugins services in your own plugins:


###Example:

```php
//Email
$email = new EmailModel;

$email->sender_name = 'My name';
$email->sender_mail = 'my@mail.com';
$email->subject     = 'Email subject';
$email->htmlBody    = 'The message';


//Create recipients array
$custom_recipients = array(
	array(
		'to' => 'some1@email.com',
		'cc' => 'some2@email.com'
	),
	array(
		'to' => 'some3@email.com',
		'cc' => 'some4@email.com'
	)
);


//Save to model
$recipients = new Mailer_RecipientsModel();
$recipients->recipients = $custom_recipients;


//Create mailer
$this->newMailer($recipients, $email);
```

###newMailer():

Starts a MailerTask based on the passed Mailer_RecipientsModel and EmailModel:

```php
craft()->mailer_main->newMailer($recipients, $email);
```

**Important:** The EmailModel's `to`, `cc`, `bcc`-attributes will be overwritten (and ignored) by the MailerTask.  Still the `to`-attribute should be set to something, otherwise if you call the Models `validation()`, it will fail.

###getUserGroupRecipients():

Creates a recipients-array from UserGroup-Ids

```php
craft()->mailer_main->getUserGroupRecipients($usergroup_ids, $exlude_user_ids=array());
```

If you want to include admins as well add 'admin' to the $usergroup_ids-array.

Optional: You can pass a second parameter with an array of user-ids to exclude.

**Example:**

```php
//Get Recipients
$usergroup_recipients = $this->getUserGroupRecipients( array(1, 5) );

//Save to model
$recipients = new Mailer_RecipientsModel();
$recipients->recipients = $usergroup_recipients;

//Create mailer
$this->newMailer($recipients, $email);
```

This example will send `$email` to all users in the usergroups with the ids: 1, 5

###getUserRecipients():

Creates a recipients-array from User-Ids

```php
craft()->mailer_main->getUserRecipients($user_ids);
```

**Example:**

```php
//Get Recipients
$user_recipients = $this->getUserRecipients( array(1, 5, 20, 6, 7, 10) );

//Save to model
$recipients = new Mailer_RecipientsModel();
$recipients->recipients = $user_recipients;

//Create mailer
$this->newMailer($recipients, $email);
```

This example will send `$email` to all users with the ids: 1, 5, 20, 6, 7, 10

###Mailer_RecipientsModel:

The Mailer_RecipientsModel specifies to whom the Email should be send:

```php
$recipients = new Mailer_RecipientsModel();

$recipients->recipients = array(
	array('to' => 'test1@example.com', 'bcc' => 'test2@example.com', 'cc' => 'test3@example.com'), //Recipients of the 1. mail
	array('to' => 'test4@example.com, test5@example.com'), //Recipients of the 2. mail
	array('to' => 'test6@example.com') //and so on...
);
```

The benefit of using a model is that you can run a validation before starting the MailerTask:

```php
if ( $recipients->validate() ) {
	//Everything is fine
}
```
