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

### 5.3.3 Container များသို့ File များ Copy ကူးခြင်းနှင့် Container များမှ File များ Copy ကူးခြင်း

တစ်ခါတစ်ရံတွင် သင်သည် run နေသော `container` တစ်ခုသို့ file တစ်ခုထပ်ထည့်လိုခြင်း သို့မဟုတ် ၎င်းမှ file တစ်ခုကို ရယူလိုပေမည်။ run နေသော `container` များရှိ file များကို ပြင်ဆင်ခြင်းသည် ပုံမှန်အားဖြင့် သင်လုပ်ဆောင်လေ့ရှိသောအရာမဟုတ်ပါ - အနည်းဆုံး production တွင်မဟုတ်ပါ - သို့သော် development ပြုလုပ်နေစဉ်အတွင်း အသုံးဝင်နိုင်သည်။ `Kubectl` သည် သင်၏ local computer နှင့် `container` တစ်ခုအကြား file များ သို့မဟုတ် directory များကို copy ကူးရန် `cp` command ကို ပေးထားသည်။

ဥပမာအားဖြင့်၊ `kiada` `container` မှ `app.js` file ကို သင်၏ local directory သို့ copy ကူးရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl cp kiada:/app.js ./kiada-app.js
```

ဆန့်ကျင်ဘက်اتجاهသို့ file များကို copy ကူးရန်၊ သင်၏ local file ကို ပထမ argument အဖြစ် သတ်မှတ်ပြီး destination ကို ဒုတိယ argument အဖြစ် သတ်မှတ်ပါ-

```bash
$ kubectl cp ./my-file.txt kiada:/my-file.txt
```

> **မှတ်ချက်**
> `kubectl cp` command သည် သင်၏ `container` တွင် `tar` binary ရှိရန် လိုအပ်သော်လည်း၊ ဤလိုအပ်ချက်သည် အနာဂတ်တွင် ပြောင်းလဲသွားနိုင်သည်။

### 5.3.4 Run နေသော Container များတွင် Command များ Execute လုပ်ခြင်း

`Container` တစ်ခုတွင် run နေသော application တစ်ခုကို debug လုပ်သည့်အခါ၊ `container` နှင့် ၎င်း၏ environment ကို အတွင်းမှ စစ်ဆေးရန် လိုအပ်ပေမည်။ `Kubectl` သည် ဤလုပ်ဆောင်ချက်ကိုလည်း ပံ့ပိုးပေးသည်။ သင်သည် `container` ၏ file system တွင်ရှိသော မည်သည့် binary file ကိုမဆို `kubectl exec` command ကို အသုံးပြု၍ execute လုပ်နိုင်သည်။

#### Container တွင် command တစ်ခုတည်းကို Invoke လုပ်ခြင်း

ဥပမာအားဖြင့်၊ `kiada` `pod` ရှိ `container` တွင် run နေသော process များကို အောက်ပါ command ကို run ခြင်းဖြင့် စာရင်းပြုစုနိုင်သည်-

```bash
$ kubectl exec kiada -- ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  1.3 812860 27356 ?        Ssl  11:54   0:00 node app.js # A
root       120  0.0  0.1  17500  2128 ?        Rs   12:22   0:00 ps aux      # B
```

*   **A** `Container` ၏ main process (`node`)
*   **B** သင် execute လုပ်ခဲ့သော `ps` command

ဤသည်မှာ အခန်း ၂ တွင် run နေသော `container` တစ်ခုရှိ process များကို လေ့လာရန် သင်အသုံးပြုခဲ့သော Docker command ၏ Kubernetes နှင့်ညီမျှသော command ဖြစ်သည်။ ၎င်းသည် သင့်အား မည်သည့် `pod` ရှိ မည်သည့် `container` တွင်မဆို command တစ်ခုကို remote မှ run ရန် ခွင့်ပြုသည်။

Command ၏ argument များကို `kubectl exec` command ၏ argument များနှင့် ခွဲခြားရန် double dash (`--`) ကို အသုံးပြုသည်ကို သတိပြုပါ။ သင် double dash ကို မေ့သွားပါက၊ command ၏ argument များကို `kubectl` ၏ option များအဖြစ် အဓိပ္ပာယ်ဖွင့်ဆိုနိုင်ပြီး၊ ၎င်းသည် မမျှော်လင့်ထားသော အပြုအမူကို ဖြစ်ပေါ်စေနိုင်သည်။ ဥပမာအားဖြင့်၊ `kiada` `pod` တွင် `curl -s localhost:8080` command ကို run ရန် ကြိုးစားကြည့်ပါ-

```bash
$ kubectl exec kiada curl -s localhost:8080
The connection to the server localhost:8080 was refused – did you specify the right host or port?
```

Node.js server က connection ကို ငြင်းပယ်နေသကဲ့သို့ ထင်ရသော်လည်း၊ ပြဿနာမှာ အခြားတစ်နေရာတွင် ရှိနေသည်။ `curl` command ကို ဘယ်တော့မှ execute မလုပ်ပါ။ error ကို `kubectl` ကိုယ်တိုင်က Kubernetes API server ကို `localhost:8080` တွင် စကားပြောရန် ကြိုးစားသည့်အခါ report တင်ခြင်းဖြစ်သည်။ ထိုနေရာတွင် server မရှိပါ။ `kubectl options` command ကို run ပါက၊ `-s` option ကို Kubernetes API server ၏ address နှင့် port ကို သတ်မှတ်ရန် အသုံးပြုနိုင်သည်ကို သင်တွေ့ရပါလိမ့်မည်။ ထို option ကို `curl` သို့ ပေးပို့မည့်အစား `kubectl` က ၎င်း၏ကိုယ်ပိုင် option အဖြစ် လက်ခံလိုက်သည်။ double dash ကို ထည့်ခြင်းဖြင့် ဤသို့မဖြစ်အောင် တားဆီးနိုင်သည်။ ကံကောင်းထောက်မစွာ၊ ဤကဲ့သို့သော အခြေအနေများကို တားဆီးရန်၊ `kubectl` ၏ version အသစ်များသည် သင် double dash ကို မေ့သွားပါက error ပြန်ပေးရန် သတ်မှတ်ထားသည်။

#### Container တွင် interactive shell တစ်ခု Run ခြင်း

ယခင် ဥပမာနှစ်ခုသည် `container` တွင် command တစ်ခုတည်းကို မည်သို့ execute လုပ်နိုင်သည်ကို ပြသခဲ့သည်။ သင်သည် `container` ၏ environment ကို စူးစမ်းလေ့လာရန် command များစွာကို run ရန် လိုအပ်ပါက၊ သင်သည် `container` အတွင်းတွင် shell တစ်ခုကို run နိုင်သည်။

`kiada-ssl` `pod` ၏ `kiada` `container` တွင် shell တစ်ခု run ရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl exec -it kiada-ssl -c kiada -- bash
root@kiada-ssl:/#
```

`-i` နှင့် `-t` option များကို အတူတကွ အသုံးပြု၍ `container` ၏ standard input ကို သင်၏ terminal သို့ ချိတ်ဆက်ပြီး ၎င်းကို interactive terminal တစ်ခုအဖြစ် သတ်မှတ်သည်။ ၎င်းသည် သင့်အား shell နှင့် အပြန်အလှန်ဆက်သွယ်နိုင်စေရန် ခွင့်ပြုသည်။

`Container` image အများစုတွင် shell နှင့် အခြားအခြေခံ Unix command များပါဝင်သော်လည်း၊ image တိုင်းတွင် ထိုသို့မဟုတ်ပါ။ image များကို သေးငယ်အောင်ထားရန်နှင့် `container` တွင် security ကို တိုးမြှင့်ရန်အတွက်၊ production တွင် အသုံးပြုသော `container` အများစုသည် `container` ၏ primary process အတွက် လိုအပ်သော binary file များမှလွဲ၍ အခြားမည်သည့် binary file များကိုမှ မပါဝင်ပါ။ ၎င်းသည် attack surface ကို သိသိသာသာ လျှော့ချပေးသော်လည်း၊ production `container` များတွင် shell များ သို့မဟုတ် အခြား tool များကို run ၍မရဟု ဆိုလိုသည်။ ကံကောင်းထောက်မစွာ၊ ephemeral containers ဟုခေါ်သော Kubernetes feature အသစ်သည် run နေသော `container` များကို ၎င်းတို့အတွင်းတွင် debug `container` တစ်ခုချိတ်ဆက်ခြင်းဖြင့် debug လုပ်ရန် ခွင့်ပြုသည်။

### 5.3.5 Run နေသော Container တစ်ခုသို့ Attach လုပ်ခြင်း

`kubectl attach` command သည် run နေသော `container` တစ်ခုနှင့် အပြန်အလှန်ဆက်သွယ်ရန် နောက်တစ်နည်းဖြစ်သည်။ ၎င်းသည် `container` ရှိ main process ၏ standard input, output နှင့် error stream များနှင့် ချိတ်ဆက်သည်။

၎င်းသည် `kubectl exec` နှင့် မည်သို့ကွာခြားသည်ကို သင်တွေးတောမိပေမည်။ `exec` command ကို အသုံးပြုသည့်အခါ၊ သင်သည် `container` တွင် process အသစ်တစ်ခုကို run သည်။ `attach` command ဖြင့်၊ သင်သည် `container` ၏ main process နှင့် ချိတ်ဆက်သည်။

`kiada-ssl` `pod` ၏ `kiada` `container` သို့ attach လုပ်ရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl attach kiada-ssl -c kiada
```

ယခု၊ အခြား terminal တစ်ခုတွင် `curl` ကို အသုံးပြု၍ application သို့ HTTP request အသစ်များ ပေးပို့သည့်အခါ၊ application က standard output သို့ log ရေးသော line များကို `kubectl attach` command execute လုပ်ထားသော terminal တွင်လည်း ပေါ်လာသည်ကို သင်တွေ့ရပါလိမ့်မည်။

#### Application ၏ Standard Input သို့ ရေးသားရန် kubectl attach ကို အသုံးပြုခြင်း

Kiada application version `0.1` သည် standard input stream မှ မဖတ်သော်လည်း၊ ၎င်းကိုပြုလုပ်သော version `0.2` ၏ source code ကို စာအုပ်၏ code archive တွင် သင်ရှာတွေ့နိုင်ပါသည်။ ဤ version သည် application ၏ standard input stream သို့ ရေးသားခြင်းဖြင့် status message တစ်ခုကို set လုပ်ရန် ခွင့်ပြုသည်။ ဤ status message ကို application ၏ response တွင် ထည့်သွင်းပါလိမ့်မည်။ ဤ application version ကို `pod` အသစ်တစ်ခုတွင် deploy လုပ်ပြီး status message ကို set လုပ်ရန် `kubectl attach` command ကို အသုံးပြုကြပါစို့။

`kiada-0.2/` directory တွင် image ကို build ရန် လိုအပ်သော artifact များကို သင်ရှာတွေ့နိုင်သည်။ သင်သည် ကြိုတင် build လုပ်ထားသော image `docker.io/luksa/kiada:0.2` ကိုလည်း အသုံးပြုနိုင်သည်။ `pod` manifest သည် `Chapter05/pod.kiada-stdin.yaml` file တွင် ရှိပြီး အောက်ပါ listing တွင် ပြသထားသည်။ ၎င်းတွင် `stdin` field ကို `true` ဟု set ထားသောကြောင့် standard input stream ကို ဖွင့်ထားရန် လိုအပ်ကြောင်း Kubernetes အား ပြောပြသည်။

**Listing 5.2။ stdin ဖွင့်ထားသော pod manifest တစ်ခု**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kiada-stdin
spec:
  containers:
  - name: kiada
    image: luksa/kiada:0.2
    stdin: true                 # A
    ports:
    - containerPort: 8080
```

*   **A** `Container` ၏ standard input ကို ဖွင့်ထားရန် ဤ field ကို `true` ဟု set ရန် လိုအပ်သည်။

Standard input ကို ဖွင့်ထားခြင်းသည် resource များကို သုံးစွဲသည်။ ၎င်းကို `false` ဟု default ထားရှိသည်။ `stdin: true` ကို သင်မသတ်မှတ်ပါက၊ `container` ၏ standard input stream ကို သင် attach လုပ်၍မရပါ။ သင်သည် standard input stream အတွက် buffer တစ်ခုကိုလည်း allocate လုပ်ရန် လိုအပ်သည်၊ သို့မဟုတ်ပါက application သည် ၎င်းမှဖတ်ရန် ကြိုးစားသည့်အခါ အမြဲတမ်း `EOF` ကို လက်ခံရရှိလိမ့်မည်။

ဤ manifest file မှ `pod` ကို `kubectl apply` command ဖြင့် ဖန်တီးပါ-

```bash
$ kubectl apply -f pod.kiada-stdin.yaml
pod/kiada-stdin created
```

Application နှင့် ဆက်သွယ်မှုပြုလုပ်နိုင်ရန်၊ `kubectl port-forward` command ကို ထပ်မံအသုံးပြုပါ၊ သို့သော် local port `8080` ကို ယခင် execute လုပ်ခဲ့သော `port-forward` command က အသုံးပြုနေဆဲဖြစ်သောကြောင့်၊ သင်သည် ၎င်းကို terminate လုပ်ရမည် သို့မဟုတ် `pod` အသစ်သို့ forward လုပ်ရန် မတူညီသော local port တစ်ခုကို ရွေးချယ်ရမည်။ ၎င်းကို အောက်ပါအတိုင်း သင်ပြုလုပ်နိုင်သည်-

```bash
$ kubectl port-forward kiada-stdin 8888:8080
Forwarding from 127.0.0.1:8888 -> 8080
Forwarding from [::1]:8888 -> 8080
```

command-line argument `8888:8080` သည် command အား local port `8888` ကို `pod` ၏ port `8080` သို့ forward လုပ်ရန် ညွှန်ကြားသည်။

ယခု သင်သည် application ကို `http://localhost:8888` တွင် access လုပ်နိုင်သည်-

```bash
$ curl localhost:8888
Kiada version 0.2. Request processed by "kiada-stdin". Client IP: ::ffff:127.0.0.1
```

`kubectl attach` ကို အသုံးပြု၍ status message ကို set လုပ်ကြပါစို့။ standard input stream သို့ attach လုပ်ရန်၊ သင်သည် `-i` flag ကိုလည်း သတ်မှတ်ရန် လိုအပ်သည်-

```bash
$ kubectl attach -i kiada-stdin
```

ယခု သင်သည် status message ကို terminal တွင် ရိုက်ထည့်ပြီး `ENTER` key ကို နှိပ်နိုင်သည်။ ဥပမာအားဖြင့်၊ အောက်ပါ message ကို ရိုက်ထည့်ပါ-

`This is my custom status message.`

Application သည် message အသစ်ကို standard output သို့ print ထုတ်သည်-

`Status message set to: This is my custom status message.`

Application သည် ယခုအခါ message ကို ၎င်း၏ HTTP request များသို့ response များတွင် ထည့်သွင်းခြင်းရှိမရှိ ကြည့်ရှုရန်၊ `curl` command ကို ထပ်မံ execute လုပ်ပါ သို့မဟုတ် သင်၏ web browser တွင် page ကို refresh လုပ်ပါ-

```bash
$ curl localhost:8888
Kiada version 0.2. Request processed by "kiada-stdin". Client IP: ::ffff:127.0.0.1
This is my custom status message. # A
```

*   **A** Status message ကို HTTP response တွင် ထည့်သွင်းထားသည်။

`kubectl attach` command run နေသော terminal တွင် နောက်ထပ် line တစ်ခုရိုက်ထည့်ခြင်းဖြင့် status message ကို ထပ်မံပြောင်းလဲနိုင်သည်။ `attach` command မှ ထွက်ရန်၊ `Control-C` သို့မဟုတ် ညီမျှသော key ကို နှိပ်ပါ။

> **မှတ်ချက်**
> `Container` definition ရှိ နောက်ထပ် field တစ်ခုဖြစ်သော `stdinOnce` သည် attach session ပြီးဆုံးသွားသောအခါ standard input channel ကို ပိတ်သင့်သလားကို ဆုံးဖြတ်သည်။ ၎င်းကို default အားဖြင့် `false` ဟု set ထားပြီး၊ `kubectl attach` session တိုင်းတွင် standard input ကို အသုံးပြုရန် ခွင့်ပြုသည်။ သင် `true` ဟု set ထားပါက၊ ပထမ client detach ဖြစ်သွားသည်နှင့် standard input ကို ပိတ်သွားမည်ဖြစ်သည်။

## 5.4 Pod တစ်ခုတွင် Container များစွာ Run ခြင်း

အခန်း 5.1 တွင် `pod` တစ်ခုတွင် `container` တစ်ခုထက်ပို၍ run ရန် အကြောင်းရင်းများကို သင်လေ့လာခဲ့ပြီး sidecar `container` concept ကို မိတ်ဆက်ခဲ့သည်။ ယခု သင်သည် Node.js application ကိုယ်တိုင်ကို မပြောင်းလဲဘဲ HTTPS support ထပ်ထည့်ရန် Kiada `pod` သို့ Envoy proxy sidecar တစ်ခုကို ထပ်ထည့်ပါမည်။

### 5.4.1 Kiada Node.js application ကို Envoy proxy အသုံးပြု၍ တိုးချဲ့ခြင်း

Application ၏ architecture အသစ်သည် မည်သို့မည်ပုံဖြစ်မည်ကို ကျွန်ုပ်အကျဉ်းချုံးရှင်းပြပါမည်။ နောက်ပုံတွင် ပြထားသည့်အတိုင်း၊ `pod` တွင် `container` နှစ်ခုပါဝင်မည်ဖြစ်သည် - Node.js နှင့် Envoy `container` အသစ်။ Node.js `container` သည် HTTP request များကို တိုက်ရိုက်ဆက်လက်ကိုင်တွယ်မည်ဖြစ်သော်လည်း HTTPS request များကို Envoy မှ ကိုင်တွယ်မည်ဖြစ်သည်။ incoming HTTPS request တစ်ခုချင်းစီအတွက်၊ Envoy သည် local loopback device (`localhost` IP address မှတစ်ဆင့်) မှတစ်ဆင့် Node.js application သို့ ပေးပို့မည့် HTTP request အသစ်တစ်ခုကို ဖန်တီးမည်ဖြစ်သည်။

*ပုံ ၅.၁၀။ Pod ၏ container များနှင့် network interface များ၏ အသေးစိတ်မြင်ကွင်း*

Envoy သည် နောက်အခန်းရှိ လေ့ကျင့်ခန်းအချို့တွင် အသုံးဝင်မည့် web-based administration interface တစ်ခုကိုလည်း ပံ့ပိုးပေးသည်။

သင်သည် TLS support ကို application ၏ JavaScript code ထဲတွင် ထည့်သွင်းပါက၊ ဤအရာအားလုံးကို application တစ်ခုတည်းဖြင့် ရရှိနိုင်မည်မှာ ရှင်းနေပါသည်။ သို့သော်၊ သင်သည် application ၏ source code ကို ပြောင်းလဲရန် ခက်ခဲသော သို့မဟုတ် မဖြစ်နိုင်သော အခြေအနေများတွင်၊ Envoy proxy ကို အသုံးပြုခြင်းသည် ပိုမိုမြန်ဆန်လွယ်ကူသော ဖြေရှင်းချက်ဖြစ်နိုင်သည်။ ၎င်းသည် Envoy မှ ပံ့ပိုးပေးသော အခြား feature များစွာကို သင်ထပ်ထည့်နိုင်သည့် ကောင်းမွန်သောအစပြုမှတ်တစ်ခုကိုလည်း ပေးသည်။ ပိုမိုလေ့လာလိုပါက `envoyproxy.io` ရှိ Envoy proxy documentation ကို ကိုးကားပါ။

### 5.4.2 Pod သို့ Envoy proxy ထပ်ထည့်ခြင်း

`Container` နှစ်ခုပါသော `pod` အသစ်တစ်ခုကို သင်ဖန်တီးပါမည်။ သင့်တွင် Node.js `container` ရှိပြီးဖြစ်သော်လည်း Envoy ကို run မည့် `container` တစ်ခုလည်း လိုအပ်သည်။

#### Envoy container image ကို ဖန်တီးခြင်း

Proxy ၏ author များသည် တရားဝင် Envoy proxy `container` image ကို Docker Hub တွင် ထုတ်ဝေထားသည်။ သင်သည် ဤ image ကို တိုက်ရိုက်အသုံးပြုနိုင်သော်လည်း၊ configuration, certificate နှင့် private key file များကို `container` ရှိ Envoy process သို့ တစ်နည်းနည်းဖြင့် ပံ့ပိုးပေးရန် လိုအပ်မည်ဖြစ်သည်။ ၎င်းကို မည်သို့ပြုလုပ်ရမည်ကို အခန်း ၇ တွင် သင်လေ့လာရပါမည်။ ယခုအချိန်တွင်၊ သင်သည် ဤ file သုံးခုစလုံးပါဝင်ပြီးသော image တစ်ခုကို အသုံးပြုပါမည်။

ကျွန်ုပ်သည် image ကို ဖန်တီးပြီး `docker.io/luksa/kiada-ssl-proxy:0.1` တွင် ရရှိနိုင်အောင် ပြုလုပ်ထားပြီးဖြစ်သော်လည်း၊ သင်ကိုယ်တိုင် build လုပ်လိုပါက၊ `Chapter05/kiada-ssl-proxy-image/` directory တွင် လိုအပ်သော file များကို သင်ရှာတွေ့နိုင်ပါသည်။

#### Container နှစ်ခုပါသော pod တစ်ခု ဖန်တီးခြင်း

`Container` နှစ်ခုပါသော `pod` ၏ manifest သည် အောက်ပါ listing တွင် ပြသထားသည်။ ၎င်းကို `Chapter05/pod.kiada-ssl.yaml` file တွင် သင်ရှာတွေ့နိုင်သည်။

**Listing 5.3။ kiada-ssl pod ၏ Manifest**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kiada-ssl
spec:
  containers:
  - name: kiada                   # A
    image: luksa/kiada:0.2        # A
    ports:                        # A
    - name: http                  # A
      containerPort: 8080         # A
  - name: envoy                   # B
    image: luksa/kiada-ssl-proxy:0.1 # B
    ports:                        # B
    - name: https                 # B
      containerPort: 8443         # B
    - name: admin                 # B
      containerPort: 9901         # B
```

*   **A** ပထမ `container` သည် kiada application ကို run သည်။
*   **B** ဒုတိယ `container` သည် envoy proxy ကို run သည်။

ဤ `pod` ၏အမည်မှာ `kiada-ssl` ဖြစ်သည်။ ၎င်းတွင် `kiada` နှင့် `envoy` ဟုခေါ်သော `container` နှစ်ခုရှိသည်။ manifest သည် အပိုင်း 5.2.1 ရှိ manifest ထက် အနည်းငယ်သာ ပိုရှုပ်ထွေးသည်။ အသစ်ပါဝင်သော field များမှာ port name များဖြစ်ပြီး၊ manifest ကို ဖတ်ရှုသူတိုင်း port တစ်ခုချင်းစီ၏ ရည်ရွယ်ချက်ကို နားလည်နိုင်စေရန် ထည့်သွင်းထားသည်။ Service တစ်ခုတွင် port များစွာကို expose လုပ်သည့်အခါ port name များသည် လိုအပ်ပါသည်။

#### Multi-container pod နှင့် အပြန်အလှန်ဆက်သွယ်ခြင်း

`kubectl apply` ကို အသုံးပြု၍ `pod` ကို ဖန်တီးပြီးနောက်၊ `kubectl port-forward` command ကို အသုံးပြု၍ port သုံးခုစလုံးသို့ forwarding ကို အောက်ပါအတိုင်း enable လုပ်နိုင်သည်-

```bash
$ kubectl port-forward kiada-ssl 8080 8443 9901
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Forwarding from 127.0.0.1:8443 -> 8443
Forwarding from [::1]:8443 -> 8443
Forwarding from 127.0.0.1:9901 -> 9901
Forwarding from [::1]:9901 -> 9901
```

ပထမဦးစွာ၊ သင်၏ browser တွင် `http://localhost:8080` URL ကိုဖွင့်ခြင်းဖြင့် သို့မဟုတ် `curl` ကို အသုံးပြု၍ application နှင့် HTTP မှတစ်ဆင့် ဆက်သွယ်နိုင်ကြောင်း အတည်ပြုပါ-

```bash
$ curl localhost:8080
Kiada version 0.2. Request processed by "kiada-ssl". Client IP: ::ffff:127.0.0.1
```

၎င်းအလုပ်လုပ်ပါက၊ သင်သည် application ကို `https://localhost:8443` တွင် HTTPS မှတစ်ဆင့် access လုပ်ရန်လည်း ကြိုးစားနိုင်သည်။ `curl` ဖြင့် အောက်ပါအတိုင်း သင်ပြုလုပ်နိုင်သည်-

```bash
$ curl https://localhost:8443 --insecure
Kiada version 0.2. Request processed by "kiada-ssl". Client IP: ::ffff:127.0.0.1
```

အောင်မြင်သွားပြီ! Envoy proxy သည် တာဝန်ကို ကောင်းမွန်စွာ ကိုင်တွယ်ဖြေရှင်းပေးသည်။ သင်၏ application သည် ယခုအခါ sidecar `container` ကို အသုံးပြု၍ HTTPS ကို support လုပ်နေပြီဖြစ်သည်။

> **`--insecure` option ကို အဘယ်ကြောင့် အသုံးပြုရသနည်း။**
> `--insecure` option ကို အသုံးပြုရသည့် အကြောင်းရင်းနှစ်ခုရှိသည်။ ပထမအကြောင်းရင်းမှာ `curl` သည် server ၏ certificate ကို default အားဖြင့် trust မလုပ်သောကြောင့်ဖြစ်သည်၊ အကြောင်းမှာ ၎င်းသည် self-signed ဖြစ်သောကြောင့်ဖြစ်သည်။ ဒုတိယအကြောင်းရင်းမှာ certificate သည် `localhost` အတွက် မဟုတ်ဘဲ `kiada.example.com` host အတွက် ထုတ်ပေးထားသောကြောင့်ဖြစ်သည်။ `--insecure` option ကို အသုံးပြုမည့်အစား၊ သင်သည် `curl` အား ဤ certificate ကို trust လုပ်ရန်နှင့် `kiada.example.com` ကို `localhost` သို့ resolve လုပ်ရန် ညွှန်ကြားနိုင်သည်-
> `$ curl https://kiada.example.com --resolve kiada.example.com:443:127.0.0.1 --cacert certs/example-com.crt`
> ၎င်းသည် ရိုက်ရန်အလွန်များသည်။ ထို့ကြောင့် ကျွန်တော်သည် `--insecure` option သို့မဟုတ် ပိုတိုသော `-k` variant ကို အသုံးပြုရခြင်းကို ပိုနှစ်သက်သည်။

#### Container များစွာပါသော pod များ၏ log များကို ပြသခြင်း

`kiada-ssl` `pod` တွင် `container` နှစ်ခုပါဝင်သောကြောင့်၊ log များကို ပြသလိုပါက၊ `--container` သို့မဟုတ် `-c` option ကို အသုံးပြု၍ `container` ၏အမည်ကို သတ်မှတ်ရန် လိုအပ်သည်။ ဥပမာအားဖြင့်၊ `kiada` `container` ၏ log များကို ကြည့်ရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl logs kiada-ssl -c kiada
```

Envoy proxy သည် `envoy` ဟုအမည်ပေးထားသော `container` တွင် run နေသောကြောင့်၊ ၎င်း၏ log များကို အောက်ပါအတိုင်း ပြသသည်-

```bash
$ kubectl logs kiada-ssl -c envoy
```

တစ်နည်းအားဖြင့်၊ `--all-containers` option ဖြင့် `container` နှစ်ခုလုံး၏ log များကို ပြသနိုင်သည်-

```bash
$ kubectl logs kiada-ssl --all-containers
```

သင်သည် ဤ command များကို အပိုင်း 5.3.2 တွင် ရှင်းပြထားသော အခြား option များနှင့် ပေါင်းစပ်နိုင်သည်။

#### Multi-container pod များ၏ Container များတွင် Command များ Run ခြင်း

`kubectl exec` command ကို အသုံးပြု၍ `pod` ၏ `container` များထဲမှ တစ်ခုတွင် shell သို့မဟုတ် အခြား command တစ်ခုကို run လိုပါက၊ `--container` သို့မဟုတ် `-c` option ကို အသုံးပြု၍ `container` name ကိုလည်း သတ်မှတ်ရန် လိုအပ်သည်။ ဥပမာအားဖြင့်၊ `envoy` `container` တွင် shell run ရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl exec -it kiada-ssl -c envoy -- bash
```

## 5.5 Pod Startup တွင် နောက်ထပ် Container များကို Run ခြင်း

`Pod` တစ်ခုတွင် `container` တစ်ခုထက်ပို၍ ပါဝင်သောအခါ၊ `container` အားလုံးသည် parallel အတိုင်း စတင်လည်ပတ်ကြသည်။ Kubernetes သည် `container` တစ်ခုက အခြား `container` တစ်ခုပေါ်တွင် မှီခိုနေကြောင်း သတ်မှတ်ပေးသည့် mechanism ကို မပံ့ပိုးသေးပါ၊ ၎င်းသည် တစ်ခုမစတင်မီ အခြားတစ်ခုကို စတင်ရန် သေချာစေမည်ဖြစ်သည်။ သို့သော်၊ Kubernetes သည် `pod` ၏ main `container` များမစတင်မီ `pod` ကို initialize လုပ်ရန် `container` အစဉ်လိုက် run ရန် ခွင့်ပြုသည်။ ဤအထူး `container` အမျိုးအစားကို ဤကဏ္ဍတွင် ရှင်းပြထားသည်။

### 5.5.1 Init container များကို မိတ်ဆက်ခြင်း

`Pod` manifest တစ်ခုသည် `pod` စတင်သည့်အခါနှင့် `pod` ၏ normal `container` များမစတင်မီ run ရန် `container` စာရင်းတစ်ခုကို သတ်မှတ်နိုင်သည်။ ဤ `container` များသည် `pod` ကို initialize လုပ်ရန် ရည်ရွယ်ပြီး **init containers** ဟု သင့်လျော်စွာ ခေါ်တွင်သည်။ အောက်ပါပုံတွင် ပြထားသည့်အတိုင်း၊ ၎င်းတို့သည် တစ်ခုပြီးတစ်ခု run ကြပြီး `pod` ၏ main `container` များမစတင်မီ အားလုံးအောင်မြင်စွာ ပြီးဆုံးရမည်ဖြစ်သည်။

*ပုံ ၅.၁၁။ Pod တစ်ခု၏ init နှင့် regular container များ မည်သို့စတင်သည်ကို ပြသသည့် အချိန်ဇယား*

`Init container` များသည် `pod` ၏ regular `container` များနှင့်တူညီသော image format နှင့် standard `container` feature များကို အသုံးပြုသောကြောင့် ၎င်းတို့သည် `pod` ၏ regular `container` များနှင့်တူသည်။ သို့သော်၊ `init container` များ၏ ရည်ရွယ်ချက်မှာ အကန့်အသတ်မရှိ run ရန်မဟုတ်ဘဲ၊ ၎င်းတို့၏ task ကို ပြီးဆုံးသည်အထိ run ရန်ဖြစ်သည်။

#### Init container များအတွက် အသုံးများသော case များ

`Init container` များကို အောက်ပါအခြေအနေများတွင် အသုံးပြုနိုင်သည်-

*   **`Pod` ၏ filesystem ကို data များဖြင့် ဖြည့်တင်းခြင်း။** ဥပမာအားဖြင့်၊ `init container` တစ်ခုသည် external source မှ data များကို ဒေါင်းလုဒ်လုပ်ပြီး main `container` အတွက် ပြင်ဆင်နိုင်သည်။
*   **`Pod` ၏ networking system ကို Initialize လုပ်ခြင်း။** `Pod` ၏ `container` အားလုံးသည် တူညီသော network namespace များကို မျှဝေသုံးစွဲသောကြောင့်၊ `init container` တစ်ခုမှ ၎င်းကို ပြုလုပ်သော ပြောင်းလဲမှုများသည် main `container` ကိုလည်း သက်ရောက်မှုရှိသည်။
*   **`Pod` ၏ main `container` များမစတင်မီ ကြိုတင်လိုအပ်ချက်တစ်ခု ပြည့်မီသည်အထိ နှောင့်နှေးခြင်း။** ဥပမာအားဖြင့်၊ main `container` သည် အခြား service တစ်ခုရရှိနိုင်မှသာ စတင်ရန် မှီခိုနေပါက၊ `init container` တစ်ခုသည် ဤ service အဆင်သင့်ဖြစ်သည်အထိ block လုပ်ထားနိုင်သည်။
*   **`Pod` စတင်တော့မည်ဖြစ်ကြောင်း external service တစ်ခုကို အသိပေးခြင်း။** Application instance အသစ်တစ်ခု စတင်သည့်အခါ external system တစ်ခုကို အသိပေးရန်လိုအပ်သော အထူးအခြေအနေများတွင်၊ ဤ notification ကို ပေးပို့ရန် `init container` ကို အသုံးပြုနိုင်သည်။

သင်သည် ဤ operation များကို main `container` ထဲတွင်ပင် လုပ်ဆောင်နိုင်သော်လည်း၊ `init container` ကို အသုံးပြုခြင်းသည် တစ်ခါတစ်ရံ ပိုကောင်းသော option ဖြစ်ပြီး အခြားအကျိုးကျေးဇူးများလည်း ရှိနိုင်သည်၊ ဥပမာ-

*   **Logic ကို ခွဲခြားထားခြင်း။** Main `container` မှ initialization logic ကို ခွဲထုတ်ခြင်းဖြင့်၊ main `container` image ကို အခြားနေရာများတွင် ပြန်လည်အသုံးပြုနိုင်ပြီး၊ initialization logic ကို အခြားသူများမှ တီထွင်ပြီး စီမံခန့်ခွဲနိုင်သည်။
*   **Security ကို တိုးမြှင့်ခြင်း။** Initialization အတွက် လိုအပ်သော tool များနှင့် credential များကို main `container` image တွင် ထည့်သွင်းရန်မလိုသောကြောင့်၊ application ၏ attack surface ကို လျှော့ချနိုင်သည်။ ဥပမာအားဖြင့်၊ application ကို external service တစ်ခုတွင် register လုပ်ရန် လိုအပ်သည်ဆိုပါစို့။ ထိုသို့ပြုလုပ်ရန်၊ ၎င်း၏ filesystem တွင် secret token တစ်ခုရှိရန် လိုအပ်သည်။ Main `container` တွင် run နေသော application တွင် attacker တစ်ဦးက filesystem ရှိ မည်သည့် file ကိုမဆို ဖတ်ရန်ခွင့်ပြုသည့် အားနည်းချက်တစ်ခုရှိပါက၊ attacker သည် ဤ token ကို ရရှိသွားနိုင်သည်။ Initialization ကို `init container` မှ လုပ်ဆောင်ခြင်းဖြင့်၊ token သည် `init container` ၏ filesystem တွင်သာ ရှိရန်လိုအပ်ပြီး၊ attacker တစ်ဦးက အလွယ်တကူ ထိုးဖောက်ဝင်ရောက်နိုင်မည်မဟုတ်ပါ။

### 5.5.2 Pod သို့ Init container များ ထပ်ထည့်ခြင်း

`Pod` manifest တစ်ခုတွင်၊ `init container` များကို `spec` section ရှိ `initContainers` field တွင် သတ်မှတ်သည်၊ regular `container` များကို `containers` field တွင် သတ်မှတ်သကဲ့သို့ပင်။

#### Pod manifest တွင် init container များကို သတ်မှတ်ခြင်း

`kiada` `pod` သို့ `init container` နှစ်ခုထပ်ထည့်သည့် ဥပမာကို ကြည့်ကြပါစို့။ ပထမ `init container` သည် initialization procedure တစ်ခုကို တုပသည်။ ၎င်းသည် ၅ စက္ကန့်ကြာ run ပြီး standard output သို့ text line အနည်းငယ် print ထုတ်သည်။

ဒုတိယ `init container` သည် `ping` command ကို အသုံးပြု၍ `pod` အတွင်းမှ သတ်မှတ်ထားသော IP address တစ်ခုကို reach လုပ်နိုင်မလား စစ်ဆေးခြင်းဖြင့် network connectivity test တစ်ခုကို လုပ်ဆောင်သည်။

**Listing 5.4။ kiada-init pod ၏ manifest**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kiada-init
spec:
  initContainers:                # A
  - name: init-demo              # B
    image: luksa/init-demo:0.1  # B
  - name: network-check          # C
    image: luksa/network-connectivity-checker:0.1 # C
  containers:                    # D
  - name: kiada                  # D
    image: luksa/kiada:0.2      # D
    stdin: true                 # D
    ports:                      # D
    - name: http                # D
      containerPort: 8080       # D
  - name: envoy                  # D
    image: luksa/kiada-ssl-proxy:0.1 # D
    ports:                      # D
    - name: https               # D
      containerPort: 8443       # D
```

*   **A** `Init container` များကို ဤနေရာတွင် သတ်မှတ်သည်။
*   **B** ပထမ `init container`
*   **C** ဒုတိယ `init container`
*   **D** Regular `container` များ

#### Init container များ အလုပ်လုပ်ပုံကို ကြည့်ရှုခြင်း

`Pod` စတင်လာသည့်အခါ ဘာတွေဖြစ်ပျက်သည်ကို စောင့်ကြည့်ရန်၊ terminal နှစ်ခုဖွင့်ပါ။ ပထမတစ်ခုတွင်၊ `pod` ၏ status ကို စောင့်ကြည့်ရန် အောက်ပါ command ကို run ပါ-

```bash
$ kubectl get pod kiada-init -w
```

ဒုတိယ terminal တွင်၊ event များကို စောင့်ကြည့်ရန် အောက်ပါ command ကို run ပါ-

```bash
$ kubectl get events -w
```

အဆင်သင့်ဖြစ်လျှင် `apply` command ကို run ခြင်းဖြင့် `pod` ကို ဖန်တီးပါ-

```bash
$ kubectl apply -f pod.kiada-init.yaml
```

#### Init container များပါသော pod တစ်ခု၏ startup ကို စစ်ဆေးခြင်း

`Pod` စတင်လာသည့်အခါ `kubectl get events -w` command မှ ပြသသော event များကို စစ်ဆေးပါ-

```bash
TYPE      REASON      MESSAGE
Normal    Scheduled   Successfully assigned pod to worker2
Normal    Pulling     Pulling image "luksa/init-demo:0.1"          # A
Normal    Pulled      Successfully pulled image                    # A
Normal    Created     Created container init-demo                  # A
Normal    Started     Started container init-demo                  # A
Normal    Pulling     Pulling image "luksa/network-connec...       # B
Normal    Pulled      Successfully pulled image                    # B
Normal    Created     Created container network-check              # B
Normal    Started     Started container network-check              # B
Normal    Pulled      Container image "luksa/kiada:0.1" already present on machine # C
Normal    Created     Created container kiada                      # C
Normal    Started     Started container kiada                      # C
Normal    Pulled      Container image "luksa/kiada-ssl-proxy:0.1" already present # D
Normal    Created     Created container envoy                      # D
Normal    Started     Started container envoy                      # D
```

*   **A** `init-demo` `container` ကို စတင်ခြင်း
*   **B** `network-check` `container` ကို စတင်ခြင်း
*   **C** `kiada` `container` ကို စတင်ခြင်း
*   **D** `envoy` `container` ကို စတင်ခြင်း

ယခု `pod` ၏ status ပြောင်းလဲမှုများကို ကြည့်ရှုပါ။

```bash
$ kubectl get po kiada-init -w
NAME         READY   STATUS     RESTARTS   AGE
kiada-init   0/2     Init:0/2   0          2s   # A
kiada-init   0/2     Init:1/2   0          6s   # B
kiada-init   0/2     PodInitializing   0   7s   # C
kiada-init   2/2     Running           0   8s   # D
```

*   **A** ပထမ `init container` run နေသည်။
*   **B** ဒုတိယ `init container` run နေသည်။
*   **C** `Init container` များ ပြီးဆုံးသွားပြီ။
*   **D** `Pod` ၏ regular `container` များ run နေပြီ။

Listing တွင်ပြထားသည့်အတိုင်း၊ `init container` များ run နေချိန်တွင်၊ `pod` ၏ status သည် ပြီးဆုံးသွားသော `init container` အရေအတွက်နှင့် စုစုပေါင်းအရေအတွက်ကို ပြသသည်။ `init container` အားလုံးပြီးဆုံးသွားသောအခါ၊ `pod` ၏ status ကို `PodInitializing` အဖြစ်ပြသသည်။ ဤအချိန်တွင် main `container` များ၏ image များကို pull လုပ်သည်။ `container` များစတင်သောအခါ၊ status သည် `Running` သို့ ပြောင်းလဲသွားသည်။

### 5.5.3 Init container များကို စစ်ဆေးခြင်း

Regular `container` များကဲ့သို့ပင်၊ သင်သည် `kubectl exec` ကို အသုံးပြု၍ run နေသော `init container` တွင် နောက်ထပ် command များကို run နိုင်ပြီး `kubectl logs` ကို အသုံးပြု၍ log များကို ပြသနိုင်သည်။

#### Init container တစ်ခု၏ log များကို ပြသခြင်း

`Init container` တစ်ခုစီ ရေးသားနိုင်သော standard နှင့် error output ကို regular `container` များအတွက်ကဲ့သို့ပင် ဖမ်းယူထားသည်။ `Init container` တစ်ခု၏ log များကို `-c` option ဖြင့် `container` ၏အမည်ကို သတ်မှတ်ခြင်းဖြင့် `kubectl logs` command ကို အသုံးပြု၍ ပြသနိုင်သည်-

```bash
$ kubectl logs kiada-init -c init-demo
```

`Init container` များသည် များသောအားဖြင့် လျင်မြန်စွာ ပြီးဆုံးသွားသောကြောင့်၊ log များကို ရယူရန် သင်သည် ဤ command ကို လျင်မြန်စွာ run ရန် လိုအပ်သည်။ ၎င်း ပြီးဆုံးသွားပြီးနောက်တွင် သင် log များကို ကြည့်ရှုနိုင်သေးသည်။

`Init container` တစ်ခု fail ဖြစ်ပြီး ၎င်းဘာကြောင့်ဖြစ်ရသည်ကို သင်ရှာဖွေလိုပါက၊ `kubectl get events` ကို အသုံးပြု၍ ၎င်း၏ status ကို စစ်ဆေးခြင်းသည် ပိုမိုလွယ်ကူသောနည်းလမ်းဖြစ်နိုင်သည်။

#### Run နေသော init container တစ်ခုသို့ Attach လုပ်ခြင်း

`Init container` များသည် များသောအားဖြင့် အချိန်တိုအတွင်းသာ run သောကြောင့် `kubectl attach` သို့မဟုတ် `kubectl exec` ဖြင့် ၎င်းတို့နှင့် အပြန်အလှန်ဆက်သွယ်ရန် ခက်ခဲသည်။ `pod.kiada-init-slow.yaml` manifest file သည် စက္ကန့် ၆၀ ကြာအောင် run သော `init container` တစ်ခုပါဝင်သော `pod` တစ်ခုကို ဖန်တီးသည်။ ၎င်းသည် သင့်အား `container` အတွင်းသို့ ဝင်ရောက်စူးစမ်းလေ့လာရန် အချိန်အလုံအလောက်ပေးသည်။

`Pod` စတင်သောအခါ၊ အောက်ပါ command ဖြင့် `container` တွင် shell တစ်ခု run ပါ-

```bash
$ kubectl exec -it kiada-init-slow -c init-demo -- sh
```

သင်သည် `container` ကို အတွင်းမှ စူးစမ်းလေ့လာရန် shell ကို အသုံးပြုနိုင်သော်လည်း၊ အချိန်တိုအတွင်းသာဖြစ်သည်။ `Container` ၏ main process သည် စက္ကန့် ၆၀ အကြာတွင် ထွက်သွားသောအခါ၊ shell process ကိုလည်း terminate လုပ်သွားမည်ဖြစ်သည်။

သင်သည် run နေသော `init container` တစ်ခုသို့ ဝင်ရောက်ခြင်းကို ၎င်းအချိန်မီမပြီးဆုံးနိုင်ဘဲ fail ဖြစ်နေသည့်အခါ အကြောင်းရင်းကို ရှာဖွေလိုမှသာ ပြုလုပ်လေ့ရှိသည်။ ပုံမှန်လည်ပတ်မှုတွင်၊ `kubectl exec` command ကို သင် run နိုင်မီ `init container` သည် terminate ဖြစ်သွားလိမ့်မည်။

## 5.6 Pod များနှင့် အခြား Object များကို ဖျက်ခြင်း

သင်သည် ဤအခန်းနှင့် အခန်း ၂ ရှိ လေ့ကျင့်ခန်းများကို စမ်းသပ်ခဲ့ပါက၊ သင်၏ cluster တွင် `pod` များနှင့် အခြား object အများအပြား ရှိနေလောက်ပြီဖြစ်သည်။ ဤအခန်းကို အဆုံးသတ်ရန်၊ ၎င်းတို့ကို ဖျက်ရန် နည်းလမ်းအမျိုးမျိုးကို သင်လေ့လာရမည်ဖြစ်သည်။ `pod` တစ်ခုကို ဖျက်ခြင်းသည် ၎င်း၏ `container` များကို terminate လုပ်ပြီး ၎င်းတို့ကို node မှ ဖယ်ရှားပါလိမ့်မည်။ `Deployment` object တစ်ခုကို ဖျက်ခြင်းသည် ၎င်း၏ `pod` များကို ဖျက်ပစ်စေပြီး၊ `LoadBalancer-typed Service` တစ်ခုကို ဖျက်ခြင်းသည် load balancer ကို deprovision လုပ်ပါလိမ့်မည် (provision လုပ်ထားခဲ့လျှင်)။

### 5.6.1 Pod တစ်ခုကို အမည်ဖြင့် ဖျက်ခြင်း

Object တစ်ခုကို ဖျက်ရန် အလွယ်ကူဆုံးနည်းလမ်းမှာ `kubectl delete` command တွင် ၎င်း၏အမျိုးအစားနှင့် အမည်ကို သတ်မှတ်ခြင်းဖြစ်သည်။ ဥပမာအားဖြင့်၊ `kiada` `pod` ကို ဖျက်ရန်၊ အောက်ပါ command ကို run ပါ-

```bash
$ kubectl delete pod kiada
pod "kiada" deleted
```

> **အကြံပြုချက်**
> ပုံမှန်အားဖြင့်၊ `kubectl delete` command သည် object မရှိတော့သည်အထိ စောင့်ဆိုင်းသည်။ စောင့်ဆိုင်းခြင်းကို ကျော်ရန်၊ command ကို `--wait=false` option ဖြင့် run ပါ။

`Pod` သည် ဖျက်သိမ်းနေစဉ်အတွင်း၊ ၎င်း၏ status သည် `Terminating` သို့ ပြောင်းလဲသွားသည်-

```bash
$ kubectl get po kiada
NAME    READY   STATUS        RESTARTS   AGE
kiada   1/1     Terminating   0          35m
```

`Container` များကို မည်သို့မည်ပုံ shutdown လုပ်သည်ကို အတိအကျသိရှိခြင်းသည် သင်၏ application က ၎င်း၏ client များအတွက် ကောင်းမွန်သောအတွေ့အကြုံကို ပေးလိုပါက အရေးကြီးပါသည်။ ၎င်းကို နောက်အခန်းတွင် ရှင်းပြထားပြီး၊ `pod` နှင့် ၎င်း၏ `container` များ၏ life cycle ကို ပိုမိုနက်ရှိုင်းစွာ လေ့လာပါမည်။

> **မှတ်ချက်**
> သင် Docker နှင့် ရင်းနှီးပါက၊ `pod` တစ်ခုကို ရပ်တန့်ပြီး နောက်မှ ပြန်လည်စတင်နိုင်မလားဟု သင်တွေးတောမိပေမည်။ အဖြေမှာ မရပါဟု ဖြစ်သည်။ Kubernetes တွင်၊ သင်သည် `pod` တစ်ခုကို လုံးဝဖယ်ရှားပြီး နောက်မှ ပြန်လည်ဖန်တီးနိုင်ရုံသာဖြစ်သည်။

#### Pod များစွာကို command တစ်ခုတည်းဖြင့် ဖျက်ခြင်း

`Pod` များစွာကို command တစ်ခုတည်းဖြင့်လည်း သင်ဖျက်နိုင်သည်။ သင် `kiada-init` နှင့် `kiada-init-slow` `pod` များကို run ခဲ့ပါက၊ ၎င်းတို့၏အမည်များကို space ခြားပြီး စာရင်းပြုစုခြင်းဖြင့် ၎င်းတို့နှစ်ခုလုံးကို ဖျက်နိုင်သည်-

```bash
$ kubectl delete pod kiada-init kiada-init-slow
```

### 5.6.2 Manifest file မှ Object များကို ဖျက်ခြင်း

သင် object တစ်ခုကို ဖန်တီးခဲ့သည့် manifest file ကို အသုံးပြု၍လည်း ၎င်းကို ဖျက်နိုင်သည်။

`pod.kiada-ssl.yaml` file မှ သင်ဖန်တီးခဲ့သော `kiada-ssl` `pod` ကို အောက်ပါ command ဖြင့် ဖျက်နိုင်သည်-

```bash
$ kubectl delete -f pod.kiada-ssl.yaml
pod "kiada-ssl" deleted
```

သင်၏ကိစ္စတွင်၊ file တွင် `pod` object တစ်ခုတည်းသာ ပါဝင်သော်လည်း၊ application တစ်ခုလုံးကို ကိုယ်စားပြုသော အမျိုးအစားအမျိုးမျိုးရှိ object များစွာပါဝင်သည့် file များကို သင်တွေ့ရလေ့ရှိသည်။ ၎င်းသည် application ကို deploy လုပ်ခြင်းနှင့် ဖယ်ရှားခြင်းကို `kubectl apply -f app.yaml` နှင့် `kubectl delete -f app.yaml` ကို execute လုပ်ရုံဖြင့် လွယ်ကူစေသည်။

#### Manifest file များစွာမှ Object များကို ဖျက်ခြင်း

တစ်ခါတစ်ရံ၊ application တစ်ခုကို manifest file များစွာတွင် သတ်မှတ်ထားသည်။ သင်သည် file များကို ကော်မာဖြင့် ခွဲခြားခြင်းဖြင့် file များစွာကို သတ်မှတ်နိုင်သည်-

```bash
$ kubectl delete -f pod.kiada.yaml,pod.kiada-ssl.yaml
```

> **မှတ်ချက်**
> သင်သည် ဤ syntax ကို အသုံးပြု၍ file များစွာကို တစ်ချိန်တည်းတွင် apply လုပ်နိုင်သည် (ဥပမာ- `kubectl apply -f pod.kiada.yaml,pod.kiada-ssl.yaml`)။

ကျွန်တော်သည် Kubernetes ကို အသုံးပြုมาသည့် နှစ်များတစ်လျှောက် ဤချဉ်းကပ်မှုကို အမှန်တကယ် မသုံးเคยသော်လည်း၊ directory တစ်ခုလုံးရှိ manifest file အားလုံးကို deploy လုပ်လေ့ရှိသည်။ `kubectl delete` command ဖြင့်လည်း ထိုသို့ပြုလုပ်နိုင်သည်။ သင်၏ manifest file အားလုံးကို `my-app/` directory တွင် ထားရှိပါက၊ ၎င်းတို့ကို အောက်ပါအတိုင်း သင်ဖျက်နိုင်သည်-

```bash
$ kubectl delete -f my-app/
```

> **မှတ်ချက်**
> သင်၏ manifest file များကို subdirectory များတွင် သိမ်းဆည်းထားပါက၊ `--recursive` flag (သို့မဟုတ် `-R`) ကို အသုံးပြုရမည်။

### 5.6.3 Pod အားလုံးကို ဖျက်ခြင်း

သင်သည် `kiada-stdin` နှင့် အခန်း ၃ တွင် `kubectl create deployment` command ကို အသုံးပြု၍ ဖန်တီးခဲ့သော `pod` များမှလွဲ၍ `pod` အားလုံးကို ဖယ်ရှားပြီးဖြစ်သည်။ သင် deployment ကို scale လုပ်ပုံပေါ်မူတည်၍ ဤ `pod` အချို့သည် run နေဆဲဖြစ်သင့်သည်-

```bash
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
kiada-stdin              1/1     Running   0          10m
kiada-9d785b578-58vhc    1/1     Running   0          1d
kiada-9d785b578-jmnj8    1/1     Running   0          1d
```

ဤ `pod` များကို အမည်ဖြင့် ဖျက်မည့်အစား၊ `--all` option ကို အသုံးပြု၍ ၎င်းတို့အားလုံးကို ဖျက်နိုင်သည်-

```bash
$ kubectl delete po --all
pod "kiada-stdin" deleted
pod "kiada-9d785b578-58vhc" deleted
pod "kiada-9d785b578-jmnj8" deleted
```

ယခု `kubectl get pods` command ကို ထပ်မံ execute လုပ်ခြင်းဖြင့် `pod` များမရှိတော့ကြောင်း အတည်ပြုပါ-

```bash
$ kubectl get po
NAME                     READY   STATUS    RESTARTS   AGE
kiada-9d785b578-cc6tk    1/1     Running   0          13s
kiada-9d785b578-h4gml    1/1     Running   0          13s
```

သင်သည် `pod` များကို ဖျက်လိုက်သော်လည်း၊ `pod` အသစ်များသည် ချက်ချင်းပေါ်လာသည်ကို တွေ့ရသောကြောင့် သင်အံ့အားသင့်သွားပေမည်။ ၎င်းမှာ `Deployment` object ၏ တစိတ်တပိုင်းအဖြစ် သင်ဖန်တီးခဲ့သော `pod` များကြောင့်ဖြစ်သည်။ `Deployment` object များကို အသက်ဝင်လာစေရန် တာဝန်ရှိသော controller သည် object တွင် သတ်မှတ်ထားသော လိုချင်သော replica အရေအတွက်နှင့် `pod` အရေအတွက် အမြဲတမ်းကိုက်ညီစေရန် သေချာစေရမည်။ သင် `Deployment` နှင့် ဆက်စပ်နေသော `pod` တစ်ခုကို ဖျက်လိုက်သောအခါ၊ controller သည် ပျောက်ဆုံးသွားသော `pod` နေရာတွင် `pod` အသစ်တစ်ခုကို ချက်ချင်းဖန်တီးသည်။

ဤ `pod` များကို ဖျက်ရန်၊ သင်သည် `Deployment` ကို zero သို့ scale လုပ်ရမည် သို့မဟုတ် object ကို လုံးဝဖျက်ပစ်ရမည်။ ၎င်းသည် သင်ဤ deployment သို့မဟုတ် ၎င်း၏ `pod` များ သင်၏ cluster တွင် မရှိစေလိုတော့ကြောင်း ညွှန်ပြသည်။

### 5.6.4 "all" keyword ကို အသုံးပြု၍ Object များကို ဖျက်ခြင်း

သင်ယခုအချိန်အထိ ဖန်တီးခဲ့သမျှအရာအားလုံး - deployment, ၎င်း၏ `pod` များ, နှင့် service အပါအဝင် - ကို အောက်ပါ command ဖြင့် ဖျက်နိုင်သည်-

```bash
$ kubectl delete all --all
pod "kiada-9d785b578-cc6tk" deleted
pod "kiada-9d785b578-h4gml" deleted
service "kubernetes" deleted
service "kiada" deleted
deployment.apps "kiada" deleted
replicaset.apps "kiada-9d785b578" deleted
```

Command ရှိ ပထမ `all` သည် object အမျိုးအစားအားလုံးကို ဖျက်လိုကြောင်း ညွှန်ပြသည်။ `--all` option သည် ထိုအမျိုးအစားများ၏ instance အားလုံးကို ဖျက်လိုကြောင်း ညွှန်ပြသည်။

`all` keyword သည် သင်၏ namespace ရှိ အဖြစ်များသော၊ namespaced resource type အများစုအတွက် အတိုကောက်တစ်ခုဖြစ်ကြောင်း သတိပြုရန် အရေးကြီးသည်။ `kubectl api-resources` command ဖြင့် မည်သည့် resource များကို `all` တွင် ထည့်သွင်းထားသည်ကို သင်ကြည့်ရှုနိုင်သည်။ `Event` object kind သည် ဤအရာ၏ ဥပမာတစ်ခုဖြစ်သည်။

> **မှတ်ချက်**
> သင်သည် `delete` command တွင် object type များစွာကို သတ်မှတ်နိုင်သည်။ ဥပမာအားဖြင့်၊ သင်သည် `kubectl delete events,all --all` ကို အသုံးပြု၍ `all` တွင်ပါဝင်သော object kind အားလုံးနှင့်အတူ event များကို ဖျက်နိုင်သည်။

## 5.7 အနှစ်ချုပ်

ဤအခန်းတွင် သင်လေ့လာခဲ့သည်မှာ-

*   `Pod` များသည် `container` တစ်ခု သို့မဟုတ် တစ်ခုထက်ပို၍ အတူတကွရှိသော အုပ်စုအဖြစ် run သည်။ ၎င်းတို့သည် deployment နှင့် horizontal scaling ၏ unit ဖြစ်သည်။
*   ပုံမှန် `container` တစ်ခုသည် process တစ်ခုတည်းသာ run သည်။ Sidecar `container` များသည် `pod` ရှိ primary `container` ကို ဖြည့်စွက်ပေးသည်။
*   `Container` များသည် အတူတကွ run ရန် လိုအပ်မှသာ တူညီသော `pod` ၏ အစိတ်အပိုင်းဖြစ်သင့်သည်။ Frontend နှင့် backend process များသည် တစ်ခုချင်းစီ scale လုပ်နိုင်ရန် သီးခြား `pod` များတွင် run သင့်သည်။
*   `Pod` တစ်ခုစတင်သောအခါ၊ ၎င်း၏ `init` `container` များသည် တစ်ခုပြီးတစ်ခု run သည်။ နောက်ဆုံး `init` `container` ပြီးဆုံးသောအခါ၊ `pod` ၏ main `container` များကို စတင်သည်။ သင်သည် `init` `container` ကို `pod` ကို အတွင်းမှ configure လုပ်ရန်၊ ၎င်း၏ main `container` များ၏ startup ကို ကြိုတင်လိုအပ်ချက်တစ်ခု ပြည့်မီသည်အထိ နှောင့်နှေးရန်၊ သို့မဟုတ် `pod` စတင်တော့မည်ဖြစ်ကြောင်း external service တစ်ခုကို အသိပေးရန် အသုံးပြုနိုင်သည်။
*   `kubectl apply -f <file>` command သည် object များကို ဖန်တီးပြီး update လုပ်ရန် declarative နည်းလမ်းဖြစ်သည်။
*   `kubectl port-forward` command သည် သင်၏ local computer မှ `pod` တစ်ခုသို့ port forwarding ကို enable လုပ်သည်။
*   `kubectl logs` command သည် `container` တစ်ခု၏ log များကို ပြသသည်။
*   `kubectl cp` command သည် file များကို သင်၏ local computer နှင့် `container` တစ်ခုအကြား copy ကူးသည်။
*   `kubectl exec` command သည် `container` တွင် command တစ်ခုကို execute လုပ်သည်။
*   `kubectl attach` command သည် run နေသော `container` ရှိ main process ၏ standard stream များနှင့် ချိတ်ဆက်သည်။
*   `kubectl delete` command သည် object များကို ဖျက်ရန် အသုံးပြုသည်။ Controller (ဥပမာ `Deployment`) မှ စီမံခန့်ခွဲသော `pod` များကို ဖျက်ရန်၊ သင်သည် controller ကိုယ်တိုင်ကို ဖျက်ရမည်။