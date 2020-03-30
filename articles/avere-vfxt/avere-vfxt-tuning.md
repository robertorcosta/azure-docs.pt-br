---
title: Ajuste de cluster do Avere vFXT – Azure
description: Visão geral de configurações personalizadas para otimizar o desempenho no Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152923"
---
# <a name="cluster-tuning"></a>Ajuste do cluster

A maioria dos clusters vFXT podem se beneficiar de configurações de desempenho personalizadas. Essas configurações ajudam o cluster a funcionar melhor com seu fluxo de trabalho, conjunto de dados e ferramentas específicos.

Essa personalização deve ser feita com a ajuda de um representante de suporte, pois pode envolver a configuração de recursos que não estão disponíveis no Painel de Controle avere.

Esta seção descreve algumas das sintonias personalizadas que podem ser feitas.

## <a name="general-optimizations"></a>Otimizações gerais

Essas alterações podem ser recomendadas com base em qualidades do conjuntos de dados ou no estilo do fluxo de trabalho.

* Se a carga de trabalho for pesada em termos de gravação, aumente o tamanho do cache de gravação do padrão de 20%.
* Se o conjunto de dados envolve muitos arquivos pequenos, aumente o limite de contagem de arquivos do cache do cluster.
* Se o trabalho envolve copiar ou mover dados entre dois repositórios, ajuste o número de threads usados para a movimentação de dados:
  * Para aumentar a velocidade, você poderá aumentar o número de threads paralelos usados.
  * Se o volume de armazenamento de back-end está se tornando sobrecarregado, você talvez precise diminuir o número de threads paralelos usados.
* Se o cluster armazena em cache os dados para um servidor de dados principal que usa ACLs NFSv4, habilite o cache do modo de acesso para simplificar a autorização de arquivo para clientes específicos.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>NAS de nuvem ou otimizações de gateway de nuvem

Em um cenário de NAS ou gateway em nuvem, o cluster vFXT fornece acesso no estilo NAS a um contêiner em nuvem. Para aproveitar as velocidades de dados mais altas entre o cluster vFXT e o armazenamento em nuvem, seu representante pode recomendar alterar as configurações para empurrar mais agressivamente os dados para o volume de armazenamento do cache. Por exemplo: 

* Aumentar o número de conexões TCP entre o cluster e o contêiner de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Intermitência de nuvem ou otimizações de WAN híbrido

Em um cenário de explosão em nuvem ou cenário de otimização de WAN de armazenamento híbrido, o cluster vFXT oferece integração entre o armazenamento de hardware em nuvem e no local. Essas alterações podem ser úteis:

* Aumentar o número de conexões TCP permitidas entre o cluster e o arquivista de núcleo
* Habilitar a configuração de Otimização de WAN para o arquivista de núcleo remoto (essa configuração pode ser usada para um arquivista local remoto ou para um arquivista de núcleo de nuvem em uma região do Azure diferente.)
* Aumente o tamanho do buffer do soquete TCP<sup>*</sup>
* Habilite a configuração "sempre para frente" para reduzir arquivos redundantemente armazenados em cache<sup>*</sup>

<sup>*</sup>Esses ajustes podem não se aplicar a todos os sistemas, dependendo da carga de trabalho e das necessidades de desempenho.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajudar a otimizar o Avere vFXT para o Azure

Para contatar a equipe de suporte sobre essas otimizações, use o procedimento descrito em [Obter ajuda com o seu sistema](avere-vfxt-open-ticket.md).
