# RAG-based Assistant to Chat with Papers With Code

This repository contains the code for building a RAG-based assistant to chat with Papers With Code using Streamlit.

## Requirements

- A GCP account with VertexAI and Cloud Run services activated
- An OpenAI API key
- A free account on [Upstash](https://upstash.com/) (serverless database)
- Pulumi installed and configured

## 1. Indexing

To index data into the vector DB, you first need to create an index on Upstash and fill in the credentials in the `.env` file:

```plaintext
UPSTASH_VECTOR_REST_URL=...
UPSTASH_VECTOR_REST_TOKEN=...
OPENAI_API_KEY=...
```

# APP LEVEL COMMANDS

```
python src/paperswithcode.py
python src/index_papers.py test --query "openai" --max_papers 5
python src/index_papers.py test-upstash
python src/index_papers.py index --query "openai" --max_papers 20
python src/brag.py
streamlit run bapp.py --theme.primaryColor "#135aaf"

docker build -t streamlit-papers-app .
docker run -d  --name papers-chat   -p 8501:8501  -e OPENAI_API_KEY="$(grep OPENAI_API_KEY .env | cut -d '=' -f2)" -e UPSTASH_VECTOR_REST_URL="$(grep UPSTASH_VECTOR_REST_URL .env | cut -d '=' -f2)"   -e UPSTASH_VECTOR_REST_TOKEN="$(grep UPSTASH_VECTOR_REST_TOKEN .env | cut -d '=' -f2)"  streamlit-papers-app
OR
docker run -d --name papers-chat -p 8501:8501 --env-file .env streamlit-papers-app
OR
docker run -d   --name papers-chat   -p 8501:8501   streamlit-papers-app

docker exec -it container_id bash
```

# Google Cloud Run

```
gcloud auth configure-docker
gcloud builds submit --tag gcr.io/vertex-460809/rag --timeout=2h
```

# Google App Engine
    App Engine in Google Cloud Platform (GCP) is a fully managed serverless platform for building and deploying applications. It abstracts away infrastructure management, so developers can focus purely on writing code.

| Feature                              | Description                                                                                        |
| ------------------------------------ | -------------------------------------------------------------------------------------------------- |
| **Serverless**                       | You don’t manage servers or infrastructure — Google handles scaling, patching, and load balancing. |
| **Auto Scaling**                     | Automatically scales up during high traffic and down to zero when idle.                            |
| **Supports Multiple Languages**      | Built-in support for Python, Java, Go, Node.js, PHP, Ruby, .NET, and custom runtimes via Docker.   |
| **Integrated with GCP Services**     | Seamlessly works with Firestore, Cloud SQL, Pub/Sub, and more.                                     |
| **App Versions & Traffic Splitting** | Deploy multiple versions of your app and gradually roll out or split traffic between them.         |
| **Built-in Security**                | Google secures the underlying infrastructure, and it supports IAM, HTTPS, and secret management.   |

| Environment  | Description                                                                                                   |
| ------------ | ------------------------------------------------------------------------------------------------------------- |
| **Standard** | Uses a sandboxed environment; very fast to start, supports auto-scaling to zero, cheaper for small workloads. |
| **Flexible** | Runs your app in Docker containers on Google Compute Engine VMs; supports more customization and libraries.   |

The equivalents of Google App Engine in Azure and AWS are:
✅ AWS Equivalent: AWS Elastic Beanstalk
✅ Azure Equivalent: Azure App Service

```
gcloud config set project vertex-460809
gcloud config get-value project
gcloud projects add-iam-policy-binding vertex-460809 --member="user:sd@gmail.com"  --role="roles/cloudbuild.builds.editor"
gcloud services enable cloudbuild.googleapis.com
gcloud auth list
gcloud config list
gcloud projects get-iam-policy $(gcloud config get-value project)
gcloud builds submit --tag gcr.io/vertex-460809/rag --timeout=2h
gcloud services enable appengine.googleapis.com
gcloud app deploy
```

# View in Streamlit
gcloud app browse

# PULUMI
```
gcloud auth application-default login

# Install required Python packages
pip install pulumi pulumi-gcp pulumi-docker

# Initialize the Pulumi stack
pulumi stack init dev

# Set your GCP project ID
pulumi config set gcp:project vertex-460809

# Optionally set other configurations
pulumi config set gcp:region us-central1
pulumi config set app-name streamlit-app

# Deploy the stack
pulumi up

# destroy
for stack in dev dev-new dev1 dev12; do
  echo "Force removing stack: $stack"
  pulumi stack rm "$stack" --yes --force
done
pulumi destroy
pulumi stack rm [<stack-name>] --force
pulumi stack ls
pulumi version
```