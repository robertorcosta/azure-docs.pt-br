---
title: banco de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando de bancada azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518298"
---
# <a name="azcopy-bench"></a>azcopy bench

Executa um benchmark de desempenho enviando dados de teste para um destino especificado. Os dados do teste são gerados automaticamente.

O comando benchmark executa o mesmo processo de upload que 'copiar', exceto que:

  - Não há parâmetro de origem.  O comando requer apenas uma URL de destino. Na versão atual, esta URL de destino deve se referir a um contêiner blob.
  
  - A carga é descrita por parâmetros de linha de comando, que controlam quantos arquivos são gerados automaticamente e quão grandes eles são. O processo de geração ocorre inteiramente na memória. O disco não é usado.
  
  - Apenas alguns dos parâmetros opcionais disponíveis para o comando de cópia são suportados.
  
  - Diagnósticos adicionais são medidos e relatados.
  
  - Por padrão, os dados transferidos são excluídos no final da execução do teste.

O modo benchmark irá sintonizar-se automaticamente ao número de conexões TCP paralelas que dá o throughput máximo. Ele exibirá esse número no final. Para evitar ajuste automático, defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE para um número específico de conexões.

Todos os tipos de autenticação usuais são suportados. No entanto, a abordagem mais conveniente para benchmarking é normalmente criar um recipiente vazio com um token SAS e usar autenticação SAS.

## <a name="examples"></a>Exemplos

```azcopy
azcopy bench [destination] [flags]
```

Execute um teste de benchmark com parâmetros padrão (adequado para redes de benchmarking de até 1 Gbps):'

- banco azcopy "https://[account].blob.core.windows.net/[container]? <SAS>"

Execute um teste de benchmark que carrega 100 arquivos, cada 2 GiB em tamanho: (adequado para benchmarking em uma rede rápida, por exemplo, 10 Gbps):'

- banco azcopy "https://[account].blob.core.windows.net/[container]? <SAS>" --contagem de arquivos 100 --tamanho por arquivo 2G

Assim como acima, mas use 50.000 arquivos, cada um com 8 MiB de tamanho e calcule seus hashes MD5 (da mesma forma que o sinalizador --put-md5 faz isso no comando de cópia). O objetivo do --put-md5 quando o benchmarking é testar se a computação MD5 afeta o throughput para a contagem e o tamanho do arquivo selecionado:

- banco azcopy "https://[account].blob.core.windows.net/[container]? <SAS>" --contagem de arquivos 50000 --tamanho por arquivo 8M --put-md5

## <a name="options"></a>Opções

**--string tipo bolha** define o tipo de bolha no destino. Usado para permitir benchmarking diferentes tipos de bolhas. Idêntico ao parâmetro do mesmo nome no comando copy (padrão "Detectar").

**flutuador de tamanho de bloco-mb** Use este tamanho de bloco (especificado no MiB). O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas - por exemplo, 0,25. Idêntico ao parâmetro do mesmo nome no comando copy.

**--exclusão de dados de teste**  Se for verdade, os dados de benchmark serão excluídos no final da execução do benchmark.  Defina-os como falsos se você quiser manter os dados no destino - por exemplo, usá-los para testes manuais fora do modo de referência (padrão verdadeiro).

**--contagem de arquivos** uint O número de arquivos de dados gerados automaticamente para usar (padrão 100).

**-h, -- ajuda**  Ajuda para o banco

**--log-level** string Defina a verbosidade de log para o arquivo log, níveis disponíveis: INFO(todas as solicitações/respostas), AVISO (respostas lentas), ERRO (apenas solicitações com falha) e NONE (sem registros de saída). (padrão "INFO")

**--put-md5**  Crie um hash MD5 de cada arquivo e salve o hash como propriedade Content-MD5 do blob/file de destino. (Por padrão, o hash NÃO é criado.) Idêntico ao parâmetro do mesmo nome no comando copy.

**--tamanho por arquivo** string Tamanho de cada arquivo Tamanho de cada arquivo de dados gerado automaticamente. Deve ser um número imediatamente seguido por K, M ou G. E.g. 12k ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

**--cap-mbps uint32**  Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.

**--cadeia de** string tipo de saída Formato da saída do comando. As opções incluem: texto, json. O valor padrão é 'texto'. (padrão "texto").

## <a name="see-also"></a>Confira também

- [azcópia](storage-ref-azcopy.md)
