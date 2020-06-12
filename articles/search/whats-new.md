---
title: Novos comunicados de recursos
titleSuffix: Azure Cognitive Search
description: Comunicados de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: f8ddc3ef56aefc6ea613af19d91cd0122435efd7
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344196"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades no Azure Cognitive Search

Conheça o que há de novo no serviço. Marque esta página para manter-se atualizado quanto ao serviço.

## <a name="feature-announcements"></a>Comunicados de recursos

### <a name="may-2020-microsoft-build"></a>Maio de 2020 (Microsoft Build)

+ O recurso de [sessões de depuração](cognitive-search-debug-session.md) está na versão prévia. [Inscreva-se para solicitar o acesso](https://aka.ms/DebugSessions). As sessões de depuração fornecem uma interface baseada em portal para investigar e resolver problemas com um conjunto de habilidades. Correções criadas na sessão de depuração podem ser salvas em conjuntos de habilidades de produção. Veja uma introdução [neste tutorial](cognitive-search-tutorial-debug-sessions.md).

+ Os aprimoramentos de segurança incluem a capacidade de [configurar um ponto de extremidade de pesquisa particular (versão prévia)](service-create-private-endpoint.md) que é inacessível na Internet pública. Você também pode [configurar regras de IP para suporte de firewall de entrada (versão prévia)](service-configure-firewall.md).

+ Use uma [identidade gerenciada pelo sistema (versão prévia)](search-howto-managed-identities-data-sources.md) para configurar uma conexão com uma fonte de dados do Azure para indexação. Aplica-se a [indexadores](search-indexer-overview.md) que ingerem conteúdo de fontes de dados do Azure, como o Banco de Dados SQL do Azure, o Azure Cosmos DB e o Armazenamento do Azure.

+ Altere a forma como as pontuações de pesquisa são calculadas, de por fragmento para todos os fragmentos, usando os parâmetros de consulta [scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics) e sessionId.

### <a name="march-2020"></a>Março de 2020

+ [Exclusão temporária do blob nativo (versão prévia)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) significa que o indexador do Armazenamento de Blobs do Azure no Azure Cognitive Search reconhecerá os blobs que estão no estado de exclusão temporária e removerá o documento de pesquisa correspondente durante a indexação.

+ A nova [API REST de Gerenciamento (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) estável está disponível. 

### <a name="february-2020"></a>Fevereiro de 2020

+ A [Detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações de identificação pessoal de um texto de entrada e oferece a opção de mascará-lo de várias maneiras.

+ A [Pesquisa de Entidade Personalizada (versão prévia)](cognitive-search-skill-custom-entity-lookup.md ) procura o texto de uma lista personalizada e definida pelo usuário de palavras e frases. Usando essa lista, ela rotula todos os documentos com entidades correspondentes. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências semelhantes, mas não exatas. 

### <a name="january-2020"></a>Janeiro de 2020

+ As [chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) agora estão em disponibilidade geral. Se você estiver usando REST, poderá acessar o recurso usando `api-version=2019-05-06`. No código gerenciado, o pacote correto ainda é o [SDK do .NET versão 8.0-versão prévia](search-dotnet-sdk-migration-version-9.md) mesmo que o recurso esteja fora da versão prévia. 

+ O acesso privado a um serviço de pesquisa está disponível por meio de dois mecanismos, ambos atualmente em versão prévia:

  + Você pode restringir o acesso a endereços IP específicos usando a API REST de Gerenciamento `api-version=2019-10-01-Preview` para criar o serviço. A API de versão prévia tem novas propriedades **IpRule** e **NetworkRuleSet** na [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Essa versão prévia do recurso está disponível em regiões selecionadas. Para obter mais informações, confira [Como usar a API REST de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Atualmente disponível por meio de uma versão prévia de acesso limitado, você pode provisionar um serviço do Azure Search que dê suporte ao Ponto de Extremidade Privado do Azure para conexões de clientes na mesma rede virtual. Para obter mais informações, confira [Criar um Ponto de Extremidade Privado para uma conexão segura](service-create-private-endpoint.md).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar aplicativo (versão prévia)](search-create-app-portal.md) é um novo assistente no portal que gera um arquivo HTML para download. O arquivo é fornecido com um script inserido que renderiza um aplicativo Web no estilo “localhost” operacional, associado a um índice em seu serviço de pesquisa. As páginas podem ser configuradas no assistente e podem conter uma barra de pesquisa, área de resultados, navegação de barra lateral e o suporte a consultas de digitação antecipada. Você pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência.

+ [Criar um ponto de extremidade privado para conexões seguras (versão prévia)](service-create-private-endpoint.md) explica como configurar um Link Privado para conexões seguras com seu serviço de pesquisa. Essa versão prévia do recurso está disponível mediante solicitação e usa o [Link Privado do Azure](../private-link/private-link-overview.md) e a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) como parte da solução.

### <a name="november-2019---ignite-conference"></a>Novembro de 2019 – Conferência do Ignite

+ O [enriquecimento incremental (versão prévia)](cognitive-search-incremental-indexing-conceptual.md) adiciona o cache e a capacidade de manutenção de status a um pipeline de enriquecimento para você poder trabalhar em etapas ou fases específicas sem perder o conteúdo que já foi processado. Anteriormente, qualquer alteração em um pipeline de enriquecimento exigia uma recompilação completa. Com o enriquecimento incremental, a saída da análise cara, especialmente a análise de imagens, é preservada.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [Extração de Documentos (versão prévia)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva usada durante a indexação que permite extrair o conteúdo de um arquivo de dentro de um conjunto de habilidades. Anteriormente, a quebra de documento só ocorria antes da execução do conjunto de habilidades. Com a adição dessa habilidade, você também pode executar essa operação dentro da execução do conjunto de habilidades.

+ A [Tradução de Texto](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva usada durante a indexação que avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado.

+ Os [modelos do Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar rapidamente suas visualizações e a análise de conteúdo enriquecido em um repositório de conhecimento na área de trabalho do Power BI. Esse modelo foi projetado para projeções de tabela do Azure criadas por meio do [assistente para Importar dados](knowledge-store-create-portal.md).

+ Agora há suporte ao [Azure Data Lake Storage Gen2 (versão prévia)](search-howto-index-azure-data-lake-storage.md), à [API do Gremlin do Cosmos DB (versão prévia)](search-howto-index-cosmosdb.md) e à [API do Cassandra do Cosmos DB (versão prévia)](search-howto-index-cosmosdb.md) em indexadores. Você pode se inscrever usando [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um email de confirmação após ser aceito no programa de versão prévia.

### <a name="july-2019"></a>Julho de 2019

+ Em disponibilidade geral na [Nuvem do Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nome do novo serviço

O Azure Search foi renomeado para **Azure Cognitive Search** para refletir o uso expandido (ainda opcional) de habilidades cognitivas e processamento de IA nas principais operações. As versões de API, pacotes NuGet, namespaces e pontos de extremidade não são alteradas. As soluções de pesquisa novas e existentes não são afetadas pela alteração do nome do serviço.

## <a name="service-updates"></a>Atualizações de serviço

Os [comunicados de atualização de serviço](https://azure.microsoft.com/updates/?product=search&status=all) para o Azure Cognitive Search podem ser encontrados no site do Azure.