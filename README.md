# SQL Injection Demo
Lab : SQL injection UNION attack, retrieving multiple values in a single column (Portswigger)
Goal:
- This lab contains a website with a SQL injection vulnerability in the product category filter. Perform a SQL injection attack that retrieves all usernames and passwords, and use the information to log in as the administrator user.

Open Burpsuite community edition
Access the lab through Portswigger account

This is the website that we are going to work-
<img width="950" height="530" alt="image" src="https://github.com/user-attachments/assets/e3f2bc02-38e9-4a19-bf06-96258faba4dc" />

As vunerability is in the product category filter, we have to select a product category and load the url.

Clicked on Gifts category and now we can the url with product category filter:
<img width="953" height="530" alt="image" src="https://github.com/user-attachments/assets/86a1614b-2ff7-4b69-884d-e039282ca87b" />

For easiness, we can use burp suite for exploring further.

In Burpsuite, Click on Proxy tab --> Click Intercept off button to turn it ON --> Load the website again
We can HTTP history of all requests in HTTP history tab

<img width="957" height="560" alt="image" src="https://github.com/user-attachments/assets/d33f7a06-490c-47c1-acc1-04b579dc8bab" />

Right click on the request with product category filter and select Send to Repeater.
<img width="1910" height="1118" alt="image" src="https://github.com/user-attachments/assets/a9f2e4c2-0937-4faa-b388-8fc848ab53b4" />

Click Intercept ON button to turn it OFF

Go to repeater tab --> Click on Send button
<img width="953" height="558" alt="image" src="https://github.com/user-attachments/assets/f83f9fe6-65c5-4717-8e94-5ce975b80d6a" />

Now we can see the request and corresponding response.
<img width="954" height="561" alt="image" src="https://github.com/user-attachments/assets/62f60acd-5470-489d-9f31-ee381bc9218f" />

As product category filter has vulnerability we have to inject SQL queries to 'GET /filter?category=Gifts' in the request.
BUT, How can we make sure whether the product category filter has vulnerability?
For that try '-- at the end of the url
  If we get 200 OK response, that means sql injection is further possible.
  If we get some error, that means we cannot inject directly in the url.

We got 200 OK response, which means url is vulnerable to SQL injection.
<img width="680" height="230" alt="image" src="https://github.com/user-attachments/assets/96d22b25-e7fc-4a11-bf2d-f7e9f03f761e" />


If we want to find the administrator password, we have to check the tables that exist in the database, columns exist for a specific table, and the values inside that particular columns.

Step 1:
Determine the number of columns retrieved by the query
<img width="709" height="269" alt="image" src="https://github.com/user-attachments/assets/b463e855-8c32-4ce0-9e67-21108bb94e4e" />

Step 2:
Determine which columns contain text data so that easily inject queries through that column
<img width="707" height="221" alt="image" src="https://github.com/user-attachments/assets/1dfeeada-4bcf-4d4f-94e3-e8b9f142a345" />
<img width="705" height="233" alt="image" src="https://github.com/user-attachments/assets/fc2cb0b7-0ae0-4d09-98d7-4efb998859b8" />

So the above 200 OK response shows that second column that returned by the query contains text data.

Step 3:
Try to find tables exist in the database. We have to use different queries for different databases. (Refer https://portswigger.net/web-security/sql-injection/cheat-sheet)
So we have to try different queries to understand which database is using by the website. 
<img width="722" height="256" alt="image" src="https://github.com/user-attachments/assets/45c90552-2488-4a36-880e-1c6fcb144495" />

Here we can see the table information_schema.tables exists, so that it can be Microsoft, PostgreSQL or MYSQL.
For display the table names in the website for better visibility, try the query in the url in browser or from burpsuite, open the browser (Risht click --> Request in browser option)

Below are the table names:
<img width="955" height="533" alt="image" src="https://github.com/user-attachments/assets/83385f1b-b70a-4f9e-a028-2fa55b1c965a" />

Check the table names and found tables that can contains usernames and passwords. From the list I found some table named users, privileges etc..Lets try the table users first.

Check the columns in the users table and find which column have text data same as we did initially. Now, check the column names of the table

<img width="780" height="232" alt="image" src="https://github.com/user-attachments/assets/72648ead-b0f3-425d-8241-4de587c60f25" />

<img width="950" height="515" alt="image" src="https://github.com/user-attachments/assets/890f3838-2d89-464d-a903-f6caf0cf7f9e" />














  
