---
title: Lista de verificação de desempenho e escalabilidade para armazenamento Blob - Armazenamento Azure
description: Uma lista de verificação de práticas comprovadas para uso com armazenamento Blob no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473937"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Lista de verificação de desempenho e escalabilidade do Armazenamento de Blobs

A Microsoft desenvolveu uma série de práticas comprovadas para o desenvolvimento de aplicativos de alto desempenho com armazenamento Blob. Essa lista de verificação identifica as principais práticas que os desenvolvedores podem seguir para otimizar o desempenho. Tenha essas práticas em mente ao criar seu aplicativo e durante todo o processo.

O Armazenamento do Azure tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os alvos de escalabilidade do Azure Storage, consulte [metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e metas de [escalabilidade e desempenho para armazenamento Blob](scalability-targets.md).

## <a name="checklist"></a>Lista de verificação

Este artigo organiza práticas comprovadas para desempenho em uma lista de verificação que você pode seguir enquanto desenvolve seu aplicativo de armazenamento Blob.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Você pode criar seu aplicativo para usar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Você está evitando se aproximar dos limites de capacidade e de transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Um grande número de clientes está acessando uma única bolha simultaneamente?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Metas de escalabilidade |[Sua aplicação está dentro das metas de escalabilidade para uma única bolha?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Particionamento |[A convenção de nomenclatura foi projetada para permitir melhor balanceamento de carga?](#partitioning) |
| &nbsp; |Rede |[Os dispositivos cliente têm largura de banda suficiente e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Rede |[Os dispositivos cliente têm um link de rede de alta qualidade?](#link-quality) |
| &nbsp; |Rede |[O aplicativo cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto ao cliente |[Você está usando SAS (assinaturas de acesso compartilhado) e CORS (compartilhamento de recursos entre origens) para habilitar o acesso direto ao Armazenamento do Azure?](#sas-and-cors) |
| &nbsp; |Cache |[O cache de dados do aplicativo é freqüentemente acessado e raramente alterado?](#reading-data) |
| &nbsp; |Cache |[O loteamento do aplicativo está sendo atualizado, filmando-os no cliente e, em seguida, carregando-os em conjuntos maiores?](#uploading-data-in-batches) |
| &nbsp; |Configuração do .NET |[Você está usando o .NET Core 2.1 ou posterior para obter um desempenho ideal?](#use-net-core) |
| &nbsp; |Configuração do .NET |[Você configurou seu cliente para usar uma quantidade suficiente de conexões simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração do .NET |[Para aplicativos .NET, você configurou o .NET para usar um número suficiente de threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Você garantiu que o paralelismo está corretamente associado para não sobrecarregar as funcionalidades do seu cliente ou se aproximar das metas de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Você está usando as últimas versões das bibliotecas de cliente e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Novas tentativas |[Você está usando uma política de repetição com uma retirada exponencial para limitar erros e tempos limite?](#timeout-and-server-busy-errors) |
| &nbsp; |Novas tentativas |[Seu aplicativo evita novas tentativas para erros que não admitem novas tentativas?](#non-retryable-errors) |
| &nbsp; |Copiando bolhas |[Você está copiando bolhas da maneira mais eficiente?](#blob-copy-apis) |
| &nbsp; |Copiando bolhas |[Você está usando a versão mais recente do AzCopy para operações de cópia em massa?](#use-azcopy) |
| &nbsp; |Copiando bolhas |[Você está usando a família Azure Data Box para importar grandes volumes de dados?](#use-azure-data-box) |
| &nbsp; |Distribuição de conteúdo |[Você usa um CDN para distribuir conteúdo?](#content-distribution) |
| &nbsp; |Use metadados |[Você armazena os metadados sobre blobs usados com frequência?](#use-metadata) |
| &nbsp; |Carregando rapidamente |[Ao tentar carregar um blob rapidamente, você carrega blocos paralelamente?](#upload-one-large-blob-quickly) |
| &nbsp; |Carregando rapidamente |[Ao tentar carregar muitos blobs rapidamente, você carrega blocos paralelamente?](#upload-many-blobs-quickly) |
| &nbsp; |Tipo de blob |[Você usa blobs de página ou de bloco quando necessário?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se o seu aplicativo se aproximar ou ultrapassar alguma das metas de escalabilidade, pode haver aumento da latência e restrição das transações. Quando o Armazenamento do Azure limita seu aplicativo, o serviço começa a retornar os códigos de erro 503 (Servidor ocupado) ou 500 (Tempo limite da operação). Evitar esses erros mantendo-se dentro dos limites dos destinos de escalabilidade é uma parte importante do aprimoramento do desempenho do seu aplicativo.

Para obter mais informações sobre metas de escalabilidade para o serviço Fila, confira [Metas de escalabilidade e desempenho do Armazenamento do Azure](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se você estiver se aproximando do número máximo de contas de armazenamento permitidas para uma determinada combinação de assinatura/região, avalie seu cenário e determine se alguma das seguintes condições se aplica:

- Você está usando contas de armazenamento para armazenar discos não gerenciados e adicionar esses discos às suas máquinas virtuais (VMs)? Para este cenário, a Microsoft recomenda o uso de discos gerenciados. Escala de discos gerenciados para você automaticamente e sem a necessidade de criar e gerenciar contas de armazenamento individuais. Para obter mais informações, consulte [Introdução aos discos gerenciados do Azure](../../virtual-machines/windows/managed-disks-overview.md)
- Você está usando uma conta de armazenamento por cliente, para fins de isolamento de dados? Para este cenário, a Microsoft recomenda o uso de um recipiente blob para cada cliente, em vez de uma conta de armazenamento inteira. O Azure Storage agora permite atribuir funções de controle de acesso (RBAC) baseada em função em uma base por contêiner. Para obter mais informações, confira [Conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](../common/storage-auth-aad-rbac-portal.md).
- Você está usando várias contas de armazenamento para aumentar a entrada, saída, operações de I/O por segundo (IOPS) ou capacidade? Nesse cenário, a Microsoft recomenda que você use maiores limites para contas de armazenamento a fim de reduzir o número de contas de armazenamento necessárias para sua carga de trabalho, se possível. Entre em contato com o [Suporte do Azure](https://azure.microsoft.com/support/options/) para solicitar maiores limites para sua conta de armazenamento. Para obter mais informações, confira [Anunciar contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e de transação

Se seu aplicativo estiver lidando com metas de escalabilidade de uma única conta de armazenamento, você pode adotar uma destas abordagens:  

- Se o aplicativo atingir a meta de transação, considere usar contas de armazenamento blob de bloco, que são otimizadas para altas taxas de transação e baixa e consistente latência. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).
- Repensar a carga de trabalho que faz com que o aplicativo se aproxime da meta de escalabilidade ou a ultrapasse. Você pode alterar o aplicativo para que ele use menos largura de banda, menos capacidade ou menos transações?
- Se o aplicativo ultrapassar uma das metas de escalabilidade propositalmente, crie diversas contas de armazenamento e particione os dados do seu aplicativo nessas contas. Se você usar esse padrão, crie o aplicativo de forma que seja possível adicionar mais contas de armazenamento posteriormente, para balancear a carga. O único custo das contas de armazenamento é o uso dos dados armazenados, das transações feitas ou dos dados transferidos.
- Se o aplicativo estiver atingindo as metas de largura de banda, considere compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados para o Armazenamento do Azure.
    Embora a compactação de dados possa economizar largura de banda e melhorar o desempenho de rede, ela também pode ter efeitos negativos sobre o desempenho. Avalie o impacto de desempenho dos requisitos de processamento adicionais para compactação e descompactação de dados no lado do cliente. Tenha em mente que armazenar dados compactados pode dificultar a solução de problemas, porque pode ser mais desafiador exibir os dados usando ferramentas padrão.
- Se o aplicativo estiver se aproximando das metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas. É melhor tentar evitar alcançar as metas de escalabilidade implementando as recomendações descritas neste artigo. No entanto, usar uma retirada exponencial para novas tentativas impedirá que seu aplicativo tente novamente com rapidez, o que poderia piorar a limitação. Para obter mais informações, confira a seção intitulada [Erros de Tempo Limite e Servidor Ocupado](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Vários clientes acessando uma única bolha simultaneamente

Se você tiver um grande número de clientes acessando uma única bolha simultaneamente, você precisará considerar tanto por blob quanto por metas de escalabilidade da conta de armazenamento. O número exato de clientes que podem acessar uma única bolha vai variar dependendo de fatores como o número de clientes que solicitam a bolha simultaneamente, o tamanho da bolha e as condições da rede.

Se a bolha pode ser distribuída através de um CDN, como imagens ou vídeos servidos a partir de um site, então você pode usar um CDN. Para obter mais informações, consulte a seção intitulada [Distribuição de conteúdo](#content-distribution).

Em outros cenários, como simulações científicas onde os dados são confidenciais, você tem duas opções. A primeira é escalonar o acesso da sua carga de trabalho de tal forma que a bolha seja acessada durante um período de tempo versus ser acessada simultaneamente. Alternativamente, você pode copiar temporariamente o blob para várias contas de armazenamento para aumentar o Total de IOPS por blob e através de contas de armazenamento. Os resultados variam dependendo do comportamento do seu aplicativo, por isso não deixe de testar padrões de concorrência durante o projeto.

### <a name="bandwidth-and-operations-per-blob"></a>Largura de banda e operações por bolha

Uma única bolha suporta até 500 solicitações por segundo. Se você tem vários clientes que precisam ler a mesma bolha e você pode exceder esse limite, então considere usar uma conta de armazenamento blob de bloco. Uma conta de armazenamento blob de bloco fornece uma taxa de solicitação mais alta, ou operações de I/O por segundo (IOPS).

Você também pode usar uma rede de entrega de conteúdo (CDN) como o Azure CDN para distribuir operações na bolha. Para obter mais informações sobre o Azure CDN, consulte [a visão geral do Azure CDN](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Particionamento

Entender como o Azure Storage particiona seus dados blob é útil para melhorar o desempenho. O Azure Storage pode servir dados em uma única partição mais rapidamente do que dados que abrangem várias partições. Ao nomear suas bolhas apropriadamente, você pode melhorar a eficiência das solicitações de leitura.

O armazenamento Blob usa um esquema de particionamento baseado em intervalo para dimensionamento e balanceamento de carga. Cada bolha tem uma chave de partição composta pelo nome blob completo (account+container+blob). A chave de partição é usada para particionar dados blob em intervalos. As faixas são então balanceadas de carga através do armazenamento Blob.

Particionamento baseado em intervalo significa que convenções de nomeação que usam pedidos léxicos (por exemplo, *mypayroll*, *myperformance,* *myemployees,* etc.) ou carimbos de tempo *(log20160101*, *log20160102,* *log20160102,* etc.) são mais propensos a resultar na co-localização das partições no mesmo servidor de partição. , até que a carga aumentada exija que elas sejam divididas em intervalos menores. A co-localização de blobs no mesmo servidor de partição melhora o desempenho, de modo que uma parte importante do aprimoramento de desempenho envolve nomear blobs de uma maneira que os organize de forma mais eficaz.

Por exemplo, todos os blobs em um contêiner podem ser servidos por um único servidor até que a carga nesses blobs exija mais rebalanceamento dos intervalos de partição. Da mesma forma, um grupo de contas levemente carregadas com seus nomes organizados em ordem léxica pode ser servido por um único servidor até que a carga em uma ou todas essas contas exija que elas sejam divididas em vários servidores de partição.

Cada operação de balanceamento de carga pode afetar a latência das chamadas de armazenamento durante a operação. A capacidade do serviço de lidar com uma súbita explosão de tráfego para uma partição é limitada pela escalabilidade de um único servidor de partição até que a operação de balanceamento de carga seja inicializada e reequilibre o intervalo da chave de partição.

Você pode seguir algumas práticas recomendadas para reduzir a frequência de tais operações.  

- Se possível, use blob ou tamanhos de bloco maiores que 4 MiB para contas de armazenamento padrão e maiores que 256 KiB para contas de armazenamento premium. Tamanhos maiores ou tamanhos de bloco ativam automaticamente bolhas de bloco de alta throughput. Blobs de bloco de alta taxa de throughput fornecem ingestão de alto desempenho que não é afetada pela nomeação de partição.
- Examine a convenção de nomeação que você usa para contas, contêineres, blobs, tabelas e filas. Considere prefixar nomes de conta, contêiner ou blob com um hash de três dígitos usando uma função de hash que melhor se adapte às suas necessidades.
- Se você organizar seus dados usando carimbos de tempo ou identificadores numéricos, certifique-se de que não está usando um padrão de tráfego somente apêndice (ou somente preparatório). Esses padrões não são adequados para um sistema de particionamento baseado em intervalo. Esses padrões podem levar a que todo o tráfego vá para uma única partição e limite o sistema de efetivamente carregar o balanceamento.

    Por exemplo, se você tem operações diárias que usam uma bolha com um carimbo de data e hora, como *yyyymmdd,* então todo o tráfego para essa operação diária é direcionado para uma única bolha, que é servida por um único servidor de partição. Considere se os limites por bolha e limites por partição atendem às suas necessidades e considere dividir essa operação em várias bolhas, se necessário. Da mesma forma, se você armazenar dados de séries tempoem em suas tabelas, todo o tráfego pode ser direcionado para a última parte do namespace da chave. Se você estiver usando IDs numéricos, prefixe o ID com um hash de três dígitos. Se você estiver usando carimbos de tempo, prefixe o carimbo de tempo com o valor de segundos, por exemplo, *ssyyymmdd*. Se o aplicativo realizar rotineiramente operações de listagem e consulta, escolha uma função de hashing que limitará o número de consultas. Em alguns casos, um prefixo aleatório pode ser suficiente.
  
- Para obter mais informações sobre o esquema de particionamento usado no Armazenamento Azure, consulte [Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Rede

As restrições físicas da rede do aplicativo podem ter um impacto considerável no desempenho. As seções a seguir descrevem algumas das limitações que os usuários podem enfrentar.  

### <a name="client-network-capability"></a>Funcionalidade da rede do cliente

A largura de banda e a qualidade do link da rede desempenham funções importantes no desempenho do aplicativo, conforme descrito nas seções a seguir.

#### <a name="throughput"></a>Produtividade

No caso da largura de banda, muitas vezes o problema está relacionado às funcionalidades do cliente. As instâncias maiores do Azure têm NICs com mais capacidade. Por isso, você deve usar uma instância maior ou mais VMs se precisar de limites de rede mais altos em um único computador. Se você estiver acessando o Armazenamento do Azure de um aplicativo local, a mesma regra se aplicará: compreender as funcionalidades de rede do dispositivo do cliente e a conectividade de rede com o local de armazenamento do Azure e aperfeiçoá-los conforme necessário ou criar seu aplicativo para trabalhar dentro de suas funcionalidades.

#### <a name="link-quality"></a>Qualidade do link

Como acontece com qualquer uso de rede, tenha em mente que as condições de rede que resultam em erros e a perda do pacote desacelerarão a taxa de transferência efetiva.  Usar WireShark ou NetMon pode ajudar a identificar esse problema.  

### <a name="location"></a>Location

Em todos os ambientes, colocar o cliente próximo ao servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com o mínimo de latência, o melhor local para o cliente é a região na qual o Azure se encontra. Por exemplo, se tiver um aplicativo Web do Azure que usa o Armazenamento do Azure, localize-os dentro de uma única região, como o Oeste dos EUA ou o Sudeste Asiático. Colocalizar recursos reduz a latência e o custo, pois o uso de largura de banda em uma única região é gratuito.  

Se os aplicativos cliente acessarem o Armazenamento do Azure, mas não estiverem hospedados no Azure, como aplicativos de dispositivo móvel ou serviços corporativos locais, a localização da conta de armazenamento em uma região próxima a esses clientes poderá reduzir a latência. Se os clientes estiverem amplamente distribuídos (por exemplo, alguns na América do Norte e outros na Europa), considere usar uma conta de armazenamento por região. A implementação dessa solução é mais fácil se os dados armazenados pelo aplicativo são específicos aos usuários e se não é necessário replicar os dados entre as contas de armazenamento.

Para uma ampla distribuição de conteúdo blob, use uma rede de entrega de conteúdo, como o Azure CDN. Para obter mais informações, confira [CDN do Azure](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que você precise autorizar um código, como o JavaScript que está sendo executado no navegador da Web de um usuário ou em um aplicativo de celular, a acessar dados no armazenamento do Azure. Uma abordagem é criar um aplicativo de serviço que funcione como um proxy. O dispositivo do usuário é autenticado no serviço que, por sua vez, autoriza o acesso aos recursos do Armazenamento do Azure. Dessa forma, você pode evitar a exposição das chaves de conta de armazenamento em dispositivos que não são seguros. No entanto, essa abordagem gera uma sobrecarga significativa no aplicativo do serviço, porque todos os dados transferidos entre o dispositivo do usuário e o Armazenamento do Azure devem passar pelo aplicativo do serviço.

Você pode evitar usar um aplicativo de serviço como um proxy para o Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado). Usando SAS, você pode permitir que o dispositivo do seu usuário faça solicitações diretamente ao Armazenamento do Azure usando um token de acesso limitado. Por exemplo, se um usuário desejar fazer upload de uma foto para seu aplicativo, seu aplicativo de serviço poderá gerar uma SAS e enviá-la ao dispositivo do usuário. O token SAS pode conceder permissão para gravar em um recurso do Armazenamento do Azure durante um intervalo especificado de tempo; depois disso, o token SAS expirará. Para obter mais informações sobre SAS, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).  

Normalmente, um navegador da Web não permitirá que o JavaScript em uma página hospedada por um site em um domínio execute determinadas operações, como de gravação, em outro domínio. Conhecida como a política de mesma origem, essa política impede que um script mal-intencionado em uma página obtenha acesso a dados em outra página da Web. No entanto, a política de mesma origem pode ser uma limitação ao criar uma solução na nuvem. O CORS (compartilhamento de recurso entre origens) é um recurso do navegador que permite que o domínio de destino se comunique com o navegador ao qual ele confia as solicitações que se originam no domínio de origem.

Por exemplo, suponha que um aplicativo Web em execução no Azure faça uma solicitação para um recurso para uma conta do Armazenamento do Azure. O aplicativo Web é o domínio de origem e a conta de armazenamento é o domínio de destino. Você pode configurar o CORS para que qualquer um dos serviços do Armazenamento do Azure se comunique com o navegador da Web cujas solicitações do domínio de origem têm a confiança do Armazenamento do Azure. Para obter mais informações sobre o CORS, confira [Suporte ao CORS (compartilhamento de recurso entre origens) para o Armazenamento do Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS e CORS podem ajudar você a evitar uma carga desnecessária em seu aplicativo Web.  

## <a name="caching"></a>Cache

Caching desempenha um papel importante na performance. As seções a seguir discutem as melhores práticas de cache.

### <a name="reading-data"></a>Leitura de dados

Em geral, a leitura de dados uma vez é preferível a lê-los duas vezes. Considere o exemplo de um aplicativo web que recuperou uma bolha de 50 MiB do Azure Storage para servir como conteúdo para um usuário. Idealmente, o aplicativo armazena o blob localmente em disco e, em seguida, recupera a versão armazenada em cache para solicitações subsequentes do usuário.

Uma maneira de evitar recuperar uma bolha se ela não tiver sido modificada desde que foi armazenada em cache é qualificar a operação GET com um cabeçalho condicional para o tempo de modificação. Se o último tempo modificado for após o tempo em que a bolha foi armazenada em cache, então a bolha será recuperada e rearmazenada em cache. Caso contrário, a bolha armazenada em cache é recuperada para um desempenho ideal.

Você também pode decidir projetar seu aplicativo para assumir que a bolha permanece inalterada por um curto período após recuperá-la. Neste caso, a aplicação não precisa verificar se a bolha foi modificada durante esse intervalo.

Dados de configuração, dados de pesquisa e outros dados que são frequentemente usados pelo aplicativo são bons candidatos ao cache.  

Para obter mais informações sobre o uso de cabeçalhos condicionais, consulte [Especificar cabeçalhos condicionais para operações de serviço Blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Upload de dados em lotes

Em alguns cenários, você pode agregar dados localmente e, em seguida, carregá-los periodicamente em um lote em vez de carregar cada pedaço de dados imediatamente. Por exemplo, suponha que um aplicativo web mantenha um arquivo de registro de atividades. O aplicativo pode carregar detalhes de cada atividade como acontece com uma tabela (que requer muitas operações de armazenamento), ou pode salvar detalhes de atividade em um arquivo de log local e, em seguida, carregar periodicamente todos os detalhes da atividade como um arquivo delimitado para uma bolha. Se cada entrada de log tiver 1 KB de tamanho, você poderá carregar milhares de entradas em uma única transação. Uma única transação suporta o upload de uma bolha de até 64 MiB de tamanho. O desenvolvedor do aplicativo deve projetar para a possibilidade de dispositivo cliente ou falhas de upload. Se os dados de atividade precisarem ser baixados por um intervalo de tempo em vez de uma única atividade, então o uso do armazenamento Blob é recomendado sobre o armazenamento da Tabela.

## <a name="net-configuration"></a>Configuração do .NET

Esta seção é útil para quem usa o .NET Framework, pois lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos parecidos aplicam-se à linguagem escolhida.  

### <a name="use-net-core"></a>Usar o .NET Core

Desenvolva seus aplicativos do Armazenamento do Azure com o .NET Core 2.1 ou superior para aproveitar os aprimoramentos de desempenho. O uso do .NET Core 3.x é recomendado quando possível.

Para saber mais sobre os aprimoramentos de desempenho no .NET Core, confira as seguintes postagens no blog:

- [Aprimoramentos de desempenho no .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Aprimoramentos de desempenho no .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar limite de conexão padrão

Em .NET, o código a seguir aumenta o limite de conexão padrão (que geralmente é dois em um ambiente cliente ou dez em um ambiente de servidor) para 100. Normalmente, você deve definir o valor para aproximadamente igual ao número de threads utilizados pelo seu aplicativo. Defina o limite da conexão antes de abrir conexões.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Para outras linguagens de programação, consulte a documentação para determinar como definir o limite de conexão.  

Para obter mais informações, consulte o blog post [Web Services: Conexões Simultâneas](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de threads

Se você estiver usando chamadas síncronas juntamente com tarefas assíncronas, será conveniente aumentar o número de threads no pool:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, confira o método [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads).  

## <a name="unbounded-parallelism"></a>Paralelismo não associado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar o paralelismo não associado, que significa que não há nenhum limite imposto sobre o número de threads ou solicitações paralelas. Limite as solicitações paralelas para fazer upload ou baixar dados, para acessar várias partições na mesma conta de armazenamento ou acessar vários itens na mesma partição. Se o paralelismo não estiver associado, o aplicativo poderá ultrapassar as funcionalidades do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, o que resultará em limitação e latências mais longas.  

## <a name="client-libraries-and-tools"></a>Bibliotecas de cliente e ferramentas

Para obter o melhor desempenho, sempre use as bibliotecas de cliente e ferramentas mais recentes fornecidas pela Microsoft. As bibliotecas de cliente do Armazenamento do Azure estão disponíveis para várias linguagens. O Armazenamento do Azure também dá suporte ao PowerShell e à CLI do Azure. A Microsoft desenvolve ativamente essas ferramentas e bibliotecas de cliente pensando no desempenho, os mantém atualizados com as versões de serviço mais recentes e verifica se eles lidam com muitas práticas de desempenho comprovadas internamente. Para obter mais informações, confira a [documentação de referência do Armazenamento do Azure](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Manipular erros de serviço

O Armazenamento do Azure retorna um erro quando o serviço não pode processar uma solicitação. Entender os erros que podem ser retornados pelo Armazenamento do Azure em um determinado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Erros de Tempo Limite e Servidor Ocupado

O Armazenamento do Azure poderá limitar seu aplicativo se ele se aproximar dos limites de escalabilidade. Em alguns casos, o Armazenamento do Azure pode ser incapaz de manipular uma solicitação devido a algumas condições transitórias. Em ambos os casos, o serviço pode retornar um erro 503 (Servidor Ocupado) ou 500 (Tempo Limite). Esses erros também poderão ocorrer se o serviço estiver reequilibrando partições de dados para permitir uma taxa de transferência mais alta. Normalmente, o aplicativo cliente deve repetir a operação que gera um erro desses erros. No entanto, se o Armazenamento do Azure estiver limitando o aplicativo porque ele está ultrapassando as metas de escalabilidade ou porque o serviço não pôde atender à solicitação por algum motivo, novas tentativas poderão piorar o problema. O uso de uma política de repetição de retirada exponencial é recomendado e o padrão das bibliotecas de cliente é esse comportamento. Por exemplo, o aplicativo pode fazer uma nova tentativa em 2 segundos, 4 segundos, 10 segundos e 30 segundos para então abandonar o processo. Dessa forma, seu aplicativo reduz significativamente sua carga sobre o serviço, em vez de exacerbar o comportamento que poderia causar a limitação.  

Pode haver novas tentativas imediatas em caso de erros de conectividade porque esses erros não são resultado de limitações e devem ser temporários.  

### <a name="non-retryable-errors"></a>Erros sem nova tentativa

As bibliotecas de cliente manipulam as novas tentativas com um reconhecimento de quais erros podem ser repetidos e quais não. No entanto, se você estiver chamando a API REST do Armazenamento do Azure diretamente, haverá alguns erros que você não deverá repetir. Por exemplo, um erro 400 (Solicitação Inválida) indica que o aplicativo cliente envia uma solicitação que não pôde ser processada, porque ela não estava no formato esperado. O reenvio dessa solicitação sempre resulta na mesma resposta, por isso as novas tentativas não são úteis. Se você estiver chamando a API REST do Armazenamento do Azure diretamente, lembre-se de possíveis erros e se eles deverão ser repetidos.

Para obter mais informações sobre os códigos de erro do Armazenamento do Azure, confira [Status e códigos de erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Copiando e movendo bolhas

O Azure Storage fornece uma série de soluções para copiar e mover bolhas dentro de uma conta de armazenamento, entre contas de armazenamento e entre sistemas locais e a nuvem. Esta seção descreve algumas dessas opções em termos de seus efeitos sobre o desempenho. Para obter informações sobre a transferência eficiente de dados para ou a partir do armazenamento Blob, consulte [Escolha uma solução Azure para transferência de dados](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>APIs de cópia blob

Para copiar blobs em contas de armazenamento, use a operação ['Bloquear bloco de url'.](/rest/api/storageservices/put-block-from-url) Esta operação copia os dados sincronizadamente de qualquer fonte de URL em uma bolha de bloco. O `Put Block from URL` uso da operação pode reduzir significativamente a largura de banda necessária quando você está migrando dados através de contas de armazenamento. Como a operação de cópia ocorre no lado do serviço, você não precisa baixar e recarregar os dados.

Para copiar dados dentro da mesma conta de armazenamento, use a operação [Copiar Blob.](/rest/api/storageservices/Copy-Blob) A cópia de dados dentro da mesma conta de armazenamento é normalmente concluída rapidamente.  

### <a name="use-azcopy"></a>Usar o AzCopy

O utilitário de linha de comando AzCopy é uma opção simples e eficiente para a transferência em massa de blobs para, a partir e através de contas de armazenamento. O AzCopy é otimizado para este cenário e pode alcançar altas taxas de transferência. A versão 10 do `Put Block From URL` AzCopy usa a operação para copiar dados blob em contas de armazenamento. Para obter mais informações, consulte [Copiar ou mover dados para o Azure Storage usando o AzCopy v10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Use a caixa de dados do Azure

Para importar grandes volumes de dados para o armazenamento Blob, considere usar a família Azure Data Box para transferências offline. Os dispositivos Data Box fornecidos pela Microsoft são uma boa escolha para mover grandes quantidades de dados para o Azure quando você está limitado por tempo, disponibilidade de rede ou custos. Para obter mais informações, consulte a documentação do [Azure DataBox](/azure/databox/).

## <a name="content-distribution"></a>Distribuição de conteúdo

Às vezes, um aplicativo precisa servir o mesmo conteúdo para muitos usuários (por exemplo, um vídeo de demonstração de produto usado na página inicial de um site), localizado nas mesmas ou várias regiões. Neste cenário, use uma CDN (Content Delivery Network, rede de entrega de conteúdo) como o Azure CDN para distribuir conteúdo blob geograficamente. Diferente das contas de armazenamento do Azure que existem em uma única região e não podem fornecer conteúdos com baixa latência a outras regiões, a CDN do Azure usa servidores em diversos data centers pelo mundo. Além disso, uma CDN geralmente tem limites de saída muito maiores do que as contas de armazenamento individuais.  

Para obter mais informações, confira [CDN do Azure](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Use metadados

O serviço Blob suporta solicitações HEAD, que podem incluir propriedades blob ou metadados. Por exemplo, se o seu aplicativo precisar dos dados Exif (formato de imagem exchangable) de uma foto, ele pode recuperar a foto e extraí-la. Para economizar largura de banda e melhorar o desempenho, seu aplicativo pode armazenar os dados Exif nos metadados da bolha quando o aplicativo carrega a foto. Em seguida, você pode recuperar os dados Exif em metadados usando apenas uma solicitação HEAD. Recuperar apenas metadados e não o conteúdo completo da bolha economiza largura de banda significativa e reduz o tempo de processamento necessário para extrair os dados do Exif. Tenha em mente que 8 KiB de metadados podem ser armazenados por bolha.  

## <a name="upload-blobs-quickly"></a>Upload de bolhas rapidamente

Para carregar bolhas rapidamente, primeiro determine se você estará carregando uma bolha ou muitas. Use as informações abaixo para identificar o método de uso correto de acordo com o seu cenário.  

### <a name="upload-one-large-blob-quickly"></a>Carregue uma bolha grande rapidamente

Para carregar uma única bolha grande rapidamente, um aplicativo cliente pode carregar seus blocos ou páginas em paralelo, tendo em conta as metas de escalabilidade para bolhas individuais e a conta de armazenamento como um todo. As bibliotecas de clientes do Azure Storage suportam o upload em paralelo. Por exemplo, você pode usar as seguintes propriedades para especificar o número de solicitações simultâneas permitidas em .NET ou Java. Bibliotecas de clientes para outros idiomas suportados oferecem opções semelhantes.

- Para .NET, defina a propriedade [BlobRequestOptions.ParallelOperationThreadCount.](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)
- Para Java/Android, ligue para o método [BlobRequestOptions.setConcurrentRequestCount (final Integer concurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Carregar muitas bolhas rapidamente

Para carregar diversos blobs com rapidez, carregue-os paralelamente. O upload em paralelo é mais rápido do que carregar bolhas únicas de cada vez com uploads de blocos paralelos porque ele espalha o upload através de várias partições do serviço de armazenamento. AzCopy executa os carregamentos em paralelo por padrão e é a opção recomendada nesse caso. Para obter mais informações, consulte [Comece com o AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Escolha o tipo correto de bolha

O Azure Storage suporta bolhas de bloco, bolhas de apêndice e bolhas de página. Em um determinado cenário de uso, o tipo de blob escolhido afeta o desempenho e a escalabilidade da solução.

Os blobs de bloco são apropriados quando você deseja carregar grandes quantidades de dados de forma eficiente. Por exemplo, um aplicativo cliente que envia fotos ou vídeos para o armazenamento Blob teria como alvo blobs de bloco.

As bolhas de apêndice são semelhantes às bolhas de bloco, pois são compostas de blocos. Quando você modifica uma bolha de apêndice, os blocos são adicionados apenas ao fim da bolha. Blobs de apêndice são úteis para cenários como registro, quando um aplicativo precisa adicionar dados a uma bolha existente.

Blobs de página são apropriados se o aplicativo precisar executar gravações aleatórias nos dados. Por exemplo, os discos da máquina virtual do Azure são armazenados como blobs de página. Para obter mais informações, consulte [Entendendo bolhas de bloco, bolhas de apêndice e bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Próximas etapas

- [Metas de escalabilidade e desempenho para armazenamento Blob](scalability-targets.md)
- [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Status e códigos de erro](/rest/api/storageservices/Status-and-Error-Codes2)
