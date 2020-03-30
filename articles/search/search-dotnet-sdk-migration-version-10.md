---
title: Upgrade para Azure Cognitive Search .NET SDK versão 10
titleSuffix: Azure Cognitive Search
description: Migre o código para o Azure Cognitive Search .NET SDK versão 10 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847516"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade para Azure Cognitive Search .NET SDK versão 10

Se você estiver usando a versão 9.0 ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar seu aplicativo para usar a versão 10.

O Azure Search é renomeado para Azure Cognitive Search na versão 10, mas os namespaces e nomes de pacotes são inalterados. Versões anteriores do SDK (9.0 e anteriores) continuam a usar o nome anterior. Para obter mais informações sobre o uso do SDK, incluindo exemplos, consulte [Como usar a Pesquisa Cognitiva do Azure a partir de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 10 adiciona vários recursos e correções de bugs, trazendo-o ao `2019-05-06`mesmo nível funcional da versão mais recente da versão rest API . Nos casos em que uma alteração quebra o código existente, vamos levá-lo através das [etapas necessárias para resolver o problema](#UpgradeSteps).

> [!NOTE]
> Se você estiver usando a versão 8.0-preview ou mais antiga, você deve atualizar para a versão 9 primeiro, e, em seguida, atualizar para a versão 10. Consulte [Atualizando para o Azure Search .NET SDK versão 9](search-dotnet-sdk-migration-version-9.md) para obter instruções.
>
> A instância do serviço de pesquisa suporta várias versões da API REST, incluindo a mais recente. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>O que há de novo na versão 10
A versão 10 do Azure Cognitive Search .NET SDK tem como alvo`2019-05-06`a versão mais recente disponível da API REST ( ) com essas atualizações:

* Introdução de duas novas habilidades - [habilidade condicional](cognitive-search-skill-conditional.md) e habilidade de tradução [de texto](cognitive-search-skill-text-translation.md).
* As entradas de habilidade do [Shaper](cognitive-search-skill-shaper.md) foram reestruturadas para acomodar a consolidação a partir de contextos aninhados. Para obter mais informações, consulte este [exemplo de definição JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Adição de duas novas funções de [mapeamento de campo:](search-indexer-field-mappings.md)
    - [Urlencode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [Urldecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Em certas ocasiões, erros e avisos que aparecem no [status de execução do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) podem ter detalhes adicionais que ajudam na depuração. `IndexerExecutionResult`foi atualizado para refletir esse comportamento.
* Habilidades individuais definidas dentro de um [skillset](cognitive-search-defining-skillset.md) podem ser identificadas opcionalmente especificando uma `name` propriedade.
* `ServiceLimits`mostra limites para `IndexerExecutionInfo` [tipos complexos](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) e mostra limites/cotas de indexadores pertinentes.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização

1. Atualize sua referência `Microsoft.Azure.Search` NuGet para usar o Console NuGet Package Manager ou clicando com o botão direito do mouse nas referências do projeto e selecionando "Gerenciar pacotes NuGet..." no Visual Studio.

2. Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. 

3. Se sua compilação falhar, você precisará corrigir cada erro de compilação. Consulte [Breaking changes in version 10](#ListOfChanges) para obter detalhes sobre como resolver cada potencial erro de compilação.

4. Após corrigir todos os avisos ou erros de build, você poderá fazer alterações no aplicativo para aproveitar as novas funcionalidades, se desejar. Novos recursos no SDK são detalhados no [que há de novo na versão 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Alterações na versão 10

Existem várias alterações na versão 10 que podem exigir alterações de código, além de reconstruir seu aplicativo.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de compilação, mas estão tecnicamente quebrando, uma vez que quebram a compatibilidade binária com conjuntos que dependem de versões anteriores dos conjuntos Azure Cognitive Search .NET SDK. Mudanças significativas que se enquadram nessa categoria também estão listadas, juntamente com recomendações. Por favor, reconstrua seu aplicativo ao atualizar para a versão 10 para evitar quaisquer problemas de compatibilidade binária.

### <a name="custom-web-api-skill-definition"></a>Definição personalizada de habilidade satisumtiva da Web

A definição da [habilidade de API da Web personalizada](cognitive-search-custom-skill-web-api.md) foi incorretamente especificada na versão 9 ou mais antiga. 

O modelo `WebApiSkill` para `HttpHeaders` especificado como uma propriedade de objeto que _contém_ um dicionário. Criar um skillset `WebApiSkill` com um construído dessa forma resultaria em uma exceção porque a API REST consideraria a solicitação mal formada. Esta questão foi corrigida, `HttpHeaders` fazendo **uma propriedade de dicionário de alto nível** sobre o modelo em si - que `WebApiSkill` é considerado um pedido válido da API REST.

Por exemplo, se você tentou instanciar um `WebApiSkill` como segue:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

alterá-lo para o seguinte, para evitar o erro de validação da API REST:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>A habilidade do Shaper permite a consolidação do contexto aninhado

A habilidade do Shaper agora pode permitir a consolidação de entrada a partir de contextos aninhados. Para habilitar essa `InputFieldMappingEntry` alteração, modificamos para que ela possa ser `Source` instanciada `SourceContext` `Inputs` especificando apenas uma propriedade, ou tanto as propriedades quanto as propriedades.

Você provavelmente não precisará fazer nenhuma alteração de código; no entanto, note que apenas uma dessas duas combinações é permitida. Isso significa que:

- Criar `InputFieldMappingEntry` um `Source` onde apenas é inicializado é válido.
- Criar `InputFieldMappingEntry` um `SourceContext` onde `Inputs` apenas e são inicializados é válido.
- Todas as outras combinações envolvendo essas três propriedades são inválidas.

Se você decidir começar a fazer uso deste novo recurso, certifique-se de que todos os seus clientes estão atualizados para usar a versão 10 primeiro, antes de implementar essa alteração. Caso contrário, há a possibilidade de que uma atualização por um cliente (usando uma versão mais antiga do SDK) para a habilidade Shaper possa resultar em erros de validação.

> [!NOTE]
> Embora o modelo `InputFieldMappingEntry` subjacente tenha sido modificado para permitir a consolidação a partir de contextos aninhados, seu uso só é válido dentro da definição de uma habilidade Shaper. O uso desse recurso em outras habilidades, embora válido no tempo de compilação, resultará em um erro de validação em tempo de execução.

## <a name="skills-can-be-identified-by-a-name"></a>Habilidades podem ser identificadas por um nome

Cada habilidade dentro de um skillset agora tem uma nova propriedade, `Name`que pode ser inicializada em seu código para ajudar a identificar a habilidade. Isso é opcional - quando não especificado (que é o padrão, se nenhuma alteração de código explícita foi feita), ele é atribuído um nome padrão usando o índice baseado em 1 da habilidade no skillset, prefixado com o caractere '#'. Por exemplo, na definição de skillset a seguir (a maioria das inicializações pulou para a brevidade):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`é atribuído um `#1` `WebApiSkill` nome, `#2`é `ShaperSkill` atribuído, `#3` é atribuído e assim por diante.

Se você optar por identificar habilidades por um nome personalizado, certifique-se de atualizar todas as instâncias de seus clientes para a versão 10 do SDK primeiro. Caso contrário, há a possibilidade de que um cliente usando `null` uma `Name` versão mais antiga do SDK possa sair da propriedade de uma habilidade, fazendo com que o cliente recue no esquema de nomeação padrão.

## <a name="details-about-errors-and-warnings"></a>Detalhes sobre erros e avisos

`ItemError`e `ItemWarning` modelos que encapsulam detalhes de erros e avisos (respectivamente) que ocorrem durante uma execução de indexador foram modificados para incluir três novas propriedades com o objetivo de ajudar na depuração do indexador. Essas propriedades são:

- `Name`: O nome da fonte na qual o erro se originou. Por exemplo, ele pode se referir a uma habilidade particular no conjunto de habilidades anexadas.
- `Details`: Detalhes adicionais sobre o erro ou aviso.
- `DocumentationLink`: Um link para um guia de solução de problemas para o erro ou aviso específico.

> [!NOTE]
> Começamos a estruturar nossos erros e avisos para incluir esses detalhes úteis sempre que possível. Estamos trabalhando para garantir que, para todos os erros e avisos, esses detalhes estejam presentes, mas é um trabalho em andamento e esses detalhes adicionais podem nem sempre ser preenchidos.

## <a name="next-steps"></a>Próximas etapas

- As alterações na habilidade do Shaper têm o maior impacto potencial no código novo ou existente. Como próximo passo, não deixe de revisitar este exemplo ilustrando a estrutura de entrada: [Shaper skill JSON exemplo de definição](cognitive-search-skill-shaper.md)
- Passe pela visão geral do enriquecimento da [IA.](cognitive-search-concept-intro.md)
- Apreciamos os seus comentários sobre o SDK. Se você encontrar problemas, sinta-se livre para nos pedir ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de prefixar seu título de emissão com "[Azure Cognitive Search]".

