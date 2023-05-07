# はじめに

- 有志勉強会第四弾です
- 今回は「マイクロサービスパターン[実践的システムデザインのためのコード解説] 」といった書籍を基にしたマイクロサービスについての解説です。

**注意点**

- マイクロサービスはあくまでアプリケーションのアーキテクチャの一形態、またはその開発手法を指します
-  なので「これが正解」ではなく「こういった形もある」といった土台を作る事が今回の勉強会の目的です。
-  マイクロサービスの概念の内、既存アプリケーションに対して部分的に適応していくといった事も可能なので、アプリケーション設計のヒントになれば、と考えています。

## 話す事

- モノリスとマイクロサービスについて
- マイクロサービスの概要とそのメリット・デメリット
- マイクロサービスが開発組織に与える影響

## 話さない事

- 具体的なサービス間通信の実装

## マイクロサービスについて

### モノリスとマイクロサービス

マイクロサービスを理解する上で、まずはモノリスという概念について説明します。

- モノリス（モノリシック・アーキテクチャ）とは


### マイクロサービスの特徴

- マイクロサービスはモジュール性の一形態
  - サービスがお互いに疎結合で、API以外では通信できない
  - 個々のサービスは専用のDBを持つ
    - オーダーサービスはORDERSテーブルを含む専用DB、顧客サービスはCUSTOMERSテーブルを含む専用DBを持つ
    - 外部アプリはAPI（REST、gRPC）を介してしか該当DBの情報を取得できない
  - 他のサービスと調整しなくとも、サービスのスキーマを変更できる。
    - 各サービスは、DDDのサブドメインに対応している。
  - サービスは独立したリポジトリ、パイプラインを持つ

### マイクロサービスの利点

- 大規模で複雑なアプリケーションの継続的デリバリ／デプロイを可能にする
  - 個々のサービスが小さく、テストが書きやすく、短時間で実行される
  - 他のサービスから独立して、個別にデプロイできる
  - ビジネス要件として、素早い要望への対応が可能で、信頼性の高いアプリケーションに
- 個々のサービスが小さく、簡単にメンテナンスできる
  - コードを理解しやすく、IDEが遅くなることもない
- サービスをそれぞれ個別にデプロイ、スケーリングできる
  - リソース要件に応じてスケール。（ハード面を考慮し）
- チームに自主性、自立性を与える
  - two piza teamを構成できる。独立して開発できるので、開発速度が早い
- 障害分離に優れている
  - 他のサービスに影響を与えない（一部分のダウンで済む）
- 新しいテクノロジを簡単に実験、採用できる
  - テクノロジスタックに長期に縛られない
  - 最初に選択したテクノロジに縛られる事がない

### マイクロサービスの欠点

- サービスの適切な分割方法を見つけるのが難しい
  - 分割方法を間違えると、一緒にデプロイする必要のある蜜結合のサービスが生まれてしまう
- 分散システムは複雑になる
  - メソッドの呼び出しよりも複雑なプロセス間通信が必要
  - プロセス間通信の失敗の処理などが必要
  - IDEはモノシリックアプリケーションの開発を対象としている事が多い
  - 高度なソフトウェア開発、デリバリースキルが必要

## ソフトウェア開発と組織

### コンウェイの法則

- 「システムを設計する組織は、組織のコミュニケーション構造をコピーしたような設計しか作れない」
- 一つの大規模なチームよりも、複数の小さなチームの方が作業速度が速くなる
  - チームとチームが担当するサービスを分割し、コミュニケーションのオーバーヘッドを避ける

### デリバリープロセス

- マイクロサービスをウォーターフォールで開発すると、開発速度の速さが打ち消されてしまう
  - Scrumやカンバン等アジャイルを取り入れる必要がある
- 継続的デリバリーを取り入れる事も重要で、自動テストや自動デプロイといった自動化が必要