# 開発環境
* PC-9801F　(MS-DOS 1.25が正常に動作するエミュレータがないので実機です)
* PC-9801 MS-DOS 1.25 (w/ MASM 1.03 & LINK 1.08)

# MS-DOS GitHubの説明

https://github.com/microsoft/MS-DOS

binとsourceの2つがありますが、binフォルダはIBMBIO.COMとIBMDOS.COMが欠落したIBM PC DOS 1.10のバイナリと全く同じであり、<b>ソースコードとは何の関係もなく、ユーティリティもIBMによるもので他のMS-DOSに付属しているユーティリティとは異なります。</b>

sourceには以下のファイルが含まれます

## Seattle Computer Products社のソースコード

ビルドにはASM.EXEとHEX2BIN.EXEの2つが必要です。(私のレポジトリにコンパイル済のバイナリがあります)

* ASM.ASM - SCP 8086 Assembler

```
ASM ASM
HEX2BIN ASM.HEX
RENAME ASM.BIN ASM.COM
```

* HEX2BIN.ASM - HEX2BIN (ASM.EXEから吐かれた.HEXをバイナリに変換するプログラム)

```
ASM HEX2BIN
HEX2BIN HEX2BIN.HEX
RENAME HEX2BIN.BIN HEX2BIN.COM
```

* IO.ASM - IO.SYS。Tarbell, CromemcoのFDCについて対応している (IO.ASM内のスイッチを変える必要あり)

```
ASM IO
HEX2BIN IO.HEX
RENAME IO.BIN IO.COM
```

* TRANS.ASM - Z80 to 8086 Translator

```
ASM TRANS
HEX2BIN TRANS.HEX
RENAME TRANS.BIN TRANS.COM
```

IO.SYS, TRANS.COMについては動作確認をしていません。

## Microsoft社のソースコード

ビルドにはMASM, LINK, EXE2BINの3つが必要です。

* COMMAND.ASM - COMMAND.COM インタプリンタ

ソース内のHIGHMEMスイッチが有効になっていますが、レポジトリでは無効にしています。
また、ソースコード内で”ZERO"が重複しているので、被らないように調整する必要があります。

```
MASM COMMAND
LINK COMMAND
EXE2BIN COMMAND.EXE COMMAND.COM
```

レポジトリではPC-9801用MS-DOS 1.25に付属のMASM 1.03を用いてコンパイルしたバイナリを置いています。
元のCOMMAND.COMと生成したCOMMAND.COMとでバイナリを比較したところ、同一ファイル判定がでました。
（PC-9801用MS-DOS 1.25は日本語非対応）

* MSDOS.ASM, STDDOS.ASM - MSDOS.SYS

```
MASM STDDOS STDDOS
LINK STDDOS
EXE2BIN STDDOS.EXE MSDOS.SYS
```

MSDOS.ASMはSTDDOS.ASM内で呼び出されているのでMASMでオブジェクトに吐き出すのはSTDDOS.ASMのみです。

特に改変する必要はありません。

PC-9801用MS-DOS 1.25のMSDOS.SYSはバージョンメッセージを出力する部分がNOPである事以外はバイナリが一致しています

ただし、更に後(182E以降)もバイナリが続いており、合計8192バイトなのでこのソースコードは欠落しているか、製品版に使用されなかった古いバージョンである可能性があります。

PC-9801では一応起動します。

![image](https://raw.githubusercontent.com/sanguisorba/DOS125_REBUILD/main/SRC/screenshot.png)

（リビルドしたMSDOS.SYSを入れるとIO.SYSから出力されたメッセージと被る）

バイナリが一致するか確認したかったのでMASM 1.03とかいう化石でコンパイルしましたが、それ以降のバージョンでも正常に動作すると思います。

# 足りないもの

MSDOS.SYSとCOMMAND.COMだけなので全てが足りないのですが、確認しているものだと

* FORMAT.OBJとリンクさせるためのOEMモジュールのスケルトンコード (いわゆる OEMFOR.ASM)
* IO.SYSのスケルトンコード
* FORMAT.OBJ
* INIT.ASM (FORMAT.OBJとリンクさせるためのモジュール。SCP社によるもの)

FORMATとIOSYSのスケルトンコードについては
”CUSTOMIZING MS-DOS version 1.23 and later”　（OEMキットのマニュアル）
で検索すれば何か出てくるかもしれません。2バージョンあるみたいで片方にはIOSYSのスケルトンコードがありません。

FORMAT.OBJはSCP社の製品版のMS-DOS 1.25に収録されています。SCP社のIO.ASMが含まれているので、SCP社のMS-DOSのソースコードとして考えると、FORMATのOEMモジュール INIT.ASMもレポジトリにはありません。INIT.ASMも製品版MS-DOSに収録されています。

それでもユーティリティのソースコードが何一つないのでMS-DOS 1.25についてはコンパイルしないで「読み物」程度にとどめておくのが良いと思います。

* CHKDSK
* DEBUG
* EDLIN
* EXE2BIN
* FILCOM
* (FORMAT)
* (SYS)

