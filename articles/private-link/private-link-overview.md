---
title: O que é o Link Privado do Azure?
description: Visão geral dos recursos do, da arquitetura e da implementação do Link Privado do Azure. Saiba como os Pontos de Extremidade Privados do Azure e o serviço de Link Privado do Azure funcionam e como usá-los.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 710c5a780841135344d92e93a02f97963b36b09e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921383"
---
# <a name="what-is-azure-private-link"></a>O que é o Link Privado do Azure? 
O Link Privado do Azure permite que você acesse os Serviços PaaS do Azure como:
 
 - **Armazenamento do Azure**
 - **Azure Cosmos DB**
 - **Banco de Dados SQL do Azure**

O Link Privado permite acesso ao cliente hospedado e aos serviços de parceiros por meio de um [ponto de extremidade privado](private-endpoint-overview.md) em sua rede virtual.

O tráfego entre sua rede virtual e o serviço viaja a rede de backbone da Microsoft. Expor seu serviço à Internet pública não é mais necessário. Você pode criar o próprio [serviço de link privado](private-link-service-overview.md) em sua rede virtual e oferecê-lo aos seus clientes. A configuração e o consumo usando o Link Privado do Azure são consistentes entre os serviços de parceiro de PaaS do Azure, de propriedade do cliente e de parceiros compartilhados.

> [!IMPORTANT]
> O Link Privado do Azure já está em disponibilidade geral. O Ponto de Extremidade Privado e o serviço de Link Privado (serviço por trás do Standard Load Balancer) estão em disponibilidade geral. O Azure PaaS diferente será integrado ao Link Privado do Azure em datas diferentes. Verifique a seção de [disponibilidade](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) abaixo para obter o status preciso do PaaS do Azure no Link Privado. Para conhecer as limitações conhecidas, confira [Ponto de Extremidade Privado](private-endpoint-overview.md#limitations) e [Serviço de Link Privado](private-link-service-overview.md#limitations). 

![Visão geral do ponto de extremidade privado](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Principais benefícios
O Link Privado do Azure fornece os seguintes benefícios:  
- **Acesse os serviços de forma privada na plataforma Azure**: Conecte sua rede virtual a serviços no Azure sem um endereço IP público na origem ou no destino. Os provedores de serviços podem renderizar seus serviços em sua rede virtual e os consumidores podem acessar esses serviços em sua rede virtual local. A plataforma de Link Privado manipulará a conectividade entre o consumidor e os serviços na rede de backbone do Azure. 
 
- **Redes locais e emparelhadas**: Acesse serviços em execução no Azure do local por meio de emparelhamento privado do ExpressRoute, túneis de VPN e redes virtuais emparelhadas usando pontos de extremidade privados. Não é necessário configurar o emparelhamento público nem atravessar a Internet para acessar o serviço. O Link Privado fornece uma forma segura de migrar cargas de trabalho para o Azure.
 
- **Proteção contra vazamento de dados**: um ponto de extremidade privado é mapeado para uma instância de um recurso de PaaS em vez de todo o serviço. Os consumidores só podem se conectar ao recurso específico. O acesso a qualquer outro recurso no serviço é bloqueado. Esse mecanismo fornece proteção contra riscos de vazamento de dados. 
 
- **Alcance Global**: Conecte-se de maneira privada aos serviços executados em outras regiões. A rede virtual do consumidor pode estar na região A e pode se conectar aos serviços por trás do Link Privado na região B.  
 
- **Estenda para seus próprios serviços**: Habilite a mesma experiência e a mesma funcionalidade para renderizar seu serviço de maneira privada para consumidores no Azure. Colocando seu serviço atrás de um Azure Load Balancer padrão, você pode habilitá-lo para o Link Privado. O consumidor pode então se conectar diretamente ao seu serviço usando um ponto de extremidade privado em sua rede virtual. Você pode gerenciar as solicitações de conexão usando um fluxo de chamada de aprovação. O Link Privado do Azure funciona para consumidores e serviços que pertencem a locatários diferentes do Azure Active Directory. 

## <a name="availability"></a>Disponibilidade 
 A tabela a seguir lista os serviços de Link Privado e as regiões em que estão disponíveis. 

|Cenário  |Serviços com suporte  |Regiões disponíveis | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Link Privado para serviços de propriedade do cliente|Serviços de Link Privado atrás do Azure Load Balancer padrão | Todas as regiões públicas  | GA <br/> [Saiba mais](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Link Privado para os serviços de PaaS do Azure   | Armazenamento do Azure        |  Todas as regiões públicas      | Visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints)  |
|  | Azure Data Lake Storage Gen2        |  Todas as regiões públicas      | Visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints)  |
|  |  Banco de Dados SQL do Azure         | Todas as regiões públicas      |   Visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
||SQL Data Warehouse do Azure| Todas as regiões públicas |Visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
||Azure Cosmos DB| Centro-oeste dos EUA, Oeste dos EUA e Centro-Norte dos EUA |Visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Banco de Dados do Azure para PostgreSQL – Servidor único         | Todas as regiões públicas      |   Visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Banco de Dados do Azure para MySQL         | Todas as regiões públicas      |   Visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Banco de Dados do Azure para MariaDB         | Todas as regiões públicas      |   Visualização <br/> [Saiba mais](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Cofre de Chave do Azure         | Todas as regiões públicas      |   Visualização   <br/> [Saiba mais](https://docs.microsoft.com/azure/key-vault/private-link-service)   |

Para obter as notificações mais recentes, confira a página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Log e monitoramento

O Link Privado do Azure tem integração ao Azure Monitor. Essa combinação permite:

 - O arquivamento dos logs em uma conta de armazenamento.
 - A transmissão de eventos para o Hub de Eventos.
 - O registro em log do Azure Monitor.

Você pode acessar as seguintes informações no Azure Monitor: 
- **Ponto de extremidade privado**: 
    - dados processados pelo Ponto de Extremidade Privado (ENTRADA/SAÍDA)
 
- **Serviço de Link Privado**:
    - dados processados pelo serviço de Link Privado (ENTRADA/SAÍDA)
    - Disponibilidade da porta NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Perguntas frequentes  
Para perguntas frequentes, confira [Perguntas frequentes sobre Link Privado do Azure](private-link-faq.md).
 
## <a name="limits"></a>limites  
Para limites, confira [Limites do Link Privado do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>SLA (Contrato de Nível de Serviço)
Para SLA, confira [SLA para o Link Privado do Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar um ponto de extremidade privado usando portal do Azure](create-private-endpoint-portal.md)
- [Início Rápido: Criar um serviço de Link Privado usando o portal do Azure](create-private-link-service-portal.md)


 
