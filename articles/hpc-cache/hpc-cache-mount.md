---
title: Monte um cache Azure HPC
description: Como conectar clientes a um serviço de cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458327"
---
# <a name="mount-the-azure-hpc-cache"></a>Montar o Azure HPC Cache

Depois que o cache é criado, os clientes NFS podem acessá-lo com um comando simples. `mount` O comando conecta um caminho específico de destino de armazenamento no Cache Azure HPC a um diretório local na máquina cliente.

O comando mount é composto por esses elementos:

* Um dos endereços de montagem do cache (listado na página de visão geral do cache)
* O caminho de namespace virtual que você definiu quando criou o destino de armazenamento
* O caminho local para usar no cliente
* Parâmetros de comando que otimizam o sucesso deste tipo de montagem NFS

A página **'Mount instructions'** para o seu cache coleta as informações e as opções recomendadas para você e cria um comando de montagem protótipo que você pode copiar. Leia [Use o utilitário de instruções de montagem](#use-the-mount-instructions-utility) para obter detalhes.

## <a name="prepare-clients"></a>Preparar clientes

Certifique-se de que seus clientes são capazes de montar o Cache Azure HPC seguindo as diretrizes nesta seção.

### <a name="provide-network-access"></a>Fornecer acesso à rede

As máquinas clientes devem ter acesso à rede à rede virtual e à sub-rede privada do cache.

Por exemplo, crie VMs clientes dentro da mesma rede virtual ou use um ponto final, gateway ou outra solução na rede virtual para acesso de fora. (Lembre-se de que nada além do cache em si deve ser hospedado dentro da sub-rede do cache.)

### <a name="install-utilities"></a>Instalar utilitários

Instale o software utilitário Linux apropriado para suportar o comando de montagem NFS:

* Para Red Hat Enterprise Linux ou SuSE:`sudo yum install -y nfs-utils`
* Para Ubuntu ou Debian:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Crie um caminho local

Crie um caminho de diretório local em cada cliente para se conectar ao cache. Crie um caminho para cada caminho de namespace que você deseja montar.

Exemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

A página [de instruções de montagem](#use-the-mount-instructions-utility) no portal Azure inclui um comando protótipo que você pode copiar.

Quando você conecta a máquina cliente ao cache, você associará esse caminho a um caminho de namespace virtual que representa uma exportação de destino de armazenamento. Crie diretórios para cada um dos caminhos de namespace virtuais que o cliente usará.

## <a name="use-the-mount-instructions-utility"></a>Use o utilitário instruções de montagem

Você pode usar a página **''Instruções de montagem'** no portal Azure para criar um comando de montagem copiado. Abra a página a partir da seção **Configurar** a exibição de cache no portal.

Antes de usar o comando em um cliente, certifique-se de que o cliente `mount` atenda aos pré-requisitos e tenha o software necessário para usar o comando NFS conforme descrito acima em [Prepare clientes](#prepare-clients).

![captura de tela de uma instância de cache Azure HPC no portal, com a página Configurar > montagem de instruções carregada](media/mount-instructions.png)

Siga este procedimento para criar o comando de montagem.

1. Personalize o **campo de caminho cliente.** Este campo dá um exemplo de comando que você pode usar para criar um caminho local no cliente. O cliente acessa o conteúdo do Cache Azure HPC localmente neste diretório.

   Clique no campo e edite o comando para conter o nome do diretório que você deseja. O nome aparece no final da seqüência após`sudo mkdir -p`

   ![captura de tela do campo de caminho do cliente com cursor posicionado no final](media/mount-edit-client.png)

   Depois de terminar de editar o campo, o comando mount na parte inferior da página é atualizado com o novo caminho do cliente.

1. Escolha o **endereço de montagem** cache da lista. Este menu lista todos os pontos de montagem do [cliente](#find-mount-command-components)do cache .

   Equilibre a carga do cliente em todos os endereços de montagem disponíveis para obter um melhor desempenho de cache.

   ![captura de tela do campo de endereço de montagem de cache, com seletor mostrando três endereços IP para escolher](media/mount-select-ip.png)

1. Escolha o **caminho de namespace virtual** a ser usado para o cliente. Esses caminhos se conectam às exportações no sistema de armazenamento back-end.

   ![captura de tela do campo caminhos namespace, com seletor aberto](media/mount-select-target.png)

   Você pode visualizar e alterar os caminhos de namespace virtuais na página do portal Desemetes. Leia [Adicionar metas de armazenamento](hpc-cache-add-storage.md) para ver como.

   Para saber mais sobre o recurso de namespace agregado do Azure HPC Cache, [leia'o namespace agregado](hpc-cache-namespace.md).

1. O campo **de comando Mount** na etapa três preenche automaticamente com um comando de montagem personalizado que usa o endereço de montagem, o caminho do namespace virtual e o caminho do cliente que você definiu nos campos anteriores.

   Clique no símbolo de cópia no lado direito do campo para copiá-lo automaticamente à sua área de transferência.

   ![captura de tela do campo caminhos namespace, com seletor aberto](media/mount-command-copy.png)

1. Use o comando de montagem copiado na máquina cliente para conectá-lo ao cache Azure HPC. Você pode emitir o comando diretamente da linha de comando do cliente ou incluir o comando mount em um script ou modelo de configuração do cliente.

## <a name="understand-mount-command-syntax"></a>Entenda a sintaxe de comando de montagem

O comando mount tem a seguinte forma:

> sudo montagem {*opções*} *cache_mount_address*:/*namespace_path* *local_path*

Exemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Após o sucesso deste comando, o conteúdo da ``hpccache`` exportação de armazenamento ficará visível no diretório do cliente.

### <a name="mount-command-options"></a>Opções de comando de montagem

Para uma montagem robusta do cliente, passe essas configurações e argumentos no comando mount:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Configurações recomendadas de comando de montagem | |
--- | ---
``hard`` | As montagens suaves do Cache Azure HPC estão associadas a falhas de aplicativos e possíveis perdas de dados.
``proto=tcp`` | Essa opção é compatível com o tratamento apropriado de erros de rede NFS.
``mountproto=tcp`` | Essa opção é compatível com o tratamento apropriado de erros de rede para operações de montagem.
``retry=<value>`` | Configure ``retry=30`` para evitar falhas transitórias de montagem. Recomenda-se um valor diferente em montagens em primeiro plano.

### <a name="find-mount-command-components"></a>Encontre componentes de comando de montagem

Se você quiser criar um comando de montagem sem usar a página **'Instruções de montagem',** você pode encontrar os endereços de montagem na página **'Visão geral'** do cache e os caminhos de namespace virtuais na página **destino Armazenamento.**

![captura de tela da página visão geral da instância de cache do Azure HPC, com uma caixa de destaque ao redor da lista de endereços de montagem no canto inferior direito](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede do cache. Em um grupo de recursos, esses NICs são listados com nomes terminando `-cluster-nic-` e um número. Não altere ou exclua essas interfaces, ou o cache ficará indisponível.

Os caminhos de namespace virtuais são mostrados na página de detalhes de cada destino de armazenamento. Clique em um nome de destino de armazenamento individual para ver seus detalhes, incluindo caminhos agregados de namespace associados a ele.

![captura de tela da página de detalhes de um alvo de armazenamento (cabeçalho "Atualizar alvo de armazenamento"). Há uma caixa de destaque em torno de uma entrada na coluna de caminho de namespace virtual da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Próximas etapas

* Para mover dados para os alvos de armazenamento do cache, leia ['Preencher o novo armazenamento Azure Blob '](hpc-cache-ingest.md).
