---
title: Como usar o armazenamento persistente no Azure Spring Cloud | Microsoft Docs
description: Como usar o armazenamento persistente no Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038445"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Como usar o armazenamento persistente no Azure Spring Cloud

O Azure Spring Cloud oferece dois tipos de armazenamento para seu aplicativo: persistente e temporário.  O Azure Spring Cloud habilita o armazenamento temporário por padrão para cada instância do aplicativo. O armazenamento temporário está limitado a 5 GB e seu caminho de montagem padrão é `/tmp`.

> [!WARNING]
> A reinicialização de uma instância de aplicativo excluirá seu armazenamento temporário associado permanentemente.

O armazenamento persistente é um contêiner de compartilhamento de arquivo gerenciado pelo Azure alocado em um escopo por aplicativo. Os dados armazenados no armazenamento persistente são compartilhados entre todas as instâncias do aplicativo. Uma instância do serviço do Azure Spring Cloud pode ter, no máximo, 10 aplicativos com disco persistente habilitado e cada aplicativo recebe 50 GB de armazenamento persistente. O caminho de montagem padrão para o armazenamento persistente é `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Habilitar armazenamento persistente usando o portal do Azure

1. Na página de serviço do Azure Spring Cloud, selecione o **Painel do aplicativo** e selecione o aplicativo que requer armazenamento persistente.
1. Na guia **Visão geral**, localize o atributo **Armazenamento Persistente** e selecione **Desabilitado**.
1. Clique em **Habilitar** para habilitar o armazenamento persistente e selecione o botão **OK** para aplicar a alteração.

Quando o armazenamento persistente for habilitado, seu tamanho e caminho serão mostrados no atributo **Armazenamento Persistente** da página **Visão geral**.

> [!WARNING]
> *Desabilitar* o armazenamento persistente desalocará o armazenamento desse aplicativo.  Todos os dados nessa conta de armazenamento serão perdidos. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Habilitar armazenamento persistente usando a CLI do Azure

Crie um aplicativo com disco persistente habilitado:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Habilite o armazenamento persistente em um aplicativo existente:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Desabilite o armazenamento persistente em um aplicativo existente:

> [!WARNING]
> Desabilitar o armazenamento persistente desalocará o armazenamento desse aplicativo, perdendo permanentemente os dados que foram perdidos lá. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [cotas de aplicativo e de serviço](spring-cloud-quotas.md) ou como [dimensionar manualmente seu aplicativo](spring-cloud-tutorial-scale-manual.md).