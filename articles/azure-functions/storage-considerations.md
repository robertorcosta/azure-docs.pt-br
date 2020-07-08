---
title: Considerações de armazenamento do Azure Functions
description: Saiba mais sobre os requisitos de armazenamento do Azure Functions e sobre a criptografia de dados armazenados.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 324516240d09a5443908cbffec514e4caba2b604
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648771"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerações de armazenamento do Azure Functions

O Azure Functions requer uma conta de armazenamento do Azure quando você cria uma instância de aplicativo de funções. Os seguintes serviços de armazenamento podem ser usados pelo aplicativo de funções:


|Serviço de armazenamento  | Uso de funções  |
|---------|---------|
| [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)     | Mantenha o estado de associações e as chaves de função.  <br/>Também usado pelos [hubs de tarefas no Durable Functions](durable/durable-functions-task-hubs.md). |
| [Arquivos do Azure](../storage/files/storage-files-introduction.md)  | Compartilhamento de arquivos usado para armazenar e executar o código do aplicativo de funções em um [Plano de consumo](functions-scale.md#consumption-plan). |
| [Armazenamento de Filas do Azure](../storage/queues/storage-queues-introduction.md)     | Usado pelos [hubs de tarefas no Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Armazenamento de Tabelas do Azure](../storage/tables/table-storage-overview.md)  |  Usado pelos [hubs de tarefas no Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Quando você cria um aplicativo de funções, é necessário criar ou vincular uma conta de armazenamento do Azure de uso geral que oferece suporte ao armazenamento de Tabelas, Blobs e Filas. Isso ocorre porque o Functions usa o Armazenamento do Azure para operações como gerenciamento de gatilhos e log de execuções de função. Algumas contas de armazenamento não oferecem suporte a filas e tabelas. Essas contas incluem contas de armazenamento somente de blob, Armazenamento Premium do Azure e contas de armazenamento de uso geral com replicação ZRS. Essas contas incompatíveis são filtradas na folha Conta de Armazenamento durante a criação de um aplicativo de funções.

Para saber mais sobre tipos de conta de armazenamento, confira [Introdução aos serviços de Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md#core-storage-services). 

Embora seja possível usar uma conta de armazenamento existente com o aplicativo de funções, verifique se ela atende a esses requisitos. As contas de armazenamento criadas como parte do fluxo de criação do aplicativo de funções têm a garantia de atender a esses requisitos de conta de armazenamento.  

## <a name="storage-account-guidance"></a>Orientações sobre a Conta de armazenamento

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, o aplicativo de funções não será executado. Para solucionar problemas relacionados ao armazenamento, consulte [Como solucionar problemas relacionados ao armazenamento](functions-recover-storage-account.md). As considerações adicionais a seguir se aplicam à Conta de armazenamento usada pelos aplicativos de funções.

### <a name="storage-account-connection-setting"></a>Configuração da conta de armazenamento

A conexão da conta de armazenamento é mantida na [configuração de aplicativo AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

As cadeias de conexão da conta de armazenamento deverão ser atualizadas se você regenerar as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Contas de armazenamento compartilhadas

É possível que vários aplicativos de funções compartilhem a mesma conta de armazenamento sem nenhum problema. Por exemplo, no Visual Studio, você pode desenvolver vários aplicativos usando o Emulador de Armazenamento do Azure. Nesse caso, o emulador age como uma única conta de armazenamento. A mesma conta de armazenamento usada pelo aplicativo de funções também pode ser usada para armazenar os dados do aplicativo. No entanto, essa abordagem nem sempre é uma boa ideia em um ambiente de produção.

### <a name="optimize-storage-performance"></a>Otimizar o desempenho de armazenamento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Criptografia do armazenamento de dados

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>Compartilhamentos de arquivos de montagem (Linux)

Você pode montar compartilhamentos de arquivos existentes do Azure para os aplicativos de funções do Linux. Ao montar um compartilhamento em seu aplicativo de funções do Linux, você pode aproveitar os modelos de machine learning existentes ou outros dados em suas funções. Use o comando [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) para montar um compartilhamento existente para o aplicativo de funções do Linux. 

Nesse comando, `share-name` é o nome do compartilhamento de arquivos do Azure existente e `custom-id` pode ser qualquer cadeia de caracteres que defina exclusivamente o compartilhamento quando montado no aplicativo de funções. Além disso, `mount-path` é o caminho do qual o compartilhamento é acessado no aplicativo de funções. `mount-path` deve estar no formato `/dir-name` e não pode começar com `/home`.

Para obter um exemplo completo, consulte os scripts em [Criar um aplicativo de funções Python e montar um compartilhamento de arquivos do Azure](scripts/functions-cli-mount-files-storage-linux.md). 

No momento, apenas um `storage-type` de `AzureFiles` é compatível. Só é possível montar cinco compartilhamentos para um determinado aplicativo de funções. A montagem de um compartilhamento de arquivos pode aumentar o tempo de inicialização a frio em pelo menos 200-300ms ou até mais quando a conta de armazenamento está em uma região diferente.

O compartilhamento montado está disponível para o código de função no `mount-path` especificado. Por exemplo, quando `mount-path` é `/path/to/mount`, você pode acessar o diretório de destino por APIs do sistema de arquivos, como no exemplo de Python a seguir:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as opções de hospedagem do Azure Functions.

> [!div class="nextstepaction"]
> [Escala e hospedagem do Azure Functions](functions-scale.md)


