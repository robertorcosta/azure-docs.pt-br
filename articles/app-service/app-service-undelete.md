---
title: Restaurar aplicativos excluídos
description: Saiba como restaurar um aplicativo excluído no Serviço de Aplicativo do Azure. Evite ter dor de cabeça com um aplicativo excluído acidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 440f46cbeebee1b552e64eba4ebc8787a47edf56
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779222"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo Serviço de Aplicativo excluído usando o PowerShell

Se excluir acidentalmente seu aplicativo no Serviço de Aplicativo do Azure, você poderá restaurá-lo usando os comandos do [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Os aplicativos excluídos são limpos do sistema 30 dias após a exclusão inicial. Depois que um aplicativo for limpo, ele não poderá ser recuperado.
>

## <a name="re-register-app-service-resource-provider"></a>Registrar novamente o provedor de recursos do Serviço de Aplicativo
Alguns clientes podem encontrar um problema em que não é possível recuperar a lista de aplicativos excluídos. Para resolver o problema, execute o seguinte comando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Listar aplicativos excluídos

Para obter a coleção de aplicativos excluídos, você pode usar `Get-AzDeletedWebApp`.

Para mais informações sobre um aplicativo específico excluído, você pode usar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

As informações detalhadas incluem:

- **DeletedSiteId**: Identificador exclusivo do aplicativo, usado em cenários em que vários aplicativos com o mesmo nome foram excluídos
- **SubscriptionID**: Assinatura que contém o recurso excluído
- **Localização**: Local do aplicativo original
- **ResourceGroupName**: Nome do grupo de recursos original
- **Name**: Nome do aplicativo original.
- **Slot**: o nome do slot.
- **Hora da exclusão**: Quando o aplicativo foi excluído  

## <a name="restore-deleted-app"></a>Restaurar aplicativo excluído
>[!NOTE]
> `Restore-AzDeletedWebApp` não tem suporte para aplicativos de funções.

Depois de identificar o aplicativo que quer restaurar, você poderá usar `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Os slots de implantação não são restaurados como parte do seu aplicativo. Se você precisar restaurar um slot de preparo, use o sinalizador `-Slot <slot-name>`.
>

As entradas para o comando são:

- **Grupo de Recursos**: Grupo de recursos de destino onde o aplicativo será restaurado
- **Name**: O nome do aplicativo que deve ser globalmente exclusivo.
- **TargetAppServicePlanName**: Plano do Serviço de Aplicativo vinculado ao aplicativo

Por padrão, `Restore-AzDeletedWebApp` restaurará a configuração do seu aplicativo, bem como um conteúdo. Se você quiser restaurar apenas o conteúdo, use o sinalizador `-RestoreContentOnly` com esse commandlet.

> [!NOTE]
> Se o aplicativo foi hospedado em e, em seguida, excluído de um Ambiente do Serviço de Aplicativo, ele só poderá ser restaurado se o Ambiente do Serviço de Aplicativo correspondente ainda existir.
>

A referência completa do commandlet está aqui: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
