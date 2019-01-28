# log_analysis
Project 1 for the FSND course 
++++++++++++++++++++++++++++
Project 1 - Log_Analysis::
++++++++++++++++++++++++++++
ABOUT: 
This project uses three data base tables called Articles, Authors and Log to fetch some useful data.

==============
INSTALLATION:
==============

This project makes use of the Linux-based virtual machine (VM) and vagrant to manage the virtual machine.

To bring the virtual machine online run the following command : 
>> vagrant up

To log into the virtual machine run the following command:
>> vagrant ssh 

To load the data, cd into the vagrant directory and use the command
>> psql -d news -f newsdata.sql.

=================================
VIEWS CREATED IN THIS PROJECT ::
=================================

>>QUERY 1:

Created a view called log_sub :
create view log_sub as select substring(path,10) as path, count(*) from log where status = '200 OK' and path like '%/article%' group by path order by count desc limit 3;

Final Query:
select articles.title,log_sub.count from articles, log_sub where log_sub.path = articles.slug;

>>QUERY 2: 
Final Query:
select authors.name from authors,log_sub,articles where articles.slug = log_sub.path and articles.author = authors.id;

>>QUERY 3: 
Created a view called all_status:
create view all_status as select date(time),count(*) from log group by date(time) order by date(time);

Created a view called status_404:
create view status_404 as select date(time),count(*) from log where status like '%404%' group by date(time) order by date(time);

Created a view called error_perc:
create view error_perc as 
select all_status.date, round(status_404.count*100.00/all_status.count,2) as Perc from all_status,status_404 where all_status.date=status_404.date order by perc desc;

Final Query: 
Date where perc is more than 1 per cent :
select date,perc from error_perc where perc > 1.0;
