---
title: Use cache Azure HPC e arquivos do Azure NetApp
description: Como usar o Cache HPC do Azure para melhorar o acesso aos dados armazenados com arquivos do Azure NetApp
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238687"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Use cache Azure HPC com arquivos do Azure NetApp

Você pode usar [o Azure NetApp Files](https://azure.microsoft.com/services/netapp/) como um alvo de armazenamento para o cache Do Azure HPC. Este artigo explica como os dois serviços podem trabalhar juntos, e dá dicas para configurá-los.

O Azure NetApp Files combina seu sistema operacional ONTAP com a escalabilidade e velocidade do Microsoft Azure. Essa combinação permite que os usuários mudem fluxos de trabalho estabelecidos para a nuvem sem reescrever o código.

A adição de um componente azure HPC Cache pode melhorar o acesso ao arquivo apresentando vários volumes de arquivos do Azure NetApp em um namespace agregado. Ele pode fornecer cache de borda para volumes localizados em uma região de serviço diferente. Ele também pode melhorar o desempenho na demanda por volumes que foram criados em níveis de serviço de nível inferior para economizar custos.

## <a name="overview"></a>Visão geral

Para usar um sistema Azure NetApp Files como armazenamento back-end com cache Azure HPC, siga este processo.

1. Crie o sistema Azure NetApp Files e volumes de acordo com a orientação no [Plan your system, abaixo](#plan-your-azure-netapp-files-system).
1. Crie o Cache Azure HPC na região onde você precisa de acesso a arquivos. (Use as instruções em [Criar um cache Azure HPC](hpc-cache-create.md).)
1. [Defina os alvos de armazenamento](#create-storage-targets-in-the-cache) no cache que apontam para os volumes de arquivos do Azure NetApp. Crie um aplicativo de armazenamento de cache para cada endereço IP exclusivo usado para acessar os volumes.
1. Que os clientes [montem o cache Azure HPC](#mount-storage-targets) em vez de montar diretamente os volumes de arquivos do Azure NetApp.

## <a name="plan-your-azure-netapp-files-system"></a>Planeje seu sistema de arquivos Azure NetApp

Ao planejar seu sistema Azure NetApp Files, preste atenção aos itens nesta seção para ter certeza de que você pode integrá-lo suavemente com o Cache Azure HPC.

Leia também a [documentação do Azure NetApp Files](../azure-netapp-files/index.yml) antes de criar volumes para uso com o Cache Azure HPC.

### <a name="nfs-client-access-only"></a>Somente acesso ao cliente NFS

O Cache Azure HPC atualmente suporta apenas acesso NFS. Ele não pode ser usado com volumes de bits do modo SMB ACL ou POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Sub-rede exclusiva para Arquivos Azure NetApp

O Azure NetApp Files usa uma única sub-rede delegada para seus volumes. Nenhum outro recurso pode usar essa sub-rede. Além disso, apenas uma sub-rede em uma rede virtual pode ser usada para arquivos do Azure NetApp. Saiba mais em [Diretrizes para o planejamento da rede Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Tamanho da sub-rede delegada

Use o tamanho mínimo para a sub-rede delegada ao criar um sistema Azure NetApp Files para uso com cache Azure HPC.

O tamanho mínimo, especificado com a máscara de rede /28, fornece 16 endereços IP. Na prática, o Azure NetApp Files usa apenas três desses endereços IP disponíveis para acesso ao volume. Isso significa que você só precisa criar três alvos de armazenamento no cache Do Azure HPC para cobrir todos os volumes.

Se a sub-rede delegada for muito grande, é possível que os volumes de arquivos do Azure NetApp usem mais endereços IP do que uma única instância de cache Azure HPC pode lidar. Um único cache pode ter no máximo dez alvos de armazenamento.

O exemplo quickstart na documentação do Azure NetApp Files usa 10.7.0.0/16 para a sub-rede delegada, o que dá uma sub-rede muito grande.

### <a name="capacity-pool-service-level"></a>Nível de serviço do pool de capacidade

Ao escolher o nível de serviço para o seu pool de capacidade, considere seu fluxo de trabalho. Se você gravar dados frequentemente no volume De arquivos do Azure NetApp, o desempenho do cache pode ser restringido se o tempo de gravação for lento. Escolha um alto nível de serviço para volumes que tenham gravações frequentes.

Volumes com baixos níveis de serviço também podem mostrar algum atraso no início de uma tarefa enquanto o cache preenche o conteúdo. Depois que o cache estiver funcionando com um bom conjunto de arquivos de trabalho, o atraso deve se tornar imperceptível.

É importante planejar o nível de serviço do pool de capacidade com antecedência, porque ele não pode ser alterado após a criação. Um novo volume precisaria ser criado em um pool de capacidade diferente, e os dados copiados.

Observe que você pode alterar a cota de armazenamento de um volume e o tamanho do pool de capacidade sem interromper o acesso.

## <a name="create-storage-targets-in-the-cache"></a>Crie metas de armazenamento no cache

Depois que o sistema Azure NetApp Files for configurado e o Cache Azure HPC for criado, defina os alvos de armazenamento no cache que apontam para os volumes do sistema de arquivos.

Crie um alvo de armazenamento para cada endereço IP usado pelos volumes de arquivos do Azure NetApp. O endereço IP está listado na página instruções de montagem do volume.

Se vários volumes compartilharem o mesmo endereço IP, você poderá usar um alvo de armazenamento para todos eles.  

Siga as [instruções de montagem na documentação Arquivos do Azure NetApp](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para encontrar os endereços IP a serem usados.

Você também pode encontrar endereços IP com o Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Os nomes de exportação no sistema Azure NetApp Files têm um único componente de caminho. Não tente criar um alvo de ``/`` armazenamento para a exportação raiz no Azure NetApp Files, porque essa exportação não fornece acesso a arquivos.

Não há restrições especiais nos caminhos de namespace virtuais para esses alvos de armazenamento.

## <a name="mount-storage-targets"></a>Montar alvos de armazenamento

As máquinas clientes devem montar o cache em vez de montar diretamente os volumes de arquivos do Azure NetApp. Siga as instruções em [Mount the Azure HPC Cache](hpc-cache-mount.md).

## <a name="next-steps"></a>Próximas etapas

* Leia mais sobre como configurar e usar [arquivos do Azure NetApp](../azure-netapp-files/index.yml)
* Para ajudar a planejar e configurar seu sistema de cache Azure HPC para usar arquivos Do Azure NetApp, [contate suporte](hpc-cache-support-ticket.md).
