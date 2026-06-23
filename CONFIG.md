# Configuration Templates

## SQL Connection Configuration

### For Python MySQL Connector

**File**: `dashboard/load_data_to_sql.py`

```python
db_config = {
    'host': 'localhost',              # Your MySQL server address
    'user': 'root',                   # Your MySQL username
    'password': 'your_password',      # Your MySQL password
    'database': 'development_db'      # Database name
}
```

### For SQLAlchemy (Alternative)

```python
from sqlalchemy import create_engine

# Connection string format:
# mysql+mysqlconnector://user:password@host:port/database

engine = create_engine(
    'mysql+mysqlconnector://root:password@localhost:3306/development_db'
)

# Load data
df.to_sql('country_stats', con=engine, if_exists='replace', index=False)
```

### For Streamlit Secrets

**File**: `.streamlit/secrets.toml`

```toml
[database]
host = "localhost"
user = "root"
password = "your_password"
database = "development_db"
```

**Use in app.py**:
```python
import streamlit as st

db_config = {
    'host': st.secrets['database']['host'],
    'user': st.secrets['database']['user'],
    'password': st.secrets['database']['password'],
    'database': st.secrets['database']['database']
}
```

---

## Environment Variables

### Windows PowerShell

```powershell
$env:MYSQL_HOST = "localhost"
$env:MYSQL_USER = "root"
$env:MYSQL_PASSWORD = "your_password"
$env:MYSQL_DATABASE = "development_db"
```

### Windows Command Prompt

```cmd
set MYSQL_HOST=localhost
set MYSQL_USER=root
set MYSQL_PASSWORD=your_password
set MYSQL_DATABASE=development_db
```

### macOS/Linux

```bash
export MYSQL_HOST=localhost
export MYSQL_USER=root
export MYSQL_PASSWORD=your_password
export MYSQL_DATABASE=development_db
```

### .env File

**File**: `.env`

```
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=your_password
MYSQL_DATABASE=development_db
JUPYTER_ENABLE_LAB=yes
```

**Load in Python**:
```python
import os
from dotenv import load_dotenv

load_dotenv()

db_config = {
    'host': os.getenv('MYSQL_HOST'),
    'user': os.getenv('MYSQL_USER'),
    'password': os.getenv('MYSQL_PASSWORD'),
    'database': os.getenv('MYSQL_DATABASE')
}
```

---

## Database Setup Examples

### MySQL Local Installation

```bash
# macOS (using Homebrew)
brew install mysql
brew services start mysql
mysql -u root

# Windows (using MySQL Installer)
# 1. Download from mysql.com
# 2. Run installer
# 3. Configure MySQL Server
# 4. Start from Services

# Linux (Ubuntu/Debian)
sudo apt-get install mysql-server
sudo mysql_secure_installation
sudo service mysql start
```

### Create Database and User

```sql
-- Create database
CREATE DATABASE IF NOT EXISTS development_db;

-- Create user
CREATE USER 'data_user'@'localhost' IDENTIFIED BY 'secure_password';

-- Grant privileges
GRANT ALL PRIVILEGES ON development_db.* TO 'data_user'@'localhost';
FLUSH PRIVILEGES;

-- Verify
SHOW DATABASES;
SELECT USER();
```

### Remote Database Connection

```python
db_config = {
    'host': '192.168.1.100',           # Remote server IP
    'port': 3306,                      # MySQL port (default 3306)
    'user': 'remote_user',
    'password': 'remote_password',
    'database': 'development_db'
}
```

---

## Alternative Databases

### PostgreSQL

```python
from sqlalchemy import create_engine

engine = create_engine(
    'postgresql://user:password@localhost:5432/development_db'
)
```

### SQLite (Local File)

```python
import sqlite3

conn = sqlite3.connect('development_db.sqlite')
cursor = conn.cursor()

# Create table
cursor.execute('''
    CREATE TABLE IF NOT EXISTS country_stats (
        country TEXT PRIMARY KEY,
        child_mort REAL,
        ...
    )
''')
```

### Microsoft SQL Server

```python
from sqlalchemy import create_engine

engine = create_engine(
    'mssql+pyodbc://user:password@server/database?driver=ODBC+Driver+17+for+SQL+Server'
)
```

---

## Docker Configuration

### docker-compose.yml

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: development_db
      MYSQL_USER: data_user
      MYSQL_PASSWORD: userpassword
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      timeout: 20s
      retries: 10

  streamlit:
    build: .
    ports:
      - "8501:8501"
    depends_on:
      - mysql
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: data_user
      MYSQL_PASSWORD: userpassword
      MYSQL_DATABASE: development_db

volumes:
  mysql_data:
```

### Dockerfile

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

EXPOSE 8501

CMD ["streamlit", "run", "dashboard/app.py"]
```

### Run with Docker

```bash
# Build and run
docker-compose up

# Access at http://localhost:8501
```

---

## Cloud Deployment

### AWS RDS Connection

```python
import mysql.connector

db_config = {
    'host': 'your-instance.c9akciq32.us-east-1.rds.amazonaws.com',
    'user': 'admin',
    'password': 'your_password',
    'database': 'development_db',
    'port': 3306,
    'ssl_ca': '/path/to/rds-ca-bundle.pem'  # For SSL
}
```

### Google Cloud SQL

```python
from google.cloud.sql.connector import Connector
import sqlalchemy

connector = Connector()

def getconn():
    return connector.connect(
        "project:region:instance",
        "mysql+pymysql",
        user="postgres",
        password="your_password",
        db="development_db",
    )

engine = sqlalchemy.create_engine(
    "mysql+pymysql://",
    creator=getconn,
)
```

### Azure Database for MySQL

```python
db_config = {
    'host': 'your-server.mysql.database.azure.com',
    'user': 'username@servername',
    'password': 'your_password',
    'database': 'development_db',
    'use_pure': True,
    'ssl_ca': '/path/to/DigiCertGlobalRootCA.crt',
    'ssl_verify_cert': True,
    'ssl_verify_identity': True
}
```

---

## Testing Configurations

### Quick Test Script

```python
# test_config.py
import mysql.connector

def test_connection(config):
    try:
        conn = mysql.connector.connect(**config)
        cursor = conn.cursor()
        cursor.execute("SELECT DATABASE()")
        print(f"✓ Connected to: {cursor.fetchone()[0]}")
        cursor.close()
        conn.close()
        return True
    except Exception as e:
        print(f"✗ Connection failed: {e}")
        return False

if __name__ == "__main__":
    config = {
        'host': 'localhost',
        'user': 'root',
        'password': 'password',
        'database': 'development_db'
    }
    test_connection(config)
```

### Run Test

```bash
python test_config.py
```

---

## Best Practices

✅ **Do**:
- Use environment variables for sensitive data
- Keep credentials out of version control
- Test connection before deployment
- Use SSL for remote connections
- Implement connection pooling
- Regular backups

❌ **Don't**:
- Hard-code passwords in source code
- Commit .env files to Git
- Use default passwords
- Expose database to public internet
- Ignore SSL warnings
- Store plain-text credentials

---

## Recommended Setup

### Local Development
```python
# .env file (git-ignored)
MYSQL_HOST=localhost
MYSQL_USER=dev_user
MYSQL_PASSWORD=dev_password
MYSQL_DATABASE=development_db
```

### Production Deployment
```python
# Environment variables (set in deployment platform)
- Use managed database service (AWS RDS, Azure DB, etc.)
- Enable SSL/TLS
- Strong passwords (16+ characters)
- Restricted network access
- Regular backups and monitoring
```

---

For more information, see: `README.md` and `QUICK_START.md`