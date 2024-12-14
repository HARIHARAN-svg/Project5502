# Project5502
name: CI/CD Pipeline - Deploy to Hugging Face Spaces

on:
  workflow_dispatch:  # Allows manual triggering of the workflow
  push:
    branches:
      - main  # Trigger the workflow when there's a push to the 'main' branch

jobs:
  # CI Job - Test
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.13'  # Use your preferred Python version

      - name: Install dependencies
        run: |
          pip install -r requirements.txt  # Install project dependencies

      - name: Run pytest
        run: |
          pytest tests/  # Run tests located in the 'tests' directory

  # CD Job - Deploy to Hugging Face Spaces
  deploy:
    needs: test  # Ensure deployment happens only after successful tests
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install dependencies
        run: |
          pip install -r requirements.txt  # Install dependencies again for deployment

      - name: Push to Hugging Face Hub
        uses: huggingface/hub-actions/push@main
        with:
          token: ${{ secrets.HF_TOKEN }}  # Hugging Face token stored in GitHub Secrets
          repo_id: your-username/your-repository  # Replace with your Hugging Face repository name
          branch: main  # Deploy from the 'main' branch

