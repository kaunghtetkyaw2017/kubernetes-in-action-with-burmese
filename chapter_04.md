# အခန်း ၄။ Kubernetes API object များအကြောင်း မိတ်ဆက်ခြင်း

ယခင်အခန်းတွင် deployed application တစ်ခုကို ဖွဲ့စည်းသည့် အခြေခံ object သုံးမျိုးကို မိတ်ဆက်ခဲ့ပါသည်။ သင်သည် `Deployment` object တစ်ခုကို ဖန်တီးခဲ့ပြီး၊ ၎င်း object က သင့် application ၏ instance တစ်ခုချင်းစီကို ကိုယ်စားပြုသော `Pod` object အများအပြားကို ထုတ်ပေးခဲ့သည်။ ပြီးနောက် load balancer တစ်ခုကို ၎င်းတို့ရှေ့တွင် ထားရှိပေးသော `Service` object တစ်ခုကို ဖန်တီးခြင်းဖြင့် ၎င်းတို့ကို ကမ္ဘာသို့ ချပြခဲ့သည်။

ဤစာအုပ်၏ ဒုတိယပိုင်းရှိ အခန်းများက ဤ object အမျိုးအစားများနှင့် အခြားအရာများကို အသေးစိတ် ရှင်းပြပါမည်။ ဤအခန်းတွင်၊ Kubernetes object များ၏ တူညီသောအင်္ဂါရပ်များကို `Node` နှင့် `Event` object များ၏ ဥပမာကို အသုံးပြု၍ တင်ပြထားပါသည်။

## 4.1 Kubernetes API နှင့် ရင်းနှီးကျွမ်းဝင်ခြင်း

Kubernetes cluster တစ်ခုတွင်၊ user များနှင့် Kubernetes component နှစ်မျိုးလုံးသည် Kubernetes API ရှိ object များကို ကိုင်တွယ်ခြင်းဖြင့် cluster နှင့် အပြန်အလှန် ဆက်သွယ်ကြသည်။

*ပုံ ၄.၁။ Kubernetes API ရှိ object များကို ကိုင်တွယ်ခြင်းဖြင့် cluster ကို configure ပြုလုပ်ခြင်း*

*   သင်သည် Kubernetes API ရှိ object များကို ကိုင်တွယ်ခြင်းဖြင့် cluster ၏ ကဏ္ဍအများစုကို ထိန်းချုပ်နိုင်သည်။
*   API object များသည် cluster အတွင်းရှိ အရာအားလုံးနီးပါးကို ကိုယ်စားပြုသည်။ Kubernetes သည် cluster ကို configure လုပ်ရန် ဤ object များကို အသုံးပြုသည်။

### 4.1.1 API ကို မိတ်ဆက်ခြင်း

Kubernetes API သည် cluster နှင့် အပြန်အလှန်ဆက်သွယ်မှု၏ ဗဟိုချက်ဖြစ်သောကြောင့် ဤစာအုပ်၏ အများစုသည် ဤ API ကို ရှင်းပြရန် ရည်ရွယ်ပါသည်။ အရေးကြီးဆုံး API object များကို နောက်အခန်းများတွင် ဖော်ပြထားသော်လည်း API ၏ အခြေခံမိတ်ဆက်ကို ဤနေရာတွင် တင်ပြထားပါသည်။

#### API ၏ architectural style ကို နားလည်ခြင်း

Kubernetes API သည် HTTP-based RESTful API တစ်ခုဖြစ်ပြီး၊ state ကို resources များဖြင့် ကိုယ်စားပြုထားသည်။ သင်သည် `POST`, `GET`, `PUT/PATCH` သို့မဟုတ် `DELETE` ကဲ့သို့သော standard HTTP method များကို အသုံးပြု၍ ထို resources များပေါ်တွင် CRUD operations (Create, Read, Update, Delete) များကို လုပ်ဆောင်နိုင်သည်။

> **အဓိပ္ပါယ်ဖွင့်ဆိုချက်**
>
> REST သည် Representational State Transfer ၏ အတိုကောက်ဖြစ်ပြီး၊ Roy Thomas Fielding ၏ ပါရဂူဘွဲ့စာတမ်းတွင် ဖော်ပြထားသည့်အတိုင်း stateless operation များကို အသုံးပြု၍ web service များမှတစ်ဆင့် computer system များအကြား အပြန်အလှန်အလုပ်လုပ်နိုင်မှုကို အကောင်အထည်ဖော်ရန်အတွက် architectural style တစ်ခုဖြစ်သည်။ ပိုမိုလေ့လာလိုပါက၊ https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm တွင် စာတမ်းကို ဖတ်ရှုပါ။

ဤ resources (သို့မဟုတ် objects) များသည် cluster ၏ configuration ကို ကိုယ်စားပြုပါသည်။ ထို့ကြောင့် cluster administrator များနှင့် cluster ထဲသို့ application များ deploy လုပ်သော engineer များသည် ဤ object များကို ကိုင်တွယ်ခြင်းဖြင့် configuration ကို လွှမ်းမိုးကြသည်။

Kubernetes community တွင် "resource" နှင့် "object" ဟူသော ဝေါဟာရများကို အပြန်အလှန် အသုံးပြုကြသော်လည်း ရှင်းပြရန်လိုအပ်သော သိမ်မွေ့သောကွဲပြားမှုများ ရှိပါသည်။

#### Resources နှင့် objects များအကြား ကွာခြားချက်ကို နားလည်ခြင်း

RESTful API များတွင် အဓိကကျသော concept မှာ resource ဖြစ်ပြီး၊ resource တစ်ခုချင်းစီကို ၎င်းကို တသီးတခြား သတ်မှတ်ပေးသည့် URI သို့မဟုတ် Uniform Resource Identifier တစ်ခု သတ်မှတ်ပေးထားသည်။ ဥပမာအားဖြင့်၊ Kubernetes API တွင်၊ application deployment များကို `deployment` resource များဖြင့် ကိုယ်စားပြုပြီး၊ ၎င်းတို့ကို `/apis/apps/v1/deployments` ဟူသော URI တွင် ဖော်ပြထားပါသည်။

သင် deployment များစာရင်းကို ရယူသောအခါ၊ API သည် deployment object instance များ၏ စာရင်းကို ပြန်ပေးသည်။ `object` ဆိုသည်မှာ `Deployment` object type ကဲ့သို့ object type တစ်ခု၏ တိကျသော instance တစ်ခုဖြစ်သည်။ instance တစ်ခုချင်းစီတွင် ၎င်း၏ကိုယ်ပိုင်အမည်ရှိပြီး ၎င်း၏ namespace အတွင်း (သို့မဟုတ် cluster-level object များအတွက် cluster တစ်ခုလုံးတွင်) တသီးတခြား သတ်မှတ်ထားသည်။ object တစ်ခုတည်းကို ၎င်း၏ကိုယ်ပိုင် resource URI တွင်လည်း ဖော်ပြထားသည်။ ဥပမာအားဖြင့်၊ `myns` namespace ထဲရှိ `mydeploy` အမည်ရှိသော deployment တစ်ခုကို `/apis/apps/v1/namespaces/myns/deployments/mydeploy` တွင် ဖော်ပြထားသည်။

ထို့ကြောင့် object တစ်ခုကို resource တစ်ခုထက်ပို၍ ဖော်ပြနိုင်သည်။ ပုံ ၄.၂ တွင် ပြထားသည့်အတိုင်း၊ `mydeploy` အမည်ရှိသော `Deployment` object instance ကို `deployments` resource ကို query လုပ်သည့်အခါ collection ၏ element တစ်ခုအနေဖြင့်လည်းကောင်း၊ တစ်ခုချင်းစီ၏ resource URI ကို တိုက်ရိုက် query လုပ်သည့်အခါ single object အနေဖြင့်လည်းကောင်း ပြန်ပေးသည်။

ထို့အပြင်၊ object type တစ်ခုအတွက် API version အများအပြားရှိပါက single object instance တစ်ခုကို resource အများအပြားမှတစ်ဆင့်လည်း ဖော်ပြနိုင်သည်။ Kubernetes version 1.15 အထိ၊ `Deployment` object များ၏ ကွဲပြားသောကိုယ်စားပြုမှု နှစ်မျိုးကို API မှ ဖော်ပြခဲ့သည်။ `/apis/apps/v1/deployments` တွင် ဖော်ပြထားသော `apps/v1` version အပြင်၊ `/apis/extensions/v1beta1/deployments` တွင် ဖော်ပြထားသော `extensions/v1beta1` ဟူသော version အဟောင်းတစ်ခုကိုလည်း API တွင် ရရှိနိုင်ခဲ့သည်။ ဤ resource နှစ်ခုသည် `Deployment` object အစုံနှစ်စုံကို ကိုယ်စားမပြုဘဲ၊ object schema တွင် သေးငယ်သော ကွဲပြားမှုများဖြင့် နည်းလမ်းနှစ်မျိုးဖြင့် ကိုယ်စားပြုထားသော အစုံတစ်ခုတည်းသာ ဖြစ်သည်။ သင်သည် ပထမ URI မှတစ်ဆင့် `Deployment` object ၏ instance တစ်ခုကို ဖန်တီးပြီးနောက် ဒုတိယ URI ကို အသုံးပြု၍ ၎င်းကို ပြန်ဖတ်နိုင်သည်။

အချို့ကိစ္စများတွင်၊ resource သည် object တစ်ခုခုကို လုံးဝကိုယ်စားမပြုပါ။ ဥပမာတစ်ခုမှာ Kubernetes API သည် client များကို subject (လူတစ်ဦး သို့မဟုတ် service တစ်ခု) က API operation တစ်ခုကို လုပ်ဆောင်ရန် ခွင့်ပြုချက်ရှိမရှိ စစ်ဆေးခွင့်ပြုသည့် နည်းလမ်းဖြစ်သည်။ ၎င်းကို `/apis/authorization.k8s.io/v1/subjectaccessreviews` resource သို့ `POST` request ပေးပို့ခြင်းဖြင့် လုပ်ဆောင်သည်။ response က request body တွင် သတ်မှတ်ထားသော operation ကို subject က လုပ်ဆောင်ရန် ခွင့်ပြုချက်ရှိမရှိကို ဖော်ပြသည်။ ဤနေရာတွင် အဓိကအချက်မှာ `POST` request ဖြင့် object တစ်ခုမှ ဖန်တီးခြင်းမရှိခြင်းပင်ဖြစ်သည်။

အထက်တွင်ဖော်ပြခဲ့သော ဥပမာများက resource သည် object နှင့် မတူကြောင်း ပြသသည်။ သင်သည် relational database system များနှင့် ရင်းနှီးကျွမ်းဝင်ပါက၊ resources နှင့် object type များကို database မှ view များနှင့် table များနှင့် နှိုင်းယှဉ်နိုင်သည်။ Resources များသည် သင် object များနှင့် အပြန်အလှန်ဆက်သွယ်သည့် view များဖြစ်သည်။

> **မှတ်ချက်**
> "resource" ဟူသော ဝေါဟာရသည် CPU နှင့် memory ကဲ့သို့သော compute resource များကိုလည်း ရည်ညွှန်းနိုင်သောကြောင့်၊ ရှုပ်ထွေးမှုကို လျှော့ချရန်အတွက် ဤစာအုပ်တွင် API resource များကို ရည်ညွှန်းရန် "objects" ဟူသော ဝေါဟာရကို အသုံးပြုထားပါသည်။

#### Object များကို မည်သို့ကိုယ်စားပြုသည်ကို နားလည်ခြင်း

သင် resource URI သို့ `GET` request တစ်ခု ပြုလုပ်သောအခါ၊ API server သည် object(များ)ကို JSON document အဖြစ် serialize လုပ်ပြီး ပြန်ပေးသည်။ သင် `kubectl` tool ကို အသုံးပြုပါက၊ ၎င်းသည် JSON document ကို တောင်းခံပြီး လက်ခံရရှိသော်လည်း၊ ပုံမှန်အားဖြင့် ၎င်းကို လူဖတ်ရန်လွယ်ကူသော format သို့ ပြောင်းလဲပေးသည်။ သင်သည် ၎င်းကို raw JSON အဖြစ် print ထုတ်ရန် သို့မဟုတ် လူများအတွက် ပိုမိုဖတ်ရှုရလွယ်ကူသော YAML သို့ ပြောင်းလဲရန်လည်း ပြောနိုင်သည်။ ဤစာအုပ်တစ်လျှောက်လုံးတွင် object များ၏ YAML ကိုယ်စားပြုမှုကို သင်တွေ့ရပါမည်။

#### Object တစ်ခု၏ အဓိက အစိတ်အပိုင်းများကို မိတ်ဆက်ခြင်း

Kubernetes API object အများစု၏ manifest တွင် အောက်ပါကဏ္ဍလေးခု ပါဝင်သည်-

*   **Type Metadata** တွင် ဤ manifest က ဖော်ပြသော object ၏ အမျိုးအစားအကြောင်း အချက်အလက်များ ပါဝင်သည်။ ၎င်းသည် object type၊ type ပါဝင်သည့် group နှင့် API version ကို သတ်မှတ်သည်။
*   **Object Metadata** တွင် object instance ၏ အခြေခံအချက်အလက်များဖြစ်သော ၎င်း၏အမည်၊ ဖန်တီးသည့်အချိန်၊ object ၏ပိုင်ရှင်နှင့် အခြား သတ်မှတ်ဖော်ပြသည့် အချက်အလက်များ ပါဝင်သည်။ `Object Metadata` ရှိ field များသည် object type အားလုံးအတွက် တူညီပါသည်။
*   **Spec** သည် သင် object ၏ လိုချင်သော state ကို သတ်မှတ်သည့် အပိုင်းဖြစ်သည်။ ၎င်း၏ field များသည် object type တစ်ခုနှင့်တစ်ခု ကွဲပြားပါသည်။ pod များအတွက်၊ ဤအပိုင်းသည် pod ၏ container များ၊ storage volume များနှင့် ၎င်း၏လည်ပတ်မှုနှင့် သက်ဆိုင်သည့် အခြားအချက်အလက်များကို သတ်မှတ်ပေးသည်။
*   **Status** တွင် object ၏ လက်ရှိ အမှန်တကယ် state ပါဝင်သည်။ pod တစ်ခုအတွက်၊ ၎င်းသည် pod ၏ condition၊ ၎င်း၏ container တစ်ခုချင်းစီ၏ status၊ ၎င်း၏ IP address၊ ၎င်း run နေသော node နှင့် ဖြစ်ပျက်နေသောအရာများကို ဖော်ပြသည့် အခြားအချက်အလက်များကို ပြောပြသည်။

#### Spec နှင့် status ကဏ္ဍများကို နားလည်ခြင်း

ယခင်ပုံတွင် သင်သတိပြုမိခဲ့သည့်အတိုင်း၊ object တစ်ခု၏ အရေးကြီးဆုံး အပိုင်းနှစ်ခုမှာ `Spec` နှင့် `Status` ကဏ္ဍများဖြစ်သည်။ သင်သည် `Spec` ကို အသုံးပြု၍ object ၏ လိုချင်သော state ကို သတ်မှတ်ပြီး `Status` မှ object ၏ အမှန်တကယ် state ကို ဖတ်ရှုသည်။

ထို့ကြောင့် သင်သည် `Spec` ကိုရေးပြီး `Status` ကိုဖတ်သူဖြစ်သည်၊ သို့သော် `Spec` ကိုဖတ်ပြီး `Status` ကိုရေးသူမှာ မည်သူ သို့မဟုတ် မည်သည့်အရာဖြစ်သနည်း။

Kubernetes Control Plane သည် သင်ဖန်တီးသော object များကို စီမံခန့်ခွဲသည့် `controller` ဟုခေါ်သော component အများအပြားကို run ထားသည်။ controller တစ်ခုချင်းစီသည် များသောအားဖြင့် object type တစ်ခုတည်းအတွက်သာ တာဝန်ရှိသည်။ ဥပမာအားဖြင့်၊ `Deployment controller` သည် `Deployment` object များကို စီမံခန့်ခွဲသည်။

ပုံ ၄.၄ တွင် ပြထားသည့်အတိုင်း၊ controller တစ်ခု၏ တာဝန်မှာ object ၏ `Spec` ကဏ္ဍမှ လိုချင်သော object state ကို ဖတ်ရှုခြင်း၊ ဤ state ကို ရရှိရန် လိုအပ်သော လုပ်ဆောင်ချက်များကို လုပ်ဆောင်ခြင်း၊ နှင့် object ၏ `Status` ကဏ္ဍသို့ ရေးသားခြင်းဖြင့် object ၏ အမှန်တကယ် state ကို ပြန်လည်အစီရင်ခံခြင်းတို့ ဖြစ်သည်။

အနှစ်သာရအားဖြင့်၊ သင်သည် API object များကို ဖန်တီးပြီး update လုပ်ခြင်းဖြင့် Kubernetes အား ဘာလုပ်ရမည်ကို ပြောပြသည်။ Kubernetes controller များသည် ၎င်းတို့ ဘာလုပ်ခဲ့သည်နှင့် ၎င်းတို့၏ အလုပ်၏ status ကို သင့်အား ပြောပြရန် တူညီသော API object များကို အသုံးပြုသည်။

#### Object အားလုံးတွင် spec နှင့် status ကဏ္ဍများ မရှိပါ

Kubernetes API object အားလုံးတွင် metadata ကဏ္ဍနှစ်ခုပါဝင်သော်လည်း၊ အားလုံးတွင် `Spec` နှင့် `Status` ကဏ္ဍများ မပါဝင်ပါ။ ထိုသို့မပါဝင်သော object များသည် များသောအားဖြင့် static data သာပါဝင်ပြီး သက်ဆိုင်ရာ controller မရှိသောကြောင့် object ၏ လိုချင်သော state နှင့် အမှန်တကယ် state ကို ခွဲခြားရန်မလိုအပ်ပါ။

ထိုကဲ့သို့သော object ၏ ဥပမာတစ်ခုမှာ `Event` object ဖြစ်သည်။ ၎င်းကို controller က စီမံခန့်ခွဲနေသော object တစ်ခုနှင့်ပတ်သက်၍ ဘာတွေဖြစ်ပျက်နေသည်ကို နောက်ထပ်အချက်အလက်များ ပေးရန်အတွက် controller အမျိုးမျိုးမှ ဖန်တီးသည်။ `Event` object ကို အပိုင်း ၄.၃ တွင် ရှင်းပြထားပါသည်။

သင်သည် object တစ်ခု၏ ယေဘုယျအကြမ်းဖျင်းကို နားလည်သွားပြီဖြစ်သောကြောင့်၊ ဤအခန်း၏ နောက်အပိုင်းတွင် `Node` object ကို အသေးစိတ်လေ့လာပြီး ၎င်း၏ဖွဲ့စည်းပုံကို ကြည့်ရှုနိုင်ပါပြီ။

## 4.2 Node object တစ်ခုကို စစ်ဆေးခြင်း

Kubernetes APIရှိ object များ၏ တူညီသောဖွဲ့စည်းပုံကို လေ့လာရန်၊ cluster node များကို ကိုယ်စားပြုသော `Node` object များကို ကြည့်ရှုပါမည်။ ယခင်အခန်းများတွင် သင်လေ့လာခဲ့သည့်အတိုင်း၊ Kubernetes cluster တစ်ခုသည် master နှင့် worker node များပါဝင်သော ကွန်ပျူတာအစုအဝေးတစ်ခုပေါ်တွင် run သည်။

အောက်ပါပုံသည် `Node` object သုံးခုနှင့် cluster ကို ဖွဲ့စည်းထားသော အမှန်တကယ် cluster machine များကို ပြသထားသည်။ `Node` object instance တစ်ခုစီသည် host တစ်ခုကို ကိုယ်စားပြုသည်။ instance တစ်ခုချင်းစီတွင်၊ `Spec` section တွင် host ၏ configuration (တစ်စိတ်တစ်ပိုင်း) ပါဝင်ပြီး `Status` section တွင် host ၏ state ပါဝင်သည်။

*ပုံ ၄.၅။ Cluster node များကို Node object များဖြင့် ကိုယ်စားပြုခြင်း*

*   Kubernetes API သည် master node ပေါ်တွင် run နေသော API server မှ ပံ့ပိုးပေးသည်။
*   `Node` object တစ်ခုစီသည် cluster node တစ်ခုကို ကိုယ်စားပြုသည်။

> **မှတ်ချက်**
> `Node` object များသည် အခြား object များနှင့် အနည်းငယ်ကွာခြားသည်။ အကြောင်းမှာ ၎င်းတို့ကို user များက ဖန်တီးခြင်းမဟုတ်ဘဲ cluster node ပေါ်တွင် run နေသော node agent ဖြစ်သည့် Kubelet မှ ဖန်တီးလေ့ရှိသောကြောင့် ဖြစ်သည်။ သင် cluster သို့ machine တစ်ခုကို ထည့်လိုက်သောအခါ၊ Kubelet သည် host ကို ကိုယ်စားပြုသော `Node` object တစ်ခုကို ဖန်တီးခြင်းဖြင့် node ကို register လုပ်သည်။ ထို့နောက် user များသည် `Spec` section ရှိ field အချို့ကို ပြင်ဆင်နိုင်သည်။

### 4.2.1 Node object တစ်ခု၏ manifest အပြည့်အစုံကို လေ့လာခြင်း

`Node` object များထဲမှ တစ်ခုကို အနီးကပ်လေ့လာကြည့်ကြပါစို့။ သင့် cluster ရှိ `Node` object အားလုံးကို `kubectl get nodes` command ဖြင့် စာရင်းပြုစုပြီး သင်စစ်ဆေးလိုသည့် တစ်ခုကို ရွေးချယ်ပါ။ ထို့နောက် `<node-name>` ကို သင်ရွေးချယ်ထားသော node ၏အမည်ဖြင့် အစားထိုးပြီး `kubectl get node <node-name> -o yaml` command ကို run ပါ။

အောက်ပါ listing သည် ကျွန်ုပ်၏ cluster ရှိ node များထဲမှ တစ်ခု၏ manifest အပြည့်အစုံကို ပြသထားသည်။

**Listing 4.1 kind-control-plane node ၏ YAML manifest**

```yaml
$ kubectl get node kind-control-plane -o yaml
apiVersion: v1                                             #A
kind: Node                                                 #A
metadata:                                                  #B
  annotations: ...
  creationTimestamp: "2020-05-03T15:09:17Z"
  labels: ...
  name: kind-control-plane                                 #C
  resourceVersion: "3220054"
  selfLink: /api/v1/nodes/kind-control-plane
  uid: 16dc1e0b-8d34-4cfb-8ade-3b0e91ec838b
spec:                                                      #D
  podCIDR: 10.244.0.0/24                                   #E
  podCIDRs:                                                #E
  - 10.244.0.0/24                                          #E
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
status:                                                    #F
  addresses:                                               #G
  - address: 172.18.0.2
    type: InternalIP
  - address: kind-control-plane
    type: Hostname
  allocatable:                                             #H
    cpu: "6"
    ephemeral-storage: "103112216Ki"
    hugepages-1Gi: "0"
    hugepages-2Mi: "0"
    memory: 6078088Ki
    pods: "110"
  capacity:                                                #H
    cpu: "6"
    ephemeral-storage: 111953712Ki
    hugepages-1Gi: "0"
    hugepages-2Mi: "0"
    memory: 6180488Ki
    pods: "110"
  conditions:                                              #I
  - lastHeartbeatTime: "2020-05-17T13:03:42Z"
    lastTransitionTime: "2020-05-03T15:09:17Z"
    message: kubelet has sufficient memory available
    reason: KubeletHasSufficientMemory
    status: "False"
    type: MemoryPressure
  - ...
  daemonEndpoints:
    kubeletEndpoint:
      Port: 10250
  images: ...
  nodeInfo:                                                #J
    architecture: amd64
    bootID: 233a359f-5897-4860-863d-06546130e1ff
    containerRuntimeVersion: containerd://1.3.3-14-g449e9269
    kernelVersion: 5.5.10-200.fc31.x86_64
    kubeProxyVersion: v1.18.2
    kubeletVersion: v1.18.2
    machineID: 74b74e389bb246e99abdf731d145142d
    operatingSystem: linux
    osImage: Ubuntu 19.10
    systemUUID: 8749f818-8269-4a02-bdc2-84bf5fa21700
```

*   **A** ဤသည်မှာ `v1` API schema ကို အသုံးပြုသော `Node` object တစ်ခုဖြစ်သည်။
*   **B** ဤ object instance ၏ metadata ဖြစ်သည်။
*   **C** node ၏ အမည်ဖြစ်သည်။
*   **D** node ၏ spec ဖြစ်သည်။
*   **E** ဤ node ပေါ်တွင် run နိုင်သော pod များအတွက် IP address range ဖြစ်သည်။
*   **F** node ၏ လက်ရှိ status ဖြစ်သည်။
*   **G** node ၏ IP address နှင့် hostname ဖြစ်သည်။
*   **H** node က ပေးနိုင်သော resource များဖြစ်သည် - CPU၊ memory၊ နှင့် pod အရေအတွက်။
*   **I** node ၏ condition များ - ၎င်းသည် memory pressure အောက်တွင်ရှိသလား၊ disk pressure အောက်တွင်ရှိသလား၊ နှင့် အခြားအရာများ။
*   **J** node အကြောင်း အထွေထွေအချက်အလက်များ။

> **မှတ်ချက်**
> YAML အစား JSON ဖြင့် object ကို ပြသရန် `-o json` option ကို အသုံးပြုပါ။

YAML manifest တွင်၊ object definition ၏ အဓိက ကဏ္ဍလေးခုနှင့် node ၏ ပိုအရေးကြီးသော property များကို သင့်အား object ၏ ခြုံငုံသုံးသပ်ချက်ကို ပေးရန်အတွက် မှတ်သားထားပါသည်။ အောက်ပါကဏ္ဍများက ၎င်းတို့ကို ရှင်းပြပါမည်။

#### Type Metadata fields

သင်တွေ့မြင်ရသည့်အတိုင်း၊ manifest သည် ဤ object manifest က သတ်မှတ်သော object ၏ API version နှင့် type ကို သတ်မှတ်ပေးသည့် `apiVersion` နှင့် `kind` field များဖြင့် စတင်သည်။ API version သည် ဤ object ကို ဖော်ပြရန်အသုံးပြုသော schema ဖြစ်သည်။ အစောပိုင်းတွင် ဖော်ပြခဲ့သည့်အတိုင်း၊ object type တစ်ခုသည် schema တစ်ခုထက်ပို၍ ဆက်စပ်နိုင်ပြီး၊ schema တစ်ခုစီတွင် object ကိုဖော်ပြရန် ကွဲပြားသော field များကို အသုံးပြုသည်။ သို့သော်၊ များသောအားဖြင့် type တစ်ခုစီအတွက် schema တစ်ခုသာ ရှိပါသည်။

ယခင် manifest ရှိ `apiVersion` သည် `v1` ဖြစ်သော်လည်း၊ နောက်အခန်းများတွင် အခြား object type များရှိ `apiVersion` တွင် version number ထက်ပို၍ ပါဝင်သည်ကို သင်တွေ့ရပါမည်။ ဥပမာအားဖြင့်၊ `Deployment` object များအတွက်၊ `apiVersion` သည် `apps/v1` ဖြစ်သည်။ မူလက field ကို API version သတ်မှတ်ရန်အတွက်သာ အသုံးပြုခဲ့သော်လည်း၊ ယခုအခါ resource က ပိုင်ဆိုင်သော API group ကို သတ်မှတ်ရန်အတွက်လည်း အသုံးပြုပါသည်။ `Node` object များသည် core API group တွင် ပါဝင်ပြီး၊ ၎င်းကို `apiVersion` field တွင် ထုံးစံအတိုင်း ချန်လှပ်ထားပါသည်။

manifest တွင် သတ်မှတ်ထားသော object ၏ type ကို `kind` field ဖြင့် သတ်မှတ်သည်။

#### Object Metadata section

`metadata` section တွင် object ၏ အမည်၊ uid၊ labels၊ annotations နှင့် အခြားအရာများအပါအဝင် ဤ object instance အကြောင်း အချက်အလက်များ ပါဝင်သည်။ ဤကဏ္ဍရှိ field အများစုကို ယခင်အခန်းတွင် ရှင်းပြပြီးဖြစ်သောကြောင့် ယခု ရှင်းပြရန်မလိုအပ်ပါ။

#### Spec section

`spec` section သည် object ၏ လိုချင်သော state ကို သတ်မှတ်သည်။ `Node` object များအတွက်၊ `spec` တွင် `podCIDR` field ပါဝင်ပြီး၊ ၎င်းသည် ဤ node ပေါ်တွင် pod များအတွက် IP address range ကို သတ်မှတ်သည်။ Pod များကို ဤ node တွင် schedule လုပ်ခြင်းမှ ကာကွယ်ရန် `unschedulable` field ကိုလည်း သင်တွေ့နိုင်သည်။

များသောအားဖြင့်၊ object တစ်ခု၏ `spec` section တွင် သင် object ကို configure လုပ်ရန် အသုံးပြုသော field များစွာ ပါဝင်သည်။

#### Status section ရှိ Fields

`status` section သည် object အမျိုးအစားအလိုက် ကွဲပြားသော်လည်း ၎င်း၏ ရည်ရွယ်ချက်မှာ အမြဲတမ်းတူညီပါသည် - ၎င်းသည် object က ကိုယ်စားပြုသောအရာ၏ နောက်ဆုံးတွေ့ရှိခဲ့သော state ကို ပါဝင်သည်။ `Node` object များအတွက်၊ `status` သည် node ၏ IP address(များ)၊ host name၊ compute resource များ ပေးနိုင်စွမ်း၊ node ၏ လက်ရှိ condition များ၊ ၎င်းဒေါင်းလုဒ်လုပ်ပြီးဖြစ်ပြီး ယခု local တွင် cache လုပ်ထားသော container image များနှင့် ၎င်း၏ operating system နှင့် ၎င်းပေါ်တွင် run နေသော Kubernetes component များ၏ version အကြောင်း အချက်အလက်များကို ဖော်ပြသည်။

### 4.2.2 object တစ်ခုချင်းစီ၏ field များကို နားလည်ခြင်း

manifest ရှိ field တစ်ခုချင်းစီအကြောင်း ပိုမိုလေ့လာလိုပါက http://kubernetes.io/docs/reference/ ရှိ API reference documentation ကို ဖတ်ရှုနိုင်သည် သို့မဟုတ် နောက်တွင် ဖော်ပြထားသည့်အတိုင်း `kubectl explain` command ကို အသုံးပြုနိုင်သည်။

#### API object field များကို လေ့လာရန် kubectl explain ကို အသုံးပြုခြင်း

`kubectl` tool တွင် object kind တစ်ခုချင်းစီ၏ explanation ကို command line မှ တိုက်ရိုက်ကြည့်ရှုနိုင်သော ကောင်းမွန်သောအင်္ဂါရပ်တစ်ခု ရှိသည်။ ဥပမာအားဖြင့်၊ `Node` object ၏ field များကို ကြည့်ရန် အောက်ပါ command ကို run ပါ။

```shell
$ kubectl explain node
KIND:     Node
VERSION:  v1
DESCRIPTION:
    Node is a worker node in Kubernetes.
FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object...
   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client...
   metadata <Object>
     Standard object's metadata. More info: ...
   spec <Object>
     Spec defines the behavior of a node...
   status <Object>
     Most recently observed status of the node. Populated by the system.
     Read-only. More info: ...
```

command သည် object ၏ explanation ကို print ထုတ်ပြီး object တွင် ပါဝင်နိုင်သော top-level field များကို စာရင်းပြုစုပေးသည်။

#### API object ၏ ဖွဲ့စည်းပုံကို ပိုမိုနက်ရှိုင်းစွာ လေ့လာခြင်း

ထို့နောက် သင်သည် field တစ်ခုချင်းစီအောက်ရှိ subfield များကို ရှာဖွေရန် ပိုမိုနက်ရှိုင်းစွာ တူးဖော်နိုင်သည်။ ဥပမာအားဖြင့်၊ သင်သည် node ၏ `spec` field ကို ရှင်းပြရန် အောက်ပါ command ကို အသုံးပြုနိုင်သည်။

```shell
$ kubectl explain node.spec
KIND:     Node
VERSION:  v1
RESOURCE: spec <Object>
DESCRIPTION:
     Spec defines the behavior of a node.
     NodeSpec describes the attributes that a node is created with.
FIELDS:
   configSource <Object>
     If specified, the source to get node configuration from...
```

အပေါ်ဆုံးတွင် ပေးထားသော API version ကို သတိပြုပါ။ အစောပိုင်းတွင် ရှင်းပြခဲ့သည့်အတိုင်း၊ တူညီသော `kind` ၏ version အများအပြား ရှိနိုင်သည်။ ကွဲပြားသော version များတွင် ကွဲပြားသော field များ သို့မဟုတ် default value များ ရှိနိုင်သည်။ သင်ကွဲပြားသော version ကို ပြသလိုပါက၊ ၎င်းကို `--api-version` option ဖြင့် သတ်မှတ်ပါ။

> **မှတ်ချက်**
> သင် object တစ်ခု၏ ဖွဲ့စည်းပုံအပြည့်အစုံ (description မပါဘဲ field များ၏ အဆင့်ဆင့်စာရင်းအပြည့်အစုံ) ကို ကြည့်လိုပါက `kubectl explain pods --recursive` ကို စမ်းကြည့်ပါ။

### 4.2.3 object တစ်ခု၏ status condition များကို နားလည်ခြင်း

`spec` နှင့် `status` section နှစ်ခုလုံးရှိ field အစုံသည် object kind တစ်ခုချင်းစီအတွက် ကွဲပြားသော်လည်း၊ `conditions` field ကို ၎င်းတို့အများအပြားတွင် တွေ့ရသည်။ ၎င်းသည် object လက်ရှိရောက်ရှိနေသော condition များ၏ စာရင်းကို ပေးသည်။ သင် object တစ်ခုကို troubleshoot လုပ်ရန် လိုအပ်သည့်အခါ ၎င်းတို့သည် အလွန်အသုံးဝင်သောကြောင့် ၎င်းတို့ကို ပိုမိုနီးကပ်စွာ လေ့လာကြည့်ကြပါစို့။ `Node` object ကို ဥပမာအဖြစ် အသုံးပြုထားသောကြောင့်၊ ဤကဏ္ဍသည် cluster node တစ်ခုနှင့်ပတ်သက်သော ပြဿနာများကို အလွယ်တကူ ဖော်ထုတ်နည်းကိုလည်း သင်ကြားပေးပါသည်။

#### Node ၏ status condition များကို မိတ်ဆက်ခြင်း

`Node` object များထဲမှ တစ်ခု၏ YAML manifest ကို ထပ်မံ print ထုတ်ကြည့်ကြပါစို့၊ သို့သော် ဤတစ်ကြိမ်တွင် `status.conditions` field ကိုသာ ပြသရန် `jq` tool ကို အသုံးပြုပါမည်။

> **အကြံပြုချက်**
> သင် object ၏ ဖွဲ့စည်းပုံတစ်စိတ်တစ်ပိုင်းကိုသာ ကြည့်လိုပါက `jq` tool သည် အလွန်အသုံးဝင်ပါသည်။ ဥပမာအားဖြင့်၊ node ၏ status condition များကို ပြသရန်၊ `kubectl get node <name> -o json | jq .status.conditions` ကို run နိုင်သည်။ YAML အတွက် တူညီသော tool မှာ `yq` ဖြစ်သည်။

```json
$ kubectl get node kind-control-plane -o json | jq .status.conditions
[
  {
    "lastHeartbeatTime": "2020-05-17T13:03:42Z",
    "lastTransitionTime": "2020-05-03T15:09:17Z",
    "message": "kubelet has sufficient memory available",
    "reason": "KubeletHasSufficientMemory",
    "status": "False",
    "type": "MemoryPressure"
  },
  {
    "lastHeartbeatTime": "2020-05-17T13:03:42Z",
    "lastTransitionTime": "2020-05-03T15:09:17Z",
    "message": "kubelet has no disk pressure",
    "reason": "KubeletHasNoDiskPressure",
    "status": "False",
    "type": "DiskPressure"
  },
  {
    "lastHeartbeatTime": "2020-05-17T13:03:42Z",
    "lastTransitionTime": "2020-05-03T15:09:17Z",
    "message": "kubelet has sufficient PID available",
    "reason": "KubeletHasSufficientPID",
    "status": "False",
    "type": "PIDPressure"
  },
  {
    "lastHeartbeatTime": "2020-05-17T13:03:42Z",
    "lastTransitionTime": "2020-05-03T15:10:15Z",
    "message": "kubelet is posting ready status",
    "reason": "KubeletReady",
    "status": "True",
    "type": "Ready"
  }
]
```

node ၏ state ကို ဖော်ပြသော condition လေးခုရှိသည်။ ၎င်းတို့မှာ `MemoryPressure`, `DiskPressure`, `PIDPressure` နှင့် `Ready` တို့ဖြစ်သည်။ Condition တစ်ခုချင်းစီ၏ status သည် `True`, `False`, သို့မဟုတ် `Unknown` ဖြစ်နိုင်သည်။

*   `MemoryPressure` condition သည် `True` ဖြစ်ပါက node တွင် memory နည်းပါးနေသည်။
*   `DiskPressure` condition သည် `True` ဖြစ်ပါက node တွင် disk space နည်းပါးနေသည်။
*   `PIDPressure` condition သည် `True` ဖြစ်ပါက node တွင် process ID များ နည်းပါးနေသည်။
*   `Ready` condition သည် `True` ဖြစ်ပါက node က pod အသစ်များကို လက်ခံရန် အဆင်သင့်ဖြစ်သည်။

`Ready` မှလွဲ၍ ကျန် condition သုံးခုလုံး၏ `False` status သည် node အတွက် ကောင်းမွန်သောအခြေအနေဖြစ်ပြီး၊ `True` သည် ပြဿနာရှိကြောင်း ညွှန်ပြသည်။ `Ready` condition အတွက်မူ ဆန့်ကျင်ဘက်ဖြစ်သည်။

စာရင်းရှိ နောက်ဆုံး condition ဖြစ်သော်လည်း၊ `Ready` condition သည် pod အသစ်များကို လက်ခံရန် node အဆင်သင့်ဖြစ်မဖြစ်ကို ညွှန်ပြသောကြောင့် အရေးကြီးဆုံးဖြစ်နိုင်သည်။ အခြား condition များ (`MemoryPressure`, `DiskPressure` နှင့် `PIDPressure`) သည် node resource များ ကုန်ခမ်းလုနီးဖြစ်နေခြင်းကို ညွှန်ပြသည်။ node တစ်ခု ထူးဆန်းစွာ ပြုမူလာပါက - ဥပမာအားဖြင့်၊ ၎င်းပေါ်တွင် run နေသော application များ resource များ ကုန်ခမ်းလာပြီး/သို့မဟုတ် crash ဖြစ်လာပါက - ဤ condition များကို စစ်ဆေးရန် သတိရပါ။

#### အခြား object kind များရှိ condition များကို နားလည်ခြင်း

`Node` object များရှိ condition list ကဲ့သို့သော list ကို အခြား object kind များစွာတွင်လည်း အသုံးပြုသည်။ အစောပိုင်းတွင် ရှင်းပြခဲ့သော condition များသည် object အများစု၏ state ကို field တစ်ခုတည်းဖြင့် ကိုယ်စားပြုမည့်အစား condition အများအပြားဖြင့် ကိုယ်စားပြုရခြင်း၏ အကြောင်းရင်းကို ကောင်းစွာ ဥပမာပေးပါသည်။

> **မှတ်ချက်**
> Condition များသည် များသောအားဖြင့် orthogonal ဖြစ်သည်၊ ဆိုလိုသည်မှာ ၎င်းတို့သည် object ၏ ဆက်စပ်မှုမရှိသော ကဏ္ဍများကို ကိုယ်စားပြုသည်။

object တစ်ခု၏ state ကို field တစ်ခုတည်းဖြင့် ကိုယ်စားပြုပါက၊ ၎င်းကို နောက်ပိုင်းတွင် value အသစ်များဖြင့် တိုးချဲ့ရန် အလွန်ခက်ခဲပေလိမ့်မည်။ အကြောင်းမှာ ၎င်းသည် object ၏ state ကို စောင့်ကြည့်ပြီး ၎င်းအပေါ်အခြေခံ၍ လုပ်ဆောင်ချက်များ လုပ်ဆောင်သော client အားလုံးကို update လုပ်ရန် လိုအပ်သောကြောင့်ဖြစ်သည်။ အချို့ object kind များသည် မူလက ထိုကဲ့သို့သော field တစ်ခုတည်းကို အသုံးပြုခဲ့ကြပြီး၊ အချို့မှာ ယခုထိ အသုံးပြုနေဆဲဖြစ်သော်လည်း၊ အများစုမှာ condition list ကို အသုံးပြုနေကြပြီဖြစ်သည်။

### 4.2.4 kubectl describe command ကို အသုံးပြု၍ object များကို စစ်ဆေးခြင်း

Kubernetes API object များ၏ ဖွဲ့စည်းပုံအပြည့်အစုံကို မှန်ကန်စွာ သဘောပေါက်စေရန်၊ သင့်အား `Node` object ၏ YAML manifest အပြည့်အစုံကို ပြသရန် လိုအပ်ခဲ့သည်။ သို့သော်၊ `kubectl` tool သည် object တစ်ခု၏ အချက်အလက်များကို ပိုမိုဖတ်ရှုရလွယ်ကူသော format ဖြင့် ပြသနိုင်သည်။

`kubectl describe` command ကို အသုံးပြု၍ `Node` object တစ်ခုကို စစ်ဆေးသည့်အခါ တွေ့ရသည့်အရာကို ကြည့်ကြပါစို့။

```shell
$ kubectl describe node kind-worker2
Name:               kind-worker2
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=kind-worker2
                    kubernetes.io/os=linux
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach-controller: true
CreationTimestamp:  Sun, 03 May 2020 17:09:48 +0200
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  kind-worker2
  AcquireTime:     <unset>
  RenewTime:       Sun, 17 May 2020 16:15:03 +0200
Conditions:
  Type             Status  ...  Reason                       Message
  ----             ------  ...  ------                       -------
  MemoryPressure   False   ...  KubeletHasSufficientMemory   ...
  DiskPressure     False   ...  KubeletHasNoDiskPressure     ...
  PIDPressure      False   ...  KubeletHasSufficientPID      ...
  Ready            True    ...  KubeletReady                 ...
Addresses:
  InternalIP:  172.18.0.4
  Hostname:    kind-worker2
...
System Info:
...
Non-terminated Pods:          (5 in total)
  Namespace                   Name                  CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                   ----                  ------------  ----------  ---------------  -------------
  kube-system                 kindnet-f8ddg         100m (5%)     100m (5%)   50Mi (0%)        50Mi (0%)
  kube-system                 kube-proxy-k89j2      0 (0%)        0 (0%)      0 (0%)           0 (0%)
  ...
Allocated resources:
...
Events:
  Type    Reason                   Age   From                      Message
  ----    ------                   ----  ----                      -------
  Normal  Starting                 3m50s kubelet, kind-worker2     ...
  Normal  NodeAllocatableEnforced  3m50s kubelet, kind-worker2     ...
  Normal  NodeHasSufficientMemory  3m50s kubelet, kind-worker2     ...
  Normal  NodeHasNoDiskPressure    3m50s kubelet, kind-worker2     ...
  Normal  NodeHasSufficientPID     3m50s kubelet, kind-worker2     ...
  Normal  Starting                 3m49s kube-proxy, kind-worker2  ...
```

သင်တွေ့မြင်ရသည့်အတိုင်း၊ `kubectl describe` command သည် သင်ယခင်က `Node` object ၏ YAML manifest တွင် တွေ့ရှိခဲ့သော အချက်အလက်အားလုံးကို ပိုမိုဖတ်ရှုရလွယ်ကူသောပုံစံဖြင့် ပြသသည်။ သင်သည် အမည်၊ IP address၊ နှင့် hostname၊ condition များနှင့် node ၏ capacity ကို ကြည့်ရှုနိုင်သည်။

`describe` command သည် node ပေါ်တွင် run နေသော pod များနှင့် node နှင့် သက်ဆိုင်သော event များကဲ့သို့ ဆက်စပ်နေသော အခြား object များကိုလည်း ပြသသည်။ ဤအချက်အလက်သည် node ၏ status တွင် မပါဝင်ပါ။ `kubectl` သည် အခြား resource များကို query လုပ်ခြင်းဖြင့် ၎င်းကို ရယူသည်။

## 4.3 Event object များမှတစ်ဆင့် cluster event များကို စောင့်ကြည့်ခြင်း

Controller များသည် object တစ်ခု၏ အမှန်တကယ် state ကို object ၏ `spec` field တွင် သတ်မှတ်ထားသော လိုချင်သော state နှင့် ညီညွတ်အောင် ပြန်လည်ညှိနှိုင်းသည့် တာဝန်ကို လုပ်ဆောင်သည့်အခါ၊ ၎င်းတို့ ဘာလုပ်ခဲ့သည်ကို ဖော်ပြရန် event များကို ထုတ်ပေးပါသည်။ Event အမျိုးအစားနှစ်မျိုးရှိသည်- `Normal` နှင့် `Warning`။ `Warning` အမျိုးအစား event များကို controller များက object ကို ညှိနှိုင်းခြင်းမှ တားဆီးသည့်အရာတစ်ခုခု ရှိသည့်အခါ ထုတ်ပေးလေ့ရှိသည်။ ဤ event အမျိုးအစားကို စောင့်ကြည့်ခြင်းဖြင့်၊ cluster ကြုံတွေ့နေရသော ပြဿနာများကို လျင်မြန်စွာ သိရှိနိုင်သည်။

### 4.3.1 Event object ကို မိတ်ဆက်ခြင်း

Kubernetes ရှိ အခြားအရာများကဲ့သို့ပင်၊ event များကို Kubernetes API မှတစ်ဆင့် ဖန်တီးပြီး ဖတ်ရှုသည့် `Event` object များဖြင့် ကိုယ်စားပြုသည်။ အောက်ပါပုံတွင် ပြထားသည့်အတိုင်း၊ ၎င်းတို့တွင် object တစ်ခုနှင့် ပတ်သက်၍ ဘာဖြစ်ခဲ့သည်၊ event ၏ source က ဘာလဲဆိုသည့် အချက်အလက်များ ပါဝင်သည်။ အခြား object များနှင့် မတူဘဲ၊ Kubernetes API object များအတွက် data store ဖြစ်သော `etcd` ပေါ်ရှိ ဝန်ထုပ်ဝန်ပိုးကို လျှော့ချရန်အတွက် `Event` object တစ်ခုစီကို ၎င်းဖန်တီးပြီး တစ်နာရီအကြာတွင် ဖျက်ပစ်သည်။

*ပုံ ၄.၇။ Event object များ၊ controller များနှင့် ၎င်းတို့ စီမံခန့်ခွဲသော object များအကြား ဆက်စပ်မှု*

*   Controller တစ်ခုသည် object ကို စီမံခန့်ခွဲသည်။ ၎င်းသည် object ၏ spec ကိုဖတ်ပြီး ၎င်း၏ status ကို update လုပ်သည်။
*   Controller သည် ၎င်းလုပ်ဆောင်ခဲ့သော လုပ်ဆောင်ချက်များနှင့် ဖြစ်ပေါ်ခဲ့နိုင်သည့် အရေးကြီးသော state အပြောင်းအလဲများကို ဖော်ပြသည့် `Event` object များကို ထုတ်ပေးသည်။
*   `Event` object တစ်ခုစီသည် ၎င်းသက်ဆိုင်သည့် object instance နှင့် ၎င်းကို ထုတ်ပေးသည့် controller ကို သတ်မှတ်သည်။
*   Cluster ရှိ object အရေအတွက်ပေါ်မူတည်၍ event အရေအတွက်များစွာ အဆက်မပြတ် ထုတ်ပေးနေပြီး `etcd` store ပေါ်တွင် သိသာသော ဝန်ထုပ်ဝန်ပိုး ဖြစ်စေသည်။ ပြဿနာကို လျော့ပါးစေရန်၊ `Event` object တစ်ခုစီကို ၎င်းဖန်တီးပြီး တစ်နာရီအကြာတွင် ဖယ်ရှားသည်။

> **မှတ်ချက်**
> Event များကို ထိန်းသိမ်းထားရမည့် အချိန်ပမာဏကို API server ၏ command-line option များမှတစ်ဆင့် configure လုပ်နိုင်သည်။

#### `kubectl get events` ကို အသုံးပြု၍ event များကို စာရင်းပြုစုခြင်း

`kubectl describe` က ပြသသော event များသည် သင် command ၏ argument အဖြစ် သတ်မှတ်သော object နှင့် သက်ဆိုင်သည်။ ၎င်းတို့၏ သဘောသဘာဝနှင့် object တစ်ခုအတွက် event များစွာကို အချိန်တိုအတွင်း ဖန်တီးနိုင်ခြင်းကြောင့်၊ ၎င်းတို့သည် object ၏ အစိတ်အပိုင်းမဟုတ်ပါ။ ၎င်းတို့ကို object ၏ YAML manifest တွင် သင်ရှာမတွေ့နိုင်ပါ၊ အကြောင်းမှာ ၎င်းတို့သည် `Node` များနှင့် သင်တွေ့ခဲ့ဖူးသော အခြား object များကဲ့သို့ပင် ကိုယ်ပိုင်တည်ရှိနေသောကြောင့် ဖြစ်သည်။

> **မှတ်ချက်**
> သင်သည် ဤကဏ္ဍရှိ လေ့ကျင့်ခန်းများကို သင့်ကိုယ်ပိုင် cluster တွင် လိုက်လုပ်လိုပါက၊ event များ `etcd` တွင် ရှိနေလောက်အောင် လတ်တလောဖြစ်စေရန် node များထဲမှ တစ်ခုကို restart လုပ်ရန် လိုအပ်နိုင်ပါသည်။ သင်ထိုသို့မလုပ်နိုင်ပါက စိတ်မပူပါနှင့်၊ ဤလေ့ကျင့်ခန်းများကို ကိုယ်တိုင်လုပ်ဆောင်ခြင်းမှ ကျော်သွားလိုက်ပါ။ အကြောင်းမှာ သင်သည် နောက်အခန်းရှိ လေ့ကျင့်ခန်းများတွင် event များကို ထုတ်ပေးပြီး စစ်ဆေးရမည်ဖြစ်သောကြောင့် ဖြစ်သည်။

`Event` များသည် သီးခြား object များဖြစ်သောကြောင့်၊ ၎င်းတို့ကို `kubectl get events` ဖြင့် စာရင်းပြုစုနိုင်သည်-

```bash
$ kubectl get ev
LAST SEEN   TYPE      REASON                    OBJECT                  MESSAGE
48s         Normal    Starting                  node/kind-worker2       Starting kubelet.
48s         Normal    NodeAllocatableEnforced   node/kind-worker2       Updated Node Allocatable
48s         Normal    NodeHasSufficientMemory   node/kind-worker2       Node kind-worker2 status is now: NodeHasSufficientMemory
48s         Normal    NodeHasNoDiskPressure     node/kind-worker2       Node kind-worker2 status is now: NodeHasNoDiskPressure
48s         Normal    NodeHasSufficientPID      node/kind-worker2       Node kind-worker2 status is now: NodeHasSufficientPID
47s         Normal    Starting                  node/kind-worker2       Starting kube-proxy...
```

> **မှတ်ချက်**
> ယခင် listing သည် `events` အစား အတိုကောက်အမည် `ev` ကို အသုံးပြုထားသည်။

Listing တွင်ပြထားသော event အချို့သည် Node ၏ status condition များနှင့် ကိုက်ညီသည်ကို သင်သတိပြုမိပါလိမ့်မည်။ ဤသို့ဖြစ်လေ့ရှိသော်လည်း၊ နောက်ထပ် event များကိုလည်း သင်တွေ့ရပါမည်။ `Starting` ဟူသော reason နှင့် event နှစ်ခုသည် ထိုကဲ့သို့သော ဥပမာများဖြစ်သည်။ ဤကိစ္စတွင်၊ ၎င်းတို့သည် Kubelet နှင့် Kube Proxy component များကို စတင်လိုက်ပြီဖြစ်ကြောင်း ညွှန်ပြသည်။

#### Event object တစ်ခုတွင် ပါဝင်သော အချက်အလက်များကို နားလည်ခြင်း

`Event` object တစ်ခုတွင် အောက်ပါ field များပါဝင်သည်-

| Field      | Description                                                                                                                            |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**       | ဤ `Event` object instance ၏ အမည်။ API မှ object ကို ရယူလိုမှသာ အသုံးဝင်သည်။                                                              |
| **Type**       | Event ၏ အမျိုးအစား။ `Normal` သို့မဟုတ် `Warning` ဖြစ်နိုင်သည်။                                                                             |
| **Reason**     | Event ဖြစ်ပွားရခြင်း၏ machine-facing description ဖြစ်သည်။                                                                                |
| **Source**     | ဤ event ကို report တင်သည့် component။ ၎င်းသည် များသောအားဖြင့် controller တစ်ခုဖြစ်သည်။                                                      |
| **Object**     | Event က ရည်ညွှန်းသော object instance။ ဥပမာ `node/xyz`။                                                                                |
| **Sub object** | Event က ရည်ညွှန်းသော sub-object။ ဥပမာ pod ၏ မည်သည့် container ဖြစ်သည်ကို ဖော်ပြသည်။                                                        |
| **Message**    | Event ၏ human-facing description ဖြစ်သည်။                                                                                             |
| **First seen** | ဤ event ပထမဆုံး ဖြစ်ပွားသည့်အချိန်။ `Event` object တစ်ခုစီကို ခဏအကြာတွင် ဖျက်ပစ်သည်ကို သတိရပါ၊ ထို့ကြောင့် ၎င်းသည် event အမှန်တကယ် ပထမဆုံးဖြစ်ပွားသည့် အကြိမ်မဟုတ်နိုင်ပါ။ |
| **Last seen**  | Event များသည် မကြာခဏ ထပ်ခါထပ်ခါ ဖြစ်ပွားတတ်သည်။ ဤ field သည် ဤ event နောက်ဆုံးဖြစ်ပွားသည့် အချိန်ကို ညွှန်ပြသည်။                               |
| **Count**      | ဤ event ဖြစ်ပွားခဲ့သည့် အကြိမ်အရေအတွက်။                                                                                                 |

> **အကြံပြုချက်**
> ဤစာအုပ်တစ်လျှောက် လေ့ကျင့်ခန်းများ ပြီးဆုံးသည့်အခါ၊ သင်၏ object များထဲမှ တစ်ခုကို အပြောင်းအလဲလုပ်တိုင်း `kubectl get events` command ကို run ကြည့်ခြင်းသည် အသုံးဝင်နိုင်ပါသည်။ ၎င်းသည် မျက်နှာပြင်နောက်ကွယ်တွင် ဘာတွေဖြစ်ပျက်နေသည်ကို လေ့လာရန် ကူညီပေးပါလိမ့်မည်။

#### Warning event များကိုသာ ပြသခြင်း

သင်ဖော်ပြနေသော object နှင့် သက်ဆိုင်သော event များကိုသာ ပြသသည့် `kubectl describe` command နှင့် မတူဘဲ၊ `kubectl get events` command သည် event အားလုံးကို ပြသသည်။ သင်စိုးရိမ်သင့်သော event များရှိမရှိ စစ်ဆေးလိုပါက ၎င်းသည် အသုံးဝင်ပါသည်။ `Normal` type event များကို လျစ်လျူရှုပြီး `Warning` type event များကိုသာ အာရုံစိုက်လိုပေမည်။

API သည် `field selector` ဟုခေါ်သော mechanism မှတစ်ဆင့် object များကို filter လုပ်ရန် နည်းလမ်းတစ်ခု ပေးထားသည်။ သတ်မှတ်ထားသော field သည် သတ်မှတ်ထားသော selector value နှင့် ကိုက်ညီသော object များကိုသာ ပြန်ပေးသည်။ သင်သည် `Warning` event များကိုသာ ပြသရန် ၎င်းကို အသုံးပြုနိုင်သည်။ `kubectl get` command သည် `--field-selector` option ဖြင့် field selector ကို သတ်မှတ်ခွင့်ပြုသည်။ `Warning` များကို ကိုယ်စားပြုသော event များကိုသာ စာရင်းပြုစုရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl get ev --field-selector type=Warning
```

### 4.3.2 Event object တစ်ခု၏ manifest အပြည့်အစုံကို ပြသခြင်း

သင့် cluster ရှိ event များကို စစ်ဆေးရန်၊ `kubectl describe` နှင့် `kubectl get events` command များသည် လုံလောက်သင့်သည်။ အခြား object များနှင့် မတူဘဲ၊ `Event` object တစ်ခု၏ YAML အပြည့်အစုံကို ပြသရန် ဘယ်တော့မှ လိုအပ်မည်မဟုတ်ပါ။ သို့သော် API က ပြန်ပေးသော Kubernetes object manifest များနှင့် ပတ်သက်၍ စိတ်အနှောင့်အယှက်ဖြစ်စရာတစ်ခုကို ပြသရန် ဤအခွင့်အရေးကို ကျွန်ုပ်အသုံးပြုလိုပါသည်။

#### Event object များတွင် spec နှင့် status section များ မရှိပါ

`Event` object ၏ ဖွဲ့စည်းပုံကို လေ့လာရန် `kubectl explain` ကို အသုံးပြုပါက၊ ၎င်းတွင် `spec` သို့မဟုတ် `status` section များ မရှိသည်ကို သင်သတိပြုမိပါလိမ့်မည်။ ကံမကောင်းစွာဖြင့်၊ ၎င်းသည် ၎င်း၏ field များကို `Node` object ကဲ့သို့ ကောင်းမွန်စွာ စုစည်းထားခြင်းမရှိဟု ဆိုလိုသည်။ ဥပမာအားဖြင့်၊ အောက်ပါ YAML ကို စစ်ဆေးပြီး object ၏ kind, metadata, နှင့် အခြား field များကို အလွယ်တကူ ရှာဖွေနိုင်မလား ကြည့်ပါ။

```yaml
apiVersion: v1                                             #A
count: 1
eventTime: null
firstTimestamp: "2020-05-17T18:16:40Z"
involvedObject:
  kind: Node
  name: kind-worker2
  uid: kind-worker2
kind: Event                                                #B
lastTimestamp: "2020-05-17T18:16:40Z"
message: Starting kubelet.
metadata:                                                  #C
  name: kind-worker2.1610a1206f4f910f
  namespace: default
  ...
reason: Starting
reportingComponent: ""
reportingInstance: ""
source:
  component: kubelet
  host: kind-worker2
type: Normal
```

*   **A** `apiVersion` သည် အပေါ်ဆုံးတွင် ရှိသည်။
*   **B** `kind` field သည် အလယ်တစ်နေရာတွင် ရှိသည်။
*   **C** `metadata` field သည် `message` field ၏နောက်တွင် ရှိသည်။

API server မှ ပြန်ပေးသော object manifest များရှိ field များသည် အက္ခရာစဉ်အတိုင်း စီထားသည်။ ၎င်းတို့သည် မျှော်လင့်ထားသည့်အတိုင်း အုပ်စုဖွဲ့ထားခြင်းမရှိပါ။ ၎င်းသည် လူသားများအတွက် ဖတ်ရှုရန် ခက်ခဲစေသည်။ ၎င်းသည် အလွန်ရှုပ်ထွေးနေသောကြောင့် လူအများအပြား Kubernetes YAML သို့မဟုတ် JSON manifest များနှင့် ဆက်ဆံရခြင်းကို မုန်းတီးကြသည်မှာ အံ့သြစရာမရှိပါ၊ အကြောင်းမှာ နှစ်မျိုးလုံးသည် ဤပြဿနာကို ခံစားနေရသောကြောင့်ဖြစ်သည်။

ဆန့်ကျင်ဘက်အားဖြင့်၊ `Node` object ၏ အစောပိုင်း YAML manifest သည် top-level field များ၏ အစီအစဉ်သည် လူတစ်ဦး မျှော်လင့်ထားသည့်အတိုင်းဖြစ်သောကြောင့် ဖတ်ရှုရန် အတော်လေးလွယ်ကူသည်- `apiVersion`, `kind`, `metadata`, `spec`, နှင့် `status`။ သင်သတိပြုမိလိမ့်မည်မှာ၊ ဤငါးခု၏ အက္ခရာစဉ်သည် အဓိပ္ပာယ်ရှိနေသောကြောင့်သာ ဖြစ်သည်။ သို့သော် ထို field များအောက်ရှိ field များသည် တူညီသောပြဿနာကို ခံစားနေရသည်၊ အကြောင်းမှာ ၎င်းတို့ကိုလည်း အက္ခရာစဉ်အတိုင်း စီထားသောကြောင့်ဖြစ်သည်။

YAML သည် လူများဖတ်ရှုရန် လွယ်ကူစေရန် ရည်ရွယ်သော်လည်း၊ Kubernetes YAML ရှိ အက္ခရာစဉ် field အစီအစဉ်သည် ဤအချက်ကို ချိုးဖောက်သည်။ ကံကောင်းထောက်မစွာ၊ object အများစုတွင် `spec` နှင့် `status` section များပါဝင်သောကြောင့်၊ အနည်းဆုံး ဤ object များရှိ top-level field များသည် ကောင်းမွန်စွာ စုစည်းထားသည်။ ကျန်အရာများအတွက်မူ၊ Kubernetes manifest များနှင့် ဆက်ဆံရာတွင် ဤကံမကောင်းသောအချက်ကို သင်လက်ခံရုံသာ ရှိပါလိမ့်မည်။

## 4.4 အနှစ်ချုပ်

ဤအခန်းတွင် သင်လေ့လာခဲ့သည်မှာ-

*   Kubernetes API object များသည် cluster ၏ configuration ကို ကိုယ်စားပြုသည်။ သင်သည် cluster ၏ အပြုအမူကို ပြောင်းလဲလိုသောအခါ၊ သင်သည် ဤ object များကို ပြင်ဆင်မွမ်းမံသည်။
*   object တစ်ခုတွင် `Type Metadata`, `Object Metadata`, `spec` (သင် object ဖြစ်စေလိုသော desired state), နှင့် `status` (object ၏ actual current state) တို့ ပါဝင်သည်။
*   Controller များသည် object ၏ `spec` ကို ဖတ်ပြီး ၎င်းကို အသက်ဝင်လာစေရန် လုပ်ဆောင်ချက်များကို လုပ်ဆောင်သည်။ ၎င်းတို့သည် object ၏ `status` ကို ရေးသားခြင်းဖြင့် ၎င်းတို့၏ အလုပ်၏ state ကို report တင်သည်။
*   `kubectl describe` command သည် object တစ်ခု၏ လူသားများဖတ်ရှုရန်လွယ်ကူသော အကျဉ်းချုပ်ကို ပေးသည်။ ၎င်းသည် object နှင့် သက်ဆိုင်သော event များကိုလည်း ပြသသည်။
*   `kubectl get -o yaml` (သို့မဟုတ် `-o json`) command သည် object ၏ manifest အပြည့်အစုံကို ပြသသည်။
*   Controller များသည် Kubernetes API object များကို စီမံခန့်ခွဲသည့်အခါ၊ ၎င်းတို့လုပ်ဆောင်ခဲ့သော လုပ်ဆောင်ချက်များကို ဖော်ပြရန် event များကို ထုတ်လွှင့်သည်။ အရာအားလုံးကဲ့သို့ပင်၊ event များကို `Event` object များဖြင့် ကိုယ်စားပြုပြီး API မှတစ်ဆင့် ရယူနိုင်သည်။ Event များသည် `Node` သို့မဟုတ် အခြား object တစ်ခုခုတွင် ဘာတွေဖြစ်ပျက်နေသည်ကို ညွှန်ပြသည်။ ၎င်းတို့သည် object နှင့်ပတ်သက်၍ မကြာသေးမီက ဖြစ်ပျက်ခဲ့သောအရာများကို ပြသပြီး ၎င်းဘာကြောင့် ပျက်စီးနေသည်ကို ညွှန်ပြချက်များ ပေးနိုင်ပါသည်။
*   `kubectl explain` command သည် command line မှ object kind တစ်ခုနှင့် ၎င်း၏ field များအကြောင်း documentation ကို လျင်မြန်စွာ ကြည့်ရှုရန် နည်းလမ်းတစ်ခု ပေးသည်။
*   `Node` object တစ်ခု၏ `status` တွင် node ၏ IP address နှင့် hostname၊ ၎င်း၏ resource capacity၊ condition များ၊ cached container image များနှင့် node အကြောင်း အခြားအချက်အလက်များ ပါဝင်သည်။ node ပေါ်တွင် run နေသော Pod များသည် node ၏ status ၏ အစိတ်အပိုင်းမဟုတ်ပါ၊ သို့သော် `kubectl describe node` command သည် ဤအချက်အလက်ကို pod resource မှ ရယူသည်။
*   Object type များစွာသည် object က ကိုယ်စားပြုသော component ၏ state ကို ညွှန်ပြရန် status condition များကို အသုံးပြုသည်။ node များအတွက်၊ ဤ condition များမှာ `MemoryPressure`, `DiskPressure` နှင့် `PIDPressure` တို့ဖြစ်သည်။
