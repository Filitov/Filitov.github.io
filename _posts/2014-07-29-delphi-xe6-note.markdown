---
layout: post
title: "2014/07/29-30 行動ＡＰＰ開發技術研習會筆記"
date: 2014-07-29 09:03:59 +0800
categories: APP
tags: [DELPHI,APP,Android,IOS,OSX]
---
# 行動ＡＰＰ開發技術研習會筆記
2014/07/29～30到台中參加中臺科大跟捷康科技辦理，李維老師主講的廣度研習的筆記。

<!-- more -->

# 第一天

## 前置作業

### 下載安裝Delphi XE6
參考[CodeData][CodeData_delphixe6]上的資料：

1. 到代理商網站註冊。
1. 下載Delphi XE6。
可以找到兩個，若要使用C++ Builder，用第一個；若只要使用Delphi，就用第二個就好，空間還可以省點。
    1. Embarcadero RAD Studio XE6 Architect - 30 day trial
    1. Delphi XE6 Architect - 30 day trial
1. 安裝，下一步下一步下一步，搞定。

[CodeData_delphixe6]: http://www.codedata.com.tw/mobile/delphi-app-dev-tutorial-1st-class-getting-started/


###設定測試環境
雖然可以用模擬器測試，可是，好像直接連接手機比較快，所以...
*應該是要先更新函式庫？*

## Delphi簡介

1. Native
1. Scripted
1. VM

## 練習：拍照，呈現在APP上

File -> New -> FireMonkey Mobile Application -> Blank Application

Project Manager 的 Target Platforms 可以更改目標平台（包含Windows應用程式），
編譯執行時會跟隨平台進行。

* TToolBar
    * TButton
        1. Align: left
        1. StyleLookup: cameratoolbutton
        1. Action: Create New Standatd Action -> TakePhotoFromCameraAction
          ->（點擊產生） TakePhotoFromCameraAction1
* TImage
    1. Name: Image1
    1. Align: Client
* TActionList
    * (自動產生的)TakePhotoFromCameraAction1
        1. OnDidFinishTaking: (點擊產生)TakePhotoFromCameraAction1DidFinishTaking

``` delphi
procedure TForm1.TakePhotoFromCameraAction1DidFinishTaking(Image: TBitmap);
begin
  Image1.Bitmap.Assign(Image);
end;
```

## 練習：拍照，分享

承上

* TToolBar (原)
    * TButton
        1. Align: right
        1. StyleLookup: actiontoolbutton
        1. Action: Create New Standatd Action -> ShowShareSheetAction
          ->（點擊產生）ShowShareSheetAction1
* TActionList
    * (自動產生的) ShowShareSheetAction1
        1. OnBeforeExecute: (點擊產生)ShowShareSheetAction1BeforeExecute

```delphi
procedure TForm1.ShowShareSheetAction1BeforeExecute(Sender: TObject);
begin
  ShowShareSheetAction1.Bitmap.Assign(Image1.Bitmap);
end;
```

## 設定應用程式

Project Manager -> (Project name) -> 右鍵 -> Option

*圖示*
Application -> Launcher icons
原為``$(BDS)\bin\Artwork\Android\FM_LauncherIcon_144x144.png``
其中``$(BDS)``即``C:\Program Files (x86)\Embarcadero\Studio\14.0``
依所需大小設定路徑

*程式顯示名稱*
Version Info -> label
原來是 ``$(ModuleName)`` 改成所要的中英文名稱

## 練習：嵌入瀏覽器

* TToolBar
    * TEdit
        1. Align: left
        1. 在元件上按右鍵，Add Item -> TSearchEditButton 產生 SearchEditButton1
        * SearchEditButton1
            1. OnClick: (點擊產生)SearchEditButton1Click
* TWebBrowser
    1. Align: Client

``` delphi
procedure TForm1.SearchEditButton1Click(Sender: TObject);
begin
  WebBrowser1.URL := Edit1.Text;
  WebBrowser1.Navigate;
end;
```

## FireMonkey UI設計

1. 使用者體驗 user experience
1. IOS: 標題粗體橫列
1. checkbox and radio button
1. grouping
1. switch button and rotate select
1. 風格一致
1. 字體大小，文字數量
1. 側邊滑入不填滿，預留間隔
1. tab在下面或上面
1. tab數量4~5個，超過就用點點點

TTabControl, TListBox, TListView

TGestureManger
Touch -> Gesture Manager

## 練習：TabControl

* TGestureManager
    1. Name: Gesture Manager1
* TTabControl
    1. TabPosition: Top (可選 Bottom 或 Dots)
    1. 右鍵 -> Add TTabItem
        * TTabItem
        * TTabItem
        * TTabItem
        * TTabItem
        * TTabItem
    1. Touch -> GestureManager: Gesture Manager1
    1. Touch -> Gestures -> Standard: 勾 Left , Right
    1. OnGesture: TabControl1Gesture

Note: TabInde是*0*based, 最後一個TabIndex是TabCount-1

```delphi
procedure TForm1.TabControl1Gesture(Sender: TObject;
  const EventInfo: TGestureEventInfo; var Handled: Boolean);
begin
  //label1.Text := TabControl1.TabCount.ToString() + ',' + TabControl1.TabIndex.ToString();
  if EventInfo.GestureID = sgiRight then
  begin
    if TabControl1.TabIndex < TabControl1.TabCount-1 then
    begin
      TabControl1.TabIndex := TabControl1.TabIndex + 1;
    end;
  end
  else
    if EventInfo.GestureID = sgiLeft then
    begin
     if TabControl1.TabIndex > 0 then
      begin
        TabControl1.TabIndex := TabControl1.TabIndex - 1;
      end;
    end;
    //label2.Text := TabControl1.TabCount.ToString() + ',' + TabControl1.TabIndex.ToString();
end;
```

## 練習： TListBox

* TListBox
    1. 右鍵 -> Items Editors
    可以新增 TListBoxItem 或 TListBoxGroupHeader 或 TListBoxGroupFooter
        * TListBoxGroupHeader
            1. Text: some data...
        * TListBoxItem
            1. Text: some data...
            1. ItemData -> Text: 同 Text
            1. ItemData -> Bitmap: 指定圖片
            1. ItemData -> Accessory: 可設定成aMore表示可再展開, aDetail表示訊息
            1. ItemData -> Detail: 右側詳細描述，搭配 StyleLookup 為 listboxitemrightdetail
            1. StyleLookup: 預設為listboxitemnodetail
        * TListBoxItem
        * TListBoxItem
        * TListBoxGroupHeader
        * TListBoxItem

## 風格檔案

風格檔案存放在``C:\Users\Public\Documents\Embarcadero\Studio\14.0\Styles``，``.Style``給行動裝置用，``.vsf``給windows應用程式用。

* TStyleBook
    1. FileName: 風格檔名稱及路徑
    2. Name: StyleBook1
* TForm
    1. StyleBook: StyleBook1


## 練習： VCL Forms Application 風格

File -> New -> VCL Forms Application

Project Manager -> (Project name) -> 右鍵 -> Option
-> Application -> Appearance: 勾選所要的風格檔

Project Manager -> (Project name) -> Target Platforms -> 右鍵 -> Add Platform:
新增Win64應用程式


## 練習： TListBox

``` delphi
procedure TForm1.Button1Click(Sender: TObject);
var
  lbi: TListBoxItem;
begin
  lbi := TListBoxItem.Create(ListBox1);
  lbi.Text := 'ttt';
  ListBox1.AddObject(lbi);
end;
```

## TListView

*TListBox*
適合少量、好寫
*TListView*
效率、適合大量

## 練習：TListView

* TListView
    1. Name: ListView1
    1. ItemAppearance -> ItemAppearance: ListItemRightDetail

``` delphi
procedure TForm1.Button1Click(Sender: TObject);
var
  lvi: TListViewItem;
begin
  lvi := ListView1.Items.Add;
  lvi.Text := 'aaa';
  lvi.Detail := 'something about aaa';

  lvi := ListView1.Items.Add;
  lvi.Text := 'bbb';
  lvi.Detail := 'something about bbb';

  lvi := ListView1.Items.Add;
  lvi.Text := 'ccc';
  lvi.Detail := 'something about ccc';

end;
```

## 練習：TListView，用PrototopeBinding餵假資料測試

* TPrototypeBindSource
    1. 右鍵 -> Fields Editor (或雙擊) -> 新增測試欄位
* TListView
    1. Name: ListView1
    1. ItemAppearance -> ItemAppearance: 視欄位需要選擇適合的

View -> LiveBinding Designer 拖曳欄位對應ListView欄位，應該就能順利呈現

若未呈現的話，可在LiveBinding Designer按右鍵 -> Refresh Designer 試試

要開啟 TListView 編輯模式：

``` delphi
ListView1.EditMode := true;
```

檢查編輯模式下使用者勾選與否。

``` delphi
var
  i: Integer;
  lvi: TListViewItem;
begin
  for i:=0 Tto ListView1.Items.Count-1 do
  begin
    lvi = ListView1.Items[i];
    if lvi.Checked then
    begin
      //Memo1.Lines.Add( lvi.Text );
    end;
  end;
end;
```

----

#第二天

##FireMonkey UI 設計

*座標系統*
左上角 (0,0)
中心點 RotationCenter

* Position
    * X
    * Y
* RotationCenter
    * X
    * Y
* RotationAngle
* Scale
    * X
    * Y
* Align

##TFlowAnimation

*TFlowAnimation*物件可以針對特定屬性進行動畫（自動遞遷）

屬性指Object Inspector中有膠卷圖示的屬性，可以從那兒直接Create New FlowAnimation。
或從Tool Palette中加入，並設定其中PropertyName。

容器可用*ClipChild*裁切掉超過範圍的部分

##資料存取

Azure、Amazon、Parse、Kinvey

|檔案|TFile
|XML|TXMLDocument
|JSON|Data.DBXJSON


* TXMLDocument
    * DOMVendor: ADOM XML v4

遠端HTTP可用 TIdHTTP

```delphi
data := IdHTTP1.Get('網址');
```

當成bStream取回，然後送入TXMLDocument處理，並告知為UTF-8

```delphi
IdHTTP1.Get('http://www.taipei.gov.tw/public/ogdi/blob/hotel.xml', bsData);
XMLDocument1.LoadFromStream(bsData, TXMLEncodingType.xetUTF_8);
```

其他亂記

```delphi
URLString := Format('https://maps.google.com/maps?q=%s,%s',
    [ItemNode.ChildValues[HOTELLATITUDE], ItemNode.ChildValues[HOTELLONGITUDE]]);
WebBrowser1.Navigate(URLString);
TabControl1.ActiveTab := TabItem2;
```


* 移動設備只適合少量資料
* 一定要處理大量資料，分段存取（可搭配DataSnap）

###LiveBinging

適合200筆以下

##資料存取

Data Explorer -> SQLite database -> （右鍵）Add New Connection -> 連線名稱 -> (右鍵)Modify

  >如果變數名稱取成中文，記得在pas的Code編輯器中，右鍵改File Format為UTF8

* Database: 路徑檔名
* StringFormat: Unicode

由Data Explorer直接拖曳table到form上

*TFDConnection
    1. Connected: true
*TFDQuery
    1. Active: true
*TListView
*TFDGUIxWaitCursor

用LiveBindings Designer連接 ListView 的 Item.Text 跟欄位

##部署資料庫檔案

承上

android Sandbox

* .\
* res
* classes
* library
* .\assets\internal\

Project -> Deployment

*AndroidManifest.xml* 該APP的設定與權力等
*classes.dex* 針對android優化的java程式碼
*Remote Path* 部署到手機上的路徑

新增資料庫檔案到Deployment，並指定remote

ConntectionDefName 清掉
點擊Connection物件，右鍵啟動ConnectionEditor

* Driver ID: SQLite
* StringFormat: unicode

指定Connection物件的BeforeConnection

``` delphi
uses System.IOUtils;

procedure TForm1.CoursedemoConnectionBeforeConnect(Sender: TObject);
var
  dbPath: String;
begin
  dbPath := String.Empty;
  CoursedemoConnection.Params.Values['Database'] := String.Empty;

  {$IFDEF MSWINDOWS}
    dbPath := 'C:\\DelphiPrac\\07 Database1\\MobileTraining.sqlite';
  {$ENDIF}
  {$IF DEFINED(IOS) or DEFINED(ANDROID) }
    //dbPath := TPath.GetDocumentsPath+PathDelim+'MobileTraining.sqlite';
    dbPath := TPath.Combine(TPath.GetDocumentsPath, 'MobileTraining.sqlite');

  {$ENDIF}

  if FileExists(dbPath) then
    CoursedemoConnection.Params.Values['Database'] := dbPath
  else
    ShowMessage('檔案不存在'+dbPath);

end;

procedure TForm1.FormActivate(Sender: TObject);
begin
  CoursedemoConnection.Connected := true;
  TbldivisionsTable.Active := true;
end;
```

*System.IOUtils.TPath.GetDocumentsPath*

```
Windows XP  C:\Documents and Settings\<username>\My Documents
Windows Vista or later  C:\Users\<username>\Documents
Mac OS X  /Users/<username>/Documents
iOS Device  /var/mobile/Applications/<application ID>/Documents
iOS Simulator  /Users/<username>/Library/Application Support/iPhone Simulator/<SDK version>/Applications/<application ID>/
Android  /data/data/<application ID>/files
```

##DataSnap

### JSON/REST/HTTP

J送 J送 J送

System.JSON
Data.DBXJSON

Delphi對JSON的每個元素有一對一對應，例如TJSONObject,TJSONPair,TJSONArray,TJSONNumber,TJSONString等等


##REST


``` delphi
  object RESTClient1: TRESTClient
    BaseURL = 'http://opendata.dot.taipei.gov.tw/opendata/gwjs_cityhall.json'
  object RESTRequest1: TRESTRequest
    Client = RESTClient1
    Response = RESTResponse1
  object RESTResponseDataSetAdapter1: TRESTResponseDataSetAdapter
    Active = True
    Dataset = FDMemTable1
    ResponseJSON = RESTResponse1
    RootElement = 'retVal'
  object RESTResponse1: TRESTResponse
  object FDMemTable1: TFDMemTable
    Active = True
```

http://www.dot.taipei.gov.tw/ct.asp?xItem=3167481&CtNode=44829&mp=117001#2



