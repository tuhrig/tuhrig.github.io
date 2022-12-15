---
layout: post
title: "ORA-28001: the password has expired"
date: "2015-06-23"
categories: 
  - "devops"
tags: 
  - "database"
  - "oracle"
  - "password"
---

Today I came about a very annoying exception. After my development setup was running smoothly for the last six months, my application was getting database errors today. I know that I didn't break something, so the problem had to be somewhere else - and it was: `ORA-28001: the password has expired`

If you install an Oracle database on a Windows system, the default password policy will make all passwords expire after exactly six months! Great. So here's how to fix that:

\-- Show all users, their expiry date and current status
-- This helps you to identify the locked users
SELECT username, expiry\_date, account\_status FROM dba\_users;
 
-- Show the profiles of the users
-- The profile defines the password policy
SELECT username, profile FROM DBA\_USERS;
 
-- Alter the "default" profile and disable the expiry date for it
-- By default, most users will have this profile
ALTER PROFILE "DEFAULT" LIMIT PASSWORD\_VERIFY\_FUNCTION NULL;
 
-- Unlock the users which are already locked
ALTER USER IDENTIFIED BY ACCOUNT UNLOCK; 

**Best regards,** Thomas
