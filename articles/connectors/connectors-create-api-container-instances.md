---
title: Implantar & gerenciar instâncias de contêineres Do Zure
description: Automatize tarefas e fluxos de trabalho que criam e gerenciam implantações de contêineres em instâncias de contêineres do Azure usando aplicativos de lógica do Azure
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046286"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Implantar e gerenciar instâncias de contêineres do Azure usando aplicativos azure logic

Com os aplicativos azure logic e o conector Azure Container Instance, você pode configurar tarefas e fluxos de trabalho automatizados que implantam e gerenciam [grupos de contêineres](../container-instances/container-instances-container-groups.md). O conector 'Exemplo de contêiner' suporta as seguintes ações:

* Criar ou excluir um grupo de contêineres
* Obtenha as propriedades de um grupo de contêineres
* Obtenha uma lista de grupos de contêineres
* Obtenha os registros de uma instância de contêiner

Use essas ações em seus aplicativos lógicos para tarefas como executar uma carga de trabalho de contêiner em resposta a um gatilho do Logic Apps. Você também pode ter outras ações usando a saída de ações de Instância de Contêiner. 

Este conector fornece apenas ações, para iniciar seu aplicativo lógico, use um gatilho separado, como um gatilho **de recorrência** para executar uma carga de trabalho de contêiner em um cronograma regular. Ou, você pode ter necessidade de ativar uma implantação de grupo de contêineres após um evento, como a chegada de um e-mail do Outlook. 

Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md) [e como criar e gerenciar instâncias de contêineres](../container-instances/container-instances-quickstart.md)

* O aplicativo lógico onde você deseja acessar suas instâncias de contêiner. Para usar uma ação, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência.**

## <a name="add-a-container-instance-action"></a>Adicionar uma ação instância de contêiner

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

     -ou-

   * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal**+** de adição () que aparece e, em seguida, **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, digite "instância do contêiner" como seu filtro. Na lista de ações, selecione a ação conector Azure's Instance's que deseja.

1. Forneça um nome para a conexão. 

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

  Por exemplo, selecione **Criar grupo de contêineres** e digite as propriedades para um grupo de contêineres e uma ou mais instâncias de contêiner no grupo, conforme mostrado na imagem a seguir (detalhe parcial):

  ![Criar grupo de contêineres](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição OpenAPI (anteriormente Swagger) do conector, revise a página de [referência](/connectors/aci/) do conector ou [a referência yaml](../container-instances/container-instances-reference-yaml.md)do grupo de contêineres .

## <a name="next-steps"></a>Próximas etapas

* Consulte um [aplicativo de lógica de exemplo](https://github.com/Azure-Samples/aci-logicapps-integration) que executa um contêiner no Azure Container Instances para analisar o sentimento do e-mail ou texto do Twitter

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)