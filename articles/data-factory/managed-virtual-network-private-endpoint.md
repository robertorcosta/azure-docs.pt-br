---
title: Rede virtual gerenciada & pontos de extremidade privados gerenciados
description: Saiba mais sobre a rede virtual gerenciada e pontos de extremidade privados gerenciados em Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: c0f23c864430b6cb2f49f924d5aaa8bde296037c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135956"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory rede virtual gerenciada (versão prévia)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explicará a rede virtual gerenciada e os pontos de extremidade privados gerenciados em Azure Data Factory.


## <a name="managed-virtual-network"></a>Rede virtual gerenciada

Quando você cria uma Azure Integration Runtime (IR) em Azure Data Factory rede virtual gerenciada (VNET), o Integration Runtime será provisionado com a rede virtual gerenciada e aproveitará os pontos de extremidade privados para se conectar com segurança aos armazenamentos de dados com suporte. 

A criação de um Azure IR na rede virtual gerenciada garante que o processo de integração de dados seja isolado e seguro. 

Benefícios do uso da rede virtual gerenciada:

- Com uma rede virtual gerenciada, você pode descarregar a carga de gerenciamento da rede virtual para Azure Data Factory. Você não precisa criar uma sub-rede para Azure Integration Runtime que pode eventualmente usar muitos IPs privados de sua rede virtual e exigiria um planejamento de infraestrutura de rede anterior. 
- Ele não exige um profundo conhecimento de rede do Azure para fazer integrações de dados com segurança. Em vez disso, começar a usar o ETL seguro é muito simplificado para engenheiros de dados. 
- A rede virtual gerenciada juntamente com pontos de extremidade privados gerenciados protege contra vazamento de dados. 

> [!IMPORTANT]
>Atualmente, a VNet gerenciada só tem suporte na mesma região que Azure Data Factory região.
 

![Arquitetura de rede virtual gerenciada por ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Pontos de extremidade privados gerenciados

Pontos de extremidade privados gerenciados são pontos de extremidade privados criados no Azure Data Factory rede virtual gerenciada estabelecendo um link privado para recursos do Azure. Azure Data Factory gerencia esses pontos de extremidade privados em seu nome. 

![Novo ponto de extremidade privado gerenciado](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory dá suporte a links privados. O link privado permite que você acesse os serviços do Azure (PaaS) (como o armazenamento do Azure, Azure Cosmos DB, Azure Synapse Analytics (anteriormente chamado de SQL Data Warehouse do Azure)).

Quando você usa um link privado, o tráfego entre os armazenamentos de dados e a rede virtual gerenciada atravessa totalmente a rede de backbone da Microsoft. O Link Privado protege contra riscos de exfiltração dos dados. Você estabelece um link privado para um recurso criando um ponto de extremidade privado.

O ponto de extremidade privado usa um endereço IP privado na rede virtual gerenciada para efetivamente colocar o serviço nele. Os pontos de extremidade privados são mapeados para um recurso específico no Azure e não para todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado por sua organização. Saiba mais sobre [links privados e pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/).

> [!NOTE]
> É recomendável que você crie Pontos de extremidade privados gerenciados para se conectar a todas as suas fontes de dados do Azure. 
 
> [!WARNING]
> Se um armazenamento de dados PaaS (BLOB, ADLS Gen2, SQL DW) tiver um ponto de extremidade privado já criado e, mesmo que ele permita acesso de todas as redes, o ADF só poderá acessá-lo usando um ponto de extremidade privado gerenciado. Certifique-se de criar um ponto de extremidade privado nesses cenários. 

Uma conexão de ponto de extremidade privado é criada em um estado "pendente" quando você cria um ponto de extremidade privado gerenciado no Azure Data Factory. Um fluxo de trabalho de aprovação é iniciado. O proprietário do recurso de link privado é responsável por aprovar ou rejeitar a conexão.

![Gerenciar o ponto de extremidade privado](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Se o proprietário aprova a conexão, o link privado é estabelecido. Caso contrário, o link privado não será estabelecido. Em ambos os casos, o Ponto de extremidade privado gerenciado será atualizado com o status da conexão.

![Aprovar ponto de extremidade privado gerenciado](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Somente um Ponto de extremidade privado gerenciado em um estado aprovado pode enviar tráfego para um determinado recurso de link privado.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos
### <a name="supported-data-sources"></a>Fontes de dados com suporte
As fontes de dados a seguir têm suporte para se conectar por meio do link privado da rede virtual gerenciada do ADF.
- Armazenamento de Blobs do Azure
- Armazenamento de Tabelas do Azure
- Arquivos do Azure
- Azure Data Lake Gen2
- Banco de dados SQL do Azure (sem incluir o Azure SQL Instância Gerenciada)
- Azure Synapse Analytics (antigo SQL Data Warehouse do Azure)
- SQL CosmosDB do Azure
- Cofre de Chave do Azure
- Link Privado do Azure

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicações de saída por meio do ponto de extremidade público da rede virtual gerenciada por ADF
- Somente a porta 443 é aberta para comunicações de saída.
- O armazenamento do Azure e o Azure Data Lake Gen2 não têm suporte para serem conectados por meio do ponto de extremidade público da rede virtual gerenciada por ADF.

### <a name="other-known-issues"></a>Outros problemas conhecidos
A execução de depuração para conectividade CosmosDB não funciona, incluindo depuração de fluxo de trabalho e depuração de pipeline.


## <a name="next-steps"></a>Próximas etapas

- Tutorial: [criar um pipeline de cópia usando rede virtual gerenciada e pontos de extremidade privados](tutorial-copy-data-portal-private.md) 
- Tutorial: [criar um pipeline de fluxo de base de mapeamento usando rede virtual gerenciada e pontos de extremidade privados](tutorial-data-flow-private.md)
