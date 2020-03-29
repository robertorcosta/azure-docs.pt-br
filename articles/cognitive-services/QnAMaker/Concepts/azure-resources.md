---
title: Recursos do Azure - QnA Maker
description: QnA Maker usa várias fontes do Azure, cada uma com um propósito diferente. Entender como eles são usados individualmente permite que você planeje e selecione o nível de preços correto ou saiba quando alterar sua camada de preços. Entender como eles são usados em combinação permite encontrar e corrigir problemas quando eles ocorrem.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 8a5cc0f4889e31470514015035a92d230c40ed43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284238"
---
# <a name="azure-resources-for-qna-maker"></a>Recursos do Azure para QnA Maker

QnA Maker usa várias fontes do Azure, cada uma com um propósito diferente. Entender como eles são usados individualmente permite que você planeje e selecione o nível de preços correto ou saiba quando alterar sua camada de preços. Entender como eles são usados _em combinação_ permite encontrar e corrigir problemas quando eles ocorrem.

## <a name="resource-planning"></a>Planejamento de recursos

Quando você desenvolve pela primeira vez uma base de conhecimento QnA Maker, na fase de protótipo, é comum ter um único recurso QnA Maker para testes e produção.

Quando você passar para a fase de desenvolvimento do projeto, você deve considerar:

* quantas línguas seu sistema de base de conhecimento irá manter
* quantas regiões você precisa de sua base de conhecimento para estar disponível em/a partir de
* quantos documentos em cada domínio seu sistema irá manter

Planeje ter um único recurso QnA Maker deter todas as bases de conhecimento que tenham a mesma linguagem, a mesma região e a mesma combinação de domínio de assunto.

## <a name="pricing-tier-considerations"></a>Considerações do nível de preços

Normalmente, há três parâmetros que você precisa considerar:

* **A taxa de transferência que você precisa do serviço**:
    * selecione o [Plano de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para o serviço de aplicativo com base em suas necessidades. Você pode [escalar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente o Aplicativo.
    * Isso também deve influenciar sua seleção de SKU **de Pesquisa Cognitiva** do Azure, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier). Além disso, talvez seja necessário ajustar a [capacidade](../../../search/search-capacity-planning.md) de pesquisa cognitiva com réplicas.

* **Tamanho e o número de bases de dados de conhecimento**: escolha a [SKU de pesquisa do Azure](https://azure.microsoft.com/pricing/details/search/) apropriada para seu cenário. Normalmente, você decide o número de bases de conhecimento que precisa com base no número de domínios de assunto diferentes. Uma vez que o domínio do assunto (para um único idioma) deve estar em uma base de conhecimento.

    Você pode publicar as bases de dados de conhecimento N-1 em uma camada específica, onde N representa os índices máximos permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice é usado para todas as bases de dados de conhecimento para criação e teste.

* **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento do QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs a três (cada uma com 1 MB de tamanho). O padrão de SKU não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                        | Gerenciamento do QnA Maker | Serviço de Aplicativo | Pesquisa Cognitiva do Azure | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Camada Gratuita   | Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| Ambiente de Desenvolvimento/Teste   | SKU Standard         | Compartilhado      | Basic        | Publicar até 14 KBs, com tamanho de 2 GB    |
| Ambiente de Produção | SKU Standard         | Basic       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

## <a name="when-to-change-a-pricing-tier"></a>Quando alterar um nível de preço

|Atualizar|Motivo|
|--|--|
|[Atualização](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker gestão SKU|Você deseja ter mais conjuntos de QnA ou fontes de documentos em sua base de conhecimento.|
|[Atualização](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) Serviço de aplicativo SKU e verificar nível de pesquisa cognitiva e [criar réplicas de pesquisa cognitiva](../../../search/search-capacity-planning.md)|Sua base de conhecimento precisa atender mais solicitações do seu aplicativo cliente, como um bot de bate-papo.|
|[Atualização](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Serviço de Busca Cognitiva Azure|Você planeja ter muitas bases de conhecimento.|

Obtenha as últimas atualizações de tempo de [execução atualizando seu Serviço de Aplicativo no portal Azure](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Considerações de nomeação de recursos

O nome de recurso para o recurso `qna-westus-f0-b`QnA Maker, como , também é usado para nomear os outros recursos.

A janela de criação do portal Azure permite criar um recurso do QnA Maker e selecionar os níveis de preços para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Captura de tela do portal Azure para criação de recursos do QnA Maker](../media/concept-azure-resource/create-blade.png)

Depois que os recursos são criados, eles têm o mesmo nome, exceto pelo recurso opcional Application Insights, que adia caracteres para o nome.

> [!div class="mx-imgBorder"]
> ![Captura de tela da lista de recursos do portal Azure](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Crie um novo grupo de recursos quando você criar um recurso Do Criador de QnA. Isso permite que você veja todos os recursos associados ao recurso QnA Maker ao pesquisar por grupo de recursos.

> [!TIP]
> Use uma convenção de nomeação para indicar níveis de preços dentro do nome do recurso ou do grupo de recursos. Quando você recebe erros ao criar uma nova base de conhecimento ou adicionar novos documentos, o limite de nível de preços da Pesquisa Cognitiva é um problema comum.

## <a name="resource-purposes"></a>Fins de recursos

Cada recurso do Azure criado com o QnA Maker tem um propósito específico:

* Recurso do QnA Maker
* Recurso de Busca Cognitiva
* Serviço de Aplicativo
* Serviço de Plano de Aplicativo
* Serviço de insights de aplicativos


### <a name="cognitive-search-resource"></a>Recurso de Busca Cognitiva

O recurso [de Busca Cognitiva](../../../search/index.yml) é usado para:

* Armazene os conjuntos de QnA
* Fornecer o ranking inicial (ranker #1) dos conjuntos de QnA em tempo de execução

#### <a name="index-usage"></a>Utilização do índice

O recurso mantém um índice para atuar como índice de teste e os demais índices se correlacionam com uma base de conhecimento publicada cada.

Um recurso com preço para conter 15 índices, terá 14 bases de conhecimento publicadas, e um índice é usado para testar todas as bases de conhecimento. Este índice de teste é particionado pela base de conhecimento para que uma consulta usando o painel de teste interativo use o índice de teste, mas apenas retorne os resultados da partição específica associada à base de conhecimento específica.

#### <a name="language-usage"></a>Uso do idioma

A primeira base de conhecimento criada no recurso QnA Maker é usada para determinar o conjunto _de idiomas único_ para o recurso de Pesquisa Cognitiva e todos os seus índices. Você só pode ter _um conjunto de idiomas_ para um serviço QnA Maker.

### <a name="qna-maker-resource"></a>Recurso do QnA Maker

O recurso QnA Maker fornece acesso às APIs de autoria e publicação, bem como à camada de processamento de linguagem natural (NLP) baseada na segunda camada de classificação (ranker #2) dos conjuntos de QnA em tempo de execução.

O segundo ranking aplica filtros inteligentes que podem incluir metadados e solicitações de acompanhamento.

#### <a name="qna-maker-resource-configuration-settings"></a>Configurações de configuração de recursos do QnA Maker

Quando você cria uma nova base de conhecimento no [portal QnA Maker,](https://qnamaker.ai)a configuração **de Idioma** é a única configuração que é aplicada no nível de recursos. Você seleciona o idioma quando cria a primeira base de conhecimento para o recurso.

### <a name="app-service-and-app-service-plan"></a>Serviço de aplicativo e plano de serviço de aplicativo

O [serviço app](../../../app-service/index.yml) é usado pelo seu aplicativo cliente para acessar as bases de conhecimento publicadas através do ponto final de tempo de execução.

Para consultar a base de conhecimento publicada, todas as bases de conhecimento publicadas usam o mesmo ponto final da URL, mas especificam o **ID da base de conhecimento** dentro da rota.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[O Application Insights](../../../azure-monitor/app/app-insights-overview.md) é usado para coletar registros de bate-papo e telemetria. Revise as [consultas comuns do Kusto](../how-to/get-analytics-knowledge-base.md) para obter informações sobre o seu serviço.

## <a name="share-services-with-qna-maker"></a>Compartilhar serviços com o QnA Maker

O QnA Maker cria vários recursos do Azure. Para reduzir a gestão e se beneficiar do compartilhamento de custos, use a tabela a seguir para entender o que você pode ou não compartilhar:

|Serviço|Compartilhar|Motivo|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do Serviço de Aplicativo|✔|Espaço em disco fixo alocado para um plano de Serviço de Aplicativo. Se outros aplicativos que compartilham o mesmo plano de Serviço de Aplicativo usarem espaço em disco significativo, a instância do QnAMaker App Service encontrará problemas.|
|Serviço de Aplicativo|X|Não é possível por design|
|Application Insights|✔|Pode ser compartilhada|
|Serviço Search|✔|1. `testkb` é um nome reservado para o serviço QnAMaker; não pode ser usado por outros.<br>2. O mapa `synonym-map` de sinônimo sinuoso com o nome é reservado para o serviço QnAMaker.<br>3. O número de bases de conhecimento publicadas é limitado pelo nível de serviço de pesquisa. Se houver índices gratuitos disponíveis, outros serviços podem usá-los.|

### <a name="using-a-single-cognitive-search-service"></a>Usando um único serviço de busca cognitiva

Se você criar um serviço de QnA e suas dependências (como pesquisa) através do portal, um serviço de pesquisa será criado para você e vinculado ao serviço QnA Maker. Depois que esses recursos forem criados, você pode atualizar a configuração do Serviço de Aplicativo para usar um serviço de pesquisa anteriormente existente e remover o que você acabou de criar.

Aprenda [a configurar o](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker para usar um recurso de Serviço Cognitivo diferente do criado como parte do processo de criação de recursos do QnA Maker.

## <a name="management-service-region"></a>Região de serviço de gestão

O serviço de gerenciamento do QnA Maker é usado apenas para o portal QnA Maker e para o processamento inicial de dados. Este serviço está disponível apenas na região **oeste dos EUA.** Nenhum dado do cliente é armazenado neste serviço dos EUA ocidental.

## <a name="keys-in-qna-maker"></a>Chaves em QnA Maker

O serviço QnA Maker lida com dois tipos de chaves: **chaves de autoragem** e **chaves de ponto final de consulta** usadas com o tempo de execução hospedado no serviço do App.

Se você está procurando sua **chave de assinatura,** [a terminologia mudou](#subscription-keys).

Use essas chaves ao fazer solicitações ao serviço através de APIs.

![Gerenciamento de chaves](../media/qnamaker-how-to-key-management/key-management.png)

|Nome|Location|Finalidade|
|--|--|--|
|Chave de criação|[Portal Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|essas chaves são usadas para acessar as [APIs do serviço de gerenciamento do QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Essas APIs permitem que você edite as perguntas e respostas em sua base de conhecimento e publique sua base de conhecimento. Essas chaves são criadas quando você cria um novo serviço QnA Maker.<br><br>Encontre essas chaves no recurso **Serviços Cognitivos** na página **Chaves.**|
|Chave de ponto final de consulta|[Portal do QnA Maker](https://www.qnamaker.ai)|Essas chaves são usadas para consultar o ponto final da base de conhecimento publicada para obter uma resposta para uma pergunta do usuário. Você normalmente usa esse ponto final de consulta em seu bot de bate-papo ou no código do aplicativo cliente que se conecta ao serviço QnA Maker. Essas chaves são criadas quando você publica sua base de conhecimento QnA Maker.<br><br>Encontre essas chaves na página **de configurações** do Serviço. Encontre esta página no menu do usuário no canto superior direito da página no menu suspenso.|

### <a name="subscription-keys"></a>Chaves de assinatura

Os termos de autoria e de consulta são termos corretivos. O termo anterior era **a chave de assinatura**. Se você vir outra documentação referente a chaves de assinatura, elas são equivalentes a chaves de ponto final de autoria e consulta (usadas no tempo de execução).

Você deve saber qual é a chave de acesso, gerenciamento de base de conhecimento ou consulta de base de conhecimento, para saber qual chave você precisa encontrar.

## <a name="next-steps"></a>Próximas etapas

* Conheça a base de [conhecimento](knowledge-base.md) do QnA Maker
* Entenda um [ciclo de vida base de conhecimento](development-lifecycle-knowledge-base.md)
* Revisar [os limites](../limits.md) do serviço e da base de conhecimento

