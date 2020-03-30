---
title: Anexar serviços cognitivos a um skillset
titleSuffix: Azure Cognitive Search
description: Instruções para anexar uma assinatura all-in-one dos Serviços Cognitivos a um pipeline de enriquecimento de IA no Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472444"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Anexar um recurso de Serviços Cognitivos a um skillset na Pesquisa Cognitiva do Azure 

Ao configurar um pipeline de enriquecimento no Azure Cognitive Search, você pode enriquecer um número limitado de documentos gratuitamente. Para cargas de trabalho maiores e mais freqüentes, você deve anexar um recurso de Serviços Cognitivos faturados.

Neste artigo, você aprenderá a anexar um recurso atribuindo uma chave a um skillset que define um pipeline de enriquecimento.

## <a name="resources-used-during-enrichment"></a>Recursos utilizados durante o enriquecimento

O Azure Cognitive Search tem uma dependência de Serviços Cognitivos, incluindo [Visão computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento óptico de [caracteres](https://azure.microsoft.com/services/cognitive-services/text-analytics/) (OCR), Análise de Texto para processamento de linguagem natural e outros enriquecimentos como [Tradução de Texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/). No contexto de enriquecimento na Pesquisa Cognitiva do Azure, esses algoritmos de IA são envoltos dentro de uma *habilidade*, colocados em um *skillset*, e referenciados por um *indexador* durante a indexação.

## <a name="how-billing-works"></a>Como funciona a cobrança

+ O Azure Cognitive Search usa a chave de recursos dos Serviços Cognitivos que você fornece em um skillset para cobrar por enriquecimento de imagem e texto. A execução de habilidades faturadas está no preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ A extração de imagens é uma operação de Pesquisa Cognitiva do Azure que ocorre quando documentos são rachados antes do enriquecimento. A extração de imagens é cobrada. Para obter preços de extração de imagens, consulte a página de preços da [Pesquisa Cognitiva do Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

+ A extração de texto também ocorre durante a frase de cracking do documento. Não é cobrado.

+ Habilidades que não chamam de Serviços Cognitivos, incluindo habilidades condicionais, modelador, mesclagem de texto e divisão de texto, não são cobradas.

## <a name="same-region-requirement"></a>Exigência da mesma região

Exigimos que a Busca Cognitiva Do Azure e os Serviços Cognitivos Azure existam na mesma região. Caso contrário, você receberá esta mensagem no tempo de execução:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Não há como mover um serviço através das regiões. Se você pegar esse erro, você deve criar um novo recurso de Serviços Cognitivos na mesma região que o Azure Cognitive Search.

> [!NOTE]
> Algumas habilidades incorporadas são baseadas em serviços cognitivos não regionais (por exemplo, a [Habilidade de Tradução de Texto](cognitive-search-skill-text-translation.md)). Usar uma habilidade não regional significa que sua solicitação pode ser atendida em uma região diferente da região de Pesquisa Cognitiva do Azure. Para obter mais informações sobre serviços não-regionais, consulte a página [produtos de Serviços Cognitivos por região.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Usar recursos gratuitos

Você pode usar uma opção de processamento limitada e gratuita para completar o tutorial de enriquecimento de IA e exercícios de partida rápida.

Os recursos gratuitos (enriquecimentos limitados) são restritos a 20 documentos por dia, por indexador. Você pode excluir e recriar o indexador para redefinir o contador.

1. Abra o assistente de dados importação:

   ![Abra o assistente de dados Importação](media/search-get-started-portal/import-data-cmd.png "Abra o assistente de dados Importação")

1. Escolha uma fonte de dados e continue a **adicionar enriquecimento de IA (Opcional)**. Para obter um passo-a-passo deste assistente, consulte [Criar um índice no portal Azure](search-get-started-portal.md).

1. Expanda **os serviços cognitivos** de conexão e selecione **Free (enriquecimentos limitados):**

   ![Seção serviços cognitivos de conexão expandida](./media/cognitive-search-attach-cognitive-services/attach1.png "Seção serviços cognitivos de conexão expandida")

1. Agora você pode continuar com os próximos passos, incluindo **adicionar habilidades cognitivas**.

## <a name="use-billable-resources"></a>Usar recursos faturáveis

Para cargas de trabalho que criam mais de 20 enriquecimentos por dia, certifique-se de anexar um recurso de Serviços Cognitivos faturados. Recomendamos que você sempre anexe um recurso de Serviços Cognitivos faturados, mesmo que você nunca pretenda chamar APIs de Serviços Cognitivos. Anexar um recurso substitui o limite diário.

Você é cobrado apenas por habilidades que chamam de APIs de Serviços Cognitivos. Você não é cobrado por [habilidades personalizadas,](cognitive-search-create-custom-skill-example.md)ou habilidades como [fusão de texto,](cognitive-search-skill-textmerger.md) [divisor de texto](cognitive-search-skill-textsplit.md)e [modelador,](cognitive-search-skill-shaper.md)que não são baseadas em API.

1. Abra o assistente de dados Importação, escolha uma fonte de dados e continue a **adicionar enriquecimento de IA (Opcional)**.

1. ExpandA **os serviços cognitivos** de anexar e, em seguida, selecione **Criar novos recursos de Serviços Cognitivos**. Uma nova guia é aberta para que você possa criar o recurso:

   ![Criar um recurso dos Serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Criar um recurso dos Serviços Cognitivos")

1. Na lista **Localização,** selecione a região onde o serviço de Pesquisa Cognitiva do Azure está localizado. Certifique-se de usar esta região por razões de desempenho. O uso dessa região também anula as cargas de largura de banda de saída em todas as regiões.

1. Na lista **de níveis de preços,** selecione **S0** para obter a coleção completa de recursos de Serviços Cognitivos, incluindo os recursos de Visão e Linguagem que respaldam as habilidades incorporadas fornecidas pelo Azure Cognitive Search.

   Para o nível S0, você pode encontrar taxas para cargas de trabalho específicas na página de preços dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na lista **Selecionar Oferta,** certifique-se de que **os Serviços Cognitivos** estão selecionados.
   + Em **recursos de linguagem,** as taxas para **Padrão de Análise de Texto** aplicam-se à indexação de IA.
   + Em **recursos vision,** as taxas para **Visão computacional S1** se aplicam.

1. Selecione **Criar** para prover o novo recurso de Serviços Cognitivos.

1. Retornar à guia anterior, que contém o assistente de dados Importar. Selecione **Atualizar** para mostrar o recurso serviços cognitivos e, em seguida, selecione o recurso:

   ![Selecione o recurso serviços cognitivos](./media/cognitive-search-attach-cognitive-services/attach2.png "Selecione o recurso serviços cognitivos")

1. Expanda a seção **Adicionar habilidades cognitivas** para selecionar as habilidades cognitivas específicas que você deseja executar em seus dados. Complete o resto do mago.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um conjunto de habilidades existente a um recurso de Serviços Cognitivos

Se você tiver um conjunto de habilidades existente, poderá anexá-lo a um recurso de Serviços Cognitivos novo ou diferente.

1. Na página **visão geral do serviço,** selecione **Skillsets:**

   ![Guia de conjuntos de habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Guia de conjuntos de habilidades")

1. Selecione o nome do skillset e, em seguida, selecione um recurso existente ou crie um novo. Selecione **OK** para confirmar as alterações.

   ![Lista de recursos do Skillset](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos do Skillset")

   Lembre-se que a opção **Free (Enriquecimentos limitados)** limita você a 20 documentos diariamente, e que você pode usar **criar novos recursos de Serviços Cognitivos** para prover um novo recurso faturado. Se você criar um novo recurso, selecione **Atualizar** para atualizar a lista de recursos dos Serviços cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar Serviços Cognitivos de modo programático

Ao definir o conjunto de habilidades de forma programática, adicione uma seção `cognitiveServices` ao conjunto de habilidades. Nessa seção, inclua a chave do recurso de Serviços Cognitivos que você deseja associar com as habilidades. Lembre-se que o recurso deve estar na mesma região que seu recurso de Pesquisa Cognitiva do Azure. Também inclua `@odata.type`e defina como `#Microsoft.Azure.Search.CognitiveServicesByKey`.

O exemplo a seguir mostra esse padrão. Observe `cognitiveServices` a seção no final da definição.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Exemplo: Estimar custos

Para estimar os custos associados à indexação da pesquisa cognitiva, comece com uma ideia de como um documento médio se parece para que você possa executar alguns números. Por exemplo, você pode aproximar:

+ 1.000 PDFs.
+ Seis páginas cada.
+ Uma imagem por página (6.000 imagens).
+ 3.000 caracteres por página.

Assuma um pipeline que consiste em quebra de documento de cada PDF, extração de imagem e texto, reconhecimento óptico de caracteres (OCR) de imagens e reconhecimento de entidades de organizações.

Os preços mostrados neste artigo são hipotéticos. Eles são usados para ilustrar o processo de estimativa. Seus custos poderiam ser menores. Para os preços reais das transações, consulte Ver [preços de Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para decifração de documento com conteúdo de texto e imagem, a extração de texto atualmente é gratuita. Para 6.000 imagens, assuma $1 para cada 1.000 imagens extraídas. Isso é um custo de $6,00 para este passo.

2. Para OCR de 6.000 imagens em inglês, a habilidade cognitiva do OCR usa o melhor algoritmo (DescribeText). Supondo um custo de US$ 2,50 por 1.000 imagens a serem analisadas, pagaríamos US$ 15,00 para essa etapa.

3. Para extração da entidade, você teria um total de três registros de texto por página. Cada registro tem 1.000 caracteres. Três registros de texto por página multiplicados por 6.000 páginas equivalem a 18.000 registros de texto. Supondo US$ 2,00 por 1.000 registros de texto, essa etapa custaria US$36,00.

Juntando tudo, você pagaria cerca de US$ 57,00 para ingerir 1.000 documentos PDF deste tipo com as habilidades descritas.

## <a name="next-steps"></a>Próximas etapas
+ [Página de preços da Pesquisa Cognitiva do Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
