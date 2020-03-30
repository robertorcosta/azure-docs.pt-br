---
title: Gerenciar o plano de serviço de aplicativos
description: Aprenda a executar diferentes tarefas para gerenciar um plano de Serviço de Aplicativo, como criar, mover, dimensionar e excluir.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, trocar, criar, gerenciar, gerenciamento
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 68cabbd00dd0b738590109cc39d8df82f5b7362d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280749"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerenciar um plano do Serviço de Aplicativo no Azure

Um [Plano do Serviço de Aplicativo do Azure](overview-hosting-plans.md) fornece os recursos que um aplicativo de serviço de aplicativo precisa para ser executado. Este guia mostra como gerenciar um Plano do Serviço de Aplicativo.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

> [!TIP]
> Se você tem um Ambiente do Serviço de Aplicativo, consulte [Criar um Plano do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Você pode criar um Plano do Serviço de Aplicativo vazio ou pode criar um plano como parte da criação de um aplicativo.

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso**.

   ![Crie um recurso no portal Azure.][createResource] 

1. Selecione **Novo** > **aplicativo da Web** ou outro tipo de aplicativo de serviço de aplicativo.

   ![Crie um aplicativo no portal do Azure.][createWebApp] 

2. Configure a seção **Detalhes da ocorrência** antes de configurar o plano serviço do aplicativo. Configurações como **Publicar** e **Sistemas Operacionais** podem alterar os níveis de preços disponíveis para o seu plano de Serviço de Aplicativo. **Região** determina onde seu plano de Serviço de Aplicativo é criado. 
   
3. Na seção Plano de Serviço do **Aplicativo,** selecione um plano existente ou crie um plano selecionando **Criar novo**.

   ![Crie um plano do Serviço de Aplicativo.][createASP] 

4. Ao criar um plano, você pode selecionar a camada de preços do novo plano. Em **Sku e tamanho,** selecione **Alterar o tamanho** para alterar o nível de preços. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover um aplicativo para um plano de Serviço de Aplicativo

Você pode mover um aplicativo para outro plano de Serviço de Aplicativo, desde que o plano de origem e o plano-alvo estejam no _mesmo grupo de recursos e região geográfica._

> [!NOTE]
> O Azure implanta cada novo plano do Serviço de Aplicativo em uma unidade de implantação, chamada internamente de espaço da Web. Cada região pode ter muitos espaços da Web, mas seu aplicativo só pode se mover entre planos criados no mesmo espaço da Web. Um Ambiente de Serviço de Aplicativo é um espaço da Web isolado, portanto, os aplicativos podem ser movidos entre planos no mesmo Ambiente de Serviço de Aplicativo, mas não entre planos em Ambientes de Serviço de Aplicativo diferentes.
>
> Você não pode especificar o espaço da web que deseja ao criar um plano, mas é possível garantir que um plano seja criado no mesmo espaço da Web de um plano existente. Em resumo, todos os planos criados com o mesmo grupo de recursos e combinação de regiões são implementados no mesmo espaço da web. Por exemplo, se você criou um plano no grupo de recursos A e na região B, qualquer plano criado subsequentemente no grupo de recursos A e na região B será implantado no mesmo espaço da Web. Observe que os planos não é possível mover espaços Web depois que elas forem criadas, para que você não pode mover um plano para "mesmo espaço da Web" como outro plano, movendo-o para outro grupo de recursos.
> 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **serviços do App** e selecione o aplicativo que deseja mover.

2. No menu à esquerda, selecione **Alterar plano de serviço de aplicativo**.

3. No **plano de parada** do plano app service, selecione um plano existente para mover o aplicativo para. A isto mostra apenas planos que estão no mesmo grupo de recursos e região geográfica do plano atual do App Service. Se esse plano não existir, ele permite criar um plano por padrão. Você também pode criar um novo plano manualmente selecionando **Criar novo**.

4. Se você criar um plano, você pode selecionar a camada de preços do novo plano. Em **Pricing Tier**, selecione o nível existente para alterá-lo. 
   
   > [!IMPORTANT]
   > Se você está movendo um aplicativo de um plano de nível mais alto para um plano de nível inferior, como de **D1** para **F1,** o aplicativo pode perder certas capacidades no plano-alvo. Por exemplo, se o aplicativo usar certificados SSL, você poderá ver esta mensagem de erro:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Ao terminar, selecione **OK**.
   
   ![Seletor de plano do Serviço de Aplicativo.][change] 

## <a name="move-an-app-to-a-different-region"></a>Mover um aplicativo para uma região diferente

A região em que seu aplicativo é executado é a região do plano de serviço de aplicativo está. No entanto, você não pode alterar a região de um plano serviço de aplicativo. Se desejar mover o aplicativo para uma região diferente, uma alternativa será a clonagem do aplicativo. A clonagem faz uma cópia do seu aplicativo em um novo plano do Serviço de Aplicativo existente em qualquer região.

É possível localizar **Clonar aplicativo** na seção **Ferramentas de Desenvolvimento** do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Você pode ler sobre elas em [Clonagem de aplicativo do Serviço de Aplicativo do Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de Serviço de Aplicativo

Para dimensionar um plano do Serviço de Aplicativo da camada de preços, consulte [Dimensionar um aplicativo no Azure](manage-scale-up.md).

Para dimensionamento horizontal de contagem de instância do aplicativo, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Deletar um plano do Serviço de Aplicativo

Para evitar encargos inesperados, quando você excluir o último aplicativo em um plano de serviço de aplicativo, o serviço de aplicativo também exclui o plano por padrão. Se optar por manter o plano em vez disso, você deve alterar o plano para camada **Gratuita** para que você não seja cobrado.

> [!IMPORTANT]
> Os planos do Serviço de Aplicativo que não têm aplicativos associados a eles ainda incorrerão em encargos, pois continuam a reservar as instâncias da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Expandir um aplicativo no Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
