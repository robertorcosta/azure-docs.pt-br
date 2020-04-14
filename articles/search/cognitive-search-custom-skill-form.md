---
title: Habilidade personalizada do Reconhecimento de Formulário (C#)
titleSuffix: Azure Cognitive Search
description: Aprenda a criar uma habilidade personalizada do Form Recognizer usando C# e Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274569"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Exemplo: Crie uma habilidade personalizada do Reconhecimento de Formulário

Neste exemplo de habilidades de pesquisa cognitiva do Azure, você aprenderá a criar uma habilidade personalizada do Reconhecimento de Formulário usando C# e Visual Studio. O Form Recognizer analisa documentos e extrai pares de chaves/valores e dados de tabela. Ao envolver o Form Recognizer na [interface de habilidades personalizadas,](cognitive-search-custom-skill-interface.md)você pode adicionar esse recurso como um passo em um pipeline de enriquecimento de ponta a ponta. O pipeline pode então carregar os documentos e fazer outras transformações.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).
- Pelo menos cinco formas do mesmo tipo. Você pode usar os dados de amostra fornecidos com este guia.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do Reconhecimento de Formulários

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Treinar seu modelo

Você precisará treinar um modelo de Reconhecimento de Formulário com seus formulários de entrada antes de usar essa habilidade. Siga o [cURL quickstart](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) para aprender a treinar um modelo. Você pode usar os formulários de amostra fornecidos nesse quickstart, ou pode usar seus próprios dados. Uma vez que o modelo é treinado, copie seu valor de ID para um local seguro.

## <a name="set-up-the-custom-skill"></a>Configure a habilidade personalizada

Este tutorial usa o projeto [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) no repositório [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub. Clone este repositório para sua máquina local e navegue até **o Vision/AnalyzeForm/** para acessar o projeto. Em seguida, abra _AnalyzeForm.csproj_ no Visual Studio. Este projeto cria um recurso de função Azure que cumpre a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) e pode ser usado para o enriquecimento do Azure Cognitive Search. Ele toma documentos de formulário como entradas, e ele produz (como texto) os pares de tecla/valor que você especifica.

Primeiro, adicione variáveis de ambiente em nível de projeto. Localize o projeto **AnalyzeForm** no painel esquerdo, clique com o botão direito do mouse e selecione **Propriedades**. Na janela **Propriedades,** clique na guia **Depuração** e, em seguida, encontre o campo **Variáveis de Ambiente.** Clique **em Adicionar** para adicionar as seguintes variáveis:
* `FORMS_RECOGNIZER_ENDPOINT_URL`com o valor definido para sua URL de ponto final.
* `FORMS_RECOGNIZER_API_KEY`com o valor definido para sua chave de assinatura.
* `FORMS_RECOGNIZER_MODEL_ID`com o valor definido para o ID do modelo que você treinou.
* `FORMS_RECOGNIZER_RETRY_DELAY`com o valor definido para 1000. Este valor é o tempo em milissegundos que o programa esperará antes de tentar novamente a consulta.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`com o valor definido para 100. Esse valor é o número de vezes que o programa consultará o serviço enquanto tenta obter uma resposta bem-sucedida.

Em seguida, abra `fieldMappings` _AnalyzeForm.cs_ e encontre a variável, que faz referência ao arquivo *field-mappings.json.* Este arquivo (e a variável que o faz referência) define a lista de chaves que você deseja extrair de seus formulários e um rótulo personalizado para cada tecla. Por exemplo, um `{ "Address:", "address" }, { "Invoice For:", "recipient" }` valor de meios que o `Address:` script `Invoice For:` só salvará os valores para os campos detectados e, e rotulará esses valores com `"address"` e, `"recipient"`respectivamente.

Por fim, `contentType` observe a variável. Este script executa o modelo given Form Recognizer em documentos remotos `application/json`que são referenciados por URL, de modo que o tipo de conteúdo é . Se você quiser analisar arquivos locais incluindo seus fluxos de bytes `contentType` nas solicitações HTTP, você precisará alterar o [tipo MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) apropriado para o seu arquivo.

## <a name="test-the-function-from-visual-studio"></a>Testar a função do Visual Studio

Depois de editar seu projeto, salve-o e defina o projeto **AnalyzeForm** como o projeto de inicialização no Visual Studio (se ele ainda não estiver definido). Em seguida, pressione **F5** para executar a função em seu ambiente local. Use um serviço REST como [Carteiro](https://www.postman.com/) para chamar a função.

### <a name="http-request"></a>Solicitação HTTP

Você fará a seguinte solicitação para chamar a função.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Corpo da solicitação

Comece com o modelo do corpo de solicitação abaixo.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Aqui você precisará fornecer a URL de um formulário que tenha o mesmo tipo que os formulários com os que você treinou. Para fins de teste, você pode usar um de seus formulários de treinamento. Se você seguiu o cURL quickstart, seus formulários estarão localizados em uma conta de armazenamento blob do Azure. Abra o Azure Storage Explorer, localize um arquivo de formulário, clique com o botão direito do mouse e **selecione Obter assinatura de acesso compartilhado**. A próxima janela de diálogo fornecerá um token URL e SAS. Digite essas cordas `"formUrl"` `"formSasToken"` nos campos e campos do seu corpo de solicitação, respectivamente.

> [!div class="mx-imgBorder"]
> ![Explorador de armazenamento Azure; um documento pdf é selecionado](media/cognitive-search-skill-form/form-sas.png)

Se você quiser analisar um documento remoto que não esteja no armazenamento blob `"formUrl"` do Azure, cole sua URL no campo e deixe o `"formSasToken"` campo em branco.

> [!NOTE]
> Quando a habilidade é integrada em um skillset, a URL e o token serão fornecidos pela Pesquisa Cognitiva.

### <a name="response"></a>Resposta

Você deverá ver uma resposta semelhante ao exemplo a seguir:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando você está satisfeito com o comportamento da função, você pode publicá-lo.

1. No **Solution Explorer** no Visual Studio, clique com o botão direito do mouse no projeto e selecione **Publicar**. Escolha **Criar nova** > **publicação**.
1. Se você ainda não conectou o Visual Studio à sua conta do Azure, selecione **Adicionar uma conta...**
1. Siga os prompts na tela. Especifique um nome exclusivo para o serviço do aplicativo, a assinatura do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que você deseja usar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem e uma nova conta de armazenamento se você ainda não tiver isso. Quando terminar, selecione **Criar**.
1. Depois que a implantação estiver concluída, observe a URL do site. Esta URL é o endereço do seu aplicativo de função no Azure. Guarde-o para um local temporário.
1. No [portal Azure,](https://portal.azure.com)navegue até o Grupo `AnalyzeForm` de Recursos e procure a Função que você publicou. Na seção **Gerenciar**, você deverá ver as chaves do host. Copie a chave de host *padrão* e salve-a em um local temporário.

## <a name="connect-to-your-pipeline"></a>Conectar-se ao seu pipeline

Para usar essa habilidade em um pipeline de Pesquisa Cognitiva, você precisará adicionar uma definição de habilidade às suas habilidades. O bloco JSON a seguir é uma definição de habilidade de amostra (você deve atualizar as entradas e saídas para refletir seu cenário particular e ambiente de habilidades). Substitua por `AzureFunctionEndpointUrl` sua URL `AzureFunctionDefaultHostKey` de função e substitua por sua tecla host.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você criou uma habilidade personalizada a partir do serviço Azure Form Recognizer. Para saber mais sobre habilidades personalizadas, consulte os seguintes recursos. 

* [Azure Search Power Skills: um repositório de habilidades personalizadas](https://github.com/*zure-Samples/azure-search-power-skills)
* [Adicione uma habilidade personalizada a um pipeline de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
* [Definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
* [Crie um skillset (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
