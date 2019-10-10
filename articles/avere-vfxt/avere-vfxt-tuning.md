---
title: Ajuste de cluster do Avere vFXT – Azure
description: Visão geral de configurações personalizadas para otimizar o desempenho no Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 274a20f84b978b38a284d1feb315d6621b7d3ccd
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256159"
---
# <a name="cluster-tuning"></a>Ajuste do cluster


A maioria dos clusters vFXT podem se beneficiar de configurações de desempenho personalizadas. Essas configurações ajudam o cluster a funcionar melhor com seu fluxo de trabalho, conjunto de dados e ferramentas específicos. 

Essa personalização deve ser feita junto a um representante de suporte, porque geralmente envolve a configuração de recursos que não estão disponíveis no Painel de Controle do Avere.

Esta seção explica alguns dos ajustes personalizados que podem ser feitos.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Otimizações gerais

Essas alterações podem ser recomendadas com base em qualidades do conjuntos de dados ou no estilo do fluxo de trabalho.

* Se a carga de trabalho for pesada em termos de gravação, aumente o tamanho do cache de gravação do padrão de 20%. 
* Se o conjunto de dados envolve muitos arquivos pequenos, aumente o limite de contagem de arquivos do cache do cluster. 
* Se o trabalho envolve copiar ou mover dados entre dois repositórios, ajuste o número de threads usados para a movimentação de dados: 
  * Para aumentar a velocidade, você poderá aumentar o número de threads paralelos usados.
  * Se o volume de armazenamento de back-end está se tornando sobrecarregado, você talvez precise diminuir o número de threads paralelos usados.
* Se o cluster armazena em cache os dados para um servidor de dados principal que usa ACLs NFSv4, habilite o cache do modo de acesso para simplificar a autorização de arquivo para clientes específicos.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>NAS de nuvem ou otimizações de gateway de nuvem

Para tirar proveito de maiores velocidades de dados entre o cluster de vFXT e o armazenamento de nuvem em um cenário de gateway ou de NAS de nuvem (em que o cluster vFXT fornece acesso de estilo NAS a um contêiner de nuvem), o representante pode recomendar a alteração de configurações como essas para enviar dados por push, de modo mais agressivo, do cache para o volume de armazenamento:

* Aumentar o número de conexões TCP entre o cluster e o contêiner de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Intermitência de nuvem ou otimizações de WAN híbrido

Em um cenário de intermitência de nuvem ou cenário de otimização de WAN de armazenamento híbrido (em que o cluster vFXT fornece integração entre a nuvem e o hardware de armazenamento local), estas alterações podem ser úteis:

* Aumentar o número de conexões TCP permitidas entre o cluster e o arquivista de núcleo
* Habilitar a configuração de Otimização de WAN para o arquivista de núcleo remoto (essa configuração pode ser usada para um arquivista local remoto ou para um arquivista de núcleo de nuvem em uma região do Azure diferente.)
* Aumentar o tamanho do buffer de soquete TCP (dependendo das necessidades de desempenho e da carga de trabalho)
* Habilitar a configuração "sempre em frente" para reduzir os arquivos armazenados em cache de forma redundante (dependendo das necessidades de desempenho e da carga de trabalho)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajudar a otimizar o Avere vFXT para o Azure

Use o procedimento descrito em [Obter ajuda com o seu sistema](avere-vfxt-open-ticket.md) entrar em contato com a equipe de suporte sobre essas otimizações. 