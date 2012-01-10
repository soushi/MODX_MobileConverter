--------------------------------------------
MobileConverter for MODX - readme.txt
--------------------------------------------

[0.目次]
  1.概要
  2.以前のバージョン(1.x系)との相違点
  3.動作確認環境
  4.インストール
  5.プラグイン設定一覧
  6.プラグイン設定例
  7.アンインストール
  8.注意事項


[1.概要]
MobileConverter(以下、MC)はブラウザから送られてくる情報(User Agent等)を元に以下
の処理を行います。

・テンプレートの切り替え
・文字コードの変更
・HTML内のimgタグに書かれている画像の種類を自動的に変更
  (jpg、gif、pngに対応)
・画像のサイズ制限も可能(基本は元画像より縮小)

次のような用途に便利です。

・MODX内に携帯用ページ、スマートフォン用、PC用ページを共存させたい。
・携帯電話やスマートフォンから同じページにアクセスした際、携帯キャリア
  やスマートフォン用に違うテンプレートを使用したい。
・HTML内に書かれたimgタグの画像の種類を携帯キャリアごとに切り替えたい。
・IEやFirefox、Operaとブラウザごとにテンプレートを切り替えたい。
・アクセスするIPアドレスに応じてテンプレートを切り替えたい。
・言語(HTTP_ACCEPT_LANGUAGE)に応じてテンプレートを切り替えたい。


[2.以前のバージョン(1.x系)との相違点]
・設定に互換性はありません。
・php4を動作対象外にしました。
・ターゲットドキュメントの指定方法がもっと柔軟になった
・グループ機能により細かいルールを記載できるようになった。
・ルールの指定にUser Agent以外の項目が指定できるようになった
  (PHPの$_SERVER内の変数なら何でも判定に利用できます)
・ルールをマッチさせる際に正規表現が利用できるようになった
・画像は必要に応じて種類の変更、サイズの縮小を実行
  (1.x系では必ずMODX管理画面のファイル管理で画像をアップする必要があった)
・プラグインの名前の制約がなくなった
  (1.x系では必ず MobileConverter という名称にする必要があった)
・2.x系の方が設定が難しいです


[3.動作確認環境]
・MODX1.0.5J-r7
  文字コード：UTF-8
・PHP5.3.8(PHP4では動作しません)
・MySQL 5.5.15
・画像を扱うためにはPHPにgbライブラリが組み込まれている必要があります。
  

[4.インストール・設定方法]
  1)事前確認
    MCの設定を開始する前にMODXの設定を確認します。

    a.「ツール」→「グローバル設定」→「インターフェースとその他の機能」にある
      「相対パスを渡す」設定が「はい」になっている事

    b.「エレメント」→「グローバル管理」→「プラグイン」の「TinyMCE Rich Text 
       Editor」をクリック
       「設定」にある「Path Options」が「siteconfig」もしくは「rootrelative」
       になっている事

    この設定が違う場合、HTMLから画像パスの抽出ができず画像の変換が行われなくな
    ります。
    (フルパス表記等の対応は検討中です)

  2)アーカイブファイルを解答します。

  3)MODXの管理画面でプラグインを新規作成します。
    「エレメント」→「エレメント管理」→「プラグイン」→「プラグインの作成」

      (全般)
      プラグイン名    : MobileConverter
      説明            : (任意)
      プラグインコード: MobileConverter.txtファイルの中身をそのまま張りつける

      (設定)
      既存のカテゴリ  : (任意)
      カテゴリ作成    : (任意)
      プラグイン設定  : ※MCの動作をここで決定します。
                        詳しくは語述します。
                        →5.プラグイン設定一覧、6.プラグイン設定例

      (システムイベント)
      次のイベントをクリックしてチェックを付けてください。

        ・OnWebPageInit
        ・OnPageNotFound
        ・(OnLoadWebDocument)
        ・(OnLoadWebPageCache)
        ・(OnWebPagePrerender)
        ※()のイベントは必要に応じて自動で追加されるため、イベント順番を気にしな
          い場合は特に手動でチェックする必要はありません


  4)設定完了
    あとは実際にブラウジングして動作を確認します。


[5.プラグイン設定一覧]
プラグイン設定に指定できる設定の一覧です。
初見だと何がなんだかわからないと思いますので、まずは[6.プラグイン設定例]を見た
ほうがわかりやすいかもしれません。
(プラグイン設定自体の記述方法はMODXの他のドキュメントやWebサイトを参考にしてく
ださい…と言ってもあまり日本語で詳しい説明サイトがないのが現状なのですが…)

MCの基本動作次のようになり、この動作をプラグイン設定に記述します。

  1)MCが動作するリソース(以前ドキュメントと言われていた)を指定
  2)MC動作対象リソースに対してルールを設定
  3)ルールにマッチしたリソースに対して文字コードの変換、テンプレートの変換や画像
    のリサイズの設定

また、この1～3の流れがグループ化されており、リソースごとに違うMC設定を割り当てる
ことができます。

  &charset
    値  ：[UTF-8|Shift_JIS|EUC-JP|等]
    既定：Shift_JIS
    省略：可能
    説明：ルールにマッチしたリソースに対してMODXの既定の文字コードから変換したい
          文字コードを指定します。

  &enableCharConv
    値  ：[yes|no]
    既定：no
    省略：可能
    説明：ルールにマッチしたリソースに対して文字コードの変換の有無を設定します。
          yesを設定すると文字コードの変換が行われます。
          デフォルトは no です。

  &enableCharConvIfnoElect
    値  ：[yes|no]
    既定：no
    省略：可能
    説明：本来、ルールにマッチしたリソースに対し文字コード変換が行われるのです
          が、この設定をyesにするとルールから外れたリソースに対しても文字コー
          ド変換が行われます。          


  &imgPath
    値  ：[Dir](,...)
    既定：/
    省略：可能
    説明：ルールにマッチしたリソースが出力するHTMLに含まれるIMGタグで、画像変換
          の対象にしたいパスを指定します。
          指定は絶対パスではなく、IMGタグのSRC属性に記述するパスです。
          ",(コンマ)"で複数のパスを指定できます。
          指定しないとすべてのパスが変換対象となります。
          デフォルトはすべてのパスが対象になっています。
          
          なお、実際の変換は変換フォーマットを指定する必要があるため、後述する
          &g[groupNo]_[ruleNo]_imgType 設定まで行わないと画像変換は行われませ
          ん。


  &imgPathExcept
    値  ：[Dir](,...)
    既定：(未設定)
    省略：可能
    説明：&imgPath設定で画像変換対象になったパスよりも下位のパスで、変換対象外に
          したいパスを指定します。
          ",(コンマ)"で複数のパスを指定できます。
          &imgPath設定に複数のパスを設定した場合、すべてのパスに対して
          &imgPathExceptが適用されます。

  &imgMaxX
  &imgMaxY
    値  ：[整数(0以上) or 整数(0以上)%](,..)
    既定：(未設定)
    省略：可能
    説明：画像を変換する際の変換サイズを指定します。
          &imgMaxXは横方向(X)、&imgMaxYは縦方向(Y)が指定でき、数値だけ指定すれば
          「ピクセル指定」となり、末尾に%をつける事により「割合指定」になります。
          なお、「ピクセル指定」「割合指定」のそれぞれの特徴は次の通りです。
          
            - ピクセル指定
                XとYのどちらかがここで指定したサイズよりも大きい場合は、ここで指
                定したサイズまで画像を縮小します。
                縦横比は維持したままの縮小です。

            - 割合指定
                指定した割合を元に画像サイズを変換します。
                100%以上を指定することで、拡大することもできます。
                &imgMaxと&imgMaxYで違う割合を入れることで縦横比が崩れます。
                ピクセル設定よりも優先される。
                (Xにピクセル設定、Yに割合設定を入れるとXのピクセル設定は無視され
                る)
          
          設定を省略した場合、もしくは0を指定した場合は縮小を行いません。
          ",(コンマ)"で複数の変換サイズを指定でき、 &imgPath で複数設定したパス
          と順番に適用されていきます。
          そのため、複数指定する場合は &imgPath と数を合わせてください。
          
            設定例)
              &imgPath = /img01/,/img02/,/img03/
              &imgMaxX = 150,50%,200
              
              ※設定はそれぞれ次のように適用される。
                /img01/ -> 150
                /img02/ -> 50%
                /img03/ -> 200


  &thumbImgPath
    値  ：[Dir]
    既定：(未設定)
    省略：可能
    説明：変換した画像の保存場所を指定します。
          パスは絶対パスではなく、URLを基準としたパスで指定してください。
          指定しない場合は、画像がある場所と同じパスに変換画像が生成されます。
          なお、変換した画像のファイル名は先頭に「.mcthumb」という頭文字がついて
          保存されます。
          
            パスと生成されるファイル名の例)
              元画像のパス  ：assets/images/hoge.jpg
              ※htmlでは<img src="assets/images/hoge.jpg">と書かれる
              MODXの絶対パス：/PATO/TO/
              変換画像種類  ：pngに変換

              - 設定を省略した場合
                  /PATH/TO/assets/images/.mcthumb.[groupNo]_hoge.png

              - assets/images/temp/と設定した場合
                  /PATH/TO/assets/images/temp/.mcthumb.[groupNo]_assets_images_hoge.png
              ※[groupNo]はグループナンバーとなり、グループについては後述します。


  &enableOutEffect
    値  ：[yes|no]
    既定：no
    省略：可能
    説明：アクセス毎に一時的にMCを無効にすることができます。
          有効/無効の制御は以下の変数をQUERY_STRINGかPOSTに含めてリクエストさ
          せます。

            キー：MC_Enable
            値  ：on もしくは off

          MC_Enable=offを含めてリクエストすることで、そのブラウザ(端末)からの
          アクセスはMCが無効になり、テンプレート/画像の変換が発生しなくなりま
          す。          
          一旦、無効になるとcookieに情報が保存され、MC_Enable=onの変数を含めて
          リクエストが行われるまでMCは機能しません。
          (もしくはブラウザを一旦落とすとcookieが消えます)

 

  &g[groupNo]_targetDoc
    値  ：[+-][リソースID][オプション](,..)
    既定：(未設定)
    省略：&g[groupNo]_targetDoc か &g[groupNo]_targetTemplate のどちらかは必須
    説明：MCが有効になるリソースIDを指定します。
          ",(コンマ)"で複数のリソースを指定できます。
          [+-][オプション]等の意味は次のとおりです。

            +…MCを有効にする(省略可能)
            -…MCを無効にする
            r…そのリソース以下のすべてのリソースが対象
            R…そのリソース以下のすべてのリソースが対象
               ただし、指定されたリソースは除く

          -----------------------------------------------------------
          指定例)
            以下のようにリソースツリーがあったとします。

              0------2-----30
                 |      |
                 |      |--41
                 |      |
                 |      |--50
                 |      
                 |---5-----21-----23
                        |      |
                        |--33  |--39
                        |
                        |--38

            2       … 2のみMCが有効
            2,21    … 2と21に対しMCが有効
            2r      … 2,30,41,50に対しMCが有効
                       (2以下のリソースがすべて含まれる)
            2R      … 30,41,50に対しMCが有効
                       (2rの時と比べ2が含まれない)
            5r,-33  … 5,21,38,23,39に対しMCが有効
                       (33は含まれない)
            5r,-21R … 5,21,33,38に対しMCが有効
                       (23,39は含まれない)
            0       … 全てのリソースに対しMCが有効
                       (0にrやRオプションは不要です)
            0,-5r,21R … 2,30,41,50,23,39に対しMCが有効
                         (複雑な例)
          -----------------------------------------------------------

          &g[groupNo]_targetDocの[groupNo]部分には数字をセットします。
          この[groupNo]が1つのMC設定のグループになり、このグループに対して色々な
          設定を行うことができます。
          また[groupNo]は1から設定する必要があり、必ず連番にする必要があります。


  &g[groupNo]_targetTemplate
    値  ：[テンプレート名]
    既定：(未設定)
    省略：&g[groupNo]_targetDoc か &g[groupNo]_targetTemplate のどちらかは必須
    説明：MCが有効になるテンプレート名を指定します。
          &g[groupNo]_targetDocと排他設定になり、同じ[groupNo]でこの2を同時に指
          定することはできません。


          正しい設定例)
            &g1_targetDoc=Grp1:Target IDs;string;10r
              ...g1の設定...
            &g2_targetTemplate=Grp2:Target Template;string;TemplateNAME
              ...g2の設定...

          間違った設定例)
            &g1_targetDoc=Grp1:Target IDs;string;10r
            &g1_targetTemplate=Grp2:Target Template;string;TemplateNAME
              ...g1の設定...
            ※g1に&g[groupNo]_targetDocと&g[groupNo]_targetTemplateの2つが存在


  &g[groupNo]_imgPath
    値  ：[Dir](,...)
    既定：&imgPath設定に従う
    省略：可能
    説明：指定した[groupNo]専用の  &imgPath を設定します。
          省略した場合は &imgPath 設定に従います。


  &g[groupNo]_imgPathExcept
    値  ：[Dir](,...)
    既定：&imgPathExcept設定に従う
    省略：可能
    説明：指定した[groupNo]専用の  &imgPathExcept を設定します。
          省略した場合は &imgPathExcept 設定に従います。


  &g[groupNo]_thumbImgPath
    値  ：[Dir]
    既定：(未設定)
    省略：可能
    説明：指定した[groupNo]専用の &thumbImgPath を設定します。
          省略した場合は &thumbImgPath 設定に従います。


  ※廃止 &g[groupNo]_useReg
    &g[groupNo]_[ruleNo]_matchStr の設定内で指定できるように変更したため廃止
    値  ：[yes|no]
    省略：可能
    説明：yesを設定するとルールで文字列のマッチを行う際、正規表現が利用可能にな
          ります。
          デフォルトは No です。


  &g[groupNo]_[ruleNo]_matchStr
    値  ：[文字列](,…)
    既定：(未設定)
    省略：不可
    説明：指定した[groupNo]のルール[ruleNo]にマッチさせる文字列を記述します。
          ターゲットになる変数(&g[groupNo]_targetKey)の値がこの文字列にマッチし
          た場合、テンプレートの切り替えや画像の変換が発生します。
          [ruleNo]は1つのグループに複数設定でき、[ruleNo]毎に切り替えるテンプ
          レートを指定できます。
          また[groupNo]は1から設定する必要があり、必ず連番にする必要があります。
          
          マッチさせる文字列には次のような表記が可能です。

            - ","で複数の文字列を記述可能(OR)
            - "+"で複数の文字列を記述可能(AND)
            - "/"で始める文字列は正規表現として扱う
          
            例)hoge もしくは fuga があれば変換発生
                hoge,fuga

            例)hoge と fuga があれば変換発生
                hoge+fuga

            例)正規表現の例
                /hoge$/,/^fuga/

            例)複雑な例
                iphone+ipad,android+mobile,android+/3\.[0-9]/

          なお、マッチさせる文字列に",+/"を利用したい場合、先頭に\をつけてくだ
          さい。
          また"/"だけは先頭になければ普通の"/"として扱います。


  &g[groupNo]_imgMaxX
  &g[groupNo]_imgMaxY
  &g[groupNo]_[ruleNo]_imgMaxX
  &g[groupNo]_[ruleNo]_imgMaxY
    値  ：[整数(0以上) or 整数(0以上)%](,..)
    既定：(未設定)
    省略：&imgMaxX / &imgMaxY 設定に従う
    説明：指定した[groupNo]もしくは[groupNo]と[ruleNo]専用の  &imgMaxX / 
          &imgMaxY を設定します。
          省略した場合は &imgMaxX / &imgMaxY 設定に従います。

          
  &g[groupNo]_[ruleNo]_charset
    値  ：[UTF-8|Shift_JIS|EUC-JP|等]
    既定：&charset 設定に従う
    省略：可能
    説明：指定した[groupNo]と[ruleNo]専用の &charset を設定します。
          省略した場合は &charset 設定に従います。


  &g[groupNo]_[ruleNo]_enableCharConv
    値  ：[yes|no]
    既定：no
    省略：可能
    説明：指定した[groupNo]と[ruleNo]にて、ルールにマッチしても文字コードの変換
          を行いたくない場合はこの設定をnoにします。
          デフォルトは no です。


  &g[groupNo]_[ruleNo]_template
    値  ：[テンプレート名]
    既定：(未設定)
    省略：不可
    説明：指定した[groupNo]と[ruleNo]にて、&g[groupNo]_[ruleNo]_matchStr設定の
          ルールにマッチした時、切り替えるテンプレートを指定します。


  &g[groupNo]_[ruleNo]_imgType
    値  ：[jpg|png|gif|inherit]
    既定：(未設定)
    省略：可能
    説明：指定した[groupNo]と[ruleNo]にて、&g[groupNo]_[ruleNo]_matchStr設定の
          ルールにマッチした時、リソース内に書かれているIMGタグの画像をここで指
          定した画像の種類に変換します。
          値にinheritを指定した場合は画像の種類は変更せず、サイズの変更のみが発
          生します。
          この設定を行わない場合は画像の変換もサイズの変更も発生しません。
          
          ",(コンマ)"で複数のパスを指定できますが、必ず &imgPath,
          &g[groupNo]_imgPath 設定の数とあわせてください。

          ",(コンマ)"で複数の変換タイプを指定でき、 &imgPath で複数設定したパス
          と順番に適用されていきます。
          そのため、複数指定する場合は &imgPath と数を合わせてください。
          
            設定例)
              &imgPath      = /img01/,/img02/,/img03/
              &g1_2_imgType = png,inherit,jpg
              
              ※設定はそれぞれ次のように適用される。
                /img01/ -> png
                /img02/ -> inherit
                /img03/ -> jpg

          なお、画像変換は必要な時に応じて自動で発生します。
          一度画像変換が発生したらそのファイルを保存しているため、2回目以降の
          アクセスには画像の変換処理が発生しません。          
          (保存した画像が使われます)
          ただし、変換した画像よりもオリジナルファイルのタイムスタンプが新しい
          場合は再度画像の変換処理が発生します。
          これはオリジナルファイルが新しくなった場合に変換した画像も新しくする
          ための処理です。
          また、手動で変換した画像を新しくしたい場合、変換画像のファイルを削除
          を行うことで次のアクセス時に新しく変換画像が作成されます。
          画像のパス等は &thumbImgPath 設定を見てください。


  &g[groupNo]_enableOutEffect
    値  ：[yes|no]
    既定：&enableOutEffect 設定に従う
    省略：可能
    説明：指定した[groupNo]と[ruleNo]専用の &enableOutEffect を設定します。
          省略した場合は &enableOutEffect 設定に従います。


  &g[groupNo]_404Resource
    値  ：[数字]
    既定：HTTP_USER_AGENT
    省略：可能
    説明：指定した[groupNo]専用の404ページ(エラーページ)になるリソースを変更しま
          す。
          これで[groupNo]毎に違う404ページを用意できます。


  &g[groupNo]_targetKey
    値  ：[HTTP_USER_AGENT|HTTP_ACCEPT_LANGUAGE|REMOTE_ADDR|等]
    既定：HTTP_USER_AGENT
    省略：可能
    説明：ルールで文字列のマッチを行う際のターゲットになる変数を指定します。
          デフォルトは HTTP_USER_AGENT になります。
          他にもHTTP_ACCEPT_LANGUAGEやREMOTE_ADDRの指定ができます。
          (正確に書くと$_SERVER変数の添え字に使われます)
          単純にUser Agentでテンプレートの切り替えを行いたい場合は、特にこの
          設定を意識する必要はありません。

          需要としてあるかわかりませんが、アクセスしてくる言語によってテンプ
          レートを分けたい、アクセスIPでテンプレートを分けたいといった用途に使
          えると思います。
          あとQUERY_STRINGも対象にできるので何かに使えるかもしれません…。


[6.プラグイン設定例]
以下、代表的な設定例です。

例-1)
  PC&携帯サイト対応(画像変換なし)
  以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&enableCharConv=文字コード変換;string;yes
&g1_targetDoc=Grp1:リソースID;string;0
&g1_1_matchStr=Grp1:ドコモ用Agent;string;Docomo
&g1_1_template=Grp1:Docomo用テンプレート;string;T_Docomo
&g1_2_matchStr=Grp1:Softbank用Agent;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Softbank用テンプレート;string;T_Softbank
&g1_3_matchStr=Grp1:AU用Agent;string;KDDI
&g1_3_template=Grp1:AU用テンプレート;string;T_au

  動作は以下のとおりです。

  ・MODXすべてのリソースが対象
  ・PCからアクセスは普通にページを表示する
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomoに切替
      - ソフトバンクの場合はテンプレートをT_Softbankに切替
      - AUの場合はテンプレートをT_auに切替


例-2)
  PC&携帯サイト対応(画像変換あり)
  以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&enableCharConv=文字コード変換;string;yes
&g1_targetDoc=Grp1:リソースID;string;0
&g1_imgPath=Grp1:対象画像パス;string;assets/images/
&g1_imgMaxX=Grp1:最大Xサイズ;string;200
&g1_imgMaxY=Grp1:最大Yサイズ;string;180
&g1_thumbImgPath=Grp1:サムネイル保存パス;string;assets/images/tmp/
&g1_1_matchStr=Grp1:ドコモ用Agent;string;Docomo
&g1_1_template=Grp1:ドコモ用テンプレート;string;T_Docomo
&g1_1_imgType=Grp1:ドコモ用画像タイプ;string;gif
&g1_2_matchStr=Grp1:Softbank用Agent;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Softbank用テンプレート;string;T_Softbank
&g1_2_imgType=Grp1:Softbank用画像タイプ;string;jpg
&g1_3_matchStr=Grp1:AU用Agent;string;KDDI
&g1_3_template=Grp1:AU用テンプレート;string;T_au
&g1_3_imgType=Grp1:AU用画像タイプ;string;png

  動作は以下のとおりです。

  ・MODXすべてのリソースが対象
  ・PCからアクセスは普通にページを表示する
  ・画像変換の設定は次のとおり
     - assets/images/ 以下の画像がすべて変換対象
     - 変換した画像は assets/images/tmp/ に保存される
     - 横のサイズが200を超える画像は200に縮小される
     - 縦のサイズが180を超える画像は180に縮小される
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomoに切替、画像をgifに変換
      - ソフトバンクの場合はテンプレートをT_Softbankに切替、画像をjpgに変換
      - AUの場合はテンプレートをT_auに切替、画像をpngに変換


例-3)
  Docomoをメインページとし、3キャリア携帯振り分け(画像変換あり)
  以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&enableCharConv=文字コード変換;string;yes
&enableCharConvIfnoElect=MC対象外も文字コード変換;list;yes,no;yes &g1_targetDoc=Grp1:リソースID;string;7r,18R
&g1_imgPath=Grp1:対象画像パス;string;assets/images/
&g1_imgMaxX=Grp1:最大Xサイズ;string;200
&g1_imgMaxY=Grp1:最大Yサイズ;string;180
&g1_thumbImgPath=Grp1:サムネイル保存パス;string;assets/images/tmp/
&g1_1_matchStr=Grp1:Softbank用Agent;string;Vodafone,J-PHONE,SoftBank
&g1_1_template=Grp1:Softbank用テンプレート;string;T_Softbank
&g1_1_imgType=Grp1:Softbank用画像タイプ;string;jpg
&g1_2_matchStr=Grp1:AU用Agent;string;KDDI
&g1_2_template=Grp1:AU用テンプレート;string;T_au
&g1_2_imgType=Grp1:AU用画像タイプ;string;png

  動作は以下のとおりです。

  ・7を含む7の子リソース以下全てと18を含まない18の子リソース以下全てが対象
  ・ルールにマッチしない場合もShift_JIS変換される(&enableCharConvIfnoElect)
  ・画像変換の設定は次のとおり
     - assets/images/ 以下の画像がすべて変換対象
     - 変換した画像は assets/images/tmp/ に保存される
     - 横のサイズが200を超える画像は200に縮小される
     - 縦のサイズが180を超える画像は180に縮小される
  ・他の携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ソフトバンクの場合はテンプレートをT_Softbankに切替、画像をjpgに変換
      - AUの場合はテンプレートをT_auに切替、画像をpngに変換


設定例-4)
  グループを複数用意した設定です。
  携帯用にテンプレートを切り替えるのですが、リソースに応じて切り替えるテンプ
  レートを変更します(画像変換なし)。

&enableCharConv=文字コード変換;string;yes
&g1_targetDoc=Grp1:リソースID;string;10r
&g1_1_matchStr=Grp1:ドコモ用Agent;string;Docomo
&g1_1_template=Grp1:ドコモ用テンプレート;string;T_Docomo1
&g1_2_matchStr=Grp1:Softbank用Agent;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Softbank用テンプレート;string;T_Softbank1
&g1_3_matchStr=Grp1:AU用Agent;string;KDDI
&g1_3_template=Grp1:AU用テンプレート;string;T_au1
&g2_targetDoc=Grp2:リソースID;string;20R
&g2_1_matchStr=Grp2:ドコモ用Agent;string;Docomo
&g2_1_template=Grp2:ドコモ用テンプレート;string;T_Docomo2
&g2_2_matchStr=Grp2:Softbank用Agent;string;Vodafone,J-PHONE,SoftBank
&g2_2_template=Grp2Softbank用テンプレート;string;T_Softbank2
&g2_3_matchStr=Grp2:AU用Agent;string;KDDI
&g2_3_template=Grp2:AU用テンプレート;string;T_au2

  動作は以下のとおりです。

  [グループ1]
  ・10を含む10の子リソース以下全てが対象
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomo1に切替
      - ソフトバンクの場合はテンプレートをT_Softbank1に切替
      - AUの場合はテンプレートをT_au1に切替

  [グループ2]
  ・20を含まない20の子リソース以下全てが対象
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomo2に切替
      - ソフトバンクの場合はテンプレートをT_Softbank2に切替
      - AUの場合はテンプレートをT_au2に切替


設定例-5)
  Android、iphone用のテンプレートを変換する例です。

&enableOutEffect=外部MC制御;string;yes
&g1_targetTemplate=Grp1:テンプレート名;string;PC用テンプレ
&g1_imgPath=Grp1:対象画像パス;string;assets/images/sp01/,assets/images/sp02/
&g1_imgMaxX=Grp1:最大Xサイズ;string;150,40%
&g1_imgMaxY=Grp1:最大Yサイズ;string;180,40%
&g1_thumbImgPath=Grp1:サムネイル保存パス;string;assets/images/tmp/sp01/,assets/images/tmp/sp02/
&g1_1_matchStr=Grp1:スマフォ用Agent;string;iphone,android
&g1_1_template=Grp1:スマフォ用テンプレート;string;T_SP
&g1_1_imgType=Grp1:スマフォ用画像タイプ;string;png,inherit

  動作は以下のとおりです。

  ・「PC用テンプレ」をテンプレートにしているリソースが対象
  ・Android,iphone(スマフォ)端末からのアクセスは以下の処理を行う
      - テンプレートをT_SPに切替
      - assets/images/sp01/ 以下の画像はX=150,Y=180以下に縮小
        ※画像をpngに変換
        ※変換した画像は assets/images/tmp/sp01/ に保存される
      - assets/images/sp02/ 以下の画像はX,Y共に40%に縮小
        ※画像タイプはそのまま
        ※変換した画像は assets/images/tmp/sp02/ に保存される

  &enableOutEffectが有効になっているので、以下のようにQUERY_STRINGをつけたリンクを用意すると、その
  ブラウザは一時的にMCの変換が無効になります。

    http://example.jp/hoge.html?MC_Enable=off
    ※POSTで渡しても可

  逆に有効にするにはMC_Enable=onをつけてリクエストさせてください。
  スマフォでもPC版ページを見せたいときに利用できます。


設定例-6)
  複雑な設定例です。
  こんな設定もできます(が、動作説明は省略します)。
  ちなみにグループ3は日本語設定のブラウザ(もしくは判断できない)のときだけテン
  プレート切替と画像変換をしている設定です。ルールは正規表現で書いています。
  
&charset=Charset;string;Shift_JIS
&g1_targetDoc=Grp1:Target IDs;string;10r
&g1_imgMaxX=Grp1:Target img max X;string;120
&g1_imgMaxY=Grp1:Target img max Y;string;100
&g1_1_matchStr=Grp1:UserAgent for Docomo;string;Docomo
&g1_1_template=Grp1:Template for Docomo;string;T_Docomo1
&g1_1_imgType=Grp1:ImageType for Docomo;string;gif
&g1_2_matchStr=Grp1:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Template for Softbank;string;T_Softbank1
&g1_2_imgType=Grp1:ImageType for Softbank;string;jpg
&g1_3_matchStr=Grp1:UserAgent for au;string;KDDI
&g1_3_template=Grp1:Template for au;string;T_au1
&g1_3_imgType=Grp1:ImageType for au;string;png
&g2_targetDoc=Grp2:Target IDs;string;20R
&g2_imgPath=Grp2:Target img Path;string;assets/images/hoge/
&g2_imgMaxX=Grp2:Target img max X;string;50
&g2_imgMaxY=Grp2:Target img max Y;string;50
&g2_thumbImgPath=Grp2:Thumbnail path;string;assets/images/tmp/
&g2_1_matchStr=Grp2:UserAgent for Docomo;string;Docomo
&g2_1_template=Grp2:Template for Docomo;string;T_Docomo2
&g2_1_imgType=Grp2:ImageType for au;string;gif
&g2_2_matchStr=Grp2:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g2_2_template=Grp2Template for Softbank;string;T_Softbank2
&g2_2_imgType=Grp2:ImageType for au;string;jpg
&g2_3_matchStr=Grp2:UserAgent for au;string;KDDI
&g2_3_template=Grp2:Template for au;string;T_au2
&g2_3_imgType=Grp3:ImageType for au;string;png
&g3_targetDoc=Grp3:Target IDs;string;30r
&g3_imgPath=Grp3:Target img path;string;assets/images/fuga1/,assets/images/fuga2/
&g3_imgMaxX=Grp3:Target img max X;string;200,100
&g3_imgMaxY=Grp3:Target img max Y;string;180,80
&g3_thumbImgPath=Grp3:Thumbnail path;string;assets/images/tmp2/
&g3_targetKey=Grp3:Target Variables;string;HTTP_ACCEPT_LANGUAGE
&g3_useReg=Use Regexp;list;yes,no;yes &g3_1_matchStr=Grp2:Str for JP;string;/^ja/,/^$/
&g3_1_template=Grp3:Template for JP;string;T_JP
&g3_1_imgType=Grp3:Image Type for JP;string;png
&g3_1_charset=Grp3:Charset;string;EUC-JP


[7.アンインストール]
1.MCプラグインを削除します。
 「リソース」→「リソース管理」→「プラグイン」→「MobileConverter」→「削除」

2.変換画像の消去
  画像のパスは &thumbImgPath,&g[groupNo]_thumbImgPath 設定を参考にしてくださ
  い。
  

[8.注意事項]
1.MCの対象になったドキュメントのキャッシュ機能は強制手的にOFFになります。
  (そうしないとテンプレート切り替えられないので)

2.このプラグインは出力されるHTMLの文字コードをShift_JIS等に変換できますが、HTML
  のMETAタグに書かれるcharsetは変換しません。
  そのためShift_JISで出力するつもりのテンプレートのcharsetはあらかじめShift_JIS
  と書いておく必要があります。

  例)
    <meta http-equiv="Content-Type" content="text/html; charset=Shift_JIS" />


3.画像の変換について
  画像の変換は出力するHTMLからimgタグを引っ張り出して、src属性に書かれている画
  像のパスを調べて変換処理を行っています。
  ここのパスの表記はbaseタグを使った表記方法で行う必要があります。
  (多くのMODXサイトはこの表記方法だと思います)

    HTMLの例)
     baseタグでサイトのベースURLを決めて、画像はそこからのパスを指定する。とい
     う感じです。     
     -------------------------------------------------------------
      <html>
        <head>
          <base href="http://YOUR_MODX_SITE.jp/path/"></base>
        </head>
        <body>
          <img src="assets/images/test01.jpg" alt="image">
        </body>
      </html>
     -------------------------------------------------------------

  MODXのどこかのバージョンからURL付のフルパス表記に対応しており、場合によって
  は設定を見直す必要があります。
  (詳しくは 4.インストール・設定方法の1を参照してください)

  また画像変換した後のimgタグではwidthとheight属性が消えてしまいます。
  これは画像のサイズによってはサイズの変更が伴うので、わざと消しています。
  ページアクセス時に毎回画像を読み込んでwidthとheightを調べて出力してもいいの
  ですが、負荷が気になるので実装していません。

