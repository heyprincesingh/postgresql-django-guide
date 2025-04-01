# Django + PostgreSQL Setup Guide

This guide provides detailed steps to ensure your PostgreSQL database is running correctly before starting your Django project.

---

## **1. Start PostgreSQL Service**

Before running your Django application, ensure PostgreSQL is up and running.

### **For Windows:**

1. **Open PowerShell as Administrator**
2. **Start PostgreSQL Service**  
   Run the following command to start the PostgreSQL service:
   ```powershell
   net start postgresql-x64-17
   ```

3. **Verify that PostgreSQL is Running**  
   You can check the status of PostgreSQL by running:
   ```powershell
   Get-Service | Where-Object { $_.DisplayName -match "PostgreSQL" }
   ```
   If PostgreSQL is running, you should see `Running` in the status column.

4. **Verify PostgreSQL is Listening on the Correct Port**  
   Run the following command to check if PostgreSQL is listening on port 5433:
   ```powershell
   netstat -abn | Select-String "LISTENING"
   ```
   Look for an entry like:
   ```
   TCP    0.0.0.0:5433           0.0.0.0:0              LISTENING
   ```
   - If PostgreSQL is listening on port **5433** instead of **5432**, update your Django `settings.py`.

5. **Manually Connect to PostgreSQL (if needed)**  
   ```powershell
   psql -U postgres -d postgres -h 127.0.0.1 -p 5433
   ```

---

### **For Automatic Startup After System Restart:**

If you don’t want to manually start PostgreSQL after each system restart, you can set PostgreSQL to start automatically with Windows:

1. **Set PostgreSQL to Auto-Start**  
   Run the following command to configure PostgreSQL to start automatically after a reboot:
   ```powershell
   sc config postgresql-x64-17 start= auto
   ```

---

## **2. Verify PostgreSQL Configuration**

### **Check if PostgreSQL is Using the Correct Port**
- Navigate to the PostgreSQL configuration folder (default location):
  ```
  C:\Program Files\PostgreSQL\17\data\postgresql.conf
  ```
- Open `postgresql.conf` and verify this line:
  ```
  listen_addresses = 'localhost'
  port = 5433
  ```
  If needed, change the port to `5432`.

### **Update `pg_hba.conf` (Client Authentication)**
- Navigate to `pg_hba.conf` in the same folder.
- Ensure the following lines exist for authentication:
  ```
  # IPv4 local connections:
  host    all             all             127.0.0.1/32            scram-sha-256
  ```

- After modifications, restart PostgreSQL:
  ```powershell
  net stop postgresql-x64-17
  net start postgresql-x64-17
  ```

---

## **3. Setup Django Database Connection**
Ensure your Django project's `settings.py` contains the correct database settings:

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

Run the following command to apply migrations and ensure Django connects successfully:
```bash
python manage.py migrate
```

---

## **4. Run the Django Development Server**
Once everything is set up, start your Django server:
```bash
python manage.py runserver
```
If everything is configured correctly, your Django app should connect to PostgreSQL without issues.

---

## **Troubleshooting**
### **PostgreSQL Connection Issues**
- Ensure PostgreSQL service is running: `Get-Service | Where-Object { $_.DisplayName -match "PostgreSQL" }`
- Try connecting manually using `psql`: `psql -U postgres -d postgres -h 127.0.0.1 -p 5433`
- If `netstat -ano | findstr :5433` does not return results, PostgreSQL might not be listening on the correct port.

### **Django Database Errors**
- Ensure the correct database credentials are in `settings.py`.
- Check for typos in the `pg_hba.conf` file.
- Restart PostgreSQL after any configuration changes.

---

This guide ensures PostgreSQL is correctly set up and running every time before working on your Django project. 🚀

---

Let me know if you need any more changes!
