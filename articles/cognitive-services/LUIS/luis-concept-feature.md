---
title: Recursos-LUIS
description: Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar.
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: c4f19ceed2e48f3f6ec2ed0958bccb7a85cff44f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742706"
---
# <a name="machine-learning-ml-features"></a>Recursos de aprendizado de máquina (ML)

No Machine Learning, um **recurso**   é uma característica ou atributo diferenciado dos dados que seu sistema observa.

Os recursos de aprendizado de máquina fornecem indicações LUISs importantes para onde procurar coisas que irão distinguir um conceito. Elas são dicas que o LUIS pode usar, mas não as regras difíceis.  Essas dicas são usadas em conjunto com os rótulos para localizar os dados.

 O LUIS dá suporte a ambas as listas de frases e ao uso de outras entidades como recursos:
* Recurso de lista de frases
* Modelo (intenção ou entidade) como um recurso

Os recursos devem ser considerados uma parte necessária do seu design de esquema.

## <a name="a-phrase-list-for-a-particular-concept"></a>Uma lista de frases para um determinado conceito

Uma lista de frases é uma lista de palavras ou frases que encapsula um determinado conceito.

Ao adicionar uma lista de frases, você pode definir o recurso como:
* **[Global](#global-features)**. Um recurso global se aplica a todo o aplicativo.

### <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Quando você precisar que seu aplicativo LUIS seja capaz de generalizar e identificar novos itens para o conceito, use uma lista de frases. Listas de frase são como um vocabulário específico ao domínio que ajudam a melhorar a qualidade da compreensão de intenções e entidades.

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

Com uma lista de frases, LUIS considera contexto e generalizações para identificar itens semelhantes a, mas não uma correspondência de texto exata.

Etapas para usar uma lista de frases:
* Comece com uma entidade de aprendizado de máquina
    * Adicionar enunciados de exemplo
    * Rótulo com uma entidade de aprendizado de máquina
* Adicionar uma lista de frases
    * Adicionar palavras com significado semelhante-não **adicione todas** as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez e, em seguida, retreine e publique.
    * Revisar e adicionar palavras sugeridas

### <a name="a-typical-scenario-for-a-phrase-list"></a>Um cenário típico para uma lista de frases

Um cenário típico para uma lista de frases é impulsionar palavras relacionadas a uma ideia específica.

Um exemplo de palavras que podem precisar de uma lista de frases para impulsionar seus significados são termos médicos. Os termos podem ter significado físico, químico, Therapeutic ou abstrato específico. O LUIS não saberá que os termos são importantes para o domínio do assunto sem uma lista de frases.

Se você quiser extrair os termos médicos:
* Primeiro, crie o exemplo declarações e Rotule os termos médicos dentro desses declarações.
* Em seguida, crie uma lista de frases com exemplos dos termos dentro do domínio do assunto. Essa lista de frases deve incluir o termo real rotulado e outros termos que descrevam o mesmo conceito.
* Adicione a lista de frases à entidade ou subentidade que extrai o conceito usado na lista de frases. O cenário mais comum é um componente (filho) de uma entidade de aprendizado de máquina. Se a lista de frases deve ser aplicada em todas as intenções ou entidades, marque a lista de frases como uma lista de frases globais. O `enabledForAllModels` sinalizador controla esse escopo de modelo na API.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Um modelo como um recurso ajuda outro modelo

Você pode adicionar um modelo (intenção ou entidade) como um recurso a outro modelo (intenção ou entidade). Ao adicionar uma intenção ou entidade existente como um recurso, a adição de um conceito bem definido com exemplos rotulados.

Ao adicionar um modelo como um recurso, você pode definir o recurso como:
* **[Necessário](#required-features)**. Um recurso necessário deve ser encontrado para que o modelo seja retornado do ponto de extremidade de previsão.
* **[Global](#global-features)**. Um recurso global se aplica a todo o aplicativo.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usar uma entidade como um recurso para uma intenção

Adicione uma entidade como um recurso a uma intenção quando a detecção dessa entidade for significativa para a intenção.

Por exemplo, se a intenção é para reservar um vôo, `BookFlight` e a entidade é informações de tíquete (como o número de estações, origem e destino), localizar a entidade de informações de tíquete deve adicionar um peso significativo à previsão da `BookFlight` intenção.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usar uma entidade como um recurso para outra entidade

Uma entidade (A) deve ser adicionada como um recurso a outra entidade (B) quando a detecção dessa entidade (A) é significativa para a previsão da entidade (B).

Por exemplo, se n entidade de endereço de envio contivesse uma subentidade endereço, então localizar a subentidade endereço adiciona um peso significativo à previsão para a entidade endereço de envio.

* Endereço de envio (entidade aprendida pelo computador)
    * Número da rua (subentidade)
    * Endereço (subentidade)
    * Cidade (subentidade)
    * Estado ou província (subentidade)
    * País/região (subentidade)
    * CEP (subentidade)

## <a name="nested-subentities-with-features"></a>Subentidades aninhadas com recursos

Uma subentidade aprendida pelo computador indica que um conceito está presente na entidade pai, se esse pai é outra subentidade ou a entidade superior. O valor da subentidade atua como um recurso para seu pai.

Uma subentidade pode ter uma lista de frases como um recurso, bem como um modelo (outra entidade) como um recurso.

Quando a subentidade tiver uma lista de frases, isso aumentará o vocabulário do conceito, mas não adicionará nenhuma informação à resposta JSON da previsão.

Quando a subentidade tem um recurso de outra entidade, a resposta JSON inclui os dados extraídos dessa outra entidade.

## <a name="required-features"></a>Recursos necessários

Um recurso necessário deve ser encontrado para que o modelo seja retornado do ponto de extremidade de previsão. Use um recurso necessário quando você souber que seus dados de entrada devem corresponder ao recurso.

Se o texto expressão não corresponder ao recurso necessário, ele não será extraído.

**Um recurso necessário usa uma entidade aprendida não Machine**:
* Entidade de expressão regular
* Entidade de lista
* Entidade predefinida

Quais são os bons recursos a serem marcados conforme necessário? Se você tiver certeza de que seu modelo será encontrado nos dados, defina o recurso conforme necessário. Um recurso necessário não retorna nada, se não for encontrado.

Continuando com o exemplo do endereço de envio:
* Endereço de envio (entidade aprendida pelo computador)
    * Número da rua (subentidade)
    * Endereço (subentidade)
    * Nome da rua (subentidade)
    * Cidade (subentidade)
    * Estado ou província (subentidade)
    * País/região (subentidade)
    * CEP (subentidade)

### <a name="required-feature-using-prebuilt-entities"></a>Recurso necessário usando entidades predefinidas

A cidade, o estado e o país/região geralmente são um conjunto fechado de listas, o que significa que elas não mudam muito ao longo do tempo. Essas entidades podem ter os recursos recomendados relevantes e esses recursos podem ser marcados como obrigatórios. Isso significa que o endereço de remessa inteiro não é retornado é que as entidades com os recursos necessários não foram encontradas.

E se a cidade, o estado ou o país/região estiverem no expressão, mas em um local ou gírias que o LUIS não espera? Se você quiser fornecer algum pós-processamento para ajudar a resolver a entidade, devido a uma pontuação de confiança baixa do LUIS, não marque o recurso conforme necessário.

Outro exemplo de um recurso necessário para o endereço de envio é tornar o número da rua um número [predefinido](luis-reference-prebuilt-entities.md) necessário. Isso permite que um usuário insira "1 Microsoft Way" ou "One Microsoft Way". Ambos serão resolvidos para um número de "1" para a subentidade número da rua.

### <a name="required-feature-using-list-entities"></a>Recurso necessário usando entidades de lista

Uma [entidade de lista](reference-entity-list.md) é usada como uma lista de nomes canônicos junto com seus sinônimos. Como um recurso necessário, se o expressão não incluir o nome canônico ou sinônimo, a entidade não será retornada como parte do ponto de extremidade de previsão.

Continuando com o exemplo de endereço de envio, suponha que sua empresa seja enviada apenas para um conjunto limitado de países/regiões. Você pode criar uma entidade de lista que inclui várias maneiras pelas quais o cliente pode referenciar o país. Se LUIS não encontrar uma correspondência exata dentro do texto do expressão, a entidade (que tem o recurso necessário da entidade List) não será retornada na previsão.

|Nome canônico|Sinônimos|
|--|--|
|Estados Unidos|EUA<br>U. S. A<br>EUA<br>EUA<br>0|

O aplicativo cliente, como um bot de chat, pode fazer uma pergunta a seguir para que o cliente entenda que a seleção de país/região é limitada e _necessária_.

### <a name="required-feature-using-regular-expression-entities"></a>Recurso necessário usando entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) usada como um recurso necessário fornece recursos avançados de correspondência de texto.

Continuando com o endereço de envio, você pode criar uma expressão regular que capture as regras de sintaxe dos códigos postais de país/região.

## <a name="global-features"></a>Recursos globais

Embora o uso mais comum seja aplicar um recurso a um modelo específico, você pode configurar o recurso como um **recurso global** para aplicá-lo a todo o seu aplicativo.

O uso mais comum de um recurso global é adicionar um vocabulário adicional, como palavras de outra linguagem, ao aplicativo. Se seus clientes usam um idioma principal, mas esperam poder usar outro idioma dentro do mesmo expressão, você pode adicionar um recurso que inclui palavras do idioma secundário.

Como o usuário esperava usar a segunda linguagem em qualquer tentativa ou entidade, ele deve ser adicionado em uma lista de frases com a lista de frases configurada como um recurso global.

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* [Estenda](schema-change-prediction-runtime.md) seus modelos de aplicativo no tempo de execução de previsão
* Veja [Adicionar recursos](luis-how-to-add-features.md) para saber como adicionar recursos ao seu aplicativo LUIS.
