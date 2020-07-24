---
title: CLI do Azure pré-requisitos para o cache do Azure HPC
description: Etapas de instalação antes de usar CLI do Azure para criar ou modificar um cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096038"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurar CLI do Azure para o cache HPC do Azure

Siga estas etapas para preparar seu ambiente antes de usar CLI do Azure para criar ou gerenciar um cache do HPC do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Instalar a CLI do Azure.

O cache HPC do Azure requer a versão 2,7 ou posterior do CLI do Azure. Execute `az --version` para localizar a versão e as bibliotecas dependentes que estão instaladas. Para instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Instalar a extensão de cache do HPC do Azure

As funções de cache do HPC do Azure não fazem parte da base de código principal, portanto, você também deve instalar a referência de extensão. Siga as instruções abaixo.

1. Entrar

   Entre usando o comando [AZ login](/cli/azure/reference-index#az-login) se você estiver usando uma versão instalada localmente da CLI.

    ```azurecli
    az login
    ```

    Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

   > [!TIP]
   > Se você tiver várias assinaturas, precisará escolher uma. Selecione-o ao iniciar uma sessão de Cloud Shell no portal do Azure ou siga as instruções em introdução [ao CLI do Azure](/cli/azure/get-started-with-azure-cli#sign-in) para definir sua assinatura na linha de comando.

2. Instalar a extensão da CLI do Azure

   As funções de cache do HPC do Azure são fornecidas como uma extensão CLI do Azure-ela ainda não faz parte do pacote da CLI principal. Você precisa instalar a referência de extensão antes de poder usá-la.

   As extensões de CLI do Azure fornecem acesso a comandos experimentais e de pré-lançamento. Para saber mais sobre extensões, incluindo atualização e desinstalação, consulte [usar extensões com CLI do Azure](/cli/azure/azure-cli-extensions-overview).

   Instale a extensão do cache HPC do Azure executando o seguinte comando:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Definir grupo de recursos padrão (opcional)

A maioria dos comandos HPC-cache exige que você passe o grupo de recursos do cache. Você pode definir o grupo de recursos padrão usando [AZ configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Próximas etapas

Depois de instalar a extensão de CLI do Azure e fazer logon, você pode usar CLI do Azure para criar e gerenciar sistemas de cache HPC do Azure.

* [Criar um Azure HPC Cache](hpc-cache-create.md)
* [CLI do Azure a documentação do HPC-cache](/cli/azure/ext/hpc-cache/hpc-cache)
