---
title: Criar uma instância de Gerenciamento de API do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para criar uma nova instância de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: acfa10ea506e063bf6b230386ca0722f1c959d18
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75442569"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Criar uma nova instância do serviço de Gerenciamento de API do Azure

O APIM (Gerenciamento de API) do Azure ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de liberar o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira o tópico [Visão geral](api-management-key-concepts.md).

Este guia de início rápido descreve as etapas para criar uma nova instância de Gerenciamento de API usando o portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![Instância de Gerenciamento de API](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-new-service"></a>Criar um novo serviço

1. No menu do portal do Azure, selecione **Criar um recurso**. Você também pode selecionar **Criar um recurso** na **Página Inicial** do Azure. 
   
   ![Selecionar Criar um recurso](./media/get-started-create-service-instance/00-CreateResource-01.png)
   
1. Na tela **Novo**, selecione **Integração** e, em seguida, selecione **Gerenciamento de API**.
   
   ![Nova instância de Gerenciamento de API do Azure](./media/get-started-create-service-instance/00-CreateResource-02.png)
   
1. Na tela **Serviço de Gerenciamento de API**, insira as configurações.
   
   ![nova instância](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)
   
   | Configuração                 | Valor sugerido                               | DESCRIÇÃO                                                                                                                                                                                                                                                                                                                         |
|-------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                | Um nome exclusivo para o serviço de gerenciamento de API | O nome não poderá ser alterado posteriormente. O nome do serviço é usado para gerar um nome de domínio padrão na forma de *{name}.azure-api.net.* Se você quiser usar um nome de domínio personalizado, consulte [Configurar um domínio personalizado](configure-custom-domain.md). <br/> O nome do serviço é usado para referir-se ao serviço e ao recurso do Azure correspondente. |
| **Assinatura**        | Sua assinatura                             | A assinatura sob a qual essa nova instância de serviço será criada. Você pode selecionar a assinatura entre as diferentes assinaturas do Azure às quais você tem acesso.                                                                                                                                                            |
| **Grupo de recursos**      | *apimResourceGroup*                           | Você pode selecionar um recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../azure-resource-manager/management/overview.md#resource-groups).                                                                                                  |
| **Localidade**            | *Oeste dos EUA*                                    | Selecione a região geográfica perto de você. Somente as regiões de serviço de Gerenciamento de API disponíveis aparecem na caixa de listagem suspensa.                                                                                                                                                                                                          |
| **Nome da organização**   | O nome da sua organização                 | Esse nome é usado em vários locais, incluindo o título do portal do desenvolvedor e o remetente de emails de notificação.                                                                                                                                                                                                             |
| **Email do administrador** | *admin\@org.com*                               | Defina o endereço de email ao qual todas as notificações de **Gerenciamento de API** serão enviadas.                                                                                                                                                                                                                                              |
| **Tipo de preços**        | *Desenvolvedor*                                   | Defina a camada **Desenvolvedor** para avaliar o serviço. Essa camada não é para uso em produção. Para obter mais informações sobre a colocação em escala das camadas de Gerenciamento de API, confira [atualizar e dimensionar](upgrade-and-scale.md).                                                                                                                                    |

3. Escolha **Criar**.

    > [!TIP]
    > Geralmente leva entre 20 e 30 minutos para criar um serviço de Gerenciamento de API. Selecionar **Fixar ao painel** torna a localização de um serviço recém-criado mais fácil.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá remover o grupo de recursos e todos os recursos relacionados seguindo estas etapas:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção. Você também pode selecionar **Grupos de recursos** na **Página Inicial**. 

   ![Navegação de grupos de recursos](./media/get-started-create-service-instance/00-DeleteResource-01.png)

1. Na página **Grupos de recursos**, selecione seu grupo de recursos.

   ![Navegação de grupos de recursos](./media/get-started-create-service-instance/00-DeleteResource-02.png)

1. Na página Grupo de recursos, selecione **Excluir grupo de recursos**. 
   
1. Digite o nome do seu grupo de recursos e depois selecione **Excluir**.

   ![Excluir grupo de recursos](./media/get-started-create-service-instance/00-DeleteResource-03.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar e publicar sua primeira API](import-and-publish.md)
