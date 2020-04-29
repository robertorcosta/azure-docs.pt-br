---
title: Restaurar aplicativos excluídos
description: Saiba como restaurar um aplicativo excluído no serviço Azure App. Evite a dor de cabeça de um aplicativo excluído acidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272484"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo Serviço de Aplicativo excluído usando o PowerShell

Se você tiver excluído acidentalmente seu aplicativo no serviço Azure App, poderá restaurá-lo usando os comandos do [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Os aplicativos excluídos são limpos do sistema 30 dias após a exclusão inicial. Depois que um aplicativo tiver sido limpo, ele não poderá ser recuperado.
>

## <a name="re-register-app-service-resource-provider"></a>Registrar novamente o provedor de recursos do serviço de aplicativo
Alguns clientes podem chegar a um problema em que a recuperação da lista de aplicativos excluídos falha. Para resolver o problema, execute o seguinte comando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Listar aplicativos excluídos

Para obter a coleção de aplicativos excluídos, você pode usar `Get-AzDeletedWebApp`.

Para obter detalhes sobre um aplicativo específico excluído, você pode usar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

As informações detalhadas incluem:

- **DeletedSiteId**: identificador exclusivo para o aplicativo, usado para cenários em que vários aplicativos com o mesmo nome foram excluídos
- **SubscriptionId**: assinatura que contém o recurso excluído
- **Local**: local do aplicativo original
- **ResourceGroupName**: nome do grupo de recursos original
- **Nome**: o nome do aplicativo original.
- **Slot**: o nome do slot.
- **Hora da exclusão**: quando o aplicativo foi excluído  

## <a name="restore-deleted-app"></a>Restaurar aplicativo excluído

Depois que o aplicativo que você deseja restaurar tiver sido identificado, você poderá restaurá- `Restore-AzDeletedWebApp`lo usando o.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Os slots de implantação não são restaurados como parte do seu aplicativo. Se você precisar restaurar um slot de preparo, use o `-Slot <slot-name>` sinalizador.
>

As entradas para o comando são:

- **Grupo de recursos**: grupo de recursos de destino em que o aplicativo será restaurado
- **Nome**: o nome do aplicativo deve ser globalmente exclusivo.
- **TargetAppServicePlanName**: plano do serviço de aplicativo vinculado ao aplicativo

Por padrão `Restore-AzDeletedWebApp` , o também restaurará a configuração do aplicativo como um conteúdo. Se você quiser restaurar apenas o conteúdo, use o `-RestoreContentOnly` sinalizador com este commandlet.

> [!NOTE]
> Se o aplicativo foi hospedado em e, em seguida, excluído de um Ambiente do Serviço de Aplicativo, ele só poderá ser restaurado se o Ambiente do Serviço de Aplicativo correspondente ainda existir.
>

Você pode encontrar a referência completa do commandlet aqui: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
