---
title: Introdução à camada Premium do Cache do Azure para Redis
description: Saiba como criar e gerenciar a Persistência do Redis, clustering do Redis e o suporte VNET para as instâncias da camada Premium do Cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 15b4764d32c536698246bddfcca50ffa6ce9b3b5
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184677"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introdução à camada Premium do Cache do Azure para Redis
O Cache do Azure para Redis é um cache distribuído e gerenciado que ajuda você a criar aplicativos altamente escalonáveis e responsivos, fornecendo acesso extremamente rápido aos seus dados. 

A nova camada Premium é uma camada pronta para Empresas, que inclui todos os recursos da camada Standard e muito mais, como melhor desempenho, cargas de trabalho maiores, recuperação de desastres, importação/exportação e segurança avançada. Continue lendo para saber mais sobre os recursos adicionais da camada de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em relação às camadas Standard ou Basic.
**Melhor desempenho em relação às camadas Standard ou Básica.** Os caches na camada Premium são implantados em hardware, que tem processadores mais rápidos e oferece melhor desempenho em comparação com a camada básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas. 

**A taxa de transferência para o Cache do mesmo tamanho é maior na camada Premium quando comparada à camada Standard.**  Por exemplo, a taxa de transferência para um Cache de 53 GB P4 (Premium) é de 250 mil solicitações por segundo quando comparada a 150 mil do C6 (Standard).

Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches Premium, consulte [perguntas frequentes sobre o cache do Azure para Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistência de dados do Redis
A camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Um cache básico ou Standard armazena todos os dados somente na memória. No caso de problemas de infraestrutura subjacentes, pode haver possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O cache do Azure para Redis oferece opções de RDB e AOF (em breve) no [Redis Persistence](https://redis.io/topics/persistence). 

Para obter instruções sobre como configurar a persistência, consulte [Como configurar a persistência para um Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Se você quiser criar caches maiores que 53 GB ou desejar fragmentar dados em vários nós do Redis, poderá usar o clustering Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica gerenciado pelo Azure para alta disponibilidade. 

**O clustering do Redis fornece uma produtividade e escala máximas.** A taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será 250 mil * 10 = 2,5 milhões solicitações por segundo. Consulte o [cache do Azure para obter perguntas frequentes](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) sobre o Redis para obter mais detalhes sobre tamanho, taxa de transferência e largura de banda com caches Premium.

Para começar com o clustering, consulte [Como configurar clustering para um Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Isolamento e segurança avançados
Os caches criados na camada Básica ou Standard podem ser acessados na Internet pública. O acesso ao Cache é restrito com base na tecla de acesso. Com a camada Premium, você pode garantir que somente os clientes em uma rede especificada possam acessar o cache. É possível implantar o Cache do Azure para Redis em uma [VNet (Rede Virtual) do Azure](https://azure.microsoft.com/services/virtual-network/). Você pode usar todos os recursos da VNet como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Redis.

Para saber mais, confira [Como configurar o suporte de Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importar/Exportar
A importação/exportação é um cache do Azure para a operação de gerenciamento de dados Redis que permite importar dados para o cache do Azure para o Redis ou exportar dados do cache do Azure para Redis importando e exportando um instantâneo do RDB (cache do Azure para o Redis) de um cache Premium para um blob de páginas em uma conta de armazenamento do Azure. Isso permite migrar entre diferentes instâncias do Cache do Azure para Redis ou preencher o cache com dados, antes do uso.

A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache do Azure para Redis carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância do servidor do Cache do Azure para Redis, e o arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Para obter mais informações, consulte [Como importar dados e exportar dados do Cache do Azure para Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reinicialização
A camada premium permite a reinicialização de um ou mais nós de seu cache sob demanda. Isso permite que você teste seu aplicativo para garantir a resiliência em caso de falhas. Você pode reinicializar os seguintes nós.

* Nó primário do cache
* Nó de réplica do cache
* Os nós primário e de réplica do cache
* Ao usar um cache Premium com clustering, você pode reinicializar o primário, a réplica ou ambos os nós para fragmentos individuais no cache

Para obter mais informações, consulte [Reinicializar](cache-administration.md#reboot) e [Perguntas frequentes sobre reinicialização](cache-administration.md#reboot-faq).

>[!NOTE]
>A funcionalidade de reinício agora está habilitada para todas as camadas do Cache do Azure para Redis.
>
>

## <a name="schedule-updates"></a>Agende atualizações
O recurso de atualizações agendadas permite designar uma janela de manutenção para seu cache. Quando a janela de manutenção é especificada, as atualizações do servidor Redis são feitas durante essa janela. Para designar uma janela de manutenção, selecione os dias desejados e especifique o horário de início da janela de manutenção para cada dia. O horário da janela de manutenção é em UTC. 

Para obter mais informações, consulte [Agendar atualizações](cache-administration.md#schedule-updates) e [Perguntas frequentes sobre agendamento de atualizações](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Apenas as atualizações do servidor Redis são realizadas durante a janela de manutenção agendada. A janela de manutenção se aplica a atualizações do Azure ou do sistema operacional da VM.
> 
> 

## <a name="geo-replication"></a>Replicação geográfica

A **replicação geográfica** fornece um mecanismo para vincular duas instâncias do Cache do Azure para Redis de camada Premium. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário se torna somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache entre regiões do Azure.

Para obter mais informações, consulte [Como configurar a replicação geográfica do Cache do Azure para Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Para dimensionar para a camada premium
Para dimensionar para a camada Premium, escolha uma das camadas Premium na folha **alterar tipo de preço** . Você também pode dimensionar seu cache para a camada premium usando o PowerShell e a CLI. Para obter instruções passo a passo, consulte [Como dimensionar o Cache do Azure para Redis](cache-how-to-scale.md) e [Como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Próximas etapas
Crie um cache e explore os novos recursos da camada premium.

* [Como configurar a persistência de um Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md)
* [Como configurar suporte de Rede Virtual para um Cache do Azure Premium para Redis](cache-how-to-premium-vnet.md)
* [Como configurar o clustering de um Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md)
* [Como importar dados para e exportar dados do Cache do Azure para Redis](cache-how-to-import-export-data.md)
* [Como administrar o Cache do Azure para Redis](cache-administration.md)

