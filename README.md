# PayLink

PayLink is a Bill Payment System that allows users to manage and pay their bills through a centralized platform.

## Project Setup

The project uses:
- Python
- FastAPI
- Microsoft SQL Server
- Docker and Docker Compose

## Prerequisites

Before running the project, make sure you have:

- Git installed
- Docker Desktop installed and running


## Installation and Setup

### 1. Clone the repository

```bash
git clone https://github.com/nouranreda19/PayLink.git
cd PayLink
```

### 2. Configure environment variables

Create a `.env` file in the project root directory and add:

```env
DB_PASSWORD=YourStrongPassword123!
```

Replace the example password with your own strong SQL Server password.

Do not commit the `.env` file to GitHub.

### 3. Build and start the containers

Run the following command:

```bash
docker compose up --build
```

This command builds the application image and starts the application and SQL Server containers.

### 4. Access the application

Open your browser and visit:

http://localhost:8000

