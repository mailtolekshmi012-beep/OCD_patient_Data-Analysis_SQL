Tools Used
-----------
• SQL Server Management Studio (SSMS)
• SQL

Skills Demonstrated
-------------------
• Data Cleaning
• Exploratory Data Analysis (EDA)
• Aggregations
• Common Table Expressions (CTEs)
• Trend Analysis
• Healthcare Data Analysis

--Retrive Howl data

     SELECT *
     FROM ocd_patient


-- Data Cleaning 

     SELECT *
     FROM ocd_patient
     WHERE Patient_ID IS NULL
     OR Age IS NULL
     OR Gender IS NULL
     OR Ethnicity IS NULL
     OR Marital_Status IS NULL
     OR Education_Level IS NULL 
     OR  OCD_Diagnosis_Date IS NULL
    OR Duration_of_Symptoms_months IS NULL
    OR Previous_Diagnoses IS NULL
    OR Family_History_of_OCD  IS NULL
    OR  Obsession_Type  IS NULL 
    OR Compulsion_Type IS NULL
    OR Y_BOCS_Score_Obsessions IS NULL 
    OR Y_BOCS_Score_Compulsions IS NULL
    OR Depression_Diagnosis IS NULL
    OR Anxiety_Diagnosis IS NULL
    OR Medications IS NULL;

---Data Exploration

1. Count of patients by gender and their average OBS Score

        SELECT  COUNT (patient_ID) AS Count_patients ,
        Gender,
        ROUND ( AVG( Y_BOCS_Score_Obsessions),2 ) AS Average_OBS_Score
        FROM ocd_patient
        GROUP BY Gender;



2. FROM 1 above , calculate the patient_Count _percentages...


       WITH  Count_Patient_gender AS 
       (

       SELECT  COUNT (patient_ID)  AS Count_patients ,
        Gender,
        ROUND ( AVG( Y_BOCS_Score_Obsessions),2 ) AS Average_OBS_Score
        
         FROM ocd_patient
        GROUP BY Gender
        )

        ,SUM_Patient AS 

            (
        SELECT SUM (Count_patients) AS Total_Count
        FROM Count_Patient_gender

          )
       SELECT 
        g.Count_patients,
        g.Average_OBS_Score ,g.Gender,
       
         (Count_patients* 100/ Total_Count )  AS patient_Count_percentages

        FROM Count_Patient_gender g
        CROSS JOIN SUM_Patient t  ;


3. Count of patient visited by month ...


       SELECT
       DATEFROMPARTS(
           YEAR (OCD_Diagnosis_Date),
           MONTH (OCD_Diagnosis_Date),
           1
        )
       AS Monthly_Diagnosis,
       COUNT (Patient_ID) AS Patient_TotalCount
       FROM ocd_patient

       GROUP BY 
       DATEFROMPARTS(
           YEAR (OCD_Diagnosis_Date),
           MONTH (OCD_Diagnosis_Date),
           1
        )

         ORDER BY Patient_TotalCount DESC;


---Another way by usinng CTE

        WITH  Total_Month_Diagonosis AS 
        (
      SELECT  
      Patient_ID, 

     DATEFROMPARTS(
            YEAR(OCD_Diagnosis_Date),
            MONTH(OCD_Diagnosis_Date),
            1
         )  
     AS monthly_Diagnosis
     FROM ocd_patient
     )

     SELECT  monthly_Diagnosis, 
    COUNT(Patient_ID) AS Patient_CountID
    FROM Total_Month_Diagonosis
    GROUP BY monthly_Diagnosis
    ORDER BY monthly_Diagnosis;


4. The most common Obsession Type and its average Obsession  score 

        SELECT 
       TOP 2 Obsession_Type,
       COUNT(Patient_ID) AS Count_patient_ID,
       ROUND (AVG(Y_BOCS_Score_Obsessions),2 ) AS AVG_Obsession_Type 
      
       FROM ocd_patient
       GROUP BY Obsession_Type
       ORDER BY  AVG_Obsession_Type DESC ;


5. The most common Compulsion_type and its average score 

       SELECT 
       TOP 2 Obsession_Type,
       COUNT(Patient_ID) AS Count_patient_ID,
       ROUND (AVG(Y_BOCS_Score_Compulsions),2 ) AS AVG_Compulsion_Type 
      
        FROM ocd_patient
       GROUP BY Obsession_Type
       ORDER BY  AVG_Compulsion_Type  DESC ;
