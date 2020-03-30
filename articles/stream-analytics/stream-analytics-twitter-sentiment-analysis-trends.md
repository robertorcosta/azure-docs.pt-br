---
title: Análise de Sentimento do Twitter em tempo real com o Azure Stream Analytics
description: Este artigo descreve como usar o Stream Analytics para análise de sentimento do Twitter em tempo real. Orientações passo a passo de geração de eventos aos dados em um painel em tempo real.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240296"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Stream Analytics do Azure

Este artigo ensina como construir uma solução de análise de sentimento de mídia social, trazendo eventos do Twitter em tempo real para o Azure Event Hubs. Você escreve uma consulta do Azure Stream Analytics para analisar os dados e armazenar os resultados para uso posterior ou criar um painel [power bi](https://powerbi.com/) para fornecer insights em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais populares. Trending topics são assuntos e atitudes que têm um alto volume de posts nas redes sociais. A análise de sentimentos, que também é chamada *de mineração de opinião,* usa ferramentas de análise de mídia social para determinar atitudes em relação a um produto ou ideia. 

A análise de tendências do Twitter em tempo real é um ótimo exemplo de uma ferramenta de análise porque o modelo de assinatura de hashtag permite que você ouça palavras-chave específicas (hashtags) e desenvolva a análise de sentimento do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa com um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter.

Para identificar os tópicos em destaque em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais.

## <a name="prerequisites"></a>Pré-requisitos

Neste guia de como fazer, você usa um aplicativo cliente que se conecta ao Twitter e procura tweets que tenham certas hashtags (que você pode definir). Para executar o aplicativo e analisar os tweets usando o Azure Streaming Analytics, você deve ter o seguinte:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Uma conta [no Twitter.](https://twitter.com)

* O aplicativo TwitterClientCore, que lê o feed do Twitter. Para obter este aplicativo, baixe [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Instale a versão 2.1.0 [do .NET Core CORE.](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)

## <a name="create-an-event-hub-for-streaming-input"></a>Crie um hub de eventos para entrada em streaming

O aplicativo de exemplo gera eventos e empurra eles para um hub de eventos do Azure. Os Azure Event Hubs são o método preferido de ingestão de eventos para stream analytics. Para obter mais informações, consulte a [documentação dos Hubs de Evento do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um namespace de hub de eventos e um hub de eventos
Nesta seção, você cria um namespace do hub de eventos e adiciona um hub de eventos a esse namespace. Namespaces do hub de evento são usados para agrupar logicamente instâncias de barramento de evento relacionadas. 

1. Faça login no portal Azure e selecione **Criar um recurso**. Então. procurar **hubs de eventos** e selecionar **Criar**.

2. Na página **Criar namespace,** digite um nome de namespace. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL, e ele deve ser único em todo o Azure. 
    
3. Selecione um nível de precificação e assinatura e crie ou escolha um grupo de recursos. Em seguida, escolha um local e selecione **Criar**. 
 
4. Quando o namespace terminar de ser implantado, navegue até o seu grupo de recursos e encontre o namespace do hub de eventos em sua lista de recursos do Azure. 

5. No novo namespace, ** + &nbsp;** selecione Event Hub . 

6. Nomeie o novo hub de eventos *socialtwitter-eh*. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. Você não precisa definir outras opções para o hub de eventos.
 
7. Selecione **Criar**.

### <a name="grant-access-to-the-event-hub"></a>Conceder acesso para o hub de eventos

Antes que um processo possa enviar dados para um hub de eventos, o hub de eventos precisa de uma política que permita o acesso. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1.  Na barra de navegação no lado esquerdo do namespace dos hubs de eventos, selecione **Event Hubs**, que está localizado na seção **Entidades.** Em seguida, selecione o hub de eventos que você acabou de criar.

2.  Na barra de navegação do lado esquerdo, selecione **Políticas de acesso compartilhado** localizadas em **Configurações**.

    >[!NOTE]
    >Há uma opção de políticas de acesso compartilhado para o namespace do hub de eventos e para o hub de eventos. Certifique-se de que você está trabalhando no contexto do seu hub de eventos, não no espaço de nome geral do hub de eventos.

3.  Na página de política de acesso, selecione **+ Adicionar**. Em seguida, digite *o socialtwitter-access* para o **nome da diretiva** e verifique a caixa de seleção **Gerenciar.**
 
4.  Selecione **Criar**.

5.  Depois que a diretiva tiver sido implantada, selecione a diretiva na lista de políticas de acesso compartilhado.

6.  Encontre a caixa rotulada **Conexão string primary-key** e selecione o botão de cópia ao lado da seqüência de conexão.
 
7.  Cole a cadeia de conexão em um editor de texto. Você precisa desta seqüência de conexão para a próxima seção depois de fazer algumas pequenas edições.

   A cadeia de conexão tem esta aparência:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Observe que a cadeia de conexão contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

   > [!NOTE]
   > Por segurança, partes da cadeia de conexão do exemplo foram removidas.

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

O aplicativo cliente obtém eventos de tweet diretamente do Twitter. Para fazer isso, ele precisa de permissão para chamar as APIs de Streaming do Twitter. Para configurar essa permissão, você pode criar um aplicativo no Twitter que gere as credenciais exclusivas (por exemplo, um token OAuth). Você pode configurar o aplicativo de cliente para usar essas credenciais ao fazer chamadas à API. 

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Se você ainda não tem um aplicativo do Twitter que você pode usar para este guia de como fazer, você pode criar um. Você já deve ter uma conta do Twitter.

> [!NOTE]
> O processo exato no Twitter para criar um aplicativo e obter o token, as chaves e segredos pode mudar. Se essas instruções não corresponderem ao que você vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. A partir de um navegador da Web, vá para [o Twitter For Developers,](https://developer.twitter.com/en/apps)crie uma conta de desenvolvedor e selecione **Criar um aplicativo**. Você poderá ver uma mensagem indicando que precisa solicitar uma conta de desenvolvedor do Twitter. Sinta-se livre para fazê-lo, e depois que sua inscrição for aprovada, você deve ver um e-mail de confirmação. Podem ser necessários vários dias para ser aprovado para uma conta de desenvolvedor.

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

2. Na página **Criar um aplicativo**, forneça os detalhes para o novo aplicativo e selecione **Criar seu aplicativo do Twitter**.

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes do aplicativo do Twitter")

3. Na página do aplicativo, selecione a guia **Chaves e Tokens** e copie os valores de **Chave de API do Consumidor** e **Chave Secreta de API do Consumidor**. Além disso, selecione **Criar** em **Token de Acesso e Segredo do Token de Acesso** para gerar os tokens de acesso. Copie os valores do **Token de Acesso** e do **Segredo do Token de Acesso**.

   Salve os valores recuperados do aplicativo do Twitter. Você precisa dos valores mais tarde.

> [!NOTE]
> As chaves e segredos do aplicativo Twitter fornecem acesso à sua conta do Twitter. Trate essas informações como confidenciais, o mesmo faça com sua senha do Twitter. Por exemplo, não insira essas informações em um aplicativo que você forneça a outras pessoas. 

### <a name="configure-the-client-application"></a>Configurar o aplicativo do cliente

Nós criamos um aplicativo de cliente que se conecta aos dados do Twitter por meio das [APIs de Streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto específico de tópicos.

Antes do aplicativo ser executado, ele requer certas informações, como as chaves do Twitter e a cadeia de conexão de hub de eventos.

1. Certifique-se de ter baixado o aplicativo [TwitterClientCore,](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) conforme listado nos pré-requisitos.

2. Use um editor de texto para abrir o arquivo *App.config.* Faça as seguintes `<appSettings>` alterações no elemento:

   * Definido `oauth_consumer_key` para a chave do consumidor do Twitter (chave API). 
   * Definido `oauth_consumer_secret` para o Twitter Consumer Secret (chave secreta aPI).
   * Definido `oauth_token` para o token de acesso do Twitter.
   * Definido `oauth_token_secret` para o segredo de token de acesso do Twitter.
   * Defina `EventHubNameConnectionString` a cadeia de conexão.
   * Definido `EventHubName` para o nome do hub de eventos (que é o valor do caminho da entidade).

3. Abra a linha de comando e navegue até o diretório onde seu aplicativo TwitterClientCore está localizado. Use o `dotnet build` comando para construir o projeto. Em seguida, `dotnet run` use o comando para executar o aplicativo. O aplicativo envia tweets para o seu Event Hub.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em tempo real do Twitter, você pode configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

1. No portal Azure, navegue até o seu grupo de recursos e selecione **+ Adicionar**. Em seguida, procure o **trabalho do Stream Analytics** e selecione **Criar**.

2. Selecione o trabalho `socialtwitter-sa-job` e especifique uma assinatura, um grupo de recursos e um local.

    É aconselhável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.

3. Selecione **Criar**. Em seguida, navegue até o seu trabalho quando a implantação estiver concluída.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. No seu trabalho de Stream Analytics, selecione **Entradas** no menu esquerdo em **Job Topology**.

2. Selecione ** + &nbsp;Adicionar entrada de** > fluxo**Event Hub**. Preencha o novo formulário **de entrada** com as seguintes informações:

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada| *TwitterStream* | Digite um pseudônimo para a entrada. |
   |Subscription  | \<Sua assinatura\> |  Selecione a assinatura do Azure que você deseja usar. |
   |Namespace do Hub de Eventos | *asa-twitter-eventhub* |
   |Nome do Hub de Eventos | *socialtwitter-eh* | Escolha *Usar existente*. Em seguida, selecione o Centro de Eventos que você criou.|
   |Tipo de compactação do evento| GZip | O tipo de compressão de dados.|

   Deixe os valores padrão restantes e **selecione Salvar**.

## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Este guia de como fazer ajuda você a escrever e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, você pode usar uma [Janela em Cascata](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. Em sua **visão geral de**trabalho, selecione Editar **consulta** perto do canto superior direito da caixa Consulta. O Azure lista as entradas e saídas configuradas para o trabalho e permite que você crie uma consulta para transformar o fluxo de entrada à medida que ele é enviado para a saída.

2. Alterar a consulta no editor de consulta para o seguinte:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Os dados do evento das mensagens devem aparecer na janela **de visualização de entrada** abaixo da consulta. Certifique-se **de que a exibição** está definida como **JSON**. Se você não vir nenhum dado, certifique-se de que seu gerador de dados está enviando eventos para o seu hub de eventos e que você selecionou **o GZip** como o tipo de compactação para a entrada.

4. Selecione **a consulta de teste** e observe os resultados na janela de resultados do **teste** abaixo da consulta.

5. Altere a consulta no editor de código para a consulta a seguir e selecione **'Teste'**

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Esta consulta retorna todos os tweets que incluem a palavra-chave *Azure*.

## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora você definiu um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo. A última etapa é definir um coletor de saída para o trabalho.  

Neste guia de como fazer, você escreve os eventos de tweet agregados da consulta de trabalho para o armazenamento Do Azure Blob.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure, para Hubs de Eventos ou Power BI, dependendo das suas necessidades de aplicativo.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Na seção **'Topologia do trabalho'** no menu de navegação à esquerda, **selecione Saídas**. 

2. Na página **Saídas,** clique em ** + &nbsp;Adicionar** e **Blob armazenamento/Data Lake Storage Gen2**:

   * **Alias de saída**: Use o nome `TwitterStream-Output`. 
   * **Opções de importação**: Selecione **Selecionar armazenamento de suas assinaturas**.
   * **Conta de armazenamento**. Selecione sua conta de armazenamento.
   * **Contêiner**. Selecione **Criar** `socialtwitter`novo e inserir .
   
4. Selecione **Salvar**.   

## <a name="start-the-job"></a>Iniciar o trabalho

Uma entrada de trabalho, uma consulta e uma saída são especificadas. Você está pronto para iniciar o trabalho do Stream Analytics.

1. Certifique-se de que o aplicativo TwitterClientCore está sendo executado. 

2. Na visão geral do trabalho, selecione **Iniciar**.

3. Na página **Iniciar de trabalho,** para iniciar o tempo de início da saída de **trabalho,** selecione **Agora** e, em seguida, selecione **Iniciar**.

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente [nosso fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
