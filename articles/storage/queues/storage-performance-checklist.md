---
title: Lista de verificação de desempenho e escalabilidade do Armazenamento de Filas – Armazenamento do Azure
description: Uma lista de verificação de práticas comprovadas para uso com o Armazenamento de Filas ao desenvolver aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 8ab4cb6b06f0f023a8f6368dac633a97afe29fd4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390018"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Lista de verificação de desempenho e escalabilidade do Armazenamento de Filas

A Microsoft desenvolveu várias práticas comprovadas para desenvolver aplicativos de alto desempenho com o Armazenamento de Filas. Essa lista de verificação identifica as principais práticas que os desenvolvedores podem seguir para otimizar o desempenho. Tenha essas práticas em mente ao criar seu aplicativo e durante todo o processo.

O Armazenamento do Azure tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre as metas de escalabilidade do Armazenamento do Azure, confira [Metas de escalabilidade e desempenho do Armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

## <a name="checklist"></a>Lista de verificação

Este artigo organiza as práticas comprovadas de desempenho em uma lista de verificação que você pode seguir enquanto desenvolve seu aplicativo de Armazenamento de Filas.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Você pode criar seu aplicativo para usar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Você está evitando se aproximar dos limites de capacidade e de transação?](#capacity-and-transaction-targets) |
| &nbsp; |Rede |[Os dispositivos cliente têm largura de banda suficiente e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Rede |[Os dispositivos cliente têm um link de rede de alta qualidade?](#link-quality) |
| &nbsp; |Rede |[O aplicativo cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto do cliente |[Você está usando SAS (assinaturas de acesso compartilhado) e CORS (compartilhamento de recursos entre origens) para habilitar o acesso direto ao Armazenamento do Azure?](#sas-and-cors) |
| &nbsp; |Configuração do .NET |[Você está usando o .NET Core 2.1 ou posterior para obter um desempenho ideal?](#use-net-core) |
| &nbsp; |Configuração do .NET |[Você configurou seu cliente para usar uma quantidade suficiente de conexões simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração do .NET |[Para aplicativos .NET, você configurou o .NET para usar um número suficiente de threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Você garantiu que o paralelismo está corretamente associado para não sobrecarregar as funcionalidades do seu cliente ou se aproximar das metas de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Você está usando as últimas versões das bibliotecas de cliente e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Novas tentativas |[Você está usando uma política de repetição com uma retirada exponencial para limitar erros e tempos limite?](#timeout-and-server-busy-errors) |
| &nbsp; |Novas tentativas |[Seu aplicativo evita novas tentativas para erros que não admitem novas tentativas?](#non-retryable-errors) |
| &nbsp; |Configuração |[Você desativou o algoritmo Nagle para melhorar o desempenho de pequenas solicitações?](#disable-nagle) |
| &nbsp; |Tamanho da mensagem |[Suas mensagens são compactas para melhorar o desempenho da fila?](#message-size) |
| &nbsp; |Recuperação em massa |[Você está recuperando várias mensagens em uma única operação GET?](#batch-retrieval) |
| &nbsp; |Frequência de sondagem |[As votações ocorrem com frequência suficiente para reduzir a latência notável do aplicativo?](#queue-polling-interval) |
| &nbsp; |Atualização de mensagem |[Você está usando a operação Atualizar Mensagem para armazenar o progresso no processamento de mensagens para que você não precise reprocessar a mensagem inteira se ocorrer um erro?](#use-update-message) |
| &nbsp; |Arquitetura |[Você usa filas para melhorar a escalabilidade de todo o aplicativo ao manter cargas de trabalho demoradas fora do caminho crítico e escalá-las independentemente?](#application-architecture) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se o seu aplicativo se aproximar ou ultrapassar alguma das metas de escalabilidade, pode haver aumento da latência e restrição das transações. Quando o Armazenamento do Azure limita seu aplicativo, o serviço começa a retornar os códigos de erro 503 (Servidor ocupado) ou 500 (Tempo limite da operação). Evitar esses erros mantendo-se dentro dos limites dos destinos de escalabilidade é uma parte importante do aprimoramento do desempenho do seu aplicativo.

Para obter mais informações sobre metas de escalabilidade para o serviço Fila, confira [Metas de escalabilidade e desempenho do Armazenamento do Azure](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#azure-queue-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se você está se aproximando do número máximo de contas de armazenamento permitidas para uma combinação de assinatura/região específica, você está usando várias contas de armazenamento para fragmentar a fim de aumentar a entrada, a saída, as IOPS (operações de E/S por segundo) ou a capacidade? Nesse cenário, a Microsoft recomenda que você use maiores limites para contas de armazenamento a fim de reduzir o número de contas de armazenamento necessárias para sua carga de trabalho, se possível. Entre em contato com o [Suporte do Azure](https://azure.microsoft.com/support/options/) para solicitar maiores limites para sua conta de armazenamento. Para obter mais informações, confira [Anunciar contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e de transação

Se seu aplicativo estiver lidando com metas de escalabilidade de uma única conta de armazenamento, você pode adotar uma destas abordagens:  

- Se as metas de escalabilidade para filas forem insuficientes para seu aplicativo, use várias filas e distribua mensagens entre elas.
- Repensar a carga de trabalho que faz com que o aplicativo se aproxime da meta de escalabilidade ou a ultrapasse. Você pode alterar o aplicativo para que ele use menos largura de banda, menos capacidade ou menos transações?
- Se o aplicativo ultrapassar uma das metas de escalabilidade propositalmente, crie diversas contas de armazenamento e particione os dados do seu aplicativo nessas contas. Se você usar esse padrão, crie o aplicativo de forma que seja possível adicionar mais contas de armazenamento posteriormente, para balancear a carga. O único custo das contas de armazenamento é o uso dos dados armazenados, das transações feitas ou dos dados transferidos.
- Se o aplicativo estiver atingindo as metas de largura de banda, considere compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados para o Armazenamento do Azure.
    Embora a compactação de dados possa economizar largura de banda e melhorar o desempenho de rede, ela também pode ter efeitos negativos sobre o desempenho. Avalie o impacto de desempenho dos requisitos de processamento adicionais para compactação e descompactação de dados no lado do cliente. Tenha em mente que armazenar dados compactados pode dificultar a solução de problemas, porque pode ser mais desafiador exibir os dados usando ferramentas padrão.
- Se o aplicativo estiver se aproximando das metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas. É melhor tentar evitar alcançar as metas de escalabilidade implementando as recomendações descritas neste artigo. No entanto, usar uma retirada exponencial para novas tentativas impedirá que seu aplicativo tente novamente com rapidez, o que poderia piorar a limitação. Para obter mais informações, confira a seção intitulada [Erros de Tempo Limite e Servidor Ocupado](#timeout-and-server-busy-errors).

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

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que você precise autorizar um código, como o JavaScript que está sendo executado no navegador da Web de um usuário ou em um aplicativo de celular, a acessar dados no armazenamento do Azure. Uma abordagem é criar um aplicativo de serviço que funcione como um proxy. O dispositivo do usuário é autenticado no serviço que, por sua vez, autoriza o acesso aos recursos do Armazenamento do Azure. Dessa forma, você pode evitar a exposição das chaves de conta de armazenamento em dispositivos que não são seguros. No entanto, essa abordagem gera uma sobrecarga significativa no aplicativo do serviço, porque todos os dados transferidos entre o dispositivo do usuário e o Armazenamento do Azure devem passar pelo aplicativo do serviço.

Você pode evitar usar um aplicativo de serviço como um proxy para o Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado). Usando SAS, você pode permitir que o dispositivo do seu usuário faça solicitações diretamente ao Armazenamento do Azure usando um token de acesso limitado. Por exemplo, se um usuário desejar fazer upload de uma foto para seu aplicativo, seu aplicativo de serviço poderá gerar uma SAS e enviá-la ao dispositivo do usuário. O token SAS pode conceder permissão para gravar em um recurso do Armazenamento do Azure durante um intervalo especificado de tempo; depois disso, o token SAS expirará. Para obter mais informações sobre SAS, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).  

Normalmente, um navegador da Web não permitirá que o JavaScript em uma página hospedada por um site em um domínio execute determinadas operações, como de gravação, em outro domínio. Conhecida como a política de mesma origem, essa política impede que um script mal-intencionado em uma página obtenha acesso a dados em outra página da Web. No entanto, a política de mesma origem pode ser uma limitação ao criar uma solução na nuvem. O CORS (compartilhamento de recurso entre origens) é um recurso do navegador que permite que o domínio de destino se comunique com o navegador ao qual ele confia as solicitações que se originam no domínio de origem.

Por exemplo, suponha que um aplicativo Web em execução no Azure faça uma solicitação para um recurso para uma conta do Armazenamento do Azure. O aplicativo Web é o domínio de origem e a conta de armazenamento é o domínio de destino. Você pode configurar o CORS para que qualquer um dos serviços do Armazenamento do Azure se comunique com o navegador da Web cujas solicitações do domínio de origem têm a confiança do Armazenamento do Azure. Para obter mais informações sobre o CORS, confira [Suporte ao CORS (compartilhamento de recurso entre origens) para o Armazenamento do Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS e CORS podem ajudar você a evitar uma carga desnecessária em seu aplicativo Web.  

## <a name="net-configuration"></a>Configuração do .NET

Esta seção é útil para quem usa o .NET Framework, pois lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos parecidos aplicam-se à linguagem escolhida.  

### <a name="use-net-core"></a>Usar o .NET Core

Desenvolva seus aplicativos do Armazenamento do Azure com o .NET Core 2.1 ou superior para aproveitar os aprimoramentos de desempenho. O uso do .NET Core 3.x é recomendado quando possível.

Para saber mais sobre os aprimoramentos de desempenho no .NET Core, confira as seguintes postagens no blog:

- [Aprimoramentos de desempenho no .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Aprimoramentos de desempenho no .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar limite de conexão padrão

No .NET, o código a seguir aumenta o limite de conexão de padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) a 100. Normalmente, você deve definir o valor para aproximadamente igual ao número de threads utilizados pelo seu aplicativo.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Defina o limite da conexão antes de abrir conexões.  

No caso de outras linguagens de programação, confira a documentação da linguagem para verificar como definir o limite da conexão.  

Para saber mais, confira a postagem no blog [Serviços Web: conexões simultâneas](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

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

## <a name="disable-nagle"></a>Desabilitar o Nagle

O algoritmo de Nagle é implementado largamente em redes TCP/IP como meio de melhorar o desempenho das redes. No entanto, ele não é ideal em todas as circunstâncias (como em ambientes altamente interativos). O algoritmo do Nagle tem um impacto negativo sobre o desempenho de solicitações para o serviço Tabela do Azure e você deve desabilite-o, se possível.

## <a name="message-size"></a>Tamanho da mensagem

O desempenho de fila e a escalabilidade diminuem conforme o tamanho da mensagem aumenta. Coloque apenas as informações de que o receptor precisa em uma mensagem.  

## <a name="batch-retrieval"></a>Recuperação em lote

Você pode recuperar até 32 mensagens de uma fila em uma única operação. A recuperação em lote pode reduzir o número de viagens de ida e volta do aplicativo cliente, o que é especialmente útil para ambientes, como dispositivos móveis, com alta latência.  

## <a name="queue-polling-interval"></a>Intervalo de sondagem de fila

A maioria dos aplicativos de sondagem para mensagens de uma fila, pode ser uma das principais fontes de transações para o aplicativo. Selecione o intervalo de sondagem com sabedoria: a sondagem muito frequente pode fazer com que seu aplicativo se aproxime das metas de escalabilidade para a fila. No entanto, em 200.000 transações para US $0,01 (no momento da gravação), um único processador sondando uma vez por segundo em um mês custaria menos de 15 centavos, assim o custo de sondagem não é normalmente um fator que afeta sua opção de intervalo de sondagem.  

Para obter informações atualizadas sobre custos, confira [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Usar Atualizar Mensagem

Você pode usar a operação **Atualizar Mensagem** para aumentar o tempo limite de invisibilidade ou para atualizar as informações de estado de uma mensagem. Usar **Atualizar Mensagem** pode ser uma abordagem mais eficiente do que ter um fluxo de trabalho que passa uma tarefa de uma fila para a próxima conforme cada etapa do trabalho é concluída. Seu aplicativo pode salvar o estado do trabalho para a mensagem e continuar trabalhando, em vez de enfileirar a mensagem novamente para a próxima etapa do trabalho cada vez que uma etapa é concluída. Tenha em mente que cada operação **Atualizar Mensagem** conta para a meta de escalabilidade.

## <a name="application-architecture"></a>Arquitetura do aplicativo

Use filas para tornar a arquitetura do seu aplicativo escalonável. A seguir temos algumas maneiras de usar filas para que seu aplicativo seja mais escalonável:  

- Você pode usar as filas para criar listas de pendências de trabalho para processamento e suavização das cargas de trabalho no seu aplicativo. Por exemplo, você pode colocar na fila as solicitações dos usuários para execução de trabalhos que requerem trabalho muito intenso do processador, como o redimensionamento das imagens carregadas.
- Você pode usar as filas para desassociar parte do aplicativo, a fim de poder escaloná-las de forma independente. Por exemplo, um front-end da Web pode colocar os resultados de pesquisa dos usuários em uma fila para análise posterior e armazenamento. Você pode adicionar mais instâncias de função de trabalho para processar os dados da fila conforme necessário.  

## <a name="next-steps"></a>Próximas etapas

- [Escalabilidade do Armazenamento do Microsoft Azure e metas de desempenho para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status e códigos de erro](/rest/api/storageservices/Status-and-Error-Codes2)
