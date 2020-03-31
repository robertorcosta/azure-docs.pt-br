---
title: Como usar o armazenamento persistente no Azure Spring Cloud | Microsoft Docs
description: Como usar o armazenamento persistente no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278528"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Usar armazenamento persistente no Azure Spring Cloud

O Azure Spring Cloud fornece dois tipos de armazenamento para sua aplicação: persistente e temporário.

Por padrão, o Azure Spring Cloud fornece armazenamento temporário para cada instância de aplicativo. O armazenamento temporário é limitado a 5 GB por instância com o caminho de montagem padrão /tmp.

> [!WARNING]
> Se você reiniciar uma instância de aplicativo, o armazenamento temporário associado será excluído permanentemente.

O armazenamento persistente é um contêiner de compartilhamento de arquivos gerenciado pelo Azure e alocado por aplicativo. Os dados armazenados no armazenamento persistente são compartilhados por todas as instâncias de um aplicativo. Uma instância do Azure Spring Cloud pode ter um máximo de 10 aplicativos com armazenamento persistente ativado. Cada aplicativo é alocado 50 GB de armazenamento persistente. O caminho de montagem padrão para armazenamento persistente é /persistente.

> [!WARNING]
> Se você desativar o armazenamento persistente de um aplicativo, todo esse armazenamento será desalocado e todos os dados armazenados serão perdidos.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Use o portal Azure para permitir o armazenamento persistente

1. Na **página inicial** do portal Azure, selecione **Todos os Recursos**.

    >![Localize o ícone Todos os Recursos](media/portal-all-resources.jpg)

1. Selecione o recurso Azure Spring Cloud que precisa de armazenamento persistente. Neste exemplo, a aplicação selecionada é chamada **upspring**.

    > ![Selecione seu aplicativo](media/select-service.jpg)

1. No título **Configurações,** selecione **Aplicativos**.

1. Seus serviços azure Spring Cloud aparecem em uma tabela.  Selecione o serviço ao qual deseja adicionar armazenamento persistente. Neste exemplo, o serviço **gateway** é selecionado.

    > ![Selecione o serviço](media/select-gateway.jpg)

1. Na página de configuração do serviço, selecione **Configuração**

1. Selecione a guia **Armazenamento persistente** e selecione **Habilitar**.

    > ![Habilite o armazenamento persistente](media/enable-persistent-storage.jpg)

Depois que o armazenamento persistente é ativado, seu tamanho e caminho são mostrados na página de configuração.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Use o Azure CLI para modificar o armazenamento persistente

Se necessário, instale a extensão Spring Cloud para o Azure CLI:

```azurecli
az extension add --name spring-cloud
```
Outras operações:

* Para criar um aplicativo com armazenamento persistente ativado:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para habilitar o armazenamento persistente para um aplicativo existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para desativar o armazenamento persistente em um aplicativo existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Se você desativar o armazenamento persistente de um aplicativo, todo esse armazenamento será desalocado e todos os dados armazenados serão perdidos permanentemente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [cotas de aplicativos e serviços.](spring-cloud-quotas.md)
* Aprenda a [dimensionar manualmente sua aplicação](spring-cloud-tutorial-scale-manual.md).
