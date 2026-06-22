FROM python:3.11-slim

WORKDIR /app

COPY EDF-AI/requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY EDF-AI/ .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]