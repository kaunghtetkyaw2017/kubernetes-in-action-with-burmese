ဤ command တစ်ခုတည်းဖြင့် application တစ်ခုလုံးကို download လုပ်ပြီး မည်သည့် dependency မှ install လုပ်စရာမလိုဘဲ အလုပ်လုပ်စေနိုင်ခဲ့သည်။ ၎င်းသည် container အတွင်းတွင် သီးခြားခွဲထုတ်၍ အလုပ်လုပ်ခဲ့ခြင်းဖြစ်သည်။

*ပုံ 2.10: busybox image ကို အသုံးပြု၍ container ထဲတွင် echo "Hello world" ကို run ခြင်း*

### အခြား Image များကို Run ခြင်း
အခြား container image များကို run ခြင်းသည်လည်း အလားတူပင်ဖြစ်သည်။ redis:alpine ကဲ့သို့သော image များကို run ရန် command ကို သတ်မှတ်ရန်မလိုဘဲ `docker run redis:alpine` ဟု ရိုက်ထည့်ရုံသာဖြစ်သည်။

### Image Tag များကို နားလည်ခြင်း
Docker image များတွင် `latest`, `buster`, `alpine`, `5.0.7-buster` ကဲ့သို့သော tag များဖြင့် version နှင့် variant များကို ခွဲခြားထားသည်။ tag ကို မသတ်မှတ်ပါက Docker သည် `latest` tag ကို အသုံးပြုသည်ဟု ယူဆသည်။

---

## 2.1.4 Open Container Initiative (OCI) နှင့် Docker အစားထိုးများကို မိတ်ဆက်ခြင်း
Docker သည် process isolation ကို ပံ့ပိုးပေးသည့် တစ်ခုတည်းသော tool မဟုတ်ပါ။

### Open Container Initiative (OCI)
Docker ၏ အောင်မြင်မှုနောက်တွင်၊ OCI သည် container format နှင့် runtime များအတွက် open industry standard များကို ဖန်တီးရန် ပေါ်ပေါက်လာခဲ့သည်။

### Container Runtime Interface (CRI) နှင့် CRI-O
Kubernetes သည် ယခုအခါ CRI မှတစ်ဆင့် container runtime များစွာကို support လုပ်သည်။ CRI-O သည် Docker အတွက် lightweight alternative တစ်ခုဖြစ်ပြီး rkt, runC ကဲ့သို့သော OCI-compliant runtime များကို Kubernetes နှင့်အတူ အသုံးပြုနိုင်စေသည်။

---

## 2.2 Kiada Application ကို Deploy လုပ်ခြင်း
ယခုအခါသင်သည် Docker ကို အသုံးပြု၍ Kiada (Kubernetes in Action Demo Application) ဟုခေါ်သော microservice application တစ်ခုကို တည်ဆောက်ပါမည်။

### 2.2.1 Kiada Suite ကို မိတ်ဆက်ခြင်း
Kiada သည် စာအုပ်ထဲမှ quote များကို ဖော်ပြခြင်း၊ Kubernetes ဆိုင်ရာ မေးခွန်းများမေးခြင်းများ ပြုလုပ်သည့် web application တစ်ခုဖြစ်သည်။

*ပုံ 2.11: Kiada Application ၏ Screenshot*
*ပုံ 2.12: Kiada Suite ၏ Architecture နှင့် လုပ်ဆောင်ပုံ*
*ပုံ 2.13: Plain-text version ၏ လုပ်ဆောင်ပုံ*

### 2.2.2 Application ကို တည်ဆောက်ခြင်း
**Container Image အတွက် Dockerfile ကို ဖန်တီးခြင်း**

App ကို image အဖြစ်ထုပ်ပိုးရန် Dockerfile လိုအပ်ပါသည်။

*Listing 2.1: App အတွက် အခြေခံ Dockerfile*
```dockerfile
FROM node:16
COPY app.js /app.js
COPY html /html
ENTRYPOINT ["node", "app.js"]
```
Container Image ကို တည်ဆောက်ခြင်း

အောက်ပါ command ကို အသုံးပြု၍ image ကို တည်ဆောက်ပြီး kiada:latest ဟု tag ပေးပါ။

Bash

$ docker build -t kiada:latest .
ပုံ 2.14: Dockerfile ကို အသုံးပြု၍ Image တည်ဆောက်ခြင်း

2.2.3 Container ကို Run ခြင်း
Image တည်ဆောက်ပြီးနောက်၊ အောက်ပါ command ဖြင့် container ကို run နိုင်ပါပြီ။

Bash

$ docker run --name kiada-container -p 1234:8080 -d kiada:latest
ပုံ 2.15: Run နေသော Container ၏ ပုံဖော်ချက်

App ကို ဝင်ရောက်ခြင်း

curl သို့မဟုတ် browser ဖြင့် http://localhost:1234 သို့ ဝင်ရောက်ကြည့်ရှုပါ။

Bash

$ curl localhost:1234
2.2.4 Container Image ကို ဖြန့်ဝေခြင်း
Image ကို အခြား computer များတွင် run နိုင်ရန်၊ ၎င်းကို Docker Hub ကဲ့သို့သော registry သို့ push လုပ်ရပါမည်။

Bash

# Image ကို သင်၏ Docker Hub ID ဖြင့် tag ပေးပါ
$ docker tag kiada:latest yourid/kiada:0.1
# Docker Hub သို့ login ဝင်ပါ
$ docker login -u yourid docker.io
# Image ကို push လုပ်ပါ
$ docker push yourid/kiada:0.1
2.2.5 Container ကို ရပ်တန့်ခြင်းနှင့် ဖျက်ခြင်း
Bash

# Container ကို ရပ်ပါ
$ docker stop kiada-container
# Container ကို ဖျက်ပါ
$ docker rm kiada-container
2.3 Container များ၏ အတွင်းပိုင်းကို နားလည်ခြင်း
2.3.1 Process ၏ Environment ကို ပြင်ဆင်ရန် Linux Namespaces ကို အသုံးပြုခြင်း
Linux Namespaces သည် process တစ်ခုချင်းစီကို ကိုယ်ပိုင် system (file system, process ID, network interface စသည်) ရှိသကဲ့သို့ ဖြစ်စေသည်။ ၎င်းသည် container isolation ၏ အဓိက နည်းပညာဖြစ်ပါသည်။

ပုံ 2.16: Network Namespace သည် Process တစ်ခု၏ Network Interface များကို ကန့်သတ်ပုံ
ပုံ 2.17: Process များ Namespace များကို မျှဝေသုံးစွဲပုံ

2.3.2 Run နေသော Container ၏ Environment ကို စူးစမ်းခြင်း
docker exec command ကို အသုံးပြု၍ run နေသော container အတွင်းသို့ ဝင်ရောက်ပြီး ၎င်း၏ environment ကို စူးစမ်းနိုင်ပါသည်။

Bash

$ docker exec -it kiada-container bash
root@...:/# ps aux
ပုံ 2.18: PID Namespace ၏ Process Tree

2.3.3 Process ၏ Resource အသုံးပြုမှုကို Linux Control Groups (cgroups) ဖြင့် ကန့်သတ်ခြင်း
cgroups သည် process တစ်ခု (သို့မဟုတ် container) အသုံးပြုနိုင်သည့် CPU, memory, network bandwidth ကဲ့သို့သော resource များကို ကန့်သတ်ပေးသည်။

Bash

# CPU ကို core 0.5 အထိသာ ကန့်သတ်ခြင်း
$ docker run --cpus="0.5" ...
# Memory ကို 100MB အထိသာ ကန့်သတ်ခြင်း
$ docker run --memory="100m" ...
2.3.4 Container များအကြား Isolation ကို ပိုမိုခိုင်မာစေခြင်း
Namespaces နှင့် cgroups များအပြင်၊ အခြားသော Linux kernel နည်းပညာများသည် container များ၏ security ကို ပိုမိုခိုင်မာစေသည်။

Capabilities: Container တစ်ခုကို kernel ၏ system call အချို့ကိုသာ အသုံးပြုခွင့်ပေးခြင်း။

seccomp: Container တစ်ခု ပြုလုပ်နိုင်သည့် system call များကို အတိအကျ filter လုပ်ခြင်း။

AppArmor နှင့် SELinux: File နှင့် system resource များအပေါ်တွင် ပိုမိုအသေးစိတ်ကျသော access control policy များ ချမှတ်ခြင်း။

2.4 အကျဉ်းချုပ်
ဤအခန်းကို ဖတ်ရှုပြီးနောက် သင်သည် container များ၏ အခြေခံသဘောတရား၊ ၎င်းတို့၏ အလုပ်လုပ်ပုံနှင့် ၎င်းတို့ကို ဖြစ်ပေါ်စေသည့် Linux kernel နည်းပညာများကို နားလည်သွားမည်ဖြစ်သည်။

Container များသည် သီးခြားခွဲထုတ်ထားသော ပုံမှန် process များဖြစ်သည်။

Container များသည် VM များထက် ပေါ့ပါးသော်လည်း isolation အနည်းငယ်နည်းပါးသည်။

Docker သည် container များကို လူကြိုက်များစေခဲ့ပြီး၊ Kubernetes သည် CRI မှတစ်ဆင့် runtime များစွာကို support လုပ်သည်။

Container image များသည် application နှင့် ၎င်း၏ dependency များကို ထုပ်ပိုးထားပြီး၊ layer များဖြင့် ဖွဲ့စည်းထားသည်။

Container များကို Linux kernel ၏ Namespaces, cgroups, Capabilities, seccomp, AppArmor/SELinux ကဲ့သို့သော feature များဖြင့် isolate လုပ်ထားသည်။

နောက်အခန်းတွင် Kubernetes ကို အသုံးပြု၍ container များကို မည်သို့ run ရမည်ကို သင်လေ့လာရပါမည်။
