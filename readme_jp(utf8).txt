MobileConverter for MODx

[機能]
ブラウザからのUser Agentを元に以下の変換を行います。

・テンプレートの切り替え
・文字コードの変更
・HTML内のIMGタグに書かれている画像の種類を変更
  (jpg、gif、pngに対応)
・画像のサイズ制限も可能

次のような用途に便利です。

・MODx内に携帯用ページとPC用ページを共存させたい。
・携帯電話から同じページにアクセスした際、携帯電話のキャリアごとに違うテンプ
  レートを使用したい。
・HTML内に書かれたIMGタグの画像の種類を携帯キャリアごとに切り替えたい。
・IEやFirefox、Operaとブラウザごとに違うテンプレートを使用したい。


[動作確認環境]
・MODx0.9.6
  文字コード：UTF-8
・PHP5.2.4(PHP4系は未検証)
・MySQL 5.0.41

※画像を扱うためにはPHPにgbライブラリが組み込まれている必要があります。
  

[インストール・設定方法]
1.アーカイブファイルを解答します。

2.MODxの管理画面でプラグインを新規作成します。
 「リソース」→「リソース管理」→「プラグイン」→「プラグインの作成」

    (全般)
    プラグイン名    : MobileConverter
                      ※名前を変えると動作しなくなります
    説明            : (任意)
    プラグインコード: MobileConverter.txtファイルの中身をそのまま張りつける

    (設定)
    既存のカテゴリ  : (任意)
    カテゴリ作成    : (任意)
    プラグイン設定  : ※このプラグインの動作をここで決定します。
                        詳しくは語述します(3.プラグイン設定例)。

    (システムイベント)
    次のイベントをクリックしてチェックを付けてください。

      ・OnWebPageInit
      ・OnFileManagerUpload

3.プラグイン設定例
本プラグインの動作はここで制御します。
以下、代表的な設定例です。
(設定をカスタマイズしたい方は下のほうにある[プラグイン設定補足]をご覧ください)

例-1)PC&携帯サイト対応(画像変換なし)
以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&targetDoc=Target document IDs;string;0
&mobileCharset=Mobile charset;string;Shift_JIS
&userAgent1=Docomo:User Agent;string;Docomo
&switchTemplate1=Docomo:Template;string;ドコモ
&userAgent2=Softbank:User Agent;string;Vodafone,J-PHONE,SoftBank
&switchTemplate2=Softbank:Template;string;ソフトバンク
&userAgent3=au:User Agent;string;KDDI
&switchTemplate3=au:Template;string;au

各項目の説明です。

Target document IDs
  …MobileConverterの対象となるドキュメントID。
    入力したドキュメントIDよりも下位のドキュメントが対象となる。
    (コンマ「,」で複数のドキュメントIDを入力可)
    0を指定するとサイト全体が対象となる。
    
Mobile charset
  …標準の文字コードから変換したい文字コードを指定する。
    デフォルトはShift_JISに変換します。

Docomo:User Agent
  …DocomoのUser Agentを指定する。
    恐らく「Docomo」と入れておけば問題ないと思います。

Docomo:Template
  …Docomo用に使用したいテンプレートを指定する。

Softbank:User Agent
  …SoftbankのUser Agentを指定する。
    度々会社名が変わっているので「Vodafone,J-PHONE,SoftBank」と3種類を入力して
    いればいいと思います。

Softbank:Template
  …Softbank用に使用したいテンプレートを指定する。

au:User Agent
  …auのUser Agentを指定する。
    恐らく「KDDI」と入れておけば問題ないと思います。

au:Template
  …au用に使用したいテンプレートを指定する。

※なお、User AgentがDocomo、Softbank、auのどれにもあたらなかった場合、テンプ
  レートの切り替えも文字コードの変換も行われません。
  (テンプレートはドキュメントに設定しているものが使われます)


例-2)PC&携帯サイト対応(画像変換あり)
以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&targetDoc=Target document IDs;string;0
&mobileCharset=Mobile charset;string;Shift_JIS
&targetImgPath=Target Convert Image Path;string;assets/images/
&thumbImgPath=Save thumbnail path;string;assets/images/mcshumb/ 
&MaxImgX=Max image size-X;string;160 
&MaxImgY=Max image size-Y;string;140 
&userAgent1=Docomo:User Agent;string;Docomo
&switchTemplate1=Docomo:Template;string;ドコモ
&switchImgType1=Docomo:Image Type;string;gif
&userAgent2=Softbank:User Agent;string;Vodafone,J-PHONE,SoftBank
&switchTemplate2=Softbank:Template;string;ソフトバンク
&switchImgType2=Softbank:Image Type;string;jpg
&userAgent3=au:User Agent;string;KDDI
&switchTemplate3=au:Template;string;au
&switchImgType3=au:Image Type;string;png 


各項目の説明です。
(既に前の例で説明している項目の説明は省きます)

Target Convert Image Path
  …画像変換を行いたい画像のパスを指定する。
    「assets/images/」と設定した場合、MODxの管理画面の「リソース」→「ファイル
    管理」で「assets/images/」以下に画像をアップした際、自動的にjpg、png、gifの
    画像ファイルを生成して保存する。
    何も入力しないとすべてのディレクトリへアップする画像が変換対象となる。

Save thumbnail path
  …自動的に生成されたjpg、png、gifファイルは通常、アップロードされたファイルと
    同じディレクトリに生成されるが、この設定を行うと自動生成されるファイルは
    すべてここに生成される。
    なお、ディレクトリは事前に作成しておく必要がある。

Max image size-X
Max image size-Y
  …画像を自動生成する際、この指定サイズよりも画像が大きい場合、このサイズまで
    縮小される。
    0を指定すると縮小されない。

Docomo:Image Type
  …Docomoからのアクセスの場合gifファイルを表示する。

Softbank:Image Type
  …Softbankからのアクセスの場合jpgファイルを表示する。

au:Image Type
  …auからのアクセスの場合pngファイルを表示する。


※なお、例-1と同様にUser AgentがDocomo、Softbank、auのどれにもあたらなかった場
  合、テンプレートの切り替えも文字コードの変換も行われません。
  また画像の変換も行われません。
※画像変換を行いたい画像はMODx管理画面の「リソース」→「ファイル管理」からアップ
  する必要があります。
  (アップ時にjpg、png、gifの画像を自動生成するため)


例-3)Docomoメインで3キャリア携帯振り分け(画像変換あり)
以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&targetDoc=Target document IDs;string;7,18
&alwaysConvertCharset=Always conver charset;list;yes,no;yes &mobileCharset=Mobile charset;string;Shift_JIS
&targetImgPath=Target Convert Image Path;string;assets/images/
&thumbImgPath=Save thumbnail path;string;assets/images/mcshumb/ 
&MaxImgX=Max image size-X;string;160 
&MaxImgY=Max image size-Y;string;140 
&userAgent1=Softbank:User Agent;string;Vodafone,J-PHONE,SoftBank
&switchTemplate1=Softbank:Template;string;ソフトバンク
&switchImgType2=Softbank:Image Type;string;jpg
&userAgent2=au:User Agent;string;KDDI
&switchTemplate2=au:Template;string;au
&switchImgType3=au:Image Type;string;png 

各項目の説明です。
(既に前の例で説明している項目の説明は省きます)

Always conver charset
  …普段はUser Agentがマッチしないと文字コードの変換は行われないが、この設定を
   「yes」にすると常に文字コードの変換が行われる。

※上記設定ではドキュメントID「7,18」より下の階層がMobileConverterの対象になります。
※ドキュメントはDocomoからのアクセスを想定して作成します。
  (User AgentがSoftbankでもAuでもない場合、Docomo用のサイトが表示されます)
  

設定例-4)ブラウザごとのテンプレート振り分け
以下の設定をプラグインの「プラグイン設定」に貼り付ける。

&targetDoc=Target document IDs;string;0
&userAgent1=Firefox:User Agent;string;Firefox
&switchTemplate1=Firefox:Template;string;Firefox
&convertCharset1=Firefox:Convert charset;list;yes,no;no &userAgent1=Opera:User Agent;string;Opera
&switchTemplate1=Opera:Template;string;Opera
&convertCharset1=Opera:Convert charset;list;yes,no;no

各項目の説明です。
(既に前の例で説明している項目の説明は省きます)

Firefox:User Agent
  …FirefoxのUser Agentを指定する。

Firefox:Templaet
  …Firefox用のテンプレートを指定する。

Firefox:Convert charset
  …Firefoxの場合、文字コードを変更する必要がないのでここでnoを指定する。

Opera:User Agent
  …OperaのUser Agentを指定する。

Opera:Templaet
  …Opera用のテンプレートを指定する。

Opera:Convert charset
  …Operaの場合、文字コードを変更する必要がないのでここでnoを指定する。

※ドキュメントはIEからのアクセスを想定して作成しておきます。


4.設定完了
あとは実際にブラウジングして確認してみましょう。


[アンインストール]
1.MobileConverterプラグインを削除します。
 「リソース」→「リソース管理」→「プラグイン」→「MobileConverter」→「削除」

2.自動生成した画像の消去
  &thumbImgPathをしている場合はそのディレクトリ、指定していない場合は画像を
  アップしたディレクトリに「.mcthumb.〜」という画像ファイルがあるのですべて
  削除する。


[プラグイン設定補足]
前項ではプラグインの設定例としていくつか代表的なものを紹介しましたが、プラグイ
ンの設定で行える設定をここに全部あげておきます。
(プラグイン設定の詳細な使い方はMODxの他のドキュメントやWebサイトを参考にしてく
ださい…と言ってもあまり日本語で詳しい説明サイトがないのが現状なのですが…)


  &targetDoc
    …MobileConverterが有効になるドキュメントIDを指定します。
      指定したドキュメント配下にあるディレクトリもすべて対象になります。
      0ですべてのドキュメントが対象になります。
      また",(コンマ)"で複数のドキュメントIDを指定できます。

        例) 34,76,192

  &mobileCharset
    …MODxの規定の文字コードから変換したい文字コードを指定します。
      省略するとShift_JISになります。

  &alwaysConverCharset
    …本来はUser Agentが条件にマッチした場合に文字コード変換が行われるのです
      が、これをyesにすると常に対象ドキュメントの文字コードを「&mobileCharset」
      で設定した文字コードに変換します。

  &targetImgPath
    …画像変換の対象になるディレクトリを指定します。
      MODxの管理画面の「リソース」→「ファイル管理」でこのディレクトリ以下に画
      像がアップされたら、自動的にjpg、png、gifのファイルを生成します。
      この項目に何も入力しないとすべてのディレクトリが対象になります。
      画像の自動生成を無効にしたい場合、そもそもこの項目をプラグインの設定に含
      めません。
      また、「&thumbImgPath」が設定されていなかった場合、画像をアップしたディ
      レクトリと同じ階層にファイルが生成されます。
      
  &thumbImgPath
    …自動生成した画像の保存先を指定します。
      保存先のディレクトリは存在している必要があります。
      何も入力しなければ、自動生成されたファイルは画像をアップしたディレクトリ
      と同じ階層に生成されます。

  &MaxImgX
  &MaxImgY
    …画像を生成する際、この指定のサイズよりも画像が大きかったらこのサイズまで
      縮小して表示します。
      0を設定したら縮小を行いません。

  &userAgent[NUM]
  &switchTemplate[NUM]
  &convertCharset[NUM]
  &switchImgType[NUM]
    …「&userAgent」でテンプレートを振り分ける条件となるUser Agentを指定します。
       User Agentの指定は「,(コンマ)」区切りで複数していすることができ、大文字
      小文字は区別しません。
      「&switchTemplate」は「&userAgent」にマッチしたときに振り分けるテンプレー
      トを指定します。
      「&convertCharset」は「&userAgent」にマッチしたときに文字コードの変換を行
      うかどうかの設定を行います。yesで変換を行い、noで変換を行いません。
       (デフォルトで文字コードは変換する)。
      「&switchImgType」は「&userAgent」にマッチしたときに出力するHTMLの中にIMG
       タグがあった場合、その中の画像をここで指定した拡張子に変換します。
       なお、変換対象の画像は「&targetImgPath」で設定したディレクト以下にある画
       像になります。何も入力していないと変換は行われません。      
       [NUM]に数字を1から順番に振っていくことで複数の振り分け設定を書くことが
       できます。また数字は必ず1から始め、連番になっている必要があります。




[注意事項]
1.MobileConverterの対象になったドキュメントのキャッシュ機能は強制手的にOFFにな
  ります。
  (じゃないとテンプレート切り替えられないので)

2.このプラグインは出力されるHTMLの文字コードをShift_JIS等に変換できますが、HTML
  のMETAタグに書かれるcharsetは変換しません。
  そのためShift_JISで出力するつもりのテンプレートのcharsetはあらかじめShift_JIS
  と書いておく必要があります。

  例)
    <meta http-equiv="Content-Type" content="text/html; charset=Shift_JIS" />

3.画像変換について
  画像変換は出力するHTMLからIMGタグを引っ張り出して、中のパスを変換しています。
  その中でもプラグイン設定の「Target Convert Image Path(&targetImgPath)」で指定
  したディレクトリ以下の画像が変換対象になります。
  そのためIMGタグで相対パスを使ったリンクを使用している場合、うまく変換できませ
  ん。
  (相対パスだとプラグイン設定で指定したパスと照らし合わせることができないため)
  このあたりは仕様となるのでうまくあわせてもらうしかないと思います…。

  推奨運用は以下のようなHTMLを出力するようにする。
  (説明に不要なタグは省略しています)

  <html>
  <head>
    <base href="http://YOUR_MODX_SITE.jp/path/"></base>
  </head>
  <body>
    <img src="assets/images/test01.jpg" alt="image">
  </body>
  </html>

  baseタグでサイトのベースURLを決めて、画像はそこからのパスを指定する。という
  感じです。
  MODxのサンプルサイトもこのbaseタグ使ってます。

  また画像変換した後のIMGタグではwidthとheight設定が消えてしまいます。
  画像のサイズによってはサイズの変更が伴うので、わざと消しています。
  ページアクセス時に毎回画像を読み込んでwidthとheightを調べて出力してもいいの
  ですが、負荷が気になるところです。
  今後の要望しだいで実装する…かもしれまえん。
  

[更新履歴]
2008/03/23 - 1.10
  ・MODxの管理画面から画像ファイルをアップする際、自動的にjpg、png、gifの3タイ
    プに変換して保存する機能を追加。
  ・各User AgentごとにHTML内のIMGタグで使用している画像を任意のjpg、png、gifに
    変換する機能を追加。
  ・テンプレートがなかった際、エラー用のテンプレートを出すように修正。
  ・User Agentか空の場合にエラーになっていたのを修正。

2008/03/09 - 1.00
  ・リリース

