---
title: Azure HPC Cache data ingest - msrsync
description: Como usar o msrsync para mover dados para um alvo de armazenamento Blob no Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168431"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC Cache data ingest - msrsync method

Este artigo fornece instruções ``msrsync`` detalhadas para usar o utilitário para copiar dados para um contêiner de armazenamento Azure Blob para uso com cache Azure HPC.

Para saber mais sobre como mover dados para o armazenamento Blob para o cache Do Azure HPC, leia [Mover dados para o armazenamento Azure Blob](hpc-cache-ingest.md).

A ``msrsync`` ferramenta pode ser usada para mover dados para um alvo de armazenamento back-end para o Cache Azure HPC. Essa ferramenta é projetada para otimizar o uso de largura de banda, executando vários processos ``rsync`` paralelos. Está disponível no GitHub em https://github.com/jbd/msrsync.

``msrsync`` divide o diretório de origem em "buckets" separados e, em seguida, executa processos ``rsync`` individuais em cada bucket.

Testes preliminares usando uma VM de quatro núcleos mostraram a melhor eficiência ao usar 64 processos. Use a opção ``msrsync````-p`` para definir o número de processos como 64.

Observe que ``msrsync`` pode gravar de e para volumes locais. A origem e o destino devem ser acessíveis como montagens locais na estação de trabalho usada para emitir o comando.

Siga estas ``msrsync`` instruções a serem usadas para preencher o armazenamento Do Azure Blob com o Cache Azure HPC:

1. Instalar ``msrsync`` e seus pré-requisitos (e``rsync`` Python 2.6 ou posterior)
1. Determine o número total de arquivos e diretórios a serem copiados.

   Por exemplo, use ``prime.py`` o ```prime.py --directory /path/to/some/directory``` utilitário com argumentos (disponível para download <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Se não ``prime.py``estiver usando, você pode calcular o ``find`` número de itens com a ferramenta GNU da seguinte forma:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Use esse número com a opção ``-f`` para definir o tamanho de buckets ao executar o comando.

1. Emita o ``msrsync`` comando para copiar arquivos:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi projetado para mover 11.000 arquivos em 64 processos de /test/source-repository para /mnt/hpccache/repositório:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
