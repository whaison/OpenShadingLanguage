オープンシェーディング言語   Open Shading Language  のためのREADME
================================
ビルドステータス：

ビルドステータス

* 目次---------------------------------------------

* 導入----------------------------------
* OSLの違い
* OSLとは
* OSLの構築
* プロジェクトの現状とロードマップ
* 連絡先
* クレジット
* 導入
------------------------
* Open Shading Languageへようこそ！--------------------------------------------

オープンシェーディング言語（OSL）は、先進のレンダラやその他のアプリケーションでプログラム可能なシェーディングのための小さくても豊富な言語であり、マテリアル、ライト、ディスプレースメント、パターン生成についての説明に理想的です。

OSLは、フィーチャー・フィルムのアニメーションや視覚効果に使用される社内レンダラーで使用するためにSony Pictures Imageworksによって開発されました。言語仕様は、他の視覚効果やそれを使用したいアニメーションスタジオからの入力で開発されました。

OSLは堅牢で実証済みで、「Men in Black 3」、「The Amazing Spider-Man」、「Oz the Great and Powerful」、「Edge of Tomorrow」などの大型VFXフィルムでの作業のための専用シェーディングシステムでした」、「Hotel Transylvania」や「Meatballs 2のチャンス」などのアニメーション機能やその他の多くの映画が制作中または現在制作中です。

OSLコードは、クリエイティブコモンズの表示3.0 Unportedのライセンスの下でのドキュメント（（ディストリビューションに付属している「LICENSE」ファイルを参照してください）「新BSD」ライセンスの下で配布されてhttp://creativecommons.org/licenses/by/を3.0 /）。一言で言えば、無料でも商業的でも、公開されているか、所有権があるかにかかわらず、OSLコードとドキュメントを希望通りに変更するだけでなく、自分のアプリケーションでOSLを自由に使用することができます。ライセンス。

* OSLの違い---------------------------------------------------

OSLには、Cと同様の構文や他のシェーディング言語があります。ただし、高度なレンダリングアルゴリズム専用に設計されており、Radiance Closure、BSDF、遅延レイトレーシングなどの機能をファーストクラスの概念として備えています。

OSLには、他のシェーディング言語では見られないいくつかのユニークな特徴があります（必ずしもすべてではありません）。ここでは、他の言語と比較してOSLが異なるいくつかのものがあります：

* サーフェスシェイプとボリュームシェーダは、最終カラーではなく、輝度クロージャを計算します。-------------------------

OSLのサーフェスシェイプとボリュームシェーダは、サーフェスまたはボリュームが光を散乱させる方法の「クロージャ」と呼ばれる明示的なシンボリックな記述を計算します。これらの放射輝度閉鎖は、特定の方向で評価され、重要な方向を見つけ出すためにサンプリングされ、後の評価および再評価のために保存される。この新しいアプローチは、レイトレーシングとグローバルイルミネーションをサポートする物理ベースのレンダラに最適です。

対照的に、他のシェーディング言語は、通常、特定の方向から見える表面色のみを計算します。これらの古いシェーダは、レンダラがほとんど何もできないが、この1つの情報を見つけるために実行する「ブラックボックス」です（たとえば、サンプルから重要な方向を検出する効果的な方法はありません）。さらに、ライトとサーフェスの物理的な単位はしばしば指定されていないので、シェーダが物理的に正しい方法で動作することを確実にすることは非常に困難です。

* サーフェスシェイダーとボリュームシェーダーは、ライトやシュートレイの上をループしません。-----------------------------------

OSLサーフェスシェーダには、「ライトループ」や明示的にトレースされた照明光はありません。代わりに、サーフェスシェーダは、サーフェスがどのように光を散乱するかを記述する放射輝度クロージャを計算し、レンダラの一部は「積分器」と呼ばれ、特定の光源セットのクロージャを評価し、どの方向の光線をトレースするかを決定します。反射や屈折などの通常の明示的なレイトレーシングを必要とするエフェクトは、放射輝度のクロージャの一部であり、他のBSDFのように見えます。

このアプローチの利点には、統合とサンプリングを一括して行うか、またはレイ・コヒーレンスを高めるために並べ替えることが含まれます。BSDFを最適にサンプリングするために「レイバジェット」を割り当てることができます。クロージャは、双方向レイトレーシングまたはメトロポリスライトトランスポートに使用することができます。シェーダを再実行する必要なく、新しい照明でクロージャを迅速に再評価することができます。

サーフェスシェイダーとライトシェーダーは同じものです。

OSLには光源用のシェーダの種類はありません。ライトは単に発光型のサーフェスであり、すべてのライトはエリアライトです。

透明性はまさに別の種類の照明です。

シェーダで透明/不透明変数を明示的に設定する必要はありません。透明度は、ライトがサーフェスと相互作用するもう1つの方法であり、サーフェスシェーダによって計算されるメインの放射輝度クロージャに含まれます。

レンダラー出力（AOV）は、「ライトパス式」を使用して指定できます。

スペキュラ、ディフューズ、リフレクション、個別ライトなど個々のライティングコンポーネントを含むイメージを出力することが望ましい場合もあります。他の言語では、通常、これらの個々の数量を集めるシェーダに多数の「出力変数」を追加します。

OSLシェーダは、これを達成するためにコードや出力変数を混乱させる必要はありません。その代わりに、どのライトパスがどの出力に貢献すべきかを記述するための正規表現ベースの表記法があります。これはすべてレンダリング側で行われます（ただし、OSL実装でサポートされています）。新しい出力が必要な場合は、シェーダをまったく変更する必要はありません。レンダラーに新しいライトパス式を伝えるだけで済みます。

シェーダはネットワークに編成されています。

OSLシェーダはモノリシックではなく、シェーダのネットワーク（シェーダグループ、グラフ、またはDAGとも呼ばれます）に編成することができ、ネットワーク内の他のノードの名前付き入力にいくつかのノードの名前付き出力を接続します。これらの接続はレンダリング時に動的に実行され、個々のシェーダノードのコンパイルには影響しません。さらに、個々のノードは遅れて評価され、その出力がそれらに依存する後のノードから「プル」されたときに限ります（シェイダーライターはこれらの細部を幸せに知らないままであり、すべてが正常に評価されたかのようにシェーダーを書きます）。

グリッドのない任意のデリバティブまたは余分なシェーディングポイント。

OSLでは、シェーダ内の任意の計算量の導関数を取り、テクスチャ座標として任意の量を使用して、適切なフィルタリングを期待できます。これは、影付きの点が長方形のグリッドに配置されることを必要とせず、または特定の接続性を有すること、または「余分な点」が陰影付けされることを必要としない。これは、導関数が隣接点との有限差分によって計算されるのではなく、シェイダーライターの介入なしに導関数につながる変数の偏微分を計算する「自動微分」によって行われるためです。

OSLはレンダリング時に積極的に最適化する

OSLは、LLVMコンパイラフレームワークを使用してシェーダネットワークをマシンコードに変換します（ちょうど時間内、つまり「JIT」）、シェーダパラメータや他のランタイム値を完全に知っているシェーダとネットワークを大幅に最適化しますシェーダがソースコードからコンパイルされたときに知られています。その結果、私たちのOSLシェーディングネットワークは、Cで手作りされた同等のシェーダよりも25％高速に実行されています。（これは私たちの古いシェーダがレンダラで動作した方法です。）

OSLとは

OSLオープンソースの配布は、次のコンポーネントで構成されています。

oslcは、OSLソースコードをアセンブリ形式の中間コード（.osoファイルの形式）に変換するスタンドアロンコンパイラです。

liboslcは、OSLCompilerクラスを実装するライブラリであり、他のアプリケーションに埋め込む必要がある場合やコンパイラが別の実行可能ファイルになることを望まない場合は、シェーダコンパイラの勇気を含んでいます。

liboslqueryは、OSLQueryクラスを実装するライブラリで、コンパイルされたシェーダのパラメータ、型、およびそれらに関連付けられたメタデータの完全なリストを含む、コンパイル済みシェーダに関する情報をクエリできます。

oslinfoは、liboslqueryを使用してシェーダとそのパラメータに関するすべての関連情報をコンソールに出力するコマンドラインプログラムです。

liboslexecはShavedSystemクラスを実装するライブラリで、アプリケーション内でコンパイルされたシェーダを実行できるようにします。現在、LLVMを使用してShaderバイトコードをx86命令にコンパイルします。

testshadeは、ポイントの長方形の配列上にシェーダ（または接続されたシェーダネットワーク）を実行し、その出力を画像として保存するためのプログラムです。これにより、完全に機能するレンダラに統合することなく、シェーダ（およびシェーディングシステム）の検証が可能になり、ほとんどのテストスイート検証の基礎となります。テストレンダリングと並んで、テストシェードは、OSLライブラリを呼び出す方法の良い例です。

testrenderは、シェーディングにOSLを使用する小さなレイトレーシングレンダラです。機能は非常に少なく（現時点では球のみが許可されています）、パフォーマンスには注意を払っていませんが、OSLライブラリを作業レンダラに組み込む方法、レンダラがどのインタフェースを供給する必要があるのか​​、BSDF /放射輝度クロージャを評価し、統合しなければならない（複数の重要度サンプリングを含む）。

いくつかのサンプルシェーダー。

ドキュメント - OSL言語仕様（シェイダーライターには有益）で構成されていますが、将来はOSLライブラリをレンダラーに統合する方法についての詳細なドキュメントがあります。

OSLの構築

OSLソースコードを作成する手順については、OSLディストリビューションの "INSTALL"ファイルを参照してください。

プロジェクトの現状とロードマップ

Sony Pictures Imageworksでは、独自のレンダラーである「Arnold」で独占的にOSLを使用しています。シェーディングのためにOSLを使用した完成した作品には、

Men in Black 3
The Amazing Spider-Man
Hotel Transylvania
Oz the Great and Powerful
Smurfs 2
Cloudy With a Chance of Meatballs 2
Amazing Spider-Man 2
Edge of Tomorrow
Blended
22 Jump Street
Guardians of the Galaxy
The Interview
Fury
American Sniper
Pixels
Hotel Transylvania 2
Concussion
Alice in Wonderland: Through the Looking Glass
さらに多くは現在生産中です。私たちのシェーダライティングチームはOSLで全面的に動作し、すべてのプロダクションでOSLを使用しています。レンダラからすべてのコードを削除して、古いスタイルの "C"シェーダを書くことさえ可能にしました。古いシェーダ機能を削除した時点で、OSLシェーダは古いシステムの同等の古いコンパイル済みCシェーダよりも一貫して優れていました。

長期的には、言語とライブラリの2.xまたは3.xカットにつながることを期待している多くのプロジェクトがあります。私たちの長期目標のうち、

その他のドキュメント、特にレンダラに統合する際に使用するOSLライブラリのすべてのパブリックAPIを記述する「統合ガイド」現在、 "testrender"のソースコードは、OSLをレンダラーに統合する方法の最良の例です。

サンプルシェーダのセットはかなり貧弱です。最終的には、シェーダから呼び出すことのできる、実用的でプロダクション品質のシェーダとユーティリティ関数をさらに広範囲に設定します。

現在、「クロージャプリミティブ」はOSLライブラリまたはレンダラのC ++で実装されていますが、OSL自体に新しいクロージャプリミティブを実装できるようにする言語の将来の仕様が欲しいです。

同様に、インテグレータもレンダラに実装されていますが、OSL自体で新しいインテグレータを実装できるように、将来のOSLリリースが必要です。

私たちは、OSLシェーダー（およびシェーダーネットワーク）をGPUや他のエキゾチックなハードウェア（少なくともそのようなハードウェア上で表現できるOSLの最大サブセット）で実行できるコードに変換できる代替の「バックエンド」を実装したいと思います。 。たとえば、モデリングシステムや照明ツールのリアルタイムプレビューウィンドウで、OSLシェーダに近い近似を表示することができます。

私たち（ソニーピクチャーズイメージワークスのレンダラー開発チーム）は、おそらくすぐにこれらの作業を行うことはできません（実際には、すべての時間帯ですべてを行うことはできません）。しかし、私たちは、オープンソースプロジェクトとして、他のユーザーや開発者が、私たちが単独で行うことができるよりも、将来のOSL開発の道筋を探る手助けをしてくれることを願っています。

連絡先

OSL GitHubページ

OSL開発メールリストの閲覧または購読

主任建築家にEメールでお問い合わせください。

最新のOSL言語仕様のPDF

SPIのOSLホームページ

Sony Pictures Imageworksの主なオープンソースのページ

あなたが戻ってプロジェクトにコードを貢献したい場合は、ログインする必要があります寄稿ライセンス契約を。

クレジット

OSLのオリジナルのデザイナーとオープンソースの管理者はLarry Gritzです。

ラリー・グリッツ、クリフ・スタイン、クリス・クーラ、アレハンドロ・コンティ、ジェイ・レイノルズ、ソロモン・ブルース、アダム・マルティネス、ブレヒト・ヴァン・ロンメルのOSLの主人公/初期の開発者。

さらにSteve Agland、Shane Ambler、Martijn Berger、Nicholas Bishop、Matthaus G. Chajdas、Thomas Dinges、Henri Fousse、Fujita Fujita、Derek Haase、Sven-Hendrik Haase、その他多くの人が、機能、バグ修正などの小さな変更を加えました。ジョン・ハドン、ダニエル・ヘッケンベルク、ロナン・ケリーエル、マックス・リアーニ、バスティアン・モンターニュ、エリック・オーシャン、ミッコ・オータマア、アレックス・シュワーラー、セルゲイ・シャリビン、ステファン・シュタインバッハ、エステバン・トバリアーレ、アレクサンダー・フォン・クーンリング （アルファベット順に記載されていますが、誰かが出てしまった場合はお知らせください）

このプロジェクトの進行を支援し、心からサポートし、特にRob Bredow、Brian Keeney、Barbara Ford、Rene Limberger、Erik Straussなどのソースをリリースすることを許可したSony Pictures Imageworksのマネージャーには、十分な感謝の意を表すことはできません。

また、SPIのクラックシェーディングチームや、ショーでOSLを使用したいと思っている勇敢なlookdev TDやCGスタッフにも大変感謝しています。彼らは私たちのモルモット、インスピレーション、テスター、そしてフィードバックの素晴らしい源として役立った。ありがとう、私たちはあなたのニーズに応えてくれることを願っています。

OSLは孤立して開発されていませんでした。私たちは、言語仕様の初期の草案を辛抱強く読んで、非常に有益なフィードバックや追加のアイデアをくれた個人やスタジオに借金があります。（関係者やスタジオの許可を得た後、名前を挙げておきたい）

OSLの実装には、他のいくつかのオープンソースパッケージが組み込まれています。

OpenImageIO（c）Larry Gritz、et al

ブースト - さまざまな著者

IlmBase（c）インダストリアルライト＆マジック

LLVMコンパイラのインフラ

-----------------------------------------------------------------------------------------------------------------------------
README for Open Shading Language
================================

Build status:

[![Build Status](https://travis-ci.org/imageworks/OpenShadingLanguage.svg?branch=master)](https://travis-ci.org/imageworks/OpenShadingLanguage)

Table of contents
------------------

* [Introduction](#introduction)
* [How OSL is different](#how-osl-is-different)
* [What OSL consists of](#what-osl-consists-of)
* [Building OSL](#building-osl)
* [Current state of the project and road map](#current-state-of-the-project-and-road-map)
* [Contacts](#contacts)
* [Credits](#credits)


Introduction
------------

Welcome to Open Shading Language!

Open Shading Language (OSL) is a small but rich language for
programmable shading in advanced renderers and other applications, ideal
for describing materials, lights, displacement, and pattern generation.

OSL was developed by Sony Pictures Imageworks for use in its in-house
renderer used for feature film animation and visual effects. The
language specification was developed with input by other visual effects
and animation studios who also wish to use it.

OSL is robust and production-proven, and was the exclusive shading
system for work on big VFX films such as "Men in Black 3", "The Amazing
Spider-Man," "Oz the Great and Powerful," and "Edge of Tomorrow," as
well as animated features such as "Hotel Transylvania" and "Cloudy With
a Chance of Meatballs 2", and many other films completed or currently in
production.

The OSL code is distributed under the "New BSD" license (see the
"LICENSE" file that comes with the distribution), and the documentation
under the Creative Commons Attribution 3.0 Unported License
(http://creativecommons.org/licenses/by/3.0/).  In short, you are free
to use OSL in your own applications, whether they are free or
commercial, open or proprietary, as well as to modify the OSL code and
documentation as you desire, provided that you retain the original
copyright notices as described in the license.



How OSL is different
--------------------

OSL has syntax similar to C, as well as other shading languages.
However, it is specifically designed for advanced rendering algorithms
and has features such as radiance closures, BSDFs, and deferred ray
tracing as first-class concepts.

OSL has several unique characteristics not found in other shading
languages (certainly not all together).  Here are some things you will
find are different in OSL compared to other languages:

* Surface and volume shaders compute radiance closures, not final colors.

  OSL's surface and volume shaders compute an explicit symbolic
  description, called a "closure", of the way a surface or volume
  scatters light, in units of radiance.  These radiance closures may be
  evaluated in particular directions, sampled to find important
  directions, or saved for later evaluation and re-evaluation.
  This new approach is ideal for a physically-based renderer that
  supports ray tracing and global illumination.

  In contrast, other shading languages usually compute just a surface
  color as visible from a particular direction.  These old shaders are
  "black boxes" that a renderer can do little with but execute to find
  this one piece of information (for example, there is no effective way
  to discover from them which directions are important to sample).
  Furthermore, the physical units of lights and surfaces are often
  underspecified, making it very difficult to ensure that shaders are
  behaving in a physically correct manner.

* Surface and volume shaders do not loop over lights or shoot rays.

  There are no "light loops" or explicitly traced illumination rays in
  OSL surface shaders.  Instead, surface shaders compute a radiance
  closure describing how the surface scatters light, and a part of the
  renderer called an "integrator" evaluates the closures for a
  particular set of light sources and determines in which directions
  rays should be traced.  Effects that would ordinarily require explicit
  ray tracing, such as reflection and refraction, are simply part of the
  radiance closure and look like any other BSDF.

  Advantages of this approach include that integration and sampling may
  be batched or re-ordered to increase ray coherence; a "ray budget" can
  be allocated to optimally sample the BSDF; the closures may be used by
  for bidirectional ray tracing or Metropolis light transport; and the
  closures may be rapidly re-evaluated with new lighting without having
  to re-run the shaders.

* Surface and light shaders are the same thing.

  OSL does not have a separate kind of shader for light sources.  Lights
  are simply surfaces that are emissive, and all lights are area lights.

* Transparency is just another kind of illumination.

  You don't need to explicitly set transparency/opacity variables in the
  shader.  Transparency is just another way for light to interact with a
  surface, and is included in the main radiance closure computed by a
  surface shader.

* Renderer outputs (AOV's) may be specified using "light path expressions."

  Sometimes it is desirable to output images containing individual
  lighting components such as specular, diffuse, reflection, individual
  lights, etc.  In other languages, this is usually accomplished by
  adding a plethora of "output variables" to the shaders that collect
  these individual quantities.

  OSL shaders need not be cluttered with any code or output variables to
  accomplish this.  Instead, there is a regular-expression-based
  notation for describing which light paths should contribute to which
  outputs.  This is all done on the renderer side (though supported by
  the OSL implementation).  If you desire a new output, there is no need
  to modify the shaders at all; you only need to tell the renderer the
  new light path expression.

* Shaders are organized into networks.

  OSL shaders are not monolithic, but rather can be organized into
  networks of shaders (sometimes called a shader group, graph, or DAG),
  with named outputs of some nodes being connected to named inputs of
  other nodes within the network.  These connections may be done
  dynamically at render time, and do not affect compilation of
  individual shader nodes.  Furthermore, the individual nodes are
  evaluated lazily, only when their outputs are "pulled" from the later
  nodes that depend on them (shader writers may remain blissfully
  unaware of these details, and write shaders as if everything is
  evaluated normally).

* Arbitrary derivatives without grids or extra shading points.

  In OSL, you can take derivatives of any computed quantity in a shader,
  and use arbitrary quantities as texture coordinates and expect correct
  filtering.  This does not require that shaded points be arranged in a
  rectangular grid, or have any particular connectivity, or that any
  "extra points" be shaded.  This is because derivatives are not
  computed by finite differences with neighboring points, but rather by
  "automatic differentiation", computing partial differentials for the
  variables that lead to derivatives, without any intervention required
  by the shader writer.

* OSL optimizes aggressively at render time

  OSL uses the LLVM compiler framework to translate shader networks into
  machine code on the fly (just in time, or "JIT"), and in the process
  heavily optimizes shaders and networks with full knowledge of the
  shader parameters and other runtime values that could not have been
  known when the shaders were compiled from source code.  As a result,
  we are seeing our OSL shading networks execute 25% faster than the
  equivalent shaders hand-crafted in C!  (That's how our old shaders
  worked in our renderer.)



What OSL consists of
--------------------

The OSL open source distribution consists of the following components:

* oslc, a standalone compiler that translates OSL source code into
  an assembly-like intermediate code (in the form of .oso files).

* liboslc, a library that implements the OSLCompiler class, which
  contains the guts of the shader compiler, in case anybody needs to
  embed it into other applications and does not desire for the compiler
  to be a separate executable.

* liboslquery, a library that implements the OSLQuery class, which
  allows applications to query information about compiled shaders,
  including a full list of its parameters, their types, and any metadata
  associated with them.

* oslinfo, a command-line program that uses liboslquery to print to the
  console all the relevant information about a shader and its parameters.

* liboslexec, a library that implements the ShadingSystem class, which
  allows compiled shaders to be executed within an application.
  Currently, it uses LLVM to JIT compile the shader bytecode to x86
  instructions.

* testshade, a program that lets you execute a shader (or connected
  shader network) on a rectangular array of points, and save any of its
  outputs as images.  This allows for verification of shaders (and the
  shading system) without needing to be integrated into a fully
  functional renderer, and is the basis for most of our testsuite
  verification.  Along with testrender, testshade is a good example
  of how to call the OSL libraries.

* testrender, a tiny ray-tracing renderer that uses OSL for shading.
  Features are very minimal (only spheres are permitted at this time)
  and there has been no attention to performance, but it demonstrates how
  the OSL libraries may be integrated into a working renderer, what
  interfaces the renderer needs to supply, and how the BSDFs/radiance
  closures should be evaluated and integrated (including with multiple
  importance sampling).

* A few sample shaders.

* Documentation -- at this point consisting of the OSL language
  specification (useful for shader writers), but in the future will have
  detailed documentation about how to integrate the OSL libraries into
  renderers.



Building OSL
------------

Please see the "INSTALL" file in the OSL distribution for instructions
for building the OSL source code.



Current state of the project and road map
-----------------------------------------

At Sony Pictures Imageworks, we are exclusively using OSL in our
proprietary renderer, "Arnold." Completed productions that used OSL for
shading have included:

    Men in Black 3
    The Amazing Spider-Man
    Hotel Transylvania
    Oz the Great and Powerful
    Smurfs 2
    Cloudy With a Chance of Meatballs 2
    Amazing Spider-Man 2
    Edge of Tomorrow
    Blended
    22 Jump Street
    Guardians of the Galaxy
    The Interview
    Fury
    American Sniper
    Pixels
    Hotel Transylvania 2
    Concussion
    Alice in Wonderland: Through the Looking Glass

And more are currently in production. Our shader-writing team works
entirely in OSL, all productions use OSL, and we've even removed all the
code from the renderer that allows people to write the old-style "C"
shaders.  At the time we removed the old shader facility, the OSL
shaders were consistently outperforming their equivalent old compiled C
shaders in the old system.

In the longer term, there are a number of projects we hope to get to
leading to a 2.x or 3.x cut of the language and library.  Among our
long-term goals:

* More documentation, in particular the "Integration Guide" that
  documents all the public APIs of the OSL libraries that you use when
  integrating into a renderer.  Currently, the source code to
  "testrender" is the best/only example of how to integrate OSL into a
  renderer.

* Our set of sample shaders is quite anemic.  We will eventually have a
  more extensive set of useful, production-quality shaders and utility
  functions you can call from your shaders.

* Currently "closure primitives" are implemented in C++ in the OSL
  library or in the renderer, but we would like a future spec of the
  language to allow new closure primitives to be implemented in OSL
  itself.

* Similarly, integrators are now implemented in the renderer, but we
  want a future OSL release to allow new integrators to be implemented
  in OSL itself.

* We would like to implement alternate "back ends" that would allow
  translation of OSL shaders (and shader networks) into code that can
  run on GPUs or other exotic hardware (at least for the biggest subset
  of OSL that can be expressed on such hardware).  This would, for
  example, allow you to view close approximations to your OSL shaders in
  realtime preview windows in a modeling system or lighting tool.

We (the renderer development team at Sony Pictures Imageworks) probably
can't do these all right away (in fact, probably can't do ALL of them in
any time range).  But we hope that as an open source project, other
users and developers will step up to help us explore more future
development avenues for OSL than we would be able to do alone.



Contacts
--------

[OSL GitHub page](https://github.com/imageworks/OpenShadingLanguage)

[Read or subscribe to the OSL development mail list](http://groups.google.com/group/osl-dev)

Email the lead architect:  lg AT imageworks DOT com

[Most recent PDF of the OSL language specification](https://github.com/imageworks/OpenShadingLanguage/blob/master/src/doc/osl-languagespec.pdf
)

[OSL home page at SPI](http://opensource.imageworks.com/?p=osl)

[Sony Pictures Imageworks main open source page](http://opensource.imageworks.com)

If you want to contribute code back to the project, you'll need to
sign [a Contributor License Agreement](http://opensource.imageworks.com/cla/).


Credits
-------

The original designer and open source administrator of OSL is Larry Gritz.

The main/early developers of OSL are (in order of joining the project):
Larry Gritz, Cliff Stein, Chris Kulla, Alejandro Conty, Jay Reynolds,
Solomon Boulos, Adam Martinez, Brecht Van Lommel.

Additionally, many others have contributed features, bug fixes, and other
small changes: Steve Agland, Shane Ambler, Martijn Berger, Nicholas Bishop,
Matthaus G. Chajdas, Thomas Dinges, Henri Fousse, Syoyo Fujita, Derek Haase,
Sven-Hendrik Haase, John Haddon, Daniel Heckenberg, Ronan Keryell, Max
Liani, Bastien Montagne, Erich Ocean, Mikko Ohtamaa, Alex Schworer, Sergey
Sharybin, Stephan Steinbach, Esteban Tovagliari, Alexander von Knorring.
(Listed alphabetically; if we've left anybody out, please let us know.)

We cannot possibly express sufficient gratitude to the managers at Sony
Pictures Imageworks who allowed this project to proceed, supported it
wholeheartedly, and permitted us to release the source, especially Rob
Bredow, Brian Keeney, Barbara Ford, Rene Limberger, and Erik Strauss.

Huge thanks also go to the crack shading team at SPI, and the brave
lookdev TDs and CG supes willing to use OSL on their shows.  They served
as our guinea pigs, inspiration, testers, and a fantastic source of
feedback.  Thank you, and we hope we've been responsive to your needs.

OSL was not developed in isolation.  We owe a debt to the individuals
and studios who patiently read early drafts of the language
specification and gave us very helpful feedback and additional ideas.
(I hope to mention them by name after we get permission of the people
and studios involved.)

The OSL implementation incorporates or depends upon several other open
source packages:

[OpenImageIO (c) Larry Gritz, et al](http://www.openimageio.org)

[Boost - various authors](http://www.boost.org)

[IlmBase (c) Industrial Light & Magic](http://www.openexr.com)

[LLVM Compiler Infrastructure](http://llvm.org)


