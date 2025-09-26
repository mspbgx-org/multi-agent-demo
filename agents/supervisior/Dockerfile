# Use Python 3.11 slim image as base
FROM python:3.11-slim

# Set working directory in the container
WORKDIR /app

# Copy source code
COPY supervisior_agent.py requirements.txt ./
RUN python -m pip install -r requirements.txt

# Run the application
CMD ["python", "supervisior_agent.py"]
