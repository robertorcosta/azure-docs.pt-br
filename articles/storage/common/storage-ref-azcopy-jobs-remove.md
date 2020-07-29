---
title: remover trabalhos do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9f544e479dd21b4c1f7f2e289e91b506aab7eff2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220045"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Remova todos os arquivos associados à ID de trabalho fornecida.

> [!NOTE] 
> Você pode personalizar o local onde os arquivos de log e de plano são salvos. Consulte o comando [azcopy env](storage-ref-azcopy-env.md) para saber mais.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opções

**-h,--ajuda**                Ajuda para remover.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps UInt32**      Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto")

**--a cadeia de caracteres Trusted-Microsoft-suffixs** especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.

## <a name="see-also"></a>Consulte também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
