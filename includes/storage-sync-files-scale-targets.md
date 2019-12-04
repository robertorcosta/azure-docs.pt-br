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
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795815"
---
| Grupos | Escolha o destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 20 serviços de sincronização de armazenamento | SIM |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 100 grupos de sincronização | SIM |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | SIM |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | SIM |
| Pontos de extremidade do servidor por grupo de sincronização | 50 pontos de extremidade de servidor | Não |
| Pontos de extremidade de servidor por servidor | 30 pontos de extremidade de servidor | SIM |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 100 milhões objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 5 milhões objetos | SIM |
| Tamanho máximo do descritor de segurança (diretórios e arquivos) do objeto | 64 KiB | SIM |
| Tamanho do arquivo | 100 GiB | Não |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | V9: com base no tamanho do cluster do sistema de arquivos (tamanho de cluster de sistema de arquivos duplo). Por exemplo, se o tamanho do cluster do sistema de arquivos for 4 KB, o tamanho mínimo do arquivo será 8 KB.<br> V8 e mais antigo: 64 KiB  | SIM |

> [!Note]  
> Um ponto de extremidade Sincronização de Arquivos do Azure pode ser escalado verticalmente para o tamanho de um compartilhamento de arquivos do Azure. Se o limite de tamanho do compartilhamento de arquivos do Azure for atingido, a sincronização não será capaz de operar.
