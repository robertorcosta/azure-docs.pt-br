---
title: Permitir acesso a intervalos de IP do indexador
titleSuffix: Azure Cognitive Search
description: Guia de instruções que descreve como configurar regras de firewall IP para que os indexadores possam ter acesso.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463410"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Configurando regras de firewall IP para habilitar o acesso ao indexador

As regras de firewall de IP nos recursos do Azure, como contas de armazenamento, contas de Cosmos DB e servidores SQL do Azure, só permitem o tráfego proveniente de intervalos de IP específicos para acessar dados.

Este artigo descreverá como configurar as regras de IP, via portal do Azure, para uma conta de armazenamento para que os indexadores do Azure Pesquisa Cognitiva possam acessar os dados com segurança. Embora específico ao armazenamento, este guia pode ser convertido diretamente em outros recursos do Azure que também oferecem regras de firewall IP para proteger o acesso aos dados.

> [!NOTE]
> As regras de firewall IP para a conta de armazenamento só serão efetivadas se a conta de armazenamento e o serviço de pesquisa estiverem em regiões diferentes. Se a instalação não permitir isso, recomendamos utilizar a opção de [exceção de serviço confiável](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obter o endereço IP do serviço de pesquisa

Obtenha o FQDN (nome de domínio totalmente qualificado) do seu serviço de pesquisa. Isso se parecerá `<search-service-name>.search.windows.net` . Você pode descobrir o FQDN procurando o serviço de pesquisa no portal do Azure.

   ![Obter o FQDN do serviço](media\search-indexer-howto-secure-access\search-service-portal.png "Obter o FQDN do serviço")

O endereço IP do serviço de pesquisa pode ser obtido executando um `nslookup` (ou um `ping` ) do FQDN. Esse será um dos endereços IP a serem adicionados às regras de firewall.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Obter os intervalos de endereços IP para a marca de serviço "AzureCognitiveSearch"

Os intervalos de endereços IP para a `AzureCognitiveSearch` marca de serviço podem ser obtidos por meio da [API de descoberta (versão prévia)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou do [arquivo JSON baixável](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Para este passo a passos, supondo que o serviço de pesquisa seja a nuvem pública do Azure, o [arquivo JSON público do Azure](https://www.microsoft.com/download/details.aspx?id=56519) deve ser baixado.

   ![Baixar arquivo JSON](media\search-indexer-howto-secure-access\service-tag.png "Baixar arquivo JSON")

No arquivo JSON, supondo que o serviço de pesquisa está no Oeste EUA Central, a lista de endereços IP para o ambiente de execução do indexador multilocatário está listada abaixo.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Para endereços IP de/32, descarte o "/32" (52.253.133.74/32-> 52.253.133.74), outros podem ser usados literalmente.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Adicionar os intervalos de endereços IP às regras de firewall de IP

A maneira mais fácil de adicionar intervalos de endereços IP à regra de firewall de uma conta de armazenamento é por meio do portal do Azure. Localize a conta de armazenamento no portal e navegue até a guia "**firewalls e redes virtuais**".

   ![Firewall e redes virtuais](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall e redes virtuais")

Adicione os três endereços IP obtidos anteriormente (1 para o IP do serviço de pesquisa, 2 para a `AzureCognitiveSearch` marca de serviço) no intervalo de endereços e clique em "**salvar**"

   ![Regras de IP de firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Regras de IP de firewall")

As regras de firewall levam 5-10 minutos para serem atualizadas depois de quais indexadores poderão acessar os dados na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como obter os dois conjuntos de endereços IP para permitir o acesso a índices, use os links a seguir para atualizar as regras de firewall de IP para algumas fontes de dados comuns.

- [Configurar firewalls do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Configurar o firewall IP para CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Configurar o firewall IP para o SQL Server do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)