---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331166"
---
| Recurso | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 100 Serviços de Sincronização de Armazenamento | Sim |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 200 grupos de sincronização | Sim |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | Sim |
| Pontos de extremidade na nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | Sim |
| Pontos de extremidade no servidor por grupo de sincronização | 100 pontos de extremidade de servidor | Sim |
| Pontos de extremidade de servidor por servidor | 30 pontos de extremidade de servidor | Sim |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 100 milhões de objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 5 milhões de objetos | Sim |
| Tamanho máximo do descritor de segurança (diretórios e arquivos) do objeto | 64 KiB | Sim |
| Tamanho do arquivo | 100 GiB | Não |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | V9 e mais recente: com base no tamanho do cluster do sistema de arquivos (tamanho duplo do cluster do sistema de arquivos). Por exemplo, caso o tamanho do cluster do sistema de arquivos seja 4 KB, o tamanho mínimo do arquivo será 8 KB.<br> V8 e uma versão mais antiga: 64 KiB  | Sim |

> [!Note]  
> Um ponto de extremidade de Sincronização de Arquivos do Azure poderá aumentar o tamanho de um compartilhamento de arquivo do Azure. Caso o limite de tamanho do compartilhamento de arquivo do Azure seja atingido, a sincronização não será capaz de funcionar.
