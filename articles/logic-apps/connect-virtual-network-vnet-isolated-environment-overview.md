---
title: Acesso às redes virtuais do Azure
description: Visão geral sobre como os ISEs (Service Environments, ambientes de serviço de integração) ajudam os aplicativos lógicos a acessar redes virtuais (VNETs) do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127255"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)

Às vezes, seus aplicativos lógicos precisam ter acesso a recursos protegidos, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão dentro de uma [rede virtual do Azure.](../virtual-network/virtual-networks-overview.md) Para configurar esse acesso, você pode [criar um ambiente de serviço de *integração* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) Um ISE é uma instância isolada do serviço Logic Apps que usa recursos dedicados e é executado separadamente do serviço "global" de aplicativos lógicos multilocatários.

A execução de aplicativos lógicos em sua própria instância isolada ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho de seus aplicativos, também conhecido como [efeito "vizinhos barulhentos".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Um ISE também fornece esses benefícios:

* Seus próprios endereços IP estáticos, que são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço multi-inquilino. Você também pode configurar um único endereço IP público, estático e previsível para se comunicar com sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais de firewall nesses sistemas de destino para cada ISE.

* Limites aumentados na duração da execução, retenção de armazenamento, throughput, tempo limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [Limites e configuração para aplicativos azure logic](logic-apps-limits-and-config.md).

> [!NOTE]
> Algumas redes virtuais do Azure usam endpoints privados[(Azure Private Link)](../private-link/private-link-overview.md)para fornecer acesso aos serviços do Azure PaaS, como O Azure Storage, Azure Cosmos DB ou Azure SQL Database, serviços de parceiros ou serviços ao cliente hospedados no Azure. Se seus aplicativos de lógica precisarem de acesso a redes virtuais que usam pontos finais privados, você deve criar, implantar e executar esses aplicativos lógicos dentro de um ISE.

Quando você cria um ISE, o Azure *injeta* ou implanta esse ISE em sua rede virtual Azure. Em seguida, você pode usar este ISE como o local para os aplicativos lógicos e contas de integração que precisam de acesso.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Os aplicativos lógicos podem acessar recursos que estão dentro ou conectados à sua rede virtual usando esses itens, que são executados na mesma ISE que seus aplicativos lógicos:

* Um gatilho ou ação incorporado com rótulo **CORE,** como o gatilho OU ação HTTP
* Um conector com etiqueta **ISE**para esse sistema ou serviço
* Um conector personalizado

Você ainda pode usar conectores que não têm o rótulo **CORE** ou **ISE** com os aplicativos lógicos em seu ISE. Esses conectores são executados no serviço de aplicativos lógicos de vários locatários. Para obter mais informações, consulte estas seções:

* [Isolado versus multi-inquilino](#difference)
* [Conecte-se a partir de um ambiente de serviço de integração](../connectors/apis-list.md#integration-service-environment)
* [Conectores ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Aplicativos de lógica, gatilhos incorporados, ações incorporadas e conectores que são executados em seu ISE usam um plano de preços que difere do plano de preços baseado no consumo. Para obter mais informações, consulte [o modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter detalhes sobre preços, consulte [preços da Logic Apps](../logic-apps/logic-apps-pricing.md).

Esta visão geral descreve mais informações sobre como um ISE dá aos seus aplicativos lógicos acesso direto à sua rede virtual Azure e compara as diferenças entre um ISE e o serviço de aplicativos lógicos de vários locatários.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolado versus multi-inquilino

Quando você cria e executa aplicativos lógicos em um ISE, você tem as mesmas experiências do usuário e recursos semelhantes ao serviço de aplicativos lógicos de vários locatários. Você pode usar todos os mesmos gatilhos, ações e conectores gerenciados incorporados que estão disponíveis no serviço de aplicativos lógicos de vários locatários. Alguns conectores gerenciados oferecem versões ISE adicionais. A diferença entre conectores ISE e conectores não-ISE existe no local onde eles são executados e os rótulos que eles têm no Logic App Designer quando você trabalha dentro de um ISE.

![Conectores com e sem rótulos em um ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Gatilhos e ações incorporados exibem o rótulo **CORE.** Eles sempre são executados na mesma ISE que seu aplicativo lógico. Os conectores gerenciados que exibem o rótulo **ISE** também são executados no mesmo ISE que seu aplicativo lógico.

  Por exemplo, aqui estão alguns conectores que oferecem versões ISE:

  * Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
  * Filas do Azure, Barramento de Serviço do Azure, Hubs de Eventos do Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 e EDIFACT

* Conectores gerenciados que não exibem rótulos adicionais sempre são executados no serviço de aplicativos lógicos de vários locatários, mas você ainda pode usar esses conectores em um aplicativo lógico hospedado pela ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Acesso aos sistemas locais

Para acessar sistemas locais ou fontes de dados conectados a uma rede virtual do Azure, os aplicativos lógicos em um ISE podem usar esses itens:

* Ação HTTP

* Conector com etiqueta ISE para esse sistema

  > [!NOTE]
  > Para usar a autenticação do Windows com o conector SQL Server em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)use a versão não ISE do conector com o gateway de dados [on-premises](../logic-apps/logic-apps-gateway-install.md). A versão rotulada com ISE não suporta autenticação do Windows.

* Conector personalizado

  * Se você tiver conectores personalizados que requerem o gateway de dados no local e você criou esses conectores fora de um ISE, os aplicativos lógicos em um ISE também podem usar esses conectores.

  * Os conectores personalizados criados em um ISE não funcionam com o gateway de dados no local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas à rede virtual que hospeda o ISE. Assim, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.

Para sistemas locais que não estejam conectados a uma rede virtual ou que não tenham conectores com etiquetaIS ISE, você deve primeiro [configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) antes que seus aplicativos de lógica possam se conectar a esses sistemas.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKUs

Ao criar seu ISE, você pode selecionar o SKU do desenvolvedor ou o SKU Premium. Aqui estão as diferenças entre essas SKUs:

* **Desenvolvedor**

  Fornece um ISE de menor custo que você pode usar para experimentação, desenvolvimento e testes, mas não para testes de produção ou desempenho. O SKU do desenvolvedor inclui gatilhos e ações incorporados, conectores padrão, conectores Corporativos e uma única conta de integração [de nível Livre](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) por um preço mensal fixo. No entanto, este SKU não inclui nenhum contrato de nível de serviço (SLA), opções para aumentar a capacidade ou redundância durante a reciclagem, o que significa que você pode sofrer atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que você pode usar para a produção e inclui suporte a SLA, gatilhos e ações incorporados, conectores padrão, conectores Enterprise, uma única conta de integração [de nível Padrão,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) opções para aumentar a capacidade e redundância durante a reciclagem por um preço mensal fixo.

> [!IMPORTANT]
> A opção SKU está disponível apenas na criação do ISE e não pode ser alterada mais tarde.

Para obter preços, consulte [preços da Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como funcionam os preços e o faturamento dos ISEs, consulte o [modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto final da ISE

Ao criar seu ISE, você pode optar por usar pontos finais de acesso internos ou externos. Sua seleção determina se os gatilhos de solicitação ou webhook em aplicativos lógicos em seu ISE podem receber chamadas de fora da sua rede virtual.

Esses pontos finais também afetam a maneira como você pode acessar entradas e saídas no histórico de execução de seus aplicativos lógicos.

* **Interno**: Pontos finais privados que permitem chamadas para aplicativos lógicos em seu ISE onde você pode visualizar e acessar as entradas e saídas de seus aplicativos lógicos no histórico de execução *apenas de dentro de sua rede virtual*

* **Externo**: Pontos finais públicos que permitem chamadas para aplicativos lógicos em seu ISE onde você pode visualizar e acessar as entradas e saídas de seus aplicativos lógicos no histórico de execução *de fora de sua rede virtual*. Se você usar os NSGs (Network Security Groups, grupos de segurança de rede), certifique-se de que eles estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execução. Para obter mais informações, consulte [Habilitar o acesso ao ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> A opção de ponto final de acesso está disponível apenas na criação do ISE e não pode ser alterada posteriormente.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração. Para saber como funcionam os preços e o faturamento das contas de integração com um ISE, consulte o [modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter preços, consulte [preços da Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a redes virtuais do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
