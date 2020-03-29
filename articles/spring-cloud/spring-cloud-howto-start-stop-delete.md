---
title: Inicie, pare e exclua seu aplicativo Azure Spring Cloud | Microsoft Docs
description: Como iniciar, parar e excluir seu aplicativo Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276833"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Inicie, pare e exclua seu aplicativo Azure Spring Cloud

Este guia explica como alterar o estado de um aplicativo no Azure Spring Cloud usando o portal Azure ou o Cli Do Zure.

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

Depois de implantar um aplicativo, você pode iniciar, parar e excluí-lo usando o portal Azure.

1. Acesse sua instância de serviço do Azure Spring Cloud no portal do Azure.
1. Selecione a guia **Painel do aplicativo**.
1. Selecione o aplicativo cujo estado você deseja alterar.
1. Na **página Visão geral** desse aplicativo, **selecione Iniciar/Parar,** **Reiniciar**ou **Excluir**.

## <a name="using-the-azure-cli"></a>Usando a CLI do Azure

> [!NOTE]
> Você pode usar parâmetros opcionais e configurar padrões com a CLI do Azure. Saiba mais sobre o Azure CLI lendo [nossa documentação de referência.](spring-cloud-cli-reference.md)  

Primeiro, instale a extensão Azure Spring Cloud para o Azure CLI da seguinte forma:

```azurecli
az extension add --name spring-cloud
```

Em seguida, selecione qualquer uma dessas operações de CLI do Azure:

* Para iniciar seu aplicativo:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para interromper seu aplicativo:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para reiniciar seu aplicativo:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para excluir seu aplicativo:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
