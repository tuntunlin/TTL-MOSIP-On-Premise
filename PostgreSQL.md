
# MOSIP Database Schema များကို စစ်ဆေးနည်း

MOSIP system တွင် database schema များကို စစ်ဆေးရန် အောက်ပါနည်းလမ်းများကို အသုံးပြုနိုင်ပါသည်။

## 1. PostgreSQL Pod ထဲသို့ဝင်ရောက်စစ်ဆေးခြင်း

```bash
# PostgreSQL pod ကိုရှာပါ
kubectl get pods -n mosip-system -l app=postgresql

# Pod ထဲသို့ဝင်ပါ
kubectl exec -it <postgresql-pod-name> -n mosip-system -- bash

# PostgreSQL shell ထဲသို့ဝင်ပါ
psql -U mosipuser -d mosip_kernel
```

## 2. Schema များစာရင်းကြည့်ရှုနည်း

```sql
-- Database အားလုံးစာရင်းကြည့်ရန်
\l

-- MOSIP databases များသို့ပြောင်းရန်
\c mosip_kernel

-- Schema များစာရင်းကြည့်ရန်
\dn

-- တစ်ခုချင်းစီအတွက် table များကြည့်ရန်
\dt <schema_name>.*
\dt idrepo.*
\dt kernel.*
\dt master.*
```

## 3. အရေးကြီးသော MOSIP Schema များကိုစစ်ဆေးခြင်း

```sql
-- Kernel schema tables
SELECT table_name FROM information_schema.tables 
WHERE table_schema = 'kernel' 
ORDER BY table_name;

-- ID Repository schema
SELECT table_name FROM information_schema.tables 
WHERE table_schema = 'idrepo' 
ORDER BY table_name;

-- Master data schema
SELECT table_name FROM information_schema.tables 
WHERE table_schema = 'master' 
ORDER BY table_name;
```

## 4. Kubernetes Secret မှ Database Credentials ရယူခြင်း

```bash
# Database credentials များကိုရယူရန်
kubectl get secret -n mosip-system mosip-db-secret -o jsonpath='{.data}' | jq 'map_values(@base64d)'
```

## 5. Schema Version စစ်ဆေးခြင်း

```sql
-- Kernel schema version
SELECT * FROM kernel.schema_migrations;

-- ID Repo schema version
SELECT * FROM idrepo.schema_migrations;

-- Master schema version
SELECT * FROM master.schema_migrations;
```

## 6. Flyway Migration History ကြည့်ရှုခြင်း

```bash
# Flyway pod ကိုရှာပါ (MOSIP deployment ပေါ်မူတည်၍)
kubectl get pods -n mosip-system -l app=flyway

# Flyway logs များကြည့်ရှုရန်
kubectl logs -n mosip-system <flyway-pod-name>
```

## 7. အရေးကြီးသော Table များ၏ Data ကိုစစ်ဆေးခြင်း

```sql
-- User details များကြည့်ရှုရန်
SELECT * FROM kernel.uin LIMIT 5;

-- Device details
SELECT * FROM master.device_master LIMIT 5;

-- Center details
SELECT * FROM master.registration_center LIMIT 5;
```

## 8. Backup လုပ်ပြီး Schema Structure ကြည့်ရှုခြင်း

```bash
# Schema dump လုပ်ရန်
kubectl exec -n mosip-system <postgresql-pod-name> -- \
pg_dump -U mosipuser -d mosip_kernel --schema-only > mosip_schema_dump.sql

# Data dictionary generate လုပ်ရန်
kubectl exec -n mosip-system <postgresql-pod-name> -- \
pg_dump -U mosipuser -d mosip_kernel --schema-only --no-owner --no-privileges \
--table='kernel.*' --table='idrepo.*' --table='master.*' > mosip_data_dictionary.sql
```

## 9. MOSIP Module အလိုက် Database Connection စစ်ဆေးခြင်း

```bash
# Kernel service logs များကြည့်ရှုရန်
kubectl logs -n mosip-system -l app=kernel-auth-service --tail=50

# Registration Processor database connection issues
kubectl logs -n mosip-system -l app=regproc-worker --tail=100 | grep -i "database"
```

MOSIP database schema များကိုစစ်ဆေးရာတွင် production environment တွင် သတိထားပြီး read-only queries များကိုသာအသုံးပြုသင့်ပါသည်။ Schema ပြောင်းလဲမှုများလိုအပ်ပါက MOSIP deployment scripts များနှင့် version compatibility ကိုအထူးဂရုပြုရန် လိုအပ်ပါသည်။
