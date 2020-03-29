---
title: Solucionando problemas ao usar o gatilho Funções do Azure para O Cosmos DB
description: Problemas comuns, solução e etapas de diagnóstico, ao usar o gatilho Funções do Azure para o Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365501"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticar e solucionar problemas ao usar o gatilho Funções do Azure para o Cosmos DB

Este artigo abrange questões comuns, solução e etapas de diagnóstico, quando você usa o [gatilho Funções Azure para Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependências

O gatilho de funções do Azure e as vinculações para cosmos DB dependem dos pacotes de extensão sobre o tempo de execução das funções do Azure base. Mantenha sempre esses pacotes atualizados, pois eles podem incluir correções e novos recursos que possam resolver quaisquer problemas potenciais que você possa encontrar:

* Para obter funções Do Zure V2, consulte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para as funções do Azure V1, consulte [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo sempre se refere às funções Azure V2 sempre que o tempo de execução for mencionado, a menos que explicitamente especificado.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o Azure Cosmos DB SDK de forma independente

A principal funcionalidade do pacote de extensão é fornecer suporte para o gatilho e vinculações do Azure Functions para Cosmos DB. Ele também inclui o [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), o que é útil se você quiser interagir com o Azure Cosmos DB programáticamente sem usar o gatilho e as amarras.

Se quiser usar o Azure Cosmos DB SDK, certifique-se de que você não adicione ao seu projeto outra referência de pacote NuGet. Em vez disso, **deixe a referência SDK resolver através do pacote de extensão de funções do Azure**. Consumir o Azure Cosmos DB SDK separadamente do gatilho e das amarras

Além disso, se você estiver criando manualmente sua própria instância do [cliente Azure Cosmos DB SDK,](./sql-api-sdk-dotnet-core.md)você deve seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem padrão Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Esse processo evitará possíveis problemas de soquete em suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários e soluçãos comuns

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Função azure falha com coleta de mensagens de erro não existe

A função do Azure falha com a mensagem de erro "Não existe o 'nome de coleta' da coleção de origem (no banco de dados 'nome do banco de dados') ou a coleção de locação 'collection2-name' (no banco de dados 'nome do banco de dados') não existe. Ambas as coleções devem existir antes do ouvinte começar. Para criar automaticamente a coleção de locação, defina 'CreateLeaseCollectionIfNot' como 'true'"

Isso significa que um ou ambos os contêineres Do Azure Cosmos necessários para que o gatilho funcione não existam ou não são acessíveis à Função Azure. **O erro em si lhe dirá qual banco de dados e contêiner do Azure Cosmos é o gatilho que procura** com base na sua configuração.

1. Verifique `ConnectionStringSetting` o atributo e se ele **faz referência a uma configuração que existe no aplicativo de função Azure**. O valor neste atributo não deve ser a string de conexão em si, mas o nome da configuração configuração.
2. Verifique se `databaseName` `collectionName` o e existe em sua conta do Azure Cosmos. Se você estiver usando a `%settingName%` substituição automática de valor (usando padrões), certifique-se de que o nome da configuração existe no aplicativo de função Azure.
3. Se você não especificar um, `LeaseCollectionName/leaseCollectionName`o padrão é "arrendamentos". Verifique se esse recipiente existe. Opcionalmente, você `CreateLeaseCollectionIfNotExists` pode definir o `true` atributo no Gatilho para criá-lo automaticamente.
4. Verifique [a configuração de Firewall da sua conta Do Azure Cosmos](how-to-configure-firewall.md) para ver se não está bloqueando a função Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>A função do Azure não começa com "A coleta de throughput compartilhada deve ter uma chave de partição"

As versões anteriores do Azure Cosmos DB Extension não suportavam o uso de um contêiner de arrendamentos que foi criado dentro de um [banco de dados de throughput compartilhado](./set-throughput.md#set-throughput-on-a-database). Para resolver esse problema, atualize a extensão [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>A função Azure não começa com "PartitionKey deve ser fornecido para esta operação".

Este erro significa que você está usando atualmente uma coleção de locação particionada com uma dependência de [extensão](#dependencies)antiga . Atualize para a versão mais recente disponível. Se você estiver atualmente em execução no Azure Functions V1, você precisará atualizar para as funções Do Zure V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>A função Azure não começa com "A coleção de locação, se particionada, deve ter a chave de partição igual à id."

Este erro significa que o contêiner de arrendamentos atuais `/id`está particionado, mas o caminho da chave de partição não é . Para resolver esse problema, você precisa recriar o contêiner de arrendamentos como `/id` a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Você vê um "Valor não pode ser nulo. Nome do parâmetro: o" em seus logs de funções do Azure quando você tenta executar o gatilho

Este problema é exibido se você estiver usando o portal Azure e tentar selecionar o botão **Executar** na tela ao inspecionar uma função Azure que usa o gatilho. O gatilho não requer que você selecione Executar para iniciar, ele será iniciado automaticamente quando a função Azure for implantada. Se você quiser verificar o fluxo de log da função Azure no portal Azure, basta ir ao seu contêiner monitorado e inserir alguns novos itens, você verá automaticamente a execução do Trigger.

### <a name="my-changes-take-too-long-to-be-received"></a>Minhas mudanças demoram muito para serem recebidas.

Este cenário pode ter múltiplas causas e todas elas devem ser verificadas:

1. O seu Azure Function está sendo implantado na mesma região da sua conta Azure Cosmos? Para melhor latência de rede, o Azure Function e sua conta Azure Cosmos devem ser colocados na mesma região do Azure.
2. As alterações que estão ocorrendo em seu contêiner Azure Cosmos são contínuas ou esporádicas?
Se for a última opção, pode haver algum atraso entre as alterações sendo armazenadas e o Azure Function pegando-as. Isso acontece porque internamente, quando o gatilho verifica as alterações em seu contêiner Azure Cosmos e não encontra nada pendente para ser lido, suspenderá por uma quantidade de tempo configurável (5 segundos, por padrão) antes de verificar novas alterações (para evitar alto consumo da RU). Você pode configurar este tempo de suspensão ao `FeedPollDelay/feedPollDelay`fazer as [configurações](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do seu gatilho (o valor é esperado em milissegundos).
3. Seu contêiner Azure Cosmos pode ser [limitado por taxa](./request-units.md).
4. Você pode `PreferredLocations` usar o atributo no gatilho para especificar uma lista separada por comma das regiões do Azure para definir uma ordem de conexão preferida personalizada.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Algumas mudanças são repetidas no meu Gatilho

O conceito de "mudança" é uma operação em um documento. Os cenários mais comuns em que eventos para o mesmo documento são recebidos são:
* A conta está usando consistência eventual. Ao consumir o feed de alteração em um nível de consistência eventual, pode haver eventos duplicados entre as operações subsequentes de leitura de feed de alteração (o último evento de uma operação de leitura aparece como o primeiro da próxima).
* O documento está sendo atualizado. O Feed de alterações pode conter várias operações para os mesmos documentos, se esse documento estiver recebendo atualizações, ele pode pegar vários eventos (um para cada atualização). Uma maneira fácil de distinguir entre diferentes operações para o mesmo documento é rastrear a `_lsn` propriedade para cada [alteração](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Se eles não corresponderem, estas são mudanças diferentes sobre o mesmo documento.
* Se você estiver identificando `id`documentos apenas por , lembre-se `id` que o identificador único para um `id` documento é a e sua chave de partição (pode haver dois documentos com a mesma chave de partição, mas diferente).

### <a name="some-changes-are-missing-in-my-trigger"></a>Algumas mudanças estão faltando no meu gatilho

Se você descobrir que algumas das mudanças que aconteceram no seu contêiner Azure Cosmos não estão sendo captadas pela Função Azure, há uma etapa inicial de investigação que precisa acontecer.

Quando sua função Azure recebe as alterações, muitas vezes as processa e, opcionalmente, envia o resultado para outro destino. Quando estiver investigando alterações em falta, certifique-se **de medir quais alterações estão sendo recebidas no ponto de ingestão** (quando a função Azure é iniciada), não no destino.

Se algumas alterações estiverem faltando no destino, isso pode significar que é algum erro acontecendo durante a execução da função Azure após o recebimento das alterações.

Neste cenário, o melhor curso de `try/catch` ação é adicionar blocos em seu código e dentro dos loops que possam estar processando as alterações, para detectar qualquer falha para um determinado subconjunto de itens e manuseá-los de acordo (envie-os para outro armazenamento para análise ou retentativa). 

> [!NOTE]
> Por padrão, o gatilho de Azure Functions para Cosmos DB não repetirá um lote de alterações se houver uma exceção sem tratamento durante a execução do código. Isso significa que a razão pela qual as mudanças não chegaram ao destino é porque você está deixando de processá-las.

Se você descobrir que algumas alterações não foram recebidas pelo seu gatilho, o cenário mais comum é que há **outra função Azure em execução**. Pode ser outra função Azure implantada no Azure ou uma função Azure em execução local na máquina de um desenvolvedor que tenha **exatamente a mesma configuração** (mesmos contêineres monitorados e de locação), e esta função Azure está roubando um subconjunto das alterações que você esperaria que sua Função Azure processasse.

Além disso, o cenário pode ser validado, se você souber quantas instâncias do Azure Function App você tem em execução. Se você inspecionar seu contêiner de locação e contar o número `Owner` de itens de locação dentro, os valores distintos da propriedade neles devem ser iguais ao número de instâncias do seu App de Função. Se houver mais proprietários do que as instâncias conhecidas do Azure Function App, significa que esses proprietários extras são os que "roubam" as alterações.

Uma maneira fácil de contornar essa situação, é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` em sua Função com um valor novo/diferente ou, alternativamente, testar com um novo contêiner de locação.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Precisa reiniciar e reprocessar todos os itens do meu contêiner desde o início 
Para reprocessar todos os itens em um contêiner desde o início:
1. Interrompa sua função Azure se estiver em execução no momento. 
1. Exclua os documentos da coleção de locação (ou exclua e recrie a coleção de locação para que ela esteja vazia)
1. Defina o atributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger em sua função como true. 
1. Reinicie a função Azure. Ele agora lerá e processará todas as mudanças desde o início. 

A configuração [StartFromStarting](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) to true dirá à função Azure para começar a ler alterações desde o início da história da coleção em vez do tempo atual. Isso só funciona quando não há arrendamentos já criados (ou seja, documentos na cobrança de locações). Definir essa propriedade como verdadeira quando há arrendamentos já criados não tem efeito; neste cenário, quando uma função é interrompida e reiniciada, ela começará a ler a partir do último ponto de verificação, conforme definido na coleção de arrendamentos. Para reprocessar desde o início, siga as etapas acima 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A vinculação só pode ser\<feita com o IReadOnlyList Document> ou JArray

Esse erro acontece se o projeto Funções do Azure (ou qualquer projeto referenciado) contiver uma referência manual do NuGet ao Azure Cosmos DB SDK com uma versão diferente da fornecida pelo [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies).

Para contornar essa situação, remova a referência manual nuGet adicionada e deixe a referência Azure Cosmos DB SDK resolver através do pacote Azure Functions Cosmos DB Extension.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Alterando o intervalo de votação da função Azure para as alterações de detecção

Como explicado anteriormente para [minhas alterações demoram muito para serem recebidas,](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)a função Azure dormirá por uma quantidade configurável de tempo (5 segundos, por padrão) antes de verificar novas alterações (para evitar alto consumo de RU). Você pode configurar este tempo de suspensão ao `FeedPollDelay/feedPollDelay`fazer as [configurações](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do seu gatilho (o valor é esperado em milissegundos).

## <a name="next-steps"></a>Próximas etapas

* [Habilite o monitoramento de suas funções do Azure](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK Solução de problemas](./troubleshoot-dot-net-sdk.md)
