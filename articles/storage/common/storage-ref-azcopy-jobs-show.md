---
title: azcopy trabalhos de exibição | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 06efebb1c92b03036f4ae0b904ebfcc4c0c0f8ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220007"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informações detalhadas para a ID de trabalho fornecida.

## <a name="synopsis"></a>Sinopse

Se apenas a ID do trabalho for fornecida sem um sinalizador, o resumo do progresso do trabalho será retornado.

A contagem de bytes e a porcentagem concluída que aparece quando você executa esse comando refletem somente os arquivos que são concluídos no trabalho. Eles não refletem os arquivos parcialmente concluídos.

Se o `with-status` sinalizador for definido, a lista de transferências no trabalho com o valor especificado será mostrada.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostra o conteúdo da ajuda para o comando show.|
|--com-cadeia de caracteres de status|Lista somente as transferências de trabalho com esse status, valores disponíveis: iniciado, êxito, falha|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|
|--Cadeia de caracteres de sufixos confiáveis da Microsoft   |Especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.|

## <a name="see-also"></a>Consulte também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
