---
title: Novidades no Azure Cognitive Search
description: Comunicados de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/30/2020
ms.openlocfilehash: 078892691bfaec62f71f9d601a42de3f80221149
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958150"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades no Azure Cognitive Search

Conheça o que há de novo no serviço. Marque esta página para manter-se atualizado quanto ao serviço.

## <a name="feature-announcements"></a>Comunicados de recursos

### <a name="june-2020"></a>Junho de 2020

+ O [Repositório de conhecimento](knowledge-store-concept-intro.md) já está em disponibilidade geral.

+ A [API REST de Serviço de Pesquisa 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) é a nova versão estável das APIs REST. Além do repositório de conhecimento, essa versão em disponibilidade geral inclui aprimoramentos à relevância e à pontuação da pesquisa.

+ O novo algoritmo de classificação de relevância agora é [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) para qualquer serviço que você criar. Para os serviços existentes, você pode optar por definir a propriedade `similarity` em campos de índice. Essa propriedade está em disponibilidade geral.

+ O novo indexador `executionEnvironment` pode ser definido explicitamente como `private`. Essa funcionalidade dá suporte ao acesso do indexador a dados externos em pontos de extremidade privados e está em disponibilidade geral.

+ O [AML (Azure Machine Learning)](cognitive-search-aml-skill.md) é um novo tipo de habilidade para integrar um ponto de extremidade de inferência por meio do Azure Machine Learning. A experiência do portal dá suporte à descoberta e à integração do seu ponto de extremidade do Azure Machine Learning em um conjunto de habilidades do Cognitive Search. A descoberta exige que o Cognitive Search e os serviços do Azure ML sejam implantados na mesma assinatura. Essa habilidade está em disponibilidade geral. Veja uma introdução [neste tutorial](cognitive-search-tutorial-aml-custom-skill.md).

### <a name="may-2020-microsoft-build"></a>Maio de 2020 (Microsoft Build)

+ O recurso de [sessões de depuração](cognitive-search-debug-session.md) está na versão prévia. As sessões de depuração fornecem uma interface baseada em portal para investigar e resolver problemas com um conjunto de habilidades. Correções criadas na sessão de depuração podem ser salvas em conjuntos de habilidades de produção. Veja uma introdução [neste tutorial](cognitive-search-tutorial-debug-sessions.md).

+ Proteja um ponto de extremidade de serviço de pesquisa da Internet pública [configurando regras de IP para o suporte de firewall de entrada](service-configure-firewall.md) ou aproveitando o [Link Privado do Azure para um ponto de extremidade de pesquisa particular](service-create-private-endpoint.md). Os dois recursos estão em disponibilidade geral.

+ Use uma [identidade gerenciada pelo sistema (versão prévia)](search-howto-managed-identities-data-sources.md) para configurar uma conexão com uma fonte de dados do Azure para indexação. Aplica-se a [indexadores](search-indexer-overview.md) que ingerem conteúdo de fontes de dados do Azure, como o Banco de Dados SQL do Azure, o Azure Cosmos DB e o Armazenamento do Azure.

+ Altere a forma como as pontuações de pesquisa são calculadas, de por fragmento a todos os fragmentos, usando os parâmetros de consulta [sessionId](index-similarity-and-scoring.md) e [scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics). Esses parâmetros estão em disponibilidade geral.

+ Adicione um parâmetro de consulta [featuresMode (versão prévia)](index-similarity-and-scoring.md#featuresMode-param) para expandir uma pontuação de relevância para mostrar mais detalhes: por pontuação de similaridade de campo, por frequência de termo de campo e por número de campo de tokens exclusivos correspondidos. Você pode consumir esses pontos de dados em algoritmos de pontuação personalizados. Para obter um exemplo que demonstra essa funcionalidade, confira [Adicionar machine learning (LearnToRank) à relevância de pesquisa](https://github.com/Azure-Samples/search-ranking-tutorial).

### <a name="march-2020"></a>Março de 2020

+ [Exclusão temporária do blob nativo (versão prévia)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) significa que o indexador do Armazenamento de Blobs do Azure no Azure Cognitive Search reconhecerá os blobs que estão no estado de exclusão temporária e removerá o documento de pesquisa correspondente durante a indexação.

+ A nova [API REST de gerenciamento (13/03/2020)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) estável já está em disponibilidade geral. 

### <a name="february-2020"></a>Fevereiro de 2020

+ A [Detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações de identificação pessoal de um texto de entrada e oferece a opção de mascará-lo de várias maneiras.

+ A [Pesquisa de Entidade Personalizada (versão prévia)](cognitive-search-skill-custom-entity-lookup.md ) procura o texto de uma lista personalizada e definida pelo usuário de palavras e frases. Usando essa lista, ela rotula todos os documentos com entidades correspondentes. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências semelhantes, mas não exatas. 

### <a name="january-2020"></a>Janeiro de 2020

+ As [chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) agora estão em disponibilidade geral. Se você estiver usando REST, poderá acessar o recurso usando `api-version=2019-05-06` ou posterior. No código gerenciado, o pacote correto ainda é o [SDK do .NET versão 8.0-versão prévia](search-dotnet-sdk-migration-version-9.md) mesmo que o recurso esteja fora da versão prévia. 

+ O acesso privado a um serviço de pesquisa está disponível por meio de dois mecanismos, ambos atualmente em versão prévia:

  + Você pode restringir o acesso a endereços IP específicos usando a API REST de Gerenciamento `api-version=2019-10-01-Preview` para criar o serviço. A API de versão prévia tem novas propriedades **IpRule** e **NetworkRuleSet** na [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Essa versão prévia do recurso está disponível em regiões selecionadas. Para obter mais informações, confira [Como usar a API REST de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar Aplicativo de Demonstração (versão prévia)](search-create-app-portal.md) é um novo assistente no portal que gera um arquivo HTML para download com acesso de consulta (somente leitura) a um índice. O arquivo é fornecido com um script inserido que renderiza um aplicativo Web no estilo “localhost” operacional, associado a um índice em seu serviço de pesquisa. As páginas podem ser configuradas no assistente e podem conter uma barra de pesquisa, área de resultados, navegação de barra lateral e o suporte a consultas de digitação antecipada. Você pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência. Um aplicativo de demonstração não é facilmente estendido para incluir camadas de segurança e hospedagem que normalmente são necessárias em cenários de produção. Você deve considerá-lo uma ferramenta de validação e teste, em vez de um atalho para um aplicativo cliente completo.

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