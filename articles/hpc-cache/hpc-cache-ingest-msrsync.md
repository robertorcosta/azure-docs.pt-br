---
title: Ingestão de dados do cache HPC do Azure-msrsync
description: Como usar o msrsync para mover dados para um destino de armazenamento de BLOBs no cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 02933ab9eeb05dbaa65fdf0c66c4a7946c3b0de1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514804"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Ingestão de dados do cache HPC do Azure – método msrsync

Este artigo fornece instruções detalhadas sobre como usar o ``msrsync`` Utilitário para copiar dados para um contêiner de armazenamento de BLOBs do Azure para uso com o cache HPC do Azure.

Para saber mais sobre como mover dados para o armazenamento de BLOBs para o cache do Azure HPC, leia [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

A ``msrsync`` ferramenta pode ser usada para mover dados para um destino de armazenamento de back-end para o cache do HPC do Azure. Essa ferramenta é projetada para otimizar o uso de largura de banda, executando vários processos ``rsync`` paralelos. Está disponível no GitHub em https://github.com/jbd/msrsync.

``msrsync`` divide o diretório de origem em "buckets" separados e, em seguida, executa processos ``rsync`` individuais em cada bucket.

Testes preliminares usando uma VM de quatro núcleos mostraram a melhor eficiência ao usar 64 processos. Use a opção ``msrsync````-p`` para definir o número de processos como 64.

Observe que ``msrsync`` pode gravar de e para volumes locais. A origem e o destino devem estar acessíveis como montagens locais na estação de trabalho usada para emitir o comando.

Siga estas instruções para usar o ``msrsync`` para preencher o armazenamento de BLOBs do Azure com o cache do HPC do Azure:

1. Instalar ``msrsync`` e seus pré-requisitos ( ``rsync`` e Python 2,6 ou posterior)
1. Determine o número total de arquivos e diretórios a serem copiados.

   Por exemplo, use o utilitário ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponíveis por download <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Se não estiver usando ``prime.py`` o, você poderá calcular o número de itens com a ferramenta GNU da ``find`` seguinte maneira:

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

   Por exemplo, este comando foi projetado para mover arquivos 11.000 em 64 processos de/Test/Source-Repository para/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
