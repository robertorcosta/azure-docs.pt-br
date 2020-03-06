---
title: Acesso às redes virtuais do Azure
description: Visão geral sobre como os ambientes de serviço de integração (ISEs) ajudam os aplicativos lógicos a acessar redes virtuais do Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: a0330ae8e69691f431756e6ea9a3027e1ac07b1c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303368"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)

Às vezes, seus aplicativos lógicos e contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços, que estão dentro de uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, você pode [criar um *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Um ISE é uma instância isolada do serviço de aplicativos lógicos que usa recursos dedicados e é executado separadamente do serviço de aplicativos lógicos multilocatários "globais".

Executar aplicativos lógicos em sua própria instância isolada separada ajuda a reduzir o impacto que outros locatários do Azure podem ter no desempenho de seus aplicativos, também conhecido como [efeito de "vizinhos ruidosas"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Um ISE também fornece estes benefícios:

* Seus próprios endereços IP estáticos, que são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço multilocatário. Você também pode configurar um único endereço IP de saída público, estático e previsível para se comunicar com os sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nesses sistemas de destino para cada ISE.

* Aumento dos limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md).

Quando você cria um ISE, o Azure *injeta* ou implanta esse ISE em sua rede virtual do Azure. Você pode usar esse ISE como o local para os aplicativos lógicos e as contas de integração que precisam de acesso.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Os aplicativos lógicos podem acessar recursos que estão dentro ou conectados à sua rede virtual usando esses itens, que são executados no mesmo ISE que seus aplicativos lógicos:

* Um gatilho ou ação interna de rótulo de **núcleo**, como o gatilho ou a ação de http
* Um conector com rótulo de **ISE**para esse sistema ou serviço
* Um conector personalizado

Você ainda pode também usar conectores que não têm o rótulo **principal** ou **ISE** com os aplicativos lógicos no ISE. Em vez disso, esses conectores são executados no serviço de aplicativos lógicos multilocatário. Para obter mais informações, consulte estas seções:

* [Isolado versus multilocatário](#difference)
* [Conectar-se de um ambiente do serviço de integração](../connectors/apis-list.md#integration-service-environment)
* [Conectores do ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para obter mais informações, consulte [modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter detalhes de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

Esta visão geral descreve mais informações sobre como um ISE fornece aos seus aplicativos lógicos e contas de integração acesso direto à sua rede virtual do Azure e compara as diferenças entre um ISE e o serviço de aplicativos lógicos multilocatários.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolado versus multilocatário

Ao criar e executar aplicativos lógicos em um ISE, você obtém as mesmas experiências de usuário e recursos semelhantes que o serviço de aplicativos lógicos multilocatários. Você pode usar todos os mesmos gatilhos internos, ações e conectores gerenciados que estão disponíveis no serviço de aplicativos lógicos multilocatários. Alguns conectores gerenciados oferecem versões adicionais do ISE. A diferença entre conectores do ISE e conectores não ISE existe no local em que eles são executados e os rótulos que eles têm no designer de aplicativo lógico quando você trabalha em um ISE.

![Conectores com e sem rótulos em um ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)



* Gatilhos e ações internas exibem o rótulo **principal** . Eles sempre são executados no mesmo ISE que seu aplicativo lógico. Os conectores gerenciados que exibem o rótulo do **ISE** também são executados no mesmo ISE que seu aplicativo lógico.

  Por exemplo, aqui estão alguns conectores que oferecem versões do ISE:

  * Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
  * Filas do Azure, Barramento de Serviço do Azure, Hubs de Eventos do Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, SQL Data Warehouse do Azure, Azure Cosmos DB
  * AS2, X12 e EDIFACT

* Os conectores gerenciados que não exibem rótulos adicionais sempre são executados no serviço de aplicativos lógicos multilocatários, mas você ainda pode usar esses conectores em um aplicativo lógico hospedado pelo ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Acesso a sistemas locais

Para acessar sistemas locais ou fontes de dados que estão conectados a uma rede virtual do Azure, os aplicativos lógicos em um ISE podem usar estes itens:

* Ação HTTP

* ISE-conector rotulado para esse sistema

  > [!NOTE]
  > Para usar a autenticação do Windows com o conector de SQL Server em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), use a versão sem ISE do conector com o [Gateway de dados local](../logic-apps/logic-apps-gateway-install.md). A versão com rótulo de ISE não dá suporte à autenticação do Windows.

* Conector personalizado

  * Se você tiver conectores personalizados que exigem o gateway de dados local e tiver criado esses conectores fora de um ISE, os aplicativos lógicos em um ISE também poderão usar esses conectores.

  * Os conectores personalizados criados em um ISE não funcionam com o gateway de dados local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas à rede virtual que hospeda o ISE. Portanto, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.

Para sistemas locais que não estão conectados a uma rede virtual ou que não têm conectores com rótulo de ISE, você deve primeiro [Configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) antes que seus aplicativos lógicos possam se conectar a esses sistemas.

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKUs do ISE

Ao criar o ISE, você pode selecionar a SKU do desenvolvedor ou a SKU Premium. Estas são as diferenças entre estas SKUs:

* **Desenvolvedor**

  Fornece um ISE de menor custo que você pode usar para experimentação, desenvolvimento e teste, mas não para teste de produção ou de desempenho. A SKU do desenvolvedor inclui gatilhos e ações internos, conectores padrão, conectores corporativos e uma única conta de integração de [camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) para um preço mensal fixo. No entanto, esse SKU não inclui nenhum SLA (contrato de nível de serviço), opções para escalar verticalmente a capacidade ou redundância durante a reciclagem, o que significa que você pode enfrentar atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que você pode usar para produção e inclui suporte a SLA, gatilhos e ações internos, conectores padrão, conectores corporativos, uma única conta de integração de [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opções para escalar verticalmente a capacidade e redundância durante a reciclagem de um preço mensal fixo.

> [!IMPORTANT]
> A opção SKU está disponível apenas na criação do ISE e não pode ser alterada posteriormente.

Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto de extremidade do ISE

Ao criar o ISE, você pode optar por usar pontos de extremidade de acesso internos ou externos. Sua seleção determina se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual.

Esses pontos de extremidade também afetam a maneira como você pode acessar entradas e saídas no histórico de execução de seus aplicativos lógicos.

* **Interno**: pontos de extremidade privados que permitem chamadas para aplicativos lógicos no ISE, onde você pode exibir e acessar as entradas e saídas dos seus aplicativos lógicos no histórico *de execução somente de dentro de sua rede virtual*

* **External**: pontos de extremidade públicos que permitem chamadas para aplicativos lógicos no ISE, onde você pode exibir e acessar as entradas e saídas dos seus aplicativos lógicos no histórico *de execução de fora da sua rede virtual*. Se você usar NSGs (grupos de segurança de rede), verifique se eles estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execuções. Para obter mais informações, consulte [habilitar o acesso para ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> A opção de ponto de extremidade de acesso só está disponível na criação do ISE e não pode ser alterada posteriormente.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração. Para saber como o preço e a cobrança funcionam para contas de integração com um ISE, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Conectar-se a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Adicionar artefatos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
