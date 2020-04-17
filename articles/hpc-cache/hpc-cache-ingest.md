---
title: Mova dados para um contêiner de nuvem do Azure HPC Cache
description: Como preencher o armazenamento Azure Blob para uso com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537926"
---
# <a name="move-data-to-azure-blob-storage"></a>Mova dados para o armazenamento Do Azure Blob

Se o seu fluxo de trabalho incluir a movimentação de dados para o armazenamento do Azure Blob, certifique-se de que você está usando uma estratégia eficiente. Você pode pré-carregar dados em um novo contêiner Blob antes de defini-los como um alvo de armazenamento ou adicionar o contêiner e, em seguida, copiar seus dados usando o Cache Azure HPC.

Este artigo explica as melhores maneiras de mover dados para o armazenamento Blob para uso com o Cache Azure HPC.

Tenha esses fatos em mente:

* O Azure HPC Cache usa um formato de armazenamento especializado para organizar dados no armazenamento Blob. É por isso que um alvo de armazenamento Blob deve ser um recipiente novo e vazio ou um contêiner Blob que foi usado anteriormente para dados do Cache Azure HPC.

* Copiar dados através do Cache Azure HPC para um destino de armazenamento back-end é mais eficiente quando você usa vários clientes e operações paralelas. Um simples comando de cópia de um cliente moverá dados lentamente.

Um utilitário baseado em Python está disponível para carregar conteúdo em um recipiente de armazenamento Blob. Leia [os dados de pré-carga no armazenamento Blob](#pre-load-data-in-blob-storage-with-clfsload) para saber mais.

Se você não quiser usar o utilitário de carregamento ou se quiser adicionar conteúdo a um alvo de armazenamento existente, siga as dicas paralelas de ingestão de dados em [Copiar dados através do Cache Azure HPC](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Dados de pré-carga no armazenamento Blob com CLFSLoad

Você pode usar o utilitário Avere CLFSLoad para copiar dados em um novo contêiner de armazenamento Blob antes de adicioná-los como um alvo de armazenamento. Este utilitário é executado em um único sistema Linux e grava dados no formato proprietário necessário para o Cache Azure HPC. CLFSLoad é a maneira mais eficiente de preencher um contêiner de armazenamento Blob para uso com o cache.

O utilitário Avere CLFSLoad está disponível por solicitação da sua equipe de cache Azure HPC. Peça o contato da sua equipe ou abra um [bilhete de suporte](hpc-cache-support-ticket.md) para solicitar ajuda.

Esta opção funciona apenas com recipientes novos e vazios. Crie o recipiente antes de usar Avere CLFSLoad.

Informações detalhadas estão incluídas na distribuição Avere CLFSLoad, que está disponível a pedido da equipe de cache do Azure HPC.

Uma visão geral do processo:

1. Prepare um sistema Linux (VM ou físico) com versão Python 3.6 ou posterior. Python 3.7 é recomendado para melhor desempenho.
1. Instale o software Avere-CLFSLoad no sistema Linux.
1. Execute a transferência da linha de comando Linux.

O utilitário Avere CLFSLoad precisa das seguintes informações:

* O ID da conta de armazenamento que contém o recipiente de armazenamento Blob
* O nome do recipiente de armazenamento Blob vazio
* Um token de assinatura de acesso compartilhado (SAS) que permite que o utilitário escreva para o contêiner
* Um caminho local para a fonte de dados - ou um diretório local que contém os dados a serem copiados, ou um caminho local para um sistema remoto montado com os dados

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copiar dados através do cache Azure HPC

Se você não quiser usar o utilitário Avere CLFSLoad ou se quiser adicionar uma grande quantidade de dados a um alvo de armazenamento Blob existente, você pode copiá-los através do cache. O Cache Azure HPC foi projetado para atender vários clientes simultaneamente, então, para copiar dados através do cache, você deve usar gravações paralelas de vários clientes.

![Diagrama mostrando a movimentação de dados de vários clientes com vários threads: na parte superior esquerda, um ícone para o armazenamento de hardware local tem várias setas vindo dele. As setas apontam para quatro computadores cliente. De cada máquina cliente, três setas apontam para o cache Azure HPC. A partir do Cache Azure HPC, várias setas apontam para o armazenamento Blob.](media/hpc-cache-parallel-ingest.png)

Os ``cp`` ``copy`` ou comandos que você normalmente usa para transferir dados de um sistema de armazenamento para outro são processos de um único segmento que copiam apenas um arquivo por vez. Isso significa que o servidor de arquivos está ingerindo apenas um arquivo por vez - o que é um desperdício dos recursos do cache.

Esta seção explica estratégias para criar um sistema de cópia de arquivos multi-cliente e multi-threaded para mover dados para o armazenamento Blob com o Cache Azure HPC. Ele explica os conceitos de transferência de arquivo e os pontos de decisão que podem ser usados para cópia de dados eficiente usando vários clientes e comandos de cópia simples.

Também explica alguns utilitários que podem ajudar. O utilitário ``msrsync`` pode ser usado para automatizar parcialmente o processo de dividir um conjunto de dados em buckets e usando comandos rsync. O script ``parallelcp`` é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente.

### <a name="strategic-planning"></a>Planejamento estratégico

Ao criar uma estratégia para copiar dados em paralelo, você deve compreender as vantagens e desvantagens de tamanho do arquivo, contagem de arquivos e profundidade de diretório.

* Quando os arquivos são pequenos, a métrica de interesse é arquivos por segundo.
* Quando os arquivos são grandes (10 MiBi ou mais), a métrica de interesse é bytes por segundo.

Cada processo de cópia tem uma taxa de transferência e uma taxa de arquivos transferidos, que podem ser medidas pelo tempo o comprimento do comando de cópia e fatorando o tamanho do arquivo e a contagem de arquivos. Explicar como medir as taxas está fora do escopo deste documento, mas é fundamental entender isso, esteja você lidando com arquivos grandes ou pequenos.

As estratégias para a ingestão de dados paralelos com o Cache Azure HPC incluem:

* Cópia manual - Você pode criar manualmente uma cópia multi-threaded em um cliente executando mais de um comando de cópia de uma vez no plano de fundo contra conjuntos predefinidos de arquivos ou caminhos. Leia [a ingestão de dados do Cache Azure HPC - método de cópia manual](hpc-cache-ingest-manual.md) para detalhes.

* Cópia parcialmente automatizada ``msrsync``  -  ``msrsync`` com é um utilitário ``rsync`` de invólucro que executa vários processos paralelos. Para obter detalhes, leia [a ingestão de dados do Cache Azure HPC - método msrsync](hpc-cache-ingest-msrsync.md).

* Cópia roteirizada com ``parallelcp`` - Aprenda a criar e executar um script de cópia paralela no [Azure HPC Cache data ingest - método de script de cópia paralela](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar seu armazenamento, saiba como os clientes podem montar o cache.

* [Acesse o sistema de cache Azure HPC](hpc-cache-mount.md)
