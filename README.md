Extracting the Data with SQL
In this Career Track Analysis with SQL and Tableau project, you’re tasked with analyzing the career track enrollments and completions of 365’s students. You’ll first need to retrieve the necessary information from an SQL database. Afterward, you’ll feed this information to Tableau and visualize the results.

Study the sql_and_tableau database, consisting of the following tables:

career_track_info

track_id – the unique identification of a track, which serves as the primary key to the table

track_name – the name of the track

career_track_student_enrollments

student_id – the unique identification of a student

track_id – the unique identification of a track. Together with the previous column, they make up the primary key to the table—i.e., each student can enroll in a specific track only once. But a student can enroll in more than one career track.

date_enrolled – the date the student enrolled in the track. A student can enroll in more than one career track.

date_completed – the date the student completed the track. If the track is not completed, the field is NULL.

Load the database from the sql_and_tableau.sql file. Using the career_track_info and career_track_student_enrollments tables in the database, select a dataset containing the following columns:

student_track_id – this serves as an identification for each student-track pair. That is, each row of the resulting table should be uniquely labeled. Do this in an ordinal manner.
Hint: Research how the ROW_NUMBER function with the OVER clause work.

student_id – the unique identification of a student
track_name – the name of the track

date_enrolled – the date the student enrolled in the track

track_completed

0 – the track is not completed (the completion date is NULL)

1 – the track is completed (the completion date is not NULL)

days_for_completion – the difference in days between the completion date and the enrollment date

Hint: Research how the DATEDIFF function works.

completion_bucket – the bucket a student falls into based on the number of days it took them to complete a track (if they have done so). The buckets are as follows:

Same day – the days_for_completion field is equal to 0

1 to 7 days – the days_for_completion field is between 1 and 7 days, inclusive

8 to 30 days – the days_for_completion field is between 8 and 30 days, inclusive

31 to 60 days – the days_for_completion field is between 31 and 60 days, inclusive

61 to 90 days – the days_for_completion field is between 61 and 90 days, inclusive

91 to 365 days – the days_for_completion field is between 91 and 365 days, inclusive

366+ days – the days_for_completion field is more extensive than 365 days

Export the obtained result as a CSV file called career_track_completions.csv.

To complete the task, follow these steps.

Subquery: Begin with a subquery that selects columns from joined tables. You want to join two tables (career_track_student_enrollments and career_track_info) based on a common column, track_id.

SELECT 

    ...
    
FROM

    career_track_student_enrollments e
    
        JOIN
        
    career_track_info i ON ...;
    
In the field list of your subquery, you want to select several columns: student_id, track_name, date_enrolled, and date_completed. You should also create three columns that can’t be found in the database: student_track_id, track_completed, and days_for_completion.

SELECT 
    ... AS student_track_id,
    
    # Selecting the four columns,
    
    ... AS track_completed,
    
    ... AS days_for_completion
    
FROM

    career_track_student_enrollments e
    
        JOIN
        
    career_track_info i ON ...;
    
To create the student_track_id column, use the ROW_NUMBER function with the OVER clause. This assigns unique row numbers to each row in the result set. The row numbers are ordered by student_id and track_name in descending order. The function should look like the following:

    ROW_NUMBER() OVER (...) AS student_track_id,
    
To create the track_complete column, check whether date_completed is NULL. In SQL, you can use the IF function to do this.

    IF(...) AS track_completed
    
To calculate the days_for_completion column, you can use the DATEDIFF function in SQL, which calculates the difference between two dates.

    DATEDIFF(...) AS days_for_completion
    
This entire subquery will act as a new dataset in your main query. So, enclose it in parentheses and give it an alias, say a.

(

...

        JOIN
        
    career_track_info i ON ...) a;
    
Main Query: Select the columns you want using a SELECT statement in the main query. You will also create a new column, completion_bucket.

SELECT 

    # Select all columns from the subquery,
    
    ... AS completion_bucket
    
FROM

( # The subquery ) a;


To create the completion_bucket column, use the SQL CASE statement. The CASE statement allows you to perform conditional logic in SQL; it behaves similarly to if-else conditions in programming. Each WHEN clause within the CASE statement will check if days_for_completion falls within a specific range, and the THEN clause will assign the appropriate bucket label. After writing all the WHEN-THEN pairs, finish the CASE statement with the END keyword, and the alias completion_bucket.

SELECT 

    # Select all columns from the subquery,
    
    CASE
    
        WHEN days_for_completion = 0 THEN 'Same day'
        
        ...
        
    END AS completion_bucket
    
FROM

( # The subquery ) a;

