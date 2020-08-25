---
title: Transformar e proteger sua API com o Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como proteger sua API com cotas e políticas de limitação (limite de taxa).
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 4c3cc572dd9629605414cd88d7735c2b31f92249
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85851252"
---
# <a name="transform-and-protect-your-api"></a>Transformar e proteger sua API

O tutorial mostra como transformar sua API para que ela não revele informações de back-end privadas. Por exemplo, você talvez queira ocultar as informações sobre a pilha de tecnologia que está em execução no back-end. Você também pode desejar ocultar URLs originais que aparecem no corpo da resposta HTTP da API e em vez disso, redirecioná-las para o gateway de APIM.

Este tutorial também mostra como é fácil adicionar proteção à API de back-end configurando o limite de taxa com o Gerenciamento de API do Azure. Por exemplo, você talvez queira limitar um número de chamadas realizadas para a API, para que não seja usada em excesso pelos desenvolvedores. Para obter mais informações, consulte [Políticas de Gerenciamento de API](api-management-policies.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> -   Transformar uma API para retirar os cabeçalhos de resposta
> -   Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM
> -   Proteger uma API adicionando a política de limite de taxa (limitação)
> -   Testar as transformações

![Políticas](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Pré-requisitos

-   Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
-   Compreenda o [conceito de políticas no Gerenciamento de API do Azure](api-management-howto-policies.md).
-   Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
-   Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformar uma API para retirar os cabeçalhos de resposta

Esta seção mostra como ocultar os cabeçalhos HTTP que você não deseja mostrar aos usuários. Neste exemplo, os cabeçalhos a seguir são excluídos na resposta HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Em sua instância de serviço do APIM, selecione **APIs** (em **GERENCIAMENTO DE API**).
2. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
3. Clique na guia **Testar** na parte superior da tela.
4. Selecione a operação **GetSpeakers**.
5. Pressione o botão **Enviar** na parte inferior da tela.

A resposta original deve se parecer com esta:

![Políticas](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Definir a política de transformação

![Definir política de saída](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. Selecione **API de Conferência de Demonstração**.
2. Na parte superior da tela, selecione a guia **Design**.
3. Selecione **Todas as operações**.
4. Na seção **Processamento de saída**, clique no ícone **</>** .
5. Posicione o cursor dentro do elemento **&lt;outbound&gt;** .
6. Na janela à direita, em **Políticas de transformação**, clique em **+ Definir cabeçalho HTTP** duas vezes (para inserir dois snippets de código de política).

   ![Políticas](./media/transform-api/transform-api.png)

7. Modifique seu código **\<outbound>** para ter a seguinte aparência:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   ![Políticas](./media/transform-api/set-policy.png)

8. Clique no botão **Salvar** .

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM

Esta seção mostra como ocultar URLs originais que aparecem no corpo da resposta HTTP da API e em vez disso, redirecioná-las para o gateway de APIM.

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Selecione **API de Conferência de Demonstração**.
2. Clique na guia **Testar** na parte superior da tela.
3. Selecione a operação **GetSpeakers**.
4. Pressione o botão **Enviar** na parte inferior da tela.

    Como você pode ver, a resposta original tem esta aparência:

    ![Políticas](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1.  Selecione **API de Conferência de Demonstração**.
2.  Selecione **Todas as operações**.
3.  Na parte superior da tela, selecione a guia **Design**.
4.  Na seção **Processamento de saída**, clique no ícone **</>** .
5.  Posicione o cursor dentro do elemento de **&lt;saída&gt;** e clique no botão **Mostrar snippets** no canto superior direito.
6.  Na janela à direita, em **Políticas de transformação**, clique em **Mascarar URLs no conteúdo**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Proteger uma API adicionando a política de limite de taxa (limitação)

Esta seção mostra como adicionar a proteção para a API de back-end configurando limites de taxa. Por exemplo, você talvez queira limitar um número de chamadas realizadas para a API, para que não seja usada em excesso pelos desenvolvedores. Neste exemplo, o limite é definido como 3 chamadas por 15 segundos para cada ID de assinatura. Depois de 15 segundos, um desenvolvedor pode repetir a chamada à API.

![Definir política de entrada](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  Selecione **API de Conferência de Demonstração**.
2.  Selecione **Todas as operações**.
3.  Na parte superior da tela, selecione a guia **Design**.
4.  Na seção **Processamento de entrada**, clique no ícone **</>** .
5.  Posicione o cursor dentro do elemento **&lt;inbound&gt;** .
6.  Na janela à direita, em **Políticas de restrição de acesso**, clique em **+ Limit call rate per key (+ Limitar taxa de chamada por chave)** .
7.  Modifique o código **rate-limit-by-key** (no elemento **\<inbound\>** ) para o seguinte código:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testar as transformações

Nesse ponto, se você examinar o código no editor de códigos, suas políticas terão esta aparência:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

O restante desta seção testa as transformações de política que você definir neste artigo.

### <a name="test-the-stripped-response-headers"></a>Testar os cabeçalhos de resposta retirados

1. Selecione **API de Conferência de Demonstração**.
2. Selecione a guia **Testar**.
3. Clique na operação **GetSpeakers**.
4. Pressione **Enviar**.

    Como você pode ver, os cabeçalhos foram retirados:

    ![Políticas](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Testar a URL substituída

1. Selecione **API de Conferência de Demonstração**.
2. Selecione a guia **Testar**.
3. Clique na operação **GetSpeakers**.
4. Pressione **Enviar**.

    Como você pode ver a URL foi substituída.

    ![Políticas](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Testar o limite de taxa (limitação)

1. Selecione **API de Conferência de Demonstração**.
2. Selecione a guia **Testar**.
3. Clique na operação **GetSpeakers**.
4. Pressione **Enviar** três vezes em uma linha.

    Depois de enviar a solicitação três vezes, você obtém a resposta **429 Muitas solicitações**.

5. Aguarde 15 segundos e pressione **Enviar** novamente. Neste momento, você deve obter uma resposta **200 OK**.

    ![Limitação](./media/transform-api/test-throttling.png)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> -   Transformar uma API para retirar os cabeçalhos de resposta
> -   Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM
> -   Proteger uma API adicionando a política de limite de taxa (limitação)
> -   Testar as transformações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Monitorar sua API](api-management-howto-use-azure-monitor.md)
