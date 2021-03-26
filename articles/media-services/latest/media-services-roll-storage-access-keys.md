---
title: Atualizar os serviços de mídia v3 após as chaves de acesso de armazenamento sem interrupção | Microsoft Docs
description: Este artigo fornece orientação sobre como atualizar os serviços de mídia v3 depois de reverter as chaves de acesso de armazenamento.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572319"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Atualizar os serviços de mídia v3 após a interrupção das chaves de acesso de armazenamento

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Você será solicitado a selecionar uma conta de armazenamento do Azure ao criar uma nova conta do AMS (serviços de mídia do Azure).  Você pode adicionar mais de uma conta de armazenamento à sua conta de serviços de mídia. Este artigo mostra como rotacionar chaves de armazenamento. Também mostra como adicionar contas de armazenamento a uma conta de mídia.

Para concluir as ações descritas neste artigo, você deve estar usando [Azure Resource Manager APIs](/rest/api/media/operations/azure-media-services-rest-api-reference) e o [PowerShell](/powershell/module/az.media).  Para obter mais informações, consulte [como gerenciar recursos do Azure com o PowerShell e o Gerenciador de recursos](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Geração de chave de acesso de armazenamento

Quando uma nova conta de armazenamento é criada, o Azure gera chaves de acesso de armazenamento de 2 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter suas conexões de armazenamento mais seguras, gere periodicamente e gire sua chave de acesso de armazenamento. Duas chaves de acesso (primária e secundária) são fornecidas para permitir que você mantenha conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. Esse procedimento também é chamado de "implantação de chaves de acesso".

Os Serviços de Mídia dependem de uma chave de armazenamento fornecida a eles. Especificamente, os localizadores que são usados para transmitir ou baixar os ativos dependem da chave de acesso de armazenamento especificada. Quando uma conta AMS é criada, ela assume uma dependência da chave de acesso de armazenamento primária por padrão. No entanto, como um usuário, você pode atualizar a chave de armazenamento que o AMS tem. Você deve permitir que os serviços de mídia saibam qual chave usar seguindo estas etapas:

>[!NOTE]
> Se tiver várias contas de armazenamento, você deverá executar esse procedimento com cada conta de armazenamento. A ordem de rotação das chaves de armazenamento não é fixa. Você pode revezar a primeira chave secundária e, em seguida, a chave primária, ou vice-versa.
>
> Antes de executar as etapas em uma conta de produção, certifique-se de testá-las em uma conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Etapas para revezar as chaves de armazenamento
 
 1. Altere a chave primária da conta de armazenamento por meio do cmdlet do PowerShell ou [do portal do Azure](https://portal.azure.com/) .
 2. Chame o `Sync-AzMediaServiceStorageKeys` cmdlet com os parâmetros apropriados para forçar a conta de mídia a escolher as chaves da conta de armazenamento
 
    O exemplo a seguir mostra como sincronizar chaves para contas de armazenamento.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Aguarde uma hora mais ou menos. Verifique se os cenários de transmissão estão funcionando.
 4. Altere a chave secundária da conta de armazenamento por meio do cmdlet do PowerShell ou portal do Azure.
 5. Chame `Sync-AzMediaServiceStorageKeys` o PowerShell com os parâmetros apropriados para forçar a conta de mídia a escolher novas chaves de conta de armazenamento.
 6. Aguarde uma hora mais ou menos. Verifique se os cenários de transmissão estão funcionando.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet do PowerShell

O exemplo a seguir demonstra como obter a conta de armazenamento e sincronizá-la com a conta do AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Etapas para adicionar contas de armazenamento à sua conta do AMS

O artigo a seguir mostra como adicionar contas de armazenamento à conta do AMS: [Anexar várias contas de armazenamento a uma conta de Serviços de Mídia](media-services-managing-multiple-storage-accounts.md).
