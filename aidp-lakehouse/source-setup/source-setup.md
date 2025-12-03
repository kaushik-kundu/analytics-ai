# Lab 1: Set Up Source System and Target System, AI Data Platform and Object Storage

## Introduction

This lab guides you through setting up Oracle Autonomous Transaction Processing (ATP) as the Source System for transactional airline data, setting up Autonomous AI Lakehouse as the Target System, AI Data Platform instance and Object Storage bucket.
This establishes a realistic starting point for extracting operational data into the lakehouse pipeline.

> **Estimated Time:** 1 hour

---

### About Oracle Autonomous Transaction Processing (ATP) and Oracle AI Data Platform (AIDP) and Autonomous AI Lakehouse

ATP is an autonomous database service optimized for transaction processing (OLTP) workloads, such as storing and managing operational data like flight records. In this workshop, ATP serves as the source system, simulating a real-world transactional database from which data is extracted for analytics.

AI Data Platform (AIDP) unifies all types of data—structured, unstructured, batch, and real-time into an open, and connected platform, laying the foundation for trusted and AI-ready data pipelines. It's an integrated environment with built-in tools and GenAI agent frameworks to build and deploy AI-powered applications faster, without the complexity of connecting separate tools.

Autonomous AI Lakehouse provides fast, secure analytics storage. Together, they form a modern lakehouse for transforming raw data into insights.

---

### Objectives

In this lab, you will:
- Provision an ATP instance
- Create a Source_XX schema in ATP
- Provision Autonomous AI Lakehouse 
- Create a Gold_XX schema in AI Lakehouse
- Provision AI Data Platform
- Provision Object Storage buckets

---

### Important Note

If multiple users are working on this workshop, then replace _XX by _01, _02, _03 etc.

For example:
- Use Source\_01, Source\_02, Source\_03 etc instead of Source\_XX
- Use Gold\_01, Gold\_02, Gold\_03 etc instead of Gold\_XX

---

### Prerequisites

This lab assumes you have:
- An Oracle Cloud account (or provided lab credentials)
- Access to Oracle Autonomous Transaction Processing (ATP)
- Access to Oracle Autonomous AI Lakehouse
- Access to Oracle AI Data Platform
- Access to Oracle Object Storage
- Basic familiarity with web-based Oracle Cloud interfaces

---

## Task 1: Provision ATP Instance

1. Log in to your cloud tenancy and navigate to **Oracle AI Database > Autonomous AI Database**

![Navigate to AI Database](./images/ai-database.png)

2. Click **Create Autonomous Database**.

3. Provide a display name (e.g., **airline-source-atp**), database name (e.g., **AIRLINESOURCE**), and select **Transaction Processing** as the workload type.

![ATP Setup](./images/atp-setup.png)

4. Set an administrator password and configure network access as needed (e.g., secure access from everywhere for simplicity).

**NOTE** If you would like to use a private database, a DB Tools Connection will need to be created to use SQL Developer web. This is outside the scope of this lab. For details, see [Create Database Tools Connection](https://docs.oracle.com/en-us/iaas/database-tools/doc/using-oracle-cloud-infrastructure-console.html).

5. Click **Create Autonomous Database**. Provisioning takes a few minutes.

---

## Task 2: Create Source_XX Schema

1. Once provisioned, navigate to **Database Actions > SQL** in the ATP instance details.

![ATP SQL](./images/atp-sql.png)

2. Sign in as the ADMIN user.

3. Create the Source_XX schema (replace "strong\_password" with a secure password):

```sql
<copy>
CREATE USER Source_XX IDENTIFIED BY "strong_password";
-- Data privileges
GRANT CONNECT, RESOURCE TO Source_XX;

-- Allow creation of tables, views, and other objects
GRANT CREATE SESSION TO Source_XX;
GRANT CREATE TABLE TO Source_XX;
GRANT CREATE VIEW TO Source_XX;
GRANT CREATE SEQUENCE TO Source_XX;
GRANT CREATE PROCEDURE TO Source_XX;
GRANT UNLIMITED TABLESPACE TO Source_XX;

-- Enable DBMS_CLOUD 
GRANT EXECUTE ON DBMS_CLOUD TO Source_XX;

-- Grant access to data_pump_dir (used for saveAsTable operation in spark)
GRANT READ, WRITE ON DIRECTORY DATA_PUMP_DIR TO Source_XX;
</copy>
```

4. Sign out of admin and navigate back to the ATP instance in the console

---

## Task 3: Add REST capabilities to Source_XX Schema

**NOTE** If unable to sign in directly as Source_XX schema, enable REST access

1. Navigate to AI DB > database actions > database users > search for Source\_XX > select three dots > enable rest > log in to sql developer web as Source\_XX

![Database Users](./images/atp-db-users.png)

![Enable REST](./images/enable-rest-source.png)

2. Once enabled edit the user and set Quota to Unlimited 

![Set Quota](./images/unlimited-quota.png)

## Task 4: Log in to SQL Developer as Source_XX Schema 

1. Navigate back to AI DB > database actions > SQL > Once in SQL Developer select ADMIN (top right) > Sign Out

2. Provide Source_XX as username and give password as defined in previous task. Sign in. 

![Sign in Source_XX Schema](./images/source-data-sign-in.png)

**NOTE** If still unable to log in, try navigating back to database user page and click the following link - 

![Access REST Source_XX](./images/source-data-sign-in-2.png)

3. Navigate to Development > SQL 

## Task 5: Provision Autonomous AI Lakehouse

1. Log in to your cloud tenancy and navigate to Oracle AI Database > Autonomous AI Database

![AI Database](./images/ai-database.png)

2. Select Create Autonomous AI Database 

3. Give it a name (e.g. **aidp-db**) and select workload type as Lakehouse. Select database version 26ai and leave other options as default. 

![Create AI Database](./images/create-aidp-db.png)

4. Provide a password and set Access Type to 'Secure access from everywhere' 

![Create AI Database](./images/create-aidp-db-2.png)

**NOTE** If you would like to use a private database, a DB Tools Connection will need to be created to use SQL Developer web. This is outside the scope of this lab. For details, see [Create Database Tools Connection](https://docs.oracle.com/en-us/iaas/database-tools/doc/using-oracle-cloud-infrastructure-console.html).

5. Create the AI Database. The provisioning process will take a few minutes.

6. Once provisioned, navigate to Database actions > SQL. This will open SQL Developer as admin user. 

![SQL Developer](./images/sql-developer.png)

---

## Task 6: Create Gold_XX Schema 

1. Create a Gold_XX Schema (User) in Autonomous Data Lakehouse. Replace "strong\_password" with your own password.

```sql
<copy>
CREATE USER gold_XX IDENTIFIED BY "strong_password";
</copy>
```

2. Grant Required Roles/Privileges to Gold_XX Schema

```sql
<copy>
-- Data privileges
GRANT CONNECT, RESOURCE TO gold_XX;

-- Allow creation of tables, views, and other objects
GRANT CREATE SESSION TO gold_XX;
GRANT CREATE TABLE TO gold_XX;
GRANT CREATE VIEW TO gold_XX;
GRANT CREATE SEQUENCE TO gold_XX;
GRANT CREATE PROCEDURE TO gold_XX;
GRANT UNLIMITED TABLESPACE TO gold_XX;

-- Enable DBMS_CLOUD 
GRANT EXECUTE ON DBMS_CLOUD TO gold_XX;

-- Grant access to data_pump_dir (used for saveAsTable operation in spark)
GRANT READ, WRITE ON DIRECTORY DATA_PUMP_DIR TO gold_XX;
</copy>
```

3. Log out of admin schema once gold_XX schema is created.

## Task 7: Add REST capabilities to GOLD_XX Schema

**NOTE** If unable to sign in directly as gold_XX schema, enable REST access

1. Navigate to AI DB > database actions > database users > search for gold\_XX > select three dots > enable rest > log in to sql developer web as gold\_XX

2. Once enabled edit the user and set Quota to Unlimited 

![Enable REST](./images/enable-rest-gold.png)

## Task 8: Log in to SQL Developer as GOLD_XX Schema 

1. Navigate back to AI DB > database actions > SQL > Once in SQL Developer select ADMIN (top right) > Sign Out

2. Provide gold_XX as username and give password as defined in previous task. Sign in. 

![Sign in Gold_XX Schema](./images/sign-in-gold.png)

**NOTE** If still unable to log in, try navigating back to database user page and click the following link - 

![Access REST Gold_XX](./images/access-rest-gold.png)

3. Navigate to Development > SQL. Once access is confirmed you can proceed to next task.

## Task 9: Provision AI Data Platform Instance

1. Navigate to Analytics & AI > AI Data Platform 

![AI Data Platform](./images/create-aidp.png)

2. Provide a name for AIDP and workspace

![Create AIDP](./images/create-aidp-2.png)

3. Set the access level as standard and explicitly 'Add' the policies. If the policies aren't added it will fail to create. Optional policies can also be added depending on the use case. For this lab, we will need to enable object deletion - 

![Enable Object Deletion](./images/aidp-add-additional-policies.png)

![Add Policies](./images/aidp-save-policies.png)

4. Create the instance. This will take a few minutes to provision.

## Task 10: Set Up Object Storage

1. Navigate to **Object Storage** in the OCI Console.

2. Create bucket **aidp-demo-bucket_XX** in the AIDP compartment.

3. Create a folder 'delta' in the bucket.

![Create New Bucket Folder](./images/bucket-create-new-folder.png)

## Next Steps

With the source data loaded in ATP, proceed to Lab 2 to extract and process it in the AI Data Platform and Lakehouse.

---

## Acknowledgements

**Authors**
* **Luke Farley**, Senior Cloud Engineer, ONA Data Platform

**Contributors**
* **Kaushik Kundu**, Master Principal Cloud Architect, ONA Data Platform S&E

**Last Updated By/Date:**
* **Kaushik Kundu**, Master Principal Cloud Architect, ONA Data Platform, December 2025