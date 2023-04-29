# はじめに

- 有志勉強会第三回です
- 今回は「EC2やコンテナにどの様にアクセスが振り分けられているのかが理解できる」状態をゴールにしたいと思います

## ロードバランサー

- ロード(load、負荷)＋バランサー(Balancer、平衡を保つためのもの)で、サーバーやネットワークに関連する用語であり、装置の名称
- この仕組みにより、Webサイトへのアクセス集中やサーバー故障などの場合でも、アクセス中の利用者に安定したサービス提供を継続可能に
- Webサーバーへの割り振り方・内容によって、L4やL7のロードバランスと呼ばれる
  - L4：IPアドレスとポート番号によって割り振る
  - L7：通信内容に応じて、リクエストを特定のサーバー群に割り振る

<https://www.kagoya.jp/howto/it-glossary/network/loadvalancer/>

### ロードバランサーの役割と機能

- 速度低下を防ぐ
  - 複数台へのサーバーへの分散が可能なので
- ダウンしたサーバーへ送らない
  - 死活監視により、正常でないサーバーにはアクセスを割り振らないように
- メンテナンス時
  - メンテナンス時のサーバーにアクセスを割り振らないように設定
- 特定のユーザーのアクセスを同じサーバーへ
  - Webアプリケーションのトランザクションの整合性（セッション）を維持するために設定できる。
  - パーシステンスと呼ばれる

## AWSのロードバランサー

- ELB(Elastic Load Balancing)というサービス名で提供されている
- AWS上ではEC2のマネジメントコンソール内に存在
  - `EC2 > ロードバランシング > ロードバランサ―`
- 本日紹介するのはALBとNLB

<https://qiita.com/suzuki-navi/items/a8f4e21d75e685df51e9>

### リスナー

- ポートとプロトコルで接続リクエストをチェックする
- リスナーのルールに従って、受信トラフィックはルーティングされる
- リスナープロトコルがHTTPSの場合は、SSLサーバー証明書をリスナーにデプロイする
  - これにより、接続クライアント⇔ロードバランサー間の通信を暗号化する

### ターゲットグループ

- 既存のロードバランサーに関連付けて利用する
- 一つ以上の登録されているターゲット（宛先）にリクエストをルーティングする為に利用

### ヘルスチェック

- ターゲットグループに登録された各ターゲットのステータスをテストする機能
- 定期的にリクエストを送信したり、ターゲットの接続への応答状態を確認する事で、ステータスを確認
  - 定期的にリクエストを送信し確認する場合はヘルスチェック用のパスを設定
- ターゲットグループ単位で定義

## ALB(Application Load Balancer)

- HTTPやHTTPSのトラフィックに対応する単一のロードバランサー
  - アプリケーション層（L7）で機能する
- トラフィックのコンテンツに基づいてリクエストを転送するリスナーのルールを設定できる
  - リスナーでは、リクエストURLのパス、HTTPSメソッド、送信元IPアドレス等の条件に応じて、柔軟に転送先を設定できる
- WebSocketにも対応
  - 双方向通信を低コストで行うための仕組み（もともとHTML5の使用の一部として策定されたプロトコル）
  - 一度コネクションを確立すると、その後の通信を専用プロトコルで行う
- セキュリティグループを関連付けできる
- IPは固定できない

## NLB(Network Load Balancer)

- TCPやUDPのトラフィックに対応するロードバランサ
  - トランスポート層（L4）で機能する
- リスナールールはデフォルトのアクションのみ設定できる
- クライアントの送信元IPアドレス・ポートをターゲットまで保持できる
- セキュリティグループを関連付けできない
  - インターネットからNLBに流入するトラフィックを制限するには、NLBのバックエンドに配置したインスタンスに関連付けるセキュリティグループによって制御する
- IPをAZ(アベイラビリティゾーン)別に固定できる

## その他

### Sticky Sessions

- Cookieを設定し、それを基にアクセス先Webサーバーを振り分け