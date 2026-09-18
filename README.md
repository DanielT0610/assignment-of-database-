# assignment-of-database-
Topic: Music concert Management System

Index
1.	Introduction
2.	Business rules 
3.	Conceptual model (ERD)	
4.	Logical design 
a.	Queries design
b.	Applications
5.	Implementation details
a.	Software used
b.	Source of data
6.	Difficulties 
a.	Design phase
b.	Implementation phase
c.	Lessons learnt
7.	Work Distribution list 
8.	Conclusion
9.	Reference		
 
1. Introducing – Music concert management system
This database was established mainly to store information about members and customers who purchased concert tickets. This records the member's basic information, concert categories, member points and payment methods. This data can be used to understand the number of people using different payment methods, the number of people attending different concerts, how many points there are... We hope that organizers can store the data in this universal database to reduce the time spent checking data.

2. Business rules 
 
▲	An Event records one or many Purchase Record, each Purchase Record is recorded in only one Event only.
 
▲	Each Purchase Record is recorded by only one Membership, each Membership is written by one or many Purchase Record.
 
▲	A Membership has only one Point records, each Point records records only one Membership.
 
▲	A Purchase Record has only one Payment Method, each Payment Method belongs to one or many Purchase Record.
 
▲	A membership has only one Gender, each gender can link to many memberships. Therefore, the relationship between membership and gender is one to many.  
 
▲	 A membership may log in to one or zero member login system account, each member login system account is logged by only one membership.
3. Conceptual model (ERD)
 
 
4 (a). Logical design – Queries design
Query Name:
Artist search event
SQL Code:
SELECT Event.Artist, Event.Concert_ID, Event.Event_Name, Event.Description, Event.Start_Date, Event.Start_Time, Event.Venue, Event.VIP_Price, Event.STD_Price, Event.TotalSeat, Event.Organizer
FROM Event
WHERE (((Event.Artist)=[ Which Artists you looking for?]));
Explanation:
Users can search for Event Artist through this Query, which will show detailed information about the event.
Result:
When user search “Luna Bright”
<img width="795" height="51" alt="image" src="https://github.com/user-attachments/assets/3beaaf65-e7c1-4541-8dfe-f7782b916e1f" />
 

Query Name:
Check the total seat left by a concert
SQL Code:
SELECT Purchase_Record.Concert_ID, Event.TotalSeat, Count(Purchase_Record.Customer_ID) AS Customer, [TotalSeat]-[Customer] AS SeatLeft
FROM Event INNER JOIN Purchase_Record ON Event.Concert_ID = Purchase_Record.[Concert_ID]
GROUP BY Purchase_Record.Concert_ID, Event.TotalSeat
HAVING (((Purchase_Record.Concert_ID)=[Which event you want to check]));
Explanation:
Ask the user which concert they want to check.
Result:
<img width="778" height="75" alt="image" src="https://github.com/user-attachments/assets/8bffd1f1-d0cd-42e0-8fec-05e496e08130" />


If the user entered “1”  
Query Name:
Customer payment method
SQL Code:
SELECT PM_NAME, COUNT(Purchase_Num) AS ['payment name']
FROM PaymentMethod, Purchase_Record
WHERE PaymentMethod.PM_CODE=Purchase_Record.PM_CODE
GROUP BY PM_NAME
ORDER BY COUNT(Purchase_Num);
Explanation:
A report showing how many people use each payment method.
Result:
<img width="497" height="419" alt="image" src="https://github.com/user-attachments/assets/d917f8ff-d930-4a9e-8920-a27b222d6a07" />
   

Query Name:
Customer points>=200
SQL Code:
SELECT Point.Customer_ID, Customer_LN, Customer_FN, Point.Customer_Point
FROM Membership, Point
WHERE Point.Customer_ID=Membership.Customer_ID
AND Point.Customer_Point>=200
ORDER BY Point.Customer_Point;
Explanation:
A report showing which customers’ customer_point equal to or greater than 200
Result:
<img width="842" height="860" alt="image" src="https://github.com/user-attachments/assets/124844f1-07ad-4bac-973f-1b81a8b8a250" />
 

Query Name:
Find all the concert before the date you want
SQL Code:
SELECT Event.Event_Name, Purchase_Record.Customer_ID, Membership.[Customer_LN], Membership.[Customer_FN], Event.VIP_Price
FROM Event, Purchase_Record, Membership
WHERE Event.Concert_ID=Purchase_Record.Concert_ID AND   Membership.Customer_ID=Purchase_Record.Customer_ID     AND[Select the concert ]=Event.Concert_ID;
Explanation: 
This SQL is use to find all the concert before the date that user entered
Result:
<img width="846" height="974" alt="image" src="https://github.com/user-attachments/assets/138d710b-35c7-40f4-85e1-fec281bbae69" />


Query Name:
Find the point of customer own
SQL Code:
SELECT Point.Customer_ID, Membership.Customer_LN, Membership.Customer_FN, Point.Customer_Point
 FROM Membership, Point 
WHERE (((Point.Customer_ID)=[Membership].[Customer_ID]) AND (([Enter the customerID])=[Point].[Customer_ID])) 
ORDER BY Point.Customer_Point;

Explanation: 
We ask users to enter the customer id and output the customer id last name and first name and their point. Which is going to show the customer can exchange what souvenir.

Result: 
the customer id is 1
<img width="794" height="86" alt="image" src="https://github.com/user-attachments/assets/f85f4145-192f-4da2-9fb3-06471b1cb324" />
 

Query Name:
Finding member all info
SQL Code:
SELECT Purchase_Num, Customer_ID, Event.Event_Name, Ticket_type, SeatNumber, PaymentMethod.PM_NAME, Purchase_Date, Purchase_Price
FROM Purchase_Record, Event, PaymentMethod 
WHERE [Enter the customer ID]=Customer_ID AND Purchase_Record.Concert_ID=Event.Concert_ID AND PaymentMethod.PM_CODE=Purchase_Record.PM_CODE;

Explanation:
Ask the users to enter the customer id and output the related customer purchase record.  We do not ask users to enter the customer's name because customer names may be repeated.

Result:
<img width="841" height="36" alt="image" src="https://github.com/user-attachments/assets/3278b437-b67b-43ad-87f9-8413f5138412" />
 

Query Name:
For customers find their seat
SQL Code:
SELECT Membership.Customer_LN, Membership.Customer_FN, Event.Event_Name, Start_Date, Start_Time, Venue, Ticket_type, Purchase_Record.SeatNumber
FROM Membership, Purchase_Record, Event
WHERE Membership.Customer_ID=Purchase_Record.Customer_ID
AND Event.Concert_ID=Purchase_Record.Concert_ID
AND [what is your seat number?]=Purchase_Record.SeatNumber;
Explanation:
Enter the customer id to find the ticket information purchased by the customer
Result:
If the user entered “23” 
<img width="841" height="42" alt="image" src="https://github.com/user-attachments/assets/bf7f0eca-baac-4401-a71a-c3ca553988ab" />

Query Name:
List of customers prefer payment method
SQL Code:
SELECT PaymentMethod.PM_NAME, Purchase_Record.Customer_ID, Membership.Customer_LN, Membership.Customer_FN 
FROM PaymentMethod, Purchase_Record, Membership
 WHERE ((([Enter payment method that you want to search])=[PM_NAME]) AND ((Membership.Customer_ID)=[Purchase_Record].[Customer_ID]) AND ((PaymentMethod.PM_CODE)=[Purchase_Record].[PM_CODE]));
Explanation: Ask users to enter the payment method. And then output the payment name customer ID and Customer last and first name. Which is helping user to find the specific payment method quickly.
Result: the payment method is Alipay
<img width="841" height="338" alt="image" src="https://github.com/user-attachments/assets/06b3c7dc-67b0-43a2-bcfa-f304789285e5" />
 

Query Name:
Organizer search event
SQL Code:
SELECT Event.Organizer, Event.Concert_ID, Event.Artist, Event.Event_Name, Event.Description, Event.Start_Date, Event.Start_Time, Event.Venue, Event.TotalSeat, Event.VIP_Price, Event.STD_Price
FROM Event
WHERE (((Event.Organizer)=[ Which organized you want?]));
Explanation:
You can search for an Event Organizer through this Query, which will show detailed information about the event.
Result:  Organizer : Warmer Limited
<img width="841" height="30" alt="image" src="https://github.com/user-attachments/assets/dd8bd641-06f9-4321-9ecc-1a9dbdb6049f" />
 

Query Name:
the population of each music genres
SQL Code:
SELECT Event.Music_Genres, Count(Purchase_Record.Customer_ID) AS ['total number of buyer'] FROM Event, Purchase_Record WHERE Event.Concert_ID=Purchase_Record.Concert_ID GROUP BY Event.Music_Genres;
Explanation:
A report showing how many members love which music genres
Result:
<img width="841" height="311" alt="image" src="https://github.com/user-attachments/assets/f6bb9f3c-eac9-4a49-b98a-2ed2965d7ea7" />
 

Query Name:
The population of each music genres
SQL Code:
SELECT Event.Music_Genres, Count(Purchase_Record.Customer_ID) AS ['total number of buyer'] FROM Event, Purchase_Record WHERE Event.Concert_ID=Purchase_Record.Concert_ID GROUP BY Event.Music_Genres;

Explanation:
A report showing how many members love which music genres
Result:
<img width="841" height="311" alt="image" src="https://github.com/user-attachments/assets/a6ab5275-bdff-4af9-af44-d71b91970841" />
<img width="841" height="311" alt="image" src="https://github.com/user-attachments/assets/a63b41ba-932e-4bf1-bd01-595657a8885a" />

 
Query Name:
The customer list of each concert
SQL Code:
SELECT Event.Event_Name, Purchase_Record.Customer_ID, Membership.Customer_LN, Membership.Customer_FN, Purchase_Record.Purchase_Price 
FROM Event, Purchase_Record, Membership 
WHERE Event.Concert_ID=Purchase_Record.Concert_ID AND Membership.Customer_ID=Purchase_Record.Customer_ID AND[Select the concert ]=Event.Concert_ID;
Explanation:
Ask the users to enter the concert ID and output the Event Name, Customer ID, customer last and first name and the concert price. Which is helping user to analyze which concert more people join. And we use the concert id for input, because it is short forms of the event name. It is increasing the analysis of speed.
Result: the concert ID is 1
 <img width="841" height="122" alt="image" src="https://github.com/user-attachments/assets/e31c803c-15a9-4f31-8c72-1cea50723277" />


Query Name:
The total income for each concert
SQL Code:
SELECT Event.Concert_ID, Event.Event_Name, SUM(Purchase_Price) AS ["total income"]
FROM Purchase_Record, Event
WHERE Purchase_Record.Concert_ID=Event.Concert_ID
GROUP BY Event.Event_Name, Event.Concert_ID
ORDER BY SUM(Purchase_Price) DESC;
Explanation:
A report showing the revenue per Concert
Result:
<img width="838" height="347" alt="image" src="https://github.com/user-attachments/assets/6bb8858c-bbda-45c1-a5a2-a6192ca200fe" />



Query Name:
Total buyers for each concert
SQL Code:
SELECT Purchase_Record.Concert_ID, Event.Event_Name, Count(Purchase_Record.Customer_ID) AS ['total number of buyer']
FROM Purchase_Record, Event
WHERE Event.Concert_ID=Purchase_Record.Concert_ID
GROUP BY Purchase_Record.Concert_ID, Event.Event_Name;
Explanation:
A report showing how many buyers in each concert
<img width="844" height="339" alt="image" src="https://github.com/user-attachments/assets/d83d35f6-b77e-48bf-985d-3815a6287f31" />

Query Name:
Upcoming Event
SQL Code:
SELECT Event.Concert_ID, Event.Event_Name, Event.Description, Event.Start_Date, Event.Start_Time, Event.Venue, Event.Artist, Event.[Music Genres], Event.TotalSeat, Event.VIP_Price, Event.STD_Price
FROM Event
WHERE Event.Start_Date > [Please enter the date (MM/DD/YYYY):];
Explanation:
Users can search for a date through this Query, which will show detailed information about upcoming events.
Result:
When users entered 01/07/2025.
 <img width="844" height="146" alt="image" src="https://github.com/user-attachments/assets/e3824216-7d8a-47ea-bc42-de2669dedfe9" />
 
Query Name:
Using Music Genre to find all concert
SQL Code:
SELECT Event.[Music Genres], Event.Artist, Event.Event_Name, Event.Concert_ID, Event.Description, Event.Start_Date, Event.Start_Time, Event.VIP_Price, Event.STD_Price, Event.TotalSeat, Event.Organizer
FROM Event
WHERE (((Event.[Music Genres])=[ Which music genres you are looking for ?]));
Explanation:
Users can search for Music Genre through this Query, which will show detailed information about the event.
Result:
When users entered “POP”
 <img width="795" height="64" alt="image" src="https://github.com/user-attachments/assets/c7deda22-576d-4c93-959e-b5bb26ea8f89" />

Query Name:
VIP/Standard Member of each concert
SQL Code:
SELECT Purchase_Record.Concert_ID, Event.Artist, Count(Purchase_Record.Customer_ID) AS [Total Customer], Sum(IIf([Ticket_type]='VIP',1,0)) AS [VIP member counter], Sum(IIf([Ticket_type]='STD',1,0)) AS [Standard member]
FROM Event INNER JOIN Purchase_Record ON Event.Concert_ID = Purchase_Record.Concert_ID
GROUP BY Purchase_Record.Concert_ID, Event.Artist;
Explanation:
A report showing how many VIP and Standard Member of each concert
Result:
 <img width="841" height="319" alt="image" src="https://github.com/user-attachments/assets/68a0df33-1f39-41c7-8d6c-07f1b7e1c3f2" />
 
4 (b). Logical design – Applications 
Name of form: 
AddingNewadmin (Staff only)
Information:
This page is for employees only and is used to view user information. (username, user ID, password)
<img width="865" height="539" alt="image" src="https://github.com/user-attachments/assets/20d5420b-8ee6-4452-87c2-43f478a38d2d" />

Guide to use:
The first button is saved.
The second button is printed. 
The third button is next record but cannot add new record.
The fourth button is the next record and could add new record.

Name of form:
AdminLoginSystem
Information:
Employees enter their account information to log in to their account.
<img width="696" height="500" alt="image" src="https://github.com/user-attachments/assets/344b197e-9442-4653-9160-5ea2190513c8" />
Guide to use:
It is for administrator log in the account.

Name of form:
Admin_Main_page (Home)
Information:
Users enter the data and submit the form.
The homepage can add new event information and employee accounts.
 <img width="865" height="555" alt="image" src="https://github.com/user-attachments/assets/99479ce2-9103-4206-a3a1-0b30861dfa3d" />

Guide to use:
Move the mouse to event adding or Create Admin account to open the page.

Name of form:
Admin_Main_page (Member View)
Information:
Used by employees to find different data. There are 4 data can obtain on this page. (Customer seats, Upcoming event, Using artist to search event, Using organizer to search event, Using music to find concert)
<img width="839" height="520" alt="image" src="https://github.com/user-attachments/assets/38c0f247-526f-4b7e-8cc5-07a0012a322a" />

Name of form:
Admin_Main_page (Tool)
Information:
Provide employees with the data they need. There are 14 queries on this page.
<img width="839" height="522" alt="image" src="https://github.com/user-attachments/assets/7792efdd-5afd-4572-859e-00e7daabd0d9" />

Name of form:
Admin_Main_page (Report)
Information:
Used for employees to input different information and provide data for reports.
<img width="795" height="563" alt="image" src="https://github.com/user-attachments/assets/6b3cce5e-e371-4602-a21a-1d3caf5315f8" />
Guide to use:
1.	Membership’s age group open the graph of membership’s age group
2.	Total buyers for each concert open the graph of total buyers for each concert
3.	Customer payment method open the graph of customer payment method
4.	The total income for each concert opens the graph of the total income for each concert
5.	Membership’s gender opens the graph of Membership’s gender
6.	Total income of each concert opens the graph of Total income of each concert
7.	Number of VIP/ Standard member of each concert open the graph of Number of VIP/ Standart member of each concert
8.	Log out for user close the form

Name of form:
Event_add
Information:
This page is used to add new event information to the table.
<img width="865" height="588" alt="image" src="https://github.com/user-attachments/assets/1c35feed-05c6-4992-9d21-36ee9ce7a7b2" />
Guide to use:
The first button is saved.
The second button is printed. 
The third button is next record but cannot add new record.
The fourth button is the next record and could add new record.

Name of form:
Main_Page
Information:
A page that shows 2 buttons that can access the direct page.
<img width="865" height="593" alt="image" src="https://github.com/user-attachments/assets/aa7a7c6c-728c-48dc-a09e-223a0dd83693" />
Guide to use:
Click the Member Login or Admin Login Buttons to access the direct page.

Name of form:
Member_Main_Page
Information:
This is a form that provides search functionality for members.
<img width="841" height="531" alt="image" src="https://github.com/user-attachments/assets/e2d368f9-ca1b-4b99-9900-96a7388074ff" />
Guide to use:
There are 5 buttons 
1.	Customers can find their seats.
2.	Customers can search for the upcoming event by the date they enter.
3.	Customers can search for detailed information events by using artist.
4.	Customers can search for detailed information events by using an organizer.
5.	Customers can search for detailed information about concert by using a gr
6.	The “Log out” button on the top right is for customer close log out the system.

Name of form:
MemberLoginSystem (Member View)
Information:
This is the login page provided for customers.
<img width="865" height="610" alt="image" src="https://github.com/user-attachments/assets/644adccc-5033-4535-960c-43d7d46172df" />
Guide to use:
Users enter the data and submit the form.
Login for user log in the account
Cancel for user close the form
Register for user to create account

Name of form:
MemberRegisterForm
Information:
When users don't have an account, they can use this page to create a new account.
<img width="609" height="501" alt="image" src="https://github.com/user-attachments/assets/ff83aa05-9d2c-40b8-8a47-76c5a27b7dba" />
Guide to use:
Users enter the data and submit the form.

4 (c). Logical design – Report
Name of Report:
Customer payment method
<img width="844" height="438" alt="image" src="https://github.com/user-attachments/assets/2b9f4979-7598-41b8-be6a-2347e75750d5" />
Explanation:
Shows the percentage of each payment method.

Name of Report:
Membership's Age Group
<img width="842" height="491" alt="image" src="https://github.com/user-attachments/assets/89d316f6-b28f-4965-aee8-dbc8ada48266" />
Explanation:
Shows the percentage of each age group.

Name of Report:
Membership's Gender
<img width="841" height="583" alt="image" src="https://github.com/user-attachments/assets/b9c2d0af-7b2d-454e-8725-876c57d08a5b" />
Explanation:
1 mean Male
2 mean Female
Shows the percentage of members by gender

Name of Report:
Number of VIP/Standard Member for each Concert.
<img width="835" height="604" alt="image" src="https://github.com/user-attachments/assets/32a84c6c-7b9a-42cf-8166-51f120166baf" />
Explanation:
A graph shows VIP/Standard ticket for each concert.

Name of Report:
The population of each music genres
<img width="865" height="715" alt="image" src="https://github.com/user-attachments/assets/8310f307-b0df-4675-a60d-ce281bd24078" />
Explanation:
Shows the percentage of each music genres.

Name of Report:
The total income for each concert
<img width="832" height="553" alt="image" src="https://github.com/user-attachments/assets/cc0f6db2-8eaf-4405-94eb-315e5cdaaca5" />
Explanation:
Shows the revenue for each concert.

Name of Report:
Total buyers for each concert
<img width="844" height="487" alt="image" src="https://github.com/user-attachments/assets/49e1c7ae-0a15-4f06-ae2a-8f3884d1d616" />
Explanation:
Shows the total buyers for each concert.

5 (a). Implementation details – Software used 
1.	We use Microsoft Access to build Music Concert Management System.
2.	Drawing ERD: https://www.lucidchart.com/ 
3.	W3school to learn how to code SQL https://www.w3schools.com/sql/
5 (b). Implementation details – Source of data
Handmade and generated from AI

6 (a). Difficulties – Design phase 
Query:
During the query design phase, we are not good enough to build a complicated query. Also, Syntax Errors, Typos, missing brackets, or incorrect operators in SQL mode keep happening. Therefore, we found some guide to help us design complicated queries.
Form:
Since this is our first time creating a more complex form, we spend more time on design and layout.
Report:
During the report design it is easy to make errors and difficult to format when designing the report.

6 (b). Difficulties – Implementation phase 
Form:
During the implementation phase, we designed the administrator and member login system, but we encountered problems when defining the login button on the login interface. For example, even if the password is wrong, you can still enter the next Main Page. So, we looked up tutorials to help with the design.

6 (c). Difficulties – Lessons learnt 
We learnt more about SQL, form and report. And understand the importance of avoiding data redundancy and inconsistency. After this project, we understand that creating a database requires more hands-on practice to 		avoid the above issues.

7. Conclusion
This database was created to manage and store the information about members and customers who purchased concert tickets, and the information about the concert events. We created different queries and reports for users to find their wanted data.  Those can help customers and administrators manage and analysis their records more easily. With this database, we can avoid data redundancy and lack of consistency. Also, our database has a centralized user main page and some preset functions for our users. It will improve users' experience.

8.	Reference
W3schools.com. W3Schools Online Web Tutorials. (n.d.). https://www.w3schools.com/sql/default.asp 
Login system reference:
COMPUTER & EXCEL SOLUTION. (2021, November 7). How to Create Login form in Microsoft Access@COMPUTEREXCELSOLUTION [Video]. YouTube. https://www.youtube.com/watch?v=RyQGBcKDBzU
DataTechs Tutorials. (2022, June 13). Creating Classic Login Screen in Microsoft Access with VBA || Securing MS Access Applications [Video]. YouTube. https://www.youtube.com/watch?v=GVVCqIasjG0

10.	Used of AI
ChatGPT 4.0

