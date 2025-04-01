# Django + PostgreSQL Setup Guide

This guide provides detailed steps to ensure your PostgreSQL database is running correctly before starting your Django project.

---

## **1. Start PostgreSQL Service**
Before running your Django application, ensure PostgreSQL is up and running.

### **For Windows:**
1. Open **PowerShell as Administrator**
2. Run the following command to restart PostgreSQL:
   ```powershell
   net stop postgresql-x64-17
   net start postgresql-x64-17
   ```
3. Verify that PostgreSQL is listening on the correct port:
   ```powershell
   netstat -ano | findstr :5433
   ```
   - If PostgreSQL is listening, you should see output containing `LISTENING`.
   - If port **5433** is used instead of **5432**, update your Django `settings.py`.

4. Check the currently running port for PostgreSQL:
   ```powershell
   netstat -abn | Select-String "LISTENING"
   ```

5. If needed, connect to PostgreSQL via `psql`:
   ```powershell
   psql -U postgres -d postgres -h 127.0.0.1 -p 5433
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
- Check the currently running port using:
  ```powershell
  netstat -abn | Select-String "LISTENING"
  ```

### **Django Database Errors**
- Ensure the correct database credentials are in `settings.py`.
- Check for typos in the `pg_hba.conf` file.
- Restart PostgreSQL after any configuration changes.

---

This guide ensures PostgreSQL is correctly set up and running every time before working on your Django project. 🚀

