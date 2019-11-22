---
title: Importar uma especificação de OpenAPI usando o Portal do Azure | Microsoft Docs
description: Saiba como importar uma especificação de OpenAPI com o Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: 2b5bcd0d3bba914b81e305c88a512645c1a1c258
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108515"
---
# <a name="import-an-openapi-specification"></a>Importar uma especificação de OpenAPI

Este artigo mostra como importar uma API de back-end de “especificação OpenAPI” residindo em https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e hospedada no Azure. O artigo também mostra como testar a API do APIM.

> [!IMPORTANT]
> Consulte este [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) para obter dicas e informações importantes relacionadas à importação do OpenAPI.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar uma API de back-end de “Especificação de OpenAPI”
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Especificação de OpenAPI** na lista **Adicionar uma nova API**.

    ![Especificação de OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Insira as configurações apropriadas. Você pode definir todos os valores da API durante a criação. Como alternativa, você pode definir algumas delas mais tarde acessando a guia **Configurações**. <br/> Se você pressionar **Tab**, alguns campos (ou todos eles) serão preenchidos com as informações do serviço de back-end especificado.

    ![Criar uma API](./media/api-management-get-started/create-api.png)

    |Configuração|Valor|DESCRIÇÃO|
    |---|---|---|
    |**Especificação OpenAPI**|https://conferenceapi.azurewebsites.net?format=json|Referencia o serviço que implementa a API. O gerenciamento de API envia as solicitações para esse endereço.|
    |**Nome de exibição**|*API de Conferência de Demonstração*|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json. <br/>Esse nome é exibido no Portal do desenvolvedor.|
    |**Nome**|*demo-conference-api*|Fornece um nome exclusivo para a API. <br/>Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Descrição**|Forneça uma descrição opcional da API.|Se você pressionar Tab depois de inserir a URL do serviço, o APIM preencherá esse campo com base no que está no json.|
    |**Sufixo da URL da API**|*conference*|O sufixo é acrescentado à URL base do serviço de gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.|
    |**Esquema de URL**|*HTTPS*|Determina quais protocolos podem ser usados para acessar a API. |
    |**Produtos**|*Ilimitado*| Publica a API associando-a a um produto. Para adicionar, opcionalmente, essa nova API a um produto, digite o nome do produto. Esta etapa pode ser repetida várias vezes para adicionar a API a vários produtos.<br/>Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Primeiro, os desenvolvedores devem assinar um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.<br/> Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web: **Starter** e **Ilimitado**. |

4. Selecione **Criar**.

> [!NOTE]
> As limitações de importação da API estão documentadas em [outro artigo](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testar a nova API no portal do Azure

![Testar mapa de API](./media/api-management-get-started/01-import-first-api-01.png)

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.

1. Selecione a API que você criou na etapa anterior (na guia **APIs**).
2. Pressione a guia **Testar**.
3. Clique em **GetSpeakers**. A página exibe campos para parâmetros de consulta, nesse caso, não há nenhum, e cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. A chave é preenchida automaticamente.
4. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
