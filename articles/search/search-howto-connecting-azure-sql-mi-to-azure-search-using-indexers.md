---
title: Conexão de instância gerenciada do Azure SQL para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite o ponto final público para permitir conexões a Instâncias Gerenciadas SQL a partir de um indexador na Pesquisa Cognitiva do Azure.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964882"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configure uma conexão de um indexador de pesquisa cognitiva do Azure à instância gerenciada do SQL

Como observado no [ConnectIng Azure SQL Database to Azure Cognitive Search usando indexadores,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)a criação de indexadores contra **instâncias gerenciadas pelo SQL** é suportada pelo Azure Cognitive Search através do ponto final público.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Criar a instância gerenciada do Azure SQL com ponto final público
Crie uma instância gerenciada sql com a opção **Ativar ponto final público** selecionada.

   ![Habilitar o ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Habilitar o ponto de extremidade público")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Habilitar o ponto final público da instância gerenciada do Azure SQL
Você também pode habilitar o ponto final público em uma instância gerenciada sql existente em **Security** > **Virtual network** > **Public endpoint** > **Enable**.

   ![Habilitar o ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Habilitar o ponto de extremidade público")

## <a name="verify-nsg-rules"></a>Verifique as regras do NSG
Verifique se o Grupo de Segurança de Rede tem as regras de **segurança de entrada** corretas que permitem conexões dos serviços do Azure.

   ![Regra de segurança de entrada do NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regra de segurança de entrada do NSG")

> [!NOTE]
> Os indexadores ainda exigem que a instância gerenciada do SQL seja configurada com um ponto final público para ler dados.
> No entanto, você pode optar por restringir o acesso de entrada`public_endpoint_inbound`a esse ponto final público, substituindo a regra atual ( ) com as seguintes regras:
>
> * Permitindo o acesso `AzureCognitiveSearch` de entrada da [tag de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`)
>
> * Permitindo o acesso de entrada a partir do endereço IP do serviço de pesquisa, que `<your-search-service-name>.search.windows.net`pode ser obtido pingando seu nome de domínio totalmente qualificado (por exemplo, ). ("FONTE" `IP address`= " NOME" = ) `search_service_inbound`
>
> Para cada uma dessas 2 regras, `3342`defina "PORT" = , "PROTOCOL" = `TCP`, "DESTINATION" = `Any`, "ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Obter seqüência de conexão ponto final público
Certifique-se de usar a seqüência de conexões para o **ponto final público** (porta 3342, não porta 1433).

   ![Cadeia de conexão de ponto final público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadeia de conexão de ponto final público")

## <a name="next-steps"></a>Próximas etapas
Com a configuração fora do caminho, agora você pode especificar uma instância gerenciada sql como a fonte de dados de um indexador de pesquisa cognitiva do Azure usando o portal ou a API REST. Consulte [Conectando o banco de dados SQL do Azure à Pesquisa Cognitiva do Azure usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.
