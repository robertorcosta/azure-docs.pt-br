---
title: O que é o Link Privado do Azure?
description: Saiba como usar o Link Privado do Azure para acessar os serviços de PaaS do Azure (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e serviços de parceiros/clientes hospedados no Azure em um ponto de extremidade privado em sua rede virtual.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 452202555734a208a9f32d6f8899e1f679df4a68
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443985"
---
# <a name="what-is-azure-private-link"></a>O que é o Link Privado do Azure? 
O Link Privado do Azure te habilita a acessar os serviços de PaaS do Azure (por exemplo, Armazenamento do Azure e Banco de Dados SQL e Azure Cosmos DB) e serviços de parceiros/clientes hospedados no Azure em um [Ponto de Extremidade Privado](private-endpoint-overview.md) em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também é possível criar seu próprio [Serviço de Link Privado](private-link-service-overview.md) em sua VNet (rede virtual) e fornecê-lo de forma privada aos seus clientes. A experiência de configuração e consumo usando o Link Privado do Azure é consistente entre os serviços de parceiro de PaaS do Azure, de propriedade do cliente e de parceiros compartilhados.

![Visão geral do ponto de extremidade privado](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Principais benefícios
O Link Privado do Azure fornece os seguintes benefícios:  
- **Acesse os serviços de forma privada na plataforma Azure**: Conecte sua rede virtual a serviços em execução no Azure de forma privada sem precisar de um endereço IP público na origem ou no destino. Os provedores de serviços podem renderizar seus serviços de forma privada em sua própria rede virtual e os consumidores podem acessar esses serviços de forma privada em sua rede virtual local. A plataforma de Link Privado manipulará a conectividade entre o consumidor e os serviços na rede de backbone do Azure. 
 
- **Redes locais e emparelhadas**: Acesse serviços em execução no Azure do local por meio de túneis de VPN/emparelhamento privado do ExpressRoute (do local) e redes virtuais emparelhadas usando pontos de extremidade privados. Não é necessário configurar o emparelhamento público nem atravessar a Internet para acessar o serviço. Essa capacidade fornece uma maneira segura de migrar cargas de trabalho para o Azure.
 
- **Proteção contra vazamento de dados**:  com o Link Privado do Azure, o ponto de extremidade privado na VNet é mapeado para uma instância específica do recurso de PaaS do cliente, em vez de todo o serviço. Usando o ponto de extremidade privado, os consumidores só podem se conectar ao recurso específico e a nenhum outro. Esse mecanismo interno fornece proteção contra riscos de vazamento de dados. 
 
- **Alcance Global**: Conecte-se de maneira privada aos serviços executados em outras regiões. Isso significa que a rede virtual do consumidor pode estar na região A e pode se conectar aos serviços por trás do Link Privado na região B.  
 
- **Estenda para seus próprios serviços**: Aproveite a mesma experiência e a mesma funcionalidade para renderizar seu próprio serviço de forma privada para seus consumidores no Azure. Colocando seu serviço atrás de um Standard Load Balancer, você pode habilitá-lo para o Link Privado. O consumidor então pode se conectar diretamente ao seu serviço usando um ponto de extremidade privado em sua própria VNet. Você pode gerenciar essas solicitações de conexão usando um fluxo de chamada de aprovação simples. O Link Privado do Azure funciona também para consumidores e serviços que pertencem a locatários diferentes do Active Directory. 

## <a name="availability"></a>Disponibilidade 
 A tabela a seguir lista os serviços de Link Privado e as regiões em que estão disponíveis. 

|Cenário  |Serviços com suporte  |Regiões disponíveis | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Link Privado para serviços de propriedade do cliente|Serviços de Link Privado atrás do Standard Load Balancer | Todas as regiões públicas  | Visualização  |
|Link Privado para os serviços de PaaS do Azure   | Armazenamento do Azure        |  Todas as regiões públicas      | Visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints).  |
|  | Azure Data Lake Storage Gen2        |  Todas as regiões públicas      | Visualização <br/> [Saiba mais](/azure/storage/common/storage-private-endpoints).  |
|  |  Banco de Dados SQL do Azure         | Todas as regiões públicas      |   Visualização      |
||SQL Data Warehouse do Azure| Todas as regiões públicas |Visualização|
||Azure Cosmos DB| Centro-oeste dos EUA, Oeste dos EUA e Centro-Norte dos EUA |Visualização|
|  |  Banco de Dados do Azure para PostgreSQL – Servidor único         | Todas as regiões públicas      |   Visualização      |
|  |  Banco de Dados do Azure para MySQL         | Todas as regiões públicas      |   Visualização      |
|  |  Banco de Dados do Azure para MariaDB         | Todas as regiões públicas      |   Visualização      |
|  |  Cofre de Chave do Azure         | Todas as regiões públicas      |   Visualização      |

Para obter as notificações mais recentes, confira a página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Log e monitoramento

O Link Privado do Azure está integrado ao Azure Monitor, permitindo que você arquive logs em uma conta de armazenamento, transmita eventos ao Hub de Eventos ou envie-os aos logs do Azure Monitor. Você pode acessar as seguintes informações no Azure Monitor: 
- **Ponto de extremidade privado**: dados processados pelo Ponto de Extremidade Privado (ENTRADA/SAÍDA)
 
- **Serviço de Link Privado**:
    - dados processados pelo serviço de Link Privado (ENTRADA/SAÍDA)
    - Disponibilidade da porta NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Perguntas frequentes  
Para perguntas frequentes, confira [Perguntas frequentes sobre Link Privado do Azure](private-link-faq.md).
 
## <a name="limits"></a>limites  
Para limites, confira [Limites do Link Privado do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Próximas etapas
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando o portal](create-private-endpoint-portal.md)
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando o PowerShell](create-private-endpoint-powershell.md)
- [Criar um Ponto de Extremidade Privado para o Servidor do Banco de Dados SQL usando a CLI ](create-private-endpoint-cli.md)
- [Criar um Ponto de Extremidade Privado para a conta de Armazenamento usando o portal ](create-private-endpoint-storage-portal.md)
- [Criar um Ponto de Extremidade Privado para a conta do Azure Cosmos usando o portal ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Criar seu próprio serviço de Link Privado usando o Azure PowerShell](create-private-link-service-powershell.md)


 
