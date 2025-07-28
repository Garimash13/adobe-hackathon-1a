# Use official lightweight Python image
FROM python:3.10-slim

# Set working directory inside container
WORKDIR /app

# Copy and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code into container
COPY extractor.py .

# Create input and output directories
RUN mkdir -p /app/input /app/output

# Run the script when the container starts
CMD ["python", "extractor.py"]
