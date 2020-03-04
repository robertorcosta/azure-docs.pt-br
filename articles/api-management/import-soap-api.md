---
title: Importar a API SOAP usando o Portal do Azure | Microsoft Docs
description: Aprenda a importar a API SOAP com o Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 359b90cc434dad04fc0296c54fcc762f3a75062d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107667"
---
# <a name="import-soap-api"></a>Importar a API SOAP

Este artigo mostra como importar uma representação XML padrão da API SOAP. O artigo também mostra como testar a API do APIM.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar a API SOAP
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Prerequisites

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **WSDL** na lista **Adicionar uma nova API**.

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. Na **Especificação WSDL**, digite a URL em que a API SOAP reside.
4. O botão de opção **Passagem SOAP** é selecionado por padrão. Com essa seleção, a API será exposta como SOAP. O consumidor precisa usar regras SOAP. Se você quiser "restify" a API, siga as etapas em [Import a SOAP API and convert it to REST](restify-soap-api.md) (Importar uma API SOAP e convertê-la para REST).

    ![Passagem](./media/import-soap-api/pass-through.png)
5. Pressione Tab.

    Os seguintes campos são preenchidos com as informações da API SOAP: Nome de exibição, nome, descrição.
6. Adicione um sufixo da URL da API. O sufixo é um nome que identifica essa API específica nesta instância do APIM. Ele deve ser exclusivo nesta instância de APIM.
9. Publica a API associando-a a um produto. Nesse caso, o produto "*Ilimitado*" é usado.  Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Primeiro, os desenvolvedores devem assinar um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    * **Inicial**
    * **Ilimitado**   
10. Selecione **Criar**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testar a nova API no portal administrativo

As operações podem ser chamadas diretamente do portal administrativo, que fornece uma maneira conveniente de exibir e testar as operações de uma API.  

1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.
3. Selecione alguma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do APIM, já é um administrador e, portanto, a chave é preenchida automaticamente. 
1. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)