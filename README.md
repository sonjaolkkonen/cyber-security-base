# Cyber Security Base Project 1

## FLAW 1: [Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)
Flaw in source code [here](https://github.com/sonjaolkkonen/cyber-security-base/blob/141041ed67fce1d228f8e08e0fe2ed44dc53c264/mysite/mysite/settings.py#L88).

This application has authentication weakness since when creating the user (regular or admin user), the application permits using weak or well-known passwords such as "Password1" or "admin/admin". 

**Hot to produce:** Create a superuser "admin" with a password "admin" by running: 

```
python3 manage.py superuser
>> Username (leave blank to use 'abc': admin
>> Email address:
>> Password:
>> Password (again)
>> Superuser created successfully.
```

**How to fix:** 
The flaw can be fixed by uncommenting lines 88-101 from [here](https://github.com/sonjaolkkonen/cyber-security-base/blob/141041ed67fce1d228f8e08e0fe2ed44dc53c264/mysite/mysite/settings.py#L88). The app will then validate the passwords (e.g. validates that the password is not too common or too similar to the given username). 

## FLAW 2: [Security Logging and Monitoring Failures](https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/)
Flaw in source code:
- ```settings.py``` lines [126-154](https://github.com/sonjaolkkonen/cyber-security-base/blob/e1b3bb3dd5b6b14a4f02544e6957d037c7b3f713/mysite/mysite/settings.py#L126)
- ```views.py``` lines [13](https://github.com/sonjaolkkonen/cyber-security-base/blob/e1b3bb3dd5b6b14a4f02544e6957d037c7b3f713/mysite/polls/views.py#L13), [80](https://github.com/sonjaolkkonen/cyber-security-base/blob/e1b3bb3dd5b6b14a4f02544e6957d037c7b3f713/mysite/polls/views.py#L80) and [92](https://github.com/sonjaolkkonen/cyber-security-base/blob/e1b3bb3dd5b6b14a4f02544e6957d037c7b3f713/mysite/polls/views.py#L92)

This application has no logging features. However, without logging and monitoring, breaches cannot be detected. 

**How to fix:**
The flaw can be fixed by uncommenting the above mentioned lines. Once this is done, a logger is configured in ```mysite/settings.py``` file and it is used to log every login and logout. Logs are saved in ```mysite/logs/debug.bug``` directory. 

## FLAW 3: [Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
Flaw in source code [here](https://github.com/sonjaolkkonen/cyber-security-base/blob/cd6cdb1b7aa6dca76744ab189531c93f92b9977e/mysite/polls/views.py#L44).

This application has a flaw in access control. Voting of the polls should be enabled only for registered users which are logged in but now due to the flaw in access control, also non registered users can vote polls.

**How to produce:** Navigate to http://127.0.0.1:8000/admin/ and login as admin user. Create a new poll. Once the new poll is created, logout. Voting is done by clicking the poll in question. Once the new poll is created, logout and you'll see that the polls are no longer clickable and thus, logged out users can't vote for the polls. The clickable links are visible only for logged in users, which is checked in template ```index.html``` in [this line](https://github.com/sonjaolkkonen/cyber-security-base/blob/cd6cdb1b7aa6dca76744ab189531c93f92b9977e/mysite/polls/templates/polls/index.html#L10). 

However, this check is not enough as it's not validated in the backend whether the user is logged in or not. User can navigate to the voting page of the poll even when not logged in by typing for example http://127.0.0.1:8000/polls/1, where "1" is indicating which poll is in question. 

**How to fix:** The flaw can be fixed by validating whether the user is logged in or not also in the backend. This can be done by uncommenting [this line](https://github.com/sonjaolkkonen/cyber-security-base/blob/cd6cdb1b7aa6dca76744ab189531c93f92b9977e/mysite/polls/views.py#L44).
