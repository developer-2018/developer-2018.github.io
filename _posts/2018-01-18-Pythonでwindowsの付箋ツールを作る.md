---
layout: post
title: Pythonで簡単にwindows付箋みたいなツールを作る
tags: Python
---

#### 前提
現場によって開発環境が変わることがあるのと、  
Windows Vistaなどでデスクトップガジェットとして提供されていた頃の付箋ツールが  
あれば良いのにな・・・と思いましたので、  
勉強がてら、Python+tkinterで、シンプルな付箋アプリを作成しました。  

#### 環境
Windows7 pro  
Python3.6(Anaconda 3.6)  
tkinter    
  
#### イメージ
![201801160920](../../file/201801180000.jpg)
  
  
#### 要点
ココらへんの機能があればOKとしています。  
- シャットダウン時に保存し、起動時に前回内容が再表示される  
- 最低限の機能（SAVE & EXIT)の実装  
- 起動時にタスクバーにアイコンが表示されないこと  
  
#### ZIP
[![fusen.zip](/icon/zip.png)](https://github.com/developer-2018/developer-2018.github.io/files/1633682/fusen.zip)

for windowsです。  
解凍したexeをドライブに配置し、スタートアップに.exeのショートカットをおいてください。  
  

#### 開発手順
今回は新規の環境構築から始めています。  
まるっきり新しく勉強される方には読んで頂けるかもしれません。  

pythonがインストールされていれば手順2.から、  
Tkinterがインストールされていれば手順3.から始めて下さい。  

#### 1. 環境構築 Pythonのインストール
Anacondaをインストールします。  
Python言語での人工知能などのライブラリや統合環境が入っているパッケージなので、色々と便利との事です。  
[Anaconda で Python 環境をインストールする](https://qiita.com/t2y/items/2a3eb58103e85d8064b6)  
  
#### 2. 環境構築 Visual Studio Code のインストール
ビルド＆デバッグはこのエディタで行っています。  
ビルドの為に拡張機能でPythonをインストールします。  
[Visual Studio CodeでPython開発環境を整える](https://qiita.com/84zume/items/27d143f529396c9fa1cc)  
  
#### 3. 環境構築 tkinter　のインストール
tkinterは、PythonでGUIを組むことのできるツールキットです。  
ウィンドウや、入力ボックスなどが簡単に作成できるものです。  
インストール自体はAnacondaに格納されているので、コマンドプロンプトで確認しましょう。  
```
python -m tkinter
```  
コマンドを入力してポップアップが出ればＯＫです。  
![201801161345](../../file/201801180001.gif)

#### 4. ソース
文字コード１１：UTF-8  
  
Tkinterは、ウィンドウの配置や、テキストボックスの配置を使用しています。  
これらをwidgetと呼びますが、具体的な使い方については以下のサイトが参考になります。  
[Widget を配置しよう](http://www.shido.info/py/tkinter2.html)

```ruby:fusen.py

#! /usr/bin/env python

import tkinter as tk
import tkinter.ttk as ttk
import configparser

#read config
config = configparser.ConfigParser()
config.read('./fusen.conf')
win_width=config.get('user', 'win_width')
win_height=config.get('user', 'win_height')

#画面
#ウィンド ウィジェットを生成
root = tk.Tk()
root.minsize(win_width, win_height)
root.geometry('-10+10')
root.resizable(True,True)
root.columnconfigure(0, weight=1)
root.rowconfigure(0, weight=1)
root.overrideredirect(True) #ウィンドウのフレーム除去

#テキストを生成
text_widget = tk.Text(root,bg='#ffc0f0',bd=0,state=tk.NORMAL,padx=0,pady=0,selectborderwidth=0)
text_widget.grid(column=0, row=0, sticky=(tk.N, tk.S, tk.E, tk.W))

file_value = open('./fusen.txt').read()
text_widget.insert('1.0',file_value) #read

def on_closing(): #save $ close
    do_output_file()
    root.destroy()

def do_output_file(): #save
    text = text_widget.get("1.0",'end-1c')
    f = open('./fusen.txt', 'w') # 書き込みモードで開く
    f.write(text) # 引数の文字列をファイルに書き込む
    f.close() # ファイルを閉じる

#popupウィンドウを作る。
menu_top = tk.Menu(root,tearoff=False)
menu_top.add_command(label='SAVE(S)',underline=5,command=do_output_file)
menu_top.add_command(label='EXIT(E)',underline=5,command=on_closing)

def showPopup(event):
    menu_top.post(event.x_root,event.y_root)

#入力待ち
root.bind('<Button-3>',showPopup)
root.protocol("WM_DELETE_WINDOW", on_closing)
root.protocol("WM_SAVE_YOURSELF", on_closing)
root.mainloop()

```


#### fusen.txt
空ファイルで良いです。  
シャットダウン時やSAVE(S)押下時に、テキストボックスの内容がここに保存されます。  
ソースと同じ階層に置いてください。  
  
  
設定ファイルです。ウィンドウのサイズなどを持っています。  
Tkinterではフレームなしの設定にすると枠が可変にできないようなので、外出ししています。  
[teratailでQA中です。](https://teratail.com/questions/108647)  
解決策がありましたら是非ご教授お願いします！  

```fusen.conf  ```
```[user]  ```
```#window width```
```win_width = 350```
```#window height```
```win_height = 450```


ソースと同じ階層に置いてください。  
  

#### 5. pipを使って.exeファイルを作る
pipはPythonに初期から付属されているパッケージ管理システムです。  
ソースをexeに変換するパッケージを導入する為に、インストールの確認をします。  
コマンドプロンプトで下記コマンドを実行してみてください。  
```
python -m pip -V
```
次のようにpipのバージョンが表示されればOKです。  
```
pip 8.1.2 from Cpython27libsite-packages (python 3.6)
```
  
確認できればexeファイルに変換していきましょう。  
今回はPyInstallerを導入しました。  

参考にしたのは以下のＵＲＬです。
[bookmarkPython スクリプトを exe ファイル化したい](http://edosha.hatenablog.jp/entry/2017/05/11/121204)

以下のコマンドを入力してください。

```
pip install pyinstaller
```

ソースがあるフォルダにcdコマンドでフォルダを移動して・・・

```
cd C:\python
```

以下コマンドで対象ソース（./fusen.py）を変換します。

```
pyinstaller -F -w ./fusen.py
```

以下のように表示されたらＯＫです。

```
…41293 INFO: Appending archive to EXE C:\python\dist\fusen.exe41433 INFO: Building EXE from out00-EXE.toc completed successfully.
```

#### エラーになる場合  
実際に実行し、詰まったりした場合は以下を確認してみてください。  
- フォルダパス、ファイル名は正しいか？(windowsであればdirコマンド、@cdコマンドで確認してください。)  
- utf-8 以外の文字コードが混じっていないか？BOM有UTF-8で保存していないか？  
[コンパイルエラー　\65279は不正な文字です。」](http://monokurotamago.hatenablog.com/entry/2014/03/01/151724)
  
  
#### 6. exeをショートカットに入れる
あとは作成した.exe,.txt,.confをCドライブ配下の適当なフォルダに置き、  
.exeのショートカットをスタートアップに置くだけです。


#### まとめ
Python初心者なので、まあ動いてほしい物は作れたので良いでしょうか。  
Tkinterは便利なインターフェースが作れそうなので、色々と作ってみたいです。

  
  
