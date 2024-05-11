# Cyber Security Base Project 1

## Polls app

Link to the GitHub repository: https://github.com/sonjaolkkonen/cyber-security-base

With this polls app users can add polls and vote on polls. There's two types of users, admins and regular users and currently only admins can create polls. Moreover, only registered users should be able to vote on polls. 
The app contains five security flaws from the [OWASP Top Ten list](https://owasp.org/www-project-top-ten/). 

## Installation
1. clone repository to your device:
   ```git clone git@github.com:sonjaolkkonen/cyber-security-base.git```
2.  make necessary migrations: ```python3 manage.py migrate```
3.  start the server: ```python3 manage.py runserver```

Go to http://127.0.0.1:8000/polls or http://127.0.0.1:8000/admin in your browser to access the application. 

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

## FLAW 4: [CSRF Cross-Site Request Forgery](https://cybersecuritybase.mooc.fi/module-2.3/1-security)
Flaw in source code:
- ```views.py``` lines [46](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L46), [65](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L65), [77](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L77) and [94](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L94).

This application doesn't have any CSRF protection. Cross-site request forgery (CSRF) is a type of malicious exploit where unauthorized commands are transmitted from a user that the web application trusts. It typically involves the attacker tricking the victim into executing actions on a web application without their consent. This attack is particularly dangerous because it exploits the trust that a website has in a user's browser. To mitigate CSRF attacks, web applications often use techniques like CSRF tokens, which are unique, unpredictable values included in each request and validated on the server side to ensure that the request originated from the legitimate user.

**How to fix:** As many frameworks (including Django) these days include CSRF defences by default, in order to produce the flaw, the source code includes ```@csrf_exempt```decorators. The flaw can be fixed by deleting the decorators from lines [46](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L46), [65](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L65), [77](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L77) and [94](https://github.com/sonjaolkkonen/cyber-security-base/blob/c2adb1a56138d835b35884fdeca56dd30d1b463c/mysite/polls/views.py#L94). After the decorators are deleted, the following lines must also be uncommented:

- ```detail.html``` line [8](https://github.com/sonjaolkkonen/cyber-security-base/blob/ac469be3a39a2710d9d6b5ac42e0e16f6c1bd459/mysite/polls/templates/polls/detail.html#L8)
- ```layout.html``` line [12](https://github.com/sonjaolkkonen/cyber-security-base/blob/ac469be3a39a2710d9d6b5ac42e0e16f6c1bd459/mysite/polls/templates/polls/layout.html#L12)
- ```login.html``` line [11](https://github.com/sonjaolkkonen/cyber-security-base/blob/ac469be3a39a2710d9d6b5ac42e0e16f6c1bd459/mysite/polls/templates/polls/login.html#L11)
- ```register.html``` line [8](https://github.com/sonjaolkkonen/cyber-security-base/blob/ac469be3a39a2710d9d6b5ac42e0e16f6c1bd459/mysite/polls/templates/polls/register.html#L8)

## FLAW 5: [Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
Flaw in source code [here](https://github.com/sonjaolkkonen/cyber-security-base/blob/7e1f57c342b8ce780c172923e2916ad6c1683696/mysite/mysite/settings.py#L26).

This application has a security configuration error in the server settings. Variable DEBUG is set to True even though it should always be set to False when in production. If there is some unhandled error in the source code and an error occurs, view with detailed error message is returned to the user. This can give the attacker more information how to breach the application. 

**How to produce:** When not logged in, navigate to http://127.0.0.1:8000/polls/logout. The server will return a page with detailed information about the application since the DEBUG variable is set to True and user is not logged in as well as the GET request is not handled in the [logout function](https://github.com/sonjaolkkonen/cyber-security-base/blob/1c883c27c32f732239f27dffcb8a439f9a1a2163/mysite/polls/views.py#L95). 

**How to fix:** The flaw can be fixed by performing following actions:
- Variable [DEBUG](https://github.com/sonjaolkkonen/cyber-security-base/blob/1c883c27c32f732239f27dffcb8a439f9a1a2163/mysite/mysite/settings.py#L26) needs to be set to False
- When running the application locally, "127.0.0.1" needs to be added to list [ALLOWED_HOSTS](https://github.com/sonjaolkkonen/cyber-security-base/blob/1c883c27c32f732239f27dffcb8a439f9a1a2163/mysite/mysite/settings.py#L28)
- Uncomment [this](https://github.com/sonjaolkkonen/cyber-security-base/blob/1c883c27c32f732239f27dffcb8a439f9a1a2163/mysite/polls/views.py#L100) line so that if anything other than POST request is done, the user is redirected to the index page.
