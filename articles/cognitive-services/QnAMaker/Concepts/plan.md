---
title: Planeje seu aplicativo-QnA Maker
description: Planejar seu aplicativo QnA Maker requer a compreensão de como o QnA Maker funciona e interage com outros serviços do Azure, bem como alguns conceitos da base de dados de conhecimento.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875700"
---
# <a name="plan-your-qna-maker-app"></a>Planejar seu aplicativo QnA Maker

Planejar seu aplicativo QnA Maker requer a compreensão de como o QnA Maker funciona e interage com outros serviços do Azure, bem como alguns conceitos da base de dados de conhecimento.

## <a name="azure-resources"></a>Recursos do Azure

Cada [recurso do Azure](azure-resources.md#resource-purposes) criado com QnA Maker tem uma finalidade específica. Como cada recurso tem sua própria finalidade, limites e [tipo de preço](azure-resources.md#pricing-tier-considerations), é importante entender o que esses recursos fazem como parte do seu processo de planejamento.

|Recurso|Finalidade|
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) recurso|Criação e previsão de consulta|
| [Pesquisa cognitiva](azure-resources.md#cognitive-search-resource) recurso|Armazenamento e pesquisa de dados|
| Recurso do [serviço de aplicativo e recurso do serviço de plano de aplicativo](azure-resources.md#app-service-and-app-service-plan)|Ponto de extremidade de previsão da consulta|
| [Application insights](azure-resources.md#application-insights) recurso|Telemetria de previsão de consulta|

### <a name="resource-planning"></a>Planejamento de recursos

Enquanto você está aprendendo a criação e a previsão de consultas, usar a camada gratuita, `F0` , de cada recurso funciona e fornecerá a experiência de previsão de criação e de consulta. Ao mudar para uma produção, ao vivo, ao cenário, reavalie sua seleção de recursos.

#### <a name="qna-maker-resource"></a>Recurso do QnA Maker

Um único recurso de QnA Maker pode hospedar mais de uma base de dados de conhecimento. O número de bases de dados de conhecimento é determinado pela quantidade de índices com suporte do tipo de preço Pesquisa Cognitiva. Saiba mais sobre a [relação de índices com bases de dados de conhecimento](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Tamanho e taxa de transferência da base de dados de conhecimento

Quando você planeja criar um aplicativo real, planeje seus recursos para o tamanho da sua base de dados de conhecimento e as solicitações de previsão de consulta que você espera.

Um tamanho da base de dados de conhecimento é controlado pelo:
* Limites de tipo de preço de [pesquisa cognitiva recurso](../../../search/search-limits-quotas-capacity.md)
* [Limites de QnA Maker](../limits.md)

A solicitação de previsão de consulta da base de dados de conhecimento é controlada pelo plano do aplicativo Web e pelo aplicativo Web. Consulte [as configurações recomendadas](azure-resources.md#recommended-settings) para planejar seu tipo de preço.

### <a name="resource-sharing"></a>Compartilhamento de recursos

Se você já tiver alguns desses recursos em uso, poderá considerar o compartilhamento de recursos. Embora alguns recursos [possam ser compartilhados](azure-resources.md#share-services-with-qna-maker), esse é um cenário avançado.

Todas as bases de dados de conhecimento criadas no mesmo recurso QnA Maker compartilham o mesmo ponto de extremidade de previsão de consulta de **teste** .

### <a name="understanding-impact-of-resource-selection"></a>Entendendo o impacto da seleção de recursos

A seleção de recursos apropriada significa que sua base de dados de conhecimento responde a previsões de consulta com êxito.

Se sua base de dados de conhecimento não estiver funcionando corretamente, normalmente o problema é o gerenciamento de recursos inadequado.

A seleção de recursos inadequados requer investigação para determinar qual [recurso precisa ser alterado](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

Uma base de dados de conhecimento está diretamente vinculada a QnA Maker recurso e mantém os pares de perguntas e respostas (QnA) usados para responder às solicitações de previsão de consulta.

### <a name="language-considerations"></a>Considerações sobre o idioma

A primeira base de dados de conhecimento criada em seu QnA Maker recurso define o idioma do recurso. Você só pode ter um idioma para um recurso QnA Maker.

Estruture seus recursos de QnA Maker por idioma ou use o [Tradutor](../../translator/translator-info-overview.md) para alterar uma consulta de outro idioma para o idioma da base de dados de conhecimento antes de enviar a consulta para o ponto de extremidade de previsão de consulta.

### <a name="ingesting-data-sources"></a>Ingestão de fontes de dados

Fontes de [dados](knowledge-base.md)ingeridas, usadas para criar uma base de conhecimento, podem ser uma das seguintes:

* URL pública
* URL privada do SharePoint
* Arquivo

O processo de ingestão converte os [tipos de conteúdo com suporte](content-types.md) para redução. Toda edição adicional da *resposta* é feita com a redução. Depois de criar sua base de dados de conhecimento, você pode editar [pares de QnA](question-answer-set.md) no portal de QnA Maker com a [criação de Rich Text](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Considerações de formato de dados

Como o formato final de um par de QnA é a redução, é importante entender o [suporte de redução](../reference-markdown-format.md) .

As imagens vinculadas devem estar disponíveis de uma URL pública para serem exibidas no painel de teste do portal de QnA Maker, bem como qualquer aplicativo cliente, porque QnA Maker não fornece autenticação para conteúdo, incluindo imagens.

### <a name="bot-personality"></a>Personalidade do bot

Adicione uma personalidade de bot à sua base de dados de conhecimento com o [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Essa personalidade é fornecida com as respostas fornecidas em um determinado Tom de conversação, como *profissional* e *amigável*. Esse Chit é fornecido como um conjunto de conversação, que você tem controle total para adicionar, editar e remover.

Uma personalidade de bot é recomendada se o bot se conectar à sua base de dados de conhecimento. Se você estiver se conectando a vários serviços, uma das quais é sua base de dados de conhecimento, você pode optar por continuar usando o Chit em sua base de dados de conhecimento, mas você deve examinar como o serviço de bot interage para saber se esse é o design de arquitetura correto para seu uso.

### <a name="conversation-flow-with-a-knowledge-base"></a>Fluxo de conversa com uma base de dados de conhecimento

O fluxo de conversa geralmente começa com uma saudação de um usuário, como `Hi` ou `Hello` . Sua base de dados de conhecimento pode responder com uma resposta geral, como `Hi, how can I help you` , e também pode fornecer uma seleção de avisos de acompanhamento para continuar a conversa.

Você deve projetar seu fluxo de conversação com um loop em mente para que um usuário saiba como usar o bot e não seja abandonado pelo bot na conversa. Os [prompts de acompanhamento](../how-to/multiturn-conversation.md) fornecem vinculação entre pares de QnA, que permitem o fluxo de conversação.

### <a name="authoring-with-collaborators"></a>Criando com colaboradores

Os colaboradores podem ser outros desenvolvedores que compartilham a pilha de desenvolvimento completa do aplicativo da base de dados de conhecimento ou podem estar limitados a apenas criar a base de dados de conhecimento.

A criação da base de dados de conhecimento dá suporte a várias [permissões de acesso baseadas em função](../how-to/collaborate-knowledge-base.md) que você aplica no portal do Azure para limitar o escopo das habilidades de um colaborador.

## <a name="integration-with-client-applications"></a>Integração com aplicativos cliente

A integração com [aplicativos cliente](integration-with-other-applications.md) significa enviar uma consulta para o ponto de extremidade de tempo de execução de previsão. Uma consulta é enviada para sua base de dados de conhecimento específica com um SDK ou uma solicitação baseada em REST para o ponto de extremidade do aplicativo Web do QnA Maker.

Para autenticar uma solicitação de cliente corretamente, o aplicativo cliente deve enviar as credenciais corretas e a ID da base de dados de conhecimento. Se você estiver usando um serviço de bot do Azure, defina a configuração como parte da configuração do bot no portal do Azure.

### <a name="conversation-flow-in-a-client-application"></a>Fluxo de conversa em um aplicativo cliente

O fluxo de conversa em um [aplicativo cliente](integration-with-other-applications.md), como um bot do Azure, pode exigir funcionalidade antes e depois de interagir com a base de dados de conhecimento.

Se o seu aplicativo cliente oferecer suporte ao fluxo de conversa, fornecendo meios alternativos para lidar com prompts de acompanhamento ou Chit-Chit, projete esses primeiros e certifique-se de que a consulta usando no aplicativo cliente seja tratada corretamente, por outro serviço ou enviada à sua base de dados de conhecimento.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>Expedição entre QnA Maker e Reconhecimento vocal (LUIS)

Um aplicativo cliente pode fornecer vários recursos, apenas um dos quais é respondido por uma base de dados de conhecimento. Outros recursos ainda precisariam entender o texto de conversação e extrair o significado dele.

Uma arquitetura de aplicativo cliente comum é usar o QnA Maker e o [reconhecimento vocal (Luis)](../../LUIS/what-is-luis.md) juntos. O LUIS fornece a classificação de texto e extração para qualquer consulta, incluindo para outros serviços, enquanto QnA Maker fornece respostas de sua base de dados de conhecimento.

Em uma [arquitetura compartilhada](../choose-natural-language-processing-service.md), a distribuição entre os dois serviços é feita com a ferramenta de [expedição](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) do bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aprendizado ativo de um aplicativo cliente

O QnA Maker usa o _aprendizado ativo_ para melhorar sua base de dados de conhecimento sugerindo perguntas alternativas para uma resposta. O aplicativo cliente é responsável por uma parte deste [aprendizado ativo](active-learning-suggestions.md). O aplicativo cliente, por meio de prompts de conversação, pode determinar se a resposta retornada da base de dados de conhecimento não era a resposta que o usuário estava procurando e determinar a melhor resposta. O aplicativo cliente precisa [enviar essas informações de volta para a base de dados de conhecimento](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) para melhorar a qualidade da previsão.

### <a name="providing-a-default-answer"></a>Fornecendo uma resposta padrão

Se sua base de dados de conhecimento não encontrar uma resposta, ela retornará a _resposta padrão_. Essa resposta é configurável no portal de QnA Maker, na página **configurações** ou nas [APIs](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Essa resposta padrão é diferente da resposta padrão do bot do Azure. A resposta padrão do bot do Azure é configurada no portal do Azure, para o bot, como parte das definições de configuração e é retornada quando o limite de pontuação não é atendido.

## <a name="prediction"></a>Previsão

A previsão é a resposta da sua base de dados de conhecimento e inclui mais informações do que apenas a resposta. Para obter uma resposta de previsão de consulta, use a [API GeneateAnswer](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Flutuações de Pontuação de previsão

Uma pontuação pode mudar com base em vários fatores:

* Número de respostas solicitadas em resposta a [GenerateAnswer](query-knowledge-base.md) com a `top` Propriedade
* Variedade de perguntas alternativas disponíveis
* Filtragem de metadados
* Consulta enviada para `test` ou `production` base de dados de conhecimento

Há uma [classificação de resposta de duas fases](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Pesquisa Cognitiva-primeira classificação-para que uma resposta seja retornada do Pesquisa Cognitiva, o número de _respostas permitidas_ precisa ser alto o suficiente para que as melhores respostas sejam retornadas por pesquisa cognitiva para que possam passar para o classificador de QnA Maker
* Classificação de QnA Maker segundo – aplique personalização e Machine Learning para determinar a melhor resposta.

### <a name="service-updates"></a>Atualizações de serviço

As atualizações de serviço são gerenciadas automaticamente aplicando as [atualizações de tempo de execução mais recentes](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

### <a name="scaling-throughput-and-resiliency"></a>Dimensionamento, produtividade e resiliência

O dimensionamento, a taxa de transferência e a resiliência são determinados pelos [recursos do Azure](../how-to/set-up-qnamaker-service-azure.md), seus tipos de preço e qualquer arquitetura ao redor, como o [Gerenciador de tráfego](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Análise com Application Insights

Todas as consultas à sua base de dados de conhecimento são armazenadas em Application Insights. Use nossas [principais consultas](../how-to/get-analytics-knowledge-base.md) para entender suas métricas.

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

O [ciclo de vida de desenvolvimento](development-lifecycle-knowledge-base.md) de uma base de dados de conhecimento é contínuo: editar, testar e publicar sua base de dados de conhecimento.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Desenvolvimento da base de dados de conhecimento de pares de QnA Maker

Seus [pares de QnA](question-answer-set.md) devem ser projetados e desenvolvidos com base no uso do aplicativo cliente.

Cada par pode conter:
* Metadados-filtráveis ao consultar. Isso permite marcar seus pares de QnA com informações adicionais sobre a origem, o conteúdo, o formato e a finalidade de seus dados.
* Prompts de acompanhamento-determine um caminho por meio de sua base de dados de conhecimento para que o usuário chegue à resposta correta.
* Perguntas alternativas-perguntas alternativas são importantes para permitir que a pesquisa corresponda à sua resposta de uma variedade de formas da pergunta. As [sugestões de aprendizado ativo são transformadas](active-learning-suggestions.md) em perguntas alternativas.

### <a name="devops-development"></a>Desenvolvimento de DevOps

Desenvolver uma base de dados de conhecimento para inserir em um pipeline do DevOps exige que a base de dados de conhecimento seja isolada durante o [teste do lote](../quickstarts/batch-testing.md).

Uma base de dados de conhecimento compartilha o índice de Pesquisa Cognitiva com todas as outras bases de dados de conhecimento no recurso de QnA Maker. Embora a base de dados de conhecimento seja isolada por partição, o compartilhamento do índice pode causar uma diferença na pontuação quando comparado à base de dados de conhecimento publicada.

Para ter a _mesma pontuação_ no `test` e nas bases de `production` dados de conhecimento, isole um recurso QnA Maker a uma única base de dados de conhecimento. Nessa arquitetura, o recurso só precisa residir até o comprimento do teste em lote isolado.

## <a name="next-step"></a>Próxima etapa

* [Recursos do Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Pares de pergunta e resposta](question-answer-set.md)