---
title: Solucionar problemas comuns de indexador de pesquisa
titleSuffix: Azure Cognitive Search
description: Corrigir erros e problemas comuns com indexadores no Azure Cognitive Search, incluindo conexão de origem de dados, firewall e documentos ausentes.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190926"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Solução de problemas comuns de indexador no Azure Cognitive Search

Os indexadores podem encontrar uma série de problemas ao indexar dados na Pesquisa Cognitiva do Azure. As principais categorias de dados são:

* [Conectando-se a uma fonte de dados ou outros recursos](#connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Ingestão de documentos para um índice](#index-errors)

## <a name="connection-errors"></a>Erros de conexão

> [!NOTE]
> Os indexadores têm suporte limitado para acessar fontes de dados e outros recursos que são protegidos pelos mecanismos de segurança da rede Azure. Atualmente, os indexadores só podem acessar fontes de dados através de mecanismos de restrição de faixa de endereço IP correspondentes ou regras de NSG quando aplicável. Os detalhes para acessar cada fonte de dados suportada podem ser encontrados abaixo.
>
> Você pode descobrir o endereço IP do seu serviço de pesquisa pingando seu nome de domínio totalmente qualificado (por exemplo, `<your-search-service-name>.search.windows.net`).
>
> Você pode descobrir a faixa `AzureCognitiveSearch` [de](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) serviço do endereço IP usando [arquivos JSON para download](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) ou através da [API de detecção](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)de marca de serviço . A gama de endereços IP é atualizada semanalmente.

### <a name="configure-firewall-rules"></a>Configurar regras de firewall

O Azure Storage, o CosmosDB e o Azure SQL fornecem um firewall configurável. Não há nenhuma mensagem de erro específica quando o firewall estiver habilitado. Normalmente, os erros de `The remote server returned an error: (403) Forbidden` firewall `Credentials provided in the connection string are invalid or have expired`são genéricos e parecem ou .

Existem 2 opções para permitir que os indexadores acessem esses recursos em tal instância:

* Desabilite o firewall, permitindo o acesso de **todas as redes** (se possível).
* Alternativamente, você pode permitir o acesso ao endereço IP do `AzureCognitiveSearch` seu serviço de pesquisa e à faixa de endereço IP de tag de [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) nas regras de firewall do seu recurso (restrição do intervalo de endereçoIP).

Detalhes para configurar restrições de intervalo de endereçoIP para cada tipo de fonte de dados podem ser encontrados a partir dos seguintes links:

* [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Limitação**: Conforme indicado na documentação acima para o Azure Storage, as restrições da gama de endereços IP só funcionarão se o serviço de pesquisa e sua conta de armazenamento estiverem em diferentes regiões.

As funções do Azure (que podem ser usadas como uma [habilidade de Api Web personalizada)](cognitive-search-custom-skill-web-api.md)também suportam [restrições de endereço IP](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). A lista de endereços IP a serem configurados seria o endereço `AzureCognitiveSearch` IP do seu serviço de pesquisa e a faixa de endereço IP da tag de serviço.

Detalhes para acessar dados no servidor SQL em uma VM Azure são descritos [aqui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configure as regras do Grupo de Segurança de Rede (NSG)

Ao acessar dados em uma instância gerenciada por SQL ou quando uma VM do Azure é usada como uri de serviço web para uma [habilidade de Api Web personalizada,](cognitive-search-custom-skill-web-api.md)os clientes não precisam se preocupar com endereços IP específicos.

Nesses casos, a VM do Azure ou a instância gerenciada pelo SQL podem ser configuradas para residir dentro de uma rede virtual. Em seguida, um grupo de segurança de rede pode ser configurado para filtrar o tipo de tráfego de rede que pode fluir para dentro e para fora das sub-redes de rede virtuais e interfaces de rede.

A `AzureCognitiveSearch` tag de serviço pode ser usada diretamente nas regras do [NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) de entrada sem precisar procurar sua gama de endereços IP.

Mais detalhes para acessar dados em uma instância gerenciada pelo SQL são descritos [aqui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>A "Indexação" do CosmosDB não está habilitada

A Pesquisa Cognitiva do Azure tem uma dependência implícita da indexação do Cosmos DB. Se você desativar a indexação automática no Cosmos DB, a Pesquisa Cognitiva do Azure retorna um estado de sucesso, mas não consegue indexar o conteúdo do contêiner. Para obter instruções sobre como verificar as configurações e ative a indexação, consulte [Gerenciar a indexação no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Erros de processamento de documentos

### <a name="unprocessable-or-unsupported-documents"></a>Documentos não processável ou sem suporte

Os documentos do indexador [de BLOB cujos formatos de documento são explicitamente compatíveis.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Às vezes, um contêiner de armazenamento de Blob contém documentos não compatíveis. Outras vezes, pode haver um documentos problemáticos. Você pode evitar interromper seu indexador nesses documentos ao [Alternar as opções de configuração](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Conteúdo do documento ausente

O indexador de Blob [localiza e extrai o texto de blobs em um contêiner](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com a extração de texto incluem:

* O documento contém apenas imagens digitalizadas. Blobs PDF que têm conteúdo não textual, como imagens digitalizadas (JPGs), não produzem resultados em um pipeline de indexação de Blob padrão. Se você tem conteúdo de imagem com elementos de texto, você pode usar a [pesquisa cognitiva](cognitive-search-concept-image-scenarios.md) para encontrar e extrair o texto.
* O indexador de Blob está configurado para metadados do índice. Para extrair o conteúdo, o indexador de Blob deve ser configurado para [extrair o conteúdo e metadados](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erros de índice

### <a name="missing-documents"></a>Documentos ausentes

Os indexadores encontram documentos de uma [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Às vezes, um documento da fonte de dados que deve ter sido indexado, aparece faltando em um índice. Há duas razões comuns para que esses erros ocorram:

* O documento ainda não foi indexado. Verifique o portal para uma execução bem-sucedida do indexador.
* O documento foi atualizado após o execução do indexador. Se o indexador estiver em um [agendamento](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), eventualmente será executado novamente e pegará o documento.
* A [consulta](/rest/api/searchservice/create-data-source) especificada nos dados de origem exclui o documento. Os indexadores não podem indexar documentos que não fazem parte da fonte de dados.
* [Mapeamentos de campo](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) ou [enriquecimento de IA](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) mudaram o documento e parece diferente do que você espera.
* Use a [API Procurar documento](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para localizar seu documento.
