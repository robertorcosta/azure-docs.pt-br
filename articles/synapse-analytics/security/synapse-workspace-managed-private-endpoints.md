---
title: Pontos de extremidade privados gerenciados
description: Um artigo que explica os Pontos de extremidade privados gerenciados no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ac62b1706749c4708d804dd2102ad3f2a7132a16
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85193644"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Pontos de extremidade privados gerenciados do Synapse (versão prévia)

Este artigo explicará os Pontos de extremidade privados gerenciados no Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Pontos de extremidade privados gerenciados

Os Pontos de extremidade privados gerenciados são pontos de extremidade privados criados na VNet de workspace gerenciada que estabelece um link privado para recursos do Azure. O Azure Synapse gerencia para você esses pontos de extremidade privados.

O Azure Synapse é compatível com links privados. O link privado permite que você acesse serviços do Azure (como o Armazenamento do Azure, o Azure Cosmos DB e o SQL Data Warehouse do Azure) e os serviços hospedados de cliente/parceiro do Azure de sua VNet do Azure com segurança.

Quando você usa um link privado, o tráfego entre sua VNet e o workspace atravessa totalmente a rede de backbone da Microsoft. O Link Privado protege contra riscos de exfiltração dos dados. Você estabelece um link privado para um recurso criando um ponto de extremidade privado.

O ponto de extremidade privado usa um endereço IP privado de sua VNet para, efetivamente, colocar o serviço em sua VNet. Os pontos de extremidade privados são mapeados para um recurso específico no Azure e não para todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado por sua organização. Saiba mais sobre [links privados e pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Os Pontos de extremidade privados gerenciados só são compatíveis com workspaces do Azure Synapse com uma VNet de workspace gerenciada.

>[!NOTE]
>Todo o tráfego de saída da VNet de workspace gerenciada, exceto por meio de pontos de extremidade privados gerenciados, será bloqueado no futuro. É recomendável que você crie pontos de extremidade privados gerenciados para se conectar a todas as suas fontes de dados do Azure externas ao workspace. 

Uma conexão de ponto de extremidade privado é criada em um estado "Pendente" quando você cria um Ponto de extremidade privado gerenciado no Azure Synapse. Um fluxo de trabalho de aprovação é iniciado. O proprietário do recurso de link privado é responsável por aprovar ou rejeitar a conexão.

Se o proprietário aprova a conexão, o link privado é estabelecido. Caso contrário, o link privado não será estabelecido. Em ambos os casos, o Ponto de extremidade privado gerenciado será atualizado com o status da conexão.

Somente um Ponto de extremidade privado gerenciado em um estado aprovado pode enviar tráfego para um determinado recurso de link privado.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Pontos de extremidade privados gerenciados para pool de SQL e SQL sob demanda

O pool de SQL e o SQL sob demanda são recursos analíticos no seu workspace do Azure Synapse. Esses recursos usam a infraestrutura multilocatário que não é implantada na [VNet de workspace gerenciada](./synapse-workspace-managed-vnet.md).

Quando um workspace é criado, o Azure Synapse cria dois Pontos de extremidade privados gerenciados para o pool de SQL e o SQL sob demanda nesse workspace. 

Esses dois Pontos de extremidade privados gerenciados são listados no Azure Synapse Studio. Selecione **Gerenciar** no painel de navegação esquerdo e, em seguida, selecione **Redes Virtuais Gerenciadas** para ver os pontos no Studio.

O Ponto de extremidade privado gerenciado que tem como destino o pool de SQL é chamado *synapse-ws-sql--\<workspacename\>* e aquele que se destina ao SQL sob demanda é chamado *synapse-ws-sqlOnDemand--\<workspacename\>* .
![Pontos de extremidade privados gerenciados para pool de SQL e SQL sob demanda](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Esses dois Pontos de extremidade privados gerenciados são criados automaticamente para você quando você cria seu workspace do Azure Synapse. Você não é cobrado por esses dois Pontos de extremidade privados gerenciados.

## <a name="next-steps"></a>Próximas etapas

[Criar Pontos de extremidade privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
