---
title: Tipos de entidade-LUIS
description: Uma entidade extrai dados de um usuário expressão no tempo de execução de previsão. Uma finalidade _opcional_e secundária é impulsionar a previsão da intenção ou de outras entidades usando a entidade como um recurso.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585633"
---
# <a name="extract-data-with-entities"></a>Extrair dados com entidades

Uma entidade extrai dados de um usuário expressão no tempo de execução de previsão. Uma finalidade _opcional_e secundária é impulsionar a previsão da intenção ou de outras entidades usando a entidade como um recurso.

Há vários tipos de entidades:

* [Entidade com aprendizado de máquina](reference-entity-machine-learned-entity.md)
* Não Machine-aprendi usado como um [recurso](luis-concept-feature.md) necessário – para correspondências exatas de texto, correspondências de padrões ou detecção por entidades predefinidas
* [Padrão. any](#patternany-entity) -para extrair texto de forma livre, como títulos de livros, de um [padrão](reference-entity-pattern-any.md)

As entidades aprendidas por máquina fornecem a mais ampla variedade de opções de extração de dados. As entidades não aprendidas por computador funcionam por correspondência de texto e são usadas como um [recurso necessário](#design-entities-for-decomposition) para uma entidade ou intenção aprendida por computador.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que você deseja extrair do expressão, como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma declaração pode incluir muitas entidades ou nenhuma. Um aplicativo cliente _pode_ precisar dos dados para executar sua tarefa.

As entidades precisam ser rotuladas consistentemente em todos os declarações de treinamento para cada tentativa em um modelo.

 Você pode definir suas próprias entidades ou usar entidades predefinidas para economizar tempo para conceitos comuns, como [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md)e [número de telefone](luis-reference-prebuilt-phonenumber.md).

|Enunciado|Entidade|Dados|
|--|--|--|
|Comprar três passagens para Nova York|Número predefinido<br>Destino|3<br>Nova Iorque|


### <a name="entities-are-optional-but-recommended"></a>As entidades são opcionais, mas recomendadas

Embora as [intenções](luis-concept-intent.md) sejam obrigatórias, as entidades são opcionais. Você não precisa criar entidades para todos os conceitos em seu aplicativo, mas apenas para aqueles em que o aplicativo cliente precisa dos dados ou a entidade atua como uma dica ou sinal para outra entidade ou intenção.

Conforme o aplicativo é reparado e uma nova necessidade de dados é identificada, você pode adicionar entidades apropriadas ao modelo LUIS mais tarde.

## <a name="entity-compared-to-intent"></a>Entidade comparada com a intenção

A entidade representa um conceito de dados _dentro do expressão_. Uma intenção classifica _todo o expressão_.

Considere os quatro declarações a seguir:

|Enunciado|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|ajuda|-|Nada para extrair.|
|Enviar algo|sendSomething|-|Nada para extrair. O modelo não tem um recurso necessário para extrair neste `something` contexto, e não há nenhum destinatário declarado.|
|Enviar Bob a presente|sendSomething|`Bob`, `present`|O modelo extrai `Bob` adicionando um recurso necessário da entidade predefinida `personName` . Uma entidade aprendida por máquina foi usada para extrair `present` .|
|Enviar Bob a caixa de chocolates|sendSomething|`Bob`, `box of chocolates`|As duas partes importantes dos dados `Bob` e as foram `box of chocolates` extraídas por entidades aprendidas por computador.|

## <a name="design-entities-for-decomposition"></a>Projetar entidades para decomposição

As entidades aprendidas por computador permitem que você projete seu esquema de aplicativo para decomposição, dividindo um conceito grande em subentidades.

A criação de decomposição permite que o LUIS retorne um nível profundo de resolução de entidade para seu aplicativo cliente. Isso permite que o aplicativo cliente se concentre em regras de negócio e deixe a resolução de dados em LUIS.

Um gatilho de entidade aprendida por máquina com base no contexto aprendido por meio de exemplo declarações.

As [**entidades aprendidas por computador**](tutorial-machine-learned-entity.md) são os extratores de nível superior. As subentidades são entidades filhas de entidades aprendidas por máquina.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Uma subentidade para um pai deve ser uma entidade aprendida por computador. A subentidade pode usar uma entidade não aprendida por máquina como um [recurso](luis-concept-feature.md).

Escolha a entidade com base em como os dados devem ser extraídos e como devem ser representados após extraídos.

|Tipo de entidade|Finalidade|
|--|--|
|[**Aprendido com o computador**](tutorial-machine-learned-entity.md)|Extraia dados aninhados e complexos aprendidos de exemplos rotulados. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinônimos extraídos com **correspondência exata de texto**.|
|[**Padrão. any**](#patternany-entity)|A entidade em que encontrar o final da entidade é difícil de determinar porque a entidade é de forma livre. Disponível somente em [padrões](luis-concept-patterns.md).|
|[**Predefinida**](luis-reference-prebuilt-entities.md)|Já treinado para extrair um tipo específico de dados, como URL ou email. Algumas dessas entidades predefinidas são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Se não houver suporte à sua cultura ou entidade específica, contribua para o projeto.|
|[**Expressão regular**](reference-entity-regular-expression.md)|Usa uma expressão regular para **correspondência exata de texto**.|

## <a name="extracting-contextually-related-data"></a>Extraindo dados relacionados contextualmente

Um expressão pode conter duas ou mais ocorrências de uma entidade em que o significado dos dados é baseado no contexto dentro do expressão. Um exemplo é um expressão para reservar um vôo que tem duas localizações geográficas, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois locais precisam ser extraídos de forma que o aplicativo cliente saiba o tipo de cada local para concluir a compra do tíquete.

Para extrair a origem e o destino, crie duas subentidades como parte da entidade aprendida pela máquina do pedido de tíquete. Para cada uma das subentidades, crie um recurso necessário que use geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Usando os recursos necessários para restringir entidades

Saiba mais sobre os [recursos necessários](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entidade pattern.any

Um padrão. Any está disponível apenas em um [padrão](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Excedendo os limites de aplicativo para entidades

Se você precisar de mais do que o [limite](luis-limits.md#model-limits), entre em contato com o suporte. Para fazer isso, colete informações detalhadas sobre seu sistema, acesse o site do [LUIS](luis-reference-regions.md#luis-website) e selecione **Suporte**. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status de previsão de entidade

O portal do LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade selecionada para um exemplo de expressão. Essa pontuação diferente é baseada no modelo treinado atual. Use essas informações para resolver erros de treinamento usando um ou mais dos seguintes itens:
* Criar um [recurso](luis-concept-feature.md) para a entidade para ajudar a identificar o conceito da entidade
* Adicionar mais [exemplos de declarações](luis-concept-utterance.md) e rótulo com a entidade
* [Examine as sugestões de aprendizado ativo](luis-concept-review-endpoint-utterances.md) para qualquer declarações recebido no ponto de extremidade de previsão que possa ajudar a identificar o conceito da entidade.

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos sobre [declarações](luis-concept-utterance.md) boas.

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.

Consulte [tutorial: extrair dados estruturados do usuário expressão com entidades aprendidas por máquina no reconhecimento vocal (Luis)](tutorial-machine-learned-entity.md) para saber como extrair dados estruturados de um expressão usando a entidade aprendida por máquina.

