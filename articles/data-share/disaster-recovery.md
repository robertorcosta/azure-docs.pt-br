---
title: Recuperação de desastres para compartilhamento de dados do Azure
description: Recuperação de desastres para compartilhamento de dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483174"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastres para compartilhamento de dados do Azure

Neste artigo, vamos mostrar como configurar um ambiente de recuperação de desastres para o Azure Data Share. As paralisações do data center do Azure são raras, mas podem durar de alguns minutos a horas. As paralisações do Data Center podem causar interrupções em ambientes que dependem do compartilhamento de dados pelo provedor de dados. Seguindo as etapas detalhadas neste artigo, os provedores de dados podem continuar a compartilhar dados com seus consumidores de dados no caso de uma paralisação do data center para a região primária que está hospedando seu compartilhamento de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Obtendo continuidade de negócios para o Azure Data Share

Para estar preparado para uma paralisação do data center, o provedor de dados pode ter um ambiente de compartilhamento de dados provisionado em uma região secundária. Existem medidas que podem ser tomadas que garantirão um remasso suave no caso de uma paralisação do data center ocorrer. 

Os provedores de dados podem prover recursos secundários do Azure Data Share em uma região adicional. Esses recursos do Compartilhamento de Dados podem ser configurados para incluir conjuntos de dados existentes no ambiente principal de compartilhamento de dados. Os consumidores de dados podem ser adicionados ao compartilhamento de dados ao configurar o ambiente DR ou adicionados em um momento posterior (ou seja, como parte das etapas de failover manual).

Se os consumidores de dados tiverem uma assinatura de compartilhamento ativa em um ambiente secundário provisionado para fins de DR, eles podem habilitar o cronograma de instantâneos como parte de um failover. Se os consumidores de dados não quiserem se inscrever em uma região secundária para fins de DR, eles podem ser convidados para o compartilhamento de dados secundários posteriormente. 

Os consumidores de dados podem ter uma assinatura de compartilhamento ativa ociosa para fins de DR, ou os provedores de dados podem adicioná-los em um momento posterior como parte dos procedimentos de failover manual. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade de Negócios e Recuperação de Desastres](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Obtenha alta disponibilidade em sua estratégia de BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).




