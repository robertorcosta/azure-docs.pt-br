---
title: O que é o Cache do Azure para Redis?
description: Saiba mais sobre o Cache do Azure para Redis para habilitar cache-aside, armazenamento em cache de conteúdo, armazenamento em cache de sessão do usuário, enfileiramento de trabalhos e mensagens e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 26f6c8e3aceddc6f766bb43a1e384d761dee32bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631365"
---
# <a name="azure-cache-for-redis"></a>Cache Redis do Azure
O Cache do Azure para Redis fornece um armazenamento de dados na memória com base no software open-source [Redis](https://redis.io/). O Redis melhora o desempenho e a escalabilidade de um aplicativo que usa muito armazenamentos de dados de back-end. Ele é capaz de processar grandes volumes de solicitação de aplicativo mantendo os dados acessados com frequência na memória do servidor, que pode ser gravada e lida rapidamente. O Redis traz uma solução de armazenamento de dados crítica de baixa latência e alta taxa de transferência para aplicativos modernos.

O Cache do Azure para Redis oferece o Redis como um serviço gerenciado. Ele fornece instâncias de servidor Redis seguras e dedicadas e compatibilidade total com a API do Redis. O serviço é operador pela Microsoft, hospedado no Azure e acessível para qualquer aplicativo dentro ou fora do Azure.

O Cache do Azure para Redis pode ser usado como um cache de dados ou conteúdo distribuído, um repositório de sessão, um agente de mensagem e muito mais. Ele pode ser implantado de modo autônomo ou junto com outro serviço de banco de dados do Azure, como o Azure SQL ou Cosmos DB.

## <a name="key-scenarios"></a>Principais cenários
O Cache do Azure para Redis aprimora o desempenho do aplicativo dando suporte a padrões de arquitetura de aplicativo comuns. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache de dados](cache-web-app-cache-aside-leaderboard.md) | Os bancos de dados geralmente são grandes demais para serem carregados diretamente em um cache. É comum usar o padrão [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar dados no cache, somente conforme necessário. Quando o sistema faz alterações nos dados, ele também pode atualizar o cache, que será distribuído a outros clientes. Além disso, o sistema pode definir uma expiração em dados ou usar uma política de remoção para disparar atualizações de dados para o cache.|
| [Cache de conteúdo](cache-aspnet-output-cache-provider.md) | Muitas páginas da Web são geradas com base em modelos que usam conteúdo estático, como cabeçalhos, rodapés e faixas. Esses itens estáticos não devem ser alterados com frequência. O uso de um cache na memória fornece acesso rápido a conteúdo estático em comparação com armazenamentos de dados de back-end. Esse padrão reduz o tempo de processamento e a carga do servidor, permitindo que servidores Web sejam mais responsivos. Ele pode permitir que você reduza o número de servidores necessários para lidar com cargas. O Cache do Azure para Redis fornece o Provedor de Cache de Saída do Redis para dar suporte a esse padrão com o ASP.NET.|
| [Repositório de sessão](cache-aspnet-session-state-provider.md) | Esse padrão é geralmente usado com carrinhos de compras e outros dados de histórico do usuário que um aplicativo Web pode desejar associar aos cookies do usuário. O armazenamento de muitas informações em um cookie pode ter um impacto negativo no desempenho conforme o tamanho do cookie aumenta e é passado e validado com cada solicitação. Uma solução típica usa o cookie como uma chave para consultar os dados em um banco de dados. O uso de um cache na memória, como o Cache do Azure para Redis, para associar as informações a um usuário é muito mais rápido que a interação com um banco de dados relacional completo. |
| Enfileiramento de mensagens e trabalhos | Aplicativos geralmente adicionam tarefas a uma fila quando as operações associadas à solicitação levam tempo para serem executadas. As operações de execução mais longa são enfileiradas para serem processadas em sequência, muitas vezes por outro servidor.  Esse método de adiamento do trabalho é chamado de enfileiramento de tarefas. O Cache do Azure para Redis fornece uma fila distribuída para habilitar esse padrão em seu aplicativo.|
| Transações distribuídas | Às vezes, os aplicativos exigem uma série de comandos em relação a um armazenamento de dados de back-end para serem executados como uma única operação atômica. Todos os comandos devem ter êxito ou ser revertidos para o estado inicial. O Cache do Azure para Redis dá suporte à execução de um lote de comandos como uma única [transação](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Versão do Redis

O Cache do Azure para Redis dá suporte ao Redis versão 4.x e, como versão prévia, 6.0. Tomamos a decisão de ignorar o Redis 5.0 para oferecer a você a última versão. Anteriormente, o Cache do Azure para Redis mantinha apenas uma só versão do Redis. Ele fornecerá uma atualização de versão principal mais recente e, pelo menos, uma versão estável mais antiga no futuro. Você pode [escolher qual versão](cache-how-to-version.md) funciona melhor para seu aplicativo.

> [!NOTE]
> Atualmente, o Redis 6.0 está em versão prévia. [Entre em contato conosco](mailto:azurecache@microsoft.com) se estiver interessado. Essa versão prévia é fornecida sem um Contrato de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Camadas de serviço
O Cache do Azure para Redis está disponível nas seguintes camadas:

| Camada | Descrição |
|---|---|
| Basic | Um cache de nó único. Essa camada dá suporte a vários tamanhos de memória (250 MB a 53 GB) e é ideal para o desenvolvimento/teste e para cargas de trabalho não críticas. A camada Básica não tem nenhum SLA (Contrato de Nível de Serviço). |
| Standard | Um cache replicado em uma configuração primária/réplica de dois nós gerenciado pelo Azure com um [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) de alta disponibilidade. |
| Premium | A camada Premium é a camada pronta para Empresas. Os Caches da camada Premium dão suporte a mais recursos e têm uma maior taxa de transferência com latências menores. Os caches na camada Premium são implantados em um hardware mais potente, fornecendo um melhor desempenho em comparação com a camada Básica ou Standard. Essa vantagem significa que a taxa de transferência para um cache do mesmo tamanho será maior na camada Premium comparado à camada Standard. |

### <a name="feature-comparison"></a>Comparação de recursos
O [Preço do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada camada. A seguinte tabela ajuda a descrever alguns dos recursos compatíveis por camada:

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

### <a name="choosing-the-right-tier"></a>Escolher a camada correta
Você deve considerar o seguinte ao escolher uma camada do Cache do Azure para Redis.

* **Memória**: as camadas Básica e Standard oferecem de 250 MB a 53 GB. A camada Premium oferece até 1,2 TB (como um cluster) ou 120 GB (não clusterizado). Para obter mais informações, consulte [Preço do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho de rede**: se você tiver uma carga de trabalho que exija uma alta taxa de transferência, a camada Premium oferece mais largura de banda quando comparada às camadas Standard ou Básica. Também dentro de cada camada, caches de tamanhos maiores têm mais largura de banda, devido à VM subjacente que hospeda o cache. Para obter mais informações, confira [Desempenho do Cache do Azure para Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Taxa de transferência**: a camada Premium oferece a maior taxa de transferência disponível. Se o cliente ou o servidor de cache atingir os limites de largura de banda, você poderá receber tempos limite no lado do cliente. Para obter mais informações, confira a tabela a seguir.
* **Alta disponibilidade**: O Cache do Azure para Redis garante que um cache Standard/Premium esteja disponível conforme nosso [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). o SLA abrange apenas a conectividade com os pontos de extremidade do cache. O SLA não abrange a proteção contra perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados.
* **Persistência de dados do Redis**: A camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Problemas de infraestrutura subjacente podem resultar em uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache do Azure para Redis oferece opções de RDB e AOF (versão prévia) na Persistência do Redis. Para saber mais, confira [Como configurar a persistência para um Cache do Azure para Redis Premium](cache-how-to-premium-persistence.md).
* **Cluster do Redis**: se desejar criar caches maiores que 120 GB ou fragmentar dados entre vários nós do Redis, você pode usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica para alta disponibilidade. Para saber mais, confira [Como configurar o clustering para um Cache do Azure para Redis Premium](cache-how-to-premium-clustering.md).
* **Segurança e isolamento de rede aprimorados**: A implantação da VNET (Rede Virtual do Azure) melhora a segurança e o isolamento do Cache do Azure para Redis, bem como as sub-redes, as políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Para saber mais, confira [Como configurar o suporte de Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md).
* **Configurar Redis**: nas camadas Standard e Premium, você pode configurar o Redis para notificações de Keyspace.
* **Quantidade máxima de conexões de cliente**: a camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanhos maiores. O clustering não aumenta o número de conexões disponíveis para um cache em cluster. Para obter mais informações, confira [Preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo dedicado para o servidor do Redis**: na camada Premium, todos os tamanhos de cache têm um núcleo dedicado para o Redis. Nas camadas Básica/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor do Redis.
* **Processamento de thread único**: o Redis, por design, usa apenas um thread para processamento de comando. O Cache do Azure para Redis também utiliza núcleos adicionais para processamento de E/S. Ter mais núcleos melhora o desempenho da taxa de transferência, embora possa não produzir dimensionamento linear. Além disso, tamanhos de VM maiores normalmente têm limites de largura de banda maiores do que os menores. Isso ajuda a evitar a saturação da rede, o que causará tempos limite em seu aplicativo.
* **Aprimoramentos de desempenho**: os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às camadas Básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas. Para obter mais informações, confira [Desempenho do Cache do Azure para Redis](cache-planning-faq.md#azure-cache-for-redis-performance)

Você pode dimensionar seu cache até uma camada superior depois de criá-lo. Não há suporte para a redução para uma camada inferior. Para obter instruções sobre dimensionamento passo a passo, confira [Como dimensionar o Cache do Azure para Redis](cache-how-to-scale.md) e [Como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Próximas etapas
* [Início Rápido de aplicativo Web ASP.NET](cache-web-app-howto.md) Crie um aplicativo Web ASP.NET simples que usa um Cache do Azure para Redis.
* [Início Rápido do .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Criar um aplicativo .NET que usa um Cache do Azure para Redis.
* [Início Rápido do .NET Core](cache-dotnet-core-quickstart.md) Criar um aplicativo .NET Core que usa um Cache do Azure para Redis.
* [Início Rápido do Node.js](cache-nodejs-get-started.md) Crie um aplicativo simples do Node.js que usa um Cache do Azure para Redis.
* [Início Rápido do Java](cache-java-get-started.md) Crie um aplicativo Java simples que usa um Cache do Azure para Redis.
* [Início Rápido do Python](cache-python-get-started.md) Crie um aplicativo simples do Python que usa um Cache do Azure para Redis.
