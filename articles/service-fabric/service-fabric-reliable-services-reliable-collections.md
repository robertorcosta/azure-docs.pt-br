---
title: Introdução às coleções confiáveis
description: Os serviços com monitoração de estado do Service Fabric fornecem coleções confiáveis que permitem escrever aplicativos em nuvem altamente disponíveis, escalonáveis e com baixa latência.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609716"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução à Reliable Collections nos serviços com monitoração de estado do Service Fabric do Azure

As Coleções Confiáveis permitem desenvolver aplicativos em nuvem altamente disponíveis, escalonáveis e de baixa latência como se você estivesse desenvolvendo aplicativos de computador único. As classes no namespace **Microsoft.ServiceFabric.Data.Collections** fornecem um conjunto de coleções que automaticamente tornam seu estado altamente disponível. Os desenvolvedores só precisam programar para as APIs de Coleções Confiáveis e permitir que as Coleções Confiáveis gerenciem o estado local e replicado.

A principal diferença entre Coleções Confiáveis e outras tecnologias de alta disponibilidade (como Redis, serviço Tabela do Azure e serviço Fila do Azure) é que o estado é mantido localmente na instância de serviço, além de se tornar altamente disponível. Isso significa que:

* Todas as leituras são locais, o que resulta em leituras de baixa latência e alta taxa de transferência.
* Todas as gravações geram o mínimo de operações de E/S de rede, o que resulta em gravações de baixa latência e alta taxa.

![Imagem da evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As Coleções Confiáveis podem ser interpretadas como a evolução natural das classes **System.Collections** : um novo conjunto de coleções projetadas para a nuvem e aplicativos com vários computadores sem aumentar a complexidade para desenvolvedores. Sendo assim, as Coleções confiáveis são:

* Replicadas: alterações de estado são replicadas para alta disponibilidade.
* Persistentes: os dados são persistidos no disco para durabilidade contra interrupções em larga escala (por exemplo, uma interrupção de energia de datacenter).
* Como as gravações são persistidas e replicadas, você não pode criar um ReliableDictionary volátil, um ReliableQueue ou outra coleção confiável que somente persista dados na memória.
* Assíncronas: as APIs são assíncronas para garantir que os threads não sejam bloqueados quando gerarem E/S.
* Transacionais: as APIs utilizam a abstração de transações para que você possa gerenciar facilmente várias Coleções Confiáveis dentro de um serviço.

As Coleções Confiáveis fornecem garantias de coerência forte prontas para uso para facilitar o raciocínio sobre o estado do aplicativo.
A coerência forte é obtida com a garantia de que as confirmações de transação só são concluídas depois que toda a transação tiver sido registrada em uma quorum de réplicas que seja a maioria, incluindo a primária.
Para obter consistência mais fraca, os aplicativos podem confirmar para o cliente/solicitante antes de a confirmação assíncrona retornar.

As APIs de Coleções Confiáveis são uma evolução das APIs de coleções simultâneas (encontradas no namespace **System.Collections.Concurrent** ):

* Assíncronas: retorna uma tarefa, já que, ao contrário das coleções concorrentes, as operações são replicadas e mantidas.
* Sem parâmetros de saída: usa `ConditionalValue<T>` para retornar um `bool` e um valor em vez de parâmetros de saída. `ConditionalValue<T>` é como `Nullable<T>`, mas não requer T para ser um struct.
* Transações: usam um objeto de transação para permitir que o usuário agrupe as ações em várias Coleções Confiáveis em uma transação.

Hoje, **Microsoft.ServiceFabric.Data.Collections** contém três coleções:

* [Dicionário Confiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa uma coleção de pares chave/valor replicada, transacional e assíncrona. Semelhante a **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
* [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma fila PEPS (primeiro a entrar, primeiro a sair) estrita, assíncrona, transacional e replicada. Semelhante a **ConcurrentQueue**, a chave pode ser de qualquer tipo.
* [Fila Simultânea Confiável](service-fabric-reliable-services-reliable-concurrent-queue.md): representa uma fila de ordenação de melhor esforço replicada, transacional e assíncrona para alta taxa de transferência. Semelhante à **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Próximos passos

* [Recomendações e Diretrizes de Coleções Confiáveis](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gerenciando dados
  * [Backup e restauração](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização de coleção confiável](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* Diversos
  * [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
