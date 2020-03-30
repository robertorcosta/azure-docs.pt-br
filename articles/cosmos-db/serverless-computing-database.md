---
title: Computação de banco de dados sem servidor com funções Azure Cosmos DB e Azure
description: Saiba como o Azure Cosmos DB e o Azure Functions podem ser usados juntos para criar aplicativos de computação sem servidor controlados por evento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 70545020899b69508a4cedb0fd7cf5495c847104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462438"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Computação de banco de dados sem servidor usando o Azure Cosmos DB e o Azure Functions

A computação sem servidor trata da capacidade de se concentrar em partes individuais de lógica que são repetíveis e sem monitoração de estado. Essas partes não exigem nenhum gerenciamento de infraestrutura e consomem recursos somente pelos segundos ou milissegundos durante os quais são executadas. No núcleo do movimento da computação sem servidor estão as funções, que são disponibilizadas no ecossistema do Azure pelo [Azure Functions](https://azure.microsoft.com/services/functions). Para aprender sobre outros ambientes de execução sem servidor no Azure, consulte [serverless na página do Azure](https://azure.microsoft.com/solutions/serverless/). 

Com a integração nativa entre o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e o Azure Functions, você pode criar gatilhos de banco de dados, associações de entrada e associações de saída diretamente em sua conta do Azure Cosmos DB. Usando o Azure Functions e o Azure Cosmos DB, você pode criar e implantar os aplicativos sem servidor controlados por evento com acesso de baixa latência a dados avançados para uma base de usuários global.

## <a name="overview"></a>Visão geral

O Azure Cosmos DB e o Azure Functions permitem integrar bancos de dados e aplicativos sem servidor das seguintes maneiras:

* Crie um **gatilho de funções azure**orientada a eventos para cosmos DB . Este gatilho depende de fluxos [de feed](change-feed.md) de alteração para monitorar seu contêiner Azure Cosmos para alterações. Quando alterações forem feitas em um contêiner, o fluxo do feed de alterações é enviado para o gatilho, que invoca o Azure Function.
* Alternativamente, vincule uma função Azure a um contêiner Azure Cosmos usando uma **ligação de entrada**. As associações de entrada leem dados de um contêiner quando uma função é executada.
* Vincule uma função a um contêiner Azure Cosmos usando uma **ligação de saída**. As associações de saída gravam dados em um contêiner quando uma função é concluída.

> [!NOTE]
> Atualmente, o gatilho de funções do Azure, as ligações de entrada e as vinculações de saída para o Cosmos DB são suportadas para uso apenas com a API SQL. Para todas as outras APIs do Azure Cosmos DB, você deve acessar o banco de dados a partir de sua função usando o cliente estático para sua API.


O seguinte diagrama ilustra cada uma destas três integrações: 

![Como o Azure Cosmos DB e o Azure Functions são integrados](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

O gatilho de funções do Azure, a vinculação de entrada e a vinculação de saída para o Azure Cosmos DB podem ser usados nas seguintes combinações:

* Um gatilho de funções do Azure para Cosmos DB pode ser usado com uma saída vinculando a um recipiente Azure Cosmos diferente. Depois que uma função executa uma ação em um item no feed de alterações, você pode gravá-la em outro contêiner (sua gravação no mesmo contêiner de origem efetivamente criará um loop recursivo). Ou, você pode usar um gatilho Funções Azure para Cosmos DB para migrar efetivamente todos os itens alterados de um contêiner para um recipiente diferente, com o uso de uma ligação de saída.
* As associações de entrada e as associações de saída do Azure Cosmos DB podem ser usadas no mesmo Azure Function. Isso funciona bem em casos em que você deseja localizar determinados dados com a associação de entrada, modificá-los no Azure Function e, em seguida, salvá-los no mesmo contêiner ou em um contêiner diferente, após a modificação.
* Uma ligação de entrada a um contêiner Azure Cosmos pode ser usada na mesma função que um gatilho Funções Azure para Cosmos DB, e pode ser usada com ou sem uma ligação de saída também. Use essa combinação para aplicar informações de moeda atualizadas (extraídas com uma associação de entrada a um contêiner de câmbio) ao feed de alterações de novos pedidos em seu serviço de carrinho de compras. O total do carrinho de compras atualizado, com a conversão de moeda atual aplicada, pode ser gravado em um terceiro contêiner usando uma associação de saída.

## <a name="use-cases"></a>Casos de uso

Os casos de uso a seguir demonstram algumas maneiras pelas quais você pode aproveitar ao máximo seus dados do Azure Cosmos DB – conectando-os ao Azure Functions controlado por evento.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de IoT - Azure Funções gatilho e vinculação de saída para Cosmos DB

Em implementações de IoT, você pode invocar uma função quando a luz do mecanismo de verificação é exibida em um carro conectado.

**Implementação:** Use um gatilho de funções do Azure e vinculação de saída para Cosmos DB

1. Um **gatilho de funções do Azure para cosmos DB** é usado para desencadear eventos relacionados a alertas de carro, como a luz do motor de verificação acesa em um carro conectado.
2. Quando a luz do mecanismo de verificação é acesa, os dados do sensor são enviados para o Azure Cosmos DB.
3. O Azure Cosmos DB cria ou atualiza novos documentos de dados do sensor, em seguida, essas alterações são transmitidas para o gatilho Funções do Azure para Cosmos DB.
4. O gatilho é invocado em cada alteração de dados para a coleta de dados do sensor, pois todas as alterações são transmitidas por meio do feed de alterações.
5. Uma condição de limite é usada na função para enviar os dados do sensor para o departamento de garantia.
6. Se a temperatura também estiver acima de determinado valor, um alerta também será enviado para o proprietário.
7. A **vinculação de saída** na função atualiza o registro do carro em outro contêiner Do Azure Cosmos para armazenar informações sobre o evento do motor de verificação.

A imagem a seguir mostra o código gravado no portal do Azure para esse gatilho.

![Criar um gatilho de funções do Azure para o Cosmos DB no portal Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de uso financeiro – Gatilho de temporizador e associação de entrada

Em implementações financeiras, você pode invocar uma função quando um saldo de conta bancária se enquadra em determinada quantia.

**Implementação:** um gatilho de temporizador com uma associação de entrada do Azure Cosmos DB

1. Usando um [gatilho de temporizador,](../azure-functions/functions-bindings-timer.md)você pode recuperar as informações do saldo da conta bancária armazenadas em um contêiner Do Azure Cosmos em intervalos cronometrados usando uma **vinculação de entrada**.
2. Se o saldo estiver abaixo do limite de saldo baixo definido pelo usuário, acompanhe com uma ação do Azure Function.
3. A associação de saída pode ser uma [integração do SendGrid](../azure-functions/functions-bindings-sendgrid.md), que envia um email de uma conta de serviço para os endereços de email identificados para cada uma das contas de saldo baixo.

As imagens a seguir mostram o código no portal do Azure para este cenário.

![Arquivo Index.js para um gatilho de Temporizador em um cenário financeiro](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Arquivo Run.csx para um gatilho de Temporizador em um cenário financeiro](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de jogos - Azure Funções gatilho e vinculação de saída para Cosmos DB 

Nos jogos, quando um novo usuário é criado, você pode procurar outros usuários que possam conhecê-los usando a [API do Azure Cosmos DB Gremlin](graph-introduction.md). Em seguida, você pode gravar os resultados em um [banco de dados de SQL do Azure Cosmos DB] para fácil recuperação.

**Implementação:** Use um gatilho de funções do Azure e vinculação de saída para Cosmos DB

1. Usando um banco de [dados gráfico](graph-introduction.md) do Azure Cosmos DB para armazenar todos os usuários, você pode criar uma nova função com um gatilho de funções do Azure para o Cosmos DB. 
2. Sempre que um novo usuário é inserido, a função é invocada e, em seguida, o resultado é armazenado usando uma **associação de saída**.
3. A função consulta o banco de dados de grafo para pesquisar todos os usuários que estão diretamente relacionados ao novo usuário e retorna o conjunto de dados para a função.
4. Em seguida, esses dados são armazenados no Azure Cosmos DB, que podem ser recuperados com facilidade por qualquer aplicativo front-end que mostre ao novo usuário seus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caso de uso de varejo – Várias funções

Em implementações de varejo, quando um usuário adiciona um item à sua cesta agora você tem a flexibilidade de criar e invocar funções para componentes de pipeline de negócios opcionais.

**Implementação:** Múltiplas funções do Azure são acionadas para o Cosmos DB ouvindo um contêiner

1. Você pode criar várias funções do Azure adicionando gatilhos de funções do Azure para o Cosmos DB para cada um - todos os quais ouvem o mesmo feed de alteração de dados do carrinho de compras. Observe que, quando várias funções escutam o mesmo feed de alterações, uma nova coleção de concessão é necessária para cada função. Para obter mais informações sobre coleções de concessão, consulte [Noções básicas sobre a biblioteca de processador do Feed de alterações](change-feed-processor.md).
2. Sempre que um novo item é adicionado ao carrinho de compras de um usuário, cada função é invocada de forma independente pelo feed de alterações do contêiner de carrinho de compras.
   * Uma função pode usar o conteúdo da cesta atual para alterar a exibição de outros itens nos quais o usuário pode estar interessado.
   * Outra função pode atualizar os totais de inventário.
   * Outra função pode enviar informações do cliente sobre determinados produtos ao departamento de marketing, que envia um mensageiro promocional. 

     Qualquer departamento pode criar um Azure Functions for Cosmos DB ouvindo o feed de alterações e certifique-se de que eles não atrasarão eventos críticos de processamento de pedidos no processo.

Em todos esses casos de uso, como a função desacoplou o próprio aplicativo, você não precisa criar novas instâncias de aplicativo o tempo todo. Em vez disso, o Azure Functions cria funções individuais para concluir processos discretos, conforme necessário.

## <a name="tooling"></a>Ferramentas

A integração nativa entre o Azure Cosmos DB e o Azure Functions está disponível no portal Azure e no Visual Studio 2019.

* No portal Funções do Azure, você pode criar um gatilho. Para obter instruções de início rápido, consulte [Criar um gatilho funções do Azure para Cosmos DB no portal Azure](https://aka.ms/cosmosdbtriggerportalfunc).
* No portal Azure Cosmos DB, você pode adicionar um gatilho Funções Do Azure para o Cosmos DB a um aplicativo acessado existente no mesmo grupo de recursos.
* No Visual Studio 2019, você pode criar o gatilho usando as ferramentas de funções do [Azure](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Por que escolher a integração do Azure Functions para a computação sem servidor?

O Azure Functions fornece a capacidade de criar unidades de trabalho escalonáveis ou partes concisas de lógica que podem ser executadas sob demanda, sem provisionamento nem gerenciamento de infraestrutura. Ao usar funções do Azure, você não precisa criar um aplicativo completo para responder às alterações no banco de dados do Azure Cosmos, você pode criar pequenas funções reutilizáveis para tarefas específicas. Além disso, também use dados do Azure Cosmos DB como entrada ou saída para um Azure Function em resposta a um evento como solicitações HTTP ou um gatilho de tempo.

O Azure Cosmos DB é o banco de dados recomendado para a arquitetura de computação sem servidor pelos seguintes motivos:

* **Acesso instantâneo a todos os seus dados**: você tem acesso granular a cada valor armazenado porque o Azure Cosmos DB [indexa automaticamente](index-policy.md) todos os dados por padrão e disponibiliza esses índices imediatamente. Isso significa que você consegue consultar constantemente, atualizar e adicionar novos itens a seu banco de dados e ter acesso instantâneo por meio do Azure Functions.

* **Sem esquema**. O Azure Cosmos DB é sem esquema e, portanto, pode exclusivamente manipular qualquer saída de dados de um Azure Function. Essa abordagem de “manipular qualquer coisa” facilita a criação de uma variedade de Funções que serão todas geradas para o Azure Cosmos DB.

* **Taxa de transferência escalonável**. A taxa de transferência pode ser escalada e reduzida verticalmente de forma instantânea no Azure Cosmos DB. Caso você tenha centenas ou milhares de Funções consultando e gravando no mesmo contêiner, expanda as [RU/s](request-units.md) para lidar com a carga. Todas as funções podem trabalhar em paralelo usando as RU/s alocadas e seus dados têm a garantia de serem [consistentes](consistency-levels.md).

* **Replicação global**. Replique dados do Azure Cosmos DB [em todo o mundo](distribute-data-globally.md) para reduzir a latência, localizando os dados na geografia mais próxima a onde os usuários se encontram. Assim como ocorre com todas as consultas do Azure Cosmos DB, os dados de gatilhos controlados por evento são dados lidos do Azure Cosmos DB mais próximo ao usuário.

Se você pretende fazer a integração com o Azure Functions para armazenar dados e não precisa de indexação profunda ou se precisa armazenar anexos e arquivos de mídia, o [gatilho do Armazenamento de Blobs do Azure](../azure-functions/functions-bindings-storage-blob.md) pode ser uma opção melhor.

Benefícios do Azure Functions: 

* **Controlado por evento**. O Azure Functions é controlado por eventos e pode escutar um feed de alterações do Azure Cosmos DB. Isso significa que você não precisa criar a lógica de escuta, apenas ficar atento às alterações que você está escutando. 

* **Sem limites**. O Functions é executado em paralelo e o serviço cria o número de funções que forem necessárias. Defina os parâmetros.

* **Bom para tarefas rápidas**. O serviço cria novas instâncias de funções sempre que um evento é acionado e fecha-as assim que a função é concluída. Você paga apenas pelo tempo durante o qual as funções são executadas.

Caso não tenha certeza se o Flow, os Aplicativos Lógicos, o Azure Functions ou o WebJobs é o melhor para sua implementação, consulte [Escolher entre o Flow, os Aplicativos Lógicos, o Azure Functions e o WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Próximas etapas

Agora vamos conectar o Azure Cosmos DB e o Azure Functions de verdade: 

* [Criar um gatilho de funções do Azure para o Cosmos DB no portal Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Criar um gatilho HTTP do Azure Functions com uma associação de entrada do Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Associações e gatilhos do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md)