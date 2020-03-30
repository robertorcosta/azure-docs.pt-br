---
title: Considerações de armazenamento para funções do Azure
description: Saiba mais sobre os requisitos de armazenamento das funções do Azure e sobre como criptografar dados armazenados.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276576"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerações de armazenamento para funções do Azure

Funções do Azure exigem uma conta do Azure Storage quando você cria uma instância de aplicativo de função. Os seguintes serviços de armazenamento podem ser usados pelo seu aplicativo de função:


|Serviço de armazenamento  | Uso de funções  |
|---------|---------|
| [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)     | Mantenha as teclas de estado e função das ligações.  <br/>Também usado por [hubs de tarefas em Funções Duráveis](durable/durable-functions-task-hubs.md). |
| [Arquivos do Azure](../storage/files/storage-files-introduction.md)  | Compartilhamento de arquivos usado para armazenar e executar o código do aplicativo da função em um [Plano de Consumo](functions-scale.md#consumption-plan). |
| [Armazenamento de fila do Azure](../storage/queues/storage-queues-introduction.md)     | Usado por [hubs de tarefas em Funções Duráveis](durable/durable-functions-task-hubs.md).   |
| [Armazenamento de tabela sinuosa](../storage/tables/table-storage-overview.md)  |  Usado por [hubs de tarefas em Funções Duráveis](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar um aplicativo de função, você deve criar ou vincular a uma conta de armazenamento Azure de uso geral que suporte o armazenamento Blob, Fila e Tabela. Isso ocorre porque as funções dependem do Armazenamento Azure para operações como gerenciamento de gatilhos e execuções de funções de registro. Algumas contas de armazenamento não suportam filas e tabelas. Essas contas incluem contas de armazenamento somente blob, armazenamento Premium Azure e contas de armazenamento de uso geral com replicação ZRS. Essas contas sem suporte são filtradas da lâmina da conta de armazenamento ao criar um aplicativo de função.

Para saber mais sobre os tipos de contas de armazenamento, consulte [Introduzindo os Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services). 

Embora você possa usar uma conta de armazenamento existente com seu aplicativo de função, você deve ter certeza de que ele atende a esses requisitos. As contas de armazenamento criadas como parte do fluxo de criação de aplicativos de função são garantidas para atender a esses requisitos de conta de armazenamento.  

## <a name="storage-account-guidance"></a>Orientação da conta de armazenamento

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, seu aplicativo de função não será executado. Para solucionar problemas relacionados ao armazenamento, consulte [Como solucionar problemas relacionados ao armazenamento](functions-recover-storage-account.md). As seguintes considerações adicionais se aplicam à conta armazenamento usada por aplicativos de função.

### <a name="storage-account-connection-setting"></a>Configuração da conexão da conta de armazenamento

A conexão de conta de armazenamento é mantida na configuração do [aplicativo AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

A seqüência de conexão da conta de armazenamento deve ser atualizada quando você regenera as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Contas de armazenamento compartilhadas

É possível que vários aplicativos de função compartilhem a mesma conta de armazenamento sem problemas. Por exemplo, no Visual Studio você pode desenvolver vários aplicativos usando o Azure Storage Emulator. Neste caso, o emulador age como uma única conta de armazenamento. A mesma conta de armazenamento usada pelo seu aplicativo de função também pode ser usada para armazenar os dados do seu aplicativo. No entanto, essa abordagem nem sempre é uma boa ideia em um ambiente de produção.

### <a name="optimize-storage-performance"></a>Otimizar o desempenho do armazenamento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Criptografia de dados de armazenamento

O Azure Storage criptografa todos os dados em uma conta de armazenamento em repouso. Para obter mais informações, consulte [a criptografia de armazenamento do Azure para obter dados em repouso](../storage/common/storage-service-encryption.md).

Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para controle adicional sobre chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar para criptografia de dados de blob e arquivo. Essas chaves devem estar presentes no Azure Key Vault para funções para poder acessar a conta de armazenamento. Para saber mais, consulte [Configure chaves gerenciadas pelo cliente com o Azure Key Vault usando o portal Azure](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Montar compartilhamentos de arquivos (Linux)

Você pode montar os compartilhamentos existentes do Azure Files para seus aplicativos de função Linux. Ao montar um compartilhamento para o seu aplicativo de função Linux, você pode aproveitar modelos de aprendizado de máquina existentes ou outros dados em suas funções. Você pode [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) usar o comando para montar um compartilhamento existente no seu aplicativo de função Linux. 

Neste comando, `share-name` está o nome do compartilhamento de arquivos `custom-id` Azure existente, e pode ser qualquer string que define exclusivamente o compartilhamento quando montado no aplicativo de função. Além `mount-path` disso, é o caminho a partir do qual o compartilhamento é acessado em seu aplicativo de função. `mount-path`deve estar no `/dir-name`formato , e não `/home`pode começar com .

Para um exemplo completo, consulte os scripts em [Criar um aplicativo de função Python e montar um compartilhamento de arquivos Azure](scripts/functions-cli-mount-files-storage-linux.md). 

Atualmente, apenas `storage-type` `AzureFiles` um dos é suportado. Você só pode montar cinco compartilhamentos para um determinado aplicativo de função. A montagem de um compartilhamento de arquivos pode aumentar o tempo de início frio em pelo menos 200-300ms, ou até mais quando a conta de armazenamento estiver em uma região diferente.

O compartilhamento montado está disponível para `mount-path` o seu código de função no especificado. Por exemplo, `mount-path` `/path/to/mount`quando é , você pode acessar o diretório de destino por APIs do sistema de arquivos, como no exemplo seguinte Python:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as opções de hospedagem do Azure Functions.

> [!div class="nextstepaction"]
> [Escala e hospedagem no Azure Functions](functions-scale.md)


