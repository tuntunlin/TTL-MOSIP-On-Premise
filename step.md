# MOSIP Cluster တည်ဆောက်ပြီးနောက် ဆက်လုပ်ရမည့်အဆင့်များ

MOSIP cluster ကိုအောင်မြင်စွာ deploy လုပ်ပြီးနောက် အောက်ပါအဆင့်များကို ဆက်လက်ဆောင်ရွက်ရန် လိုအပ်ပါသည်။

## 1. စနစ်စစ်ဆေးခြင်း (System Verification)

```bash
# Pod အားလုံးအလုပ်လုပ်နေကြောင်းစစ်ဆေးပါ
kubectl get pods -n mosip-system

# Service များကိုစစ်ဆေးပါ
kubectl get svc -n mosip-system

# Ingress ရှိပါက စစ်ဆေးပါ
kubectl get ingress -n mosip-system
```

## 2. MOSIP Module များ Configuration

```bash
# Config server မှ configuration များကိုစစ်ဆေးပါ
kubectl exec -it $(kubectl get pods -n mosip-system -l app=config-server -o jsonpath='{.items[0].metadata.name}') -n mosip-system -- cat /config/application.properties

# Kernel properties များကိုပြင်ဆင်ပါ
kubectl edit configmap kernel-properties -n mosip-system
```

## 3. Database Initialization

```bash
# MOSIP database schema များကိုစစ်ဆေးပါ
kubectl exec -it $(kubectl get pods -n mosip-system -l app=mosip-db -o jsonpath='{.items[0].metadata.name}') -n mosip-system -- psql -U mosipuser -d mosip_kernel
```

## 4. Keycloak Setup

```bash
# Keycloak admin credentials ရယူပါ
kubectl get secret keycloak-secret -n mosip-system -o jsonpath='{.data}' | jq 'map_values(@base64d)'

# Keycloak realm import လုပ်ပါ
kubectl exec -it $(kubectl get pods -n mosip-system -l app=keycloak -o jsonpath='{.items[0].metadata.name}') -n mosip-system -- /opt/keycloak/bin/kc.sh import --file /opt/keycloak/data/import/mosip-realm.json
```

## 5. Network Configuration

```bash
# Ingress controller ကိုစစ်ဆေးပါ
kubectl get ingress -n mosip-system

# DNS records များကိုသတ်မှတ်ပါ
# (MOSIP modules များအတွက် hostname များ)
```

## 6. Monitoring နှင့် Logging Setup

```bash
# Prometheus နှင့် Grafana deploy လုပ်ပါ
helm install prometheus-stack prometheus-community/kube-prometheus-stack -n monitoring

# EFK stack (Elasticsearch, Fluentd, Kibana) တပ်ဆင်ပါ
helm install efk elastic/elasticsearch -n logging
```

## 7. Backup Strategy ချမှတ်ခြင်း

```bash
# Velero backup tool တပ်ဆင်ပါ
velero install --provider aws --plugins velero/velero-plugin-for-aws:v1.0.0 --bucket mosip-backup --secret-file ./credentials-velero --use-volume-snapshots=false --backup-location-config region=minio,s3ForcePathStyle="true",s3Url=http://minio.mosip-system.svc:9000
```

## 8. Security Hardening

```bash
# Network policies များကိုသတ်မှတ်ပါ
kubectl apply -f https://raw.githubusercontent.com/mosip/mosip-infra/master/security/network-policies.yaml

# Pod security policies များကိုသတ်မှတ်ပါ
kubectl apply -f https://raw.githubusercontent.com/mosip/mosip-infra/master/security/psp.yaml
```

## 9. Testing နှင့် Validation

```bash
# MOSIP API များကိုစမ်းသပ်ပါ
curl -X GET "http://$(kubectl get svc -n mosip-system regproc -o jsonpath='{.status.loadBalancer.ingress[0].ip}')/v1/registrationprocessor/status"

# Postman collection များဖြင့်စမ်းသပ်ပါ
```

## 10. Production Readiness

```bash
# Horizontal Pod Autoscaler များသတ်မှတ်ပါ
kubectl autoscale deployment kernel-auth-service -n mosip-system --cpu-percent=80 --min=2 --max=10

# Resource quotas များသတ်မှတ်ပါ
kubectl apply -f https://raw.githubusercontent.com/mosip/mosip-infra/master/resource-quotas.yaml
```

MOSIP cluster ကို production environment တွင်အသုံးပြုမည်ဆိုပါက အောက်ပါတို့ကိုထပ်မံစစ်ဆေးပါ:
- High availability configuration
- Disaster recovery plan
- Regular backup schedule
- Security audit
- Performance benchmarking


1. Prerequisite Components
Kubernetes Cluster Dependencies

    Ingress Controller (Nginx, Traefik, etc.)

    Cert Manager (TLS certificates management အတွက်)

    External DNS (optional, external DNS records management အတွက်)

Storage

    Persistent Volume Provisioner (Rook Ceph, Longhorn, သို့မဟုတ် cloud provider specific storage class)

Monitoring

    Prometheus Operator (monitoring stack အတွက်)

    Grafana (visualization အတွက်)

2. MOSIP Core Dependencies
Database Layer

    PostgreSQL (သို့မဟုတ် cloud managed database service)

    Keycloak (identity and access management အတွက်)

Messaging

    Kafka (event streaming platform)

    Zookeeper (Kafka အတွက် coordination service)

Cache

    Redis (caching layer အတွက်)

3. MOSIP Core Modules (အစဉ်လိုက်)

    Config Server - MOSIP configuration management

    Kernel Services - Core utility services

    Registration Processor - Registration data processing

    ID Repository - Biometric and demographic data storage

    ID Authentication - Authentication services

    Partner Management - Partner onboarding and management

    Admin Services - System administration

4. Deployment အဆင့်ဆင့်

    Prerequisite Helm charts တင်ပါ

    MOSIP custom Helm charts တင်ပါ

    Configuration values များကို override လုပ်ပါ

    Namespace များ create လုပ်ပါ

    Core modules များကို dependency order အလိုက် deploy လုပ်ပါ

MOSIP deployment သည် ရှုပ်ထွေးသော process တစ်ခုဖြစ်ပြီး environment ပေါ်မူတည်၍ ကွဲပြားမှုများ ရှိနိုင်ပါသည်။ MOSIP official documentation တွင် အတိအကျသော deployment guide များကို ရှာဖွေဖတ်ရှုသင့်ပါသည်။
