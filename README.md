## このシステムについて

このシステムは[株式会社レイヤード](https://layered.inc/)と、[おおくぼ総合内科クリニック](https://ohkubo-med.jp/)　院長 大久保辰雄医師による共同開発サービスです。

## システム概要

+ このシステムは医療機関において患者からの問診を聴取するためのシステムです。
+ 当システムは全てクラウドサービスです。システムを利用するためにインターネット環境が必要です。
+ 当システムでは、患者が問診を入力する機能、医療機関側で患者の問診内容を確認するための機能、その他設定機能よりなっています。

## 利用規約
[システムの利用規約（医療機関等向け）](https://layered-inc.github.io/symview-description/TOS.html)

## 問診結果による推論について

当システムでは、患者の問診回答内容に応じて、自動的に質門が遷移し、問診結果に応じて可能性の高い疾患の推論を行っています。
推論のアルゴリズムは下記より成り立っています。


#### 疾患マスタ
+ 当システムでは、内科及び小児科の診療所／クリニックで遭遇する可能性のある疾患を中心に約600疾患（2017年11月現在）をマスタデータとして格納しています。
+ 疾患マスタでは、各疾患の症状や症状の関連度、年代や性別ごとの発生割合などのデータを保持しています。


#### 基礎点数
+ 各疾患の発生頻度を表すデータとして、疾患ごとに基礎点数という数値をデータとして持っています。
+ 基礎点数は０点から１００点までの範囲で点数付けを行います。０点はそのクリニックで発見する可能性が限りなく薄い疾患、１００点は日常的に遭遇する疾患となります。
+ 標榜する診療科目や地域性などの違いにより、医療機関ごとに疾患の発生頻度は異なる可能性があるため、基礎点数は医療機関ごとに設定できるようになっています。

#### 主訴カテゴライズ
+ 当システムでは、患者が主訴として訴える症状を、症状とは別に主訴カテゴライズとしてデータを持っています。
+ 患者が選んだ最初の主訴カテゴライズに基づき、疾患の絞込が行われます。
+ 主訴カテゴライズは医療機関ごとに何を採用するか、選ぶことが出来ます。

#### 発症様式
+ 各疾患は、急性、あるいは慢性の疾患（症状の現れ方も含む）のデータを持っており、患者の回答する症状の発症様式に基づき、疾患の絞込が行われます。
+ 疾患は慢性であっても、急性での症状が現れる可能性がある場合には、急性、慢性両方のデータを保有しています。

#### 影響要素
+ 各疾患の発生頻度（基礎点数）は、（１）患者の年齢、（２）患者の性別、（３）受診時の季節、により影響を受けます。
+ 各疾患のマスタデータにおいて影響要素ごとのオッズ比を持ち、患者の回答状況に応じて該当のオッズ比を利用します。
+ 年齢の影響要素は、（１）乳幼児（０〜６歳）、（２）小児(７歳〜１７歳）、（３）成人（１８歳〜５９歳）、（４）老人（６０歳〜）の４区分に分類しています
+ 季節の影響要素は、（１）１２月〜２月、（２）３月〜５月、（３）６月〜８月、（４）９月〜11月、の４区分に分類しています。

#### 鑑別質問
+ 当システムは、疾患を推論するための質問をデータとして持っています。
+ 各質問は、回答内容に応じて、疾患の確率に影響を与える尤度比のデータを持っています。

#### 推論ロジック
+ 疾患の推論は、ベイズ統計学にもとづいて計算が行われています。
+ 主訴カテゴライズに基づいて絞り込まれた疾患に対して、基礎点数、およびそこに影響を与える影響様子のオッズ比に基づき事前確率が計算されます。
+ 事前確率に対して、随伴する症状や、その他の鑑別質問などの質問を通じて、疾患の確率が上下します。
+ 全ての質問が完了した時点で、事前確率、および各質問の尤度比の設定に応じて疾患ごとの問診事後確率が計算されます。
+ 尤度比は、簡易的に＋３点〜−３点までの設定としており、尤度比点数は下記の通り扱っています。

<table>
  <tr>
    <th>尤度比点数</th>
    <th>尤度比</th>
    <th>確率変動（簡易）</th>
    <th>備考</th>
  </tr>
  <tr>
    <td>+3</td>
    <td>10</td>
    <td>+45%</td>
    <td>SpPinな質問</td>
  </tr>
  <tr>
    <td>+2</td>
    <td>5</td>
    <td>+30%</td>
    <td></td>
  </tr>
  <tr>
    <td>+1</td>
    <td>2</td>
    <td>+15%</td>
    <td></td>
  </tr>
  <tr>
    <td>-1</td>
    <td>0.5</td>
    <td>1/2</td>
    <td></td>
  </tr>
  <tr>
    <td>-2</td>
    <td>0.2</td>
    <td>1/5</td>
    <td></td>
  </tr>
  <tr>
    <td>-3</td>
    <td>0.1</td>
    <td>1/10</td>
    <td>SnOutな質問</td>
  </tr>  
</table>
※実際には簡易計算法ではなく、ベイズの定理に基づく正式な計算式で計算を行っています。



## 参考文献
このシステムの開発にあたって参考とした文献は下記の通りです。
* [診断推論と確率ーベッドサイドでのベイズの定理ー 日本プライマリ・ケア連合学会誌,2013,vol.36 no.3 p.191-197 ](https://www.jstage.jst.go.jp/article/generalist/36/3/36_191/_pdf)
* マクギーの身体診断学 改定第2版(Steven McGee, MD) 診断と治療社
* The 臨床推論（大西弘高 編）　南山堂
* かぜ診療マニュアル第2版　（山本舜悟　編）　日本医事新報社
* 抗微生物薬適正使用の手引 第1版　厚生労働省健康局結核感染症課
* ワシントンマニュアル 第13版 (Hemant Godara, MD/Angela Hirbe, MD/ Michael Nassif,MD他）　メディカル・サイエンス・インターナショナル
* 今日の治療指針 2015 (山口徹 北原光夫 監修）　医学書院
* プライマリ・ケア医に贈るパール563　(Lawrence M.Tierney,Jr. , Sanjay Saint, Mary A. Whooley 編）　日経BP社
* プライマリ・ケアの現場で役立つ一発診断100 （宮田靖志／中川紘明） 文光堂
* ジェネラリストのための内科外来マニュアル（金城光代／金城紀与史／岸田直樹） 医学書院
* かぜとかぜにみえる重症疾患の見わけ方 かぜ診療マニュアル 第2版 （山本舜悟 編著） 日本医事新報社
* ケースブック　問診と身体所見でここまでわかる！ (高久史麿／箕輪良行　監訳）　メディカル・サイエンス・インターナショナル
* 総合外来初診の心得21か条 (福井次矢／小泉俊三／伴信太郎　監修）　医学書院
* 見逃し症例から学ぶ日常診療のピットフォール（生坂政臣)　医学書院
* 早わざ外来診断術（生坂政臣 監訳）　中山書店
* めざせ！外来診療の達人 第3版 (生坂政臣）　日本医事新報社
* 週刊日本医事新報　No.4644　今日読んで、明日からできる　診断推論 (野口善令 編)　日本医事新報
* 内科で診る不定愁訴 (加藤温 監修）　中山書店
* コモンディジーズブック　日常外来での鑑別と患者への説明のために　一般社団法人日本内科学会専門医部会
* 増刊レジデントノート　診断の決め手を各科の決め手を各科専門医が教えます(大西弘高／福士元春／木村琢磨 編)　羊土社
* 総合小児医療　初期診療を磨くセンスとサイエンス　（田原卓浩 総編集）　中山書店
