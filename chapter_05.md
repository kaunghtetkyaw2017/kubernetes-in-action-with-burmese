# အခန်း ၅။ Pod များတွင် Workload များ Run ခြင်း

ယခင်အခန်း၏ သုံးသပ်ချက်အနေဖြင့်၊ အခန်း ၃ တွင် Kubernetes တွင် အနည်းဆုံး application တစ်ခုကို deploy လုပ်ရန် သင်ဖန်တီးခဲ့သော object အမျိုးအစားသုံးမျိုးကို ပြသသည့် diagram တစ်ခုကို ပြန်လည်ကြည့်ရှုကြပါစို့။ ပုံ ၅.၁ သည် ၎င်းတို့အချင်းချင်း မည်သို့ဆက်စပ်နေသည်နှင့် system တွင် ၎င်းတို့၏ လုပ်ဆောင်ချက်များကို ပြသထားသည်။

*ပုံ ၅.၁။ Deployed application တစ်ခုတွင် ပါဝင်သော အခြေခံ object အမျိုးအစား သုံးမျိုး*

ယခု သင်သည် ဤ object များကို Kubernetes API မှတစ်ဆင့် မည်သို့ဖော်ပြသည်ကို အခြေခံနားလည်သဘောပေါက်ပြီဖြစ်သည်။ ဤအခန်းနှင့် နောက်ဆက်တွဲအခန်းများတွင်၊ သင်သည် ၎င်းတို့တစ်ခုချင်းစီနှင့် ပြည့်စုံသော application တစ်ခုကို deploy လုပ်ရာတွင် အသုံးပြုလေ့ရှိသော အခြားများစွာသော object များအကြောင်း အသေးစိတ်လေ့လာရမည်ဖြစ်သည်။ `Pod` object မှ စတင်ကြပါစို့၊ အကြောင်းမှာ ၎င်းသည် Kubernetes တွင် ဗဟိုအကျဆုံးနှင့် အရေးကြီးဆုံး concept ကို ကိုယ်စားပြုသောကြောင့်ဖြစ်သည်။

## 5.1 Pod များကို နားလည်ခြင်း

သင်သိပြီးသားဖြစ်သည့်အတိုင်း၊ `pod` ဆိုသည်မှာ အတူတကွတည်ရှိသော `container` အုပ်စုတစ်ခုဖြစ်ပြီး Kubernetes တွင် အခြေခံကျဆုံးသော တည်ဆောက်မှုအစိတ်အပိုင်း ဖြစ်သည်။ `container` များကို တစ်ခုချင်း deploy လုပ်မည့်အစား၊ `container` အုပ်စုတစ်စုကို `pod` ဟုခေါ်သော unit တစ်ခုတည်းအဖြစ် သင် deploy လုပ်ပြီး စီမံခန့်ခွဲနိုင်သည်။ `pod` များတွင် `container` များစွာပါဝင်နိုင်သော်လည်း၊ `pod` တစ်ခုတွင် `container` တစ်ခုတည်းသာ ပါဝင်ခြင်းသည်လည်း မဆန်းပါ။ `pod` တစ်ခုတွင် `container` များစွာရှိလျှင်၊ ၎င်းတို့အားလုံးသည် တူညီသော worker node တစ်ခုပေါ်တွင် run ကြသည် — `pod` instance တစ်ခုတည်းသည် node များစွာကို ဘယ်တော့မှ ခွဲခြမ်း၍ run ခြင်းမရှိပါ။ ပုံ ၅.၂ သည် ဤအချက်အလက်ကို ပုံဖော်နားလည်ရန် ကူညီပေးပါလိမ့်မည်။

*ပုံ ၅.၂။ pod တစ်ခု၏ container အားလုံးသည် တူညီသော node ပေါ်တွင် run သည်။ pod တစ်ခုသည် node များစွာကို ဘယ်တော့မှ ခွဲခြမ်း run ခြင်းမရှိပါ။*

### 5.1.1 ကျွန်ုပ်တို့ pod များကို အဘယ်ကြောင့် လိုအပ်သည်ကို နားလည်ခြင်း

ဥပမာအားဖြင့်၊ `container` တစ်ခုတည်းတွင် process များစွာ run ခြင်းနှင့်မတူဘဲ၊ `container` များစွာကို အတူတကွ run ရန် အဘယ်ကြောင့် လိုအပ်သည်ကို ဆွေးနွေးကြပါစို့။

#### container တစ်ခုတွင် process များစွာ မပါဝင်သင့်သည့် အကြောင်းရင်းကို နားလည်ခြင်း

IPC (Inter-Process Communication) သို့မဟုတ် shared file များမှတစ်ဆင့် အချင်းချင်း ဆက်သွယ်သော process များစွာပါဝင်သည့် application တစ်ခုကို စဉ်းစားကြည့်ပါ။ ၎င်းတို့သည် တူညီသော ကွန်ပျူတာပေါ်တွင် run ရန် လိုအပ်သည်။ အခန်း ၂ တွင်၊ `container` တစ်ခုစီသည် သီးခြားကွန်ပျူတာ သို့မဟုတ် virtual machine တစ်ခုနှင့်တူသည်ကို သင်လေ့လာခဲ့သည်။ ကွန်ပျူတာတစ်ခုသည် များသောအားဖြင့် process များစွာကို run ထားသည်၊ `container` များလည်း ထိုသို့လုပ်ဆောင်နိုင်သည်။ application တစ်ခုကို ဖွဲ့စည်းသည့် process အားလုံးကို `container` တစ်ခုတည်းတွင် run နိုင်သော်လည်း၊ ၎င်းသည် `container` ကို စီမံခန့်ခွဲရန် အလွန်ခက်ခဲစေသည်။

`Container` များသည် ၎င်း spawn လုပ်သော child process များကို မရေတွက်ဘဲ process တစ်ခုတည်းသာ run ရန် ဒီဇိုင်းထုတ်ထားသည်။ `container` tool များနှင့် Kubernetes နှစ်မျိုးလုံးသည် ဤအချက်ကို အခြေခံ၍ တီထွင်ထားသည်။ ဥပမာအားဖြင့်၊ `container` တစ်ခုတွင် run နေသော process သည် ၎င်း၏ log များကို standard output သို့ ရေးသားရန် မျှော်လင့်ရသည်။ သင် log များကို ပြသရန် အသုံးပြုသော Docker နှင့် Kubernetes command များသည် ဤ output မှ ဖမ်းယူထားသည်များကိုသာ ပြသသည်။ `container` တွင် process တစ်ခုတည်းသာ run နေပါက၊ ၎င်းသည် တစ်ဦးတည်းသော writer ဖြစ်သည်။ သို့သော် သင် `container` တွင် process များစွာ run ပါက၊ ၎င်းတို့အားလုံးသည် တူညီသော output stream သို့ ရေးသားကြပြီး၊ ၎င်းသည် log များကို ခွဲခြမ်းစိတ်ဖြာရန် ခက်ခဲစေနိုင်သည်။

ထို့အပြင်၊ `container` များသည် ၎င်း၏ primary process ရပ်တန့်သွားသောအခါ ၎င်း၏ child process များကို မှန်ကန်စွာ ရပ်တန့်ခြင်း (reaping) မလုပ်သောကြောင့် zombie process များ ဖြစ်ပေါ်လာနိုင်သည်။ Kubernetes သည် `container` ၏ primary process ၏ status ကို စောင့်ကြည့်ခြင်းဖြင့် `container` ၏ health ကို စီမံခန့်ခွဲသည်။ ၎င်းသည် `container` အတွင်းရှိ အခြား process များကို စောင့်ကြည့်ခြင်းမရှိပါ။

#### pod များက container များကို အတူတကွ အလုပ်လုပ်နိုင်အောင် မည်သို့ကူညီပေးသည်

အချင်းချင်း အလွန်နီးကပ်စွာ ဆက်စပ်နေသော process များကို `container` တစ်ခုတည်းတွင် ထားရှိမည့်အစား၊ ၎င်းတို့ကို သီးခြား `container` များတွင် ထားပြီး `pod` တစ်ခုတည်းတွင် အတူတကွ run နိုင်သည်။ ဤ process များသည် အနည်းငယ် သီးခြားဖြစ်သော်လည်း လုံးဝမဟုတ်ပါ - ၎င်းတို့သည် အချို့သော resource များကို မျှဝေသုံးစွဲကြသည်။ ၎င်းသည် သင့်အား ကဏ္ဍနှစ်ခုလုံး၏ အကောင်းဆုံးကို ပေးသည်။ သင်သည် `container` များက ပေးသော feature အားလုံးကို အသုံးပြုနိုင်သော်လည်း process များကို အတူတကွ အလုပ်လုပ်ခွင့်လည်း ပေးနိုင်သည်။ `pod` တစ်ခုသည် ဤအပြန်အလှန်ချိတ်ဆက်ထားသော `container` များကို unit တစ်ခုတည်းအဖြစ် စီမံခန့်ခွဲနိုင်စေသည်။

ဒုတိယအခန်းတွင်၊ `container` တစ်ခုသည် ၎င်း၏ကိုယ်ပိုင် Linux namespace အစုံကို အသုံးပြုသော်လည်း၊ ၎င်းသည် အချို့ကို အခြား `container` များနှင့် မျှဝေနိုင်သည်ကို သင်လေ့လာခဲ့သည်။ ဤ namespace များကို မျှဝေခြင်းသည် Kubernetes နှင့် `container` runtime က `container` များကို `pod` များအဖြစ် ပေါင်းစပ်သည့် နည်းလမ်းပင်ဖြစ်သည်။

ပုံ ၅.၃ တွင် ပြထားသည့်အတိုင်း၊ `pod` တစ်ခုရှိ `container` အားလုံးသည် တူညီသော Network namespace ကို မျှဝေသုံးစွဲကြပြီး ထို့ကြောင့် ၎င်းနှင့်သက်ဆိုင်သော network interface များ၊ IP address(များ) နှင့် port space ကို မျှဝေသုံးစွဲကြသည်။

*ပုံ ၅.၃။ pod တစ်ခုရှိ Container များသည် တူညီသော network interface များကို မျှဝေသုံးစွဲကြသည်*

port space ကို မျှဝေသုံးစွဲသောကြောင့်၊ တူညီသော `pod` ၏ `container` များတွင် run နေသော process များသည် တူညီသော port number များကို bind လုပ်၍မရပါ။ သို့သော် အခြား `pod` များရှိ process များသည် ၎င်းတို့၏ကိုယ်ပိုင် network interface များနှင့် port space များရှိသောကြောင့် port conflict များကို ဖယ်ရှားပေးသည်။ `pod` တစ်ခုရှိ `container` အားလုံးသည် တူညီသော UTS နှင့် IPC namespace များကိုလည်း မျှဝေသုံးစွဲသောကြောင့် ၎င်းတို့သည် တူညီသော hostname ကို မြင်ရပြီး System V inter-process communication mechanism များမှတစ်ဆင့် အချင်းချင်း ဆက်သွယ်နိုင်သည်။ သို့သော်၊ `pod` တစ်ခုရှိ `container` တစ်ခုချင်းစီသည် ၎င်း၏ကိုယ်ပိုင် PID namespace ကို ပုံမှန်အားဖြင့် ရရှိသော်လည်း ၎င်းတို့ကို မျှဝေရန် configure လုပ်နိုင်သည်။

#### container များသည် မတူညီသော filesystem များရှိသည်

ဆန့်ကျင်ဘက်အားဖြင့်၊ `container` တစ်ခုစီသည် ၎င်း၏ကိုယ်ပိုင် Mount namespace အမြဲရှိပြီး၊ ၎င်းကို ကိုယ်ပိုင် filesystem ပေးသည်။ သို့သော် `container` နှစ်ခုက filesystem ၏ တစ်စိတ်တစ်ပိုင်းကို မျှဝေရန် လိုအပ်သည့်အခါ၊ သင်သည် `pod` သို့ `volume` တစ်ခုကို ထည့်ပြီး ၎င်းကို `container` နှစ်ခုလုံးတွင် mount လုပ်နိုင်သည်။ `container` နှစ်ခုသည် သီးခြား Mount namespace နှစ်ခုကို အသုံးပြုနေဆဲဖြစ်သော်လည်း၊ shared `volume` ကို နှစ်ခုလုံးတွင် mount လုပ်ထားသည်။ အခန်း ၇ တွင် `volume` များအကြောင်း ပိုမိုလေ့လာရပါမည်။

### 5.1.2 container များကို pod များအဖြစ် ဖွဲ့စည်းခြင်း

`pod` တစ်ခုချင်းစီကို သီးခြားကွန်ပျူတာတစ်ခုအဖြစ် သင်စဉ်းစားနိုင်သည်။ application များစွာကို host လုပ်လေ့ရှိသော virtual machine များနှင့် မတူဘဲ၊ သင်သည် များသောအားဖြင့် `pod` တစ်ခုစီတွင် application တစ်ခုတည်းသာ run သည်။ `pod` တစ်ခုတည်းတွင် application များစွာကို ပေါင်းစပ်ရန် ဘယ်တော့မှ မလိုအပ်ပါ၊ အကြောင်းမှာ `pod` များသည် resource overhead နီးပါးမရှိသောကြောင့်ဖြစ်သည်။

#### container များစွာကို pod တစ်ခုတည်းတွင် မည်သည့်အချိန်၌ ထည့်သွင်းရမည်နည်း

အောက်ပါပုံတွင်ပြထားသည့်အတိုင်း၊ multi-tier application တစ်ခု၏ component အားလုံးကို `pod` တစ်ခုတည်းတွင် ထည့်သွင်းရန် စဉ်းစားမိနိုင်သည်။ ဥပမာအားဖြင့်၊ frontend web server နှင့် backend database တို့ကို `container` နှစ်ခုတွင် ထည့်ပြီး ၎င်းတို့ကို `pod` တစ်ခုတည်းတွင် run နိုင်သည်။

frontend server နှင့် database နှစ်ခုလုံးကို `pod` တစ်ခုတည်းတွင် run ခြင်းကို မည်သည့်အရာကမှ မတားဆီးသော်လည်း၊ ၎င်းသည် အကောင်းဆုံးနည်းလမ်းမဟုတ်ပါ။ `pod` တစ်ခု၏ `container` အားလုံးသည် အမြဲတမ်း အတူတကွတည်ရှိသည်ကို ကျွန်တော်ရှင်းပြခဲ့ပြီးဖြစ်သည်၊ သို့သော် web server နှင့် database တို့သည် တူညီသောကွန်ပျူတာပေါ်တွင် run ရန် လိုအပ်ပါသလား။ အဖြေမှာ ရှင်းလင်းစွာ မလိုအပ်ပါ၊ အကြောင်းမှာ ၎င်းတို့သည် network မှတစ်ဆင့် အလွယ်တကူ ဆက်သွယ်နိုင်သောကြောင့်ဖြစ်သည်။ ထို့ကြောင့် ၎င်းတို့ကို တူညီသော `pod` တွင် မ run သင့်ပါ။

frontend နှင့် backend နှစ်ခုလုံးသည် တူညီသော `pod` တွင်ရှိပါက၊ နှစ်ခုလုံးသည် တူညီသော cluster node ပေါ်တွင် run သည်။ သင်၌ node နှစ်ခုရှိသော cluster တစ်ခုရှိပြီး ဤ `pod` တစ်ခုတည်းကိုသာ ဖန်တီးပါက၊ သင်သည် worker node တစ်ခုတည်းကိုသာ အသုံးပြုနေပြီး ဒုတိယ node ပေါ်ရှိ computing resource များကို အသုံးမပြုပါ။ ၎င်းသည် CPU, memory, disk storage နှင့် bandwidth တို့ကို ဖြုန်းတီးခြင်းဖြစ်သည်။ `container` များကို `pod` နှစ်ခုအဖြစ် ခွဲခြမ်းခြင်းဖြင့် Kubernetes အား frontend `pod` ကို node တစ်ခုပေါ်တွင် ထားရှိပြီး backend `pod` ကို အခြား node တစ်ခုပေါ်တွင် ထားရှိခွင့်ပေးကာ၊ သင်၏ hardware အသုံးပြုမှုကို ပိုမိုကောင်းမွန်စေသည်။

#### တစ်ခုချင်း scaling လုပ်နိုင်ရန် pod များစွာအဖြစ် ခွဲခြမ်းခြင်း

single `pod` ကို မသုံးသင့်သည့် နောက်အကြောင်းရင်းတစ်ခုမှာ scaling ဖြစ်သည်။ `pod` သည် Kubernetes တွင် scaling ၏ အခြေခံ unit ဖြစ်သည်။ `pod` တစ်ခု၏ `container` များကို တစ်ခုချင်း scale လုပ်၍မရပါ။ frontend နှင့် backend နှစ်ခုလုံးကို `pod` တစ်ခုတည်းတွင် ထားပါက၊ သင်သည် ၎င်းတို့ကို အတူတကွ scale လုပ်ရမည်ဖြစ်သည်။

application stack များကို `pod` များစွာအဖြစ် ခွဲခြမ်းခြင်းသည် မှန်ကန်သောချဉ်းကပ်မှု ဖြစ်သည်။ သို့သော်၊ မည်သည့်အချိန်၌ `container` များစွာကို `pod` တစ်ခုတည်းတွင် run သနည်း။

#### Sidecar container များကို မိတ်ဆက်ခြင်း

`container` များစွာကို `pod` တစ်ခုတည်းတွင် ထားခြင်းသည် application တွင် primary process တစ်ခုနှင့် primary process ၏ လုပ်ဆောင်ချက်ကို ဖြည့်စွက်ပေးသော process တစ်ခု သို့မဟုတ် တစ်ခုထက်ပို၍ ပါဝင်မှသာ သင့်လျော်သည်။ ဖြည့်စွက် process run နေသော `container` ကို **sidecar container** ဟုခေါ်သည်။ အကြောင်းမှာ ၎င်းသည် ဆိုင်ကယ်ကို ပိုမိုတည်ငြိမ်စေပြီး နောက်ထပ်ခရီးသည်တစ်ဦးကို သယ်ဆောင်နိုင်ခွင့်ပေးသော ဆိုင်ကယ်ဘေးတွဲ (motorcycle sidecar) နှင့် ဆင်တူသောကြောင့်ဖြစ်သည်။ သို့သော် ဆိုင်ကယ်များနှင့်မတူဘဲ၊ `pod` တစ်ခုတွင် sidecar တစ်ခုထက်ပို၍ ရှိနိုင်သည် (ပုံ ၅.၅ တွင် ပြထားသည်)။

*ပုံ ၅.၅။ primary နှင့် sidecar container(များ) ပါသော pod တစ်ခု*

ဖြည့်စွက် process တစ်ခုဆိုသည်မှာ ဘာလဲဟု စိတ်ကူးကြည့်ရန် ခက်ခဲသောကြောင့်၊ ဥပမာအချို့ကို ကျွန်တော်ပေးပါမည်။ အခန်း ၂ တွင်၊ Node.js application run နေသော `container` တစ်ခုပါဝင်သည့် `pod` များကို သင် deploy ခဲ့သည်။ Node.js application သည် HTTP protocol ကိုသာ support လုပ်သည်။ ၎င်းကို HTTPS support လုပ်စေရန်၊ ကျွန်ုပ်တို့ JavaScript code အနည်းငယ် ထပ်ထည့်နိုင်သော်လည်း၊ ရှိပြီးသား application ကို လုံးဝမပြောင်းလဲဘဲလည်း လုပ်ဆောင်နိုင်သည် - `pod` သို့ နောက်ထပ် `container` တစ်ခု ထပ်ထည့်ခြင်းဖြင့် - HTTPS traffic ကို HTTP သို့ ပြောင်းလဲပြီး Node.js `container` သို့ forward လုပ်ပေးသော reverse proxy တစ်ခုဖြစ်သည်။ Node.js `container` သည် primary `container` ဖြစ်ပြီး၊ proxy run နေသော `container` သည် sidecar `container` ဖြစ်သည်။ ပုံ ၅.၆ သည် ဤဥပမာကို ပြသထားသည်။

*ပုံ ၅.၆။ HTTPS traffic ကို HTTP သို့ ပြောင်းလဲပေးသော Sidecar container*

နောက်ဥပမာတစ်ခု (ပုံ ၅.၇) တွင်၊ primary `container` သည် ၎င်း၏ webroot directory မှ file များကို serve လုပ်သော web server ကို run ထားသည်။ `pod` ရှိ အခြား `container` သည် external source မှ content များကို အချိန်မှန် ဒေါင်းလုဒ်လုပ်ပြီး web server ၏ webroot directory တွင် သိမ်းဆည်းသော agent တစ်ခုဖြစ်သည်။ အစောပိုင်းတွင် ကျွန်တော်ပြောခဲ့သည့်အတိုင်း၊ `container` နှစ်ခုသည် `volume` တစ်ခုကို မျှဝေသုံးစွဲခြင်းဖြင့် file များကို မျှဝေနိုင်သည်။ webroot directory သည် ဤ `volume` ပေါ်တွင် တည်ရှိမည်ဖြစ်သည်။

*ပုံ ၅.၇။ Web server container သို့ shared volume မှတစ်ဆင့် content ပေးပို့သော Sidecar container*

sidecar `container` များ၏ အခြားဥပမာများမှာ log rotator များနှင့် collector များ၊ data processor များ၊ communication adapter များနှင့် အခြားအရာများ ဖြစ်သည်။

application ၏ ရှိပြီးသား code ကို ပြောင်းလဲခြင်းနှင့် မတူဘဲ၊ sidecar တစ်ခုထပ်ထည့်ခြင်းသည် `pod` ၏ resource လိုအပ်ချက်များကို တိုးစေသည်၊ အကြောင်းမှာ `pod` တွင် နောက်ထပ် process တစ်ခု run ရန် လိုအပ်သောကြောင့်ဖြစ်သည်။ သို့သော်၊ legacy application များသို့ code ထပ်ထည့်ခြင်းသည် အလွန်ခက်ခဲနိုင်သည်ကို သတိရပါ။

#### container များကို pod များစွာအဖြစ် ခွဲခြမ်းရန် မည်သို့ဆုံးဖြတ်ရမည်နည်း

sidecar pattern ကို အသုံးပြုပြီး `container` များကို `pod` တစ်ခုတည်းတွင် ထားရန် သို့မဟုတ် သီးခြား `pod` များတွင် ထားရန် ဆုံးဖြတ်သည့်အခါ၊ သင့်ကိုယ်သင် အောက်ပါမေးခွန်းများကို မေးပါ-

*   ဤ `container` များသည် တူညီသော host ပေါ်တွင် run ရန် လိုအပ်ပါသလား။
*   ၎င်းတို့ကို unit တစ်ခုတည်းအဖြစ် စီမံခန့်ခွဲလိုပါသလား။
*   ၎င်းတို့သည် သီးခြား component များဖြစ်မည့်အစား ပေါင်းစည်းထားသော အစုအဖွဲ့တစ်ခု ဖွဲ့စည်းပါသလား။
*   ၎င်းတို့ကို အတူတကွ scale လုပ်ရန် လိုအပ်ပါသလား။
*   node တစ်ခုတည်းက ၎င်းတို့၏ ပေါင်းစပ် resource လိုအပ်ချက်များကို ဖြည့်ဆည်းပေးနိုင်ပါသလား။

ဤမေးခွန်းအားလုံးအတွက် အဖြေမှာ ဟုတ်သည်ဖြစ်ပါက၊ ၎င်းတို့အားလုံးကို တူညီသော `pod` တွင် ထားပါ။ ယေဘုယျအားဖြင့်၊ `container` များကို တူညီသော `pod` ၏ အစိတ်အပိုင်းဖြစ်ရန် တိကျသောအကြောင်းရင်း မရှိလျှင် သီးခြား `pod` များတွင် အမြဲထားပါ။

## 5.2 Pod များကို YAML သို့မဟုတ် JSON file များမှ ဖန်တီးခြင်း

ယခင်အခန်းများတွင် သင် `pod` များကို တိုက်ရိုက်ဖန်တီးမည့်အစား `kubectl run` နှင့် `kubectl expose` ကဲ့သို့သော imperative command များကို အသုံးပြု၍ object များဖန်တီးခဲ့သည်။ ၎င်းသည် object များကို လျင်မြန်စွာဖန်တီးရန် လွယ်ကူသောနည်းလမ်းဖြစ်သော်လည်း၊ ၎င်းသည် သင်၏ application ကို မည်သို့ deploy လုပ်သည်ကို မှတ်တမ်းတင်ထားခြင်းမရှိပါ။

Kubernetes နှင့် အလုပ်လုပ်သည့်အခါ၊ သင်သည် object များကို YAML သို့မဟုတ် JSON format ဖြင့် ရေးသားထားသော manifest file များမှတစ်ဆင့် ဖန်တီး၊ update လုပ်ပြီး delete လုပ်သင့်သည်။ ဤချဉ်းကပ်မှုကို declarative approach ဟုခေါ်သည်။ ဤ manifest file များကို အသုံးပြုခြင်းသည် သင်၏ deployment များကို ပြန်လည်ပြုလုပ်နိုင်စေရန် သေချာစေပြီး၊ ဤ file များကို VCS (Version Control System) တွင် သိမ်းဆည်းခြင်းဖြင့် ပြောင်းလဲမှုအားလုံး၏ history ကို သင်သိမ်းဆည်းထားနိုင်သည်။ code များကို သိမ်းဆည်းသကဲ့သို့ပင်။

တကယ်တော့၊ ဤစာအုပ်ပါ လေ့ကျင့်ခန်းများ၏ application manifest များအားလုံးကို VCS တွင် သိမ်းဆည်းထားပါသည်။ ၎င်းတို့ကို GitHub တွင် `github.com/luksa/kubernetes-in-action-2nd-edition` တွင် သင်ရှာတွေ့နိုင်ပါသည်။

### 5.2.1 pod တစ်ခုအတွက် YAML manifest တစ်ခု ဖန်တီးခြင်း

ယခင်အခန်းတွင် ရှိပြီးသား API object များ၏ YAML manifest များကို မည်သို့ရယူပြီး စစ်ဆေးရမည်ကို သင်လေ့လာခဲ့သည်။ ယခု သင်သည် object manifest တစ်ခုကို အစမှစ၍ ဖန်တီးမည်ဖြစ်သည်။

သင်သည် `pod.kiada.yaml` ဟုခေါ်သော file တစ်ခုကို သင်၏ကွန်ပျူတာပေါ်တွင် သင်နှစ်သက်ရာနေရာ၌ ဖန်တီးခြင်းဖြင့် စတင်ပါမည်။ ဤ file ကို စာအုပ်၏ code archive ရှိ `Chapter05/` directory တွင်လည်း သင်ရှာတွေ့နိုင်သည်။ အောက်ပါ listing သည် file ၏ content များကို ပြသထားသည်။

**Listing 5.1။ အခြေခံ pod manifest file တစ်ခု**

```yaml
apiVersion: v1              # A
kind: Pod                   # B
metadata:
  name: kiada                # C
spec:
  containers:
  - name: kiada              # D
    image: luksa/kiada:0.1   # E
    ports:
    - containerPort: 8080    # F
```

*   **A** ဤသည်မှာ `v1` API schema ကို အသုံးပြုသော object တစ်ခုဖြစ်သည်။
*   **B** object ၏ အမျိုးအစား (kind) မှာ `Pod` ဖြစ်သည်။
*   **C** `pod` ၏ အမည်မှာ `kiada` ဖြစ်သည်။
*   **D** `pod` တွင် `container` တစ်ခုပါဝင်ပြီး ၎င်း၏အမည်မှာ `kiada` ဖြစ်သည်။
*   **E** `container` သည် `luksa/kiada:0.1` image ကို အသုံးပြုသည်။
*   **F** `container` အတွင်းရှိ application သည် port `8080` တွင် listen လုပ်နေသည်။

ဤ `pod` manifest သည် အတော်လေးရိုးရှင်းပြီး အလွန်ရှုပ်ထွေးခြင်းမရှိကြောင်း သင်သဘောတူမည်မှာ သေချာပါသည်။ သို့သော် သင်သည် ဤစာအုပ်ကို ဆက်လက်ဖတ်ရှုပြီး ဤ manifest သို့ နောက်ထပ် field များ ထပ်ထည့်သည့်အခါ ဤသို့မဟုတ်တော့ပါ။ သို့သော် ၎င်းကို နောက်မှ ဆက်လက်လေ့လာပါမည်။

object ကို မဖန်တီးမီ၊ manifest ကို အသေးစိတ်လေ့လာကြည့်ကြပါစို့။ ၎င်းသည် object ကို ဖော်ပြရန် Kubernetes API ၏ version `v1` ကို အသုံးပြုသည်။ object kind မှာ `Pod` ဖြစ်ပြီး object ၏ name မှာ `kiada` ဖြစ်သည်။ `pod` တွင် `kiada` ဟုခေါ်သော `container` တစ်ခုပါဝင်ပြီး `luksa/kiada:0.1` image ကို အခြေခံထားသည်။ `pod` definition တွင် `container` အတွင်းရှိ application သည် port `8080` တွင် listen လုပ်ကြောင်းကိုလည်း သတ်မှတ်ထားသည်။

> **အကြံပြုချက်**
> `pod` manifest တစ်ခုကို အစမှစ၍ ဖန်တီးလိုသည့်အခါတိုင်း၊ file ကို ဖန်တီးရန်နှင့် field များထပ်ထည့်ရန် edit လုပ်ရန် အောက်ပါ command ကိုလည်း အသုံးပြုနိုင်သည်- `kubectl run kiada --image=luksa/kiada:0.1 --dry-run=client -o yaml > mypod.yaml`။ `--dry-run=client` flag သည် `kubectl` အား API မှတစ်ဆင့် object ကို အမှန်တကယ်ဖန်တီးမည့်အစား definition ကို output ထုတ်ရန် ပြောပြသည်။

YAML file ရှိ field များသည် ရှင်းလင်းပြတ်သားသော်လည်း၊ field တစ်ခုချင်းစီအကြောင်း ပိုမိုသိရှိလိုပါက သို့မဟုတ် နောက်ထပ် field များ ထပ်ထည့်နိုင်မလား သိလိုပါက `kubectl explain pods` command ကို အသုံးပြုရန် သတိရပါ။

### 5.2.2 pod ကို ဖန်တီးခြင်း

ယခု `pod` manifest file အဆင်သင့်ဖြစ်ပြီဖြစ်သောကြောင့်၊ Kubernetes API server သို့ post တင်ခြင်းဖြင့် object ကို ဖန်တီးနိုင်ပြီဖြစ်သည်။

#### manifest file ကို apply လုပ်ခြင်း

file ကို apply လုပ်ရန် `kubectl apply` command ကို အသုံးပြုပါ-

```bash
$ kubectl apply -f pod.kiada.yaml
pod/kiada created
```

`kubectl apply` command ကို object များဖန်တီးရန်နှင့် ရှိပြီးသား object များကို ပြင်ဆင်ရန်အတွက် အသုံးပြုသည်။ နောက်ပိုင်းတွင် သင်၏ `pod` object ကို ပြင်ဆင်လိုပါက၊ `pod.kiada.yaml` file ကို edit လုပ်ပြီး `apply` command ကို ထပ်မံ run နိုင်သည်။ `pod` ၏ field အချို့သည် ပြင်ဆင်၍မရသောကြောင့် update လုပ်ခြင်းသည် fail ဖြစ်နိုင်သော်လည်း၊ `pod` ကို delete လုပ်ပြီး ပြန်လည်ဖန်တီးနိုင်သည်။ `pod` များနှင့် အခြား object များကို မည်သို့ delete လုပ်ရမည်ကို ဤအခန်း၏အဆုံးတွင် သင်လေ့လာရပါမည်။

#### run နေသော pod တစ်ခု၏ manifest အပြည့်အစုံကို ရယူခြင်း

`pod` object သည် ယခု cluster configuration ၏ အစိတ်အပိုင်းတစ်ခုဖြစ်သည်။ အောက်ပါ command ဖြင့် API မှ object manifest အပြည့်အစုံကို ပြန်လည်ဖတ်ရှုနိုင်သည်-

```bash
$ kubectl get po kiada -o yaml
```

ဤ command ကို run ပါက၊ manifest သည် `pod.kiada.yaml` file ရှိ manifest ထက် အတော်လေးကြီးထွားလာသည်ကို သင်သတိပြုမိပါလိမ့်မည်။ `metadata` section သည် ယခုအခါ ပိုကြီးလာပြီး object တွင် `status` section တစ်ခုပါလာသည်ကို သင်တွေ့ရပါမည်။ `spec` section သည်လည်း field အများအပြားဖြင့် ကြီးထွားလာသည်။ ဤ field အသစ်များအကြောင်း ပိုမိုလေ့လာရန် `kubectl explain` ကို အသုံးပြုနိုင်သော်လည်း၊ အများစုမှာ Kubernetes ၏ internal တွင် အသုံးပြုသောကြောင့် သင်စိတ်ပူရန်မလိုအပ်ပါ။

#### pod ၏ status ကို စစ်ဆေးခြင်း

`Pod` object ၏ manifest အပြည့်အစုံတွင် `pod` ၏ status ပါဝင်သော်လည်း၊ သင်သည် ယခင်အခန်းတွင် လေ့လာခဲ့သည့်အတိုင်း `kubectl get pod -o wide` သို့မဟုတ် `kubectl describe` command ကို အသုံးပြု၍ status ကို ပိုမိုလွယ်ကူစွာ စစ်ဆေးနိုင်သည်။

#### pod အသေးစိတ်ကို ကြည့်ရန် kubectl describe ကို အသုံးပြုခြင်း

`pod` ၏ အသေးစိတ်အချက်အလက်များကို ပိုမိုပြည့်စုံစွာ ကြည့်ရှုရန် `kubectl describe` command ကို အသုံးပြုပါ-

```bash
$ kubectl describe pod kiada
Name:           kiada
Namespace:      default
Priority:       0
Node:           worker2/172.18.0.4
Start Time:     Mon, 27 Jan 2020 12:53:28 +0100
...
```

listing တွင် output အပြည့်အစုံကို မပြသော်လည်း၊ သင်ကိုယ်တိုင် command ကို run ပါက၊ `kubectl get -o yaml` command ကို အသုံးပြု၍ object manifest အပြည့်အစုံကို print ထုတ်သည့်အခါ တွေ့ရမည့် အချက်အလက်အားလုံးနီးပါးကို သင်တွေ့ရပါလိမ့်မည်။

#### မျက်နှာပြင်နောက်ကွယ်တွင် ဘာတွေဖြစ်ပျက်နေသည်ကို ကြည့်ရန် event များကို စစ်ဆေးခြင်း

ယခင်အခန်းတွင် `Node` object တစ်ခုကို စစ်ဆေးရန် `describe node` command ကို အသုံးပြုခဲ့သကဲ့သို့၊ `describe pod` command သည် output ၏အောက်ခြေတွင် `pod` နှင့် သက်ဆိုင်သော event အများအပြားကို ပြသသင့်သည်။

သင်မှတ်မိပါက၊ ဤ event များသည် object ၏ အစိတ်အပိုင်းမဟုတ်ဘဲ သီးခြား object များဖြစ်သည်။ `pod` ကို ဖန်တီးသည့်အခါ ဘာတွေဖြစ်ပျက်သည်ကို ပိုမိုလေ့လာရန် ၎င်းတို့ကို print ထုတ်ကြည့်ကြပါစို့။

```bash
$ kubectl get events
LAST SEEN   TYPE     REASON      OBJECT      MESSAGE
2m17s       Normal   Scheduled   pod/kiada   Successfully assigned default/kiada to worker2
2m16s       Normal   Pulling     pod/kiada   Pulling image "luksa/kiada:0.1"
2m10s       Normal   Pulled      pod/kiada   Successfully pulled image "luksa/kiada:0.1"
2m10s       Normal   Created     pod/kiada   Created container kiada
2m10s       Normal   Started     pod/kiada   Started container kiada
```

ဤ event များကို အချိန်순 (chronological order) အတိုင်း print ထုတ်ထားသည်။ နောက်ဆုံး event သည် အောက်ခြေတွင် ရှိသည်။ `pod` ကို ပထမဦးစွာ worker node များထဲမှ တစ်ခုသို့ assign လုပ်ခဲ့ပြီး၊ ထို့နောက် `container` image ကို pull လုပ်ခဲ့သည်၊ ထို့နောက် `container` ကို ဖန်တီးပြီး နောက်ဆုံးတွင် start လုပ်ခဲ့သည်ကို သင်တွေ့ရသည်။

`Warning` event များ မပြသသောကြောင့် အရာအားလုံး အဆင်ပြေနေပုံရသည်။ သင်၏ cluster တွင် ထိုသို့မဟုတ်ပါက၊ `pod` fail ဖြစ်ခြင်းကို troubleshoot လုပ်နည်းကို လေ့လာရန် အပိုင်း 5.4 ကို ဖတ်ရှုသင့်သည်။

## 5.3 Pod ရှိ application သို့ request များ ပေးပို့ခြင်း

အခန်း ၂ တွင်၊ သင်၏ `pod`(များ) တွင် run နေသော application နှင့် စကားပြောနိုင်ရန် load balancer တစ်ခုကို provision လုပ်ပေးသော service တစ်ခုဖန်တီးရန် `kubectl expose` command ကို သင်အသုံးပြုခဲ့သည်။ ယခု သင်သည် မတူညီသောချဉ်းကပ်မှုကို အသုံးပြုပါမည်။ Development နှင့် troubleshooting အတွက်၊ သင်သည် service တစ်ခုလုံးကို ကျော်ဖြတ်ပြီး `pod` နှင့် တိုက်ရိုက်စကားပြောလိုပေမည်။

### 5.3.1 pod ၏ IP address နှင့် port ကို ရယူခြင်း

`pod` တစ်ခုစီသည် ၎င်း၏ကိုယ်ပိုင် IP address ကို ရရှိသည်။ ဤ IP address ကို cluster အတွင်းမှသာ ရောက်ရှိနိုင်သည်။ သင်၏ `pod` ၏ IP ကို ရှာဖွေရန်၊ `kubectl get` command ကို `wide` output option ဖြင့် အသုံးပြုပါ-

```bash
$ kubectl get pod kiada -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE      ...
kiada   1/1     Running   0          35m   10.244.2.4   worker2   ...
```

`IP` column တွင် ပြထားသည့်အတိုင်း၊ ကျွန်ုပ်၏ `pod` ၏ IP မှာ `10.244.2.4` ဖြစ်သည်။ ယခု application က listen လုပ်နေသော port number ကို ကျွန်ုပ်ရှာဖွေရန် လိုအပ်သည်။

#### application က အသုံးပြုသော port number ကို ရယူခြင်း

ကျွန်တော်သာ application ၏ author မဟုတ်ပါက၊ application က မည်သည့် port ကို listen လုပ်နေသည်ကို သိရှိရန် ခက်ခဲပါလိမ့်မည်။ ကျွန်တော်သည် ၎င်း၏ source code သို့မဟုတ် `container` image ၏ `Dockerfile` ကို စစ်ဆေးနိုင်သည်၊ အကြောင်းမှာ port ကို များသောအားဖြင့် ထိုနေရာတွင် သတ်မှတ်ထားသောကြောင့်ဖြစ်သည်။ သို့သော် ကျွန်တော်သည် ထိုနှစ်ခုစလုံးကို access မလုပ်နိုင်ပါ။ အခြားသူတစ်ဦးက `pod` ကို ဖန်တီးခဲ့ပါက၊ ၎င်း listen လုပ်နေသော port ကို ကျွန်တော်ဘယ်လိုသိနိုင်မလဲ။

ကံကောင်းထောက်မစွာ၊ သင်သည် `pod` definition တွင် port စာရင်းတစ်ခုကို သတ်မှတ်နိုင်သည်။ port များကို သတ်မှတ်ရန် မလိုအပ်သော်လည်း၊ ၎င်းကိုပြုလုပ်ခြင်းသည် `pod` နှင့်အလုပ်လုပ်သော မည်သူမဆိုအတွက် အလွန်အသုံးဝင်သည်။ သင်၏ `pod` manifest တွင် `containerPort` field ကို သတ်မှတ်ပါက၊ `kubectl describe` command သည် ၎င်းကိုပြသမည်ဖြစ်ပြီး၊ ထိုအချက်အလက်ကို `pod` အကြောင်းလေ့လာလိုသူ မည်သူမဆို အလွယ်တကူ မြင်တွေ့နိုင်သည်။

#### worker node များမှ pod သို့ ချိတ်ဆက်ခြင်း

Kubernetes network model အရ `pod` တစ်ခုချင်းစီကို အခြား `pod` တိုင်းမှ access လုပ်နိုင်ပြီး node တိုင်းသည် cluster ရှိ မည်သည့် node ပေါ်ရှိ မည်သည့် `pod` ကိုမဆို reach လုပ်နိုင်သည်ဟု သတ်မှတ်ထားသည်။ ဤအချက်ကြောင့်၊ သင်၏ `pod` နှင့် ဆက်သွယ်ရန် နည်းလမ်းတစ်ခုမှာ သင်၏ worker node များထဲမှ တစ်ခုသို့ log in ဝင်ပြီး ထိုမှ `pod` နှင့် စကားပြောခြင်းဖြစ်သည်။ node တစ်ခုသို့ log in ဝင်ပုံသည် သင်၏ cluster ကို deploy လုပ်ရန် အသုံးပြုခဲ့သောအရာပေါ် မူတည်သည်ကို သင်သိပြီးဖြစ်သည်။ `kind` ကို အသုံးပြုပါက `docker exec -it kind-worker bash` ကို run ပါ၊ သို့မဟုတ် `Minikube` ကို အသုံးပြုပါက `minikube ssh` ကို run ပါ။ GKE တွင် `gcloud compute ssh` command ကို အသုံးပြုပါ။ အခြား cluster များအတွက် ၎င်းတို့၏ documentation ကို ကိုးကားပါ။

node သို့ log in ဝင်ပြီးသည်နှင့်၊ `pod` ၏ IP နှင့် port ဖြင့် `curl` command ကို အသုံးပြု၍ သင်၏ application ကို access လုပ်ပါ။ ကျွန်ုပ်၏ `pod` ၏ IP မှာ `10.244.2.4` ဖြစ်ပြီး port မှာ `8080` ဖြစ်သောကြောင့် အောက်ပါ command ကို run ပါမည်-

```bash
$ curl 10.244.2.4:8080
Kiada version 0.1. Request processed by "kiada". Client IP: ::ffff:10.244.2.1
```

ပုံမှန်အားဖြင့် သင်သည် သင်၏ `pod` များနှင့် စကားပြောရန် ဤနည်းလမ်းကို အသုံးမပြုသော်လည်း၊ သင်၏ `pod` နှင့် အခြား component များအကြား ဆက်သွယ်မှုပြဿနာများရှိပါက၊ `pod` ကို node မှ access လုပ်နိုင်မလား စစ်ဆေးရန် ဤနည်းလမ်းကို အသုံးပြုရန် လိုအပ်ပေမည်။

#### အခြား pod တစ်ခုမှ pod သို့ ချိတ်ဆက်ခြင်း

`pod` တစ်ခုနှင့် စကားပြောရန် node တစ်ခုသို့ log in ဝင်ခြင်းသည် ပုံမှန်လုပ်ဆောင်သည့် နည်းလမ်းမဟုတ်ပါ။ များသောအားဖြင့်၊ သင်သည် pod-to-pod ဆက်သွယ်မှုကို debug လုပ်ရန် လိုအပ်သည့်အခါ၊ သင်သည် client `pod` တစ်ခုကို run ပြီး ၎င်းအတွင်းတွင် shell ကို run ခြင်းဖြင့် ပြုလုပ်သည်။ ဥပမာအားဖြင့်၊ `kiada` `pod` ကို `curlimages/curl` image ကို အသုံးပြု၍ `pod` တစ်ခုမှ ဆက်သွယ်ရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl run -it --restart=Never --rm client-pod -- curl 10.244.2.4:8080
Kiada version 0.1. Request processed by "kiada". Client IP: ::ffff:10.244.2.5
pod "client-pod" deleted
```

ဤ command သည် `curlimages/curl` image မှ ဖန်တီးထားသော `container` တစ်ခုပါဝင်သည့် `pod` တစ်ခုကို run သည်။ သင်သည် `curl` binary executable ကို ပံ့ပိုးပေးသည့် အခြားမည်သည့် image ကိုမဆို အသုံးပြုနိုင်သည်။ `-it` option သည် သင်၏ console ကို `container` ၏ standard input နှင့် output သို့ ချိတ်ဆက်ပေးသည်၊ `--restart=Never` option သည် `curl` command နှင့် ၎င်း၏ `container` ရပ်တန့်သွားသောအခါ `pod` ကို `Completed` အဖြစ် သတ်မှတ်ပေးပြီး၊ `--rm` option သည် အဆုံးတွင် `pod` ကို ဖယ်ရှားပေးသည်။ `pod` ၏အမည်မှာ `client-pod` ဖြစ်ပြီး ၎င်း၏ `container` တွင် execute လုပ်သော command မှာ `curl 10.244.2.4:8080` ဖြစ်သည်။

> **မှတ်ချက်**
> သင်သည် client `pod` တွင် `bash` shell ကို run ရန် command ကို ပြင်ဆင်ပြီး shell မှ `curl` ကို run နိုင်သည်။

`pod` တစ်ခုက အခြား `pod` တစ်ခုကို access လုပ်နိုင်မလား စစ်ဆေးရန် `pod` တစ်ခုဖန်တီးခြင်းသည် pod-to-pod ဆက်သွယ်မှုကို အထူးစမ်းသပ်သည့်အခါ အသုံးဝင်သည်။ သင်၏ `pod` က request များကို တုံ့ပြန်မလား သိလိုရုံသာဖြစ်ပါက၊ နောက်ကဏ္ဍတွင် ရှင်းပြထားသော နည်းလမ်းကိုလည်း အသုံးပြုနိုင်သည်။

#### kubectl port forward မှတစ်ဆင့် pod များနှင့် ချိတ်ဆက်ခြင်း

Kubernetes က `pod` တစ်ခုနှင့် တိုက်ရိုက်ဆက်သွယ်ရန် ပိုမိုလွယ်ကူသောနည်းလမ်းကို ပေးထားသည်။ `kubectl port-forward` command ကို အသုံးပြု၍ သင်သည် သင်၏ local computer ပေါ်ရှိ network port တစ်ခုကို `pod` ၏ port တစ်ခုသို့ forward လုပ်နိုင်သည်။

*ပုံ ၅.၈။ kubectl port-forward ဖြင့် pod တစ်ခုသို့ ချိတ်ဆက်ခြင်း*

`pod` နှင့် ဆက်သွယ်ရေးလမ်းကြောင်းတစ်ခုဖွင့်ရန်၊ သင် `pod` ၏ IP ကိုပင် ရှာရန်မလိုအပ်ပါ၊ အကြောင်းမှာ သင်သည် ၎င်း၏အမည်နှင့် port ကိုသာ သတ်မှတ်ရန်လိုအပ်သောကြောင့်ဖြစ်သည်။ အောက်ပါ command သည် သင်၏ကွန်ပျူတာ၏ local port `8080` ကို `kiada` `pod` ၏ port `8080` သို့ forward လုပ်ပေးသော proxy တစ်ခုကို စတင်သည်-

```bash
$ kubectl port-forward kiada 8080
...
Forwarding from 127.0.0.1:8080 -> 8080
...
Forwarding from [::1]:8080 -> 8080
```

proxy သည် ယခုအခါ incoming connection များကို စောင့်နေသည်။ အခြား terminal တစ်ခုတွင် အောက်ပါ `curl` command ကို run ပါ-

```bash
$ curl localhost:8080
Kiada version 0.1. Request processed by "kiada". Client IP: ::ffff:127.0.0.1
```

သင်တွေ့မြင်ရသည့်အတိုင်း၊ `curl` သည် local proxy သို့ ချိတ်ဆက်ခဲ့ပြီး `pod` မှ response ကို လက်ခံရရှိခဲ့သည်။ `port-forward` command သည် development နှင့် troubleshooting အတွင်း `pod` တစ်ခုနှင့် ဆက်သွယ်ရန် အလွယ်ကူဆုံးနည်းလမ်းဖြစ်သော်လည်း၊ ၎င်းသည် မျက်နှာပြင်နောက်ကွယ်တွင် ဖြစ်ပျက်နေသောအရာများနှင့် ပတ်သက်၍ အရှုပ်ထွေးဆုံးနည်းလမ်းလည်းဖြစ်သည်။ ဆက်သွယ်မှုသည် component များစွာကို ဖြတ်သန်းသွားသောကြောင့်၊ ဆက်သွယ်ရေးလမ်းကြောင်းတွင် တစ်ခုခုပျက်စီးနေပါက၊ `pod` ကိုယ်တိုင်နှင့် ၎င်း၏ network အခြေအနေ ကောင်းမွန်နေသော်လည်း `pod` နှင့် စကားပြောနိုင်မည်မဟုတ်ပါ။

*ပုံ ၅.၉။ port forwarding အသုံးပြုသည့်အခါ curl နှင့် container အကြား ရှည်လျားသော ဆက်သွယ်ရေးလမ်းကြောင်း*

ပုံတွင်ပြထားသည့်အတိုင်း၊ `curl` process သည် proxy သို့ ချိတ်ဆက်သည်၊ ၎င်း proxy က API server သို့ ချိတ်ဆက်သည်၊ ၎င်း API server က `pod` ကို host လုပ်နေသော node ပေါ်ရှိ Kubelet သို့ ချိတ်ဆက်သည်၊ ထို့နောက် Kubelet က `pod` ၏ loopback device (တစ်နည်းအားဖြင့် `localhost` address) မှတစ်ဆင့် `container` သို့ ချိတ်ဆက်သည်။ ဆက်သွယ်ရေးလမ်းကြောင်းသည် အလွန်ရှည်လျားသည်ကို သင်သဘောတူမည်မှာ သေချာပါသည်။

> **မှတ်ချက်**
> Kubelet က ၎င်းကို reach လုပ်နိုင်ရန် `container` အတွင်းရှိ application သည် loopback device ပေါ်ရှိ port တစ်ခုတွင် bind လုပ်ထားရမည်။ ၎င်းသည် `pod` ၏ `eth0` network interface တွင်သာ listen လုပ်နေပါက၊ သင်သည် `kubectl port-forward` command ဖြင့် ၎င်းကို reach လုပ်နိုင်မည်မဟုတ်ပါ။

### 5.3.2 Application log များကို ကြည့်ရှုခြင်း

သင်၏ Node.js application သည် ၎င်း၏ log ကို standard output stream သို့ ရေးသားသည်။ log ကို file တစ်ခုသို့ ရေးသားမည့်အစား၊ containerized application များသည် များသောအားဖြင့် standard output (`stdout`) နှင့် standard error stream (`stderr`) သို့ log ရေးသားကြသည်။ ၎င်းသည် `container` runtime အား output ကို ကြားဖြတ်ဖမ်းယူပြီး၊ ၎င်းကို တသမတ်တည်းရှိသော နေရာတစ်ခု (များသောအားဖြင့် `/var/log/containers`) တွင် သိမ်းဆည်းရန်နှင့် application တစ်ခုချင်းစီက ၎င်း၏ log file များကို မည်သည့်နေရာတွင် သိမ်းဆည်းသည်ကို သိရန်မလိုဘဲ log ကို access လုပ်နိုင်စေရန် ခွင့်ပြုသည်။

Docker ကို အသုံးပြု၍ `container` တစ်ခုတွင် application တစ်ခုကို run သောအခါ၊ သင်သည် `docker logs <container-id>` ဖြင့် ၎င်း၏ log ကို ပြသနိုင်သည်။ သင်၏ application ကို Kubernetes တွင် run သောအခါ၊ `pod` ကို host လုပ်နေသော node သို့ log in ဝင်ပြီး `docker logs` ကို အသုံးပြု၍ ၎င်း၏ log ကို ပြသနိုင်သော်လည်း၊ Kubernetes သည် `kubectl logs` command ဖြင့် ၎င်းကိုပြုလုပ်ရန် ပိုမိုလွယ်ကူသောနည်းလမ်းကို ပေးထားသည်။

#### `kubectl logs` ဖြင့် pod ၏ log ကို ရယူခြင်း

သင်၏ `pod` (ပိုမိုတိကျစွာပြောရလျှင်၊ `container` ၏ log) ၏ log ကို ကြည့်ရှုရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl logs kiada
Kiada - Kubernetes in Action Demo Application
---------------------------------------------
Kiada 0.1 starting...
Local hostname is kiada
Listening on port 8080
Received request for / from ...
```

#### Log များကို real time တွင် stream လုပ်ခြင်း

သင်၏ application ၏ log ကို real time တွင် ကြည့်လိုပါက၊ `-f` သို့မဟုတ် `--follow` option ကို အသုံးပြုပါ-

```bash
$ kubectl logs kiada -f
```

ယခု အခြား terminal တစ်ခုတွင် `pod` သို့ request များ ပေးပို့သည့်အခါ၊ application log များသည် ချက်ချင်းပေါ်လာမည်ဖြစ်သည်။

#### Log line များတွင် timestamp များ ထပ်ထည့်ခြင်း

log statement တွင် timestamp ကို ထည့်သွင်းရန် ကျွန်ုပ်တို့မေ့သွားသည်ကို သင်သတိပြုမိပေမည်။ timestamp မပါသော log များသည် အသုံးဝင်မှု အကန့်အသတ်ရှိသည်။ ကံကောင်းထောက်မစွာ၊ `container` runtime သည် application မှ ထုတ်လုပ်သော line တိုင်းတွင် လက်ရှိ timestamp ကို ချိတ်ဆက်ပေးသည်။ သင်သည် ဤ timestamp များကို `--timestamps=true` option ကို အသုံးပြု၍ ပြသနိုင်သည်-

```bash
$ kubectl logs kiada --timestamps=true
2020-02-01T09:44:40.954641934Z Kiada - Kubernetes in Action Demo Application
2020-02-01T09:44:40.954843234Z ---------------------------------------------
2020-02-01T09:44:40.955032432Z Kiada 0.1 starting...
...
```

> **အကြံပြုချက်**
> သင်သည် value မပါဘဲ `--timestamps` ဟုသာ ရိုက်ထည့်ခြင်းဖြင့် timestamp များကို ပြသနိုင်သည်။ boolean option များအတွက်၊ option name ကို သတ်မှတ်ရုံဖြင့် option ကို `true` သို့ set လုပ်ပေးသည်။

#### သတ်မှတ်ထားသော အချိန်ကာလတစ်ခုမှ log များကို ရယူခြင်း

log ၏ အစမှစ၍ အရာအားလုံးကို သင်အမြဲတမ်း မလိုအပ်ပါ။ `--since` option ကို အသုံးပြု၍ နောက်ဆုံးနာရီအတွင်းမှ log entry များကိုသာ ပြသရန် `kubectl` ကို ပြောနိုင်သည်။ ဥပမာအားဖြင့်-

```bash
$ kubectl logs kiada --since=1h
```

RFC3339 format timestamp ကို အသုံးပြု၍ `--since-time` option ဖြင့် တိကျသောအချိန်တစ်ခုမှစ၍ log များကိုလည်း သင်ရယူနိုင်သည်-

```bash
$ kubectl logs kiada --since-time=2020-02-01T09:50:00Z
```

#### Log ၏ နောက်ဆုံး line အနည်းငယ်ကို ပြသခြင်း

output ကို ကန့်သတ်ရန် အချိန်ကို အသုံးပြုမည့်အစား၊ သင်သည် log ၏ အဆုံးမှ line မည်မျှကို ပြသလိုသည်ကိုလည်း သတ်မှတ်နိုင်သည်။ နောက်ဆုံး line ဆယ်ခုကို ပြသရန်၊ စမ်းကြည့်ပါ-

```bash
$ kubectl logs kiada --tail=10
```

> **မှတ်ချက်**
> value တစ်ခုလိုအပ်သော `Kubectl` option များကို equal sign သို့မဟုတ် space ဖြင့် သတ်မှတ်နိုင်သည်။ `--tail=10` အစား `--tail 10` ဟုလည်း ရိုက်ထည့်နိုင်သည်။

#### Pod ၏ log များရရှိနိုင်မှုကို နားလည်ခြင်း

Kubernetes သည် `container` တစ်ခုစီအတွက် သီးခြား log file တစ်ခုကို သိမ်းဆည်းသည်။ ၎င်းတို့ကို များသောအားဖြင့် `container` run နေသော node ပေါ်ရှိ `/var/log/containers` တွင် သိမ်းဆည်းသည်။ `container` တစ်ခုစီအတွက် သီးခြား file တစ်ခုကို ဖန်တီးသည်။ `container` restart ဖြစ်ပါက၊ ၎င်း၏ log များကို file အသစ်တစ်ခုသို့ ရေးသားသည်။ ဤအချက်ကြောင့်၊ သင် `kubectl logs -f` ဖြင့် ၎င်း၏ log ကို follow လုပ်နေစဉ် `container` restart ဖြစ်ပါက၊ command သည် ရပ်တန့်သွားမည်ဖြစ်ပြီး၊ `container` အသစ်၏ log များကို stream လုပ်ရန် ၎င်းကို ထပ်မံ run ရန် လိုအပ်မည်ဖြစ်သည်။

`kubectl logs` command သည် လက်ရှိ `container` ၏ log များကိုသာ ပြသသည်။ ယခင် `container` ၏ log ကို ကြည့်ရန်၊ `-p` သို့မဟုတ် `--previous` flag ကို အသုံးပြုပါ-

```bash
$ kubectl logs kiada -p
```

log များကို rotate လုပ်ပြီး နောက်ဆုံးတွင် ဖယ်ရှားသည်ကို သတိရပါ။ ၎င်းတို့သည် အမြဲတမ်း ရရှိနိုင်မည်မဟုတ်ပါ။ သင်၏ log များကို အကန့်အသတ်မရှိ သိမ်းဆည်းရန်၊ ၎င်းတို့ကို ဗဟိုချုပ်ကိုင်ထားသော logging system သို့ ပေးပို့ရမည်။ အခန်း ၂၃ တွင် မည်သို့ပြုလုပ်ရမည်ကို ရှင်းပြထားသည်။

#### Log များကို file များသို့ ရေးသားသော application များကော ဘယ်လိုလဲ။

သင်၏ application သည် ၎င်း၏ log များကို `stdout` အစား file တစ်ခုသို့ ရေးသားပါက၊ ထို file ကို မည်သို့ access လုပ်ရမည်ကို သင်တွေးတောမိပေမည်။ အကောင်းဆုံးမှာ၊ သင်သည် log များကို ဗဟိုနေရာတစ်ခုတွင် ကြည့်ရှုနိုင်ရန် ဗဟိုချုပ်ကိုင်ထားသော logging system ကို configure လုပ်သင့်သော်လည်း၊ တစ်ခါတစ်ရံတွင် သင်သည် အရာရာကို ရိုးရှင်းစွာထားလိုပြီး log များကို manual access လုပ်ရန် စိတ်မပူပါ။ နောက်လာမည့်ကဏ္ဍနှစ်ခုတွင်၊ `container` မှ log နှင့် အခြား file များကို သင်၏ကွန်ပျူတာသို့ copy ကူးနည်းနှင့် ဆန့်ကျင်ဘက်اتجاهသို့ copy ကူးနည်း၊ နှင့် run နေသော `container` များတွင် command များ execute လုပ်နည်းတို့ကို သင်လေ့လာရပါမည်။ သင်သည် `container` အတွင်းရှိ log file များ သို့မဟုတ် အခြားမည်သည့် file ကိုမဆို ပြသရန် ဤနည်းလမ်းနှစ်ခုစလုံးကို အသုံးပြုနိုင်သည်။