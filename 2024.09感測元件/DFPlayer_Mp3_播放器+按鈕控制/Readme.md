## DFPlayer Mini Mp3 腳位圖
>![](https://github.com/derricktsai0904/Arduino/blob/master/07.DFPlayer%20Mini%20Mp3/DFPlayerMiniMp3.jpg?raw=true)

## Arduino 電路圖
>![](https://github.com/derricktsai0904/Course/blob/main/2024.09%E6%84%9F%E6%B8%AC%E5%85%83%E4%BB%B6/DFPlayer_Mp3_%E6%92%AD%E6%94%BE%E5%99%A8%2B%E6%8C%89%E9%88%95%E6%8E%A7%E5%88%B6/DFPlayerMp3Button.jpg?raw=true)


## 接線方式

| DFPlayer Mini| Arduino板 |
| ------- | ----------- |
|VCC | 5V |
|GND | GND |
|RX | Pin 10 (加1K電阻) |
|TX | Pin 11 |
|SPK1,SPK2 | 分別接喇叭單體的兩條線，沒有序順限制|

## 注意事項
管理程式庫下載以下檔案，請注意測試結果 (1.1.1版本 播放語音才會正常)
<br>
DFPlayer Mini Mp3 By Makuna 
<br>
>![](https://github.com/derricktsai0904/Course/blob/main/2024.09%E6%84%9F%E6%B8%AC%E5%85%83%E4%BB%B6/DFPlayer_Mp3_%E6%92%AD%E6%94%BE%E5%99%A8/DFPlayer.jpg?raw=true)
<br>

## 程式說明
[以下程式來源 DFPlayerMp3.ino ]:[[https://github.com/derricktsai0904/Arduino/blob/master/06.ESP32%E6%8E%A7%E5%88%B6/04.ESP32-CAM%2BUltrasonic/ESP32-CAM-Ultrasonic-Line_LED.ino](https://github.com/derricktsai0904/Arduino/blob/master/07.DFPlayer%20Mini%20Mp3/DFPlayerMP3.ino) "DFPlayerMp3.ino"
[以下程式來源 DFPlayerMp3.ino ]

``` arduino
#include <DFMiniMp3.h>  
//使用軟體Serial
#include <SoftwareSerial.h>  
SoftwareSerial mySerial(11,10); // RX, TX

const int sw = 3000; // 定義播放雨音的長度 3 秒

// 宣告 notify class
//
class Mp3Notify; 

// 定義類型，其中第一個參數為決定用硬體Serial或軟體Serail
//
typedef DFMiniMp3<SoftwareSerial, Mp3Notify> DfMp3;  //如果用UNO板就用軟體Serail
//typedef DFMiniMp3<HardwareSerial, Mp3Notify> DfMp3;  //如果用Mega板就用硬體Serail

// 建立mp3物件
//
DfMp3 dfmp3(mySerial);

// 實作notification的類別，可以在不同的事件中，寫入想要進行的動作
// 若沒有特別要進行的處理，這裡不用修改
class Mp3Notify
{
public:
  static void PrintlnSourceAction(DfMp3_PlaySources source, const char* action)
  {
    if (source & DfMp3_PlaySources_Sd) 
    {
        Serial.print("SD Card, ");
    }
    if (source & DfMp3_PlaySources_Usb) 
    {
        Serial.print("USB Disk, ");
    }
    if (source & DfMp3_PlaySources_Flash) 
    {
        Serial.print("Flash, ");
    }
    Serial.println(action);
  }
  static void OnError([[maybe_unused]] DfMp3& mp3, uint16_t errorCode)
  {
    // 錯誤訊息
    Serial.println();
    Serial.print("Com Error ");
    Serial.println(errorCode);
  }
  static void OnPlayFinished([[maybe_unused]] DfMp3& mp3, [[maybe_unused]] DfMp3_PlaySources source, uint16_t track)
  {
    Serial.print("Play finished for #");
    Serial.println(track);  
  }
  static void OnPlaySourceOnline([[maybe_unused]] DfMp3& mp3, DfMp3_PlaySources source)
  {
    PrintlnSourceAction(source, "online");
  }
  static void OnPlaySourceInserted([[maybe_unused]] DfMp3& mp3, DfMp3_PlaySources source)
  {
    PrintlnSourceAction(source, "inserted");
  }
  static void OnPlaySourceRemoved([[maybe_unused]] DfMp3& mp3, DfMp3_PlaySources source)
  {
    PrintlnSourceAction(source, "removed");
  }
};

void setup() 
{
  Serial.begin(9600); //啟用監控視窗
  Serial.println("initializing...");
  
  dfmp3.begin();  //開始使用DFPlayer模組

  //重置DFPlayer模組，會聽到"波"一聲
  dfmp3.reset();

  //音量控制，0~30
  //uint16_t volume = dfmp3.getVolume();
  //Serial.print("volume ");
  //Serial.println(volume);
  dfmp3.setVolume(28);
  
  //volume = dfmp3.getVolume();
  //Serial.print("volume ");
  //Serial.println(volume);
  
  //取得所有MP3檔的總數
  //uint16_t count = dfmp3.getTotalTrackCount(DfMp3_PlaySource_Sd);
  //Serial.print("files ");
  //Serial.println(count);

  Serial.println("Play Mp3 Voice...");

  // 播放MP3，參數就是第N首。如要播第一首MP3，參數就是1，第二首就是2
  pinMode(6,INPUT);
  pinMode(7,INPUT);
  pinMode(8,INPUT);
  pinMode(9,INPUT);
}

void loop() 
{
  int bt1 = digitalRead(6);
  int bt2 = digitalRead(7);
  int bt3 = digitalRead(8);
  int bt4 = digitalRead(9);

  Serial.print("bt1 = ");
  Serial.print(bt1);
  Serial.print("bt2 = ");
  Serial.print(bt2);
  Serial.print("bt3 = ");
  Serial.print(bt3);
  Serial.print("bt4 = ");
  Serial.println(bt4);

  delay(100);

  if(bt1==1){
     dfmp3.playMp3FolderTrack(1);
     delay(sw);
  }
  else if(bt2==1){
     dfmp3.playMp3FolderTrack(2);
     delay(sw);
  }
  else if(bt3==1){
     dfmp3.playMp3FolderTrack(3);
     delay(sw);
  }
  else if(bt4==1){
     dfmp3.playMp3FolderTrack(4);
     delay(sw);
  }
}
```


