---
title: Defina fluxos de trabalho de moderação com o console REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Você pode usar as APIs de revisão de conteúdo do Azure para definir fluxos de trabalho e limiares personalizados com base em suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754178"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definir e usar fluxos de trabalho de moderação (REST)

Os fluxos de trabalho são filtros personalizados baseados em nuvem que você pode usar para lidar com o conteúdo de forma mais eficiente. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas apropriadas. Este guia mostra como usar as APIs REST do fluxo de trabalho, através do console aPI, para criar e usar fluxos de trabalho. Uma vez que você entenda a estrutura das APIs, você pode facilmente portar essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Faça login ou crie uma conta no site da [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderador de Conteúdo.

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Para criar ou atualizar um fluxo de trabalho, vá para a página de referência **[do Workflow - Crie ou Atualize](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** a API e selecione o botão para sua região-chave (você pode encontrá-lo na URL endpoint na página **Credenciais** da [ferramenta Revisão).](https://contentmoderator.cognitive.microsoft.com/) Isso inicia o console da API, onde você pode facilmente construir e executar chamadas de API REST.

![Seleção de região da página Fluxo de trabalho - Criar ou atualizar](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Digite parâmetros de chamada REST

Digite valores para **equipe,** **nome de fluxo de trabalho**e chave de assinatura **Ocp-Apim:**

- **equipe**: O ID da equipe que você criou ao configurar sua conta [de ferramenta De revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **DeD** na tela Credenciais da ferramenta Revisão).
- **nome do fluxo de trabalho**: O nome de um novo fluxo de trabalho a ser adicionado (ou um nome existente, se você quiser atualizar um fluxo de trabalho existente).
- **Chave de assinatura Ocp-Apim**: Sua tecla moderadora de conteúdo. Você pode encontrá-lo na guia **Configurações** da [ferramenta 'Revisar'.](https://contentmoderator.cognitive.microsoft.com)

![Cabeçalhos e parâmetros de consulta do console Fluxo de trabalho - Criar ou atualizar](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Insira uma definição de fluxo de trabalho

1. Editar a **caixa de corpo solicitar** para inserir a solicitação JSON com detalhes para **Descrição** e **Tipo** (ou `Image` `Text`).
2. Para **Expressão,** copie a expressão JSON do fluxo de trabalho padrão. Sua seqüência JSON final deve ser assim:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Você pode definir expressões simples, complexas e até mesmo aninhadas para seus fluxos de trabalho usando esta API. A documentação [Workflow - Create or Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) tem exemplos de lógica mais complexa.

### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem sucedida, o**Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá `true`.

### <a name="examine-the-new-workflow"></a>Examine o novo fluxo de trabalho

Na [ferramenta Revisão,](https://contentmoderator.cognitive.microsoft.com/)selecione > **'Fluxos de trabalho '** **'Configurações'** Seu novo fluxo de trabalho deve aparecer na lista.

![Revisar lista de ferramentas de fluxos de trabalho](images/workflow-console-new-workflow.PNG)

Selecione a opção **Editar** para o seu fluxo de trabalho e vá para a guia **'Desnatado'.** Aqui, você pode ver uma representação intuitiva da lógica JSON.

![Guia Designer para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obter detalhes do fluxo de trabalho

Para recuperar detalhes sobre um fluxo de trabalho existente, vá para a página de referência **[workflow - Obtenha](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** a página de referência da API e selecione o botão para sua região (a região em que sua chave é administrada).

![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

Digite os parâmetros de chamada REST como na seção acima. Certifique-se de que desta vez, **nome do fluxo de trabalho** é o nome de um fluxo de trabalho existente.

![Obter cabeçalhos e parâmetros de consulta](images/workflow-get-default.PNG)

Selecione **Enviar**. Se a operação for bem-sucedida, o **status resposta** será `200 OK`e a caixa de conteúdo **Resposta** exibirá o fluxo de trabalho no formato JSON, como o seguinte:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar fluxos de trabalho com [trabalhos moderação de conteúdo](try-review-api-job.md).