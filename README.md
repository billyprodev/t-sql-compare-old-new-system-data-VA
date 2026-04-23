# t-sql-compare-old-new-system-data-VA

PRE SOP Steps and observations
1.	I installed Microsoft SQL Server 2025 SQLExpress by downloading the latest version from the Microsoft website https://go.microsoft.com/fwlink/p/?linkid=2216019&clcid=0x409&culture=en-us&country=us
2.	I installed SSMS 22 by downloading the latest version from the Microsoft website https://learn.microsoft.com/en-us/ssms/install/install
3.	I generated a database called mssqldb and did all my work in there.
4.	I followed the exercise’s instructions to download the old and newsystem data
5.	I read the documentation and looked through the files then created the proper tables structure, that’s also when it became apparent that I will be able to go the ETL route for the Beneficiaries tables (with formatted columns) and the ELT route (with nvarchar columns) for the Claims tables.
a.	CAR 1A NEWSYSTEM has this data row that seems invalid because amt columns are supposed to be 0 not null.   


Standard Operating Procedure
(I numbered the files accordingly in the repo to match each numbered point below.)
1.	 I uploaded the data onto the database using auto generated flat file importer through the SSMS UI for the small Beneficiaries files I did not have any issues but switched to the auto generated SSIS file importer (I did not have full SSIS software installed on my machine but I would have used that instead and written clean import packages) because the larger Claims files were causing a timeout issue using the SSMS flat file importer UI. 
2.	 I then queried the Information Schema to get table names and column names so I can feed it into my query builder Excel file. In the excel file I was able to generate the new columns name (_OLD, _NEW and _M) and prep calculations for the number of records matches
3.	I used the query builder to create the queries for views that include the new _M columns that are going to be used later for compare calculations.
4.	Given the size of the datasets we are dealing with, I focus on the task at hand which is identifying the records that don’t match and extracting any patterns. So I create compare nonmatching tables to hold the resulting records that are not matching between OLD and NEW only so that I can do some querying later on that requires a bit more computation thus less data will allow to run faster.
5.	I query the nonmatching tables to extract the number of mismatch per column in the dataset. I could then pivot here but I decide to bring the pivot operation within Excel my presentation layer. There I add below the pivoted table the list of records from the nonmatching tables so that I have a match count at both the row level and column level for each _M column.
6.	I then extract the SumRecordsMatchBetweenOldAndNew and the details.
7.	I save them under separate Excel files that are ready to be used for my analysis and to create the report. Since I am a visual guy, I created a visual heatmap of where the problems are in the dataset using conditional formatting. You can zoom out and literally see the clusters of issues, or you can use the nonmatching col sum sheet to sort and identify the columns with the lowest matches to start your investigation.


Report

We created tables for nonmatching records from the compare OLD vs NEW views. We look at the data from the perspective of each set of OLD/NEW table.


DE1_0_2008_Beneficiary_Summary_File_Sample_1 NEW has 207 non matching records.

•	This represents 207/ 116352 =~0.1779% of the dataset; 

•	There are 49 records found in OLD that are not found in NEWSYSTEM;

•	After that, it seems that the biggest driver of differences is BENE_BIRTH_DT followed by BENRES_OP, PPPYMT_OP, BENRES_CAR, BENE_HMO_CVRAGE_TOTONS, MEDREIMB_OP, BENRES_IP, PLAN_CVRGOS_NUM, MEDREIMB_IP, PPPYMT_IP, BENE_HI_CVRAGE_TOTONS, MEDREIMB_CAR, BENE_SMI_CVRAGE_TOTONS then PPPYMT_CAR. There are 2 records where the BENE_DEATH_DT does not match.

•	In the majority of cases, the BENE_BIRTH_DT is missing in NEWSYSTEM or the Beneficiary is younger in the NEWSYSTEM.


DE1_0_2009_Beneficiary_Summary_File_Sample_1 NEW has 247 non matching records.

•	This represents 247/ 114538 =~0.2156% of the dataset;

•	There are 58 records found in OLD that are not found in NEWSYSTEM;

•	After that, it seems that the biggest driver of differences is BENE_BIRTH_DT followed by MEDREIMB_OP, BENRES_CAR, PPPYMT_CAR, BENE_HMO_CVRAGE_TOTONS, BENRES_IP, PPPYMT_IP, MEDREIMB_IP, BENE_HI_CVRAGE_TOTONS, BENE_SMI_CVRAGE_TOTONS, BENRES_OP, PLAN_CVRGOS_NUM, MEDREIMB_CAR then PPPYMT_OP.

•	In the majority of cases, the BENE_BIRTH_DT is missing in NEWSYSTEM or the Beneficiary is younger in the NEWSYSTEM.


DE1_0_2010_Beneficiary_Summary_File_Sample_1 NEW has 206 non matching records.

•	This represents 206/ 112754 =~0.1827% of the dataset;

•	There are 58 records found in OLD that are not found in NEWSYSTEM;

•	After that, it seems that the biggest driver of differences is BENE_BIRTH_DT followed by MEDREIMB_OP, BENRES_CAR, PPPYMT_CAR, BENE_HMO_CVRAGE_TOTONS, BENRES_IP, PPPYMT_IP, MEDREIMB_IP, BENE_HI_CVRAGE_TOTONS, BENE_SMI_CVRAGE_TOTONS, BENRES_OP, PLAN_CVRGOS_NUM, MEDREIMB_CAR then PPPYMT_OP. 

•	In the majority of cases, the BENE_BIRTH_DT is missing in NEWSYSTEM or the Beneficiary is younger in the NEWSYSTEM.


DE1_0_2008_to_2010_Carrier_Claims_Sample_1A NEW has 9997 non matching records.

•	This represents 9997/ 2370667 =~0.4217% of the dataset;

•	All claims found in OLD are found in NEWSYSTEM;

•	The biggest drivers of differences are LINE_NCH_PMT_AMT, LINE_PRCSG_IND_CD, LINE_BENE_PTB_DDCTBL_AMT, LINE_BENE_PRMRY_PYR_PD_AMT, LINE_ALOWD_CHRG_AMT, LINE_COINSRNC_AMT, LINE_ICD9_DGNS_CD, ICD9_DGNS_CD, TAX_NUM, PRF_PHYSN_NPI, HCPCS_CD, CLM_FROM_DT.

•	In the majority of cases, LINE_NCH_PMT_AMT is higher in OLD vs NEWSYSTEM.


DE1_0_2008_to_2010_Carrier_Claims_Sample_1B NEW has 10081 non matching records.

•	This represents 10081/ 2370668 =~0.4252% of the dataset

•	All claims found in OLD are found in NEWSYSTEM

•	The biggest drivers of differences are LINE_NCH_PMT_AMT, LINE_PRCSG_IND_CD, LINE_BENE_PTB_DDCTBL_AMT, LINE_BENE_PRMRY_PYR_PD_AMT, LINE_ALOWD_CHRG_AMT, LINE_COINSRNC_AMT, LINE_ICD9_DGNS_CD, ICD9_DGNS_CD, TAX_NUM, PRF_PHYSN_NPI, HCPCS_CD, CLM_FROM_DT.

•	In the majority of cases, LINE_NCH_PMT_AMT is higher in OLD vs NEWSYSTEM.
