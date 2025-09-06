# SQL Injection Demo
**Lab** : SQL injection attack, retrieve administrator password
## Goal
This lab contains a website with a SQL injection vulnerability in the product category filter.  
The task is to:
- Perform a SQL injection attack to retrieve all usernames and passwords.
- Use the information to log in as the administrator user.

---

## Step 1: Setup
1. Open **Burp Suite Community Edition**.
2. Access the lab through your **PortSwigger account**.

This is the website we will be working on:
<img width="950" height="530" alt="image" src="https://github.com/user-attachments/assets/e3f2bc02-38e9-4a19-bf06-96258faba4dc" />

---

## Step 2: Identify the Vulnerable Parameter
The vulnerability is in the **product category filter**.  
Select a product category to load the URL.

For example, after clicking on the **Gifts** category, the URL looks like this:

<img width="953" height="530" alt="image" src="https://github.com/user-attachments/assets/86a1614b-2ff7-4b69-884d-e039282ca87b" />

---

For convenience, we will use **Burp Suite** to explore further.

---

## Step 3: Capture the Request in Burp Suite
1. In Burp Suite, go to the **Proxy** tab.  
2. Click **Intercept off** to turn it **on**.  
3. Reload the website. You will now see the HTTP history of all requests.

<img width="957" height="560" alt="image" src="https://github.com/user-attachments/assets/d33f7a06-490c-47c1-acc1-04b579dc8bab" />

4. Right-click the request with the product category filter → **Send to Repeater**.  
5. Turn Intercept **off** again.  
6. Go to the **Repeater** tab and click **Send** to view the request and response.  

<img width="1910" height="1118" alt="image" src="https://github.com/user-attachments/assets/a9f2e4c2-0937-4faa-b388-8fc848ab53b4" />

5. Turn Intercept **off** again.  

<img width="953" height="558" alt="image" src="https://github.com/user-attachments/assets/f83f9fe6-65c5-4717-8e94-5ce975b80d6a" />

6. Go to the **Repeater** tab and click **Send** to view the request and response.

<img width="954" height="561" alt="image" src="https://github.com/user-attachments/assets/62f60acd-5470-489d-9f31-ee381bc9218f" />

---

- ## Step 4: Test for SQL Injection
Since the product category filter may be vulnerable, inject queries into:
GET /filter?category=Gifts

### Confirming Vulnerability
- Add `'--` at the end of the URL.  
- If the response returns **200 OK**, SQL injection is possible.  
- If an error occurs, direct injection into the URL is not possible.

Result: We received **200 OK**, confirming that the parameter is vulnerable.

<img width="680" height="230" alt="image" src="https://github.com/user-attachments/assets/96d22b25-e7fc-4a11-bf2d-f7e9f03f761e" />

---
## Step 5: Exploitation

### Step 5.1: Determine Number of Columns
Use queries with `NULL` values until the response succeeds.

<img width="709" height="269" alt="image" src="https://github.com/user-attachments/assets/b463e855-8c32-4ce0-9e67-21108bb94e4e" />

---

### Step 5.2: Find Text Columns
Check which columns accept text data by injecting a string.

<img width="707" height="221" alt="image" src="https://github.com/user-attachments/assets/1dfeeada-4bcf-4d4f-94e3-e8b9f142a345" />

Here, the **second column** accepts text data.

---
### Step 5.3: Identify Database and Tables
Use database-specific queries. Reference: [PortSwigger SQLi Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

We discovered that the table `information_schema.tables` exists.  
This indicates the database may be **MySQL, PostgreSQL, or SQL Server**.

<img width="705" height="233" alt="image" src="https://github.com/user-attachments/assets/fc2cb0b7-0ae0-4d09-98d7-4efb998859b8" />

<img width="722" height="256" alt="image" src="https://github.com/user-attachments/assets/45c90552-2488-4a36-880e-1c6fcb144495" />

Display table names directly on the website for clarity:  
(Right-click request → **Request in browser** → Send)

Result:

<img width="955" height="533" alt="image" src="https://github.com/user-attachments/assets/83385f1b-b70a-4f9e-a028-2fa55b1c965a" />

From the table list, the `users` table looks promising.

---
### Step 5.4: Extract Columns from Users Table
Query the `users` table to check available columns.

<img width="780" height="232" alt="image" src="https://github.com/user-attachments/assets/72648ead-b0f3-425d-8241-4de587c60f25" />

We found columns named **username** and **password**.

<img width="950" height="515" alt="image" src="https://github.com/user-attachments/assets/890f3838-2d89-464d-a903-f6caf0cf7f9e" />

---

Now we understood that username and password columns exists, which will probably have administrator password.

### Step 5.5: Extract Data
Use UNION queries to retrieve values:

' UNION SELECT NULL, username FROM users--
' UNION SELECT NULL, password FROM users--

<img width="947" height="521" alt="image" src="https://github.com/user-attachments/assets/705e16e3-6089-4a44-b2e4-e81fd2ab9b04" />

<img width="940" height="512" alt="image" src="https://github.com/user-attachments/assets/ab4b1323-52f1-476e-81a0-01521c68593e" />

If we have to display username and password together,

<img width="958" height="539" alt="image" src="https://github.com/user-attachments/assets/5515c614-c3f8-4369-9933-81841f2928cc" />

!!!!!!!! THUS WE FOUND ADMINISTRATOR PASSWORD AS k16ox8fv115nnl3pm8ce !!!!!!!

Login with the username and password
<img width="901" height="338" alt="image" src="https://github.com/user-attachments/assets/e3b7d393-8dde-4be9-b48d-5da6efafb5aa" />

