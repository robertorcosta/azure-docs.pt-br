---
title: Endereços de IP do Azure Integration Runtime
description: Saiba quais endereços IP você deve permitir o tráfego de entrada, para configurar corretamente os firewalls para proteger o acesso à rede para armazenamentos de dados.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: f3eed03c585ba6b48a21b36c21cb77585456fc2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660271"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Endereços de IP do Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os endereços IP que Azure Integration Runtime usa depende da região em que o tempo de execução de integração do Azure está localizado. *Todos* Os tempos de execução de integração do Azure que estão na mesma região usam os mesmos intervalos de endereços IP.

> [!IMPORTANT]  
> Os fluxos de dados não dão suporte ao uso de intervalos de IP fixos.
>
> Você pode usar esses intervalos de IP para a movimentação de dados, pipeline e execuções de atividades externas. Esses intervalos de IP podem ser usados para a lista de permissões em armazenamentos de dados/grupo de segurança de rede (NSG)/firewalls para acesso de entrada do tempo de execução de integração do Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime endereços IP: regiões específicas

Permita o tráfego dos endereços IP listados para o tempo de execução de integração do Azure na região específica do Azure onde os recursos estão localizados. Você pode obter uma lista de intervalos IP de marcas de serviço no [link de download do intervalo de IP das marcas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). Por exemplo, se a região do Azure for **AustraliaEast**, você poderá obter uma lista de intervalos de IP de **DataFactory. AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Problema conhecido com o armazenamento do Azure

* Ao se conectar à conta de armazenamento do Azure, as regras de rede IP não têm nenhum efeito nas solicitações originadas do tempo de execução de integração do Azure na mesma região que a conta de armazenamento. Para obter mais detalhes, [Veja este artigo](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Em vez disso, sugerimos o uso [de serviços confiáveis durante a conexão com o armazenamento do Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Próximas etapas

* [Considerações sobre segurança para movimentação de dados no Azure Data Factory](data-movement-security-considerations.md)
