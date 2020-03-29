---
title: Tipos de entidade - LUIS
titleSuffix: Azure Cognitive Services
description: 'As entidades extraem dados do enunciado. Os tipos de entidades dão-lhe extração previsível de dados. Existem dois tipos de entidades: aprendida por máquina e não-aprendida por máquinas. É importante saber com que tipo de entidade você está trabalhando em declarações.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221024"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entidades e seu propósito em LUIS

O objetivo principal das entidades é dar ao cliente a aplicação previsível extração de dados. Um propósito _opcional,_ secundário, é aumentar a previsão da intenção ou de outras entidades com descritores.

Existem dois tipos de entidades:

* máquina aprendida - a partir do contexto
* não-aprendizagem de máquina - para correspondências exatas de texto, correspondências de padrão ou detecção por entidades pré-construídas

Entidades aprendidas por máquina fornecem a mais ampla gama de opções de extração de dados. Entidades não-aprendidas por máquina funcionam por correspondência de texto e podem ser usadas independentemente ou como uma [restrição](#design-entities-for-decomposition) a uma entidade aprendida por máquina.

## <a name="entities-represent-data"></a>Entidades representam dados

Entidades são dados que você deseja retirar do enunciado, como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma declaração pode incluir muitas entidades ou nenhuma. Um aplicativo cliente _pode_ precisar dos dados para executar sua tarefa.

As entidades precisam ser rotuladas de forma consistente em todas as declarações de treinamento para cada intenção em um modelo.

 Você pode definir suas próprias entidades ou usar entidades pré-construídas para economizar tempo para conceitos comuns, como [datetimeV2,](luis-reference-prebuilt-datetimev2.md) [ordinal,](luis-reference-prebuilt-ordinal.md) [e-mail](luis-reference-prebuilt-email.md)e [número de telefone.](luis-reference-prebuilt-phonenumber.md)

|Enunciado|Entidade|Dados|
|--|--|--|
|Comprar três passagens para Nova York|Número predefinido<br>Location.Destination|3<br>Nova Iorque|
|Comprar uma passagem de Nova York para Londres em 5 de março|Location.Origin<br>Location.Destination<br>datetimeV2 predefinido|Nova Iorque<br>London<br>5 de março de 2018|

### <a name="entities-are-optional"></a>Entidades são opcionais

Embora as intenções sejam obrigatórias, as entidades são opcionais. Você não precisa criar entidades para cada conceito no aplicativo, mas apenas aquelas necessárias para que o aplicativo cliente execute uma ação.

Se suas declarações não tiverem dados que o aplicativo cliente exige, você não precisa adicionar entidades. À medida que seu aplicativo se desenvolve e uma nova necessidade de dados é identificada, você pode adicionar entidades apropriadas ao seu modelo LUIS mais tarde.

## <a name="entity-compared-to-intent"></a>Entidade comparada com a intenção

A entidade representa um conceito de dados dentro do enunciado que você quer extraído.

Uma enunciada pode incluir, opcionalmente, entidades. Em comparação, a previsão da intenção de um pronunciamento é _necessária_ e representa toda a expressão. LUIS requer expressões de exemplo estão contidas em uma intenção.

Considere as seguintes 4 expressões:

|Enunciado|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|ajuda|-|Nada para extrair.|
|Envie algo|sendSomething|-|Nada para extrair. O modelo não foi `something` treinado para extrair neste contexto, e também não há destinatário.|
|Envie um presente ao Bob.|sendSomething|`Bob`, `present`|O modelo foi treinado com a [entidade preconstruída personName,](luis-reference-prebuilt-person.md) que extraiu o nome `Bob`. Uma entidade aprendida por máquina `present`foi usada para extrair .|
|Mande uma caixa de chocolates ao Bob.|sendSomething|`Bob`, `box of chocolates`|Os dois dados importantes, `Bob` `box of chocolates`e os , foram extraídos por entidades.|

## <a name="design-entities-for-decomposition"></a>Projeto de entidades para decomposição

É um bom projeto de entidade para fazer da sua entidade de alto nível uma entidade aprendida por máquina. Isso permite alterações no design da entidade ao longo do tempo e o uso de **subcomponentes (entidades** infantis), opcionalmente com **restrições** e **descritores,** para decompor a entidade de nível superior nas partes necessárias pelo aplicativo cliente.

O projeto para decomposição permite que a LUIS retorne um profundo grau de resolução da entidade ao seu aplicativo cliente. Isso permite que seu aplicativo cliente se concentre nas regras do negócio e deixe a resolução de dados para luis.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Entidades aprendidas por máquina são coleções primárias de dados

[**As entidades aprendidas por**](tutorial-machine-learned-entity.md) máquina são a unidade de dados de alto nível. Subcomponentes são entidades infantis de entidades aprendidas por máquinas.

Uma entidade aprendida por máquina suscia com base no contexto aprendido através de declarações de treinamento. **As restrições** são regras opcionais aplicadas a uma entidade aprendida por máquina que restringe ainda mais o acionamento com base na definição exata de correspondência de texto de uma entidade não-aprendida por máquina, como uma [Lista](reference-entity-list.md) ou [Regex](reference-entity-regular-expression.md). Por exemplo, `size` uma entidade aprendida por `sizeList` máquina pode ter `size` uma restrição de uma entidade `sizeList` de lista que restrinja a entidade a acionar somente quando valores contidos dentro da entidade são encontrados.

[**Descritores**](luis-concept-feature.md) são recursos aplicados para aumentar a relevância das palavras ou frases para a previsão. Eles são chamados *de descritores* porque são usados para *descrever* uma intenção ou entidade. Os descritores descrevem traços distintos ou atributos de dados, como palavras ou frases importantes que LUIS observa e aprende.

Quando você cria um recurso de lista de frases em seu aplicativo LUIS, ele é ativado globalmente por padrão e se aplica uniformemente em todas as intenções e entidades. No entanto, se você aplicar a lista de frases como um descritor (recurso) de uma entidade (ou *modelo)* aprendida por máquina, então seu escopo se reduz para aplicar apenas a esse modelo e não é mais usado com todos os outros modelos. O uso de uma lista de frases como descritor para um modelo ajuda a decomposição, auxiliando na precisão do modelo a que é aplicado.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Escolha a entidade com base em como os dados devem ser extraídos e como devem ser representados após extraídos.

|Tipo de entidade|Finalidade|
|--|--|
|[**Aprendido por máquina**](tutorial-machine-learned-entity.md)|Entidades aprendidas por máquina aprendem com o contexto no enunciado. Agrupamento parental de entidades, independentemente do tipo de entidade. Isso torna significativa a variação da colocação em expressões por exemplo. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinônimos extraídos com **correspondência de texto exata**.|
|[**Padrão.qualquer**](reference-entity-pattern-any.md)|Entidade na qual é difícil determinar o final da entidade. |
|[**Predefinida**](luis-reference-prebuilt-entities.md)|Já treinado para extrair tipos específicos de dados, como URL ou e-mail. Algumas dessas entidades predefinidas são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Se não houver suporte à sua cultura ou entidade específica, contribua para o projeto.|
|[**Expressão regular**](reference-entity-regular-expression.md)|Usa expressão regular para **correspondência de texto exata**.|

## <a name="extracting-contextually-related-data"></a>Extraindo dados contextualmente relacionados

Uma declaração pode conter duas ou mais ocorrências de uma entidade onde o significado dos dados é baseado no contexto dentro do enunciado. Um exemplo é um enunciado para reservar um voo que tenha dois locais, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois exemplos `location` de uma entidade precisam ser extraídos. O cliente-aplicativo precisa saber o tipo de local para cada um para concluir a compra do ingresso.

Existem duas técnicas para extrair dados contextualmente relacionados:

 * A `location` entidade é uma entidade aprendida por máquina e `origin` `destination` usa duas entidades subcomponentes para capturar o e (preferido)
 * A `location` entidade usa `origin` dois **papéis** de e`destination`

Várias entidades podem existir em um pronunciamento e podem ser extraídas sem usar decomposição ou papéis se o contexto em que são usados não tem significado. Por exemplo, se o enunciado `I want to travel to Seattle, Cairo, and London.`incluir uma lista de locais, esta é uma lista onde cada item não tem um significado adicional.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Usando entidades subcomponentes de uma entidade aprendida por máquina para definir o contexto

Você pode usar uma [**entidade aprendida por máquina**](tutorial-machine-learned-entity.md) para extrair os dados que descrevem a ação de reservar um voo e, em seguida, decompor a entidade de nível superior nas partes separadas necessárias pelo aplicativo cliente.

Neste exemplo, `Book a flight from Seattle to Cairo`a entidade de nível `travelAction` superior poderia `flight from Seattle to Cairo`ser e rotulada para extrair . Em seguida, duas entidades subcomponentes são criadas, chamadas `origin` e `geographyV2` `destination`, ambas com uma restrição aplicada da entidade pré-construída. Nas declarações de treinamento, os `origin` e `destination` são rotulados adequadamente.

### <a name="using-entity-role-to-define-context"></a>Usando o papel da entidade para definir o contexto

Um Papel é um pseudônimo nomeado para uma entidade com base no contexto dentro do enunciado. Uma função pode ser usada com qualquer tipo de entidade predefinida ou personalizada e usada em padrões e enunciados de exemplo. Neste exemplo, `location` a entidade precisa `origin` `destination` de dois papéis e ambos precisam ser marcados nas enunciadas de exemplos.

Se luis `location` encontrar o mas não pode determinar o papel, a entidade de localização ainda é devolvida. O aplicativo cliente precisaria acompanhar uma pergunta para determinar qual tipo de localização o usuário quis dizer.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se você precisar de mais do que o número máximo de entidades

Se você precisar de mais do que o limite, contate o suporte. Para fazer isso, colete informações detalhadas sobre seu sistema, acesse o site do [LUIS](luis-reference-regions.md#luis-website) e selecione **Suporte**. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status de previsão da entidade

O portal LUIS mostra quando a entidade, em um exemplo de pronunciamento, tem uma previsão de entidade diferente da entidade que você selecionou. Essa pontuação diferente é baseada no modelo treinado atual.

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos sobre [declarações](luis-concept-utterance.md) boas.

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.

Veja [tutorial: Extraia dados estruturados do enunciado do usuário com entidades aprendidas por máquina em Compreensão de Linguagem (LUIS)](tutorial-machine-learned-entity.md) para aprender a extrair dados estruturados de uma enunciada usando a entidade aprendida pela máquina.
 
