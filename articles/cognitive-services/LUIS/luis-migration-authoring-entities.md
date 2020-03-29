---
title: Migrar para entidade aprendida por máquina V3
titleSuffix: Azure Cognitive Services
description: A autoria do V3 fornece um novo tipo de entidade, a entidade aprendida por máquina, juntamente com a capacidade de adicionar relações à entidade aprendida por máquina e outras entidades ou características do aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563829"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrar para entidade de autoria V3

A autoria do V3 fornece um novo tipo de entidade, a entidade aprendida por máquina, juntamente com a capacidade de adicionar relações à entidade aprendida por máquina e outras entidades ou características do aplicativo.

## <a name="entities-are-decomposable-in-v3"></a>Entidades são descomposáveis em V3

As entidades criadas com as APIs de autoria v3, utilizando as [APIs](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) ou com o [portal de visualização,](https://preview.luis.ai/)permitem que você construa um modelo de entidade em camadas com pais e filhos. O pai é conhecido como a **entidade aprendida pela máquina** e as crianças são conhecidas como **subcomponentes** da entidade aprendida pela máquina.

Cada subcomponente também é uma entidade aprendida por máquina, mas com as opções de configuração adicionadas de restrições e descritores.

* **As restrições** são regras exatas de correspondência de texto que garantem que uma entidade seja extraída quando corresponde a uma regra. A regra é definida por uma entidade exata de correspondência de texto, atualmente: uma [entidade pré-construída,](luis-reference-prebuilt-entities.md)uma [entidade de expressão regular](reference-entity-regular-expression.md)ou entidade de [lista.](reference-entity-list.md)
* **Descritores** são [características,](luis-concept-feature.md)como listas de frases ou entidades, que são usadas para indicar fortemente a entidade.

A autoria do V3 fornece um novo tipo de entidade, a entidade aprendida por máquina, juntamente com a capacidade de adicionar relações à entidade aprendida por máquina e outras entidades ou características do aplicativo.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Como essas novas relações se comparam à autoria v2

A autoria da V2 forneceu entidades hierárquicas e compostas, juntamente com funções e recursos para realizar essa mesma tarefa. Como as entidades, características e papéis não estavam explicitamente relacionados entre si, era difícil entender como LUIS implicava as relações durante a previsão.

Com o V3, a relação é explícita e projetada pelos autores do aplicativo. Isso permite que você, como autor do aplicativo,:

* Veja visualmente como LUIS está prevendo essas relações, nos exemplos de enunciados
* Teste para essas relações com o [painel de teste interativo](luis-interactive-test.md) ou no ponto final
* Use esses relacionamentos no aplicativo do cliente, através de um [objeto .json](reference-entity-machine-learned-entity.md) bem estruturado, chamado.

## <a name="planning"></a>Planejamento

Ao migrar, considere o seguinte em seu plano de migração:

* Faça backup do seu aplicativo LUIS e execute a migração em um aplicativo separado. Ter um aplicativo V2 e V3 disponível ao mesmo tempo permite validar as alterações necessárias e o impacto nos resultados da previsão.
* Capturar métricas atuais de sucesso de previsão
* Capture as informações atuais do painel como um instantâneo do status do aplicativo
* Revise as intenções, entidades, listas de frases, padrões e testes em lote
* Os seguintes elementos podem ser migrados **sem alterações:**
    * Intenções
    * Entidades
        * Entidade de expressão regular
        * Entidade de lista
    * Recursos
        * Lista de frases
* Os seguintes elementos precisam ser migrados **com alterações:**
    * Entidades
        * Entidade hierárquica
        * Entidade composta
    * Funções - as funções só podem ser aplicadas a uma entidade aprendida por máquina (pai). Funções não podem ser aplicadas a subcomponentes
    * Testes em lote e padrões que usam as entidades hierárquicas e compostas

Ao projetar seu plano de migração, deixe tempo para revisar as entidades aprendidas por máquina final, depois de todas as entidades hierárquicas e compostas terem sido migradas. Enquanto uma migração direta funcionará, depois que você fizer a alteração e revisar os resultados do teste em lote e a previsão JSON, o JSON mais unificado pode levá-lo a fazer alterações para que as informações finais entregues ao aplicativo do lado do cliente sejam organizadas de forma diferente. Isso é semelhante à refatoração de código e deve ser tratado com o mesmo processo de revisão que sua organização tem em vigor.

Se você não tiver testes em lote para o seu modelo V2 e migrar os testes em lote para o modelo V3 como parte da migração, você não poderá validar como a migração afetará os resultados da previsão de ponto final.

## <a name="migrating-from-v2-entities"></a>Migração de entidades V2

À medida que você começa a se mover para o modelo de autoria v3, você deve considerar como se mover para a entidade aprendida pela máquina, e seus subcomponentes, incluindo as restrições e descritores.

A tabela a seguir observa quais entidades precisam migrar de um V2 para um projeto de entidade V3.

|Tipo de entidade de autoria V2|Tipo de entidade de autoria V3|Exemplo|
|--|--|--|
|Entidade composta|Entidade aprendida por máquina|[Saiba Mais](#migrate-v2-composite-entity)|
|Entidade hierárquica|Papel da entidade aprendida por máquina|[Saiba Mais](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrar entidade Composta V2

Cada filho do composto V2 deve ser representado com um subcomponente da entidade aprendida por máquina V3. Se a criança composta for uma expressão pré-construída, regular ou uma entidade de lista, isso deve ser aplicado como uma **restrição** ao subcomponente que representa a criança.

Considerações ao planejar migrar uma entidade composta para uma entidade aprendida por máquina:
* Entidades infantis não podem ser usadas em padrões
* Entidades infantis não são mais compartilhadas
* Entidades infantis precisam ser rotuladas se costumavam ser não-aprendidas

### <a name="existing-descriptors"></a>Descritores existentes

Qualquer lista de frases usada para impulsionar palavras na entidade composta deve ser aplicada como descritor para a entidade aprendida pela máquina (pai), a entidade subcomponente (filho) ou a intenção (se a lista de frases se aplicar apenas a uma intenção). Planeje adicionar o descritor à entidade que ele deve impulsionar mais significativamente. Não adicione o descritor genericamente à entidade aprendida pela máquina (pai), se isso aumentar mais significativamente a previsão de um subcomponente (criança).

### <a name="new-descriptors"></a>Novos descritores

Na autoria da V3, adicione uma etapa de planejamento para avaliar as entidades como possíveis descritores para todas as entidades e intenções.

### <a name="example-entity"></a>Entidade exemplo

Essa entidade é apenas um exemplo. Sua própria migração de entidade pode exigir outras considerações.

Considere um composto V2 para `order` modificar uma pizza que usa:
* data pré-construídaV2 para o tempo de entrega
* lista de frases para impulsionar certas palavras, como pizza, torta, crosta, e cobertura
* listar entidade para detectar coberturas como cogumelos, azeitonas, pepperoni.

Um exemplo de expressão para esta entidade é:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

A tabela a seguir demonstra a migração:

|Modelos V2|Modelos V3|
|--|--|
|Pai - Entidade componente nomeada`Order`|Pai - Entidade aprendida por máquina nomeada`Order`|
|Criança - Data pré-construídaV2|* Migrar entidade pré-construída para novo aplicativo.<br>* Adicionar restrição ao pai para datação pré-construídaV2.|
|Criança - entidade de lista para coberturas|* Migrar a entidade da lista para o novo aplicativo.<br>* Em seguida, adicione uma restrição ao pai para a entidade da lista.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrar entidade hierárquica V2

Na autoria v2, uma entidade hierárquica foi fornecida antes dos papéis existentes em LUIS. Ambos serviram ao mesmo propósito de extrair entidades com base no uso do contexto. Se você tem entidades hierárquicas, você pode pensar neles como entidades simples com papéis.

Na autoria v3:
* Uma função pode ser aplicada na entidade aprendida por máquina (pai).
* Um papel não pode ser aplicado a nenhum subcomponente.

Essa entidade é apenas um exemplo. Sua própria migração de entidade pode exigir outras considerações.

Considere uma entidade hierárquica `order`V2 para modificar uma pizza:
* onde cada criança determina uma cobertura original ou a cobertura final

Um exemplo de expressão para esta entidade é:

`Change the topping from mushrooms to olives`

A tabela a seguir demonstra a migração:

|Modelos V2|Modelos V3|
|--|--|
|Pai - Entidade componente nomeada`Order`|Pai - Entidade aprendida por máquina nomeada`Order`|
|Criança - Entidade hierárquica com cobertura de pizza original e final|* Adicione `Order` papel a cada cobertura.|

## <a name="next-steps"></a>Próximas etapas

* [Recursos para desenvolvedores](developer-reference-resource.md)
