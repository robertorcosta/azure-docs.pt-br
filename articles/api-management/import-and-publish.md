---
title: Importar e publicar sua primeira API no Gerenciamento de API do Azure
description: Saiba como importar uma API de Especificação OpenAPI para o Gerenciamento de API do Azure e testar sua API no portal do Azure.
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
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 0b5fbb49e2f60f101f16988538af86c2caf550eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202836"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar sua primeira API

Este tutorial mostra como importar uma API de back-end da Especificação OpenAPI em formato JSON para o Gerenciamento de API do Azure. A Microsoft fornece a API de back-end e a hospeda no Azure em [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Após importar a API de back-end para o Gerenciamento de API, sua API do Gerenciamento de API se tornará uma fachada para a API de back-end. Você pode personalizar a fachada de acordo com suas necessidades no Gerenciamento de API sem mexer na API de back-end. Para obter mais informações, consulte [Transformar e proteger sua API](transform-api.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar uma API para o Gerenciamento de API
> * Testar a API no Portal do Azure

![Nova API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Pré-requisitos

- Noções básicas sobre a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
- [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

Esta seção mostra como importar e publicar uma API de back-end da Especificação OpenAPI.

1. No painel de navegação esquerdo da sua instância do Gerenciamento de API, selecione **APIs** na seção **Gerenciamento de API**.
1. Selecione o bloco **OpenAPI** e, em seguida, selecione **Completo** na tela pop-up.
1. Na tela **Criar com base na especificação OpenAPI**, use os valores da tabela a seguir para criar sua API.

   Um asterisco vermelho ao lado de um campo no formulário indica que ele é obrigatório. Você pode definir os valores da API durante a criação ou mais tarde, acessando a guia **Configurações**.

   ![Criar uma API](./media/api-management-import-and-publish/create-api.png)

   |Configuração|Valor|Descrição|
   |-------|-----|-----------|
   |**Especificação OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|O serviço que implementa a API. O gerenciamento de API envia as solicitações para esse endereço.|
   |**Nome de exibição**|Depois de inserir a URL de serviço anterior, o Gerenciamento de API preencherá esse campo com base no JSON.|O nome exibido no portal do desenvolvedor.|
   |**Nome**|Depois de inserir a URL de serviço anterior, o Gerenciamento de API preencherá esse campo com base no JSON.|Um nome exclusivo para a API.|
   |**Descrição**|Depois de inserir a URL de serviço anterior, o Gerenciamento de API preencherá esse campo com base no JSON.|Uma descrição opcional da API.|
   |**Esquema de URL**|**HTTPS**|Quais protocolos podem ser usados para acessar a API.|
   |**Sufixo da URL da API**|*conference*|O sufixo acrescentado à URL base do serviço de Gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo; assim, o sufixo deve ser único para cada API para um editor específico.|
   |**Marcas**| |As marcas para organizar APIs para pesquisa, agrupamento ou filtragem.|
   |**Produtos**|**Ilimitado**|Associação de uma ou mais APIs. Cada instância de Gerenciamento de API é fornecida com dois produtos função Web: **Starter** e **Ilimitado**. Você publica uma API associando-a a um produto, **Ilimitado** neste exemplo.<br/>É possível incluir várias APIs em um produto e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Para adicionar essa API a outro produto, digite ou selecione o nome dele. Repita essa etapa para adicionar a API a vários produtos. Você também pode adicionar APIs aos produtos mais tarde por meio da página **Configurações**.<br/>Para obter acesso à API, os desenvolvedores devem, primeiro, inscrever-se em um produto. Quando eles assinam, recebem uma chave de assinatura boa para qualquer API nesse produto. <br/>Se você criou a instância do Gerenciamento de API, você já é um administrador, portanto, está inscrito em cada produto na instância.|
   |**Gateways**|**Gerenciado**|Gateways de API que expõem a API. Esse campo está disponível somente nos serviços dos níveis **Desenvolvedor** e **Premium**.<br/>O gateway **gerenciado** indica o gateway interno do serviço de Gerenciamento de API e que é hospedado pela Microsoft no Azure. Outros gateways são [gateways auto-hospedados](self-hosted-gateway-overview.md) e estão disponíveis apenas nas camadas de serviço Premium e Desenvolvedor. Você pode implantá-los localmente ou em outras nuvens.<br/>Se não houver gateway selecionado, a API não estará disponível e suas solicitações de API não terão sucesso.|
   |**Controlar a versão desta API?**|Selecionar ou cancelar seleção|Para obter mais informações sobre o controle de versão, confira [Publicar várias versões de sua API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Para publicar a API para consumidores de API, você deve associá-la a um produto.

2. Selecione **Criar**.

Se você tiver problemas com a importação de uma definição de API, confira a [lista de problemas e restrições conhecidos](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testar a nova API no portal do Azure

Você pode chamar operações de API diretamente do portal do Azure, que oferece uma forma fácil de exibir e testar as operações.

1. No painel de navegação esquerdo da sua instância do Gerenciamento de API, selecione **APIs** na seção **Gerenciamento de API** e selecione **API de Conferência de Demonstração**.
1. Selecione a guia **Testar** e, em seguida, **GetSpeakers**. A página mostrará **Parâmetros de consulta** e **Cabeçalhos**, se houver. O **Ocp-Apim-Subscription-Key** é preenchido automaticamente para a chave de assinatura associada a esta API.
1. Selecione **Enviar**.

   ![Testar mapa de API](./media/api-management-import-and-publish/01-import-first-api-01.png)

   O back-end responde com **200 OK** e alguns dados.

## <a name="next-steps"></a><a name="next-steps"> </a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure

Vá para o próximo tutorial para aprender a criar e publicar um produto:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
