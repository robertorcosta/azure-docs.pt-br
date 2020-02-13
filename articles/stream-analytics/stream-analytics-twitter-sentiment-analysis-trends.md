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
ms.openlocfilehash: 7023651b09abc8c3124c7bf71608018d5cb72e25
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161981"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Stream Analytics do Azure

Este artigo ensina como criar uma solução de análise de sentimentos de mídia social colocando eventos do Twitter em tempo real nos hubs de eventos do Azure. Você escreve uma consulta de Azure Stream Analytics para analisar os dados e armazenar os resultados para uso posterior ou criar um painel de [Power bi](https://powerbi.com/) para fornecer informações em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais populares. Os tópicos de tendência são assuntos e atitudes que têm um alto volume de postagens na mídia social. A análise de sentimentos, que também é chamada de *mineração de opinião*, usa ferramentas de análise de mídia social para determinar atitudes em relação a um produto ou uma ideia. 

A análise de tendências do Twitter em tempo real é um ótimo exemplo de uma ferramenta de análise porque o modelo de assinatura de hashtag permite que você ouça palavras-chave específicas (hashtags) e desenvolva a análise de sentimentos do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa com um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter.

Para identificar os tópicos em destaque em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais.

## <a name="prerequisites"></a>Prerequisites

Neste guia de instruções, você usa um aplicativo cliente que se conecta ao Twitter e procura Tweets que têm determinadas hashtags (que você pode definir). Para executar o aplicativo e analisar os tweets usando o Azure streaming Analytics, você deve ter o seguinte:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Uma conta do [Twitter](https://twitter.com) .

* O aplicativo TwitterClientCore, que lê o feed do Twitter. Para obter esse aplicativo, baixe o [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Instale o [CLI do .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).

## <a name="create-an-event-hub-for-streaming-input"></a>Criar um hub de eventos para entrada de streaming

O aplicativo de exemplo gera eventos e empurra eles para um hub de eventos do Azure. Os hubs de eventos do Azure são o método preferencial de ingestão de eventos para Stream Analytics. Para obter mais informações, consulte a [documentação dos Hubs de Evento do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um namespace de hub de eventos e um hub de eventos
Nesta seção, você cria um namespace de Hub de eventos e adiciona um hub de eventos a esse namespace. Namespaces do hub de evento são usados para agrupar logicamente instâncias de barramento de evento relacionadas. 

1. Faça logon no portal do Azure e selecione **criar um recurso**. Clique. Procure por **hubs de eventos** e selecione **criar**.

2. Na página **criar namespace** , insira um nome de namespace. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione um tipo de preço e uma assinatura e crie ou escolha um grupo de recursos. Em seguida, escolha um local e selecione **criar**. 
 
4. Quando o namespace terminar a implantação, navegue até o grupo de recursos e localize o namespace do hub de eventos na lista de recursos do Azure. 

5. No novo namespace, selecione **+&nbsp;Hub de eventos**. 

6. Nomeie o novo hub de eventos *socialtwitter-eh*. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. Você não precisa definir outras opções para o hub de eventos.
 
7. Selecione **Criar**.

### <a name="grant-access-to-the-event-hub"></a>Conceder acesso para o hub de eventos

Antes que um processo possa enviar dados para um hub de eventos, o Hub de eventos precisa de uma política que permita o acesso. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1.  Na barra de navegação no lado esquerdo do namespace de seus hubs de eventos, selecione **hubs de eventos**, que está localizado na seção **entidades** . Em seguida, selecione o Hub de eventos que você acabou de criar.

2.  Na barra de navegação no lado esquerdo, selecione **políticas de acesso compartilhado** localizadas em **configurações**.

    >[!NOTE]
    >Há uma opção de políticas de acesso compartilhado em para o namespace do hub de eventos e para o Hub de eventos. Verifique se você está trabalhando no contexto do seu hub de eventos, não no namespace geral do hub de eventos.

3.  Na página política de acesso, selecione **+ Adicionar**. Em seguida, digite *socialtwitter* para o **nome da política** e marque a caixa de seleção **gerenciar** .
 
4.  Selecione **Criar**.

5.  Depois que a política tiver sido implantada, selecione a política na lista de políticas de acesso compartilhado.

6.  Localize a caixa denominada **cadeia de conexão Primary-Key** e selecione o botão copiar ao lado da cadeia de conexão.
 
7.  Cole a cadeia de conexão em um editor de texto. Você precisará dessa cadeia de conexão para a próxima seção depois de fazer algumas pequenas edições.

   A cadeia de conexão tem esta aparência:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Observe que a cadeia de conexão contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

   > [!NOTE]
   > Por segurança, partes da cadeia de conexão do exemplo foram removidas.

8.  No editor de texto, remova o `EntityPath` par da cadeia de conexão (não se esqueça de remover o ponto e vírgula anterior). Quando você terminar, a cadeia de conexão terá esta aparência:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=
   ```

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

O aplicativo cliente obtém eventos de tweet diretamente do Twitter. Para fazer isso, ele precisa de permissão para chamar as APIs de Streaming do Twitter. Para configurar essa permissão, você pode criar um aplicativo no Twitter que gere as credenciais exclusivas (por exemplo, um token OAuth). Você pode configurar o aplicativo de cliente para usar essas credenciais ao fazer chamadas à API. 

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Se você ainda não tiver um aplicativo do Twitter que possa ser usado para este guia de instruções, você poderá criar um. Você já deve ter uma conta do Twitter.

> [!NOTE]
> O processo exato no Twitter para criar um aplicativo e obter o token, as chaves e segredos pode mudar. Se essas instruções não corresponderem ao que você vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. Em um navegador da Web, acesse [Twitter para Desenvolvedores](https://developer.twitter.com/en/apps) e selecione **Criar um aplicativo**. Você poderá ver uma mensagem indicando que precisa solicitar uma conta de desenvolvedor do Twitter. Fique à vontade para fazer isso e, depois que seu aplicativo tiver sido aprovado, você verá um email de confirmação. Podem ser necessários vários dias para ser aprovado para uma conta de desenvolvedor.

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

2. Na página **Criar um aplicativo**, forneça os detalhes para o novo aplicativo e selecione **Criar seu aplicativo do Twitter**.

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes do aplicativo do Twitter")

3. Na página do aplicativo, selecione a guia **Chaves e Tokens** e copie os valores de **Chave de API do Consumidor** e **Chave Secreta de API do Consumidor**. Além disso, selecione **Criar** em **Token de Acesso e Segredo do Token de Acesso** para gerar os tokens de acesso. Copie os valores do **Token de Acesso** e do **Segredo do Token de Acesso**.

   Salve os valores recuperados do aplicativo do Twitter. Você precisará dos valores mais tarde.

> [!NOTE]
> As chaves e segredos do aplicativo Twitter fornecem acesso à sua conta do Twitter. Trate essas informações como confidenciais, o mesmo faça com sua senha do Twitter. Por exemplo, não insira essas informações em um aplicativo que você forneça a outras pessoas. 

### <a name="configure-the-client-application"></a>Configurar o aplicativo do cliente

Nós criamos um aplicativo de cliente que se conecta aos dados do Twitter por meio das [APIs de Streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto específico de tópicos.

Antes do aplicativo ser executado, ele requer certas informações, como as chaves do Twitter e a cadeia de conexão de hub de eventos.

1. Verifique se você baixou o aplicativo [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) , conforme listado nos pré-requisitos.

2. Use um editor de texto para abrir o arquivo *app. config* . Faça as seguintes alterações no elemento `<appSettings>`:

   * Defina `oauth_consumer_key` para a chave de consumidor do Twitter (chave de API). 
   * Defina `oauth_consumer_secret` para o segredo do consumidor do Twitter (chave de segredo da API).
   * Defina `oauth_token` para o token de acesso do Twitter.
   * Defina `oauth_token_secret` para o segredo de token de acesso do Twitter.
   * Defina `EventHubNameConnectionString` a cadeia de conexão. Certifique-se de que você use a cadeia de conexão do qual você removeu o `EntityPath` par chave-valor.
   * Defina `EventHubName` para o nome do hub de eventos (que é o valor do caminho da entidade).

3. Abra a linha de comando e navegue até o diretório onde seu aplicativo TwitterClientCore está localizado. Use o `dotnet build` de comando para compilar o projeto. Em seguida, use o comando `dotnet run` para executar o aplicativo. O aplicativo envia tweets para o Hub de eventos.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em tempo real do Twitter, você pode configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

1. Na portal do Azure, navegue até o grupo de recursos e selecione **+ Adicionar**. Em seguida, pesquise **Stream Analytics trabalho** e selecione **criar**.

2. Selecione o trabalho `socialtwitter-sa-job` e especifique uma assinatura, um grupo de recursos e um local.

    É aconselhável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.

3. Selecione **Criar**. Em seguida, navegue até seu trabalho quando a implantação for concluída.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. Em seu trabalho de Stream Analytics, selecione **entradas** no menu à esquerda em **topologia do trabalho**.

2. Selecione **+&nbsp;Adicionar entrada de fluxo** > **Hub de eventos**. Preencha o novo formulário de **entrada** com as seguintes informações:

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada| *TwitterStream* | Insira um alias para a entrada. |
   |Subscription  | \<Sua assinatura\> |  Selecione a assinatura do Azure que você deseja usar. |
   |Namespace do Hub de Eventos | *asa-Twitter-eventhub* |
   |Nome do Hub de Eventos | *socialtwitter-eh* | Escolha *usar existente*. Em seguida, selecione o Hub de eventos que você criou.|
   |Tipo de compactação de evento| GZip | O tipo de compactação de dados.|

   Deixe os valores padrão restantes e selecione **salvar**.

## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Este guia de instruções ajuda você a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, você pode usar uma [Janela em Cascata](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. Em sua **visão geral**do trabalho, selecione **Editar consulta** próximo à parte superior direita da caixa consulta. O Azure lista as entradas e saídas configuradas para o trabalho e permite que você crie uma consulta para transformar o fluxo de entrada conforme ele é enviado para a saída.

2. Altere a consulta no editor de consultas para o seguinte:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Os dados de evento das mensagens devem aparecer na janela de **visualização de entrada** abaixo da consulta. Verifique se a **exibição** está definida como **JSON**. Se você não vir nenhum dado, verifique se o gerador de dados está enviando eventos para o Hub de eventos e se você selecionou **gzip** como o tipo de compactação para a entrada.

4. Selecione **testar consulta** e observe os resultados na janela de **resultados de teste** abaixo da consulta.

5. Altere a consulta no editor de código para o seguinte e selecione **testar consulta**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Essa consulta retorna todos os tweets que incluem a palavra-chave *Azure*.

## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora você definiu um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo. A última etapa é definir um coletor de saída para o trabalho.  

Neste guia de instruções, você escreve os eventos de tweet agregados da consulta de trabalho para o armazenamento de BLOBs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure, para Hubs de Eventos ou Power BI, dependendo das suas necessidades de aplicativo.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Na seção **topologia do trabalho** , no menu de navegação à esquerda, selecione **saídas**. 

2. Na página **saídas** , clique em **+&nbsp;adicionar** e **armazenamento de blob/data Lake Storage Gen2**:

   * **Alias de saída**: Use o nome `TwitterStream-Output`. 
   * **Opções de importação**: selecione **selecionar armazenamento em suas assinaturas**.
   * **Conta de armazenamento**. Selecione sua conta de armazenamento.
   * **Contêiner**. Selecione **criar novo** e insira `socialtwitter`.
   
4. Clique em **Salvar**.   

## <a name="start-the-job"></a>Iniciar o trabalho

Uma entrada de trabalho, uma consulta e uma saída são especificadas. Você está pronto para iniciar o trabalho do Stream Analytics.

1. Verifique se o aplicativo TwitterClientCore está em execução. 

2. Na visão geral do trabalho, selecione **Iniciar**.

3. Na página **Iniciar trabalho** , para **hora de início da saída do trabalho**, selecione **agora** e, em seguida, selecione **Iniciar**.

## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)