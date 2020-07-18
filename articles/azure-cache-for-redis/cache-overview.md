---
title: O que é o Cache do Azure para Redis?
description: Saiba mais sobre o Cache do Azure para Redis para habilitar cache-aside, armazenamento em cache de conteúdo, armazenamento em cache de sessão do usuário, enfileiramento de trabalhos e mensagens e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 75c6f8ec8410ff90b3da4fb6a50c9ef8ba7d1618
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184611"
---
# <a name="azure-cache-for-redis"></a>Cache Redis do Azure

O Cache do Azure para Redis fornece um armazenamento de dados na memória com base no software open-source [Redis](https://redis.io/). Quando usado como um cache, o Redis aprimora o desempenho e a escalabilidade de sistemas que dependem fortemente de armazenamentos de dados de back-end. O desempenho é aprimorado com a cópia dos dados acessados com frequência para um armazenamento rápido localizado próximo ao aplicativo. Com o Cache do Azure para Redis, esse armazenamento rápido está localizado na memória, em vez de ser carregado do disco por um banco de dados.

O Cache do Azure para Redis pode ser usado como um cache de dados distribuído, um repositório de sessão e um agente de mensagem. O desempenho do aplicativo é aprimorado com o uso da baixa latência, do desempenho de alta taxa de transferência do mecanismo Redis.

O Cache do Azure para Redis oferece acesso a um Cache Redis seguro e dedicado. Ele é gerenciado pela Microsoft, hospedado no Azure e acessível para qualquer aplicativo dentro ou fora do Azure.

## <a name="using-azure-cache-for-redis"></a>Como usar o Cache Redis do Azure

O Cache do Azure para Redis aprimora o desempenho do aplicativo dando suporte a padrões de arquitetura de aplicativo comuns. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Os bancos de dados geralmente são grandes demais para serem carregados diretamente em um cache. É comum usar o padrão [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar dados no cache, somente conforme necessário. Quando o sistema faz alterações nos dados, ele também pode atualizar o cache, que será distribuído a outros clientes. Além disso, o sistema pode definir uma expiração em dados ou usar uma política de remoção para disparar atualizações de dados para o cache.|
| [Cache de conteúdo](cache-aspnet-output-cache-provider.md) | Muitas páginas da Web são geradas com base em modelos que usam conteúdo estático, como cabeçalhos, rodapés e faixas. Esses itens estáticos não devem ser alterados com frequência. O uso de um cache na memória fornece acesso rápido a conteúdo estático em comparação com armazenamentos de dados de back-end. Esse padrão reduz o tempo de processamento e a carga do servidor, permitindo que servidores Web sejam mais responsivos. Ele pode permitir que você reduza o número de servidores necessários para lidar com cargas. O Cache do Azure para Redis fornece o Provedor de Cache de Saída do Redis para dar suporte a esse padrão com o ASP.NET.|
| [Cache de sessão de usuário](cache-aspnet-session-state-provider.md) | Esse padrão é geralmente usado com carrinhos de compras e outros dados de histórico do usuário que um aplicativo Web pode desejar associar aos cookies do usuário. O armazenamento de muitas informações em um cookie pode ter um impacto negativo no desempenho conforme o tamanho do cookie aumenta e é passado e validado com cada solicitação. Uma solução típica usa o cookie como uma chave para consultar os dados em um banco de dados. O uso de um cache na memória, como o Cache do Azure para Redis, para associar as informações a um usuário é muito mais rápido que a interação com um banco de dados relacional completo. |
| Enfileiramento de mensagens e trabalhos | Aplicativos geralmente adicionam tarefas a uma fila quando as operações associadas à solicitação levam tempo para serem executadas. As operações de execução mais longa são enfileiradas para serem processadas em sequência, muitas vezes por outro servidor.  Esse método de adiamento do trabalho é chamado de enfileiramento de tarefas. O Cache do Azure para Redis fornece uma fila distribuída para habilitar esse padrão em seu aplicativo.|
| Transações distribuídas | Às vezes, os aplicativos exigem uma série de comandos em relação a um armazenamento de dados de back-end para serem executados como uma única operação atômica. Todos os comandos devem ter êxito ou ser revertidos para o estado inicial. O Cache do Azure para Redis dá suporte à execução de um lote de comandos como uma única [transação](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Ofertas de Cache do Azure para Redis para ofertas

O Cache do Azure para Redis está disponível nas seguintes camadas:

| Camada | Descrição |
|---|---|
Basic | Um cache de nó único. Essa camada dá suporte a vários tamanhos de memória (250 MB a 53 GB) e é ideal para o desenvolvimento/teste e para cargas de trabalho não críticas. A camada Básica não tem nenhum SLA (Contrato de Nível de Serviço) |
| Standard | Um cache replicado em uma configuração primária/réplica de dois nós gerenciado pelo Azure com um SLA de alta disponibilidade (99,9%) |
| Premium | A camada Premium é a camada pronta para Empresas. Os Caches da camada Premium dão suporte a mais recursos e têm uma maior taxa de transferência com latências menores. Os caches na camada Premium são implantados em um hardware mais potente, fornecendo um melhor desempenho em comparação com a camada Básica ou Standard. Essa vantagem significa que a taxa de transferência para um cache do mesmo tamanho será maior na camada Premium comparado à camada Standard. |

> [!TIP]
> Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches premium, confira [Perguntas frequentes sobre o Cache do Azure para Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Você pode dimensionar seu cache até uma camada superior depois de criá-lo. Não há suporte para a redução para uma camada inferior. Para obter instruções sobre dimensionamento passo a passo, confira [Como dimensionar o Cache do Azure para Redis](cache-how-to-scale.md) e [Como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Comparação de recursos

A página [Preço do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada camada. A seguinte tabela ajuda a descrever alguns dos recursos compatíveis por camada:

| Descrição do recurso | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistência de dados do Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Segurança por meio de regras de Firewall](cache-configure.md#firewall) |✔|✔|✔|
| Criptografia em trânsito |✔|✔|✔|
| [Isolamento e segurança aprimorada com a VNET](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importar/exportar](cache-how-to-import-export-data.md) |✔|-|-|
| [Atualizações agendadas](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Replicação geográfica](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido de aplicativo Web ASP.NET](cache-web-app-howto.md) Crie um aplicativo Web ASP.NET simples que usa um Cache do Azure para Redis.
* [Início Rápido do .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Criar um aplicativo .NET que usa um Cache do Azure para Redis.
* [Início Rápido do .NET Core](cache-dotnet-core-quickstart.md) Criar um aplicativo .NET Core que usa um Cache do Azure para Redis.
* [Início Rápido do Node.js](cache-nodejs-get-started.md) Crie um aplicativo simples do Node.js que usa um Cache do Azure para Redis.
* [Início Rápido do Java](cache-java-get-started.md) Crie um aplicativo Java simples que usa um Cache do Azure para Redis.
* [Início Rápido do Python](cache-python-get-started.md) Crie um aplicativo simples do Python que usa um Cache do Azure para Redis.
