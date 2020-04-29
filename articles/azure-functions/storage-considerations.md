---
title: Considerações de armazenamento para Azure Functions
description: Saiba mais sobre os requisitos de armazenamento do Azure Functions e sobre a criptografia de dados armazenados.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984156"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerações de armazenamento para Azure Functions

Azure Functions requer uma conta de armazenamento do Azure quando você cria uma instância do aplicativo de funções. Os seguintes serviços de armazenamento podem ser usados pelo seu aplicativo de funções:


|Serviço de armazenamento  | Uso de funções  |
|---------|---------|
| [Armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md)     | Mantenha o estado de associações e as teclas de função.  <br/>Também usado por [hubs de tarefas no Durable Functions](durable/durable-functions-task-hubs.md). |
| [Arquivos do Azure](../storage/files/storage-files-introduction.md)  | Compartilhamento de arquivos usado para armazenar e executar o código do aplicativo de funções em um [plano de consumo](functions-scale.md#consumption-plan). |
| [Armazenamento de filas do Azure](../storage/queues/storage-queues-introduction.md)     | Usado pelos [hubs de tarefas no Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Armazenamento de tabelas do Azure](../storage/tables/table-storage-overview.md)  |  Usado pelos [hubs de tarefas no Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar um aplicativo de funções, você deve criar ou vincular a uma conta de armazenamento do Azure de uso geral que dá suporte ao armazenamento de BLOBs, filas e tabelas. Isso ocorre porque as funções se baseiam no armazenamento do Azure para operações como gerenciar gatilhos e registrar execuções de função. Algumas contas de armazenamento não dão suporte a filas e tabelas. Essas contas incluem contas de armazenamento somente BLOB, armazenamento Premium do Azure e contas de armazenamento de uso geral com replicação ZRS. Essas contas sem suporte são filtradas da folha da conta de armazenamento ao criar um aplicativo de funções.

Para saber mais sobre os tipos de conta de armazenamento, consulte [apresentando os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#core-storage-services). 

Embora seja possível usar uma conta de armazenamento existente com seu aplicativo de funções, você deve verificar se ela atende a esses requisitos. As contas de armazenamento criadas como parte do fluxo de criação do aplicativo de funções têm a garantia de atender a esses requisitos de conta de armazenamento.  

## <a name="storage-account-guidance"></a>Diretrizes da conta de armazenamento

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, seu aplicativo de funções não será executado. Para solucionar problemas relacionados ao armazenamento, consulte [como solucionar problemas relacionados ao armazenamento](functions-recover-storage-account.md). As considerações adicionais a seguir se aplicam à conta de armazenamento usada pelos aplicativos de funções.

### <a name="storage-account-connection-setting"></a>Configuração de conexão da conta de armazenamento

A conexão da conta de armazenamento é mantida na [configuração do aplicativo AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

A cadeia de conexão da conta de armazenamento deve ser atualizada quando você regenera as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Contas de armazenamento compartilhado

É possível que vários aplicativos de funções compartilhem a mesma conta de armazenamento sem nenhum problema. Por exemplo, no Visual Studio, você pode desenvolver vários aplicativos usando o emulador de armazenamento do Azure. Nesse caso, o emulador age como uma única conta de armazenamento. A mesma conta de armazenamento usada por seu aplicativo de funções também pode ser usada para armazenar os dados do aplicativo. No entanto, essa abordagem nem sempre é uma boa ideia em um ambiente de produção.

### <a name="optimize-storage-performance"></a>Otimizar o desempenho do armazenamento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Criptografia de dados de armazenamento

O armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Para obter mais informações, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar para criptografia de dados de BLOB e arquivo. Essas chaves devem estar presentes no Azure Key Vault para que o Functions possa acessar a conta de armazenamento. Para saber mais, confira [Configurar chaves gerenciadas pelo cliente com Azure Key Vault usando o portal do Azure](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Compartilhamentos de arquivos de montagem (Linux)

Você pode montar compartilhamentos de arquivos existentes do Azure para seus aplicativos de funções do Linux. Ao montar um compartilhamento em seu aplicativo de funções do Linux, você pode aproveitar os modelos de aprendizado de máquina existentes ou outros dados em suas funções. Você pode usar o [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) comando para montar um compartilhamento existente para seu aplicativo de funções do Linux. 

Nesse comando, `share-name` é o nome do compartilhamento de arquivos existente do Azure e `custom-id` pode ser qualquer cadeia de caracteres que define exclusivamente o compartilhamento quando montado no aplicativo de funções. Além disso `mount-path` , é o caminho do qual o compartilhamento é acessado em seu aplicativo de funções. `mount-path`deve estar no formato `/dir-name`e não pode começar com. `/home`

Para obter um exemplo completo, consulte os scripts em [criar um aplicativo de funções Python e montar um compartilhamento de arquivos do Azure](scripts/functions-cli-mount-files-storage-linux.md). 

Atualmente, há suporte `storage-type` apenas `AzureFiles` para um de. Você só pode montar cinco compartilhamentos para um determinado aplicativo de funções. A montagem de um compartilhamento de arquivos pode aumentar a hora de início frio por pelo menos 200 300 MS ou ainda mais quando a conta de armazenamento está em uma região diferente.

O compartilhamento montado está disponível para seu código de função no `mount-path` especificado. Por exemplo, quando `mount-path` é `/path/to/mount`, você pode acessar o diretório de destino por APIs do sistema de arquivos, como no exemplo de Python a seguir:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as opções de Hospedagem de Azure Functions.

> [!div class="nextstepaction"]
> [Escala e hospedagem no Azure Functions](functions-scale.md)


