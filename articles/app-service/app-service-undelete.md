---
title: Restaurar aplicativos excluídos
description: Saiba como restaurar um aplicativo excluído no Azure App Service. Evite a dor de cabeça de um aplicativo apagado acidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272484"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo Serviço de Aplicativo excluído usando o PowerShell

Se você exclui acidentalmente seu aplicativo no Azure App Service, você pode restaurá-lo usando os comandos do [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Os aplicativos excluídos são eliminados do sistema 30 dias após a exclusão inicial. Uma vez que um aplicativo tenha sido eliminado, ele não pode ser recuperado.
>

## <a name="re-register-app-service-resource-provider"></a>Recadastrmine o provedor de recursos do App Service
Alguns clientes podem se deparar com um problema em que a recuperação da lista de aplicativos excluídos falha. Para resolver o problema, execute o seguinte comando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Listar aplicativos excluídos

Para obter a coleção de aplicativos `Get-AzDeletedWebApp`excluídos, você pode usar .

Para obter detalhes sobre um aplicativo excluído específico, você pode usar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

As informações detalhadas incluem:

- **DeletedSiteId**: Identificador exclusivo para o aplicativo, usado para cenários onde vários aplicativos com o mesmo nome foram excluídos
- **SubscriptionID**: Assinatura contendo o recurso excluído
- **Localização**: Localização do aplicativo original
- **ResourceGroupName**: Nome do grupo de recursos original
- **Nome**: Nome do aplicativo original.
- **Slot:** o nome do slot.
- **Tempo de exclusão**: Quando o aplicativo foi excluído  

## <a name="restore-deleted-app"></a>Restaurar aplicativo excluído

Uma vez que o aplicativo que você deseja `Restore-AzDeletedWebApp`restaurar tenha sido identificado, você pode restaurá-lo usando .

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Os slots de implantação não são restaurados como parte do seu aplicativo. Se você precisar restaurar um slot de preparação use a `-Slot <slot-name>` bandeira.
>

As entradas para comando são:

- **Grupo de recursos**: Grupo de recursos-alvo onde o aplicativo será restaurado
- **Nome**: Nome para o aplicativo, deve ser globalmente único.
- **TargetAppServicePlanName**: Plano de serviço de aplicativo vinculado ao aplicativo

Por `Restore-AzDeletedWebApp` padrão, restaurará tanto a configuração do aplicativo quanto um conteúdo. Se você quiser apenas restaurar o `-RestoreContentOnly` conteúdo, use a bandeira com este comando.

> [!NOTE]
> Se o aplicativo foi hospedado e depois excluído de um ambiente de serviço de aplicativo, então ele só poderá ser restaurado se o ambiente de serviço de aplicativo correspondente ainda existir.
>

Você pode encontrar a referência completa de comando aqui: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
