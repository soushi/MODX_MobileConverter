MobileConverter for MODx

[機能]
PCや携帯端末から同じドキュメントにアクセスした際に、User Agentを元に文字コード
変換&テンプレートの切り替えを行います。
また別な用途としてIE、Firefox、Opera等ブラウザごとに違うテンプレートを表示した
いときにも利用できます。

[動作確認環境]
・MODx0.9.6(UTF-8)

・PHP5.2.4
  PHP4系は未検証
・MySQL 5.0.41

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
                        詳しくは語述します(3.プラグイン設定)。

    (システムイベント)
    次のイベントをクリックしてチェックを付けてください。

      ・OnWebPageInit

3.プラグイン設定
本プラグインの動作はここで制御します。
(プラグイン設定の詳細な使い方はMODxの他のドキュメントやWebサイトを参考にしてく
ださい…と言ってもあまり日本語で詳しい説明サイトがないのが現状なのですが…)

以下プラグイン設定に入れることができる設定項目です。
※下のほうに実際の設定例があります。

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


  &userAgent[NUM]
  &switchTemplate[NUM]
  &convertCharset[NUM]
    …「&userAgent」でテンプレートを振り分ける条件となるUser Agentを指定します。
       User Agentの指定は「,(コンマ)」区切りで複数していすることができ、大文字
      小文字は区別しません。
      「&switchTemplate」は「&userAgent」にマッチしたときに振り分けるテンプレー
      トを指定します。
      「&convertCharset」は「&userAgent」にマッチしたときに文字コードの変更を行
      うかどうかの設定を行います。yesで変更を行い、noで変更を行いません。
       (デフォルトで文字コードは変更する)。       
       [NUM]に数字を1から順番に振っていくことで複数の振り分け設定を書くことが
       できます。また数字は必ず1から始め、連番になっている必要があります。

設定例-1)PC&3キャリア携帯振り分け
  ・対象ドキュメントにはPC用のテンプレートが設定してある。
  ・サイト全体がMobileConverterの対象
  ・ドコモ携帯の場合はShift_JISに変換し、「ドコモ」テンプレートに切り替える。
  ・ソフトバンク携帯の場合はShift_JISに変換し、「ソフトバンク」テンプレートに
    切り替える。
  ・AU携帯の場合はShift_JISに変換し、「AU」テンプレートに切り替える。
  ・どこにもマッチしなければPC用のテンプレートを表示する。

&targetDoc=Target document IDs;string;0
&mobileCharset=Mobile charset;string;Shift_JIS
&userAgent1=Docomo:User Agent;string;Docomo
&switchTemplate1=Docomo:Template;string;ドコモ
&userAgent2=Softbank:User Agent;string;Vodafone,J-PHONE,SoftBank
&switchTemplate2=Softbank:Template;string;ソフトバンク
&userAgent3=au:User Agent;string;KDDI
&switchTemplate3=au:Template;string;au


設定例-2)PC&携帯振り分け
  ・対象ドキュメントにはPC用のテンプレートが設定してある。
  ・ドキュメントIDの10以下がMobileConverterの対象
  ・ドコモ携帯、ソフトバンク、AUの場合はShift_JISに変換し、「携帯用」テンプ
    レートに切り替える。
  ・どこにもマッチしなければPC用のテンプレートを表示する。

&targetDoc=Target document IDs;string;10
&mobileCharset=Mobile charset;string;Shift_JIS
&userAgent1=Mobile:User Agent;string;Docomo,Vodafone,J-PHONE,SoftBank,KDDI
&switchTemplate1=Mobile:Template;string;携帯


設定例-3)ドコモメインで3キャリア携帯振り分け
  ・対象ドキュメントにはドコモ携帯用のテンプレートが設定してある。
  ・ドキュメントIDの7と18以下がMobileConverterの対象
  ・対象ドキュメントは常にShift_JISに変換される。
  ・ソフトバンク携帯の場合は「ソフトバンク」テンプレートに切り替える。    
  ・AU携帯の場合は「AU」テンプレートに切り替える。
  ・どこにもマッチしなければドコモ携帯用のテンプレートを表示する。

&targetDoc=Target document IDs;string;7,18
&alwaysConvertCharset=Always conver charset;list;yes,no;yes &mobileCharset=Mobile charset;string;Shift_JIS
&userAgent1=Softbank:User Agent;string;Vodafone,J-PHONE,SoftBank
&switchTemplate1=Softbank:Template;string;ソフトバンク
&userAgent2=au:User Agent;string;KDDI
&switchTemplate2=au:Template;string;au


設定例-4)PC&WindowsCE(Windowsモバイル)振り分け
  ・対象ドキュメントにはPC用のテンプレートが設定してある。
  ・ドキュメントIDの6以下がMobileConverterの対象
  ・Windows CEからのアクセスの場合は「WindowsCE用」テンプレートに切り替える。
    (特にShift_JIS変換は行わない)
  ・どこにもマッチしなければPC用のテンプレートを表示する。

&targetDoc=Target document IDs;string;6
&userAgent1=WindowsCE:User Agent;string;Windows CE
&switchTemplate1=WindowsCE:Template;string;WindowsCE
&convertCharset1=WindowsCE:Convert charset;list;yes,no;no


設定例-5)ブラウザごとのテンプレート振り分け
  ・対象ドキュメントにはIE用のテンプレートが設定してある。
  ・サイト全体がMobileConverterの対象
  ・Firefoxからのアクセスの場合は「Firefix用」テンプレートに切り替える。
    (特にShift_JIS変換は行わない)
  ・Operaからのアクセスの場合は「Opera用」テンプレートに切り替える。
    (特にShift_JIS変換は行わない)
  ・どこにもマッチしなければIE用のテンプレートを表示する。

&targetDoc=Target document IDs;string;0
&userAgent1=Firefox:User Agent;string;Firefox
&switchTemplate1=Firefox:Template;string;Firefox
&convertCharset1=Firefox:Convert charset;list;yes,no;no &userAgent1=Opera:User Agent;string;Opera
&switchTemplate1=Opera:Template;string;Opera
&convertCharset1=Opera:Convert charset;list;yes,no;no


[注意点]
このプラグインは出力されるHTMLの文字コードをShift_JIS等に変換できますが、HTMLの
METAタグに書かれるcharsetは変換しません。
そのためShift_JISで出力するつもりのテンプレートのcharsetはあらかじめShift_JISと
書いておく必要があります。

  例)
    <meta http-equiv="Content-Type" content="text/html; charset=Shift_JIS" />

[更新履歴]
2008/03/09 - 1.00
  ・リリース

