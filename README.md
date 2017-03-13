# Haskellのグラフ描画ライブラリChartの導入

at [Haskell.hs もくもく会 ディープラーニング #07](https://remotehs.connpass.com/event/51656/)

## 目標

最低限のHaskellデータ処理環境を作る

- グラフ描画ライブラリ: [Chart](https://github.com/timbod7/haskell-chart/wiki)
    - 基本としては、2つの系統がある
        - [Chart-diagrams](https://hackage.haskell.org/package/Chart-diagrams)
            - cairoに依存しない
            - SVGとPSファイルのみ
        - [Chart-cairo](https://hackage.haskell.org/package/Chart-cairo)
            - cairoに依存
            - 出力はPNG, PS, SVG, PDF
        - 参考: [How to Use Backend (Chart)](https://github.com/timbod7/haskell-chart/wiki/How-to-use-backends)
    - GUIウインドウ(GTK+)に出す場合
        - [Chart-gtk](https://hackage.haskell.org/package/Chart-gtk)
            - Chart-cairoベース
            - cairoとGTK+（バージョン2.x系）に依存
- 今回やらない（参考までに・次回やりたい）
    -  [hmatrix](https://github.com/albertoruiz/hmatrix)
        - ベクトル・行列のデータ型と演算
        - PythonでいうNumPy的なやつ
        - チュートリアル: <http://dis.um.es/~alberto/hmatrix/hmatrix.html>


## 必要なもの

以下、macOSを前提にします。

- Stack
    - `$ brew install haskell-stack`
- GTK+
    - `brew install gtk+`
    - 注意
        - GTK+はバージョン2.x系と3.x系がある
        - Chartには2.x系が必要
        - Homebrewには `gtk+3` (3.x系) パッケージもあるので注意

## インストール手順

### Stackプロジェクトを作ってセットアップする

```
$ stack new (任意のプロジェクト名) simple
$ stack setup
```

- `simple`テンプレートを使用（デフォルトのものは面倒なので）

### Stackの設定を編集する

#### `stack.yaml`

[サンプル](https://github.com/sky-y/hs-chart-tutorial/blob/master/stack.yaml)も参照してください。

```
resolver: lts-8.2
```

- こだわりなければ、そのままを推奨
- ここを変更したい（新しいバージョンにしたい）場合は、他のライブラリのバージョンにも注意すること

```
extra-deps:
- gtk-0.14.6
- Chart-gtk-1.8.2
```

```
flags:
  gtk:
    have-quartz-gtk: true
```

- `have-quartz-gtk`: Mac (Quartz) に対応させるためのフラグ

#### `(プロジェクト名).cabal`

[サンプル](https://github.com/sky-y/hs-chart-tutorial/blob/master/chart-tutorial.cabal)も参照

`executable`セクション内:

```
  hs-source-dirs:      src
  main-is:             Main.hs
  build-depends:       base
                     , cairo
                     , Chart
                     , Chart-diagrams
                     , Chart-cairo
                     , Chart-gtk
  default-language:    Haskell2010
  build-depends:       base >= 4.7 && < 5
```

- Stackのデフォルトテンプレートを使う場合は、 `library` セクションに `build-depends` を追記する

### gtk2hs-buildtoolsをインストール

gtk2hs-buildtools: HaskellでGTK+系ライブラリのビルドに必要なツール群。

- `~/.local/bin`にPATHを通す（`.bashrc`などに）
    - ここに`stack install`がコマンドをインストールするので
- `$ stack install gtk2hs-buildtools`

### ソースを書く

- src/Main.hs

```haskell
import Graphics.Rendering.Chart.Easy
import Graphics.Rendering.Chart.Gtk

signal :: [Double] -> [(Double,Double)]
signal xs = [ (x,(sin (x*3.14159/45) + 1) / 2 * (sin (x*3.14159/5))) | x <- xs ]

main :: IO ()
main = toWindow 400 400 $ do
  layout_title .= "Amplitude Modulation"
  setColors [opaque blue, opaque red]
  plot (line "am" [signal [0,(0.5)..400]])
  plot (points "am points" (signal [0,7..400]))
```

### ビルドする

```
$ stack build
```

### 実行する

```
$ stack exec 
```

- こっそりウインドウ(App)が起動しているはず（分かりにくいけど）

## 参考

- [kayhide/deep-learning-from-scratch](https://github.com/kayhide/deep-learning-from-scratch)
- [kayhide/chart-test](https://github.com/kayhide/chart-test)
