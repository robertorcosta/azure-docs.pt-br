---
title: Criar um Azure HPC Cache
description: Como criar uma instância do Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582189"
---
# <a name="plan-the-aggregated-namespace"></a>Planejar o namespace agregado

O Azure HPC Cache permite que os clientes acessem uma variedade de sistemas de armazenamento através de um namespace virtual que oculta os detalhes do sistema de armazenamento back-end.

Quando você adiciona um alvo de armazenamento, você define o caminho do arquivo voltado para o cliente. As máquinas clientes montam esse caminho de arquivo e podem fazer solicitações de leitura de arquivos para o cache em vez de montar o sistema de armazenamento diretamente.

Como o Cache Azure HPC gerencia esse sistema de arquivos virtuais, você pode alterar o destino de armazenamento sem alterar o caminho voltado para o cliente. Por exemplo, você pode substituir um sistema de armazenamento de hardware por armazenamento em nuvem sem precisar reescrever procedimentos voltados para o cliente.

## <a name="aggregated-namespace-example"></a>Exemplo de namespace agregado

Planeje seu namespace agregado para que as máquinas clientes possam alcançar convenientemente as informações de que precisam e para que os administradores e engenheiros de fluxo de trabalho possam facilmente distinguir os caminhos.

Por exemplo, considere um sistema onde uma instância de cache Azure HPC está sendo usada para processar dados armazenados no Azure Blob. A análise requer arquivos de modelo que são armazenados em um datacenter no local.

Os dados do modelo são armazenados em um data center e as informações necessárias para este trabalho são armazenadas nesses subdiretórios:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

O sistema de armazenamento de data center expõe essas exportações:

    /
    /goldline
    /goldline/templates

Os dados a serem analisados foram copiados para um contêiner de armazenamento Azure Blob chamado "sourcecollection" usando o [utilitário CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir fácil acesso através do cache, considere criar alvos de armazenamento com esses caminhos de namespace virtuais:

| Sistema de armazenamento back-end <br/> (Caminho de arquivo NFS ou recipiente Blob) | Caminho de namespace virtual |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| fontecollection                        | /fonte/               |

Um destino de armazenamento NFS pode ter vários caminhos de namespace virtuais, desde que cada um consulte um caminho de exportação único.

Como os caminhos de origem do NFS são subdiretórios da mesma exportação, você precisará definir vários caminhos de namespace a partir do mesmo alvo de armazenamento.

| Nome de host de destino de armazenamento  | Caminho de exportação do NFS      | Caminho do subdiretório | Caminho do namespace    |
|--------------------------|----------------------|-------------------|-------------------|
| *Endereço IP ou nome de host* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Endereço IP ou nome de host* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Um aplicativo cliente pode montar o cache e acessar ``/source`` ``/templates/sku798``facilmente ``/templates/sku980``os caminhos agregados de arquivo namespace, e .

## <a name="next-steps"></a>Próximas etapas

Depois de decidir como configurar seu sistema de arquivos virtual, crie metas de armazenamento para mapear seu armazenamento back-end para os [caminhos](hpc-cache-add-storage.md) de arquivos virtuais voltados para o cliente.
