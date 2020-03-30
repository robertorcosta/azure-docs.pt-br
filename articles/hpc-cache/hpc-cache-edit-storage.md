---
title: Atualize os alvos de armazenamento do Cache Azure HPC
description: Como editar os alvos de armazenamento do Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867000"
---
# <a name="edit-storage-targets"></a>Editar destinos de armazenamento

Você pode remover ou modificar um destino de armazenamento da página de **destinos de armazenamento** do cache.

## <a name="remove-a-storage-target"></a>Remova um alvo de armazenamento

Para remover um destino de armazenamento, selecione-o na lista e clique no botão **Excluir.**

Essa ação remove a associação de destino de armazenamento com este sistema de cache Azure HPC, mas não altera o sistema de armazenamento back-end. Por exemplo, se você usou um contêiner de armazenamento Azure Blob, o contêiner e seu conteúdo ainda existem depois de excluí-lo do cache. Você pode adicionar o contêiner a um cache Azure HPC diferente, adicioná-lo a este cache ou excluí-lo com o portal Azure.

Quaisquer alterações de arquivo armazenadas no cache são escritas no sistema de armazenamento back-end antes que o destino de armazenamento seja removido. Esse processo pode levar uma hora ou mais se um monte de dados alterados estiver no cache.

## <a name="update-storage-targets"></a>Atualizar metas de armazenamento

Você pode editar alvos de armazenamento para modificar algumas de suas propriedades. Diferentes propriedades são editáveis para diferentes tipos de armazenamento:

* Para alvos de armazenamento Blob, você pode alterar o caminho do namespace.

* Para metas de armazenamento NFS, você pode alterar essas propriedades:

  * Caminho do namespace
  * Modelo de uso
  * Exportação
  * Subdiretório de exportação

Você não pode editar o nome, o tipo ou o sistema de armazenamento back-end de um destino de armazenamento (contêiner Blob ou endereço IP do hostname/NFS). Se você precisar alterar essas propriedades, exclua o destino de armazenamento e crie uma substituição com o novo valor.

Para modificar um destino de armazenamento, clique no nome do destino de armazenamento para abrir sua página de detalhes. Alguns campos na página são editáveis.

![captura de tela da página de edição para um alvo de armazenamento NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Atualize um destino de armazenamento NFS

Para um destino de armazenamento NFS, você pode atualizar várias propriedades. (Consulte a captura de tela acima para uma página de edição por exemplo.)

* **Modelo de uso** - O modelo de uso influencia a forma como o cache retém dados. Leia [Escolha um modelo de uso](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.
* **Caminho de namespace virtual** - O caminho que os clientes usam para montar esse alvo de armazenamento. Leia [Planejar o espaço de nome agregado](hpc-cache-namespace.md) para obter detalhes.
* **Caminho de exportação NFS** - A exportação do sistema de armazenamento para usar para este caminho de namespace.
* **Caminho do subdiretório** - O subdiretório (sob a exportação) para associar-se a este caminho de namespace. Deixe este campo em branco se não precisar especificar um subdiretório.

Cada caminho namespace precisa de uma combinação única de exportação e subdiretório. Ou seja, você não pode fazer dois caminhos diferentes voltados para o cliente para o mesmo diretório no sistema de armazenamento back-end.

Depois de fazer alterações, clique em **OK** para atualizar o destino de armazenamento ou clique **em Cancelar** para descartar alterações.

## <a name="update-an-azure-blob-storage-target"></a>Atualize um alvo de armazenamento do Azure Blob

A página de detalhes de um destino de armazenamento Blob permite modificar o caminho do namespace virtual.

![captura de tela da página de edição para um alvo de armazenamento blob](media/hpc-cache-edit-storage-blob.png)

Quando terminar, clique em **OK** para atualizar o destino de armazenamento ou clique **em Cancelar** alterações.

## <a name="next-steps"></a>Próximas etapas

* Leia [Adicionar metas de armazenamento](hpc-cache-add-storage.md) para saber mais sobre essas opções.
* Leia [Planeje o namespace agregado](hpc-cache-namespace.md) para obter mais dicas sobre como usar caminhos virtuais.
