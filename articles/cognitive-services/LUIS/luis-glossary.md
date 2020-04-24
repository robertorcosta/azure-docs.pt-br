---
title: Glossário-LUIS
titleSuffix: Azure Cognitive Services
description: O glossário explica os termos que você pode encontrar à medida que trabalha com o Serviço de API do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: f764b0d42e08e68b45d49a5aae8542f05707ccde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099352"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de noções básicas de linguagem de conceitos e vocabulário comuns
O glossário do LUIS (Reconhecimento vocal) explica os termos que você pode encontrar à medida que trabalha com o Serviço de API do LUIS.

## <a name="active-version"></a><a name="active-version"></a>Versão ativa

A versão ativa do LUIS é a versão que recebe alterações no modelo. No portal do [Luis](luis-reference-regions.md) , se você quiser fazer alterações em uma versão que não seja a versão ativa, primeiro você precisará definir essa versão como ativa.

## <a name="authoring"></a><a name="authoring"></a>Criação

A criação é a capacidade de criar, gerenciar e implantar um [aplicativo Luis](#luis-app), usando o portal do [Luis](luis-reference-regions.md) ou as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Chave de criação

Anteriormente denominada chave "programática". Usada para criar o aplicativo. Não usada para consultas de ponto de extremidade no nível de produção. Para obter mais informações, confira [Limites da chave](luis-limits.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Arquivo JSON de teste em lote

O teste em lotes é a capacidade de validar um modelo de aplicativo LUIS atual com um conjunto de teste consistente e conhecido de declarações de usuário. O teste em lotes é definido em um [arquivo formatado em JSON](luis-concept-batch-test.md#batch-file-format).

Consulte também:
* [Conceitos](luis-concept-batch-test.md)
* [Como fazer](luis-how-to-batch-test.md)
* [Tutorial](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Colaborador

Um colaborador/colaborador, não é o [proprietário](#owner) do aplicativo, mas tem as mesmas permissões para adicionar, editar e excluir as intenções, entidades, declarações.

## <a name="contributor"></a><a name="contributor"></a>Colaborador

Um colaborador é a mesma coisa que um [colaborador.](#collaborator)

## <a name="descriptor"></a><a name="descriptor"></a>Descritor

Um descritor é um [recurso](#features) aplicado a um modelo no tempo de treinamento, incluindo [listas de frases](#phrase-list) e [entidades](#entity).

## <a name="domain"></a><a name="domain"></a>Controlador

No contexto do LUIS, um **domínio** é uma área de conhecimento. Seu domínio é específico para a área de conhecimento do aplicativo. Isso pode ser uma área geral, como o aplicativo de agente de viagem. Um aplicativo de agente de viagem também pode ser específico apenas para as áreas de informações da sua empresa, como os locais geográficos, idiomas e serviços específicos.

## <a name="endpoint"></a><a name="endpoint"></a>Extremidade

A URL do [ponto de extremidade LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) é onde você envia consultas LUIS após o [aplicativo LUIS](#luis-app) ser criado e publicado. A URL de ponto de extremidade contém a região do aplicativo publicado, bem como a ID do aplicativo. Você pode encontrar o ponto de extremidade na página **[Chaves e pontos de extremidade](luis-how-to-azure-subscription.md)** do aplicativo ou obter a URL do ponto de extremidade na API [Obter Informações do Aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37).

## <a name="entity"></a><a name="entity"></a>Entidade

[Entidades](luis-concept-entity-types.md) são importantes palavras em [declarações](luis-concept-utterance.md) que descrevem informações relevantes para a [intenção](luis-concept-intent.md), e algumas vezes são essenciais para ela. Uma entidade é essencialmente um tipo de dados no LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>Medida F

No [teste de lote](luis-interactive-test.md#batch-testing), uma medida da precisão do teste.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falso negativo (FN)

No [teste de lote](luis-interactive-test.md#batch-testing), os pontos de dados representam enunciados em que o aplicativo previu incorretamente a ausência de entidade/intenção de destino.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falso positivo (FP)

No [teste de lote](luis-interactive-test.md#batch-testing), os pontos de dados representam enunciados em que o aplicativo previu incorretamente a existência de entidade/intenção de destino.

## <a name="features"></a><a name="features"></a>Recursos

No aprendizado de máquina, um [recurso](luis-concept-feature.md) é um traço ou atributo específico de dados que seu sistema observa.

## <a name="intent"></a><a name="intent"></a>Intencional

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa na entrada de um usuário, como reservar um voo, pagar uma fatura ou localizar um artigo de notícias. No LUIS, a previsão da intenção é baseada em toda a declaração. As entidades, por comparação, são partes de uma declaração.

## <a name="labeling"></a><a name="labeling"></a>Rotulação

Rotular, ou marcar, é o processo de associar uma palavra ou frase no [expressão](#utterance) de uma intenção a uma [entidade](#entity) (DataType).

## <a name="luis-app"></a><a name="luis-app"></a>Aplicativo LUIS

Um aplicativo LUIS é uma coleção de modelos de linguagem para processamento de linguagem natural, incluindo [intenções](#intent), [entidades](#entity)e rotulada [declarações](#utterance).

## <a name="owner"></a><a name="owner"></a>Proprietário

Cada aplicativo tem um proprietário, que é a pessoa que criou o aplicativo. O proprietário pode adicionar [colaboradores](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Padrões
O recurso Padrão anterior é substituído por [Padrões](luis-concept-patterns.md). Use padrões para melhorar a precisão da previsão fornecendo menos exemplos de treinamento.

## <a name="phrase-list"></a><a name="phrase-list"></a>Lista de frases

Uma [lista de frases](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratada da mesma forma (por exemplo, nomes de cidades ou produtos). Uma lista intercambiável é tratada como sinônimos.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Domínio predefinido

Um [domínio predefinido](luis-how-to-use-prebuilt-domains.md) é um aplicativo LUIS configurado para um domínio específico, como automação residencial (HomeAutomation) ou reservas de restaurante (RestaurantReservation). As intenções, declarações e entidades são configuradas para esse domínio.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Entidade predefinida

Uma [entidade predefinida](luis-prebuilt-entities.md) é um LUIS de entidade que fornece tipos comuns de informações, como número, URL e email. Opte por adicionar uma entidade predefinida ao seu aplicativo.

## <a name="precision"></a><a name="precision"></a>Preciso
No [teste de lote](luis-interactive-test.md#batch-testing), a precisão (também chamada de valor preditivo positivo) é a fração de enunciados relevantes entre os enunciados recuperados.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Chave programática

Renomeada para [chave de criação](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publicar

Publicar significa tornar uma versão ativa do LUIS disponível no [ponto de extremidade](#endpoint) de preparo ou de produção.

## <a name="quota"></a><a name="quota"></a>Cota

A cota LUIS é a limitação do [nível de assinatura do Azure](https://aka.ms/luis-price-tier). A cota LUIS pode ser limitada por solicitações por segundo (HTTP Status 429) e solicitações totais em um mês (HTTP Status 403).

## <a name="recall"></a><a name="recall"></a>Recuperação
No [teste de lote](luis-interactive-test.md#batch-testing), recall (também conhecido como sensibilidade) é a capacidade de generalização do LUIS.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Dicionário semântico
Um dicionário semântico é fornecido na página Entidade de lista e na página Lista de frases. O dicionário semântico fornece sugestões de palavras com base no escopo atual.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Análise de Sentimento
A Análise de sentimento fornece valores positivos ou negativos das declarações fornecidas pela [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Espriming de fala

A preparação da fala permite que seu serviço de fala seja preparado com seu modelo LUIS.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Correção ortográfica

Habilite verificação ortográfica do Bing para corrigir as palavras com ortografia incorreta nos enunciados antes da previsão.

## <a name="starter-key"></a><a name="starter-key"></a>Chave de início

Uma chave gratuita a ser usada ao iniciar pela primeira vez usando LUIS.

## <a name="structure"></a><a name="structure"></a>Estrutura

Adicione estrutura a uma entidade aprendida por máquina para fornecer subcomponentes com descritores (recursos) e restrições (entidades regulares ou de lista).

## <a name="subscription-key"></a><a name="subscription-key"></a>Chave de assinatura

A chave de assinatura é a chave de **ponto de extremidade de previsão** associada ao serviço Luis [que você criou no Azure](luis-how-to-azure-subscription.md). Essa chave não é a [chave de criação](#programmatic-key). Se você tiver uma chave de ponto de extremidade, ela deverá ser usada para qualquer solicitação de ponto de extremidade, em vez da chave de criação. É possível ver a chave de ponto de extremidade atual dentro da URL do ponto de extremidade na parte inferior da [**página Chaves e pontos de extremidade**](luis-how-to-azure-subscription.md) no site do [LUIS](luis-reference-regions.md). É o valor do par nome/valor de **subscription-key**.

## <a name="test"></a><a name="test"></a>Teste

[Testar](luis-interactive-test.md#test-your-app) um aplicativo LUIS significa passar uma declaração para o LUIS e exibir os resultados JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Diferença de fuso horário

O ponto de extremidade inclui timezoneOffset. Este é o número, em minutos, que você deseja adicionar ou remover da entidade predefinida datetimeV2. Por exemplo, se a declaração for "que horas são agora?", o datetimeV2 retornado será a hora atual da solicitação do cliente. Se a solicitação do cliente for proveniente de um bot ou de outro aplicativo que não for o mesmo que o usuário do seu bot, você deverá passar o deslocamento entre o bot e o usuário.

Confira [Alterar o fuso horário da entidade datetimeV2 predefinida](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
Um token é a menor unidade pode ser rotulada em uma entidade. A geração de tokens é baseada na [cultura](luis-language-support.md#tokenization) do aplicativo.

## <a name="train"></a><a name="train"></a>Treinar

Treinamento é o processo de ensinar ao LUIS as alterações na versão ativa desde o último treinamento.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Negativo verdadeiro (TN)

No [teste de lote](luis-interactive-test.md#batch-testing), os pontos de dados representam enunciados em que o aplicativo previu corretamente a ausência de entidade/intenção de destino.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Positivo verdadeiro (TP)

No [teste de lote](luis-interactive-test.md#batch-testing), os pontos de dados representam enunciados em que o aplicativo previu corretamente a existência de entidade/intenção de destino.

## <a name="utterance"></a><a name="utterance"></a>Enunciado

Uma declaração é a frase de linguagem natural como "reservar 2 passagens para Seattle na próxima terça-feira". Os exemplos de declaração são adicionados à intenção.

## <a name="version"></a><a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) do LUIS é um modelo de dados específico associado à ID do aplicativo LUIS e ao ponto de extremidade publicado. Todo aplicativo LUIS tem pelo menos uma versão.
