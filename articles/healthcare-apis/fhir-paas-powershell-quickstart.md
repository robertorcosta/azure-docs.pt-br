---
title: 'Início Rápido: Implantar a API do Azure para FHIR usando o PowerShell'
description: Neste guia de início rápido, você aprenderá a implantar a API do Azure para FHIR usando o PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: matjazl
ms.openlocfilehash: 4b2772b449b3c398c8c8932db58b7078b7501824
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851963"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Início Rápido: Implantar a API do Azure para FHIR usando o PowerShell

Neste guia de início rápido, você aprenderá a implantar a API do Azure para FHIR usando o PowerShell.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Registrar o provedor de recursos da API do Azure para FHIR

Se o provedor de recursos das `Microsoft.HealthcareApis` ainda não estiver registrado para sua assinatura, você poderá registrá-lo com:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Criar um grupo de recursos do Azure

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Implantar a API do Azure para FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> Dependendo da versão do módulo `Az` do PowerShell que você instalou, o servidor FHIR provisionado pode ser configurado para usar [RBAC local](configure-local-rbac.md) e ter o usuário atualmente conectado do PowerShell na lista de IDs de objeto de identidade permitidos para o serviço FHIR implantado. No futuro, recomendamos que você [use o RBAC do Azure](configure-azure-rbac.md) para atribuir funções de plano de dados e talvez seja necessário excluir essa ID de objeto de usuários após a implantação para habilitar o modo RBAC do Azure.


## <a name="fetch-capability-statement"></a>Buscar instrução de funcionalidade

Você poderá validar que a conta da API do Azure para FHIR está em execução buscando uma instrução de funcionalidade FHIR:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando este aplicativo, exclua o grupo de recursos por meio das seguintes etapas:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou a API do Azure para FHIR em sua assinatura. Para definir configurações adicionais em sua API do Azure para FHIR, vá para o guia de instruções de configurações adicionais.

>[!div class="nextstepaction"]
>[Configurações adicionais na API do Azure para FHIR](azure-api-for-fhir-additional-settings.md)
