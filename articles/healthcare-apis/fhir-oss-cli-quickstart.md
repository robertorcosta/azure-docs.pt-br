---
title: 'CLI do Azure: Implantar o servidor FHIR de software livre para o Azure – API do Azure para Azure'
description: Este guia de início rápido explica como implantar o servido FHIR de software livre da Microsoft para o Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.custom: devx-track-azurecli
ms.openlocfilehash: dc65bedc8b008d9628d09ef3e4bfc7cc6ea12e95
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495243"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Início Rápido: Implantar o servidor FHIR de software livre usando a CLI do Azure

Neste guia de início rápido, você aprenderá a implantar um servidor FHIR de software livre&reg; no Azure usando a CLI do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Criar grupo de recursos

Escolha um nome para o grupo de recursos que conterá os recursos provisionados e crie-o:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Implantar modelo

O [repositório do GitHub](https://github.com/Microsoft/fhir-server) do servidor FHIR da Microsoft para Azure contém um modelo que implantará todos os recursos necessários. Implante-o com:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Verifique se o servidor FHIR está em execução

Obtenha uma declaração de capacidade do servidor FHIR com:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Levará um minuto para que o servidor responda pela primeira vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando este aplicativo, exclua o grupo de recursos por meio das seguintes etapas:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou o servidor FHIR de software livre da Microsoft para o Azure em sua assinatura. Para saber como acessar a API do FHIR usando o Postman, prossiga para o tutorial do Postman.
 
>[!div class="nextstepaction"]
>[Acessar a API do FHIR usando o Postman](access-fhir-postman-tutorial.md)
