+++ 
date = 2015-10-20T16:51:08-05:00
title = "Multiple email addresses for your ServiceNow instance"
description = ""
slug = "simulating-multiple-email-addresses-in-your-servicenow-instance" 
tags = ["ServiceNow"]
categories = []
externalLink = ""
series = []
+++

Ever wish you had multiple inboxes available to your ServiceNow instance? If you find yourself needing to build out an inbound email integration, and if you find that it's often challenging uniquely identifying which messages are meant for what, this might help you out. 

## [Sub-addressing](https://en.wikipedia.org/wiki/Email_address#Sub-addressing) to the rescue. 

Sub-addressing allows us to add **extra** information to the destination email address without changing where that email will be delivered. For example, say my ServiceNow instance email address is joshnow@service-now.com. Using sub-addressing, I can add information after the instance name by adding a +, followed by that extra information. 

**Example**: joshnow+pri1@service-now.com. 

Using this example, everything after the + is ignored for delivery purposes. The message is still delivered to joshnow@service-now.com. The powerful thing is that the extra value is preserved in the email received by the instance. 

## How can we use this to our advantage?

Say we want all emails from "System A" to use one inbound email action. Perhaps System A is a monitoring tool and should be processed in a particular way. If every email comes **from** one address, we can just set up a condition that looks for that address. But what if these emails will come from two addresses? Or 100 addresses? The condition becomes unmanageably complex. 

In this scenario, we need some way to consistently "tag" emails so we can easily identify them using a condition in our desired Inbound Action. 

You might ask: why not just add a value to the subject, or body of the email and check for that value using a condition? This may work in some scenarios, but we may not always have control over the contents of the email or the subject line. 

## We **do** control the 'to' address

Continuing to use "System A" as our example, we send all emails to an address such as "joshnow+SystemA@service-now.com". In our inbound action, we now have the ability to inspect the "to" address of the received email. We can then use this in a condition such as: `email.to.indexOf("+SystemA") >= 0`. 

## One 'Gotcha'

If we are tightly controlling which action fires based on a sub-address, it's highly likely we do **not** want other, "catch-all" inbound actions to also fire. 

To fix this, add `event.state="stop_processing";` at the end of the Inbound Action script, and make sure the action will run **before** other, less tightly conditioned actions. This bit of code tells the email engine to stop processing when this action is finished. 

## In conclusion

Sub-addressing effectively allows us to use an unlimited number of unique addresses when sending emails to our instances, and can greatly simplify conditional processing on those emails. I'll leave you with a few additional scenarios where this may come in handy. 

 * Use +pri1, +pri2, etc. to control the priority of incidents opened via email
 * Use +inc, +chg, +prb, etc. to control the **type** of record created
 * Use +user_id (+jnerius), etc. to tie emails from a generic address to a specific source user