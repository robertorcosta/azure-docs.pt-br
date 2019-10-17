---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391541"
---
| Grupos | Escolha o destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 20 serviços de sincronização de armazenamento | SIM |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 100 grupos de sincronização | SIM |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | SIM |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | SIM |
| Pontos de extremidade do servidor por grupo de sincronização | 50 pontos de extremidade de servidor | Não |
| Pontos de extremidade de servidor por servidor | 30 pontos de extremidade de servidor | SIM |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 50 milhões objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 5 milhões objetos | SIM |
| Tamanho máximo do descritor de segurança (diretórios e arquivos) do objeto | 64 KiB | SIM |
| Tamanho do arquivo | 100 GiB | Não |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | 64 KiB | SIM |
| Sessões de sincronização simultâneas | Agente v4 e posterior: o limite varia de acordo com os recursos do sistema disponíveis. <BR> Agente V3: duas sessões de sincronização ativas por processador ou um máximo de oito sessões de sincronização ativas por servidor. | SIM

> [!Note]  
> Um ponto de extremidade Sincronização de Arquivos do Azure pode ser escalado verticalmente para o tamanho de um compartilhamento de arquivos do Azure. Se o limite de tamanho do compartilhamento de arquivos do Azure for atingido, a sincronização não será capaz de operar.
