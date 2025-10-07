# အခန်း ၃ သင်၏ ပထမဆုံး Application ကို Deploy လုပ်ခြင်း

ဤအခန်းသည် သင့်အား local single-node development Kubernetes cluster တစ်ခုကို run ရန် သို့မဟုတ် cloud တွင် သင့်တော်သော၊ managed multi-node cluster တစ်ခုကို setup လုပ်ရန် ပြသရန် ရည်ရွယ်ပါသည်။ သင်၏ cluster run ပြီးသည်နှင့်၊ ယခင်အခန်းတွင် သင်ဖန်တီးခဲ့သော container ကို run ရန် ၎င်းကို အသုံးပြုပါမည်။

**ဤအခန်းတွင် ပါဝင်သောအကြောင်းအရာများ**

*   သင်၏ laptop တွင် local Kubernetes cluster တစ်ခု run ခြင်း
*   Google Kubernetes Engine တွင် cluster တစ်ခု setup လုပ်ခြင်း
*   Amazon Elastic Kubernetes Service တွင် cluster တစ်ခု setup လုပ်ခြင်း
*   kubectl command-line tool ကို setup လုပ်ပြီး အသုံးပြုခြင်း
*   Kubernetes တွင် application တစ်ခုကို deploy လုပ်ပြီး ကမ္ဘာတစ်ဝှမ်းတွင် ရရှိနိုင်အောင် ပြုလုပ်ခြင်း
*   Application ကို horizontally scale လုပ်ခြင်း

> **မှတ်ချက်**
> သင်သည် ဤအခန်းအတွက် code file များကို https://github.com/luksa/kubernetes-in-action-2nd-edition/tree/master/Chapter03 တွင် ရှာဖွေနိုင်ပါသည်။

## ၃.၁ Kubernetes Cluster တစ်ခုကို Deploy လုပ်ခြင်း

စုံလင်သော၊ multi-node Kubernetes cluster တစ်ခုကို setup လုပ်ခြင်းသည် Linux နှင့် network administration ကို အကျွမ်းတဝင်မရှိပါက ရိုးရှင်းသော အလုပ်မဟုတ်ပါ။ သင့်တော်သော Kubernetes installation တစ်ခုသည် physical သို့မဟုတ် virtual machine အများအပြားကို ဖြန့်ကျက်ထားပြီး cluster ရှိ container များအားလုံး အချင်းချင်း ဆက်သွယ်နိုင်ရန် သင့်တော်သော network setup လိုအပ်ပါသည်။

သင်သည် Kubernetes ကို သင်၏ laptop computer၊ သင်၏ အဖွဲ့အစည်း၏ infrastructure တွင် သို့မဟုတ် cloud provider များ (Google Compute Engine, Amazon EC2, Microsoft Azure, စသည်တို့) မှ ပံ့ပိုးပေးသော virtual machine များတွင် install လုပ်နိုင်ပါသည်။ တစ်နည်းအားဖြင့်၊ cloud provider အများစုသည် ယခုအခါ managed Kubernetes service များကို ပေးဆောင်နေပြီး၊ installation နှင့် management ၏ အလုပ်ရှုပ်ခြင်းမှ သင့်ကို ကယ်တင်ပေးပါသည်။ အကြီးဆုံး cloud provider များ ပေးဆောင်သည့်အရာများ၏ အကျဉ်းချုပ်မှာ ဤသို့ဖြစ်သည်-

*   Google သည် GKE - Google Kubernetes Engine ကို ပေးသည်၊
*   Amazon တွင် EKS - Amazon Elastic Kubernetes Service ရှိသည်၊
*   Microsoft တွင် AKS - Azure Kubernetes Service ရှိသည်၊
*   IBM တွင် IBM Cloud Kubernetes Service ရှိသည်၊
*   Alibaba သည် Alibaba Cloud Container Service ကို ပံ့ပိုးပေးသည်။

Kubernetes ကို install လုပ်ပြီး manage လုပ်ခြင်းသည် ၎င်းကို အသုံးပြုခြင်းထက် များစွာ ပိုမိုခက်ခဲပါသည်၊ အထူးသဖြင့် သင်သည် ၎င်း၏ architecture နှင့် လုပ်ဆောင်ပုံကို အကျွမ်းတဝင် မဖြစ်မချင်း ပိုဆိုးပါသည်။ ဤအကြောင်းကြောင့်၊ ကျွန်ုပ်တို့သည် အလုပ်လုပ်သော Kubernetes cluster တစ်ခု ရရှိရန် အလွယ်ကူဆုံးနည်းလမ်းများဖြင့် စတင်ပါမည်။ သင်၏ local computer တွင် single-node Kubernetes cluster တစ်ခုကို run ရန် နည်းလမ်းများစွာကို သင်လေ့လာရမည်ဖြစ်ပြီး Google Kubernetes Engine (GKE) တွင် run နေသော hosted cluster တစ်ခုကို မည်သို့အသုံးပြုရမည်ကို လေ့လာရမည်ဖြစ်သည်။

တတိယရွေးချယ်မှုမှာ kubeadm tool ကို အသုံးပြု၍ cluster တစ်ခု install လုပ်ခြင်းဖြစ်ပြီး၊ ၎င်းကို နောက်ဆက်တွဲ B တွင် ရှင်းပြထားပါသည်။ ထိုနေရာရှိ သင်ခန်းစာသည် virtual machine များကို အသုံးပြု၍ three-node Kubernetes cluster တစ်ခုကို မည်သို့ setup လုပ်ရမည်ကို ပြသပါမည်။ သို့သော် သင်သည် Kubernetes ကို အသုံးပြုခြင်းနှင့် အကျွမ်းတဝင်ဖြစ်ပြီးမှသာ ၎င်းကို စမ်းကြည့်လိုပေမည်။ အခြားရွေးချယ်စရာများစွာလည်း ရှိသော်လည်း၊ ၎င်းတို့သည် ဤစာအုပ်၏ ဘောင်ပြင်ပတွင် ရှိနေပါသည်။ ပိုမိုလေ့လာရန် kubernetes.io website ကို ကိုးကားပါ။

အကယ်၍ သင်သည် အခြားသူတစ်ဦးမှ deploy လုပ်ထားသော ရှိပြီးသား cluster တစ်ခုသို့ access ရရှိထားပါက၊ သင်သည် ဤအပိုင်းကို ကျော်သွားနိုင်ပြီး Kubernetes cluster များနှင့် မည်သို့ အပြန်အလှန်ဆက်သွယ်ရမည်ကို သင်လေ့လာရမည့် အပိုင်း ၃.၂ သို့ ဆက်သွားနိုင်ပါသည်။

### ၃.၁.၁ Docker Desktop တွင် ပါဝင်သော Kubernetes Cluster ကို အသုံးပြုခြင်း

အကယ်၍ သင်သည် macOS သို့မဟုတ် Windows ကို အသုံးပြုပါက၊ ယခင်အခန်းရှိ လေ့ကျင့်ခန်းများကို run ရန် Docker Desktop ကို install လုပ်ထားပြီး ဖြစ်နိုင်ပါသည်။ ၎င်းတွင် သင်သည် ၎င်း၏ Settings dialog box မှတစ်ဆင့် enable လုပ်နိုင်သော single-node Kubernetes cluster တစ်ခု ပါဝင်ပါသည်။ ဤသည်မှာ သင်၏ Kubernetes ခရီးကို စတင်ရန် အလွယ်ကူဆုံးနည်းလမ်း ဖြစ်နိုင်သော်လည်း၊ Kubernetes ၏ version သည် နောက်ပိုင်းတွင် ဖော်ပြထားသော အခြားရွေးချယ်စရာများကို အသုံးပြုသည့်အခါကဲ့သို့ နောက်ဆုံးပေါ် မဟုတ်နိုင်ကြောင်း သတိပြုပါ။

> **မှတ်ချက်**
> နည်းပညာအရ cluster မဟုတ်သော်လည်း၊ Docker Desktop မှ ပံ့ပိုးပေးသော single-node Kubernetes system သည် ဤစာအုပ်တွင် ဆွေးနွေးထားသော အကြောင်းအရာအများစုကို လေ့လာရန် လုံလောက်သင့်ပါသည်။ လေ့ကျင့်ခန်းတစ်ခုတွင် multi-node cluster လိုအပ်သည့်အခါ၊ ကျွန်ုပ် ထောက်ပြပါမည်။

**Docker Desktop တွင် Kubernetes ကို Enable လုပ်ခြင်း**

Docker Desktop သည် သင်၏ computer တွင် install လုပ်ထားပြီးဖြစ်သည်ဟု ယူဆပါက၊ သင်သည် system tray ရှိ whale icon ကို click နှိပ်ပြီး Settings dialog box ကို ဖွင့်ခြင်းဖြင့် Kubernetes cluster ကို စတင်နိုင်ပါသည်။ Kubernetes tab ကို click နှိပ်ပြီး Enable Kubernetes checkbox ကို select လုပ်ထားကြောင်း သေချာပါစေ။ Control Plane ကို ဖွဲ့စည်းသည့် component များသည် Docker container များအဖြစ် run သော်လည်း၊ သင်သည် `docker ps` command ကို ခေါ်သည့်အခါ run နေသော container များစာရင်းတွင် ၎င်းတို့ကို မပြပါ။ ၎င်းတို့ကို ပြသရန်၊ Show system containers checkbox ကို select လုပ်ပါ။

> **မှတ်ချက်**
> Cluster ၏ ကနဦး installation သည် Kubernetes component များအတွက် container image အားလုံးကို download လုပ်ရမည်ဖြစ်သောကြောင့် မိနစ်အနည်းငယ် ကြာနိုင်ပါသည်။

*ပုံ ၃.၁ Windows အတွက် Docker Desktop ရှိ Settings dialog box*

သင်သည် cluster ကို reset လုပ်ပြီး ၎င်းတွင် deploy လုပ်ထားသော object အားလုံးကို ဖယ်ရှားလိုသည့်အခါ Reset Kubernetes Cluster ခလုတ်ကို သတိရပါ။

**System ကို မြင်ယောင်ကြည့်ခြင်း**

Kubernetes cluster ကို ဖွဲ့စည်းသည့် component အမျိုးမျိုးသည် Docker Desktop တွင် မည်သည့်နေရာတွင် run သည်ကို နားလည်ရန်၊ အောက်ပါပုံကို ကြည့်ပါ။

*ပုံ ၃.၂ Docker Desktop တွင် run နေသော Kubernetes*

Docker Desktop သည် Docker Daemon နှင့် container အားလုံးကို host လုပ်သော Linux virtual machine တစ်ခုကို setup လုပ်ပါသည်။ ဤ VM သည် node ကို manage လုပ်သော Kubernetes agent ဖြစ်သည့် Kubelet ကိုလည်း run ပါသည်။ Control Plane ၏ component များသည် container များတွင် run သကဲ့သို့၊ သင် deploy လုပ်သော application အားလုံးလည်း container များတွင် run ပါ။

run နေသော container များကို စာရင်းပြုစုရန်၊ သင်သည် VM သို့ log on ရန် မလိုအပ်ပါ၊ အဘယ်ကြောင့်ဆိုသော် သင်၏ host OS တွင် ရရှိနိုင်သော `docker` CLI tool သည် ၎င်းတို့ကို ပြသပေးသောကြောင့် ဖြစ်ပါသည်။

**Virtual Machine ကို အတွင်းမှ လေ့လာခြင်း**

ဤစာရေးသားချိန်တွင်၊ သင်သည် VM ကို အတွင်းမှ လေ့လာလိုပါက ၎င်းထဲသို့ log in ဝင်ရန် Docker Desktop သည် command တစ်ခုမျှ မပေးပါ။ သို့သော်၊ သင်သည် remote shell တစ်ခု run ရန် VM ၏ namespace များကို အသုံးပြုရန် configure လုပ်ထားသော အထူး container တစ်ခုကို run နိုင်ပြီး၊ ၎င်းသည် remote server တစ်ခုသို့ access ရန် SSH ကို အသုံးပြုခြင်းနှင့် တူညီပါသည်။ container ကို run ရန်၊ အောက်ပါ command ကို execute လုပ်ပါ-

```bash
$ docker run --net=host --ipc=host --uts=host --pid=host --privileged \
--security-opt=seccomp=unconfined -it --rm -v /:/host alpine chroot /host
```

ဤရှည်လျားသော command ကို ရှင်းပြရန် လိုအပ်ပါသည်-

*   container ကို `alpine` image မှ ဖန်တီးပါသည်။
*   `--net`, `--ipc`, `--uts` နှင့် `--pid` flag များသည် container ကို sandboxed ဖြစ်မည့်အစား host ၏ namespace များကို အသုံးပြုစေပြီး၊ `--privileged` နှင့် `--security-opt` flag များသည် container ကို sys-call အားလုံးသို့ အကန့်အသတ်မရှိ access ပေးပါသည်။
*   `-it` flag သည် container ကို interactive mode ဖြင့် run စေပြီး `--rm` flag သည် container ရပ်သွားသည့်အခါ ဖျက်ပစ်ကြောင်း သေချာစေပါသည်။
*   `-v` flag သည် host ၏ root directory ကို container ရှိ `/host` directory သို့ mount လုပ်ပါသည်။ `chroot /host` command သည် ဤ directory ကို container ရှိ root directory ဖြစ်စေပါသည်။

သင် command ကို run ပြီးနောက်၊ သင်သည် VM ထဲသို့ SSH ဖြင့် ဝင်ထားသကဲ့သို့ တူညီသော shell တစ်ခုထဲတွင် ရှိနေပါသည်။ ဤ shell ကို အသုံးပြု၍ VM ကို လေ့လာပါ - `ps aux` command ကို execute လုပ်၍ process များကို စာရင်းပြုစုကြည့်ပါ သို့မဟုတ် `ip addr` ကို run ၍ network interface များကို လေ့လာကြည့်ပါ။

### ၃.၁.၂ Minikube ကို အသုံးပြု၍ Local Cluster တစ်ခု Run ခြင်း

Kubernetes cluster တစ်ခု ဖန်တီးရန် နောက်တစ်နည်းမှာ Kubernetes community မှ ထိန်းသိမ်းထားသော tool ဖြစ်သည့် Minikube ကို အသုံးပြုခြင်း ဖြစ်ပါသည်။ Minikube မှ deploy လုပ်သော Kubernetes version သည် Docker Desktop မှ deploy လုပ်သော version ထက် ပိုမိုနောက်ဆုံးပေါ် ဖြစ်လေ့ရှိပါသည်။ cluster တွင် node တစ်ခုတည်း ပါဝင်ပြီး Kubernetes ကို စမ်းသပ်ရန်နှင့် application များကို locally develop လုပ်ရန် နှစ်မျိုးလုံးအတွက် သင့်တော်ပါသည်။ ၎င်းသည် Kubernetes ကို Linux VM တစ်ခုတွင် run လေ့ရှိသော်လည်း၊ သင်၏ computer သည် Linux-based ဖြစ်ပါက၊ ၎င်းသည် Docker မှတစ်ဆင့် သင်၏ host OS တွင် Kubernetes ကို တိုက်ရိုက် deploy လုပ်နိုင်ပါသည်။

> **မှတ်ချက်**
> အကယ်၍ သင်သည် Minikube ကို VM တစ်ခု အသုံးပြုရန် configure လုပ်ပါက၊ သင်သည် Docker မလိုအပ်ပါ၊ သို့သော် VirtualBox ကဲ့သို့သော hypervisor တစ်ခု လိုအပ်ပါသည်။ အခြားတစ်กรณีတွင် သင်သည် Docker လိုအပ်သော်လည်း hypervisor မလိုအပ်ပါ။

**Minikube ကို Install လုပ်ခြင်း**

Minikube သည် macOS, Linux, နှင့် Windows ကို support လုပ်ပါသည်။ ၎င်းတွင် GitHub ရှိ Minikube repository (http://github.com/kubernetes/minikube) တွင် သင်ရှာဖွေနိုင်သော binary executable file တစ်ခုတည်း ရှိပါသည်။ ထိုနေရာတွင် ထုတ်ပြန်ထားသော လက်ရှိ installation လမ်းညွှန်များကို လိုက်နာခြင်းသည် အကောင်းဆုံးဖြစ်သော်လည်း၊ အကြမ်းဖျင်းအားဖြင့် သင်သည် ၎င်းကို အောက်ပါအတိုင်း install လုပ်သည်-

macOS တွင် သင်သည် ၎င်းကို Brew Package Manager ကို အသုံးပြု၍ install လုပ်နိုင်ပြီး၊ Windows တွင် သင် download လုပ်နိုင်သော installer တစ်ခု ရှိပြီး၊ Linux တွင် သင်သည် `.deb` သို့မဟုတ် `.rpm` package တစ်ခုကို download လုပ်နိုင်သည် သို့မဟုတ် binary file ကို download လုပ်ပြီး အောက်ပါ command ဖြင့် executable ဖြစ်အောင် ပြုလုပ်နိုင်သည်-

```bash
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

သင်၏ တိကျသော OS အတွက် အသေးစိတ်အချက်အလက်များအတွက်၊ online ရှိ installation guide ကို ကိုးကားပါ။

**Minikube ဖြင့် Kubernetes cluster တစ်ခု စတင်ခြင်း**

Minikube install လုပ်ပြီးနောက်၊ Kubernetes cluster ကို အောက်တွင် ပြထားသည့်အတိုင်း စတင်ပါ-

```bash
$ minikube start
* minikube v1.11.0 on Fedora 31
* Using the virtualbox driver based on user configuration
* Downloading VM boot image...
> minikube-v1.11.0.iso.sha256: 65 B / 65 B [] 100.00% ? p/s 0s
> minikube-v1.11.0.iso: 174.99 MiB / 174.99 MiB [] 100.00% 50.16 MiB p/s 4s
* Starting control plane node minikube in cluster minikube
* Downloading Kubernetes v1.18.3 preload...
> preloaded-images-k8s-v3-v1.18.3-docker-overlay2-amd64.tar.lz4:
* Creating virtualbox VM (CPUs=2, Memory=6000MB, Disk=20000MB)...
* Preparing Kubernetes v1.18.3 on Docker 19.03.8...
* Verifying Kubernetes components...
* Enabled addons: default-storageclass, storage-provisioner
* Done! kubectl is now configured to use "minikube"
```

VM image နှင့် Kubernetes component များ၏ container image များကို download လုပ်ရမည်ဖြစ်သောကြောင့် ဤ process သည် မိနစ်အနည်းငယ် ကြာနိုင်ပါသည်။

> **အကြံပြုချက်**
> အကယ်၍ သင်သည် Linux ကို အသုံးပြုပါက၊ VM မပါဘဲ cluster ဖန်တီးခြင်းဖြင့် Minikube မှ လိုအပ်သော resource များကို လျှော့ချနိုင်ပါသည်။ ဤ command ကို အသုံးပြုပါ- `minikube start --vm-driver=none`

**Minikube ၏ status ကို စစ်ဆေးခြင်း**

`minikube start` command ပြီးဆုံးသွားသည့်အခါ၊ သင်သည် `minikube status` command ကို run ၍ cluster ၏ status ကို စစ်ဆေးနိုင်သည်-

```bash
$ minikube status
* host: Running
* kubelet: Running
* apiserver: Running
* kubeconfig: Configured
```

command ၏ output သည် Kubernetes host (Kubernetes ကို host လုပ်သော VM) run နေကြောင်း၊ node ကို manage လုပ်ရန် တာဝန်ရှိသော agent ဖြစ်သည့် Kubelet နှင့် Kubernetes API server တို့လည်း run နေကြောင်း ပြသပါသည်။ နောက်ဆုံးလိုင်းသည် `kubectl` command-line tool (CLI) ကို Minikube မှ ပံ့ပိုးပေးသော Kubernetes cluster ကို အသုံးပြုရန် configure လုပ်ထားကြောင်း ပြသပါသည်။ Minikube သည် CLI tool ကို install မလုပ်သော်လည်း၊ ၎င်း၏ configuration file ကို ဖန်တီးပေးပါသည်။ CLI tool ၏ installation ကို အပိုင်း ၃.၂ တွင် ရှင်းပြထားပါသည်။

**System ကို မြင်ယောင်ကြည့်ခြင်း**

system ၏ architecture သည် Docker Desktop တွင် ရှိသည်နှင့် တူညီပြီး အောက်ပါပုံတွင် ပြထားပါသည်။

*ပုံ ၃.၃ Minikube ကို အသုံးပြု၍ single-node Kubernetes cluster တစ်ခု run ခြင်း*

Control Plane component များသည် VM တွင် သို့မဟုတ် သင်သည် cluster ဖန်တီးရန် `--vm-driver=none` option ကို အသုံးပြုခဲ့ပါက သင်၏ host OS တွင် တိုက်ရိုက် container များဖြင့် run ပါသည်။ Kubelet သည် VM ၏ သို့မဟုတ် သင်၏ host OS တွင် တိုက်ရိုက် run ပါသည်။ ၎င်းသည် သင် deploy လုပ်သော application များကို Docker Daemon မှတစ်ဆင့် cluster တွင် run ပါ။

သင်သည် `minikube ssh` ကို run ၍ Minikube VM ထဲသို့ log in ဝင်ပြီး ၎င်းကို အတွင်းမှ လေ့လာနိုင်ပါသည်။ ဥပမာအားဖြင့်၊ သင်သည် `ps aux` ကို run ၍ run နေသော process များကို စာရင်းပြုစုခြင်း သို့မဟုတ် `docker ps` ကို run ၍ run နေသော container များကို စာရင်းပြုစုခြင်းဖြင့် VM တွင် run နေသောအရာများကို ကြည့်ရှုနိုင်ပါသည်။

> **အကြံပြုချက်**
> Docker Desktop တွင်ကဲ့သို့ သင်၏ local `docker` CLI instance ကို အသုံးပြု၍ container များကို စာရင်းပြုစုလိုပါက၊ အောက်ပါ command ကို run ပါ- `eval $(minikube docker-env)`

### ၃.၁.၃ kind (Kubernetes in Docker) ကို အသုံးပြု၍ Local Cluster တစ်ခု Run ခြင်း

Minikube ၏ အခြားရွေးချယ်စရာတစ်ခုမှာ kind (Kubernetes-in-Docker) ဖြစ်ပြီး၊ ၎င်းသည် Minikube ကဲ့သို့ ရင့်ကျက်မှု မရှိသေးပါ။ Kubernetes ကို virtual machine တွင် သို့မဟုတ် host တွင် တိုက်ရိုက် run မည့်အစား၊ kind သည် Kubernetes cluster node တစ်ခုစီကို container တစ်ခုအတွင်းတွင် run ပါသည်။ Minikube နှင့် မတူဘဲ၊ ၎င်းသည် container အများအပြားကို စတင်ခြင်းဖြင့် multi-node cluster များ ဖန်တီးနိုင်စေပါသည်။ သင် Kubernetes သို့ deploy လုပ်သော အမှန်တကယ် application container များသည် ထို့နောက် ဤ node container များအတွင်းတွင် run ပါသည်။ system ကို အောက်ပါပုံတွင် ပြထားပါသည်။

*ပုံ ၃.၄ kind ကို အသုံးပြု၍ multi-node Kubernetes cluster တစ်ခု run ခြင်း*

ယခင်အခန်းတွင် ကျွန်ုပ်သည် container တစ်ခုတွင် run နေသော process သည် အမှန်တကယ်တွင် host OS တွင် run နေကြောင်း ဖော်ပြခဲ့ပါသည်။ ဤသည်မှာ သင်သည် kind ကို အသုံးပြု၍ Kubernetes ကို run သည့်အခါ၊ Kubernetes component အားလုံးသည် သင်၏ host OS တွင် run နေကြောင်း ဆိုလိုပါသည်။ သင် Kubernetes cluster သို့ deploy လုပ်သော application များသည်လည်း သင်၏ host OS တွင် run ပါ။

ဤသည်မှာ kind ကို development နှင့် testing အတွက် အကောင်းဆုံး tool ဖြစ်စေပါသည်၊ အဘယ်ကြောင့်ဆိုသော် အရာအားလုံး locally run ပြီး သင်သည် run နေသော process များကို container ပြင်ပတွင် run သကဲ့သို့ လွယ်ကူစွာ debug လုပ်နိုင်သောကြောင့် ဖြစ်ပါသည်။ ကျွန်ုပ်သည် Kubernetes တွင် app များကို develop လုပ်သည့်အခါ ဤချဉ်းကပ်မှုကို ပိုမိုနှစ်သက်ပါသည်၊ အဘယ်ကြောင့်ဆိုသော် ၎င်းသည် ကျွန်ုပ်အား Wireshark ကဲ့သို့သော network traffic analysis tool များကို သို့မဟုတ် ကျွန်ုပ်၏ web browser ကိုပင် ကျွန်ုပ်၏ application များကို run နေသော container များအတွင်းတွင် run နိုင်စေသောကြောင့် ဖြစ်ပါသည်။ ကျွန်ုပ်သည် ဤ tool များကို container ၏ network သို့မဟုတ် အခြား namespace များတွင် run နိုင်စေသော `nsenter` ဟုခေါ်သော tool ကို အသုံးပြုပါသည်။

အကယ်၍ သင်သည် Kubernetes ကို အသစ်စက်စက် လေ့လာနေသူဖြစ်ပါက၊ Minikube ဖြင့် စတင်ခြင်းသည် အလုံခြုံဆုံးဖြစ်သော်လည်း၊ သင်သည် စွန့်စားလိုစိတ်ရှိပါက၊ kind ဖြင့် မည်သို့စတင်ရမည်ကို ဤတွင် ဖော်ပြထားပါသည်။

**kind ကို Install လုပ်ခြင်း**

Minikube ကဲ့သို့ပင်၊ kind တွင် binary executable file တစ်ခုတည်း ပါဝင်ပါသည်။ ၎င်းကို install လုပ်ရန်၊ https://kind.sigs.k8s.io/docs/user/quick-start/ ရှိ installation လမ်းညွှန်များကို ကိုးကားပါ။ macOS နှင့် Linux တွင်၊ ၎င်းကို install လုပ်ရန် command များမှာ အောက်ပါအတိုင်းဖြစ်သည်-

```bash
$ curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-$(uname)-amd64
$ chmod +x ./kind
$ mv ./kind /some-dir-in-your-PATH/kind
```

နောက်ဆုံး version က ဘာလဲဆိုတာကို document တွင် စစ်ဆေးပြီး အပေါ်က ဥပမာရှိ `v0.7.0` အစား ၎င်းကို အသုံးပြုပါ။ `/some-dir-in-your-PATH/` ကို သင်၏ path ရှိ အမှန်တကယ် directory တစ်ခုဖြင့် အစားထိုးပါ။

> **မှတ်ချက်**
> kind ကို အသုံးပြုရန် သင်၏ system တွင် Docker install လုပ်ထားရပါမည်။

**kind ဖြင့် Kubernetes cluster တစ်ခု စတင်ခြင်း**

cluster အသစ်တစ်ခု စတင်ခြင်းသည် Minikube ဖြင့် စတင်သကဲ့သို့ လွယ်ကူပါသည်။ အောက်ပါ command ကို execute လုပ်ပါ-

```bash
$ kind create cluster
```

Minikube ကဲ့သို့ပင်၊ kind သည် ၎င်းဖန်တီးသော cluster ကို အသုံးပြုရန် `kubectl` ကို configure လုပ်ပါသည်။

**kind ဖြင့် multi-node cluster တစ်ခု စတင်ခြင်း**

Kind သည် default အားဖြင့် single-node cluster တစ်ခု run ပါသည်။ အကယ်၍ သင်သည် worker node အများအပြားပါသော cluster တစ်ခု run လိုပါက၊ သင်သည် configuration file တစ်ခုကို အရင် ဖန်တီးရပါမည်။ အောက်ပါ listing သည် ဤ file (`Chapter03/kind-multi-node.yaml`) ၏ contents များကို ပြသပါသည်။

*Listing ၃.၁ kind tool ဖြင့် three-node cluster တစ်ခု run ရန် Config file*

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

file ရှိပြီးသည်နှင့်၊ အောက်ပါ command ကို အသုံးပြု၍ cluster ကို ဖန်တီးပါ-

```bash
$ kind create cluster --config kind-multi-node.yaml
```

**worker node များကို စာရင်းပြုစုခြင်း**

ဤစာရေးသားချိန်တွင်၊ kind သည် cluster ၏ status ကို စစ်ဆေးရန် command တစ်ခုမျှ မပေးပါ၊ သို့သော် သင်သည် `kind get nodes` ကို အသုံးပြု၍ cluster node များကို စာရင်းပြုစုနိုင်သည်-

```bash
$ kind get nodes
kind-worker2
kind-worker
kind-control-plane
```

node တစ်ခုစီသည် container တစ်ခုအဖြစ် run သောကြောင့်၊ သင်သည် `docker ps` ကို အသုံးပြု၍ run နေသော container များကို စာရင်းပြုစုခြင်းဖြင့် node များကို ကြည့်ရှုနိုင်သည်-

```bash
$ docker ps
CONTAINER ID   IMAGE                  ...   NAMES
45d0f712eac0   kindest/node:v1.18.2   ...   kind-worker2
d1e88e98e3ae   kindest/node:v1.18.2   ...   kind-worker
4b7751144ca4   kindest/node:v1.18.2   ...   kind-control-plane
```

**kind မှ provision လုပ်ထားသော cluster node များထဲသို့ Log in ဝင်ခြင်း**

Minikube တွင် သင်သည် node ထဲတွင် run နေသော process များကို လေ့လာလိုပါက node ထဲသို့ log in ဝင်ရန် `minikube ssh` ကို အသုံးပြုသော်လည်း၊ kind တွင် သင်သည် `docker exec` ကို အသုံးပြုပါသည်။ ဥပမာအားဖြင့်၊ `kind-control-plane` ဟုခေါ်သော node ထဲသို့ ဝင်ရန်၊ run ပါ-

```bash
$ docker exec -it kind-control-plane bash
```

Docker ကို container များ run ရန် အသုံးပြုမည့်အစား၊ kind မှ ဖန်တီးသော node များသည် CRI-O container runtime ကို အသုံးပြုပြီး၊ ယခင်အခန်းတွင် ကျွန်ုပ်သည် ၎င်းကို Docker ၏ lightweight alternative တစ်ခုအဖြစ် ဖော်ပြခဲ့ပါသည်။ `crictl` CLI tool ကို CRI-O နှင့် အပြန်အလှန် ဆက်သွယ်ရန် အသုံးပြုပါသည်။ ၎င်း၏အသုံးပြုပုံသည် `docker` tool နှင့် အလွန်ဆင်တူပါသည်။ node ထဲသို့ log in ဝင်ပြီးနောက်၊ `docker ps` အစား `crictl ps` ကို run ၍ ၎င်းတွင် run နေသော container များကို စာရင်းပြုစုပါ။ ဤတွင် command နှင့် ၎င်း၏ output ၏ ဥပမာတစ်ခု ဖြစ်သည်-

```bash
root@kind-control-plane:/# crictl ps
CONTAINER      IMAGE              CREATED        STATE    NAME ...
eb516548c180f  c7f44d171fb72      15 mins ago    Running  coredns
...
```

### ၃.၁.၄ Google Kubernetes Engine (GKE) ဖြင့် Managed Cluster တစ်ခု တည်ဆောက်ခြင်း

local cluster တစ်ခုအစား full-fledged multi-node Kubernetes cluster တစ်ခုကို အသုံးပြုလိုပါက၊ Google Kubernetes Engine (GKE) မှ ပံ့ပိုးပေးသော managed cluster ကဲ့သို့သော managed cluster တစ်ခုကို အသုံးပြုနိုင်ပါသည်။ ဤနည်းဖြင့်၊ သင်သည် cluster node အားလုံးနှင့် networking ကို manually setup လုပ်ရန် မလိုအပ်ပါ၊ ၎င်းသည် Kubernetes နှင့် ပထမဆုံးခြေလှမ်းလှမ်းနေသူတစ်ဦးအတွက် များသောအားဖြင့် အလွန်ခက်ခဲပါသည်။ GKE ကဲ့သို့သော managed solution တစ်ခုကို အသုံးပြုခြင်းသည် သင်သည် မမှန်ကန်စွာ configure လုပ်ထားသော cluster တစ်ခုနှင့် အဆုံးသတ်မည်မဟုတ်ကြောင်း သေချာစေပါသည်။

**Google Cloud ကို Setup လုပ်ပြီး gcloud client binary ကို Install လုပ်ခြင်း**

Kubernetes cluster အသစ်တစ်ခု setup မလုပ်မီ၊ သင်သည် သင်၏ GKE environment ကို setup လုပ်ရပါမည်။ ဤ process သည် အနာဂတ်တွင် ပြောင်းလဲနိုင်သောကြောင့်၊ ကျွန်ုပ်သည် ဤနေရာတွင် အထွေထွေညွှန်ကြားချက်အနည်းငယ်သာ ပေးပါမည်။ စုံလင်သော ညွှန်ကြားချက်များအတွက်၊ https://cloud.google.com/container-engine/docs/before-you-begin ကို ကိုးကားပါ။

အကြမ်းဖျင်းအားဖြင့်၊ လုပ်ငန်းစဉ်တစ်ခုလုံးတွင် ပါဝင်သည်မှာ-

1.  သင်မရှိသေးပါက Google account တစ်ခုအတွက် sign up လုပ်ပါ။
2.  Google Cloud Platform Console တွင် project တစ်ခု ဖန်တီးပါ။
3.  Billing ကို enable လုပ်ပါ။ ဤသည်မှာ သင်၏ credit card အချက်အလက် လိုအပ်သော်လည်း၊ Google သည် အခမဲ့ $300 credit ဖြင့် 12-month free trial ပေးပါသည်။ ပြီးတော့ သူတို့သည် free trial ပြီးဆုံးသွားသည့်အခါ အလိုအလျောက် charge မလုပ်ပါ။
4.  `gcloud` tool ပါဝင်သော Google Cloud SDK ကို download လုပ်ပြီး install လုပ်ပါ။
5.  `gcloud` command-line tool ကို အသုံးပြု၍ cluster ကို ဖန်တီးပါ။

> **မှတ်ချက်**
> အချို့သော operation များ (ဥပမာ အဆင့် ၂ ရှိ တစ်ခု) သည် ပြီးမြောက်ရန် မိနစ်အနည်းငယ် ကြာနိုင်သောကြောင့်၊ စိတ်အေးအေးထားပြီး ကော်ဖီတစ်ခွက် သောက်ပါ။

**node သုံးခုပါသော GKE Kubernetes cluster တစ်ခု ဖန်တီးခြင်း**

သင်၏ cluster ကို မဖန်တီးမီ၊ ၎င်းကို မည်သည့် geographical region နှင့် zone တွင် ဖန်တီးသင့်သည်ကို သင် ဆုံးဖြတ်ရပါမည်။ ရရှိနိုင်သော location များစာရင်းကို ကြည့်ရှုရန် https://cloud.google.com/compute/docs/regions-zones ကို ကိုးကားပါ။ အောက်ပါဥပမာများတွင်၊ ကျွန်ုပ်သည် Germany, Frankfurt တွင် အခြေစိုက်သော `europe-west3` region ကို အသုံးပြုပါသည်။ ၎င်းတွင် zone သုံးခုရှိသည် - ကျွန်ုပ်သည် `europe-west3-c` zone ကို အသုံးပြုပါမည်။ `gcloud` operation အားလုံးအတွက် default zone ကို အောက်ပါ command ဖြင့် set လုပ်နိုင်သည်-

```bash
$ gcloud config set compute/zone europe-west3-c
```

Kubernetes cluster ကို ဤသို့ ဖန်တီးပါ-

```bash
$ gcloud container clusters create kiada --num-nodes 3
Creating cluster kiada in europe-west3-c...
kubeconfig entry generated for kiada.
NAME   LOCAT.    MASTER_VER   MASTER_IP    MACH_TYPE       NODES  S...
kiada  eu-w3-c   1.13.11.     5.24.21.22   n1-standard-1... 3      R...
```

> **မှတ်ချက်**
> ကျွန်ုပ်သည် worker node သုံးခုလုံးကို zone တစ်ခုတည်းတွင် ဖန်တီးနေသော်လည်း၊ သင်သည် region ရှိ zone အားလုံးတွင် ၎င်းတို့ကို ဖြန့်ကျက်နိုင်ပြီး၊ `compute/zone` config value ကို zone တစ်ခုတည်းအစား region တစ်ခုလုံးသို့ set လုပ်ခြင်းဖြင့် ၎င်းကို ပြုလုပ်နိုင်ပါသည်။ အကယ်၍ သင်သည် ဤသို့ပြုလုပ်ပါက၊ `--num-nodes` သည် zone တစ်ခုလျှင် node အရေအတွက်ကို ညွှန်ပြကြောင်း သတိပြုပါ။ အကယ်၍ region တွင် zone သုံးခု ပါဝင်ပြီး သင်သည် node သုံးခုသာ လိုချင်ပါက၊ သင်သည် `--num-nodes` ကို 1 သို့ set လုပ်ရပါမည်။

သင်သည် ယခု worker node သုံးခုပါသော run နေသော Kubernetes cluster တစ်ခု ရှိသင့်ပါသည်။ node တစ်ခုစီသည် Google Compute Engine (GCE) infrastructure-as-a-service platform မှ ပံ့ပိုးပေးသော virtual machine တစ်ခု ဖြစ်ပါသည်။ သင်သည် အောက်ပါ command ကို အသုံးပြု၍ GCE virtual machine များကို စာရင်းပြုစုနိုင်သည်-

```bash
$ gcloud compute instances list
NAME       ZONE        MACHINE_TYPE   ... INTERNAL_IP  EXTERNAL_IP
...-ctlk   eu-west3-c  n1-standard-1  ... 10.156.0.16  34.89.238.55
...-gj1f   eu-west3-c  n1-standard-1  ... 10.156.0.14  35.242.223.97
...-r01z   eu-west3-c  n1-standard-1  ... 10.156.0.15  35.198.191.189
```

> **အကြံပြုချက်**
> VM တစ်ခုစီသည် ကုန်ကျစရိတ် ရှိပါသည်။ သင်၏ cluster ၏ ကုန်ကျစရိတ်ကို လျှော့ချရန်၊ သင်သည် node အရေအတွက်ကို တစ်ခုသို့ လျှော့ချနိုင်သည်၊ သို့မဟုတ် အသုံးမပြုသည့်အခါတွင် သုညသို့ပင် လျှော့ချနိုင်ပါသည်။ အသေးစိတ်အတွက် နောက်အပိုင်းကို ကြည့်ပါ။

system ကို အောက်ပါပုံတွင် ပြထားပါသည်။ သင်၏ worker node များသာ GCE virtual machine များတွင် run ကြောင်း သတိပြုပါ။ control plane သည် အခြားနေရာတွင် run ပါသည် - သင်သည် ၎င်းကို host လုပ်သော machine များသို့ access မရနိုင်ပါ။

*ပုံ ၃.၅ Google Kubernetes Engine ရှိ သင်၏ Kubernetes cluster*

**Node အရေအတွက်ကို Scale လုပ်ခြင်း**

Google သည် သင့်အား သင်၏ cluster ရှိ node အရေအတွက်ကို အလွယ်တကူ တိုးမြှင့်ခြင်း သို့မဟုတ် လျှော့ချခြင်း ပြုလုပ်နိုင်စေပါသည်။ ဤစာအုပ်ရှိ လေ့ကျင့်ခန်းအများစုအတွက် သင်သည် ပိုက်ဆံချွေတာလိုပါက ၎င်းကို node တစ်ခုတည်းသို့ scale down နိုင်ပါသည်။ သင်သည် သင်၏ cluster သည် ကုန်ကျစရိတ် တစ်စုံတစ်ရာ မရှိစေရန် ၎င်းကို သုညသို့ပင် scale down နိုင်ပါသည်။

cluster ကို သုညသို့ scale လုပ်ရန်၊ အောက်ပါ command ကို အသုံးပြုပါ-

```bash
$ gcloud container clusters resize kiada --size 0
```

သုညသို့ scale လုပ်ခြင်း၏ ကောင်းမွန်သည့်အချက်မှာ သင် deploy လုပ်သော application များအပါအဝင် သင်၏ Kubernetes cluster တွင် သင်ဖန်တီးသော object တစ်ခုမျှ ဖျက်ပစ်ခြင်း မခံရခြင်း ဖြစ်ပါသည်။ သင်သည် သုညသို့ scale down ပါက၊ application များသည် run ရန် node များ မရှိတော့သဖြင့် run မည်မဟုတ်ပါ။ သို့သော် သင်သည် cluster ကို ပြန်လည် scale up လိုက်သည်နှင့်၊ ၎င်းတို့သည် redeploy လုပ်မည်ဖြစ်သည်။ worker node မရှိသည့်တိုင်၊ သင်သည် Kubernetes API နှင့် အပြန်အလှန် ဆက်သွယ်နိုင်ပါသေးသည် (သင်သည် object များကို ဖန်တီး၊ update လုပ်၊ နှင့် ဖျက်ပစ်နိုင်သည်)။

**GKE worker node တစ်ခုကို စစ်ဆေးခြင်း**

သင်၏ node များတွင် run နေသောအရာကို စိတ်ဝင်စားပါက၊ သင်သည် အောက်ပါ command ဖြင့် ၎င်းတို့ထဲသို့ log in ဝင်နိုင်သည် (ယခင် command ၏ output မှ node name တစ်ခုကို အသုံးပြုပါ)-

```bash
$ gcloud compute ssh gke-kiada-default-pool-9bba9b18-4glf
```

node ထဲသို့ log in ဝင်နေစဉ်၊ သင်သည် `docker ps` ဖြင့် run နေသော container အားလုံးကို စာရင်းပြုစုကြည့်နိုင်ပါသည်။ သင်သည် application တစ်ခုမျှ run မထားသေးသောကြောင့်၊ သင်သည် Kubernetes system container များကိုသာ မြင်ရပါမည်။ ၎င်းတို့သည် ဘာလဲဆိုတာ အခုအချိန်မှာ အရေးမကြီးပါ၊ သို့သော် သင်သည် ၎င်းတို့အကြောင်းကို နောက်ပိုင်းအခန်းများတွင် လေ့လာရပါမည်။

### ၃.၁.၅ Amazon Elastic Kubernetes Service (EKS) ကို အသုံးပြု၍ Cluster တစ်ခု တည်ဆောက်ခြင်း

အကယ်၍ သင်သည် သင်၏ Kubernetes cluster ကို cloud တွင် deploy လုပ်ရန် Google အစား Amazon ကို အသုံးပြုလိုပါက၊ Amazon Elastic Kubernetes Service (EKS) ကို စမ်းကြည့်နိုင်ပါသည်။ အခြေခံများကို ကျော်ဖြတ်ကြည့်ကြပါစို့။

ပထမဦးစွာ၊ သင်သည် https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html ရှိ လမ်းညွှန်ချက်များအတိုင်း `eksctl` command-line tool ကို install လုပ်ရပါမည်။

**EKS Kubernetes cluster တစ်ခု ဖန်တီးခြင်း**

`eksctl` ကို အသုံးပြု၍ EKS Kubernetes cluster တစ်ခု ဖန်တီးခြင်းသည်... ... သင်သည် ဤ node များတွင် run နေသောအရာကို စိတ်ဝင်စားပါက၊ သင်သည် ၎င်းတို့နှင့် ချိတ်ဆက်ရန် SSH ကို အသုံးပြုနိုင်ပါသည်။ cluster ဖန်တီးသော command တွင် အသုံးပြုသော `--ssh-access` flag သည် သင်၏ SSH public key ကို node သို့ import လုပ်ကြောင်း သေချာစေပါသည်။

GKE နှင့် Minikube ကဲ့သို့ပင်၊ သင်သည် node ထဲသို့ log in ဝင်ပြီးသည်နှင့်၊ သင်သည် `docker ps` ဖြင့် run နေသော container အားလုံးကို စာရင်းပြုစုကြည့်နိုင်ပါသည်။ သင်သည် ကျွန်ုပ်တို့ အစောပိုင်းက ဖော်ပြခဲ့သော cluster များရှိ container များနှင့် ဆင်တူသော container များကို မြင်ရမည်ဟု မျှော်လင့်နိုင်ပါသည်။

### ၃.၁.၆ Multi-node Cluster တစ်ခုကို အစမှစ၍ Deploy လုပ်ခြင်း

သင်သည် Kubernetes ကို ပိုမိုနက်ရှိုင်းစွာ နားလည်လာသည်အထိ၊ multi-node cluster တစ်ခုကို အစမှစ၍ install လုပ်ရန် မကြိုးစားရန် ကျွန်ုပ် အလေးအနက် အကြံပြုပါသည်။ အကယ်၍ သင်သည် အတွေ့အကြုံရှိသော system administrator တစ်ဦးဖြစ်ပါက၊ သင်သည် ၎င်းကို များစွာသော နာကျင်မှုနှင့် ဒုက္ခမရှိဘဲ ပြုလုပ်နိုင်ပေမည်၊ သို့သော် လူအများစုသည် အစောပိုင်းက ဖော်ပြခဲ့သော နည်းလမ်းများထဲမှ တစ်ခုကို အရင် စမ်းကြည့်လိုပေမည်။ Kubernetes cluster များကို ကောင်းမွန်စွာ manage လုပ်ခြင်းသည် အလွန်ခက်ခဲပါသည်။ installation တစ်ခုတည်းကပင် လျှော့မတွက်သင့်သော အလုပ်တစ်ခု ဖြစ်ပါသည်။

အကယ်၍ သင်သည် စွန့်စားလိုစိတ် ရှိနေသေးပါက၊ သင်သည် နောက်ဆက်တွဲ B ရှိ လမ်းညွှန်ချက်များဖြင့် စတင်နိုင်ပြီး...

## ၃.၂ သင်၏ Kubernetes Cluster နှင့် အပြန်အလှန် ဆက်သွယ်ခြင်း

`kubectl` ("kube-control" ဟု အသံထွက်) ဟုခေါ်သော command-line tool သည် Kubernetes cluster များနှင့် အပြန်အလှန် ဆက်သွယ်ရန် အဓိကနည်းလမ်း ဖြစ်ပါသည်။ နောက်ပုံတွင် ပြထားသည့်အတိုင်း၊ tool သည် Kubernetes API server နှင့် ဆက်သွယ်ပြီး၊ ၎င်းသည် Kubernetes Control Plane ၏ တစ်စိတ်တစ်ပိုင်း ဖြစ်ပါသည်။ control plane သည် သင် API မှတစ်ဆင့် ပြုလုပ်ခဲ့သော အပြောင်းအလဲများအပေါ် အခြေခံ၍ လိုအပ်သမျှကို လုပ်ဆောင်ရန် အခြား component များကို trigger လုပ်ပါသည်။

*ပုံ ၃.၆ သင်သည် Kubernetes cluster တစ်ခုနှင့် မည်သို့ အပြန်အလှန်ဆက်သွယ်သည်*

### ၃.၂.၁ kubectl - Kubernetes Command-line Client ကို Setup လုပ်ခြင်း

`kubectl` သည် သင်၏ computer သို့ download လုပ်ပြီး သင်၏ path ထဲတွင် ထားရမည့် executable file တစ်ခုတည်း ဖြစ်ပါသည်။ ၎င်းသည် `kubeconfig` ဟုခေါ်သော configuration file တစ်ခုမှ ၎င်း၏ configuration ကို load လုပ်ပါသည်။ `kubectl` ကို အသုံးပြုရန်၊ သင်သည် ၎င်းကို install လုပ်ပြီး `kubeconfig` file ကို ပြင်ဆင်ရမည်ဖြစ်ပြီး၊ သို့မှသာ `kubectl` သည် မည်သည့် cluster နှင့် စကားပြောရမည်ကို သိနိုင်မည်ဖြစ်သည်။

**kubectl ကို Download လုပ်ပြီး Install လုပ်ခြင်း**

Linux အတွက် နောက်ဆုံး stable release ကို အောက်ပါ command များဖြင့် download လုပ်ပြီး install လုပ်နိုင်သည်-

```bash
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
$chmod +x kubectl
$ sudo mv kubectl /usr/local/bin/
```

macOS တွင် `kubectl` ကို install လုပ်ရန်၊ သင်သည် တူညီသော command ကို run နိုင်သော်လည်း URL ရှိ `linux` ကို `darwin` ဖြင့် အစားထိုးပါ၊ သို့မဟုတ် Homebrew မှတစ်ဆင့် tool ကို `brew install kubectl` ကို run ၍ install လုပ်ပါ။

Windows တွင်၊ `kubectl.exe` ကို https://storage.googleapis.com/kubernetes-release/release/v1.18.2/bin/windows/amd64/kubectl.exe မှ download လုပ်ပါ။ နောက်ဆုံး version ကို download လုပ်ရန်၊ ပထမဦးစွာ https://storage.googleapis.com/kubernetes-release/release/stable.txt သို့သွား၍ နောက်ဆုံး stable version က ဘာလဲဆိုတာ ကြည့်ပြီး ပထမ URL ရှိ version number ကို ဤ version ဖြင့် အစားထိုးပါ။ သင်သည် ၎င်းကို မှန်ကန်စွာ install လုပ်ထားခြင်း ရှိမရှိ စစ်ဆေးရန်၊ `kubectl --help` ကို run ပါ။ `kubectl` သည် သင်၏ Kubernetes cluster နှင့် စကားပြောရန် configure လုပ်ထားခြင်း ရှိနိုင်သည် သို့မဟုတ် မရှိနိုင်သေးကြောင်း သတိပြုပါ၊ ၎င်းသည် command အများစု အလုပ်မလုပ်သေးနိုင်ကြောင်း ဆိုလိုပါသည်။

> **အကြံပြုချက်**
> သင်သည် အချက်အလက်ပိုမိုရရှိရန် `kubectl` command မည်သည့်အရာကိုမဆို `--help` ကို အမြဲတမ်း နောက်ဆက်တွဲ ထည့်နိုင်သည်။

**kubectl အတွက် alias အတိုတစ်ခု Setup လုပ်ခြင်း**

သင်သည် `kubectl` ကို မကြာခဏ အသုံးပြုရပါမည်။ command အပြည့်အစုံကို အကြိမ်တိုင်း ရိုက်ရခြင်းသည် အချိန်ကုန်သက်သာမှုမရှိဘဲ၊ သင်သည် alias နှင့် tab completion ကို setup လုပ်ခြင်းဖြင့် အရှိန်မြှင့်နိုင်သည်... ... `k` binary ကို `kubectl` အပြင်တွင် ထည့်သွင်းပါ။

**kubectl အတွက် tab completion ကို Configure လုပ်ခြင်း**

`k` ကဲ့သို့သော alias အတိုဖြင့်ပင်၊ သင်သည် များစွာ ရိုက်ရဦးမည်ဖြစ်သည်။ ကံကောင်းစွာဖြင့်၊ `kubectl` command သည် bash နှင့် zsh shell နှစ်မျိုးလုံးအတွက် shell completion code ကို output လုပ်နိုင်ပါသည်။ ၎င်းသည် command name များသာမက object name များကိုပါ tab completion လုပ်နိုင်စေပါသည်။ ဥပမာအားဖြင့်၊ နောက်ပိုင်းတွင် သင်သည် အောက်ပါ command ကို execute လုပ်၍ cluster node တစ်ခု၏ အသေးစိတ်အချက်အလက်များကို မည်သို့ကြည့်ရှုရမည်ကို လေ့လာရပါမည်-

```bash
$ kubectl describe node gke-kiada-default-pool-9bba9b18-4glf
```

ဤသည်မှာ သင်သည် အချိန်တိုင်း ထပ်ခါထပ်ခါ ရိုက်ရမည့် အရာများစွာ ဖြစ်ပါသည်။ tab completion ဖြင့်၊ အရာများသည် ပိုမိုလွယ်ကူပါသည်။ သင်သည် token တစ်ခုစီ၏ အစပိုင်း character အနည်းငယ်ကို ရိုက်ပြီးနောက် `TAB` ကို နှိပ်ရုံသာ ဖြစ်သည်-

```bash
$ kubectl desc<TAB> no<TAB> gke-ku<TAB>
```

bash တွင် tab completion ကို enable လုပ်ရန်၊ သင်သည် `bash-completion` ဟုခေါ်သော package တစ်ခုကို အရင် install လုပ်ပြီး အောက်ပါ command ကို run ရပါမည် (သင်သည် ၎င်းကို `~/.bashrc` သို့မဟုတ် တူညီသော file တစ်ခုသို့လည်း ထည့်နိုင်သည်)-

```bash
$ source <(kubectl completion bash)
```

> **မှတ်ချက်**
> ဤသည်မှာ bash တွင် completion ကို enable လုပ်ပါသည်။ သင်သည် ဤ command ကို အခြား shell တစ်ခုဖြင့်လည်း run နိုင်သည်...

### ၃.၂.၂ kubeconfig File ကို နားလည်ခြင်း

`kubeconfig` configuration file သည် `~/.kube/config` တွင် တည်ရှိပါသည်။ အကယ်၍ သင်သည် သင်၏ cluster ကို Docker Desktop, Minikube သို့မဟုတ် GKE ကို အသုံးပြု၍ deploy လုပ်ခဲ့ပါက၊ file ကို သင့်အတွက် ဖန်တီးပေးထားပါသည်။ အကယ်၍ သင်သည် ရှိပြီးသား cluster တစ်ခုသို့ access ရရှိခဲ့ပါက၊ သင်သည် file ကို လက်ခံရရှိသင့်ပါသည်။ `kind` ကဲ့သို့သော အခြား tool များသည် file ကို အခြား location တစ်ခုသို့ ရေးသားထားနိုင်ပါသည်။ file ကို default location သို့ ရွှေ့မည့်အစား၊ သင်သည် `KUBECONFIG` environment variable ကို အောက်ပါအတိုင်း set လုပ်ခြင်းဖြင့် `kubectl` ကို ၎င်းသို့ ညွှန်ပြနိုင်သည်-

```bash
$ export KUBECONFIG=/path/to/custom/kubeconfig
```

`kubectl` ၏ configuration ကို manage လုပ်ပုံနှင့် config file တစ်ခုကို အစမှစ၍ ဖန်တီးပုံအကြောင်း ပိုမိုလေ့လာရန်၊ နောက်ဆက်တွဲ A ကို ကိုးကားပါ။

> **မှတ်ချက်**
> အကယ်၍ သင်သည် Kubernetes cluster အများအပြား (ဥပမာ Minikube နှင့် GKE နှစ်မျိုးလုံး) ကို အသုံးပြုလိုပါက၊ မတူညီသော `kubectl` context များအကြား switch လုပ်ပုံအကြောင်း အချက်အလက်များအတွက် နောက်ဆက်တွဲ A ကို ကြည့်ပါ။

### ၃.၂.၃ kubectl ကို အသုံးပြုခြင်း

သင်သည် `kubectl` ကို install လုပ်ပြီး configure လုပ်ထားသည်ဟု ယူဆပါက၊ သင်သည် ယခု ၎င်းကို အသုံးပြု၍ သင်၏ cluster နှင့် စကားပြောနိုင်ပြီဖြစ်သည်။

**cluster run နေခြင်းနှင့် kubectl စကားပြောနိုင်ခြင်း ရှိမရှိ စစ်ဆေးခြင်း**

သင်၏ cluster အလုပ်လုပ်နေခြင်း ရှိမရှိ စစ်ဆေးရန်...

**Cluster node များကို စာရင်းပြုစုခြင်း**

ယခု သင်၏ cluster ရှိ node အားလုံးကို စာရင်းပြုစုရန် `kubectl get nodes` command ကို အသုံးပြုပါ။ ဤတွင် `kind` မှ provision လုပ်ထားသော cluster တစ်ခုတွင် command ကို run သည့်အခါ ထုတ်ပေးသော output ဖြစ်သည်-

```bash
$ kubectl get nodes
NAME                 STATUS   ROLES    AGE   VERSION
control-plane        Ready    <none>   12m   v1.18.2
kind-worker          Ready    <none>   12m   v1.18.2
kind-worker2         Ready    <none>   12m   v1.18.2
```

Kubernetes ရှိ အရာအားလုံးကို object တစ်ခုဖြင့် ကိုယ်စားပြုပြီး RESTful API မှတစ်ဆင့် retrieve လုပ်ပြီး manipulate လုပ်နိုင်ပါသည်။ `kubectl get` command သည် သတ်မှတ်ထားသော type ၏ object များစာရင်းကို API မှ retrieve လုပ်ပါသည်။ သင်သည် ဤ command ကို အချိန်တိုင်း အသုံးပြုရမည်ဖြစ်သော်လည်း၊ ၎င်းသည် စာရင်းပြုစုထားသော object များအကြောင်း အကျဉ်းချုပ် အချက်အလက်များကိုသာ ပြသပါသည်။

**Object တစ်ခု၏ အပိုအသေးစိတ်အချက်အလက်များကို Retrieve လုပ်ခြင်း**

object တစ်ခုအကြောင်း ပိုမိုအသေးစိတ် အချက်အလက်များကို ကြည့်ရှုရန်၊ သင်သည် `kubectl describe` command ကို အသုံးပြုပြီး၊ ၎င်းသည် ပိုမိုများပြားသော အချက်အလက်များကို ပြသပါသည်-

```bash
$ kubectl describe node gke-kiada-85f6-node-0rrx
```

ကျွန်ုပ်သည် `describe` command ၏ အမှန်တကယ် output ကို ချန်လှပ်ထားပါသည်၊ အဘယ်ကြောင့်ဆိုသော် ၎င်းသည် အလွန်ကျယ်ပြန့်ပြီး ဤနေရာတွင် လုံးဝ ဖတ်မရနိုင်သောကြောင့် ဖြစ်သည်...

သင်သည် စာအုပ်တစ်အုပ်လုံးတွင် `kubectl` command အများအပြားအကြောင်း ပိုမိုလေ့လာရပါမည်။

### ၃.၂.၄ Web Dashboard များမှတစ်ဆင့် Kubernetes နှင့် အပြန်အလှန် ဆက်သွယ်ခြင်း

အကယ်၍ သင်သည် graphical web user interface များကို အသုံးပြုလိုပါက၊ Kubernetes သည် ကောင်းမွန်သော web dashboard တစ်ခုနှင့်လည်း လာကြောင်း ကြားရသည့်အခါ သင် ပျော်ရွှင်ပါလိမ့်မည်။ သို့သော်၊ dashboard ၏ functionality သည် Kubernetes နှင့် အပြန်အလှန် ဆက်သွယ်ရန် အဓိက tool ဖြစ်သော `kubectl` ထက် သိသိသာသာ နောက်ကျကျန်နေနိုင်ကြောင်း သတိပြုပါ။

မည်သို့ပင်ဖြစ်စေ၊ dashboard သည် မတူညီသော resource များကို context တွင် ပြသပြီး Kubernetes ရှိ အဓိက resource type များက ဘာလဲဆိုတာနှင့် ၎င်းတို့သည် အချင်းချင်း မည်သို့ ဆက်စပ်နေသည်ကို ခံစားချက်ရရန် ကောင်းမွန်သော အစပြုမှုတစ်ခု ဖြစ်နိုင်ပါသည်။ dashboard သည် deploy လုပ်ထားသော object များကို modify လုပ်ရန် ဖြစ်နိုင်ခြေကိုလည်း ပေးပြီး action တစ်ခုစီအတွက် တူညီသော `kubectl` command ကို ပြသပါသည် - အစပြုသူအများစု တန်ဖိုးထားမည့် feature တစ်ခု ဖြစ်ပါသည်။

*ပုံ ၃.၇ သည် cluster တွင် deploy လုပ်ထားသော workload နှစ်ခုပါသော dashboard ကို ပြသပါသည်။*

*ပုံ ၃.၇ Kubernetes web-based dashboard ၏ Screenshot*

သင်သည် ဤစာအုပ်တွင် dashboard ကို အသုံးမပြုသော်လည်း၊ သင်သည် `kubectl` မှတစ်ဆင့် object များကို ဖန်တီးပြီးနောက် သင်၏ cluster တွင် deploy လုပ်ထားသော object များ၏ graphical view ကို အမြန်ကြည့်ရှုရန် ၎င်းကို အမြဲတမ်း ဖွင့်နိုင်ပါသည်။

**Docker Desktop တွင် dashboard ကို Access လုပ်ခြင်း**

ကံမကောင်းစွာဖြင့်၊ Docker Desktop သည် Kubernetes dashboard ကို default အားဖြင့် install မလုပ်ပါ။ ၎င်းကို access လုပ်ခြင်းသည်လည်း မလွယ်ကူပါ၊ သို့သော် ဤတွင် မည်သို့လုပ်ဆောင်ရမည်ကို ဖော်ပြထားပါသည်။ ပထမဦးစွာ၊ သင်သည် ၎င်းကို အောက်ပါ command ကို အသုံးပြု၍ install လုပ်ရန် လိုအပ်သည်-

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc5/aio/deploy/recommended.yaml
```

နောက်ဆုံး version number ကို ရှာဖွေရန် github.com/kubernetes/dashboard ကို ကိုးကားပါ။ dashboard ကို install လုပ်ပြီးနောက်၊ သင် run ရမည့် နောက် command မှာ-

```bash
$ kubectl proxy
```

ဤ command သည် API server သို့ local proxy တစ်ခု run စေပြီး၊ သင်သည် ၎င်းမှတစ်ဆင့် service များကို access လုပ်နိုင်စေပါသည်။ proxy process ကို run နေစေပြီး browser ကို အသုံးပြု၍ dashboard ကို အောက်ပါ URL တွင် ဖွင့်ပါ-
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

သင်သည် authentication page တစ်ခုသို့ ရောက်ရှိပါလိမ့်မည်။ သင်သည် authentication token တစ်ခုကို retrieve လုပ်ရန် အောက်ပါ command ကို run ရပါမည်။

```powershell
PS C:\> kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | sls admin-user | ForEach-Object { $_ -Split '\s+' } | Select -First 1)
```

> **မှတ်ချက်**
> ဤ command ကို Windows PowerShell တွင် run ရပါမည်။

`kubernetes-dashboard-token-xyz` အောက်တွင် စာရင်းပြုစုထားသော token ကို ရှာဖွေပြီး သင်၏ browser တွင် ပြထားသော authentication page ရှိ token field ထဲသို့ paste လုပ်ပါ။ ဤသို့ပြုလုပ်ပြီးနောက်၊ သင်သည် dashboard ကို အသုံးပြုနိုင်သင့်ပါသည်။ သင်သည် ၎င်းကို အသုံးပြုပြီးသည့်အခါ၊ `kubectl proxy` process ကို `Control-C` ကို အသုံးပြု၍ ရပ်ဆိုင်းပါ။

**Minikube အသုံးပြုသည့်အခါ dashboard ကို Access လုပ်ခြင်း**

အကယ်၍ သင်သည် Minikube ကို အသုံးပြုနေပါက... ... kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard ရှိ guide ကို လိုက်နာခြင်းဖြင့်။

## ၃.၃ သင်၏ ပထမဆုံး Application ကို Kubernetes တွင် Run ခြင်း

ယခုအချိန်သည် သင်၏ cluster သို့ တစ်စုံတစ်ခုကို နောက်ဆုံးတွင် deploy လုပ်ရန် အချိန်ကျရောက်ပြီဖြစ်သည်။ များသောအားဖြင့်၊ application တစ်ခုကို deploy လုပ်ရန်၊ သင်သည် သင်၏ application တွင် ပါဝင်သော component အားလုံးကို ဖော်ပြသည့် JSON သို့မဟုတ် YAML file တစ်ခုကို ပြင်ဆင်ပြီး ထို file ကို သင်၏ cluster သို့ apply လုပ်ရမည်ဖြစ်သည်။ ဤသည်မှာ declarative approach ဖြစ်သည်။

ဤသည်မှာ သင်၏ Kubernetes တွင် application တစ်ခုကို ပထမဆုံးအကြိမ် deploy လုပ်ခြင်း ဖြစ်နိုင်သောကြောင့်၊ ဤသို့လုပ်ဆောင်ရန် ပိုမိုလွယ်ကူသောနည်းလမ်းတစ်ခုကို ရွေးချယ်ကြပါစို့။ ကျွန်ုပ်တို့သည် သင်၏ application ကို deploy လုပ်ရန် ရိုးရှင်းသော၊ တစ်ကြောင်းတည်း imperative command များကို အသုံးပြုပါမည်။

### ၃.၃.၁ သင်၏ Application ကို Deploy လုပ်ခြင်း

application တစ်ခုကို deploy လုပ်ရန် imperative နည်းလမ်းမှာ `kubectl create deployment` command ကို အသုံးပြုခြင်း ဖြစ်ပါသည်။ command ကိုယ်တိုင်က ညွှန်ပြသည့်အတိုင်း၊ ၎င်းသည် cluster တွင် deploy လုပ်ထားသော application တစ်ခုကို ကိုယ်စားပြုသည့် Deployment object တစ်ခုကို ဖန်တီးပါသည်။ imperative command ကို အသုံးပြုခြင်းဖြင့်၊ သင်သည် YAML သို့မဟုတ် JSON manifest များကို ရေးသားသည့်အခါကဲ့သို့ Deployment object များ၏ structure ကို သိရန် မလိုအပ်တော့ပါ။

**Deployment တစ်ခု ဖန်တီးခြင်း**

```bash
$ kubectl create deployment kiada --image=luksa/kiada:0.1
deployment.apps/kiada created
```

command တွင်၊ သင်သည် အရာသုံးခုကို သတ်မှတ်သည်-

1.  သင်သည် `deployment` object တစ်ခု ဖန်တီးလိုသည်။
2.  သင်သည် object ကို `kiada` ဟုခေါ်လိုသည်။
3.  သင်သည် deployment ကို container image `luksa/kiada:0.1` ကို အသုံးပြုစေလိုသည်။

default အားဖြင့်၊ image ကို Docker Hub မှ pull လုပ်သော်လည်း၊ သင်သည် image name တွင် image registry ကိုလည်း သတ်မှတ်နိုင်သည် (ဥပမာ `quay.io/luksa/kiada:0.1`)။

> **မှတ်ချက်**
> image သည် public registry တစ်ခုတွင် သိမ်းဆည်းထားပြီး access authorization မပါဘဲ pull လုပ်နိုင်ကြောင်း သေချာပါစေ။ private image များကို pull လုပ်ရန် credential များကို မည်သို့ပေးရမည်ကို အခန်း ၈ တွင် သင်လေ့လာရပါမည်။

Deployment object ကို ယခု Kubernetes API တွင် သိမ်းဆည်းထားပါသည်။ ဤ object ၏ တည်ရှိမှုသည် Kubernetes အား `luksa/kiada:0.1` container ကို သင်၏ cluster တွင် run ရမည်ဖြစ်ကြောင်း ပြောပြပါသည်။ သင်သည် သင်၏ လိုလားသော state ကို ဖော်ပြခဲ့ပြီးဖြစ်သည်။ Kubernetes သည် ယခု အမှန်တကယ် state သည် သင်၏ ဆန္ဒများကို ထင်ဟပ်ကြောင်း သေချာစေရပါမည်။

**Deployment များကို စာရင်းပြုစုခြင်း**

Kubernetes နှင့် အပြန်အလှန်ဆက်သွယ်ခြင်းသည် ၎င်း၏ API မှတစ်ဆင့် object များကို ဖန်တီးခြင်းနှင့် manipulate လုပ်ခြင်းတို့တွင် အဓိက ပါဝင်ပါသည်။ ... cluster တွင် လက်ရှိတည်ရှိနေသော အရာများ။ သင်၏ cluster တွင် Deployment တစ်ခုတည်းသာ ရှိသည်။ ၎င်းသည် `UP-TO-DATE` column တွင် ပြထားသည့်အတိုင်း သင်၏ application ၏ instance တစ်ခုကို run သော်လည်း၊ `AVAILABLE` column သည် application သည် မရရှိနိုင်သေးကြောင်း ညွှန်ပြပါသည်။ ၎င်းသည် `READY` column တွင် ပြထားသည့်အတိုင်း container သည် အဆင်သင့်မဖြစ်သေးသောကြောင့် ဖြစ်သည်။ သင်သည် စုစုပေါင်း container တစ်ခုမှ သုညသည် အဆင်သင့်ဖြစ်ကြောင်း တွေ့မြင်နိုင်ပါသည်။

သင်သည် `kubectl get containers` ကို run ၍ Kubernetes အား run နေသော container အားလုံးကို စာရင်းပြုစုခိုင်းနိုင်ခြင်း ရှိမရှိ သင် သိချင်နေပေမည်။ ဤသို့ စမ်းကြည့်ကြပါစို့။

```bash
$ kubectl get containers
error: the server doesn't have a resource type "containers"
```

command သည် Kubernetes တွင် "Container" object type မရှိသောကြောင့် fail ဖြစ်ပါသည်။ ဤသည်မှာ Kubernetes သည် container များ run ခြင်းနှင့် ပတ်သက်နေသောကြောင့် ထူးဆန်းပုံရနိုင်သော်လည်း၊ အနည်းငယ်ကွဲလွဲမှု ရှိပါသည်။ container သည် Kubernetes တွင် deployment ၏ အသေးငယ်ဆုံး unit မဟုတ်ပါ။ ဒါဆို ဘာလဲ?

**Pods များကို မိတ်ဆက်ခြင်း**

Kubernetes တွင်၊ container တစ်ခုချင်းစီကို deploy လုပ်မည့်အစား၊ သင်သည် co-located container များအုပ်စု—pods ဟုခေါ်သော အရာများကို deploy လုပ်ပြီး manage လုပ်ပါသည်။ ဝေလငါးအုပ်စု (pod of whales) သို့မဟုတ် ပဲတောင့် (pea pod) ကဲ့သို့ သင်သိသည့်အတိုင်း ဖြစ်ပါသည်။

pod တစ်ခုသည်...

*ပုံ ၃.၈ တွင် ဖော်ပြထားသည့်အတိုင်း၊ သင်သည် pod တစ်ခုစီကို application တစ်ခုပါဝင်သော သီးခြား logical computer တစ်ခုအဖြစ် စဉ်းစားနိုင်ပါသည်။*

application တွင် container တစ်ခုတွင် run နေသော process တစ်ခုတည်း ပါဝင်နိုင်သည်၊ သို့မဟုတ် main application process နှင့် အပို supporting process များပါဝင်နိုင်ပြီး၊ တစ်ခုစီသည် သီးခြား container တစ်ခုတွင် run ပါသည်။ Pods များသည် cluster ၏ worker node အားလုံးတွင် ဖြန့်ကျက်ထားပါသည်။

pod တစ်ခုစီတွင် ၎င်း၏ကိုယ်ပိုင် IP, hostname, processes, network interface များနှင့် အခြား resource များ ရှိပါသည်။ pod တစ်ခုတည်း၏ အစိတ်အပိုင်းဖြစ်သော Container များသည် ၎င်းတို့သည် computer တွင် တစ်ခုတည်းသာ run နေသည်ဟု ထင်ကြပါသည်။ ၎င်းတို့သည် node တစ်ခုတည်းတွင် တည်ရှိနေသည့်တိုင်၊ အခြား pod တစ်ခု၏ process များကို မမြင်ရပါ။

**Pods များကို စာရင်းပြုစုခြင်း**

Container များသည် top-level Kubernetes object မဟုတ်သောကြောင့်၊ သင်သည် ၎င်းတို့ကို စာရင်းပြုစု၍ မရပါ။ သို့သော် သင်သည် pod များကို စာရင်းပြုစုနိုင်ပါသည်။ `kubectl get pods` command ၏ အောက်ပါ output တွင် ပြထားသည့်အတိုင်း၊ Deployment object ကို ဖန်တီးခြင်းဖြင့်၊ သင်သည် pod တစ်ခု deploy လုပ်ခဲ့သည်-

```bash
$ kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
kiada-9d785b578-p449x  0/1     Pending   0          1m  #A
```

... status သည် `Pending` ဖြစ်နေသောကြောင့်၊ application သို့မဟုတ် container သည် run မနေသေးပါ။ ဤသည်မှာ pod တွင် container တစ်ခုတည်းရှိပြီး ၎င်းသည် အဆင်သင့်မဖြစ်သေးကြောင်း ညွှန်ပြသည့် `READY` column တွင်လည်း ဖော်ပြထားပါသည်။

pod သည် `Pending` ဖြစ်နေရခြင်းအကြောင်းရင်းမှာ pod ကို assign လုပ်ထားသော worker node သည် ၎င်းကို run နိုင်ရန်အတွက် container image ကို အရင် download လုပ်ရမည်ဖြစ်သောကြောင့် ဖြစ်ပါသည်။ download ပြီးဆုံးသွားသည့်အခါ၊ pod ၏ container ကို ဖန်တီးပြီး pod သည် `Running` state သို့ ဝင်ရောက်ပါသည်။

အကယ်၍ Kubernetes သည် image ကို registry မှ pull လုပ်၍မရပါက၊ `kubectl get pods` command သည် ၎င်းကို `STATUS` column တွင် ညွှန်ပြပါမည်။ အကယ်၍ သင်သည် သင်၏ကိုယ်ပိုင် image ကို အသုံးပြုနေပါက၊ ၎င်းကို Docker Hub တွင် public အဖြစ် mark လုပ်ထားကြောင်း သေချာပါစေ။ `docker pull` command ဖြင့် အခြား computer တစ်ခုတွင် image ကို manually pull လုပ်ကြည့်ပါ။

အကယ်၍ အခြားပြဿနာတစ်ခုသည် သင်၏ pod ကို run မရအောင် ဖြစ်စေပါက၊ သို့မဟုတ် သင်သည် pod အကြောင်း အချက်အလက်ပိုမို ကြည့်ရှုလိုပါက၊ သင်သည် အစောပိုင်းက ကြည့်ရှုခဲ့သည့်အတိုင်း `kubectl describe pod` command ကိုလည်း အသုံးပြုနိုင်သည်...

သင် `kubectl create` command ကို run သည့်အခါ၊ ၎င်းသည် Kubernetes API server သို့ HTTP request တစ်ခု ပို့ခြင်းဖြင့် cluster တွင် Deployment object အသစ်တစ်ခုကို ဖန်တီးပါသည်။ Kubernetes သည် ထို့နောက် Pod object အသစ်တစ်ခုကို ဖန်တီးပြီး၊ ၎င်းကို worker node တစ်ခုသို့ assign လုပ် သို့မဟုတ် schedule လုပ်ပါသည်။ worker node ပေါ်ရှိ Kubernetes agent (Kubelet) သည် အသစ်ဖန်တီးထားသော Pod object ကို သတိပြုမိပြီး၊ ၎င်းသည် ၎င်း၏ node သို့ schedule လုပ်ထားသည်ကို မြင်ကာ Docker အား သတ်မှတ်ထားသော image ကို registry မှ pull လုပ်ရန်၊ image မှ container တစ်ခု ဖန်တီးရန်၊ နှင့် ၎င်းကို execute လုပ်ရန် ညွှန်ကြားပါသည်။

> **DEFINITION**
> `scheduling` ဟူသော ဝေါဟာရသည် pod ကို node တစ်ခုသို့ assign လုပ်ခြင်းကို ရည်ညွှန်းပါသည်။ pod သည် ချက်ချင်း run ပြီး၊ အနာဂတ်တွင် တစ်ချိန်ချိန်တွင် မဟုတ်ပါ။ operating system ရှိ CPU scheduler သည် process တစ်ခုကို မည်သည့် CPU တွင် run ရမည်ကို ရွေးချယ်သကဲ့သို့၊ Kubernetes ရှိ scheduler သည် container တစ်ခုစီကို မည်သည့် worker node တွင် execute လုပ်သင့်သည်ကို ဆုံးဖြတ်ပါသည်။ OS process နှင့် မတူဘဲ၊ pod တစ်ခုကို node တစ်ခုသို့ assign လုပ်ပြီးသည်နှင့်၊ ၎င်းသည် ထို node တွင်သာ run ပါသည်။ ၎င်း fail ဖြစ်သည့်တိုင်၊ ဤ pod ၏ instance ကို အခြား node များသို့ ဘယ်တော့မှ မရွှေ့ပါ၊ CPU process...

သင်၏ Kubernetes cluster ကို run ရန် သင်အသုံးပြုသောအရာပေါ် မူတည်၍၊ သင်၏ cluster ရှိ worker node အရေအတွက်သည် ကွဲပြားနိုင်ပါသည်။ ပုံသည် pod ကို schedule လုပ်ထားသော worker node ကိုသာ ပြသပါသည်။ multi-node cluster တွင်၊ အခြား worker node တစ်ခုမျှ ဤ process တွင် မပါဝင်ပါ။

### ၃.၃.၂ သင်၏ Application ကို အများသုံးနိုင်ရန် Expose လုပ်ခြင်း

သင်၏ application သည် ယခု run နေပြီဖြစ်သောကြောင့်၊ နောက်ဖြေရှင်းရမည့်မေးခွန်းမှာ ၎င်းကို မည်သို့ access လုပ်ရမည်နည်း။ pod တစ်ခုစီသည် ၎င်း၏ကိုယ်ပိုင် IP address ရရှိကြောင်း ကျွန်ုပ်ဖော်ပြခဲ့သော်လည်း၊ ဤ address သည် cluster အတွင်းပိုင်းတွင်သာ ရှိပြီး အပြင်မှ access လုပ်၍မရပါ။ pod ကို အပြင်မှ access လုပ်နိုင်စေရန်၊ သင်သည် ၎င်းကို Service object တစ်ခု ဖန်တီးခြင်းဖြင့် expose လုပ်ပါမည်။

Service object type အများအပြား ရှိပါသည်။ သင်လိုအပ်သော type ကို သင် ဆုံးဖြတ်ပါသည်။ အချို့သည် pod များကို cluster အတွင်းတွင်သာ expose လုပ်ပြီး၊ အချို့သည် ၎င်းတို့ကို အပြင်မှ expose လုပ်ပါသည်။ `LoadBalancer` type ပါသော service တစ်ခုသည် external load balancer တစ်ခုကို provision လုပ်ပြီး၊ ၎င်းသည် service ကို public IP မှတစ်ဆင့် access လုပ်နိုင်စေပါသည်။ ဤသည်မှာ သင်ယခု ဖန်တီးမည့် service type ဖြစ်သည်။

**Service တစ်ခု ဖန်တီးခြင်း**

service ကို ဖန်တီးရန် အလွယ်ကူဆုံးနည်းလမ်းမှာ အောက်ပါအတိုင်း... ... Deployment ကို။

**Service များကို စာရင်းပြုစုခြင်း**

Service များသည် Pods, Deployments, Nodes နှင့် Kubernetes ရှိ အခြားအရာအားလုံးနီးပါးကဲ့သို့ API object များဖြစ်သောကြောင့်၊ သင်သည် `kubectl get services` ကို execute လုပ်၍ ၎င်းတို့ကို စာရင်းပြုစုနိုင်သည်-

```bash
$ kubectl get svc
NAME        TYPE          CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes  ClusterIP     10.19.240.1    <none>        443/TCP          34m
kiada       LoadBalancer  10.19.243.17   <pending>     8080:30838/TCP   4s
```

> **မှတ်ချက်**
> `svc` ကို `services` အစား အတိုကောက်အဖြစ် အသုံးပြုသည်ကို သတိပြုပါ။ resource type အများစုတွင် object type အပြည့်အစုံအစား သင်အသုံးပြုနိုင်သော short name တစ်ခု ရှိသည် (ဥပမာ `po` သည် pods အတွက်၊ `no` သည် nodes အတွက်၊ နှင့် `deploy` သည် deployments အတွက် ဖြစ်သည်)။

စာရင်းသည် ၎င်းတို့၏ type, IP များနှင့် ၎င်းတို့ expose လုပ်သော port များပါသော service နှစ်ခုကို ပြသပါသည်။ `kubernetes` service ကို ယခုအချိန်တွင် လျစ်လျူရှုပြီး `kiada` service ကို အနီးကပ်ကြည့်ပါ။ ၎င်းတွင် external IP address မရှိသေးပါ။ ၎င်းရရှိခြင်း ရှိမရှိသည် သင်သည် cluster ကို မည်သို့ deploy လုပ်ခဲ့သည်ပေါ် မူတည်ပါသည်။

**`kubectl api-resources` ဖြင့် ရရှိနိုင်သော object type များကို စာရင်းပြုစုခြင်း**

သင်သည် `kubec...` ကို အသုံးပြုခဲ့သည်... ... balancer ကို provision လုပ်ပြီး ၎င်းကို သင်၏ cluster သို့ traffic forward လုပ်ရန် configure လုပ်ပါသည်။ infrastructure သည် Kubernetes အား load balancer ၏ IP address ကို ပြောပြပြီး ဤသည်မှာ သင်၏ service ၏ external address ဖြစ်လာပါသည်။

Service object ကို ဖန်တီးခြင်း၊ load balancer ကို provision လုပ်ခြင်း၊ နှင့် ၎င်းသည် connection များကို cluster ထဲသို့ မည်သို့ forward လုပ်သည်ကို အောက်ပါပုံတွင် ပြထားပါသည်။

*ပုံ ၃.၁၀ LoadBalancer type ၏ Service object တစ်ခုကို ဖန်တီးသည့်အခါ ဘာဖြစ်သွားသလဲ*

load balancer ကို provision လုပ်ခြင်းသည် အချိန်အနည်းငယ် ကြာသောကြောင့်၊ နောက်ထပ် စက္ကန့်အနည်းငယ် စောင့်ပြီး IP address assign လုပ်ပြီးပြီလားဆိုတာကို ပြန်စစ်ဆေးကြပါစို့။ ဤတစ်ကြိမ်တွင်၊ service အားလုံးကို စာရင်းပြုစုမည့်အစား၊ သင်သည် `kiada` service ကိုသာ အောက်ပါအတိုင်း ပြသပါမည်-

```bash
$ kubectl get svc kiada
NAME    TYPE          CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
kiada   LoadBalancer  10.19.243.17   35.246.179.22  8080:30838/TCP   82s
```

external IP ကို ယခု ပြသထားပါသည်။ ဤသည်မှာ load balancer သည် ကမ္ဘာတစ်ဝှမ်းရှိ client များအတွက် သင်၏ application သို့ request များကို forward လုပ်ရန် အသင့်ဖြစ်နေပြီဟု ဆိုလိုပါသည်။

> **မှတ်ချက်**
> အကယ်၍ သင်သည် သင်၏ cluster ကို deploy လုပ်ခဲ့ပါက...
> ... address ကို `localhost` အဖြစ် ပြသပြီး၊ Kubernetes နှင့် application run နေသော VM ကို မဟုတ်ဘဲ၊ သင်၏ Windows သို့မဟုတ် macOS machine ကို ရည်ညွှန်းပါသည်။ အကယ်၍ သင်သည် cluster ဖန်တီးရန် Minikube ကို အသုံးပြုပါက၊ load balancer တစ်ခုမျှ ဖန်တီးခြင်း မရှိသော်လည်း၊ သင်သည် service ကို အခြားနည်းလမ်းဖြင့် access လုပ်နိုင်ပါသည်။ ဤအကြောင်းကို နောက်ပိုင်းတွင် ပိုမိုဖော်ပြပါမည်။

**သင်၏ application ကို load balancer မှတစ်ဆင့် Access လုပ်ခြင်း**

သင်သည် ယခု သင်၏ application သို့ service ၏ external IP နှင့် port မှတစ်ဆင့် request များ ပို့နိုင်ပြီဖြစ်သည်-

```bash
$ curl 35.246.179.22:8080
Kiada version 0.1. Request processed by "kiada-9d785b578-p449x". Client IP: ::ffff:1.2.3.4
```

> **မှတ်ချက်**
> အကယ်၍ သင်သည် Docker Desktop ကို အသုံးပြုပါက၊ service သည် သင်၏ host operating system အတွင်းမှ `localhost:8080` တွင် ရရှိနိုင်ပါသည်။ ၎င်းကို access လုပ်ရန် `curl` သို့မဟုတ် သင်၏ browser ကို အသုံးပြုပါ။

ဂုဏ်ယူပါသည်။! အကယ်၍ သင်သည် Google Kubernetes Engine ကို အသုံးပြုပါက၊ သင်သည် သင်၏ application ကို ကမ္ဘာတစ်ဝှမ်းရှိ user များထံ အောင်မြင်စွာ ထုတ်ဝေလိုက်ပြီဖြစ်သည်။ ၎င်း၏ IP နှင့် port ကို သိသော မည်သူမဆို ယခု ၎င်းကို access လုပ်နိုင်ပြီဖြစ်သည်။ အကယ်၍ သင်သည် cluster ကို deploy လုပ်ရန် လိုအပ်သော အဆင့်များကို မရေတွက်ပါက၊ သင်၏ application ကို deploy လုပ်ရန် ရိုးရှင်းသော command နှစ်ခုသာ လိုအပ်သည်- `kubectl create depl...` ... troubleshooting။ ဤသို့ မည်သို့လုပ်ဆောင်ရမည်ကို အခန်း ၅ တွင် သင်လေ့လာရပါမည်။ ယခုအချိန်တွင်၊ load balancer မရရှိနိုင်ပါက သင်၏ service ကို access လုပ်ရန် နောက်ထပ် ပိုမိုလွယ်ကူသောနည်းလမ်းကို လေ့လာကြပါစို့။

Minikube သည် အောက်ပါ command ကို အသုံးပြုပါက service ကို မည်သည့်နေရာတွင် access လုပ်ရမည်ကို သင့်အား ပြောပြနိုင်သည်-

```bash
$ minikube service kiada --url
http://192.168.99.102:30838
```

command သည် service ၏ URL ကို ထုတ်ပေးပါသည်။ သင်သည် ယခု သင်၏ application ကို access လုပ်ရန် ထို URL သို့ `curl` သို့မဟုတ် သင်၏ browser ကို ညွှန်ပြနိုင်သည်-

```bash
$ curl http://192.168.99.102:30838
Kiada version 0.1. Request processed by "kiada-9d785b578-p449x". Client IP: ::ffff:172.17.0.1
```

> **အကြံပြုချက်**
> အကယ်၍ သင်သည် `minikube service` command ကို run သည့်အခါ `--url` option ကို ချန်လှပ်ထားပါက၊ သင်၏ browser သည် ဖွင့်ပြီး service URL ကို load လုပ်ပါမည်။

ဤ IP address နှင့် port သည် မည်သည့်နေရာမှ လာသည်ကို သင် သိချင်နေပေမည်။ ဤသည်မှာ Minikube virtual machine ၏ IP ဖြစ်ပါသည်။ သင်သည် `minikube ip` command ကို execute လုပ်၍ ၎င်းကို confirm လုပ်နိုင်ပါသည်။ Minikube VM သည် သင်၏ single worker node လည်း ဖြစ်ပါသည်။ port `30838` သည် node port ဟုခေါ်သော အရာ ဖြစ်ပါသည်။ ၎င်းသည် connection များကို သင်၏ service သို့ forward လုပ်သော worker node ပေါ်ရှိ port ဖြစ်ပါသည်။ သင်သည်... ... container ကို အပိုင်း ၃.၁.၁ တွင် ဖော်ပြထားသည့်အတိုင်း။

အကယ်၍ သင်သည် သင်၏ worker node များထဲမှ အနည်းဆုံး တစ်ခု၏ IP ကို သိပါက၊ firewall rule များက သင် port ကို access လုပ်ခြင်းကို မတားဆီးပါက၊ သင်သည် ဤ IP:port combination မှတစ်ဆင့် သင်၏ service ကို access လုပ်နိုင်သင့်ပါသည်။

နောက်ပုံသည် external client များသည် node port များမှတစ်ဆင့် application ကို မည်သို့ access လုပ်သည်ကို ပြသပါသည်။

*ပုံ ၃.၁၁ Service ၏ node port မှတစ်ဆင့် Connection routing*

ဤသည်ကို ကျွန်ုပ် အစောပိုင်းက load balancer သည် connection များကို node များသို့ forward လုပ်ပြီး node များသည် ထို့နောက် ၎င်းတို့ကို container များသို့ forward လုပ်သည်ဟု ဖော်ပြခဲ့သည့်အရာနှင့် ချိတ်ဆက်ရန်- node port များသည် load balancer သည် incoming request များကို ပို့သည့်နေရာ အတိအကျ ဖြစ်ပါသည်။ Kubernetes သည် ထို့နောက် ၎င်းတို့ကို container တွင် run နေသော application သို့ forward လုပ်ကြောင်း သေချာစေပါသည်။ ၎င်းသည် ဤသို့ မည်သို့လုပ်ဆောင်သည်ကို အခန်း ၁၀ တွင် သင်လေ့လာရပါမည်၊ ၎င်းတွင် ကျွန်ုပ်တို့သည် service များအကြောင်း ပိုမိုနက်ရှိုင်းစွာ လေ့လာပါမည်။ ထိုအချိန်အထိ ၎င်းအကြောင်း များများစားစား မစဉ်းစားပါနှင့်။ အစား၊ Kubernetes သည် အခြား ဘာလုပ်နိုင်သေးသည်ကို ကြည့်ရန် ကျွန်ုပ်တို့၏ cluster နှင့် အနည်းငယ် ပိုကစားကြပါစို့။

### ၃.၃.၃ Application ကို Horizontally Scale လုပ်ခြင်း

သင်သည် ယခု run နေသော application တစ်ခု ရှိသည်... ... သင်သည် သင်၏ application deployment များကို scale လုပ်နိုင်သည့်။ သင်သည် လက်ရှိတွင် သင်၏ application ၏ instance တစ်ခုတည်းကို run နေပါသည်။ သင်၏ application ကို အသုံးပြုသော user များ ပိုမိုများပြားလာသည်ကို သင် ရုတ်တရက် တွေ့မြင်ရသည်ဟု စဉ်းစားပါ။ instance တစ်ခုတည်းသည် load ကို မကိုင်တွယ်နိုင်တော့ပါ။ load ကို ဖြန့်ကျက်ပြီး သင်၏ user များထံ service ပေးရန် အပို instance များကို run ရန် လိုအပ်ပါသည်။ ဤသည်ကို scaling out ဟုခေါ်ပါသည်။ Kubernetes ဖြင့်၊ ၎င်းသည် လွယ်ကူပါသည်။

**run နေသော application instance အရေအတွက်ကို တိုးမြှင့်ခြင်း**

သင်၏ application ကို deploy လုပ်ရန်၊ သင်သည် Deployment object တစ်ခုကို ဖန်တီးခဲ့သည်။ default အားဖြင့်၊ ၎င်းသည် သင်၏ application ၏ instance တစ်ခုတည်းကို run ပါသည်။ အပို instance များကို run ရန်၊ သင်သည် Deployment object ကို အောက်ပါ command ဖြင့် scale လုပ်ရန်သာ လိုအပ်သည်-

```bash
$ kubectl scale deployment kiada --replicas=3
deployment.apps/kiada scaled
```

သင်သည် ယခု Kubernetes အား သင်၏ pod ၏ တူညီသော copy သို့မဟုတ် replica သုံးခု run လိုကြောင်း ပြောခဲ့သည်။ သင်သည် Kubernetes အား ဘာလုပ်ရမည်ကို ညွှန်ကြားခြင်း မရှိကြောင်း သတိပြုပါ။ သင်သည် pod နှစ်ခု ထပ်ထည့်ရန် ၎င်းကို မပြောခဲ့ပါ။ သင်သည် replica အရေအတွက်အသစ်ကိုသာ set လုပ်ပြီး Kubernetes အား ဆုံးဖြတ်ခွင့် ပေးခဲ့သည်...

```
... NAME kiada READY 3/3 UP-TO-DATE 3 AVAILABLE 3 AGE 18m
```

instance သုံးခုသည် ယခု up to date ဖြစ်ပြီး available ဖြစ်နေပြီး container သုံးခုမှ သုံးခုသည် ready ဖြစ်နေသည်။ ဤသည်မှာ command output မှ မရှင်းလင်းသော်လည်း၊ container သုံးခုသည် pod instance တစ်ခုတည်း၏ အစိတ်အပိုင်း မဟုတ်ပါ။ container တစ်ခုစီပါသော pod သုံးခု ရှိပါသည်။ သင်သည် pod များကို စာရင်းပြုစုခြင်းဖြင့် ၎င်းကို confirm လုပ်နိုင်သည်-

```bash
$ kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
kiada-9d785b578-58vhc  1/1     Running   0          17s
kiada-9d785b578-jmnj8  1/1     Running   0          17s
kiada-9d785b578-p449x  1/1     Running   0          18m
```

သင်မြင်သည့်အတိုင်း၊ pod သုံးခု ယခု ရှိနေသည်။ `READY` column တွင် ညွှန်ပြသည့်အတိုင်း၊ တစ်ခုစီတွင် container တစ်ခုတည်း ရှိပြီး၊ container အားလုံးသည် ready ဖြစ်နေသည်။ pod အားလုံးသည် `Running` ဖြစ်နေသည်။

**pod များကို စာရင်းပြုစုသည့်အခါ pod များ၏ host node ကို ပြသခြင်း**

အကယ်၍ သင်သည် single-node cluster တစ်ခုကို အသုံးပြုပါက၊ သင်၏ pod အားလုံးသည် node တစ်ခုတည်းတွင် run ပါသည်။ သို့သော် multi-node cluster တွင်၊ pod သုံးခုသည် cluster တစ်ခုလုံးတွင် ဖြန့်ကျက်ထားသင့်သည်။ pod များ run နေသော node များကို ကြည့်ရန်...

**host node သည် အရေးကြီးပါသလား?**

၎င်းတို့ run နေသော node မည်သို့ပင်ဖြစ်စေ၊ သင်၏ application ၏ instance အားလုံးသည် တူညီသော container image မှ ဖန်တီးထားသော container များတွင် run သောကြောင့် တူညီသော OS environment ရှိပါသည်။ သင်သည် ယခင်အခန်းမှ မတူညီသော OS kernel တစ်ခုတည်းသာ ဖြစ်နိုင်သည်ကို မှတ်မိနိုင်သော်လည်း၊ ဤသည်မှာ မတူညီသော node များက မတူညီသော kernel version များကို အသုံးပြုသည့်အခါ သို့မဟုတ် မတူညီသော kernel module များကို load လုပ်သည့်အခါတွင်သာ ဖြစ်ပေါ်ပါသည်။

ထို့အပြင်၊ pod တစ်ခုစီသည် ၎င်း၏ကိုယ်ပိုင် IP ရရှိပြီး အခြား pod တစ်ခုနှင့် တူညီသောနည်းလမ်းဖြင့် ဆက်သွယ်နိုင်သည် - အခြား pod သည် worker node တစ်ခုတည်းတွင် ရှိခြင်း၊ server rack တစ်ခုတည်းရှိ အခြား node တွင် ရှိခြင်း၊ သို့မဟုတ် data center လုံးဝ မတူညီသည့်နေရာတွင် ရှိခြင်းသည် အရေးမကြီးပါ။

ယခုအချိန်အထိ၊ သင်သည် pod များအတွက် resource requirement များကို set မလုပ်သေးသော်လည်း၊ သင်သည် ၎င်းတို့ကို set လုပ်ခဲ့ပါက၊ pod တစ်ခုစီသည် တောင်းဆိုထားသော compute resource ပမာဏကို allocate လုပ်ခံရမည်ဖြစ်သည်။ pod ၏ requirement များကို ဖြည့်ဆည်းပေးသရွေ့၊ မည်သည့် node က ဤ resource များကို ပံ့ပိုးပေးသည်ကို pod အတွက် အရေးမကြီးသင့်ပါ။

ထို့ကြောင့်၊ pod တစ်ခုကို မည်သည့်နေရာတွင် schedule လုပ်သည်ကို သင် ဂရုမစိုက်သင့်ပါ။ ၎င်းသည် default `kubectl` ...
... instance တစ်ခုစီကို အကြိမ်တိုင်း? ဤတွင် အဖြေဖြစ်သည်-

```bash
$ curl 35.246.179.22:8080
Kiada version 0.1. Request processed by "kiada-9d785b578-58vhc". Client IP: ::ffff:1.2.3.4 #A
$ curl 35.246.179.22:8080
Kiada version 0.1. Request processed by "kiada-9d785b578-p449x". Client IP: ::ffff:1.2.3.4 #B
$ curl 35.246.179.22:8080
Kiada version 0.1. Request processed by "kiada-9d785b578-jmnj8". Client IP: ::ffff:1.2.3.4 #C
$ curl 35.246.179.22:8080
Kiada version 0.1. Request processed by "kiada-9d785b578-p449x". Client IP: ::ffff:1.2.3.4 #D
```

အကယ်၍ သင်သည် response များကို အနီးကပ်ကြည့်ရှုပါက၊ ၎င်းတို့သည် pod များ၏ name များနှင့် ကိုက်ညီသည်ကို သင်တွေ့ရပါမည်။ request တစ်ခုစီသည် random order ဖြင့် မတူညီသော pod တစ်ခုသို့ ရောက်ရှိပါသည်။ ဤသည်မှာ Kubernetes ရှိ service များသည် ၎င်းတို့၏နောက်တွင် pod instance တစ်ခုထက်ပို၍ ရှိသည့်အခါ လုပ်ဆောင်သည့်အရာ ဖြစ်သည်။ ၎င်းတို့သည် pod များ၏ရှေ့တွင် load balancer များအဖြစ် လုပ်ဆောင်ပါသည်။ အောက်ပါပုံကို အသုံးပြု၍ system ကို မြင်ယောင်ကြည့်ကြပါစို့။

*ပုံ ၃.၁၂ Pod အများအပြားတွင် Load balancing ပြုလုပ်ခြင်း*

ပုံတွင် ပြထားသည့်အတိုင်း၊ သင်သည် ဤ load balancing... ... Kubernetes service ကိုယ်တိုင်မှ ပံ့ပိုးပေးသော၊ GKE သို့မဟုတ် cloud တွင် run နေသော အခြား cluster တွင် run သည့်အခါ infrastructure မှ ပံ့ပိုးပေးသော အပို load balancer နှင့်။ သင်သည် Minikube ကို အသုံးပြုပြီး external load balancer မရှိသည့်တိုင်၊ သင်၏ request များကို service ကိုယ်တိုင်မှ pod သုံးခုတွင် ဖြန့်ကျက်ထားပါသည်။ အကယ်၍ သင်သည် GKE ကို အသုံးပြုပါက၊ အမှန်တကယ်တွင် load balancer နှစ်ခု ရှိပါသည်။ ပုံသည် infrastructure မှ ပံ့ပိုးပေးသော load balancer သည် request များကို node များတွင် ဖြန့်ကျက်ပြီး၊ service သည် ထို့နောက် request များကို pod များတွင် ဖြန့်ကျက်သည်ကို ပြသပါသည်။

ကျွန်ုပ်သိပါသည်၊ ဤသည်မှာ ယခုအချိန်တွင် အလွန် ရှုပ်ထွေးနိုင်သော်လည်း၊ ၎င်းသည် အခန်း ၁၀ တွင် အားလုံး ရှင်းလင်းသွားသင့်ပါသည်။

### ၃.၃.၄ Deploy လုပ်ထားသော Application ကို နားလည်ခြင်း

ဤအခန်းကို အဆုံးသတ်ရန်၊ သင်၏ system တွင် ပါဝင်သောအရာများကို ပြန်လည်သုံးသပ်ကြပါစို့။ သင်၏ system ကို ကြည့်ရန် နည်းလမ်းနှစ်မျိုး ရှိသည် - logical နှင့် physical view။ သင်သည် ပုံ ၃.၁၂ တွင် physical view ကို မြင်တွေ့ခဲ့ပြီးဖြစ်သည်။ Minikube ကို အသုံးပြုသည့်အခါတွင် node တစ်ခုတည်းပေါ်တွင် deploy လုပ်ထားသော run နေသော container သုံးခု ရှိသည်။ အကယ်၍ သင်သည် Kuber...

*ပုံ ၃.၁၃ သင်၏ deploy လုပ်ထားသော application တွင် Deployment, Pods အများအပြား, နှင့် Service တစ်ခု ပါဝင်သည်။*

object များမှာ အောက်ပါအတိုင်းဖြစ်သည်-

*   သင်ဖန်တီးခဲ့သော Deployment object၊
*   Deployment အပေါ် အခြေခံ၍ အလိုအလျောက် ဖန်တီးခဲ့သော Pod object များ၊ နှင့်
*   သင် manually ဖန်တီးခဲ့သော Service object။

ဖော်ပြခဲ့သော သုံးခုအကြားတွင် အခြား object များ ရှိသော်လည်း၊ သင်သည် ၎င်းတို့ကို ယခုအချိန်တွင် သိရန် မလိုအပ်သေးပါ။ သင်သည် ၎င်းတို့အကြောင်းကို နောက်ပိုင်းအခန်းများတွင် လေ့လာရပါမည်။

အခန်း ၁ တွင် Kubernetes သည် infrastructure ကို abstract လုပ်သည်ဟု ကျွန်ုပ်ရှင်းပြခဲ့သည်ကို သတိရပါ။ သင်၏ application ၏ logical view သည် ဤအရာ၏ ကောင်းမွန်သော ဥပမာတစ်ခု ဖြစ်ပါသည်။ node များ၊ ရှုပ်ထွေးသော network topology၊ physical load balancer များ မရှိပါ။ သင်၏ application များနှင့် supporting object များကိုသာ ပါဝင်သော ရိုးရှင်းသော view တစ်ခုသာ ဖြစ်သည်။ ဤ object များသည် အတူတကွ မည်သို့ အံဝင်ခွင်ကျဖြစ်ပြီး သင်၏ သေးငယ်သော setup တွင် မည်သည့် role ကို ကစားသည်ကို ကြည့်ကြပါစို့။

Deployment object သည် application deployment တစ်ခုကို ကိုယ်စားပြုပါသည်။ ၎င်းသည်... ... သင်၏ application ၏ replica မည်မျှကို Kubernetes က run သင့်သည်ကို သတ်မှတ်သည်။ replica တစ်ခုစီကို Pod object တစ်ခုဖြင့် ကိုယ်စားပြုပါသည်။ Service object သည် ဤ replica များသို့ communication entry point တစ်ခုတည်းကို ကိုယ်စားပြုပါသည်။

**Pods များကို နားလည်ခြင်း**

သင်၏ system ၏ အဓိကနှင့် အရေးကြီးဆုံးအစိတ်အပိုင်းမှာ pod များ ဖြစ်ပါသည်။ pod definition တစ်ခုစီတွင် pod ကို ဖွဲ့စည်းသည့် container တစ်ခု သို့မဟုတ် တစ်ခုထက်ပို၍ ပါဝင်ပါသည်။ Kubernetes သည် pod တစ်ခုကို အသက်ဝင်စေသည့်အခါ၊ ၎င်းသည် ၎င်း၏ definition တွင် သတ်မှတ်ထားသော container အားလုံးကို run ပါသည်။ Pod object တစ်ခု တည်ရှိနေသရွေ့၊ Kubernetes သည် ၎င်း၏ container များ ဆက်လက် run နေကြောင်း သေချာစေရန် အကောင်းဆုံး ကြိုးစားပါမည်။ ၎င်းသည် Pod object ကို ဖျက်ပစ်သည့်အခါတွင်သာ ၎င်းတို့ကို shut down လုပ်ပါသည်။

**Deployment ၏ Role ကို နားလည်ခြင်း**

သင်သည် Deployment object ကို ပထမဆုံး ဖန်တီးခဲ့သည့်အခါ၊ Pod object တစ်ခုတည်းသာ ဖန်တီးခဲ့သည်။ သို့သော် သင်သည် Deployment တွင် လိုလားသော replica အရေအတွက်ကို တိုးမြှင့်ခဲ့သည့်အခါ၊ Kubernetes သည် အပို replica များကို ဖန်တီးခဲ့သည်။ Kubernetes သည် အမှန်တကယ် pod အရေအတွက်သည် လိုလားသော အရေအတွက်နှင့် အမြဲတမ်း ကိုက်ညီကြောင်း သေချာစေပါသည်။

အကယ်၍ pod တစ်ခု သို့မဟုတ် တစ်ခုထက်ပို၍ ပျောက်ကွယ်သွားပါက သို့မဟုတ်...

**Service တစ်ခု အဘယ်ကြောင့် လိုအပ်သည်ကို နားလည်ခြင်း**

သင်၏ system ၏ တတိယ component မှာ Service object ဖြစ်ပါသည်။ ၎င်းကို ဖန်တီးခြင်းဖြင့်၊ သင်သည် သင်၏ pod များသို့ communication entry point တစ်ခုတည်း လိုအပ်ကြောင်း Kubernetes ကို ပြောပြပါသည်။ service သည် လက်ရှိ deploy လုပ်ထားသော replica အရေအတွက် မည်သို့ပင်ဖြစ်စေ၊ သင်၏ pod များနှင့် စကားပြောရန် IP address တစ်ခုတည်း ပေးပါသည်။ အကယ်၍ service ကို pod အများအပြားက support လုပ်ပါက၊ ၎င်းသည် load balancer အဖြစ် လုပ်ဆောင်ပါသည်။ သို့သော် pod တစ်ခုတည်းသာ ရှိသည့်တိုင်၊ သင်သည် ၎င်းကို service မှတစ်ဆင့် expose လုပ်လိုသေးသည်။ အဘယ်ကြောင့်ဆိုသည်ကို နားလည်ရန်၊ သင်သည် pod များအကြောင်း အရေးကြီးသော အသေးစိတ်အချက်တစ်ချက်ကို လေ့လာရန် လိုအပ်ပါသည်။

Pods များသည် ephemeral ဖြစ်ပါသည်။ pod တစ်ခုသည် အချိန်မရွေး ပျောက်ကွယ်သွားနိုင်ပါသည်။ ဤသည်မှာ ၎င်း၏ host node fail ဖြစ်သည့်အခါ၊ တစ်စုံတစ်ဦးက pod ကို မတော်တဆ ဖျက်ပစ်သည့်အခါ၊ သို့မဟုတ် အခြား ပိုမိုအရေးကြီးသော pod များအတွက် နေရာပေးရန် pod ကို ကျန်းမာသော node တစ်ခုမှ evict လုပ်သည့်အခါ ဖြစ်ပွားနိုင်ပါသည်။ ယခင်အပိုင်းတွင် ရှင်းပြခဲ့သည့်အတိုင်း၊ pod များကို Deployment မှတစ်ဆင့် ဖန်တီးသည့်အခါ၊ ပျောက်ဆုံးနေသော pod တစ်ခုကို အသစ်တစ်ခုဖြင့် ချက်ချင်း အစားထိုးပါသည်။ ဤအသစ် pod သည် ၎င်းအစားထိုးသော pod နှင့် တူညီသည် မဟုတ်ပါ။ ၎င်းသည် လုံးဝအသစ် pod တစ်ခုဖြစ်ပြီး၊ IP address အသစ်ဖြင့်...

## ၃.၄ အနှစ်ချုပ်

ဤအခန်းပြီးဆုံးသွားသည့်အခါ၊ သင်သည် Kubernetes cluster တစ်ခုကို ရယူပြီး အခြေခံ object သုံးမျိုးကို အသုံးပြု၍ သင်၏ ပထမဆုံး application ကို ၎င်းတွင် deploy လုပ်ခဲ့သည်။ သင်လေ့လာခဲ့သည်မှာ-

*   သင်သည် Kubernetes cluster တစ်ခုကို Google Kubernetes Engine (GKE) ကဲ့သို့သော managed Kubernetes service ကို အသုံးပြု၍ အလွယ်တကူ ရယူနိုင်သည်၊ ထိုနေရာတွင် cloud provider သည် သင်၏ကိုယ်စား Kubernetes ကို manage လုပ်ပြီး၊ သင်သည် သင်၏ application များကို deploy လုပ်ရန် ၎င်း၏ API ကိုသာ အသုံးပြုရပါသည်။
*   သင်သည် Kubernetes ကို cloud တွင် သင်ကိုယ်တိုင် install လုပ်နိုင်သော်လည်း၊ သင်သည် Kubernetes ကို manage လုပ်ခြင်း၏ အဘက်ဘက်ကို ကျွမ်းကျင်လာသည်အထိ ဤသည်မှာ အကောင်းဆုံး idea မဟုတ်ကြောင်း မကြာခဏ သက်သေပြခဲ့ပြီးဖြစ်သည်။
*   သင်သည် Kubernetes ကို Docker Desktop သို့မဟုတ် Minikube ကဲ့သို့သော tool များကို အသုံးပြု၍ သင်၏ laptop တွင်ပင် locally install လုပ်နိုင်ပြီး၊ ၎င်းသည် Kubernetes ကို Linux VM တွင် run စေသည်၊ သို့မဟုတ် kind ကို အသုံးပြု၍ master နှင့် worker node များကို Docker container များအဖြစ် run ပြီး application container များကို ထို container များအတွင်းတွင် run စေသည်။
*   command-line tool ဖြစ်သော `kubectl` သည် သင် Kubernetes နှင့် အပြန်အလှန် ဆက်သွယ်ရန် ပုံမှန်နည်းလမ်း ဖြစ်ပါသည်။ web-based dashboard တစ်ခုလည်း ရှိသော်လည်း၊ ၎င်းသည် CLI tool ကဲ့သို့ stable မဖြစ်ပြီး up to date မဖြစ်ပါ။
*   `kubectl` ဖြင့် ပိုမိုမြန်ဆန်စွာ အလုပ်လုပ်ရန်၊ ၎င်းအတွက် alias အတိုတစ်ခု define လုပ်ပြီး shell completion ကို enable လုပ်ခြင်းသည် အသုံးဝင်ပါသည်။
*   application တစ်ခုကို `kubectl create deployment` ကို အသုံးပြု၍ deploy လုပ်နိုင်ပါသည်။ ၎င်းကို `kubectl expose deployment` ကို run ၍ client များသို့ expose လုပ်နိုင်ပါသည်။
*   application ကို horizontally scale လုပ်ခြင်းသည် လွယ်ကူပါသည်- `kubectl scale deployment` သည် Kubernetes အား replica အသစ်များ ထည့်ရန် သို့မဟုတ် ရှိပြီးသား replica များကို ဖယ်ရှားရန် ညွှန်ကြားသည်
