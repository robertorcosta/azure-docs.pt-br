---
title: bancada de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy banca.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 40ff6c6c76e255945681e678ef296ffcf9978f61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485183"
---
# <a name="azcopy-benchmark"></a>parâmetro de comparação azcopy

Executa um parâmetro de comparação de desempenho carregando dados de teste para um destino especificado. Os dados de teste são gerados automaticamente.

O comando de parâmetro de comparação executa o mesmo processo de upload como ' Copy ', exceto pelo seguinte:

  - Não há nenhum parâmetro de origem.  O comando requer apenas uma URL de destino. 
  
  - A carga é descrita por parâmetros de linha de comando, que controlam quantos arquivos são gerados automaticamente e seu tamanho. O processo de geração ocorre inteiramente na memória. O disco não é usado.
  
  - Há suporte apenas para alguns dos parâmetros opcionais disponíveis para o comando de cópia.
  
  - Diagnósticos adicionais são medidos e relatados.
  
  - Por padrão, os dados transferidos são excluídos no final da execução de teste.

O modo de parâmetro de comparação se ajustará automaticamente ao número de conexões TCP paralelas que fornece a taxa de transferência máxima. Ele exibirá esse número no final. Para evitar o ajuste automática, defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE como um número específico de conexões.

Todos os tipos de autenticação usuais têm suporte. No entanto, a abordagem mais conveniente para o benchmarking normalmente é criar um contêiner vazio com um token SAS e usar a autenticação SAS.

## <a name="examples"></a>Exemplos

```azcopy
azcopy benchmark [destination] [flags]
```

Execute um teste de benchmark com parâmetros padrão (adequado para redes de parâmetro de comparação de até 1 Gbps): '

- azcopy bancada "https://[conta]. blob. Core. Windows. net/[contêiner]? <SAS> "

Execute um teste de benchmark que carregue 100 arquivos, cada um com 2 GiB de tamanho: (adequado para benchmarking em uma rede rápida, por exemplo, 10 Gbps): '

- azcopy bancada "https://[conta]. blob. Core. Windows. net/[contêiner]? <SAS> " --contagem de arquivos 100--tamanho-por-arquivo 2G

Execute um teste de benchmark, mas use 50.000 arquivos, cada 8 MiB em tamanho e calcule seus hashes MD5 (da mesma forma que o `--put-md5` sinalizador faz isso no comando de cópia). A finalidade de `--put-md5` quando o benchmark é testar se a computação MD5 afeta a taxa de transferência para a contagem de arquivos e o tamanho selecionados:

- azcopy bancada "https://[conta]. blob. Core. Windows. net/[contêiner]? <SAS> " --contagem de arquivos 50000--tamanho-por-arquivo de 8 m--Put-MD5

## <a name="options"></a>Opções

**--blob-Type** cadeia de caracteres define o tipo de blob no destino. Usado para permitir o parâmetro de comparação de tipos diferentes de BLOB. Idêntico ao parâmetro de mesmo nome no comando de cópia (padrão "Detect").

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB). O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas-por exemplo, 0,25. Idêntico ao parâmetro de mesmo nome no comando de cópia.

**--delete-test-data**  Se for true, os dados de parâmetro de comparação serão excluídos no final da execução do parâmetro de comparação.  Defina-a como false se você quiser manter os dados no destino, por exemplo, para usá-los para testes manuais fora do modo de parâmetro de comparação (padrão true).

**--File-Count** uint o número de arquivos de dados gerados automaticamente a serem usados (padrão 100).

**-h,--ajuda**  Ajuda para o bancada

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: informações (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão "INFO")

**--Put-MD5**  Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do blob/arquivo de destino. (Por padrão, o hash não é criado.) Idêntico ao parâmetro de mesmo nome no comando de cópia.

--tamanho da cadeia de caracteres de **tamanho por arquivo** de cada arquivo de dados gerado automaticamente. Deve ser um número imediatamente seguido por K, M ou G. por exemplo, 12K ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps UInt32**  Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto").

**--a cadeia de caracteres Trusted-Microsoft-suffixs** especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
