# MOSIP Core Modules Deployment on a Cluster

MOSIP (Modular Open Source Identity Platform) ကို cluster တစ်ခုပေါ်တွင် deploy လုပ်ရန် အဆင့်ဆင့်လမ်းညွှန်ချက်များ:

## ပြင်ဆင်မှုများ

1. **Cluster Setup**:
   - Kubernetes cluster (v1.19+) ကို ပြင်ဆင်ပါ
   - Helm (v3.2.0+) install လုပ်ပါ
   - Ingress controller (Nginx/Traefik) configure လုပ်ပါ

2. **MOSIP Configuration**:
   - MOSIP GitHub repository မှ `deployment/v3` directory ကို clone လုပ်ပါ
   - `env` directory တွင် သင့်လျော်သော configuration files များကို ပြင်ဆင်ပါ

## Deployment အဆင့်များ

1. **Namespace တည်ဆောက်ခြင်း**:
   ```bash
   kubectl create namespace mosip
   ```

2. **Helm Charts တပ်ဆင်ခြင်း**:
   ```bash
   helm repo add mosip https://mosip.github.io/mosip-helm
   helm repo update
   ```

3. **Core Modules တပ်ဆင်ခြင်း**:

   a. **Config Server**:
   ```bash
   helm -n mosip install mosip-config-server mosip/mosip-config-server -f env/config-server-values.yaml
   ```

   b. **Kernel**:
   ```bash
   helm -n mosip install mosip-kernel mosip/mosip-kernel -f env/kernel-values.yaml
   ```

   c. **Admin Services**:
   ```bash
   helm -n mosip install mosip-admin mosip/mosip-admin -f env/admin-values.yaml
   ```

   d. **Pre-Registration**:
   ```bash
   helm -n mosip install mosip-prereg mosip/mosip-prereg -f env/prereg-values.yaml
   ```

   e. **Registration Processor**:
   ```bash
   helm -n mosip install mosip-regproc mosip/mosip-regproc -f env/regproc-values.yaml
   ```

   f. **ID Repository**:
   ```bash
   helm -n mosip install mosip-idrepo mosip/mosip-idrepo -f env/idrepo-values.yaml
   ```

4. **Database Initialization**:
   ```bash
   helm -n mosip install mosip-init mosip/mosip-init -f env/init-values.yaml
   ```

## Post-Deployment စစ်ဆေးမှုများ

1. Pod status များကို စစ်ဆေးပါ:
   ```bash
   kubectl -n mosip get pods
   ```

2. Services များကို စစ်ဆေးပါ:
   ```bash
   kubectl -n mosip get svc
   ```

3. Ingress routes များကို စစ်ဆေးပါ:
   ```bash
   kubectl -n mosip get ingress
   ```

## အရေးကြီးသော မှတ်ချက်များ

- MOSIP deployment အတွက် minimum resource requirements များကို သေချာစွာ ပြည့်မီပါစေ
- Production environment အတွက် proper TLS certificates များ configure လုပ်ပါ
- Monitoring နှင့် logging solutions (Prometheus, Grafana, ELK) များကို ထည့်သွင်းစဉ်းစားပါ
- Regular backups များ configure လုပ်ပါ

MOSIP documentation တွင် အသေးစိတ်အချက်အလက်များကို ဆက်လက်ဖတ်ရှုနိုင်ပါသည်။
