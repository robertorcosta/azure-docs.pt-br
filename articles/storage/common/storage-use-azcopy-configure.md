---
title: Configure, otimize e soluciona problemas do AzCopy com o Armazenamento Azure | Microsoft Docs
description: Configure, otimize e soluciona problemas do AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d2cb40d7510e46539db46bdb61ec2d64c0fd1ec7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526488"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas do AzCopy

O AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos para ou de uma conta de armazenamento. Este artigo ajuda você a executar tarefas avançadas de configuração e ajuda você a solucionar problemas que podem surgir à medida que você usa o AzCopy.

> [!NOTE]
> Se você está procurando conteúdo para ajudá-lo a começar com o AzCopy, consulte qualquer um dos seguintes artigos:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)
> - [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
> - [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Definir configurações de proxy

Para configurar as configurações de proxy para `https_proxy` AzCopy, defina a variável ambiente. Se você executar o AzCopy no Windows, o AzCopy detectará automaticamente as configurações do proxy, para que você não precise usar essa configuração no Windows. Se você optar por usar essa configuração no Windows, ela substituirá a detecção automática.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | Em um uso rápido de comando:`set https_proxy=<proxy IP>:<proxy port>`<br> No uso do PowerShell:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **Macos** | `export https_proxy=<proxy IP>:<proxy port>` |

Atualmente, o AzCopy não suporta proxies que requerem autenticação com NTLM ou Kerberos.

## <a name="optimize-performance"></a>Otimizar desempenho

Você pode fazer benchmark de desempenho e, em seguida, usar comandos e variáveis de ambiente para encontrar uma troca ideal entre desempenho e consumo de recursos.

Esta seção ajuda você a executar essas tarefas de otimização:

> [!div class="checklist"]
> * Executar testes de benchmark
> * Otimizar a taxa de transferência
> * Otimizar o uso da memória 
> * Otimizar a sincronização de arquivos

### <a name="run-benchmark-tests"></a>Executar testes de benchmark

Você pode executar um teste de benchmark de desempenho em contêineres blob específicos para visualizar estatísticas gerais de desempenho e gargalos de desempenho de identidade. 

> [!NOTE]
> Na versão atual, esse recurso está disponível apenas para recipientes Blob Storage.

Use o seguinte comando para executar um teste de benchmark de desempenho.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (''). Use aspas individuais em todos os shells de comando, exceto no Windows Command Shell (cmd.exe). Se você estiver usando um Shell de Comando do Windows (cmd.exe), encerre os argumentos de caminho com aspas duplas (""" em vez de aspas simples ('').

Este comando executa um benchmark de desempenho enviando dados de teste para um destino especificado. Os dados do teste são gerados na memória, carregados para o destino e excluídos do destino após a conclusão do teste. Você pode especificar quantos arquivos gerar e qual o tamanho que deseja que eles sejam usando parâmetros de comando opcionais.

Para obter documentos de referência detalhados, consulte [o banco de azcopy](storage-ref-azcopy-bench.md).

Para exibir orientações detalhadas `azcopy bench -h` de ajuda para este comando, digite e pressione a tecla ENTER.

### <a name="optimize-throughput"></a>Otimizar a taxa de transferência

Você pode `cap-mbps` usar a bandeira em seus comandos para colocar um teto na taxa de dados de throughput. Por exemplo, o comando a seguir retoma `10` um throughput de trabalho e caps para megabits (MB) por segundo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

O throughput pode diminuir ao transferir pequenos arquivos. Você pode aumentar o throughput `AZCOPY_CONCURRENCY_VALUE` definindo a variável de ambiente. Esta variável especifica o número de solicitações simultâneas que podem ocorrer.  

Se o computador tiver menos de 5 CPUs, então `32`o valor desta variável será definido como . Caso contrário, o valor padrão é igual a 16 multiplicado pelo número de CPUs. O valor máximo padrão `3000`desta variável é, mas você pode definir manualmente este valor mais ou menos. 

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Macos** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Use `azcopy env` o para verificar o valor atual desta variável. Se o valor estiver em branco, então você pode ler qual valor está sendo usado olhando para o início de qualquer arquivo de log do AzCopy. O valor selecionado, e a razão pela qual foi selecionado, são relatados lá.

Antes de definir esta variável, recomendamos que você execute um teste de benchmark. O processo de teste de benchmark informará o valor de concorrência recomendado. Alternativamente, se as condições de rede e cargas variarem, defina essa variável para a palavra `AUTO` em vez de para um número específico. Isso fará com que o AzCopy sempre execute o mesmo processo de ajuste automático que usa em testes de benchmark.

### <a name="optimize-memory-use"></a>Otimizar o uso da memória

Defina `AZCOPY_BUFFER_GB` a variável de ambiente para especificar a quantidade máxima de memória do sistema que você deseja que o AzCopy use ao baixar e carregar arquivos.
Expresse esse valor em gigabytes (GB).

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **Macos** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Otimizar a sincronização de arquivos

O comando [sync](storage-ref-azcopy-sync.md) identifica todos os arquivos no destino e, em seguida, compara nomes de arquivos e últimos carimbos de tempo modificados antes de iniciar a operação de sincronização. Se você tem um grande número de arquivos, então você pode melhorar o desempenho eliminando este processamento inicial. 

Para isso, use o comando [azcopy](storage-ref-azcopy-copy.md) copy `--overwrite` e `ifSourceNewer`defina a bandeira para . O AzCopy comparará os arquivos à medida que são copiados sem realizar quaisquer varreduras e comparações iniciais. Isso fornece uma vantagem de desempenho nos casos em que há um grande número de arquivos para comparar.

O comando [azcopy copy](storage-ref-azcopy-copy.md) não exclui arquivos do destino, então se você quiser excluir arquivos no destino quando eles não existirem `--delete-destination` mais na origem, use o comando [azcopy sync](storage-ref-azcopy-sync.md) com o sinalizador definido como um valor ou `true` `prompt`. 

## <a name="troubleshoot-issues"></a>Solucionar problemas

O AzCopy cria arquivos de log e plano para cada trabalho. Você pode usar os logs para investigar e solucionar problemas potenciais. 

Os registros conterão o status`UPLOADFAILED` `COPYFAILED`de `DOWNLOADFAILED`falha ( e ), o caminho completo e o motivo da falha.

Por padrão, os arquivos de log `%USERPROFILE%\.azcopy` e plan estão `$HOME$\.azcopy` localizados no diretório no Windows ou diretório no Mac e Linux, mas você pode alterar esse local se quiser.

O erro relevante não é necessariamente o primeiro erro que aparece no arquivo. Para erros como erros de rede, tempo outs e erros ocupados do servidor, o AzCopy tentará novamente até 20 vezes e geralmente o processo de repetição é bem sucedido.  O primeiro erro que você vê pode ser algo inofensivo que foi julgado com sucesso.  Então, em vez de olhar para o primeiro erro no `UPLOADFAILED` `COPYFAILED`arquivo, `DOWNLOADFAILED`procure os erros que estão próximos, ou . 

> [!IMPORTANT]
> Ao enviar uma solicitação ao Microsoft Support (ou solucionar problemas envolvendo terceiros), compartilhe a versão redigida do comando que deseja executar. Isso garante que o SAS não seja compartilhado acidentalmente com ninguém. Você pode encontrar a versão editada no início do arquivo de log.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O seguinte comando obterá `UPLOADFAILED` todos `04dc9ca9-158f-7945-5933-564021086c79` os erros com status do log:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Exibir e retomar trabalhos

Cada operação de transferência criará um trabalho do AzCopy. Use o seguinte comando para visualizar o histórico de trabalhos:

```
azcopy jobs list
```

Para exibir as estatísticas de trabalho, use o seguinte comando:

```
azcopy jobs show <job-id>
```

Para filtrar as transferências por status, use o seguinte comando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Use o comando a seguir para retomar um trabalho com falha/cancelamento. Este comando usa seu identificador junto com o token SAS, pois não é persistente por razões de segurança:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Envolver argumentos de caminho, como o token SAS com aspas simples (''). Use aspas individuais em todos os shells de comando, exceto no Windows Command Shell (cmd.exe). Se você estiver usando um Shell de Comando do Windows (cmd.exe), encerre os argumentos de caminho com aspas duplas (""" em vez de aspas simples ('').

Quando você retoma um trabalho, o AzCopy olha para o arquivo do plano de trabalho. O arquivo do plano lista todos os arquivos que foram identificados para processamento quando o trabalho foi criado pela primeira vez. Quando você retomar um trabalho, o AzCopy tentará transferir todos os arquivos que estão listados no arquivo do plano que ainda não foram transferidos.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Alterar a localização do plano e registrar arquivos

Por padrão, os arquivos de plano `%USERPROFILE%\.azcopy` e log estão localizados no diretório no Windows, ou no `$HOME$\.azcopy` diretório no Mac e Linux. Você pode mudar este local.

### <a name="change-the-location-of-plan-files"></a>Alterar a localização dos arquivos do plano

Use qualquer um desses comandos.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Macos** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Use `azcopy env` o para verificar o valor atual desta variável. Se o valor estiver em branco, os arquivos do plano serão gravados no local padrão.

### <a name="change-the-location-of-log-files"></a>Alterar a localização dos arquivos de log

Use qualquer um desses comandos.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **Macos** | `export AZCOPY_LOG_LOCATION=<value>` |

Use `azcopy env` o para verificar o valor atual desta variável. Se o valor estiver em branco, os logs serão gravados no local padrão.

## <a name="change-the-default-log-level"></a>Alterar o nível de log padrão

Por padrão, o nível de `INFO`log do AzCopy é definido como . Se você quiser reduzir a verbosidade de log para economizar espaço ``--log-level`` em disco, sobreescreva essa configuração usando a opção. 

Os níveis de `NONE` `DEBUG`registro `INFO` `WARNING`disponíveis `ERROR` `PANIC`são: `FATAL`, , , , , , e .

## <a name="remove-plan-and-log-files"></a>Remover arquivos de plano e registro

Se você quiser remover todos os arquivos de plano e registro `azcopy jobs clean` de sua máquina local para economizar espaço em disco, use o comando.

Para remover o plano e registrar arquivos `azcopy jobs rm <job-id>`associados a apenas um trabalho, use . Substitua `<job-id>` o espaço reservado neste exemplo pela carteira de trabalho.


