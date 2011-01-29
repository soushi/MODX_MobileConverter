--------------------------------------------
MobileConverter for MODx - readme.txt
--------------------------------------------

[0.目次]
  1.概要
  2.以前のバージョン(1.x系)との相違点
  3.動作確認環境
  4.インストール
  5.プラグイン設定
  6.プラグイン設定例
  7.アンインストール
  8.注意事項
  9.更新履歴

[1.概要]
ブラウザから送られてくる情報(User Agent等)を元に以下の処理を行います。

・テンプレートの切り替え
・文字コードの変更
・HTML内のimgタグに書かれている画像の種類を自動的に変更
  (jpg、gif、pngに対応)
・画像のサイズ制限も可能(指定したサイズよりも大きい画像の縮小)

次のような用途に便利です。

・modx内に携帯用ページとPC用ページを共存させたい。
・携帯電話から同じページにアクセスした際、携帯電話のキャリアごとに違うテンプ
  レートを使用したい。
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
  (1.x系では必ずmodx管理画面のファイル管理で画像をアップする必要があった)
・プラグインの名前の制約がなくなった
  (1.x系では必ず MobileConverter という名称にする必要があった)
・2.x系の方が設定が難しいです

[3.動作確認環境]
・MODx1.0.2J
  文字コード：UTF-8
・PHP5.2.12(PHP4では動作しません)
・MySQL 5.0.90
・画像を扱うためにはPHPにgbライブラリが組み込まれている必要があります。
  

[4.インストール・設定方法]
1)アーカイブファイルを解答します。

2)MODxの管理画面でプラグインを新規作成します。
 「エレメント」→「エレメント管理」→「プラグイン」→「プラグインの作成」

    (全般)
    プラグイン名    : MobileConverter
    説明            : (任意)
    プラグインコード: MobileConverter.txtファイルの中身をそのまま張りつける

    (設定)
    既存のカテゴリ  : (任意)
    カテゴリ作成    : (任意)
    プラグイン設定  : ※MobileConverterの動作をここで決定します。
                        詳しくは語述します(5.プラグイン設定、6.プラグイン設定例)。

    (システムイベント)
    次のイベントをクリックしてチェックを付けてください。

      ・OnWebPageInit

3)設定完了
あとは実際にブラウジングして動作を確認します。

[5.プラグイン設定補足]
プラグイン設定に利用できるすべての設定です。
初見だと何がなんだかわからないと思いますので、まずは[6.プラグイン設定例]を見た
ほうがわかりやすいかもしれません。
(プラグイン設定自体の記述方法はMODxの他のドキュメントやWebサイトを参考にしてく
ださい…と言ってもあまり日本語で詳しい説明サイトがないのが現状なのですが…)


  &charset
    値  ：[UTF-8|Shift_JIS|EUC-JP|等]
    省略：可能
    説明：modxの規定の文字コードから変換したい文字コードを指定します。
          デフォルトはShift_JISになります。


  &enableCharConvIfnoElect
    値  ：[yes|no]
    省略：可能
    説明：本来、ルールにマッチしたリソースに対し文字コード変換が行われるのです
          が、この設定をyesにするとルールから外れたリソースに対しても文字コー
          ド変換が行われます。          
          デフォルトはnoです。


  &g[groupNo]_targetDoc
    値  ：[+-][数字][オプション][,…]
    省略：不可
    説明：MobileConverterが有効になるリソースIDを指定します。
          ",(コンマ)"で複数のリソースを指定できます。
          オプション等の意味は次のとおりです。

            +…MobileConverterを有効にする(省略可能)
            -…MobileConverterを無効にする
            r…そのリソース以下のすべてのリソースが対象
            R…そのリソース以下のすべてのリソースが対象
               ただし、指定されたリソースは除く

          -----------------------------------------------------------
          指定例)
            以下のようにリソースがあったとします。

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

            2       … 2のみMobileConverterが有効
            2,21    … 2と21に対しMobileConverterが有効
            2r      … 2,30,41,50に対しMobileConverterが有効
                       (2以下のリソースがすべて含まれる)
            2R      … 30,41,50に対しMobileConverterが有効
                       (2rの時と比べ2が含まれない)
            5r,-33  … 5,21,38,23,39に対しMobileConverterが有効
                       (33は含まれない)
            5r,-21R … 5,21,33,38に対しMobileConverterが有効
                       (23,39は含まれない)
            0       … 全てのリソースに対しMobileConverterが有効
                       (0にrやRオプションは不要です)
            0,-5r,21R … 2,30,41,50,23,39に対しMobileConverterが有効
                         (複雑な例)
          -----------------------------------------------------------

          [groupNo]には数字をセットします。
          この[groupNo]が同じ数字のプラグイン設定が1つのグループとして機能しま
          す。
          またgroupNoは1から設定する必要があり、必ず連番にする必要があります。


  &imgPath
  &g[groupNo]_imgPath
    値  ：[Dir]
    省略：可能
    説明：画像変換の対象になるパス(ディレクトリ)を指定します。
          ",(コンマ)"で複数のパスを指定できます。
          パスはIMGタグのSRC属性に記述するパスになります。
          このパスから外れる画像は変換対象になりません。
          デフォルトはすべてのパスが対象になっています。
          ただし、後術する &g[groupNo]_[ruleNo]_switchImgType 設定を行わないと
          画像変換は行われません。

          [groupNo]は &g[groupNo]_targetDoc で設定した[groupNo]と連動します。
          &imgPathの指定だと[groupNo]にかかわらず、すべてのグループに対し設定
          が有効になります。
          なお、設定は &g[groupNo]_imgPath の方が優先されます。


  &imgMaxX
  &g[groupNo]_imgMaxX
  &imgMaxY
  &g[groupNo]_imgMaxY
    値  ：[整数(0以上)]
    省略：可能
    説明：画像を変換する際、この指定のサイズよりも画像が大きい場合はここで指定
          したサイズまで縮小します。
          設定を省略した場合、もしくは0を指定した場合は縮小を行いません。
          ",(コンマ)"で複数のパスを指定できますが、必ず &imgPath,
          &g[groupNo]_imgPath 設定の数とあわせてください。

          [groupNo]は &g[groupNo]_targetDoc で設定した[groupNo]と連動します。
          &imgMaxX,&imgMaxYの指定だと[groupNo]にかかわらず、すべてのグループに
          対し設定が有効になります。
          なお、設定は &g[groupNo]_imgMaxX,&g[groupNo]_imgMaxY の方が優先され
          ます。

          
  &thumbImgPath
  &g[groupNo]_thumbImgPath
    値  ：[Dir]
    省略：可能
    説明：変換した画像の保存場所を指定します。
          パスは絶対パスではなく、URLを基準としたパスで指定します。
          指定しない場合は、画像がある場所と同じパスに変換画像が生成されます。
          なお、変換した画像のファイル名は次のように決定します。

            元画像のパス  ：assets/images/hoge.jpg
            ※タグでは<img src="assets/images/hoge.jpg">と書かれる
            modxの絶対パス：/PATO/TO/
            変換画像種類  ：pngに変換

            - 設定を省略した場合
                /PATH/TO/assets/images/.mcthumb.[groupNo]_hoge.png

            - assets/images/temp/と設定した場合
                /PATH/TO/assets/images/temp/.mcthumb.[groupNo]_assets_images_hoge.png

          [groupNo]は &g[groupNo]_targetDoc で設定した[groupNo]と連動します。
          &thumbImgPathの指定だと[groupNo]にかかわらず、すべてのグループに対し
          設定が有効になります。
          なお、設定は &g[groupNo]_thumbImgPath の方が優先されます。


  &g[groupNo]_targetKey
    値  ：[HTTP_USER_AGENT|HTTP_ACCEPT_LANGUAGE|REMOTE_ADDR|等]
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


  &g[groupNo]_useReg
    値  ：[yes|no]
    省略：可能
    説明：yesを設定するとルールで文字列のマッチを行う際、正規表現が利用可能にな
          ります。
          デフォルトは No です。


  &g[groupNo]_[ruleNo]_matchStr
    値  ：[文字列][,…]
    省略：不可
    説明：ルールにマッチさせる文字列を記述します。
          ",(コンマ)"で複数の文字列を記述することができます。
          ルールにマッチした場合、テンプレートの切り替えや画像の変換が発生しま
          す。
          &g[groupNo]_useReg 設定が yes になっている場合は、正規表現で記述する
          必要があります。

            - 通常の記述の例
                hoge,fuga

            - 正規表現で記述する場合の例
                /hoge$/,/^fuga/

          [ruleNo]には数字をセットし、1つのグループに複数のルールを持たせること
          ができます。
          ([groupNo]と同じく1から設定する必要があり、必ず連番にする必要があり
          ます)
          各ルールごとに切り替えるテンプレートの指定や、変換する画像の指定が可
          能です。
          

  &g[groupNo]_[ruleNo]_charset
    値  ：[UTF-8|Shift_JIS|EUC-JP|等]
    省略：可能
    説明：modxの規定の文字コードから変換したい文字コードを指定します。
          デフォルトは&charsetで指定した値になります。
          (&charsetも指定していない場合は"Shift_JIS")

          [groupNo]と[ruleNo]が同じ数字の　&g[groupNo]_[ruleNo]_matchStr 設定で
          マッチしたものに対して文字コードの変換が発生します。


  &g[groupNo]_[ruleNo]_enableCharConv
    値  ：[yes|no]
    省略：可能
    説明：ルールにマッチしても文字コードの変換を行いたくない場合はこの設定をno
          にします。
          デフォルトは no です。

          [groupNo]と[ruleNo]が同じ数字の　&g[groupNo]_[ruleNo]_matchStr 設定で
          マッチしたものに対して設定が有効になります。


  &g[groupNo]_[ruleNo]_template
    値  ：[テンプレート名]
    省略：不可
    説明：ルールにマッチした際に切り替えるテンプレートを指定します。

          [groupNo]と[ruleNo]が同じ数字の　&g[groupNo]_[ruleNo]_matchStr 設定で
          マッチしたものに対して設定が有効になります。
          

  &g[groupNo]_[ruleNo]_imgType
    値  ：[jpg|png|gif]
    省略：可能
    説明：ルールにマッチしたリソース内に書かれているimgタグの画像をここで指定
          した画像の種類に変換します。
          この設定を行わない場合は変換が発生しません。
          &imgPath、&g[groupNo]_imgPath 設定で更に変換対象の画像を限定すること
          ができます。
          &imgMaxX,&g[groupNo]_imgMaxX,&imgMaxY,&g[groupNo]_imgMaxY 設定で大き
          な画像の自動縮小を行うことができます。

          [groupNo]と[ruleNo]が同じ数字の　&g[groupNo]_[ruleNo]_matchStr 設定
          でマッチしたものが画像変換の対象になります。

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



[6.プラグイン設定例]
以下、代表的な設定例です。

例-1)
  PC&携帯サイト対応(画像変換なし)
  以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&g1_targetDoc=Grp1:Target IDs;string;0
&g1_1_matchStr=Grp1:UserAgent for Docomo;string;Docomo
&g1_1_template=Grp1:Template for Docomo;string;T_Docomo
&g1_2_matchStr=Grp1:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Template for Softbank;string;T_Softbank
&g1_3_matchStr=Grp1:UserAgent for au;string;KDDI
&g1_3_template=Grp1:Template for au;string;T_au

  動作は以下のとおりです。

  ・modxすべてのリソースが対象
  ・PCからアクセスは普通にページを表示する
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomoに切替
      - ソフトバンクの場合はテンプレートをT_Softbankに切替
      - AUの場合はテンプレートをT_auに切替


例-2)
  PC&携帯サイト対応(画像変換あり)
  以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&g1_targetDoc=Grp1:Target IDs;string;0
&g1_imgPath=Grp1:Target img path;string;assets/images/
&g1_imgMaxX=Grp1:Target img max X;string;200
&g1_imgMaxY=Grp1:Target img max Y;string;180
&g1_thumbImgPath=Grp1:Thumbnail path;string;assets/images/tmp/
&g1_1_matchStr=Grp1:UserAgent for Docomo;string;Docomo
&g1_1_template=Grp1:Template for Docomo;string;T_Docomo
&g1_1_imgType=Grp1:ImageType for Docomo;string;gif
&g1_2_matchStr=Grp1:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Template for Softbank;string;T_Softbank
&g1_2_imgType=Grp1:ImageType for Softbank;string;jpg
&g1_3_matchStr=Grp1:UserAgent for au;string;KDDI
&g1_3_template=Grp1:Template for au;string;T_au
&g1_3_imgType=Grp1:ImageType for au;string;png

  動作は以下のとおりです。

  ・modxすべてのリソースが対象
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

&enableCharConvIfnoElect=Char Conv If no Elect;list;yes,no;yes &g1_targetDoc=Grp1:Target IDs;string;7r,18R
&g1_imgPath=Grp1:Target img path;string;assets/images/
&g1_imgMaxX=Grp1:Target img max X;string;200
&g1_imgMaxY=Grp1:Target img max Y;string;180
&g1_thumbImgPath=Grp1:Thumbnail path;string;assets/images/tmp/
&g1_1_matchStr=Grp1:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g1_1_template=Grp1:Template for Softbank;string;T_Softbank
&g1_1_imgType=Grp1:ImageType for Softbank;string;jpg
&g1_2_matchStr=Grp1:UserAgent for au;string;KDDI
&g1_2_template=Grp1:Template for au;string;T_au
&g1_2_imgType=Grp1:ImageType for au;string;png

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

&g1_targetDoc=Grp1:Target IDs;string;10r
&g1_1_matchStr=Grp1:UserAgent for Docomo;string;Docomo
&g1_1_template=Grp1:Template for Docomo;string;T_Docomo1
&g1_2_matchStr=Grp1:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g1_2_template=Grp1:Template for Softbank;string;T_Softbank1
&g1_3_matchStr=Grp1:UserAgent for au;string;KDDI
&g1_3_template=Grp1:Template for au;string;T_au1
&g2_targetDoc=Grp2:Target IDs;string;20R
&g2_1_matchStr=Grp2:UserAgent for Docomo;string;Docomo
&g2_1_template=Grp2:Template for Docomo;string;T_Docomo2
&g2_2_matchStr=Grp2:UserAgent for Softbank;string;Vodafone,J-PHONE,SoftBank
&g2_2_template=Grp2Template for Softbank;string;T_Softbank2
&g2_3_matchStr=Grp2:UserAgent for au;string;KDDI
&g2_3_template=Grp2:Template for au;string;T_au2

  動作は以下のとおりです。

  〜グループ1〜
  ・10を含む10の子リソース以下全てが対象
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomo1に切替
      - ソフトバンクの場合はテンプレートをT_Softbank1に切替
      - AUの場合はテンプレートをT_au1に切替

  〜グループ2〜
  ・20を含まない20の子リソース以下全てが対象
  ・携帯端末からのアクセスは以下の処理を行う
      - 文字コードをShift_JISに変換
      - ドコモの場合はテンプレートをT_Docomo2に切替
      - ソフトバンクの場合はテンプレートをT_Softbank2に切替
      - AUの場合はテンプレートをT_au2に切替

設定例-5)
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
1.MobileConverterプラグインを削除します。
 「リソース」→「リソース管理」→「プラグイン」→「MobileConverter」→「削除」

2.変換画像の消去
  画像のパスは &thumbImgPath,&g[groupNo]_thumbImgPath 設定を参考にしてくださ
  い。
  

[8.注意事項]
1.MobileConverterの対象になったドキュメントのキャッシュ機能は強制手的にOFFにな
  ります。
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
  (多くのmodxサイトはこの表記方法だと思います)

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

  また画像変換した後のimgタグではwidthとheight属性が消えてしまいます。
  これは画像のサイズによってはサイズの変更が伴うので、わざと消しています。
  ページアクセス時に毎回画像を読み込んでwidthとheightを調べて出力してもいいの
  ですが、負荷が気になるので実装していません。
  

