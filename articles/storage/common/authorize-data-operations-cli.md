---
title: Autorizar o acesso a dados de blob ou fila com o Azure CLI
titleSuffix: Azure Storage
description: Especificar como autorizar operações de dados contra blob ou dados de fila com o Cli do Azure. Você pode autorizar operações de dados usando credenciais Azure AD, com a chave de acesso da conta ou com um token de assinatura de acesso compartilhado (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207687"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Autorizar o acesso a dados de blob ou fila com o Azure CLI

O Azure Storage fornece extensões para o Azure CLI que permitem especificar como você deseja autorizar operações em dados de blob ou fila. Você pode autorizar operações de dados das seguintes maneiras:

- Com um diretor de segurança do Azure Active Directory (Azure AD). A Microsoft recomenda o uso de credenciais Azure AD para maior segurança e facilidade de uso.
- Com a chave de acesso da conta ou um token de assinatura de acesso compartilhado (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Especificar como as operações de dados são autorizadas

Os comandos Azure CLI para leitura e escrita `--auth-mode` de blob e dados de fila incluem o parâmetro opcional. Especifique este parâmetro para indicar como uma operação de dados deve ser autorizada:

- Defina `--auth-mode` o `login` parâmetro para fazer login usando um princípio de segurança Azure AD (recomendado).
- Defina `--auth-mode` o parâmetro `key` para o valor legado para tentar recuperar a chave de acesso da conta a ser usada para autorização. Se você omitir o `--auth-mode` parâmetro, então o Azure CLI também tentará recuperar a chave de acesso.

Para usar `--auth-mode` o parâmetro, certifique-se de que você instalou a versão 2.0.46 do Azure CLI ou posterior. Execute `az --version` para verificar sua versão instalada.

> [!IMPORTANT]
> Se você omitir o `--auth-mode` parâmetro `key`ou configurá-lo para , então o Azure CLI tentará usar a chave de acesso da conta para autorização. Neste caso, a Microsoft recomenda que você forneça a chave de acesso no comando ou **na** AZURE_STORAGE_KEY variável de ambiente. Para obter mais informações sobre variáveis de ambiente, consulte a seção [definida variáveis de ambiente para parâmetros de autorização](#set-environment-variables-for-authorization-parameters).
>
> Se você não fornecer a chave de acesso, o Azure CLI tentará ligar para o provedor de recursos do Azure Storage para recuperá-la para cada operação. A realização de muitas operações de dados que requerem uma chamada para o provedor de recursos pode resultar em estrangulamento. Para obter mais informações sobre os limites do provedor de recursos, consulte [metas de escalabilidade e desempenho para o provedor de recursos Azure Storage](scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizar com credenciais AD do Azure

Quando você faz login no Azure CLI com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Esse token é usado automaticamente pelo Azure CLI para autorizar operações de dados subseqüentes contra o armazenamento Blob ou Queue. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

Você pode atribuir permissões para blob e dados de fila para um principal de segurança Azure AD através do RBAC (Role-Based Access Control). Para obter mais informações sobre as funções RBAC no Azure Storage, consulte [Gerenciar os direitos de acesso aos dados de armazenamento do Azure com o RBAC](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Permissões para chamadas de operações de dados

As extensões de armazenamento do Azure são suportadas para operações em dados de blob e fila. Quais operações você pode chamar depende das permissões concedidas ao diretor de segurança do Azure AD com o qual você faz login no Azure CLI. Permissões para contêineres ou filas de armazenamento do Azure são atribuídas via RBAC. Por exemplo, se você tiver a função **Blob Data Reader,** poderá executar comandos de scripting que lêem dados de um contêiner ou fila. Se você tiver a função **Blob Data Contributor,** poderá executar comandos de scripting que leiam, escrevam ou excluam um contêiner ou fila ou os dados que contêm.

Para obter detalhes sobre as permissões necessárias para cada operação de armazenamento do Azure em um contêiner ou fila, consulte [Operações de armazenamento de chamadas com tokens OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Exemplo: Autorizar uma operação para criar um contêiner com credenciais Azure AD

O exemplo a seguir mostra como criar um contêiner a partir do Azure CLI usando suas credenciais Azure AD. Para criar o contêiner, você precisará fazer login na CLI do Azure e precisará de um grupo de recursos e uma conta de armazenamento. Para saber como criar esses recursos, consulte [Quickstart: Crie, baixe e liste blobs com o Azure CLI](../blobs/storage-quickstart-blobs-cli.md).

1. Antes de criar o contêiner, atribua a função [de contribuinte de dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de armazenamento a si mesmo. Mesmo que você seja o proprietário da conta, você precisa de permissões explícitas para executar operações de dados contra a conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [O acesso de Grant ao Blob do Azure e os dados de fila com o RBAC no portal Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função RBAC podem levar alguns minutos para se propagar.

1. Ligue para o comando [az storage container create](/cli/azure/storage/container#az-storage-container-create) com o `--auth-mode` parâmetro definido para `login` criar o contêiner usando suas credenciais Azure AD. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorize com a chave de acesso à conta

Se você possui a chave da conta, você pode chamar qualquer operação de dados do Azure Storage. Em geral, usar a chave da conta é menos seguro. Se a chave da conta estiver comprometida, todos os dados da sua conta podem estar comprometidos.

O exemplo a seguir mostra como criar um contêiner usando a chave de acesso da conta. Especifique a chave `--auth-mode` da conta `key` e forneça o parâmetro com o valor:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizar com um token SAS

Se você possui um token SAS, você pode chamar operações de dados que são permitidas pelo SAS. O exemplo a seguir mostra como criar um contêiner usando um token SAS:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Definir variáveis de ambiente para parâmetros de autorização

Você pode especificar parâmetros de autorização em variáveis de ambiente para evitar incluí-los em cada chamada para uma operação de dados do Azure Storage. A tabela a seguir descreve as variáveis de ambiente disponíveis.

| Variável de ambiente                  | Descrição                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    O nome da conta de armazenamento. Esta variável deve ser usada em conjunto com a chave da conta de armazenamento ou um token SAS. Se nenhum dos dois estiver presente, o Azure CLI tentará recuperar a chave de acesso da conta de armazenamento usando a conta Azure AD autenticada. Se um grande número de comandos for executado de uma só vez, o limite de estrangulamento do provedor de recursos do Azure Storage pode ser atingido. Para obter mais informações sobre os limites do provedor de recursos, consulte [metas de escalabilidade e desempenho para o provedor de recursos Azure Storage](scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    A chave da conta de armazenamento. Esta variável deve ser usada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Uma seqüência de conexão que inclui a chave da conta de armazenamento ou um token SAS. Esta variável deve ser usada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Um token de assinatura de acesso compartilhado (SAS). Esta variável deve ser usada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    O modo de autorização com o qual executar o comando. Os valores `login` permitidos são `key`(recomendados) ou . Se você `login`especificar, o Azure CLI usa suas credenciais Azure AD para autorizar a operação de dados. Se você especificar o modo legado, `key` o Azure CLI tentará consultar a chave de acesso à conta e autorizar o comando com a chave.    |

## <a name="next-steps"></a>Próximas etapas

- [Use o Azure CLI para atribuir uma função RBAC para acesso a dados de blob e fila](storage-auth-aad-rbac-cli.md)
- [Autorize o acesso a dados de blob e fila com identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
