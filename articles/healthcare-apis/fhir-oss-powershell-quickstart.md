---
title: 'PowerShell: Implantar o servidor FHIR para Azure – API do Azure para FHIR'
description: Este guia de início rápido explica como implantar o servidor FHIR de software livre da Microsoft usando o PowerShell.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f959b884f67f354599b99bb6dd24918b28d13382
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "84819882"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Início Rápido: Implantar o servidor FHIR de software livre usando o PowerShell

Neste guia de início rápido, saiba como implantar o servidor FHIR da Microsoft de software livre para o Azure usando o PowerShell.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Escolha um nome para o grupo de recursos que conterá os recursos provisionados e crie-o:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Implantar o modelo de servidor FHIR

O [repositório do GitHub](https://github.com/Microsoft/fhir-server) do servidor FHIR da Microsoft para Azure contém um modelo que implantará todos os recursos necessários. Implante-o com:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Verifique se o servidor FHIR está em execução

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Levará um minuto para que o servidor responda pela primeira vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando este aplicativo, exclua o grupo de recursos por meio das seguintes etapas:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou o servidor FHIR de software livre da Microsoft para o Azure em sua assinatura. Para saber como acessar a API do FHIR usando o Postman, prossiga para o tutorial do Postman.
 
>[!div class="nextstepaction"]
>[Acessar a API do FHIR usando o Postman](access-fhir-postman-tutorial.md)
