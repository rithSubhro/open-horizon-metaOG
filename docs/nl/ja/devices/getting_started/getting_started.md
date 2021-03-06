---

copyright:
years: 2019
lastupdated: "2019-08-01"

---

{:new_window: target="blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:child: .link .ulchildlink}
{:childlinks: .ullinks}

# 始めに
{: #getting_started}

以下のトピックを確認して、エッジ・ソフトウェアおよびサービスを管理する方法など、{{site.data.keyword.edge_devices_notm}} の使用を開始する上で役立ててください。
{:shortdesc}

{{site.data.keyword.edge_devices_notm}} デバイスに必要なソフトウェアのインストールについて詳しくは、以下のトピックを参照してください。

* [エッジ・デバイスの準備](../installing/adding_devices.md)
* [Hello World サンプルを使用したエッジ・デバイスでの {{site.data.keyword.horizon_agent}}のインストールと登録](../installing/registration.md)
* [エッジ・サービスの追加サンプル](../installing/additional_examples.md)
  * [ホスト CPU 負荷率のサンプル (cpu2evtstreams)](../installing/cpu_load_example.md)
  * [Hello world](policy.md)
  * [ソフトウェア無線のエッジ処理](../installing/software_defined_radio_ex.md)
  * [オフライン音声アシスタント (processtext)](../installing/offline_voice_assistant.md)
  * [Watson speech to text](../installing/watson_speech.md)
* [hzn コマンドの探索](../installing/exploring_hzn.md)

## {{site.data.keyword.edge_devices_notm}} の仕組みの概要
{: #overview}

{{site.data.keyword.edge_devices_notm}} は、デプロイメント・リスクを最小化し、エッジ・ノードのサービス・ソフトウェア・ライフサイクルを完全に自律的に管理するために、エッジ・ノード管理専用に設計されています。
{:shortdesc}

## {{site.data.keyword.edge_devices_notm}} のアーキテクチャー

{: #iec4d_arch}

その他のエッジ・コンピューティング・ソリューションでは通常、以下のアーキテクチャー戦略の 1 つに焦点を合わせます。

* エッジ・ノード・ソフトウェアのコンプライアンスを適用するための強力な中央集中権限。
* エッジ・ノード所有者へのソフトウェア・コンプライアンスの責任の移譲。エッジ・ノード所有者は、ソフトウェア更新をモニターし、手動で自身のエッジ・ノードをコンプライアンスを満たした状態にする必要があります。

前者では、権限が中央集中化されるため、単一障害点が生じ、攻撃者が全エッジ・ノードを制御するために悪用できるターゲットとなってしまいます。 後者のソリューションでは、エッジ・ノードの大部分で最新のソフトウェア更新がインストールされていない状態になる可能性があります。 エッジ・ノードの一部が最新バージョンでない場合や、使用可能なすべての修正が適用されていない場合、エッジ・ノードは攻撃者に対して脆弱になる可能性があります。 どちらの方法も通常、信頼の確立の基盤として、中央の権限に依存します。

<p align="center">
<img src="../../images/edge/overview_illustration.svg" width="70%" alt="エッジ・コンピューティングのグローバルな範囲を示す図。">
</p>

上記のソリューションのアプローチとは対照的に、{{site.data.keyword.edge_devices_notm}} は非集中型です。 {{site.data.keyword.edge_devices_notm}} は、手操作による介入なしで、エッジ・ノード上のサービス・ソフトウェアのコンプライアンスを自動的に管理します。 各エッジ・ノードでは、完全に自律的な非集中型エージェント・プロセスが、{{site.data.keyword.edge_devices_notm}} へのマシンの登録時に指定されたポリシーに従って実行されます。 完全に自律的な非集中型 agbot (合意ボット) のプロセスは通常、中央の場所で実行されますが、エッジ・ノード上も含め、任意の場所で実行できます。 エージェント・プロセスと同様に、agbot はポリシーによって制御されます。 エージェントおよび agbot は、エッジ・ノードのエッジ・サービス・ソフトウェア・ライフサイクル管理のほとんどを処理し、エッジ・ノードでソフトウェア・コンプライアンスを適用します。

効率性のため、{{site.data.keyword.edge_devices_notm}} には、2 つの集中型サービス (exchange と switchboard) が含まれています。 これらのサービスには、自律型のエージェントおよび agbot のプロセスに対する中央の権限はありません。 代わりに、これらのサービスは、シンプルなディスカバリー・サービスおよびメタデータ共有サービス (exchange) と、ピアツーピア通信をサポートするためのプライベート・メールボックス・サービス (switchboard) を提供します。 これらのサービスでは、エージェントおよび agbot の完全に自律的な処理がサポートされます。

最後に、{{site.data.keyword.edge_devices_notm}} コンソールは、管理者がポリシーを設定し、エッジ・ノードの状況をモニターするのに役立ちます。

5 つの {{site.data.keyword.edge_devices_notm}} コンポーネント・タイプ (エージェント、agbot、exchange、switchboard およびコンソール) のそれぞれに、制限された責任領域があります。 各コンポーネントは、それぞれの責任領域の外で動作するための権限や資格情報を備えていません。 責任を分割し、権限と資格情報の範囲を定めることで、{{site.data.keyword.edge_devices_notm}} は、エッジ・ノード・デプロイメントのリスク管理を提供します。

## ディスカバリーおよびネゴシエーション
{: #discovery_negotiation}

{{site.data.keyword.edge_devices_notm}} は、[1{{site.data.keyword.horizon_open}} ![新しいタブで開く](../../images/icons/launch-glyph.svg "新しいタブで開く")](https://github.com/open-horizon/) プロジェクトに基づいており、主に非集中の分散型です。 自律的なエージェントおよび合意ボット (agbot) のプロセスが、すべての登録されているエッジ・ノードのソフトウェア管理についてコラボレーションします。

自律的なエージェント・プロセスは、各 Horizon エッジ・ノードで実行され、エッジ・デバイス所有者によって設定されたポリシーを適用します。

自律的な agbot は、exchange 内のデプロイメント・パターンおよびポリシーをモニターし、まだ準拠していないエッジ・ノード・エージェントを探します。 agbot は、準拠している状態にするためにエッジ・ノードに合意を提案します。 agbot とエージェントが合意に達すると、エッジ・ノード上のエッジ・サービスのソフトウェア・ライフサイクルを共同で管理するようになります。

agbot とエージェントは、以下の中央集中サービスを使用して、{{site.data.keyword.edge_devices_notm}} で相手を検索し、信頼を確立して、セキュアに通信します。

* {{site.data.keyword.horizon_exchange}}。ディスカバリーを支援します。
* {{site.data.keyword.horizon_switch}}。agbot とエージェントの間のセキュアなピアツーピアのプライベート通信を可能にします。

<img src="../../images/edge/distributed.svg" width="90%" alt="中央集中サービスおよび非集中型のサービス">

### {{site.data.keyword.horizon_exchange}}
{: #iec4d_exchange}

{{site.data.keyword.horizon_exchange}} により、エッジ・デバイス所有者は、ソフトウェア・ライフサイクル管理用にエッジ・ノードを登録できます。 {{site.data.keyword.edge_devices_notm}} の {{site.data.keyword.horizon_exchange}} にエッジ・ノードを登録する際には、エッジ・ノードのデプロイメント・パターンまたはポリシーを指定します。 (本質的に、デプロイメント・パターンとは、単にエッジ・ノードを管理するためのポリシーの事前定義された名前付きセットです。) デプロイメント・パターンおよびポリシーは、設計、開発、テスト、署名が済んでおり、さらに {{site.data.keyword.horizon_exchange}} に公開されている必要があります。

各エッジ・ノードは、固有の ID およびセキュリティー・トークンで登録されます。 ノードは、自分の組織から提供されているパターンまたはポリシーを使用するように、あるいは別の組織から提供されているパターンを使用するように登録できます。

パターンまたはポリシーが {{site.data.keyword.horizon_exchange}} に公開されると、agbot は新規または更新されたパターンまたはポリシーの影響を受けるエッジ・ノードを検出します。 登録されているエッジ・ノードが見つかると、agbot はエッジ・ノード・エージェントとネゴシエーションします。

{{site.data.keyword.horizon_exchange}} は、デプロイメント・パターンまたはポリシーを使用するように登録されているエッジ・ノードを agbot が検出できるようにしますが、{{site.data.keyword.horizon_exchange}} は、エッジ・ノード・ソフトウェア管理プロセスに直接は関与しません。 agbot およびエージェントが、ソフトウェア管理プロセスを処理します。 {{site.data.keyword.horizon_exchange}} は、エッジ・ノードに対する権限を一切備えておらず、エッジ・ノード・エージェントとの通信を開始することもありません。

### {{site.data.keyword.horizon_switch}}
{: #horizon_switch}

agbot は、新規または更新されたパターンまたはポリシーの影響を受けるエッジ・ノードを検出すると、{{site.data.keyword.horizon}} switchboard を使用して、そのノード上のエージェントにプライベート・メッセージを送信します。 このメッセージは、エッジ・ノードのソフトウェア・ライフサイクル管理を共同で行うことに合意するよう提案するものです。 {{site.data.keyword.horizon_switch}} のプライベート・メールボックスで agbot からのメッセージを受信すると、エージェントはそのメッセージを復号して提案を評価します。 提案が自身のノード・ポリシーの範囲内にある場合、ノードは受諾メッセージを agbot に送信します。 そうでない場合、ノードは提案を拒否します。 agbot が {{site.data.keyword.horizon_switch}} のプライベート・メールボックスで合意の受諾を受信すると、ネゴシエーションは完了します。

エージェントと agbot はいずれも、公開鍵を {{site.data.keyword.horizon_switch}} にポストすることで、Perfect Forward Secrecy を使用するセキュアなプライベート通信ができるようにします。 この暗号化では、{{site.data.keyword.horizon_switch}} はメールボックス・マネージャーの役割のみを果たします。 メッセージを復号することはできません。

注: すべての通信は {{site.data.keyword.horizon_switch}} によって仲介されるため、エッジ・ノードの IP アドレスは、各エッジ・ノード上のエージェントがその情報を公開するまで、agbot に対して公開されません。 エージェントは、エージェントと agbot が合意のネゴシエーションに成功したときにその情報を公開します。

## エッジ・ソフトウェア・ライフサイクル管理
{: #edge_lifecycle}

agbot とエージェントが特定のパターンまたはポリシーのセットに対する合意に達した後は、エッジ・ノード上のパターンまたはポリシーのソフトウェア・ライフサイクルを共同で管理するようになります。 agbot は、時間の経過と共に変化するパターンまたはポリシーをモニターし、エッジ・ノードの準拠性をモニターします。 エージェントは、ソフトウェアをエッジ・ノード上にローカルでダウンロードし、ソフトウェアの署名を検証し、検証された場合はソフトウェアを実行してモニターします。 必要に応じて、エージェントはソフトウェアを更新し、適切な場合はソフトウェアを停止します。

エージェントは、指定されたエッジ・サービスの Docker コンテナー・イメージを適切なレジストリーからプルし、コンテナー・イメージの署名を検証します。 その後、エージェントは、パターンまたはポリシーに指定された構成を使用して、逆の依存関係の順番でコンテナーを開始します。 コンテナーが実行中の場合、ローカル・エージェントはコンテナーをモニターします。 コンテナーが予期せず実行を停止した場合、エージェントはコンテナーを再起動して、エッジ・ノードでパターンまたはポリシーが準拠されるようにします。

エージェントの障害に対する許容度は限定されています。 コンテナーが繰り返しすぐに異常終了する場合、エージェントは絶えず障害が発生しているサービスの再始動の試行を停止し、合意を取り消します。

### {{site.data.keyword.horizon}} サービスの依存関係
{: #service_dependencies}

エッジ・サービスは、使用する他のエッジ・サービスへの依存関係をメタデータ内に指定できます。 エッジ・サービスがパターンまたはポリシーの結果としてエッジ・ノードにデプロイされると、エージェントはそのエッジ・サービスが必要とするすべてのエッジ・サービスも (逆の依存関係の順番で) デプロイします。 サービスの依存関係のレベルはすべてサポートされます。

### {{site.data.keyword.horizon}} Docker ネットワーキング
{: #docker_networking}

{{site.data.keyword.horizon}} は、Docker ネットワーキング機能を使用して、特定の Docker コンテナーを必要とするサービスのみがそのコンテナーに接続できるように Docker コンテナーを分離します。 別のサービスに依存するサービス・コンテナーが開始されると、そのサービス・コンテナーは従属サービス・コンテナーのプライベート・ネットワークに接続されます。 これにより、異なる組織によって作成されたエッジ・サービスの実行が容易になります。 これは、各エッジ・サービスが、自身のメタデータにのみリストされている他のサービスにアクセスできるためです。
