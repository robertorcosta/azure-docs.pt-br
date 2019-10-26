---
title: CLI do Azure Service Fabric - sfctl | Microsoft Docs
description: Descreve os comandos do sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 7e7fc7bbc65e92960d7839f6531ef1f7c1935ed3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900872"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar clusters e entidades do Service Fabric. Esta versão é compatível com o tempo de execução do Service Fabric 6,5.

Os comandos seguem o padrão de substantivo-verbo. Confira subgrupos para saber mais.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Criar, excluir e gerenciar aplicativos e tipos de aplicativo. |
| [chaos](service-fabric-sfctl-chaos.md) | Iniciar, parar e emitir relatórios sobre o serviço de teste de caos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selecionar, gerenciar e operar clusters do Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Criar, excluir e gerenciar aplicativos do Docker Compose. |
| [contêiner](service-fabric-sfctl-container.md) | Execute comandos relacionados ao contêiner em um nó de cluster. |
| [events](service-fabric-sfctl-events.md) | Recupere eventos do repositório de eventos (se o serviço EventStore já estiver instalado). |
| [is](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [malha](service-fabric-sfctl-mesh.md) | Excluir e gerenciar aplicativos de Malha do Service Fabric. |
| [node](service-fabric-sfctl-node.md) | Gerenciar os nós que formam um cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consultar e gerenciar partições para qualquer serviço. |
| [property](service-fabric-sfctl-property.md) | Propriedades de armazenamento e a consulta em nomes do Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Gerenciar as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço de gerenciador de reparo. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerencie clusters autônomos do Service Fabric. |
| [service](service-fabric-sfctl-service.md) | Crie, exclua e gerencie o serviço, os tipos de serviço e os pacotes de serviço. |
| [configurações](service-fabric-sfctl-settings.md) | Defina as configurações locais para essa instância do sfctl. |
| [store](service-fabric-sfctl-store.md) | Execute operações de arquivo de nível básico no repositório de imagens do cluster. |

## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).