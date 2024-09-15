---
title: "MWAA Environment"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1 </b> "
---

## Create MVAA Environment

1. Create S3 bucket containing resources

    - Go to [S3 console](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1#)
    - Create S3 Bucket MWAA

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-01.png)

    - Create dags folder: containing dag files

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-02.png)

    - Upload requirements.txt file (If available)
    - Upload plugin.zip file (If available)

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-03.png)
2. Create MVAA Environment
    - Go to [MVAA console](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments)
    - Create MVAA Environment
        - Select **Create environment**
        - Enter environment name

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-04.png)
        - Select source S3 bucket

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-05.png)

    - Network settings
        - Select **Web server/public access** to be able to access UI from the internet

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-06.png)

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-07.png)

    - Select environment type (according to your needs)

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-08.png)

    - Select IAM role for Airflow: can create new if not available

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-09.png)

    - Add necessary execution permissions

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-10.png)

3. Open connections to the MWAA environment

    - Create [VPC endpoint](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Endpoints:)

        1. [x] S3 endpoint

        2. [x] Log endpoint

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-11.png)

    - Enable UI access from the internet

        -
        Access [Airflow Security Group](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#SecurityGroups:)

        - Update Inbound rule

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-12.png)

4. Access MWAA UI

    - Go to [MVAA console](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments)

    - Select **Open Airflow UI**

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-13.png)

      Please use **airflow** for both username and password

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-14.png)

5. Clean up resources

    - Delete MWAA environment

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-15.png)

    - Delete VPC endpoint

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-16.png)

    - Delete IAM role

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-17.png)

    - Delete Security Group

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-18.png)
    - Delete S3 bucket