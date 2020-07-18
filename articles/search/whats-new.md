---
title: Novidades no Azure Cognitive Search
description: Comunicados de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/10/2020
ms.openlocfilehash: d2567a5a0374fd05f17e1153c3b2d7f3df50e0d9
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246989"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades no Azure Cognitive Search

Conheça o que há de novo no serviço. Marque esta página para manter-se atualizado quanto ao serviço.

## <a name="feature-announcements"></a>Comunicados de recursos

### <a name="june-2020"></a>Junho de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
[**Repositório de conhecimento**](knowledge-store-concept-intro.md) | Enriquecimento de IA | Saída de um indexador aprimorado de IA, armazenando conteúdo no Armazenamento do Azure para uso em outros aplicativos e processos. | Em disponibilidade geral. </br> Use a [API REST de Pesquisa 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) ou posterior ou o portal. |
| [**API REST de Pesquisa de 2020-06-30**](https://docs.microsoft.com/rest/api/searchservice/) | REST | Uma nova versão estável das APIs REST. Além do repositório de conhecimento, essa versão inclui aprimoramentos à relevância e à pontuação da pesquisa. | Em disponibilidade geral. |
| [**Algoritmo de relevância Okapi BM25**](https://en.wikipedia.org/wiki/Okapi_BM25) | Consulta | Novo algoritmo de classificação de relevância usado automaticamente para todos os serviços de pesquisa criados após 15 de julho. Para os serviços criados antes dessa data, você pode optar por definir a propriedade `similarity` em campos de índice. | Em disponibilidade geral. </br> Use a [API REST de Pesquisa 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) ou posterior ou a API REST 2019-05-06. |
| **executionEnvironment** | Segurança (indexadores) | Defina explicitamente essa propriedade de configuração do indexador como `private` para forçar todas as conexões a fontes de dados externas por um ponto de extremidade privado. Aplicável somente aos serviços de pesquisa que aproveitam o Link Privado do Azure. | Em disponibilidade geral. </br> Use [API REST de Pesquisa 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) para definir esse parâmetro de configuração geral. |

### <a name="may-2020-microsoft-build"></a>Maio de 2020 (Microsoft Build)

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Sessões de depuração**](cognitive-search-debug-session.md) | Enriquecimento de IA | As sessões de depuração fornecem uma interface baseada em portal para investigar e resolver problemas com um conjunto de habilidades existente. Correções criadas na sessão de depuração podem ser salvas em conjuntos de habilidades de produção. Veja uma introdução [neste tutorial](cognitive-search-tutorial-debug-sessions.md). | Versão prévia pública, no portal. |
| [**Regras de IP para suporte de firewall de entrada**](service-configure-firewall.md) | Segurança | Limite o acesso a um ponto de extremidade de serviço de pesquisa a endereços IP específicos. | Em disponibilidade geral. </br> Use a [API REST de Gerenciamento 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) ou posterior ou o portal. |
| [**Link Privado do Azure para um ponto de extremidade de pesquisa privado**](service-create-private-endpoint.md) | Segurança| Proteja um serviço de pesquisa da Internet pública executando-o como um recurso de link privado, acessível somente para aplicativos cliente e outros serviços do Azure na mesma rede virtual. | Em disponibilidade geral. </br> Use a [API REST de Gerenciamento 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) ou posterior ou o portal. |
| [**Identidade gerenciada pelo sistema (versão prévia)** ](search-howto-managed-identities-data-sources.md) | Segurança (indexadores) | Registre um serviço de pesquisa como um serviço confiável com Azure Active Directory para configurar conexões com uma fonte de dados do Azure compatível para fins de indexação. Aplica-se a [indexadores](search-indexer-overview.md) que ingerem conteúdo de fontes de dados do Azure, como o Banco de Dados SQL do Azure, o Azure Cosmos DB e o Armazenamento do Azure. | Versão prévia pública. </br> Use o portal para registrar o serviço de pesquisa. |
| [**parâmetro de consulta sessionId**](index-similarity-and-scoring.md), [parâmetro scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics) | Consulta (relevância) | Adicione sessionID a uma consulta para estabelecer uma sessão para calcular pontuações de pesquisa, com scoringStatistics=global para coletar pontuações de todos os fragmentos, de modo a obter cálculos de pontuação de pesquisa mais consistentes. | Em disponibilidade geral. </br> Use a [API REST de Pesquisa 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) ou posterior ou a API REST 2019-05-06. |
| [**featuresMode (versão prévia)** ](index-similarity-and-scoring.md#featuresMode-param) | Consulta | Adicione esse parâmetro de consulta para expandir uma pontuação de relevância para mostrar mais detalhes: por pontuação de similaridade de campo, por frequência de termo de campo e por número de campo de tokens exclusivos correspondidos. Você pode consumir esses pontos de dados em algoritmos de pontuação personalizados. Para obter um exemplo que demonstra essa funcionalidade, confira [Adicionar machine learning (LearnToRank) à relevância de pesquisa](https://github.com/Azure-Samples/search-ranking-tutorial). | Versão prévia pública. </br> Use a [API REST de Pesquisa 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) ou a API REST 2019-05-06-Preview. |

### <a name="march-2020"></a>Março de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Exclusão reversível de blob nativo (versão prévia)** ](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | Indexadores | Um indexador do Armazenamento de Blobs do Azure no Azure Cognitive Search reconhecerá os blobs que estão no estado de exclusão temporária e removerá o documento de pesquisa correspondente durante a indexação. | Versão prévia pública. </br> Use a [API REST de Pesquisa 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) e a API REST 2019-05-06-Preview, com o indexador de execução em uma fonte de dados de Blob do Azure que tenha a "exclusão reversível" nativa habilitada. |
| [**API REST de gerenciamento (2020-03-13)** ](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | Nova API REST estável para criar e gerenciar um serviço de pesquisa. Adiciona suporte ao firewall de IP e ao Link Privado | Em disponibilidade geral. |

### <a name="february-2020"></a>Fevereiro de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Detecção de PII (versão prévia)** ](cognitive-search-skill-pii-detection.md) | Enriquecimento de IA | Uma habilidade cognitiva usada durante a indexação que extrai informações de identificação pessoal de um texto de entrada e oferece a opção de mascará-lo de várias maneiras. | Versão prévia pública. </br> Use o portal, a [API REST de Pesquisa 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) ou a API REST 2019-05-06-Preview. |
| [**Pesquisa de Entidade Personalizada (versão prévia)** ](cognitive-search-skill-custom-entity-lookup.md )| Enriquecimento de IA | Uma nova habilidade cognitiva que procura o texto de uma lista personalizada e definida pelo usuário de palavras e frases. Usando essa lista, ela rotula todos os documentos com entidades correspondentes. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências semelhantes, mas não exatas. | Versão prévia pública. </br> Use o portal, a [API REST de Pesquisa 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) ou a API REST 2019-05-06-Preview. |

### <a name="january-2020"></a>Janeiro de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Chaves de criptografia gerenciadas pelo cliente**](search-security-manage-encryption-keys.md) |Segurança | Adiciona uma camada extra de criptografia além da criptografia interna da plataforma. Usando uma chave de criptografia que você cria e gerencia, você pode criptografar o conteúdo do índice e os mapas de sinônimos antes que o conteúdo atinja um serviço de pesquisa. | Em disponibilidade geral. </br> Use a API REST de Pesquisa 2019-05-06 ou posterior. No código gerenciado, o pacote correto ainda é o [SDK do .NET versão 8.0-versão prévia](search-dotnet-sdk-migration-version-9.md) mesmo que o recurso esteja fora da versão prévia. |
| [**Regras de IP para suporte de firewall de entrada (versão prévia)** ](service-configure-firewall.md) | Segurança | Limite o acesso a um ponto de extremidade de serviço de pesquisa a endereços IP específicos. A API de versão prévia tem novas propriedades **IpRule** e **NetworkRuleSet** na [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Essa versão prévia do recurso está disponível em regiões selecionadas. |  Versão prévia pública usando a api-version=2019-10-01-Preview.  |
| [**Link Privado do Azure para um ponto de extremidade de pesquisa privado (versão prévia)** ](service-create-private-endpoint.md) | Segurança| Proteja um serviço de pesquisa da Internet pública executando-o como um recurso de link privado, acessível somente para aplicativos cliente e outros serviços do Azure na mesma rede virtual. | Versão prévia pública usando a api-version=2019-10-01-Preview.  |

## <a name="feature-announcements-in-2019"></a>Comunicados de recursos em 2019

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