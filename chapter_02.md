# အခန်း (၂) Container များကို နားလည်ခြင်း

**ဤအခန်းတွင် လေ့လာရမည့်အကြောင်းအရာများ**

* Container ဆိုသည်မှာ အဘယ်နည်း။
* Container နှင့် Virtual Machine (VM) တို့၏ ကွာခြားချက်များ။
* Docker ကို အသုံးပြု၍ Container Image တစ်ခုကို ဖန်တီးခြင်း၊ အသုံးပြုခြင်း နှင့် မျှဝေခြင်း။
* Container များကို ဖြစ်ပေါ်စေသည့် Linux Kernel ၏ နည်းပညာများ။

Kubernetes သည် အဓိကအားဖြင့် container များအတွင်းတွင် အလုပ်လုပ်သော application များကို စီမံခန့်ခွဲပါသည်။ ထို့ကြောင့် Kubernetes ကို အသေးစိတ်မလေ့လာမီ၊ container ဆိုသည်မှာ အဘယ်နည်းကို ကောင်းစွာနားလည်ထားရန် အလွန်အရေးကြီးပါသည်။ ဤအခန်းတွင် ပုံမှန် Kubernetes အသုံးပြုသူတစ်ဦး သိရှိထားသင့်သော Linux container များ၏ အခြေခံသဘောတရားများကို ရှင်းလင်းတင်ပြပါမည်။

## 2.1 Container များအား မိတ်ဆက်ခြင်း

အခန်း (၁) တွင် သင်လေ့လာခဲ့သည့်အတိုင်း၊ operating system တစ်ခုတည်းတွင် အလုပ်လုပ်နေသော microservice များသည် တစ်ခုနှင့်တစ်ခု မတူညီသော သို့မဟုတ် ပဋိပက္ခဖြစ်နိုင်သော library version များကို လိုအပ်နိုင်ပါသည်။

System တစ်ခုတွင် application အနည်းငယ်သာရှိပါက၊ application တစ်ခုချင်းစီအတွက် သီးသန့် virtual machine (VM) တစ်လုံးစီ ခွဲဝေချထားပေးပြီး ကိုယ်ပိုင် operating system တွင် အလုပ်လုပ်စေခြင်း нь ပုံမှန်ဖြစ်ပါသည်။ သို့သော် microservice များ သေးငယ်လာပြီး အရေအတွက်များပြားလာသောအခါ၊ hardware ကုန်ကျစရိတ်ကို ထိန်းချုပ်ရန်နှင့် အရင်းအမြစ်များ မဖြုန်းတီးစေရန်အတွက် microservice တစ်ခုချင်းစီကို VM တစ်လုံးစီပေးရန် မဖြစ်နိုင်တော့ပါ။

၎င်းသည် hardware အရင်းအမြစ်များ ဖြုန်းတီးခြင်းမျှသာမက၊ VM တစ်ခုချင်းစီကို သီးခြားစီ configure လုပ်ပြီး စီမံခန့်ခွဲရန် လိုအပ်သောကြောင့် VM အရေအတွက်များပြားလာသည်နှင့်အမျှ ဝန်ထမ်းအင်အားနှင့် ပိုမိုရှုပ်ထွေးသော automation system များလည်း လိုအပ်လာပါသည်။ microservice architecture များသို့ ပြောင်းလဲလာခြင်းကြောင့် VM များအတွက် အခြားရွေးချယ်စရာတစ်ခု လိုအပ်လာပြီး၊ ထိုအရာမှာ Container များပင် ဖြစ်သည်။

### 2.1.1 Container များကို Virtual Machine များနှင့် နှိုင်းယှဉ်ခြင်း

ယခုအခါတွင် development နှင့် operations အဖွဲ့များသည် microservice တစ်ခုချင်းစီ၏ environment ကို သီးခြားခွဲထုတ် (isolate) ရန် virtual machine များအစား container များကို ပိုမိုအသုံးပြုလာကြသည်။ Container များသည် service အများအပြားကို host computer တစ်လုံးတည်းတွင် အလုပ်လုပ်စေနိုင်ပြီး၊ တစ်ခုနှင့်တစ်ခု သီးခြားစီဖြစ်နေစေရန် ထိန်းသိမ်းပေးနိုင်သည်။ ၎င်းသည် VM များနှင့် ဆင်တူသော်လည်း overhead (အပိုဝန်) အလွန်နည်းပါးပါသည်။

VM တစ်ခုချင်းစီသည် သီးခြား operating system တစ်ခုနှင့် system process အများအပြားကို run ရသော်လည်း၊ container အတွင်းရှိ process သည် host computer ၏ operating system ထဲတွင်ပင် အလုပ်လုပ်ပါသည်။ operating system တစ်ခုတည်းသာရှိသောကြောင့် system process များ ထပ်ซ้อนနေခြင်းမရှိပါ။ application process များအားလုံးသည် OS တစ်ခုတည်းတွင် အလုပ်လုပ်နေသော်လည်း၊ ၎င်းတို့၏ environment များကို သီးခြားခွဲထုတ်ထားပါသည်။ သို့သော် ၎င်းသည် VM များလောက်တော့ isolation မကောင်းပါ။

**Container နှင့် Virtual Machine တို့၏ Overhead (အပိုဝန်) နှိုင်းယှဉ်ချက်**

VM များနှင့် နှိုင်းယှဉ်လျှင် container များသည် အပို resource pool သို့မဟုတ် OS-level process များ မလိုအပ်သောကြောင့် အလွန်ပေါ့ပါးပါသည်။ VM တစ်လုံးသည် ကိုယ်ပိုင် system process များကို run ရန် အပို computing resource များ လိုအပ်သော်လည်း၊ container သည် host OS အတွင်းရှိ သီးခြား process တစ်ခုမျှသာဖြစ်ပြီး application က သုံးစွဲသည့် resource များကိုသာ အသုံးပြုသည်။ ၎င်းတို့တွင် overhead လုံးဝနီးပါးမရှိပါ။

**ပုံ 2.1: Application အုပ်စုများကို ခွဲထုတ်ရန် VM များအသုံးပြုခြင်း နှင့် Application တစ်ခုချင်းစီကို ခွဲထုတ်ရန် Container များအသုံးပြုခြင်း**

VM များ၏ resource overhead ကြောင့် application အများအပြားကို VM တစ်ခုထဲတွင် စုပေါင်းထည့်သွင်းလေ့ရှိသည်။ သို့သော် container များတွင် overhead မရှိသောကြောင့် application တစ်ခုချင်းစီအတွက် သီးခြား container တစ်ခု ဖန်တီးနိုင်သည်။ အမှန်မှာ၊ container တစ်ခုတည်းတွင် application တစ်ခုထက်ပို၍ မ run သင့်ပါ။ Kubernetes အပါအဝင် container ဆိုင်ရာ software အားလုံးသည် container တစ်ခုတွင် application တစ်ခုသာရှိသည်ဟူသော အခြေခံမူပေါ်တွင် ဒီဇိုင်းပြုလုပ်ထားပါသည်။

**Container နှင့် Virtual Machine တို့၏ Start-up Time (စတင်ချိန်) နှိုင်းယှဉ်ချက်**

overhead နည်းပါးသည့်အပြင်၊ container များသည် application process ကိုသာ စတင်ရန်လိုအပ်သောကြောင့် VM များထက် application ကို ပိုမိုမြန်ဆန်စွာ စတင်နိုင်သည်။ VM အသစ်တစ်ခုကို boot တက်သကဲ့သို့ အပို system process များကို စတင်ရန်မလိုအပ်ပါ။

**Container နှင့် Virtual Machine တို့၏ Isolation (သီးခြားခွဲထုတ်ခြင်း) နှိုင်းယှဉ်ချက်**

Resource အသုံးပြုမှုတွင် container များက သာလွန်သော်လည်း အားနည်းချက်တစ်ခုရှိပါသည်။ VM များသည် ကိုယ်ပိုင် kernel တစ်ခုစီဖြင့် အလုပ်လုပ်သောကြောင့် လုံးဝ သီးခြားဖြစ်နေသော်လည်း၊ container များအားလုံးသည် host OS ၏ kernel တစ်ခုတည်းကို မျှဝေသုံးစွဲကြသည်။

**ပုံ 2.2: VM နှင့် Container အတွင်းရှိ App များ Hardware ကို အသုံးပြုပုံ**

> **မှတ်ချက်**: Hypervisor နှစ်မျိုးရှိသည်။ Type 1 သည် host OS မလိုအပ်သော်လည်း Type 2 က လိုအပ်သည်။

**ပုံ 2.3: Application များကို Bare Metal၊ VM နှင့် Container များတွင် အလုပ်လုပ်ပုံ ကွာခြားချက်**

ပုံတွင် မြင်တွေ့ရသည့်အတိုင်း၊ container များသည် kernel တစ်ခုတည်းကို မျှဝေသုံးစွဲသော်လည်း kernel ၏ နည်းပညာများက ၎င်းတို့ကို တစ်ခုနှင့်တစ်ခု သီးခြားဖြစ်နေစေရန် ကူညီပေးသည်။ Application တစ်ခုချင်းစီသည် ကိုယ်ပိုင် OS တွင် အလုပ်လုပ်နေသကဲ့သို့ ခံစားရသည်။

**Container Isolation ၏ Security သက်ရောက်မှုများ**

VM များ၏ အဓိကအားသာချက်မှာ kernel တစ်ခုစီဖြင့် လုံးဝသီးခြားဖြစ်နေခြင်းဖြစ်ပြီး၊ container များက kernel တစ်ခုတည်းကို မျှဝေသုံးစွဲသောကြောင့် security risk ရှိနိုင်သည်။ kernel တွင် bug တစ်ခုရှိပါက container တစ်ခုမှ application သည် အခြား container များ၏ memory ကို ဝင်ရောက်ဖတ်ရှုနိုင်သည်။ ထို့အပြင်၊ container တစ်ခုက memory အားလုံးကို အလွန်အကျွံသုံးစွဲပါက အခြား container များကို ထိခိုက်နိုင်သည်။ Kubernetes တွင်မူ node များပေါ်၌ swap ကို disable လုပ်ထားသောကြောင့် ဤပြဿနာကို ကာကွယ်ထားသည်။

**Container နှင့် VM တို့ကို ဖြစ်ပေါ်စေသော နည်းပညာများ**

VM များကို CPU ၏ virtualization support နှင့် hypervisor software တို့ဖြင့် ဖြစ်ပေါ်စေပြီး၊ container များကိုမူ Linux kernel ကိုယ်တိုင်က ပံ့ပိုးပေးသော နည်းပညာများဖြင့် ဖြစ်ပေါ်စေပါသည်။

### 2.1.2 Docker Container Platform ကို မိတ်ဆက်ခြင်း

Container နည်းပညာများသည် Docker ကြောင့် ကျယ်ပြန့်စွာ လူသိများလာခဲ့သည်။ Docker သည် application နှင့် ၎င်း၏လိုအပ်သော library များ၊ dependency များအားလုံးကို Docker-enabled computer တိုင်းတွင် အလွယ်တကူ deploy လုပ်နိုင်သည့် package တစ်ခုအဖြစ် ထုပ်ပိုးခြင်းကို ရိုးရှင်းလွယ်ကူစေခဲ့သည်။

**Container, Image နှင့် Registry တို့ကို မိတ်ဆက်ခြင်း**

Docker သည် application များကို ထုပ်ပိုးခြင်း (packaging)၊ ဖြန့်ဝေခြင်း (distributing) နှင့် အသုံးပြုခြင်း (running) တို့အတွက် platform တစ်ခုဖြစ်သည်။

**ပုံ 2.4: Docker ၏ အဓိက သဘောတရားသုံးခု - Image, Registry, Container**

* **Images (Image များ)**: Container image သည် သင်၏ application နှင့် ၎င်း၏ environment ကို ထုပ်ပိုးထားသည့် အရာဖြစ်သည်။ ၎င်းတွင် application အတွက်လိုအပ်သော file system နှင့် application ကို မည်သို့ run ရမည် စသည့် metadata များ ပါဝင်သည်။
* **Registries (Registry များ)**: Registry သည် container image များကို သိမ်းဆည်းထားသည့် repository ဖြစ်ပြီး လူအများအကြား image များကို ဖလှယ်နိုင်စေသည်။
* **Containers (Container များ)**: Container ကို image မှတစ်ဆင့် ဖန်တီးသည်။ run နေသော container သည် host OS အတွင်းရှိ သီးခြား process တစ်ခုဖြစ်ပြီး၊ ၎င်း၏ environment ကို host နှင့် အခြား process များမှ ခွဲထုတ်ထားသည်။

**Container Image တစ်ခုကို တည်ဆောက်ခြင်း၊ ဖြန့်ဝေခြင်းနှင့် အသုံးပြုခြင်း**

Developer တစ်ဦးသည် container image ကို တည်ဆောက်ပြီး registry သို့ push (upload) လုပ်သည်။ ထို့နောက် အခြားသူတစ်ဦးက ထို image ကို pull (download) လုပ်ပြီး မည်သည့် Docker-enabled computer တွင်မဆို run နိုင်သည်။

* **ပုံ 2.5: Container image တည်ဆောက်ခြင်း**
* **ပုံ 2.6: Container image ကို registry သို့ upload လုပ်ခြင်း**
* **ပုံ 2.7: Container ကို အခြား computer တွင် run ခြင်း**

**Image Layers (Image အလွှာများ) ကို နားလည်ခြင်း**

Container image များသည် VM image များကဲ့သို့ တစ်ခုတည်းသော ကြီးမားသည့် file မဟုတ်ဘဲ၊ မျှဝေသုံးစွဲနိုင်သော layer (အလွှာ) များဖြင့် ဖွဲ့စည်းထားသည်။ ၎င်းကြောင့် image များကို download လုပ်ရာတွင် ပိုမိုမြန်ဆန်ပြီး storage နေရာကိုလည်း ချွေတာနိုင်သည်။

**ပုံ 2.8: Container များ Image Layer များကို မျှဝေသုံးစွဲပုံ**

Container များသည် filesystem များကို **Copy-on-Write (CoW)** နည်းလမ်းဖြင့် သီးခြားခွဲထုတ်ထားသည်။ container တစ်ခုသည် မျှဝေထားသော file ကို ပြင်ဆင်သည့်အခါ၊ ထို file ၏ မိတ္တူကို ၎င်း၏ ကိုယ်ပိုင် read/write layer တွင် ဖန်တီးပြီး ပြင်ဆင်သည်။ ဤသို့ဖြင့် အခြား container များက ထိုပြောင်းလဲမှုကို မမြင်ရပါ။

> **သတိပေးချက်**: File permission သို့မဟုတ် ownership ပြောင်းလဲခြင်းကဲ့သို့သော လုပ်ဆောင်ချက်များသည် file တစ်ခုလုံးကို read/write layer တွင် copy ကူးစေပြီး image အရွယ်အစားကို ကြီးမားစေနိုင်သည်။

**Container Image များ၏ Portability (သယ်ဆောင်နိုင်စွမ်း) ကန့်သတ်ချက်များ**

Container များသည် ကိုယ်ပိုင် kernel မရှိသောကြောင့်၊ containerized application တစ်ခုသည် သီးခြား kernel version သို့မဟုတ် module တစ်ခုကို လိုအပ်ပါက၊ ၎င်းသည် computer တိုင်းတွင် အလုပ်လုပ်နိုင်မည်မဟုတ်ပါ။

**ပုံ 2.9: သီးခြား kernel feature များ လိုအပ်သော container သည် နေရာတိုင်းတွင် အလုပ်မလုပ်နိုင်ပါ**

### 2.1.3 Docker ကို Install လုပ်ပြီး "Hello World" Container ကို Run ကြည့်ခြင်း

**Docker ကို Install လုပ်ခြင်း**

Linux computer တွင် Docker ကို တိုက်ရိုက် install လုပ်ခြင်းသည် အကောင်းဆုံးဖြစ်သည်။ macOS သို့မဟုတ် Windows အသုံးပြုသူများအတွက် Docker Desktop application က လိုအပ်သော Linux VM ကို အလိုအလျောက် setup လုပ်ပေးပါလိမ့်မည်။ အသေးစိတ် install လုပ်နည်းကို http://docs.docker.com/install တွင် လေ့လာပါ။

**"Hello World" Container ကို Run ခြင်း**

Docker install ပြီးနောက်၊ `docker` CLI tool ကို အသုံးပြု၍ `busybox` image မှ "Hello World" ကို run ကြည့်ပါ။

```bash
$ docker run busybox echo "Hello World"
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
...
Status: Downloaded newer image for busybox:latest
Hello World
