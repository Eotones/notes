# speechSynthesis強制使用Chrome中的Google小姐中文語音

網路上的`window.speechSynthesis`教學主要都只有說切換指定語言

像是這樣就能切換成中文語音:

```javascript
var synth = window.speechSynthesis;
var u = new SpeechSynthesisUtterance();
u.lang = 'zh-TW';
u.text = "你要讀出的中文內容";
synth.speak(u);
```
但是這種寫法在系統有支援多種語音的情況下只會選擇系統預設語音

* win7繁中 - 無支援中文語音
* win7簡中 - 有支援微軟中文語音(系統預設)
* win10 - 簡繁中都有支援微軟中文語音(系統預設)

如果想要切換成其他語音(例如Chrome才有的Google中文語音)

就要另外再寫

```javascript
var synth = window.speechSynthesis;
var u = new SpeechSynthesisUtterance();
//u.lang = 'zh-TW'; //這句絕對不要用
u.text = "你要讀出的中文內容";

// 用 synth.getVoices() 取得支援的語音列表
// 要注意網頁開啟後首次執行 window.speechSynthesis 後才會把語音功能載入
// 大概要花幾秒鐘的時間才能完全載入,再來才能正確取得語音列表
// 所以要想辦法延遲 synth.getVoices(); 的執行時間
// 
// 或是使用下面的事件偵測
// (這個事件在載入過程會被觸發多次,很難用,他是偵測changed不是偵測完全載入完成,
// 但這是window.speechSynthesis裡唯一的一個事件偵測,沒其他選擇)
// var voices;
// window.speechSynthesis.onvoiceschanged = function() {
//   voices = synth.getVoices();
//   // 注意 speak() 不要直接寫在這裡,不然會被重覆觸發多次
// };
var voices = synth.getVoices();

for(let index = 0; index < voices.length; index++) {
  /*
  "Google US English"
  "Google 日本語"
  "Google 普通话（中国大陆）"
  "Google 粤語（香港）"
  "Google 國語（臺灣）"
  */

  //console.log(this.voices[index].name);
  if(voices[index].name == "Google 國語（臺灣）"){       
    //u.lang = 'zh-TW'; //這句絕對不要用
    //要使用Google中文語音的話請不要再用u.lang指定語言
    //不然可能又會被切回系統預設的中文語音
    u.voice = voices[index];
    break;
  }else{
    //如果沒有則使用預設中文語音
    u.lang = 'zh-TW';
  }
}

synth.speak(u);
```

## 參考資料

**window.speechSynthesis**是瀏覽器內原生的語音功能,詳細用法可以看以下MDN的教學文件

* window.speechSynthesis
  * [SpeechSynthesis - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis)
* SpeechSynthesisUtterance
  * [SpeechSynthesisUtterance - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance)
* window.speechSynthesis.onvoiceschanged
  * Stack Overflow上關於`window.speechSynthesis.onvoiceschanged`的[討論](https://stackoverflow.com/questions/21513706/getting-the-list-of-voices-in-speechsynthesis-of-chrome-web-speech-api?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)
* 列出瀏覽器有支援哪些語音和測試語音的小工具
  * [Speech synthesiser](https://mdn.github.io/web-speech-api/speak-easy-synthesis/)


## 範例

**tts.js:**
```javascript
class tts_zhtw {
  constructor(){
    console.log("tts constructor");
    window.speechSynthesis.cancel(); //強制中斷之前的語音
    this.synth = window.speechSynthesis;
    
    this.u = null;
    this.u = new SpeechSynthesisUtterance();

    if (localStorage.getItem("ls_rate") === null) {
      this.u.rate = 1.2; // 語速 0.1~10
    }else{
      this.u.rate = Number(localStorage.getItem("ls_rate"));
    }

    if (localStorage.getItem("ls_volume") === null) {
      this.u.volume = 0.5; //音量 0~1
    }else{
      this.u.volume = Number(localStorage.getItem("ls_volume"));
    }

    if (localStorage.getItem("ls_pitch") === null) {
      this.u.pitch = 1; //語調 0.1~2
    }else{
      this.u.pitch = Number(localStorage.getItem("ls_pitch"));
    }


    this.u.onend = function(event){
      //console.log(event);
      console.log("tts.onend");
    };

    this.u.onerror = function(event){
      //console.log(event);
      console.log("tts.onerror", event);
      this.cancel2();
    };
  }

  speak2(textToSpeak){
    //console.log(document.getElementById("ttsCheck").checked == true ? "[語音開啟]" : "[語音關閉]");

    this.voices = this.synth.getVoices();
    for(let index = 0; index < this.voices.length; index++) {
      /*
      "Google US English"
      "Google 日本語"
      "Google 普通话（中国大陆）"
      "Google 粤語（香港）"
      "Google 國語（臺灣）"
      */

      //console.log(this.voices[index].name);
      if(this.voices[index].name == "Google 國語（臺灣）"){
        //console.log("Y");
        //this.u.lang = 'zh-TW';
        this.u.voice = this.voices[index];
        break;
      }else{
        //console.log("N");
        this.u.lang = 'zh-TW';
      }
    }

    //console.log("test");

    this.u.text = this._textFilter(textToSpeak);

    this.u.onstart = function(event){
      //console.log(event);
      console.log("tts.onstart", textToSpeak);
    };

    if( (this.u.text.length > 0) || (this.u.text != null) ){
      this.synth.speak(this.u);
    }

    return this;
  }

  cancel2(){
    console.log("tts cancel");
    window.speechSynthesis.cancel();
  }

  volume(volume_val){
    let volume = Number(volume_val);
    if(volume >= 0 && volume <= 1){
      tts.u.volume = volume;
      localStorage.setItem("ls_volume", volume);
      console.log(`音量調整為: ${tts.u.volume}`);
    }else{
      console.log(`超出範圍`);
    }
  }
  rate(rate_val){
    let rate = Number(rate_val);
    if(rate >= 0.5 && rate <= 2){
      tts.u.rate = rate;
      localStorage.setItem("ls_rate", rate);
      console.log(`語速調整為: ${tts.u.rate}`);
    }else{
      console.log(`超出範圍`);
    }
  }
  pitch(pitch_val){
    let pitch = Number(pitch_val);
    if(pitch >= 0.1 && pitch <= 2){
      tts.u.pitch = pitch;
      localStorage.setItem("ls_pitch", pitch);
      console.log(`語調調整為: ${tts.u.pitch}`);
    }else{
      console.log(`超出範圍`);
    }
  }
  reset(){
    //localStorage.clear();
    localStorage.removeItem("ls_volume");
    localStorage.removeItem("ls_rate");
    localStorage.removeItem("ls_pitch");
  }

  _textFilter(msg){
    msg = msg.replace(/^(1){4,}$/g, "一一一");
    msg = msg.replace(/^(2){4,}$/g, "二二二");
    msg = msg.replace(/^(3){4,}$/g, "三三三");
    msg = msg.replace(/^(4){4,}$/g, "四四四");
    msg = msg.replace(/^(5){4,}$/g, "五五五");
    msg = msg.replace(/^(6){4,}$/g, "六六六");
    msg = msg.replace(/^(7){4,}$/g, "七七七");
    msg = msg.replace(/^(8){4,}$/g, "八八八");
    msg = msg.replace(/^(9){4,}$/g, "九九九");

    msg = msg.replace(/^(w){4,}$/gi, "哇拉");
    msg = msg.replace(/^(~){3,}$/g, "~~~");
    msg = msg.replace(/^(\.){3,}$/g, "...");

    msg = msg.replace(/^484$/gi, "四八四");
    msg = msg.replace(/^87$/g, "八七");
    msg = msg.replace(/^94$/g, "九四");
    msg = msg.replace(/^9487$/g, "九四八七");

    //過濾emoji(最多3個,超過就刪除)
    msg = msg.replace(/(\ud83d[\ude00-\ude4f]){4,}/g, "");

    return msg;
  }
}


// example

var tts = new tts_zhtw();
tts.speak2("中文語音測試");
tts.speak2("第二句");
tts.speak2("第三句");

// or
tts.speak2("中文語音測試").speak2("第二句").speak2("第三句");
```