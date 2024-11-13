---
title: "UE5.3.2+Xcode16でプロジェクトが起動できなくなった時の対処法"
emoji: "🚰"
type: "tech"
topics:
- "xcode"
- "macos"
- "ue5"
  published: true
  published_at: "2024-10-22 20:14"
---

24/10/22
macOS15にアップデート後、xcodeを16に更新してからUE5でプロジェクトが起動できなくなったので、それを対処した時のメモ

結論としては、↓以下のようなエラーが出たときは、xcode15.◯に切り替える(元に戻す)と治る

---
### 起きたこと
unreal engineで新規のプロジェクトをC++で作成しようとした時、以下のようなエラーwindowが表示された。

> Setting up bundled DotNet SDK
/Users/{ユーザー}/Library/Unreal Engine/UE_5.3/Engine/Build/BatchFiles/Mac/../../../Binaries/ThirdParty/DotNet/6.0.302/mac-arm64
Running dotnet Engine/Binaries/DotNET/UnrealBuildTool/UnrealBuildTool.dll Development Mac -Project=/{path}/MyProject2/MyProject2.uproject -TargetType=Editor -Progress -NoEngineChanges -NoHotReloadFromIDE
Log file: /Users/{ユーザー}/Library/Application Support/Epic/UnrealBuildTool/Log.txt
Creating makefile for MyProject2Editor (no existing makefile)
Total execution time: 1.15 seconds
Platform Mac is not a valid platform to build. Check that the SDK is installed properly.

その時点では`could not be compiled. Try rebuilding from source manually.`の時のように
環境を変えたり、C++のコードミスからのコンパイルエラーだったりかな?と考えていたが、とりあえず提示されているlog.txtを見てみることに...
ターミナルで以下のファイルを開いた
```
vi /Users/{ユーザー}/Library/Application\ Support/Epic/UnrealBuildTool/Log.txt
```
以下はその内容

> Deleting old log file: /Users/{ユーザー}/Library/Application Support/Epic/UnrealBuildTool/Log-backup-2024.10.22-07.47.30.txt
Skipping /Users/{ユーザー}/Library/Unreal Engine/UE_5.3/Engine/Intermediate/Build/BuildRules/UE5Rules.dll: File is installed
Skipping /Users/{ユーザー}/Library/Unreal Engine/UE_5.3/Engine/Intermediate/Build/BuildRules/UE5ProgramRules.dll: File is installed
Creating makefile for MyProject2Editor (no existing makefile)
Total execution time: 1.15 seconds
Platform Mac is not a valid platform to build. Check that the SDK is installed properly.
BuildException: Platform Mac is not a valid platform to build. Check that the SDK is installed properly.
at UnrealBuildTool.UEBuildTarget.Create(TargetDescriptor Descriptor, Boolean bSkipRulesCompile, Boolean bForceRulesCompile, Boolean bUsePrecompiled, UnrealIntermediateEnvironment IntermediateEnvironment, ILogger Logger) in /Users/build/Build/++UE5/Sync/Engine/Saved/CsTools/Engine/Source/Programs/UnrealBuildTool/Configuration/UEBuildTarget.cs:line 1251
at UnrealBuildTool.BuildMode.CreateMakefileAsync(BuildConfiguration BuildConfiguration, TargetDescriptor TargetDescriptor, ISourceFileWorkingSet WorkingSet, ILogger Logger) in /Users/build/Build/++UE5/Sync/Engine/Saved/CsTools/Engine/Source/Programs/UnrealBuildTool/Modes/BuildMode.cs:line 1114
at UnrealBuildTool.BuildMode.BuildAsync(List`1 TargetDescriptors, BuildConfiguration BuildConfiguration, ISourceFileWorkingSet WorkingSet, BuildOptions Options, FileReference WriteOutdatedActionsFile, ILogger Logger, Boolean bSkipPreBuildTargets) in /Users/build/Build/++UE5/Sync/Engine/Saved/CsTools/Engine/Source/Programs/UnrealBuildTool/Modes/BuildMode.cs:line 396
at UnrealBuildTool.BuildMode.ExecuteAsync(CommandLineArguments Arguments, ILogger Logger) in /Users/build/Build/++UE5/Sync/Engine/Saved/CsTools/Engine/Source/Programs/UnrealBuildTool/Modes/BuildMode.cs:line 252
at UnrealBuildTool.UnrealBuildTool.Main(String[] ArgumentsArray) in /Users/build/Build/++UE5/Sync/Engine/Saved/CsTools/Engine/Source/Programs/UnrealBuildTool/UnrealBuildTool.cs:line 659
WriteFileIfChanged() wrote 0 changed files of 0 requested writes.
...

というような感じ(一部抜粋)
よく分からん！！

元々動いていたこともあり、直近で変わった環境で思い当たったのが
macOS15への更新
それに伴ったxcode16へのアップデート
この2つ
最初はxcodeをアップデートした時にうまくリンクできてないのかなと思い、
```
xcode-select -s /Application/Xcode_16.app
```
だったり、
```
xcode-select --install
```
```
xcodebuild -license accept
```
などを一通り試してみたが上手くいかず、試しに**アップデート前のxcode15.4をアーカイブからダウンロードして、切り替えてみよう**ということに。

#### xcode15のダウンロード
以下リンクから旧版はダウンロードできた
https://developer.apple.com/download/applications/


![](https://storage.googleapis.com/zenn-user-upload/fb42c59a01b3-20241022.png)

zipファイルがダウンロードされ解凍する

ここであることに気づく

![](https://storage.googleapis.com/zenn-user-upload/d55a4dfa24b3-20241022.png =300x)

使えなくね？

開こうとしても
![](https://storage.googleapis.com/zenn-user-upload/3d5a5ff265f5-20241022.png =250x)
> “Xcode_15”を使用するには、最新のバージョンにアップデートする必要があります。

というようなメッセージと共に`app storeで検索`ボタンが...(いつもの)

macOSをアップデートしてしまったので古いバージョンは使えない
当たり前。

#### 対処法

対処法はないか探しているとこんな記事が

https://zenn.dev/akagisrapid/articles/22212a1e65821c

今回のmacOS15(Sequoia)の一つ前の話だが、**xcodeは`appフォルダ/Contents/MacOS/Xcode`を直接開けば実行できる**っとのこと
今回も同じくできそうだと思い、実行してみると...

```
open /Applications/Xcode_15.app/Contents/MacOS/Xcode
```
スクリプトが実行されて
![](https://storage.googleapis.com/zenn-user-upload/a26f3cacee7e-20241022.png =400x)

なんと開いた

![](https://storage.googleapis.com/zenn-user-upload/faabf4721e22-20241022.png =400x)

うおおおおおおお
非推奨でも開く手段があったのは今回初知りでした


その後無事`xcode-select`で切り替えも成功しました

![](https://storage.googleapis.com/zenn-user-upload/262bfccd76e1-20241022.png =400x)


やっとのことで、再度！UEでプロジェクトを作成してみると、
![](https://storage.googleapis.com/zenn-user-upload/26e783ad2744-20241022.png)

開いた！！！！

---

#### これで無事解決

ちなみにブループリントで作成した時は、今回のエラーは発生しない
が、メニューから新規C++クラスを追加した時は発生した。
結局コンパイルはどちらで作成しても出来ないらしい

そもそもエラーログでxcodeのサポート周りのこと教えて欲しかった...

下手にmacOSはあげるものじゃないですね。

というか、以前のxcodeが開けるならosのバージョン上げやすい？
いつかこの機能無くなった時が困りますが...

危うくmacOSダウングレード(一旦初期化)するところでした
