---
title: Monte um cache Azure HPC
description: Como conectar clientes a um serviço de cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582224"
---
# <a name="mount-the-azure-hpc-cache"></a>Montar o Azure HPC Cache

Depois que o cache é criado, os clientes NFS podem acessá-lo com um comando de montagem simples.

O comando de montagem é composto de dois elementos:

* Um dos endereços de montagem do cache (listado na página de visão geral do cache)
* O caminho de namespace virtual que você definiu quando criou o destino de armazenamento

![captura de tela da página visão geral da instância de cache do Azure HPC, com uma caixa de destaque ao redor da lista de endereços de montagem no canto inferior direito](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede do cache. Em um grupo de recursos, esses NICs são listados com nomes terminando `-cluster-nic-` e um número. Não altere ou exclua essas interfaces, ou o cache ficará indisponível.

Os caminhos de namespace virtuais são mostrados na página **Dedestino alvos.** Clique em um nome de destino de armazenamento individual para ver seus detalhes, incluindo caminhos agregados de namespace associados a ele.

![captura de tela do painel de destino armazenamento do cache, com uma caixa de destaque em torno de uma entrada na coluna Caminho da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Sintaxe de comando de montagem

Use um comando de montagem como o seguinte:

> montagem sudo *cache_mount_address*:/*namespace_path* *local_path* {*opções*}

Exemplo:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Após o sucesso deste comando, o conteúdo da ``hpccache`` exportação de armazenamento deve ser visível no diretório do cliente.

> [!NOTE] 
> Seus clientes devem ser capazes de acessar a rede virtual e a sub-rede que abriga seu cache. Por exemplo, crie VMs clientes dentro da mesma rede virtual ou use um ponto final, gateway ou outra solução na rede virtual para acesso de fora. Lembre-se que nada mais pode ser hospedado dentro da sub-rede do cache.

### <a name="mount-command-options"></a>Opções de comando de montagem

Para uma montagem robusta do cliente, passe essas configurações e argumentos no comando mount: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Configurações recomendadas de comando de montagem | |
--- | --- 
``hard`` | As montagens suaves do Cache Azure HPC estão associadas a falhas de aplicativos e possíveis perdas de dados. 
``proto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede NFS.
``mountproto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede para operações de montagem.
``retry=n`` | Configure ``retry=30`` para evitar falhas transitórias de montagem. Recomenda-se um valor diferente em montagens em primeiro plano.

## <a name="next-steps"></a>Próximas etapas

* Para mover dados para os alvos de armazenamento do cache, leia ['Preencher o novo armazenamento Azure Blob '](hpc-cache-ingest.md).
