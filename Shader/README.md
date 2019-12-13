Unity Engine Challenge by mixi Shader チュートリアル
===========

これは Unity Engine Challenge by mixi のShaderチュートリアルです。
Shaderの基礎的な知識や実装方法について、例題を元に理解深めていってください。

## 頂点シェーダ / フラグメントシェーダとは
シェーダとは色、ライティング、頂点などのレンダリング命令をプログラマブルに記述することで、高度なグラフィック表現を描画処理できるプログラミング言語です。

シェーダ言語の中でも柔軟性の高くモデルの頂点や色をいじることができるのが頂点シェーダ(Vertex Shader)とフラグメントシェーダ(Fragment Shader)です。

頂点シェーダでは、メッシュの頂点に対して操作を行う命令を実装することができます。メッシュを変形させることで波紋などの表現ができたりします。

また、フラグメントシェーダでは、ピクセルのカラー値に対して操作を行う命令を実装することができます。グラデーションや特定のピクセルの色を変えたりできます。


## UV座標とは
UV座標とは、テクスチャ上の座標を表します。

左下が原点(0,0)として、0~1の座標系で表されます。

<img width="200" alt="スクリーンショット 2019-12-03 10 58 32" src="https://user-images.githubusercontent.com/30618540/70321036-947bde80-1869-11ea-948c-38d318e39479.png">

シェーダ内では、テクスチャのどの座標を扱うかという情報となるため、

頂点シェーダ内で頂点情報と一緒にUV座標を指定することで、フラグメントシェーダでそのピクセルのカラー値にアクセスすることができます。




## 例題01：RGB値を変更して色を変える

シーン上に表示されているQuadの色を変えるようなShaderを実装してください。


### 進め方
- `Assets/Scenes/Tutorial01` を開いてください。
- `Assets/Shaders/Tutorial01` を開いてください。
- 頂点シェーダ `frag` に実装を追加してください。

### 実装について
`frag`関数の返り値は各ピクセルのカラー値です。
`tex2D`関数はモデルのテクスチャ情報とUV座標情報をを渡すことでピクセルの色計算して返します。
```
float4 c = tex2D(_MainTex, i.uv);
```
`float4`というのは4要素をもつ型です。
各要素へは`r`,`g`,`b`,`a`もしくは`x`,`y`,`z`,`w`としてアクセスできます。`c.rgb`など組み合わせて扱うこともできます。


## 例題02：UV座標を使ってグラデーションをさせる

シーン上に表示されているQuadにグラデーションをかけるようなShaderを実装してください。
なお、グラデーションの実装にはUV座標を使用してください。

### 進め方
- `Assets/Scenes/Tutorial02` を開いてください。
- `Assets/Shaders/Tutorial02` を開いてください。
- 頂点シェーダ `frag` に実装を追加してください。

### 実装について
モデルの色を変える方法は例題01で実装したとおりです。

グラデーションかける方法は色々ありますが、今回はUV座標を使った実装をしてください。

モデルのUV座標は`frag(v2f i)`の引数`i`から`i.uv`として得られます。

`v2f`とはshaderファイル内で定義されている構造体です。
```
struct v2f
{
    float4 vertex : SV_POSITION;
    float2 uv : TEXCOORD0;
};
```
`float2`は、`x`,`y`の2要素を持つ型です。`float4`と同様に`i.uv.x`のようにアクセスできます。


## 例題03：頂点座標を変更して波を作る

シーン上に表示されているPlaneの頂点座標を変更して波Shaderを実装してください。


### 進め方
- `Assets/Scenes/Tutorial03` を開いてください。
- `Assets/Shaders/Tutorial03` を開いてください。
- 頂点シェーダ `vert` に実装を追加してください。

### 実装について
`vert`関数の返り値は各ピクセルの頂点情報です。

引数で渡される`appdata v`はUnityから受け取るモデルの頂点情報で、それを`UnityObjectToClipPos(v.vertex)`でスクリーン上に描画する座標を計算して頂点情報を作成しています。

また、波を実装するには常時値が変わるような変数が必要ですが、組み込み変数として、時間`_Time`を使用できます。

上述の`UnityObjectToClipPos(float3 pos)`や`_Time`はUnityが用意している組み込みの関数や変数になります。
- https://docs.unity3d.com/2019.2/Documentation/Manual/SL-BuiltinFunctions.html
- https://docs.unity3d.com/2019.2/Documentation/Manual/SL-UnityShaderVariables.html


## 例題04：実機ビルド
実際の問題では、実機ビルドをして解いてもらう問題があります。

そのため、実機ビルドの手順について確認しておいてください。

実機ビルドするのは例題01, 02, 03のどれでもよいです。

**手順**
- `File -> BuildSetting`を選択。
- iOS or Androidを選択して`Switch Platform`。
    - iOSの場合は`Run In as Xcode`をRelease → Debugに変えるとビルド時間が短くできます。
- `Add Open Scene`を押して、確認したいSceneを`Scene In Build`に追加。
- 実機を自身のPCに接続。
    - 接続許可を聞くダイアログが出た場合は、「はい」を選択。
- `Build and Run`を選択。


## 補足：関数について
下記のような関数が組み込みで用意されてるため、実装のヒントに役立ててください。

**fmod(x, y)**
- `x / y`の余剰を返す

**floor(x)**
- `x`の少数を切り捨てて返す

**step(x, y)**
- `x`と`y`の値を比較して、`x>=y`なら1、`x<y`なら0を返す

**saturate(x)**
- `x`を`0 <= x <= 1`の範囲に留めて返す

**clamp(x, a, b)**
- `x`を`a <= x <= b`の範囲に留めて返す

**abs(x)**
- `x`の絶対値を返す

**sin(x)**, **cos(x)**
- `sin`, `cos`の値を返す

これ以外にも様々な関数があるので、調べてより知識を深めてみてください！
- 参考：https://developer.download.nvidia.com/CgTutorial/cg_tutorial_appendix_e.html