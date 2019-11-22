---
title: Importar e publicar sua primeira API no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como importar e publicar sua primeira API com o gerenciamento de API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: bae762b4603b2f5f80447a16671fed4e37e62b95
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108546"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar sua primeira API 

Este tutorial mostra como importar uma API de back-end da “especificação OpenAPI” residindo em https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e hospedada no Azure. 

Depois que a API de back-end é importada para o APIM (gerenciamento de API), a API do APIM torna-se uma fachada para a API de back-end. Quando você importar a API de back-end, a API de origem e a API do APIM serão idênticas. O APIM permite que você personalize a fachada de acordo com suas necessidades sem mexer na API de back-end. Para obter mais informações, consulte [Transformar e proteger sua API](transform-api.md). 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

![Nova API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

Esta seção mostra como importar e publicar uma API de back-end da especificação OpenAPI.
 
1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Especificação do OpenAPI** na lista e clique em **Completo** no pop-up.

    ![Criar uma API](./media/api-management-get-started/create-api.png)

    Você pode definir os valores da API durante a criação ou mais tarde, acessando a guia **Configurações**. O asterisco vermelho ao lado de um campo indica que ele é obrigatório.

    Use os valores da tabela abaixo para criar sua primeira API.

    | Configuração                   | Valor                                              | DESCRIÇÃO                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Especificação OpenAPI** | https://conferenceapi.azurewebsites.net?format=json | Referencia o serviço que implementa a API. O gerenciamento de API envia as solicitações para esse endereço.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Nome de exibição**          | *API de Conferência de Demonstração*                              | Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json. <br/>Esse nome é exibido no Portal do desenvolvedor.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Nome**                  | *demo-conference-api*                              | Fornece um nome exclusivo para a API. <br/>Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Descrição**           | Forneça uma descrição opcional da API.        | Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **Esquema de URL**            | *HTTPS*                                            | Determina quais protocolos podem ser usados para acessar a API.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **Sufixo da URL da API**        | *conference*                                       | O sufixo é acrescentado à URL base do serviço de gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Produtos**              | *Ilimitado*                                        | Os produtos são associações de uma ou mais APIs. É possível incluir várias APIs em um produto e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. <br/>Você publica a API associando-a a um produto (neste exemplo, *Ilimitado*). Para adicionar essa nova API a um produto, digite o nome do produto (você também pode fazer isso posteriormente, na página **Configurações**). Esta etapa pode ser repetida várias vezes para adicionar a API a vários produtos.<br/>Para obter acesso à API, os desenvolvedores devem, primeiro, inscrever-se em um produto. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. <br/> Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto.<br/> Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web: **Starter** e **Ilimitado**. |
    | **Marcas**                  |                                                    | Marcas para organizar APIs. As marcas podem ser usadas para pesquisa, agrupamento ou filtragem.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Controlar a versão desta API?**     |                                                    | Para obter mais informações sobre o controle de versão, consulte [Publicar várias versões de sua API](api-management-get-started-publish-versions.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Para publicar a API, você deve associá-la a um produto. É possível fazer isso na **página Configurações**.

3. Selecione **Criar**.

> [!TIP]
> Se você estiver tendo problemas com a importação de sua própria definição de API, [veja a lista de problemas e restrições conhecidos](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testar a nova API no portal do Azure

![Testar mapa de API](./media/api-management-get-started/01-import-first-api-01.png)

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.

1. Selecione a API que você criou na etapa anterior (na guia **APIs**).
2. Pressione a guia **Testar**.
3. Clique em **GetSpeakers**. A página exibe campos para parâmetros de consulta, nesse caso, não há nenhum, e cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. A chave é preenchida automaticamente.
4. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
