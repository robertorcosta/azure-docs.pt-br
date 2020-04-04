---
title: Monte um cache Azure HPC
description: Como conectar clientes a um serviço de cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657376"
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

Crie um caminho de diretório local em cada cliente para se conectar ao cache. Crie um caminho para cada destino de armazenamento que você deseja montar.

Exemplo: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Use o utilitário instruções de montagem

Abra a página **''Configurar instruções'** na seção **Configurar** a exibição de cache no portal Azure.

![captura de tela de uma instância de cache Azure HPC no portal, com a página Configurar > montagem de instruções carregada](media/mount-instructions.png)

A página de comando de montagem inclui informações sobre o processo de montagem do cliente e pré-requisitos, além de campos que você pode usar para criar um comando de montagem copiável.

Para usar esta página, siga este procedimento:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Revise os pré-requisitos do cliente e instale `mount` os utilitários necessários para usar o comando NFS conforme descrito acima em [Prepare clientes](#prepare-clients).

1. Passo um de **montagem do seu sistema de arquivos**<!-- label will change --> dá um comando de exemplo para criar o caminho local no cliente. Este é o caminho que o cliente usará para acessar o conteúdo do Cache Azure HPC.

   Observe o nome do caminho para que você possa modificá-lo no comando, se necessário.

1. Na segunda etapa, selecione um dos endereços IP disponíveis. Todos os pontos de montagem do [cliente](#find-mount-command-components) do cache estão listados aqui. Certifique-se de que você tem um sistema para equilibrar a carga entre todos os endereços IP.

1. O campo na etapa três preenche automaticamente com um comando de montagem protótipo. Clique no símbolo de cópia no lado direito do campo para copiá-lo automaticamente à sua área de transferência.

   > [!NOTE]
   > Verifique o comando de cópia antes de usá-lo. Você pode precisar personalizar o caminho de montagem do cliente e o caminho de namespace virtual de destino de armazenamento, que ainda não são selecionáveis nesta interface. Você também deve atualizar as opções de comando de montagem para refletir as [opções recomendadas](#mount-command-options) abaixo. Leia [Entenda a sintaxe de comando de montagem](#understand-mount-command-syntax) para obter ajuda.

1. Use o comando de montagem copiado (com edições, se necessário) na máquina cliente para conectá-lo ao alvo de armazenamento no Cache Azure HPC. Você pode emitir o comando diretamente da linha de comando do cliente ou incluir o comando mount em um script ou modelo de configuração do cliente.

## <a name="understand-mount-command-syntax"></a>Entenda a sintaxe de comando de montagem

O comando mount tem a seguinte forma:

> montagem sudo *cache_mount_address*:/*namespace_path* *local_path* {*opções*}

Exemplo:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
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

Se você quiser criar um comando de montagem sem usar a página **'Instruções de montagem',** você pode encontrar os endereços de montagem na página **'Visão geral'** do cache e os caminhos de namespace virtuais na página **Destinos de armazenamento.**

![captura de tela da página visão geral da instância de cache do Azure HPC, com uma caixa de destaque ao redor da lista de endereços de montagem no canto inferior direito](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede do cache. Em um grupo de recursos, esses NICs são listados com nomes terminando `-cluster-nic-` e um número. Não altere ou exclua essas interfaces, ou o cache ficará indisponível.

Os caminhos de namespace virtuais são mostrados na página **Dedestino alvos.** Clique em um nome de destino de armazenamento individual para ver seus detalhes, incluindo caminhos agregados de namespace associados a ele.

![captura de tela do painel de destino armazenamento do cache, com uma caixa de destaque em torno de uma entrada na coluna Caminho da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Próximas etapas

* Para mover dados para os alvos de armazenamento do cache, leia ['Preencher o novo armazenamento Azure Blob '](hpc-cache-ingest.md).
