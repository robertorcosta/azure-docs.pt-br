---
title: Configurar o namespace agregado do cache do HPC do Azure
description: Como criar caminhos voltados para o cliente para armazenamento de back-end com o cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: a39b6973767b72e69a82750ab8d0d487c733eda0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657101"
---
# <a name="set-up-the-aggregated-namespace"></a>Configurar o namespace agregado

Depois de criar destinos de armazenamento, você também deve criar caminhos de namespace para eles. Os computadores cliente usam esses caminhos virtuais para acessar arquivos por meio do cache em vez de conectarem-se diretamente ao armazenamento de back-end. Esse sistema permite que os administradores de cache alterem os sistemas de armazenamento back-end sem precisar reescrever as instruções do cliente.

Leia [planejar o namespace agregado](hpc-cache-namespace.md) para saber mais sobre esse recurso.

A página **namespace** na portal do Azure mostra os caminhos que os clientes usam para acessar seus dados por meio do cache. Use esta página para criar, remover ou alterar caminhos de namespace. Você também pode configurar caminhos de namespace usando o CLI do Azure.

Todos os caminhos voltados para o cliente existentes são listados na página **namespace** . Se um destino de armazenamento não tiver nenhum caminho, ele não aparecerá na tabela.

Você pode classificar as colunas da tabela clicando nas setas e entender melhor o namespace agregado do cache.

![captura de tela da página de namespace do portal com dois caminhos em uma tabela. Cabeçalhos de coluna: caminho de namespace, destino de armazenamento, caminho de exportação e subdiretório de exportação. Os itens na primeira coluna são links clicáveis. Botões principais: Adicionar caminho do namespace, atualizar, excluir](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Adicionar ou editar caminhos de namespace voltados para o cliente

Você deve criar pelo menos um caminho de namespace antes que os clientes possam acessar o destino de armazenamento. (Leia [montar o cache do HPC do Azure](hpc-cache-mount.md) para obter mais informações sobre o acesso do cliente.)

### <a name="blob-namespace-paths"></a>Caminhos de namespace de BLOB

Um destino de armazenamento de BLOBs do Azure pode ter apenas um caminho de namespace.

Siga as instruções abaixo para definir ou alterar o caminho com o portal do Azure ou CLI do Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, carregue a página Configurações de **namespace** . Você pode adicionar, alterar ou excluir caminhos de namespace desta página.

* **Adicionar um novo caminho:** Clique no botão **+ Adicionar** na parte superior e preencha as informações no painel de edição.

  * Selecione o destino de armazenamento na lista suspensa. (Nesta captura de tela, o destino de armazenamento de BLOBs não pode ser selecionado porque já tem um caminho de namespace.)

    ![Captura de tela do novo namespace editar campos com o seletor de destino de armazenamento exposto](media/namespace-select-storage-target.png)

  * Para um destino de armazenamento de BLOBs do Azure, os caminhos de exportação e subdiretório são definidos automaticamente como ``/`` .

* **Alterar um caminho existente:** Clique no caminho do namespace. O painel Editar é aberto e você pode modificar o caminho.

  ![Captura de tela da página de namespace depois de clicar em um caminho de namespace de blob-os campos de edição aparecem em um painel à direita](media/edit-namespace-blob.png)

* **Excluir um caminho de namespace:** Marque a caixa de seleção à esquerda do caminho e clique no botão **excluir** .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Ao usar o CLI do Azure, você deve adicionar um caminho de namespace ao criar o destino de armazenamento. Leia [Adicionar um novo destino de armazenamento de BLOBs do Azure](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) para obter detalhes.

Para atualizar o caminho do namespace do destino, use o comando [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) . Os argumentos para o comando Update são semelhantes aos argumentos no comando Create, exceto que você não passa o nome do contêiner ou a conta de armazenamento.

Não é possível excluir um caminho de namespace de um destino de armazenamento de blob com o CLI do Azure, mas você pode substituir o caminho por um valor diferente.

---

### <a name="nfs-namespace-paths"></a>Caminhos de namespace NFS

Um destino de armazenamento NFS pode ter vários caminhos virtuais, desde que cada caminho represente uma exportação ou subdiretório diferente no mesmo sistema de armazenamento.

Ao planejar seu namespace para um destino de armazenamento NFS, tenha em mente que cada caminho deve ser exclusivo e não pode ser um subdiretório de outro caminho de namespace. Por exemplo, se você tiver um caminho de namespace que é chamado ``/parent-a`` , também não poderá criar caminhos de namespace como ``/parent-a/user1`` e ``/parent-a/user2`` . Esses caminhos de diretório já estão acessíveis no namespace como subdiretórios do ``/parent-a`` .

Todos os caminhos de namespace para um sistema de armazenamento NFS são criados em um destino de armazenamento. A maioria das configurações de cache pode dar suporte a até dez caminhos de namespace por destino de armazenamento, mas configurações maiores podem dar suporte a até 20.

Esta lista mostra o número máximo de caminhos de namespace por configuração.

* Taxa de transferência de até 2 GB/s:

  * caminhos de namespace cache-10 de 3 TB
  * caminhos de namespace de 6 TB de cache-10
  * 23 TB de namespace de cache-20

* Taxa de transferência de até 5 GB/s:

  * caminhos de namespace de 6 TB de cache-10
  * 12 TB de namespace de cache-10
  * caminhos de namespace cache-20 de 24 TB

* Taxa de transferência de até 8 GB/s:

  * 12 TB de namespace de cache-10
  * caminhos de namespace de cache-10 de 24 TB
  * caminhos de namespace cache-20 de 48 TB

Para cada caminho de namespace NFS, forneça o caminho voltado para o cliente, a exportação do sistema de armazenamento e, opcionalmente, um subdiretório de exportação.

### <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, carregue a página Configurações de **namespace** . Você pode adicionar, editar ou excluir caminhos de namespace desta página.

* **Para adicionar um novo caminho:** Clique no botão **+ Adicionar** na parte superior e preencha as informações no painel de edição.
* **Para alterar um caminho existente:** Clique no caminho do namespace. O painel Editar é aberto e você pode modificar o caminho.
* **Para excluir um caminho de namespace:** Marque a caixa de seleção à esquerda do caminho e clique no botão **excluir** .

Preencha esses valores para cada caminho de namespace:

* **Caminho do namespace** -o caminho do arquivo voltado para o cliente.

* **Destino de armazenamento** -se estiver criando um novo caminho de namespace, selecione um destino de armazenamento no menu suspenso.

* **Caminho de exportação** -Insira o caminho para a exportação de NFS. Certifique-se de digitar o nome de exportação corretamente-o portal valida a sintaxe para esse campo, mas não verifica a exportação até que você envie a alteração.

* **Exportar subdiretório** – se você quiser que esse caminho monte um subdiretório específico da exportação, insira-o aqui. Caso contrário, deixe esse campo em branco.

![captura de tela da página namespace do portal com a página de atualização aberta à direita](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Ao usar o CLI do Azure, você deve adicionar pelo menos um caminho de namespace ao criar o destino de armazenamento. Leia [Adicionar um novo destino de armazenamento NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) para obter detalhes.

Para atualizar o caminho do namespace do destino ou para adicionar outros caminhos, use o comando [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Use a ``--junction`` opção para especificar todos os caminhos de namespace que você deseja.

As opções usadas para o comando Update são semelhantes ao comando "Create", exceto que você não passa as informações do sistema de armazenamento (endereço IP ou hostname) e o modelo de uso é opcional. Leia [Adicionar um novo destino de armazenamento NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) para obter mais detalhes sobre a sintaxe da ``--junction`` opção.

---

## <a name="next-steps"></a>Próximas etapas

Depois de criar o namespace agregado para seus destinos de armazenamento, você pode montar clientes no cache. Leia estes artigos para saber mais.

* [Montar o Azure HPC Cache](hpc-cache-mount.md)
* [Mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md)
