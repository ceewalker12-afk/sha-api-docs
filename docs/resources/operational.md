# Operational Documentation

---

## Installation Guide

### Prerequisites
- Python 3.8+
- pip
- Git
- PostgreSQL 14+ (production) or SQLite (development)

### Step 1 — Clone the Repository
```bash
git clone https://github.com/your-org/sha-system.git
cd sha-system
```

### Step 2 — Create Virtual Environment
```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate
```

### Step 3 — Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 4 — Configure Environment
```bash
cp .env.example .env
```

Edit `.env`:
```env
FLASK_SECRET_KEY=generate-a-random-64-char-string
JWT_SECRET=another-random-64-char-string
FLASK_HOST=0.0.0.0
FLASK_PORT=5000
FLASK_DEBUG=False

# Database (leave blank for SQLite in development)
DATABASE_URL=postgresql://user:password@localhost/sha_db

# Africa's Talking (SMS/USSD)
AFRICASTALKING_API_KEY=your_key
AFRICASTALKING_USERNAME=your_username

# M-Pesa (Daraja API)
MPESA_CONSUMER_KEY=your_key
MPESA_CONSUMER_SECRET=your_secret
MPESA_SHORTCODE=your_shortcode
MPESA_PASSKEY=your_passkey
MPESA_CALLBACK_URL=https://your-domain.com/api/payments/callback
```

### Step 5 — Initialize Database
```bash
python setup_system.py
```

This creates all tables and a default admin user. **Save the printed admin password.**

### Step 6 — Start the Server

**Windows:**
```bash
start.bat
```

**Linux/Mac:**
```bash
python main_app.py
```

**With Docker:**
```bash
docker-compose up -d
```

### Step 7 — Verify Installation
```bash
curl http://localhost:5000/health
```

Expected:
```json
{ "status": "healthy", "service": "SHA Verification System", "version": "1.0.0" }
```

---

## Configuration Guide

### Key Configuration Options

| Variable | Default | Description |
|----------|---------|-------------|
| `FLASK_PORT` | `5000` | API server port |
| `FLASK_DEBUG` | `False` | Enable debug mode (dev only) |
| `JWT_EXPIRATION_HOURS` | `24` | Token expiry in hours |
| `LOG_LEVEL` | `INFO` | Logging level |
| `MAX_CONTENT_LENGTH` | `16MB` | Max upload size |
| `RATE_LIMIT` | `100/hour` | API rate limit per key |

### Feature Flags

Enable/disable services without code changes:

```env
SMS_ENABLED=true
SMS_MOCK=false          # true = log SMS instead of sending
EMAIL_ENABLED=true
EMAIL_MOCK=false
USSD_ENABLED=true
ML_ENABLED=true
MPESA_ENABLED=true
MPESA_MOCK=false        # true = simulate payments
```

Check current feature state:
```bash
curl http://localhost:5000/api/system/features \
  -H "Authorization: Bearer $ADMIN_TOKEN"
```

---

## Backup Procedures

### Automated Daily Backup (SQLite)
```bash
# Add to Windows Task Scheduler or Linux cron
python -c "
from backend.database.models import Database
db = Database()
db.create_backup()
print('Backup complete')
"
```

### Manual Backup
```bash
# SQLite
cp sha_system.db backups/sha_backup_$(date +%Y%m%d_%H%M%S).db

# PostgreSQL
pg_dump sha_db > backups/sha_backup_$(date +%Y%m%d_%H%M%S).sql
```

### Backup Encryption
```bash
# Encrypt backup before storing
python -c "
from cryptography.fernet import Fernet
key = open('.backup_key', 'rb').read()
f = Fernet(key)
data = open('backups/sha_backup.db', 'rb').read()
encrypted = f.encrypt(data)
open('backups/sha_backup.db.enc', 'wb').write(encrypted)
"
```

### Backup Schedule Recommendation
| Frequency | Type | Retention |
|-----------|------|-----------|
| Daily 02:00 | Full backup | 90 days |
| Every 6 hours | Incremental | 7 days |
| Weekly | Archive | 1 year |

---

## Recovery Procedures

### Restore from Backup (SQLite)
```bash
# Stop the server first
# Restore
cp backups/sha_backup_20240601_020000.db sha_system.db

# Verify
python verify_startup.py

# Restart server
python main_app.py
```

### Restore from Encrypted Backup
```bash
python -c "
from cryptography.fernet import Fernet
key = open('.backup_key', 'rb').read()
f = Fernet(key)
data = open('backups/sha_backup.db.enc', 'rb').read()
decrypted = f.decrypt(data)
open('sha_system.db', 'wb').write(decrypted)
print('Restore complete')
"
```

### Restore PostgreSQL
```bash
psql sha_db < backups/sha_backup_20240601.sql
```

---

## Monitoring Setup

### Built-in Health Endpoints
```bash
# Overall health
curl http://localhost:5000/health

# API health
curl http://localhost:5000/api/health

# USSD health
curl http://localhost:5000/ussd/health

# Service status
curl http://localhost:5000/api/system/status
```

### Recommended Monitoring Stack

**Option 1 — Simple (UptimeRobot)**
- Monitor `GET /health` every 5 minutes
- Alert via SMS/email on failure
- Free tier available

**Option 2 — Full Stack (Grafana + Prometheus)**
```yaml
# prometheus.yml
scrape_configs:
  - job_name: sha_system
    static_configs:
      - targets: ['localhost:5000']
    metrics_path: /health
```

**Option 3 — Log Monitoring (Loki)**
```bash
# Loki is already integrated — configure endpoint in .env
LOKI_URL=http://your-loki-instance:3100
```

### Log Files
```
logs/sha_system.log     # Main application log (rotates at 10MB, 5 backups)
logs/self_test.log      # Startup self-test results
```

### Key Metrics to Monitor
| Metric | Alert Threshold |
|--------|----------------|
| API response time | > 1 second |
| Error rate | > 1% of requests |
| Active fraud alerts | > 20 unresolved |
| Disk space | < 10% free |
| Memory usage | > 85% |
| Failed logins | > 10 in 5 minutes |
