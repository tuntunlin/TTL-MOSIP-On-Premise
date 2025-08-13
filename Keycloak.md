
# MetalLB က bare metal ပေါ်မှာ LoadBalancer type services တွေကို external IP ပေးနိုင်တယ်
MOSIP Components များအတွက်
MetalLB ကို MOSIP on-premise deployment အတွက် လိုအပ်ပါသည်၊ အထူးသဖြင့် LoadBalancer type services တွေအသုံးပြုမယ်ဆိုရင်။
MetalLB လိုအပ်ခြင်းအကြောင်းရင်းများ
LoadBalancer Services အတွက် External IP ပေးနိုင်ရန်:
On-premise Kubernetes clusters တွေမှာ cloud provider load balancers မရှိပါ
MetalLB က bare metal ပေါ်မှာ LoadBalancer type services တွေကို external IP ပေးနိုင်တယ်
MOSIP Components များအတွက်:
Keycloak (Authentication)
Registration Processor
IDA (Identity Authentication)
Resident Services
အခြား external access လိုအပ်တဲ့ services တွေ

### MetalLB Install လုပ်ခြင်း
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### 1. IP Address Pool သတ်မှတ်ခြင်း
```sh
kubectl apply -f - <<EOF
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: mosip-ip-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.100-192.168.1.200  # သင့်ရဲ့ network နှင့်ကိုက်ညီသော IP range
  autoAssign: true
EOF
```
### 2. L2 Advertisement သတ်မှတ်ခြင်း
```sh
kubectl apply -f - <<EOF
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: mosip-l2-advertisement
  namespace: metallb-system
spec:
  ipAddressPools:
  - mosip-ip-pool
```
### 3. MOSIP Services များကို LoadBalancer အဖြစ်ပြောင်းလဲခြင်း
```sh
# Keycloak service
kubectl patch svc keycloak -n mosip-system -p '{"spec":{"type":"LoadBalancer"}}'

# Registration Processor
kubectl patch svc regproc -n mosip-system -p '{"spec":{"type":"LoadBalancer"}}'

# ID Authentication Service
kubectl patch svc ida -n mosip-system -p '{"spec":{"type":"LoadBalancer"}}'
```
