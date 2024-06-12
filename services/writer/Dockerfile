FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

ADD . /app

EXPOSE 8080

CMD ["python3", "main.py"]
