---

copyright:
years: 2019
lastupdated: "2019-06-24"
 
---

{:new_window: target="blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:child: .link .ulchildlink}
{:childlinks: .ullinks}

# 보안 및 개인정보 보호
{: #security_privacy}

{{site.data.keyword.edge_notm}}({{site.data.keyword.ieam}})의 경우 [Open Horizon](https://github.com/open-horizon)을 기반으로 여러 다른 보안 기술을 사용하여 공격에 대해 보안을 유지하고 개인정보를 보호합니다. {{site.data.keyword.ieam}}에서는 에지 소프트웨어 관리를 위해 지리적으로 분산된 자율 에이전트 프로세스에 의존합니다. 그 결과, {{site.data.keyword.ieam}} 관리 허브 및 에이전트는 보안 침해의 잠재적 대상이 됩니다. 이 문서에서는 {{site.data.keyword.ieam}}에서 위협을 완화하거나 없애는 방법을 설명합니다.
{:shortdesc}

## 관리 허브
{{site.data.keyword.ieam}} 관리 허브는 OpenShift Container Platform에 배치됩니다. 따라서 내재된 보안 메커니즘 이점 모두를 상속합니다. 모든 {{site.data.keyword.ieam}} 관리 허브 네트워크 트래픽은 TSL 보안 시작점을 순회합니다. {{site.data.keyword.ieam}} 관리 허브 컴포넌트 사이의 관리 허브 네트워크 통신은 TLS 없이 수행됩니다.

## 보안 제어 플레인
{: #dc_pane}

{{site.data.keyword.ieam}} 관리 허브 및 분산 에이전트는 제어 플레인을 통해 통신하며 에지 노드에 워크로드와 모델을 배치합니다. 일반적인 중앙 집중식 IoT(Internet of Things) 플랫폼 및 클라우드 기반 제어 시스템과 대조적으로, {{site.data.keyword.ieam}}의 제어 플레인은 대부분 분산됩니다. 각 액터가 해당 태스크를 완료하는 데 필요한 최소 레벨의 권한만 가지도록 시스템 내 각 액터의 권한 범위는 제한되어 있습니다. 단일 액터로 전체 시스템을 제어할 수 없습니다. 또한 단일 액터는 단일 에지 노드, 호스트 또는 소프트웨어 컴포넌트를 절충하여 시스템의 모든 에지 노드에 대한 액세스 권한을 얻을 수 없습니다.

제어 플레인은 세 개의 다른 소프트웨어 엔티티로 구현됩니다.
* 오픈 {{site.data.keyword.horizon}} 에이전트
* 오픈 {{site.data.keyword.horizon}} agbots
* 오픈 {{site.data.keyword.horizon_exchange}}

오픈 {{site.data.keyword.horizon}} 에이전트와 agbots는 제어 플레인 내 1차 액터입니다. {{site.data.keyword.horizon_exchange}}에서는 에이전트와 agbots 사이의 검색 및 보안 통신을 용이하게 합니다. 이와 함께, Perfect Forward Secrecy라고 하는 알고리즘을 사용하여 메시지 수준 보호를 제공합니다.

기본적으로 에이전트와 agbots는 TLS 1.3을 통해 Exchange와 통신합니다. 그러나 TLS 자체가 충분한 보안을 제공하지 않습니다. {{site.data.keyword.ieam}}에서는 네트워크에서 전송하기 전에 에이전트와 agbots 사이에 전달되는 모든 제어 메시지를 암호화합니다. 각 에이전트와 agbots는 2048비트 키 쌍을 생성하고 교환 시 공개 키를 공개합니다. 개인 키는 각 액터의 루트 보호 스토리지에 저장됩니다. 시스템의 다른 액터는 메시지 수신자의 공개 키를 사용하여 제어 플레인 메시지를 암호화하는 데 사용되는 대칭 키를 암호화합니다. 이를 통해 의도된 수신자만 대칭 키를 복호화할 수 있도록 보장합니다(즉, 메시지 자체). 제어 플레인에서 Perfect Forward Secrecy 사용은 TLS 에서 방지하지 못하는 MITM(Man-in-the-middle) 공격 방지와 같은 추가 보안을 제공합니다.

### 에이전트
{: #agents}

{{site.data.keyword.horizon_open}} 에이전트는 {{site.data.keyword.ieam}}의 다른 모든 액터보다 수가 많습니다. 에이전트는 각 관리 에지 노드에서 실행됩니다. 각 에이전트에는 해당 단일 에지 노드만 관리할 수 있는 권한이 있습니다. 손상된 에이전트에는 다른 에지 노드 또는 시스템의 다른 컴포넌트에 영향을 미칠 수 있는 권한이 없습니다. 각 노드에는 루트 보호된 스토리지에 저장된 고유한 인증 정보가 있습니다. {{site.data.keyword.horizon_exchange}}에서는 노드가 고유한 리소스에만 액세스할 수 있도록 보장합니다. `hzn register` 명령을 사용하여 노드가 등록된 경우 인증 토큰을 제공할 수 있습니다. 그러나 사람이 노드 인증 정보를 인식하지 못하도록 에이전트에서 고유한 토큰을 생성하도록 하는 것이 좋습니다. 그러면 에지 노드를 위협할 가능성이 줄어듭니다.

호스트 네트워크에서 청취 포트가 없으므로 네트워크 공격으로부터 에이전트가 보안됩니다. 에이전트와 관리 허브 사이의 모든 통신은 관리 허브를 폴링하여 에이전트에 의해 이루어집니다. 또한 노드 호스트에 대한 침입을 방지하기 위해 네트워크 방화벽으로 에지 노드를 보호하도록 강력하게 권장합니다. 이러한 보호에도 불구하고 에이전트 호스트 운영 체제 또는 에이전트 프로세스가 자체가 해킹되거나 다른 방식으로 손상되면 해당 에지 노드만 손상됩니다. {{site.data.keyword.ieam}} 시스템의 다른 모든 파트는 영향을 받지 않습니다.

에이전트는 컨테이너화된 워크로드를 다운로드 및 시작할 책임이 있습니다. 다운로드된 컨테이너 이미지 및 해당 구성이 손상되지 않았는지 확인하기 위해 에이전트는 컨테이너 이미지 디지털 서명과 배치 구성 디지털 서명을 확인합니다. 컨테이너가 컨테이너 레지스트리에 저장되면 레지스트리는 이미지에 대한 디지털 서명(예: SHA256 해시)을 제공합니다. 컨테이너 레지스트리는 서명을 작성하는 데 사용되는 키를 관리합니다. {{site.data.keyword.ieam}} 서비스가 `hzn exchange service publish` 명령을 사용하여 공개되면 이미지 서명을 확보하고 {{site.data.keyword.horizon_exchange}}에서 공개된 서비스로 저장합니다. 이미지 디지털 서명은 보안 제어 플레인을 통해 에이전트에 전달됩니다. 이를 통해 에이전트는 다운로드된 이미지에 대해 컨테이너 이미지 서명을 확인할 수 있습니다. 이미지 서명이 이미지와 일치하지 않으면 에이전트는 컨테이너를 시작하지 않습니다. 프로세스는 한 가지를 예외로, 컨테이너 구성과 비슷합니다. `hzn exchange service publish` 명령은 컨테이너 구성에 서명하고 {{site.data.keyword.horizon_exchange}}에서 서명을 저장합니다. 이 경우 사용자(서비스 공개 측)는 서명을 작성하는 데 사용되는 RSA 키를 제공해야 합니다. 사용자에게 아직 키가 없는 경우 `hzn key create` 명령을 사용하여 이 목적으로 키를 생성할 수 있습니다. 공개 키는 컨테이너 구성의 서명과 함께 교환 시 저장되며, 보안 제어 플레인을 통해 에이전트에 전달됩니다. 그런 다음, 에이전트는 공개 키를 사용하여 컨테이너 구성을 확인할 수 있습니다. 각 컨테이너 구성에 다른 키 쌍을 사용하려는 경우 이 컨테이너 구성에 서명하는 데 사용된 개인 키가 더 이상 필요하지 않으면 지금 버릴 수 있습니다. 워크로드 공개에 대한 자세한 정보는 [에지 서비스 개발](../developing/developing_edge_services.md)을 참조하십시오.

모델이 에지 노드에 배치된 경우 에이전트는 모델을 다운로드하고 호스트의 루트 보호 스토리지에 저장합니다. 에이전트가 시작되면 각 서비스에 대한 인증 정보가 제공됩니다. 서비스는 해당 인증 정보를 사용하여 서비스가 액세스할 수 있는 모델에 대한 액세스를 사용하고 자체적으로 식별합니다. {{site.data.keyword.ieam}}의 모든 모델 오브젝트는 모델에 액세스할 수 있는 서비스 목록을 표시합니다. 각 서비스는 {{site.data.keyword.ieam}}에 의해 다시 시작될 때마다 새 인증 정보를 가져옵니다. 모델 오브젝트는 {{site.data.keyword.ieam}}에 의해 암호화되지 않습니다. {{site.data.keyword.ieam}}에 의해 모델 오브젝트가 비트 단위로 처리되기 때문에 서비스 구현은 필요한 경우 모델을 자유롭게 암호화합니다. MMS 사용 방법에 대한 자세한 정보는 [모델 관리 세부사항](../developing/model_management_details.md)을 참조하십시오.

### Agbot
{: #agbots}

{{site.data.keyword.ieam}} 관리 허브는 agbot의 여러 인스턴스를 포함합니다. 이들은 관리 허브에 등록된 모든 에지 노드에 대한 워크로드 배치 시작을 책임집니다. Agbot은 주기적으로 Exchange에 공개된 모든 배치 정책 및 패턴을 확인하여 해당 패턴 및 정책의 서비스가 올바른 모든 에지 노드에 배치되었는지 확인합니다. Agbot이 배치 요청을 시작할 때 보안 제어 플레인을 통해 요청을 전송합니다. 에이전트가 요청을 승인하기로 결정하면 배치 요청은 워크로드 및 해당 구성을 확인하기 위해 에이전트에 필요한 모든 항목을 포함합니다. 에이전트 수행 작업에 대한 보안 세부사항은 [에이전트](security_privacy.md#agents)를 참조하십시오. Agbot은 모델을 배치할 위치와 시점을 MMS에 지시합니다. 모델 관리 방법에 대한 보안 세부사항은 [에이전트](security_privacy.md#agents)를 참조하십시오.

손상된 agbot은 악의적인 워크로드 배치를 제안할 수 있지만, 제안된 배치는 에이전트 섹션에 명시된 보안 요구사항을 충족해야 합니다. Agbot이 워크로드 배치를 시작해도 워크로드 및 컨테이너 구성을 작성할 권한이 없으므로 악의적인 워크로드를 제안할 수 없습니다.

## {{site.data.keyword.horizon_exchange}}
{: #exchange}

{{site.data.keyword.horizon_exchange}}는 중앙화, 복제, 로드 밸런싱된 REST API 서버입니다. 이는 사용자, 조직, 에지 노드, 공개된 서비스, 정책, 패턴에 대한 메타데이터의 공유 데이터베이스로 작동합니다. 또한 이를 통해 분산된 에이전트 및 agbot에서 메시지를 검색할 수 있을 때까지 보안 제어 플레인에 대한 스토리지를 제공하여 컨테이너화된 워크로드를 배치할 수 있습니다. {{site.data.keyword.horizon_exchange}}에서는 제어 메시지를 읽을 수 없습니다. 메시지를 복호화하기 위해 개인 RSA 키를 처리하지 못하기 때문입니다. 따라서 손상된 {{site.data.keyword.horizon_exchange}}는 제어 플레인 트래픽을 감시할 수 없습니다. Exchange 역할에 대한 자세한 정보는 [{{site.data.keyword.edge}} 개요](../getting_started/overview_ieam.md)를 참조하십시오.

## 서비스 거부(DoS) 공격
{: #denial}

{{site.data.keyword.ieam}} 관리 허브는 중앙화된 서비스입니다. 일반적인 클라우드 기반 환경의 중앙화된 서비스는 일반적으로 서비스 거부(DoS) 공격에 취약합니다. 워크로드 배치를 협상하거나 허브에 먼저 등록된 경우에만 에이전트에 연결이 필요합니다. 이외의 경우에 에이전트는 {{site.data.keyword.ieam}} 관리 허브에서 연결이 끊어진 동안에도 일반적으로 계속 작동합니다.  이를 통해 {{site.data.keyword.ieam}} 에이전트는 관리 허브가 공격을 받아도 에지 노드에 활성 상태로 남아 있습니다.

### 모델 관리 시스템
{: malware}

{{site.data.keyword.ieam}}에서는 MMS에 업로드되는 데이터에 대한 악성코드 또는 바이러스 스캔을 수행하지 않습니다. MMS에 업로드하기 전에 업로드되는 데이터를 스캔했는지 확인하십시오.

## 저장 데이터
{: #drest}

{{site.data.keyword.ieam}}에서는 저장 데이터의 암호화를 지원하지 않습니다. 저장 데이터 암호화는 {{site.data.keyword.ieam}} 관리 허브 또는 에이전트가 실행 중인 호스트 운영 체제에 적절한 유틸리티로 구현해야 합니다.

## 보안 표준
{: #standards}

다음 보안 표준이 {{site.data.keyword.ieam}}에서 사용됩니다.
* TLS 1.2(HTTPS)는 관리 허브 사이에서 전송되는 데이터의 암호화에 사용됩니다.
* AES 256비트 암호화는 전송 중인 데이터, 특히 보안 제어 플레인에서 전달되는 메시지에 사용됩니다.
* 2048비트 RSA 키 쌍은 전송 중인 데이터, 특히 보안 제어 플레인에서 전달되는 AES 256 대칭 키에 사용됩니다.
* **hzn exchange service publish** 명령을 사용할 때 컨테이너 배치 구성에 서명하는 데 사용자가 제공하는 RSA 키.
* 사용자가 이 명령을 사용하도록 선택한 경우 **hzn key create** 명령에서 생성한, RSA 키 쌍. 이 키의 비트 크기는 기본적으로 4096이지만, 사용자에 의해 변경될 수 있습니다.

## 요약
{: #summary}

{{site.data.keyword.edge_notm}}에서는 원하지 않는 액세스로부터 보호하기 위해 해시, 암호화 서명, 암호화를 사용합니다. 대부부분을 탈중앙화하여 {{site.data.keyword.ieam}}는 에지 컴퓨팅 환경에서 일반적으로 발견되는 대부분의 공격에 대한 노출을 방지합니다. 참가자 역할에 대한 권한 범위를 제한하여 {{site.data.keyword.ieam}}에는 손상된 호스트 또는 손상된 소프트웨어 컴포넌트로 인한 해당 시스템 파트의 잠재적인 손상이 포함됩니다. {{site.data.keyword.ieam}}에서 사용되는 {{site.data.keyword.horizon}} 서비스의 중앙 집중식 서비스에 대한 대규모 외부 공격도 에지의 워크로드 실행에 최소한의 영향을 미칩니다.
