---
title: "Local Environment "
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

## Create environment

1. Install [Docker](https://www.docker.com/)
2. You download
    - [docker-compose.yaml](/repo_pmt_ws-fcj-003/resources/docker-compose.yaml)
    - [Dockerfile](/repo_pmt_ws-fcj-003/resources/Dockerfile)
    - [requirements.txt](/repo_pmt_ws-fcj-003/resources/requirements.txt)
3. Create a folder with struct below:

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-01.png)

4. Set up folder mount docker container

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-02.png)
5. Run docker compose

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-03.png)
6. Let’s navigate to our Airflow homepage http://localhost:8080/ and will prompt you to login. Please use **airflow**
   for both username and password.

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-04.png)

    - There are a number of example dags present if enabled: **AIRFLOW__CORE__LOAD_EXAMPLES: 'true'**

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-05.png)