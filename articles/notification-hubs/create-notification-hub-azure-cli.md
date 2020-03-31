---
title: Início Rápido – Criar um hub de notificação do Azure usando a CLI do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a criar um hub de notificação do Azure usando a CLI do Azure.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069479"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Início Rápido: Criar um hub de notificação do Azure usando a CLI do Azure

Os Hubs de Notificação do Azure fornecem um mecanismo de push expansível e fácil de usar que permite que você envie notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu etc.) de qualquer back-end (nuvem ou local). Para obter mais informações sobre o serviço, confira [O que são os Hubs de Notificação do Azure?](notification-hubs-push-notification-overview.md).

Neste início rápido, você criará um hub de notificação usando a CLI do Azure. A primeira seção fornece as etapas para criar um namespace do hub de notificação e para consultar informações da política de acesso desse namespace. A segunda seção fornece as etapas para criar um hub de notificação em um namespace existente.  Além disso, você aprenderá a criar uma política de acesso personalizada.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os Hubs de Notificação exigem a versão 2.0.67 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão e as bibliotecas dependentes que estão instaladas. Para instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Entrar.

   Entre usando o comando [az login](/cli/azure/reference-index#az-login) se estiver usando uma instalação local da CLI.

    ```azurecli-interactive
    az login
    ```

    Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

2. Instale a extensão de CLI do Azure.

   Para executar os comandos da CLI do Azure para os hubs de notificação, instale a [extensão da CLI do Azure para os Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Crie um grupos de recursos.

   Os hubs de notificação do Azure, assim como todos os recursos do Azure, precisam ser implantados em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.

   Para este início rápido, crie um grupo de recursos chamado *spnhubrg* no local *eastus* com o seguinte comando [az group create](/cli/azure/group#az-group-create):

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Criar o namespace do hub de notificação

1. Criar um namespace para seus hubs de notificação

   Um namespace contém um ou mais hubs e o nome deve ser exclusivo em todas as assinaturas do Azure.  Para verificar a disponibilidade do namespace de serviço fornecido, use o comando [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).  Execute o comando [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) para criar um namespace.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Listar as chaves e as cadeias de conexão para a política de acesso do namespace.

   Uma política de acesso chamada **RootManageSharedAccessKey** é criada automaticamente para um novo namespace.  Cada política de acesso tem dois conjuntos de chaves e cadeias de conexão.  Para listar as chaves e as cadeias de conexão para o namespace, execute o comando [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Criar hubs de notificação

1. Criar seu primeiro hub de notificação.

   Agora, um hub de notificação pode ser criado no novo namespace.  Execute o comando [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) para criar um hub de notificação.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Criar um segundo hub de notificação.

   Vários hubs de notificação podem ser criados em um namespace.  Para criar um segundo hub de notificação no mesmo namespace, execute o comando `az notification-hub create` novamente usando outro nome de hub.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Trabalhar com políticas de acesso

1. Criar uma regra de autorização para um hub de notificação.

   Uma política de acesso é criada automaticamente para cada novo hub de notificação.  Para criar e personalizar sua própria política de acesso, use o comando [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create).

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Listar políticas de acesso para um hub de notificação.

   Para consultar quais políticas de acesso existem para um hub de notificação, use o comando [az  notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list).

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Não use a política **DefaultFullSharedAccessSignature** no aplicativo. Ela é destinada a ser usada apenas em seu back-end.  Use somente políticas de acesso do tipo **Listen** no aplicativo cliente.

3. Listar as chaves e as cadeias de conexão para uma política de acesso do hub de notificação

   Há dois conjuntos de chaves e cadeias de conexão para cada política de acesso.  Você precisará delas para manipular notificações por push mais tarde.  Para listar as chaves e as cadeias de conexão para uma política de acesso do hub de notificação, use o comando [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Um [namespace do hub de notificação](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e um [hub de notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) têm políticas de acesso separadas.  Verifique se você está usando a referência da CLI do Azure correta ao consultar as chaves e as cadeias de conexão.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, use o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Confira também

Descubra todos os recursos para gerenciar hubs de notificação com a CLI do Azure.

* [Lista de referência completa da CLI do Azure para os Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub)
* [Lista de referência da CLI do Azure para o namespace dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Lista de referência da CLI do Azure para regras de autorização dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Lista de referência da CLI do Azure para credenciais dos Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/credential)
