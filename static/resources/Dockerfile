FROM apache/airflow:2.10.0

RUN pip install -U pip --upgrade pip

COPY requirements.txt /requirements.txt

RUN pip install --no-cache-dir  -r /requirements.txt