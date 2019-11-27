+++ 
date = 2015-03-03T16:17:47-05:00
title = "Server-side \"hasRoleExactly\" in ServiceNow"
description = "ServiceNow provides the convenient `gs.hasRole(role)` method for checking to see if the current user has the specified role(s). This works great if you're only worried about securing something against non-admins, but it will **always** return *true* if the current user is an admin."
slug = "serverside-hasroleexactly-in-servicenow" 
tags = ["ServiceNow"]
categories = []
externalLink = ""
series = []
+++

ServiceNow provides the convenient `gs.hasRole(role)` method for checking to see if the current user has the specified role(s). This works great if you're only worried about securing something against non-admins, but it will **always** return *true* if the current user is an admin. 

There are times when this just won't work. A common use case is securing a method in a script include that might take an action only if the user has the necessary role. If our code does something like: return sensitive data about a user from a web service, we might want to prevent admins from automatically getting this access. 

There is a **client-side** method called `g_user.hasRoleExactly(role)`, and this does pretty much what it sounds like: it only returns true if the user actually has the role in question. But this won't help us in our server-side code. 

This can be solved by creating our own "hasRoleExactly" method. 

    function hasRoleExactly(role) {
        var au = new ArrayUtil();
        var roles = gs.getSession().getRoles() + ''; 
        var roleArray = roles.split(","); 
        var isAuthorized = au.contains(roleArray, role); 
        return isAuthorized; 
    }

This currently won't work if you pass in an array of roles, but it could be easily adapted to support this.

Also note that this doesn't prevent an admin from just impersonating another user that has the necessary role, and an admin will be able to find other ways around this as well (just comment out the hasRoleExactly check!). However, a check like this ensures a **deliberate** action must be taken by an admin to circumvent the security check. 