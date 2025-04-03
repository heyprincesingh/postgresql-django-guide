# Django + PostgreSQL Setup Guide

This guide provides detailed steps to create a compatible PostgreSQL database and ensure it is running correctly before starting your Django project.

---

## **Prerequisites**
- PostgreSQL installed and running on your machine or server.
- pgAdmin (or another PostgreSQL client tool) installed.
- Administrative access to the PostgreSQL database.

---

## **Step 1: Create a New Database**

### **1.1: Using pgAdmin**
1. **Open pgAdmin** and connect to your PostgreSQL server.
2. Right-click on **Databases** in the left panel.
3. Select **Create** → **Database**.
4. In the **Create Database** window:
   - **Name**: Enter `db-name`.
   - Select the appropriate **Owner** (usually the default user).
   - Click **Save**.

### **1.2: Using Terminal (psql Command Line)**
1. Open the terminal and log in to PostgreSQL:
   ```bash
   psql -U postgres
   ```
2. Create a new database:
   ```sql
   CREATE DATABASE "db-name";
   ```
3. Assign ownership (optional):
   ```sql
   ALTER DATABASE "db-name" OWNER TO your_username;
   ```

---

## **Step 2: Set the `search_path` for a User or Database**

### **2.1: Using pgAdmin (GUI Method)**
#### **Set `search_path` for a Specific User**
1. In **pgAdmin**, navigate to **Servers → Your Server → Databases → db-name → Login/Group Roles**.
2. Find the **User** (e.g., `prince`) you want to modify and right-click on it.
3. Select **Properties**.
4. Go to the **"Variables"** tab.
5. Click **Add** and set:
   - **Name**: `search_path`
   - **Value**: `newschema`
6. Click **Save**.

#### **Set `search_path` at the Database Level (for all users)**
1. In **pgAdmin**, right-click on the **`db-name`** database under **Databases**.
2. Select **Properties**.
3. Go to the **"Parameters"** tab.
4. Click **Add** and enter:
   - **Name**: `search_path`
   - **Value**: `newschema`
5. Click **Save**.

### **2.2: Using Terminal (psql Command Line)**
#### **Set `search_path` for a Specific User**
```sql
ALTER ROLE your_username SET search_path TO newschema;
```

#### **Set `search_path` at the Database Level**
```sql
ALTER DATABASE "db-name" SET search_path TO newschema;
```

---

## **Step 3: Set Timezone to UTC**

### **3.1: Using pgAdmin**
1. Right-click on the **`db-name`** database in **pgAdmin** and select **Properties**.
2. Go to the **"Parameters"** tab.
3. Click **Add** and set:
   - **Name**: `timezone`
   - **Value**: `UTC`
4. Click **Save**.

### **3.2: Using Terminal (psql Command Line)**
#### **Set TimeZone for the Database**
```sql
ALTER DATABASE "db-name" SET timezone TO 'UTC';
```
#### **Set TimeZone for a Specific User**
```sql
ALTER ROLE admin SET timezone TO 'UTC';
```

---

## **Step 4: Verify Configuration**

### **4.1: Verify `search_path`**
```sql
SHOW search_path;
```

### **4.2: Verify Timezone**
```sql
SHOW timezone;
```

---

## **Step 5: Additional Configuration (Optional)**

### **5.1: Configure Automatic Connection Settings**
You can configure PostgreSQL to automatically set the `search_path` and `timezone` each time a new session starts by adding the following to the `postgresql.conf` file:

```bash
# Set the default search path
search_path = 'newschema'

# Set the default timezone
timezone = 'UTC'
```

- This ensures that every session uses the specified `search_path` and `timezone` without manual setup.

---

## **Starting PostgreSQL Service**

Before running your Django application, ensure PostgreSQL is up and running.

### **For Windows:**

1. **Open PowerShell as Administrator**
2. **Start PostgreSQL Service**  
   ```powershell
   net start postgresql-x64-17
   ```
3. **Verify that PostgreSQL is Running**  
   ```powershell
   Get-Service | Where-Object { $_.DisplayName -match "PostgreSQL" }
   ```
4. **Verify PostgreSQL is Listening on the Correct Port**  
   ```powershell
   netstat -abn | Select-String "LISTENING"
   ```

---

### **For Automatic Startup After System Restart:**
Set PostgreSQL to auto-start:
```powershell
sc config postgresql-x64-17 start= auto
```

---

## **Django Database Connection Setup**
Ensure `settings.py` contains the correct database settings:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'your_database_name',
        'USER': 'postgres',
        'PASSWORD': 'your_password',
        'HOST': '127.0.0.1',
        'PORT': '5433',  # Change if using 5432
    }
}
```

Run migrations to apply changes:
```bash
python manage.py migrate
```

---

## **Run Django Development Server**
```bash
python manage.py runserver
```

If everything is configured correctly, your Django app should connect to PostgreSQL without issues.

---

## **Troubleshooting**

### **PostgreSQL Connection Issues**
- Ensure PostgreSQL service is running:
  ```powershell
  Get-Service | Where-Object { $_.DisplayName -match "PostgreSQL" }
  ```
- Try connecting manually using `psql`:
  ```powershell
  psql -U postgres -d postgres -h 127.0.0.1 -p 5433
  ```
- Check if PostgreSQL is listening:
  ```powershell
  netstat -ano | findstr :5433
  ```

### **Django Database Errors**
- Ensure correct database credentials in `settings.py`.
- Restart PostgreSQL after configuration changes.

---
