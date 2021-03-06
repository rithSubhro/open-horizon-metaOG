---

copyright:
years: 2020
lastupdated: "2020-4-24"

---

{:new_window: target="blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:child: .link .ulchildlink}
{:childlinks: .ullinks}

# ポリシーの概要
{: #policy_overview}

Add a graphic showing the pieces involved in the policy (ex, node, service and business policy, constraints, properties). 

このセクションでは、ポリシーの概念について説明します。 

さまざまなシステム・コンポーネントに関する包括的な概要については、[IBM Edge Application Manager for Devices の仕組みの概要](../getting_started/overview.md)を参照してください。 実践的なポリシー例については、[Hello World](../getting_started/policy.md) を参照してください。

何千ものエッジ・ノードを 1 人の管理者が同時に管理することはできないため、数万を超えるように規模を拡大すると、対処不可能な状態になってしまいます。 このレベルのスケーリングを実現するため、{{site.data.keyword.edge_devices_notm}} は、いつ、どこに、サービスおよび機械学習モデルを自律的にデプロイするのかを、ポリシーを使用して判断します。 ポリシーは、デプロイメント・パターンに代わるものです。

ポリシーはポリシー言語を使用して表されます。この言語は、モデル・ポリシー、ノード・ポリシー、サービス・ポリシー、およびデプロイメント・ポリシーに適用される柔軟な言語です。 ポリシー言語は、属性 (`プロパティー`と呼ばれます) を定義し、固有の要件 (`制約`と呼ばれます) を表明します。 これにより、システムの各部分が {{site.data.keyword.edge_devices_notm}} デプロイメント・エンジンへの入力を提供できます。 サービスをデプロイするには、その前に、モデル、ノード、サービス、およびデプロイメントのポリシー制約が検査され、すべての要件が満たされていることが確認される必要があります。

ノード (サービスがデプロイされる場所) が要求を表現できるため、{{site.data.keyword.edge_devices_notm}} ポリシーは双方向ポリシー・システムであると言えます。 {{site.data.keyword.edge_devices_notm}} ポリシー・デプロイメント・システムにおいて、ノードはスレーブではありません。 結果として、ポリシーは、サービスおよびモデルのデプロイメントをパターンよりも細かく制御できます。 ポリシーが使用されている場合、{{site.data.keyword.edge_devices_notm}} は、指定されたサービスをデプロイできるノードを検索し、存在するノードを分析して、それらのノードが適合している (ポリシー内である) ことを確認します。 ノードが「ポリシー内である」のは、サービスを最初にデプロイした、ノード、サービス、およびデプロイメントのポリシーがまだ効力を保っている場合か、または、それらのポリシーのいずれかへの変更がポリシー互換性に影響しない場合です。 ポリシーを使用することで、関心事の分離がより確かなものになり、エッジ・ノード所有者、サービス開発者、およびビジネス所有者が独立して自身のポリシーを明瞭に表現できるようになります。

以下の 4 つのタイプのポリシーがあります。

* ノード
* サービス
* デプロイメント
* モデル
