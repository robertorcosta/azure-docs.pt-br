---
title: Converta dados JSON com transformações líquidas
description: Crie transformações ou mapas para transformações avançadas de JSON usando o Aplicativo Lógico do Azure e o modelo Liquid
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879166"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Realize transformações avançadas de JSON com modelos Liquid em Aplicativos Lógicos do Azure

Você pode executar transformações básicas de JSON em seus aplicativos lógicos com ações de operação de dados nativos como **Compor** ou **Analisar JSON**. Para executar transformações avançadas de JSON, você pode criar modelos ou mapas com [Liquid](https://shopify.github.io/liquid/), que é uma linguagem do modelo de código-fonte aberto para aplicativos Web flexíveis. Um modelo Liquid define como transformar a saída JSON e suporta transformações JSON mais complexas, como iterações, fluxos de controle, variáveis e assim por diante.

Antes de realizar uma transformação Liquid em seu aplicativo lógico, você deve primeiro definir o mapeamento JSON para JSON com um modelo Liquid e armazenar esse mapa em sua conta de integração. Este artigo mostra como criar e usar esse modelo ou mapa Liquid.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta básica [de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Conhecimento básico sobre [linguagem de modelo líquido](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Criar um modelo ou mapa Liquid para sua conta de integração

1. Para este exemplo, crie o modelo Liquid de amostra descrito nesta etapa. No modelo Liquid, você pode usar [filtros Liquid](https://shopify.github.io/liquid/basics/introduction/#filters), que usam convenções de nomeação [DotLiquid](https://github.com/dotliquid/dotliquid) e C#.

   > [!NOTE]
   > Certifique-se de que os nomes do filtro *usem o invólucro de frase sinuoso* em seu modelo. Caso contrário, os filtros não funcionarão. Além disso, os mapas têm [limites de tamanho de arquivo.](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. No [portal Azure](https://portal.azure.com), da caixa de `integration accounts`pesquisa do Azure, digite e selecione **contas de Integração**.

   ![Encontre "Contas de integração"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Encontre e selecione sua conta de integração.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. No **painel Visão geral,** em **Componentes,** selecione **Mapas**.

    ![Selecione o azulejo "Mapas"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. No painel **Mapas,** selecione **Adicionar** e forneça esses detalhes para o seu mapa:

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Nome** | `JsonToJsonTemplate` | O nome de seu mapa, que é "JsontoJsonTemplate" neste exemplo | 
   | **Tipo de mapa** | **liquid** | O tipo do mapa. Para JSON para transformação de JSON, você deve selecionar **Liquid**. | 
   | **Mapa** | `SimpleJsonToJsonTemplate.liquid` | Um arquivo de modelo ou mapa Liquid existente para usar para a transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para localizar este arquivo, você pode usar o seletor de arquivos. Para obter limites de tamanho do mapa, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Adicionar modelo líquido](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicione a ação Liquid para transformação de JSON

1. No portal do Azure, siga estas etapas para [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. No Designer do Aplicativo Lógico, adicione o [Gatilho de solicitação](../connectors/connectors-native-reqres.md#add-request) ao seu aplicativo lógico.

1. No gatilho, escolha **Nova etapa**. Na caixa de `liquid` pesquisa, digite como seu filtro e selecione esta ação: **Transforme JSON em JSON - Liquid**

   ![Localizar e selecionar a ação Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Abra a lista **Mapa** e selecione seu modelo Liquid, que é "JsonToJsonTemplate" neste exemplo.

   ![Selecionar mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas estiver vazia, o aplicativo lógico muito provavelmente não está vinculado à sua conta de integração. 
   Para vincular seu aplicativo lógico à conta de integração que tem o modelo ou mapa Liquid, siga estas etapas:

   1. No menu aplicativo lógica, selecione **Configurações de fluxo de trabalho**.

   1. Na **lista Selecionar uma conta Integração,** selecione sua conta de integração e selecione **Salvar**.

      ![Vincular o aplicativo lógico a uma conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Agora adicione a propriedade **Conteúdo** a esta ação. Abra a **lista Adicionar novos parâmetros** e selecione **Conteúdo**.

   ![Adicionar propriedade "Conteúdo" à ação](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Para definir o valor da propriedade **Conteúdo,** clique na caixa **Conteúdo para** que a lista de conteúdo dinâmico seja exibida. Selecione o **token Corpo,** que representa a saída de conteúdo do corpo do gatilho.

   ![Selecione o token "Body" para o valor da propriedade "Conteúdo"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Após a conclusão, a ação será semelhante a este exemplo:

   ![Concluído "Transforme JSON em JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Poste a entrada JSON no seu aplicativo lógico do [Postman](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. A saída JSON transformada do seu aplicativo lógico é parecida com este exemplo:
  
![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação de Liquid
O Liquid não está limitado a apenas transformações de JSON. Aqui estão outras ações de transformação disponíveis que usam o Liquid.

* Transformar o JSON em texto
  
  Aqui está o modelo Liquid usado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Aqui estão as entradas e saídas de exemplo:
  
   ![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformar XML em JSON
  
  Aqui está o modelo Liquid usado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Aqui estão as entradas e saídas de exemplo:

   ![Exemplo de saída XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformar XML em texto
  
  Aqui está o modelo Liquid usado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Aqui estão as entradas e saídas de exemplo:

   ![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Conheça o Enterprise Integration Pack")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Conheça os mapas de integração empresarial")  

