---
title: Terminologia do Azure Data Share
description: Conheça os termos comuns usados para descrever os recursos usados no Azure Data Share (provedor de dados, consumidor de dados, compartilhamento de dados, assinatura de compartilhamento, snapshot, convite, destinatário.)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468545"
---
# <a name="azure-data-share-concepts"></a>Conceitos de compartilhamento de dados do Azure 

O Azure Data Share introduz uma nova terminologia relacionada ao compartilhamento de dados. Este artigo explica alguns termos frequentemente usados que você pode ver usados durante todo o serviço. 

## <a name="data-provider"></a>Provedor de dados

Um provedor de dados é a organização que está compartilhando dados com seus consumidores. Normalmente, o provedor de dados pode ser um proprietário ou um curador dos dados. Os provedores de dados querem compartilhar dados de vários tipos. Alguns exemplos de dados que um provedor de dados pode querer compartilhar incluem dados brutos, como dados de ponto de vendas ou séries tempométricas. Um provedor de dados também pode querer compartilhar dados pré-processados e curados que já contenham análises e insights. 

## <a name="data-consumer"></a>Consumidor de dados 

Um consumidor de dados é a organização que está recebendo dados de um provedor de dados. O consumidor de dados pode estar querendo juntar os dados compartilhados com seus próprios dados para obter insights. Em alguns casos, o consumidor de dados pode estar recebendo dados que já foram processados. 

## <a name="data-share"></a>Data Share

Um compartilhamento de dados é um grupo de conjuntos de dados que são compartilhados como uma única entidade. Os conjuntos de dados podem ser de várias fontes de dados do Azure que são suportadas pelo Azure Data Share. Atualmente, o Azure Data Share suporta o Azure Blob Storage e o Azure Data Lake Store. 

## <a name="share-subscription"></a>Compartilhar Assinatura 

Uma Assinatura de Compartilhamento é criada quando um consumidor de dados aceita um convite de compartilhamento de dados de um provedor de dados. Os provedores de dados podem visualizar assinaturas ativas de compartilhamento navegando para **ações enviadas** em sua conta Azure Data Share e selecionando Assinaturas de Compartilhamento de **Ações**.

Um consumidor de dados pode verificar se eles têm uma assinatura ativa de ações navegando para **ações recebidas** e visualizando o status de suas ações recebidas. 

## <a name="snapshot"></a>Instantâneo

Um instantâneo pode ser criado por um consumidor de dados quando eles aceitam um convite de compartilhamento de dados. Quando eles aceitam um convite, eles podem desencadear um instantâneo completo dos dados compartilhados com eles. O instantâneo é uma cópia dos dados no momento em que o consumidor de dados gerou o instantâneo. 

Existem dois tipos de instantâneos - completos e incrementais. Um instantâneo completo contém todos os dados dentro do compartilhamento de dados. Um instantâneo incremental contém todos os dados atualizados/adicionados desde que o último instantâneo foi acionado. 

## <a name="snapshot-settings-in-azure-data-share"></a>Configurações de instantâneo no Azure Data Share
 
Um provedor de dados pode habilitar uma configuração de instantâneo para um compartilhamento de dados. Essa configuração permite que os consumidores de dados recebam atualizações incrementais à medida que ocorrem. Essa configuração deve ser habilitada se o provedor de dados quiser que seus consumidores de dados recebam atualizações de dados compartilhados. 

Se um provedor de dados habilitar essa configuração, um intervalo de recorrência pode ser selecionado. O intervalo de recorrência pode ser de hora em hora ou de cada dia. 

Um consumidor de dados tem a opção de optar por esse cronograma de instantâneos para receber atualizações incrementais, o que inclui todos os dados que mudaram desde que geraram um novo instantâneo. 

## <a name="invitation"></a>Convite

Um provedor de dados pode convidar vários destinatários para seu compartilhamento de dados. Eles podem fazê-lo adicionando destinatários ao compartilhamento de dados. Os convites também podem ser adicionados após a criação de um compartilhamento de dados. 

Um provedor de dados pode excluir um convite depois de enviado se não tiver sido aceito. Se o provedor de dados excluir um convite e ele ainda não tiver sido aceito, o consumidor de dados não poderá aceitá-lo. 

Convites podem ser ressentidos até cinco vezes por dia. 

## <a name="recipient"></a>Destinatário

Um destinatário é alguém que recebe um convite para um compartilhamento de dados. Normalmente, um provedor de dados adicionará destinatários ao compartilhamento de dados que eles criam. Uma vez que o destinatário de um convite aceita o convite, ele se torna um consumidor de dados.  

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).
