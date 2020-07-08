---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204477"
---
- Os instantâneos incrementais atualmente não podem ser movidos entre assinaturas.
- No momento, você pode gerar apenas URIs SAS de até cinco instantâneos de uma família de instantâneos específica em um determinado momento.
- Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.
- Você não pode obter as alterações entre os instantâneos feitos antes e depois da alteração no tamanho do disco pai entre 4 TB de limite. Você precisa baixar novamente a cópia completa do instantâneo criado após o redimensionamento. Subsequentemente, você pode obter as alterações entre os instantâneos criados após o redimensionamento entre 4 TB de limite. 
