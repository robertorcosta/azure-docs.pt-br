---
title: Atualizar destinos de armazenamento do cache HPC do Azure
description: Como editar destinos de armazenamento do cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092449"
---
# <a name="edit-storage-targets"></a>Editar destinos de armazenamento

Você pode remover ou modificar um destino de armazenamento da página do portal de **destinos de armazenamento** do cache ou usando o CLI do Azure.

> [!TIP]
> O [vídeo Gerenciando o cache HPC do Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra como editar um destino de armazenamento no portal do Azure.

## <a name="remove-a-storage-target"></a>Remover um destino de armazenamento

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover um destino de armazenamento, selecione-o na lista e clique no botão **excluir** .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Use [AZ HPC-cache Storage-Target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) para excluir um destino de armazenamento do cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Essa ação remove a associação de destino de armazenamento com este sistema de cache HPC do Azure, mas não altera o sistema de armazenamento de back-end. Por exemplo, se você usou um contêiner de armazenamento de BLOBs do Azure, o contêiner e seu conteúdo ainda existem depois de você excluí-lo do cache. Você pode adicionar o contêiner a um cache do HPC do Azure diferente, adicioná-lo novamente a esse cache ou excluí-lo com o portal do Azure.

Todas as alterações de arquivo armazenadas no cache são gravadas no sistema de armazenamento de back-end antes de o destino de armazenamento ser removido. Esse processo pode levar uma hora ou mais se muitos dados alterados estiverem no cache.

## <a name="update-storage-targets"></a>Atualizar destinos de armazenamento

Você pode editar os destinos de armazenamento para modificar algumas de suas propriedades. Propriedades diferentes são editáveis para diferentes tipos de armazenamento:

* Para destinos de armazenamento de BLOBs, você pode alterar o caminho do namespace.

* Para destinos de armazenamento NFS, você pode alterar essas propriedades:

  * Caminho do namespace
  * Modelo de uso
  * Exportação
  * Exportar subdiretório

Você não pode editar o nome, tipo ou sistema de armazenamento de back-end de um destino de armazenamento (contêiner de BLOB ou endereço IP/nome de host do NFS). Se você precisar alterar essas propriedades, exclua o destino de armazenamento e crie uma substituição com o novo valor.

No portal do Azure, você pode ver quais campos são editáveis clicando no nome do destino de armazenamento e abrindo sua página de detalhes. Você também pode modificar os destinos de armazenamento com o CLI do Azure.

![captura de tela da página de edição de um destino de armazenamento NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Atualizar um destino de armazenamento NFS

Para um destino de armazenamento NFS, você pode atualizar várias propriedades. (Consulte a captura de tela acima para ver um exemplo de página de edição.)

* **Modelo de uso** -o modelo de uso influencia como o cache retém os dados. Leia [escolher um modelo de uso](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.
* **Caminho do namespace virtual** -o caminho que os clientes usam para montar esse destino de armazenamento. Leia [planejar o namespace agregado](hpc-cache-namespace.md) para obter detalhes.
* **Caminho de exportação de NFS** -a exportação do sistema de armazenamento a ser usada para este caminho de namespace.
* **Caminho de subdiretório** -o subdiretório (sob a exportação) a ser associado a este caminho de namespace. Deixe esse campo em branco se você não precisar especificar um subdiretório.

Cada caminho de namespace precisa de uma combinação exclusiva de exportação e subdiretório. Ou seja, você não pode fazer dois caminhos diferentes voltados para o cliente para exatamente o mesmo diretório no sistema de armazenamento de back-end.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Depois de fazer alterações, clique em **OK** para atualizar o destino de armazenamento ou clique em **Cancelar** para descartar as alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Use o comando [AZ NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) para alterar o modelo de uso, o caminho do namespace virtual e os valores de exportação ou subdiretório do NFS para um destino de armazenamento.

* Para alterar o modelo de uso, use a ``--nfs3-usage-model`` opção. Exemplo: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Para alterar o caminho do namespace, exportar ou exportar o subdiretório, use a ``--junction`` opção.

  O ``--junction`` parâmetro usa estes valores:

  * ``namespace-path``-O caminho do arquivo virtual voltado para o cliente
  * ``nfs-export``-A exportação do sistema de armazenamento para associar ao caminho voltado para o cliente
  * ``target-path``(opcional)-um subdiretório da exportação, se necessário

  Exemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

O nome do cache, o nome do destino de armazenamento e o grupo de recursos são necessários em todos os comandos de atualização.

Exemplo de comando: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Se o cache for interrompido ou não estiver em um estado íntegro, a atualização se aplicará depois que o cache estiver íntegro.

---

## <a name="update-an-azure-blob-storage-target"></a>Atualizar um destino de armazenamento de BLOBs do Azure

Para um destino de armazenamento de BLOBs, você pode modificar o caminho do namespace virtual.

### <a name="portal"></a>[Portal](#tab/azure-portal)

A página de detalhes de um destino de armazenamento de BLOBs permite modificar o caminho do namespace virtual.

![captura de tela da página de edição de um destino de armazenamento de BLOBs](media/hpc-cache-edit-storage-blob.png)

Quando terminar, clique em **OK** para atualizar o destino de armazenamento ou clique em **Cancelar** para descartar as alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Use [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) para atualizar o caminho do namespace de um destino.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Se o cache for interrompido ou não estiver em um estado íntegro, a atualização será aplicada depois que o cache estiver íntegro.

---

## <a name="next-steps"></a>Próximas etapas

* Leia [Adicionar destinos de armazenamento](hpc-cache-add-storage.md) para saber mais sobre essas opções.
* Leia [planejar o namespace agregado](hpc-cache-namespace.md) para obter mais dicas sobre como usar caminhos virtuais.
