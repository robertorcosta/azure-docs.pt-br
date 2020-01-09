---
title: Análise de Sentimento do Twitter em tempo real com o Azure Stream Analytics
description: Este artigo descreve como usar o Stream Analytics para análise de sentimento do Twitter em tempo real. Orientações passo a passo de geração de eventos aos dados em um painel em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: f3ab21d55b7d59bb58760bfba452b4ea2d103496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369891"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Stream Analytics do Azure

Aprenda a compilar uma solução de análise de sentimento para análise de mídia social colocando os eventos em tempo real do Twitter nos Hubs de Eventos do Azure. Em seguida, escreva uma consulta de Azure Stream Analytics para analisar os dados e armazenar os resultados para uso posterior ou criar um painel de [Power bi](https://powerbi.com/) para fornecer informações em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais populares. Os tópicos de tendência são assuntos e atitudes que têm um alto volume de postagens na mídia social. A análise de sentimentos, que também é chamada de *mineração de opinião*, usa ferramentas de análise de mídia social para determinar atitudes em relação a um produto ou uma ideia. 

A análise de tendências do Twitter em tempo real é um ótimo exemplo de uma ferramenta de análise porque o modelo de assinatura de hashtag permite que você ouça palavras-chave específicas (hashtags) e desenvolva a análise de sentimentos do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa com um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter.

Para identificar os tópicos em destaque em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais.

## <a name="prerequisites"></a>Pré-requisitos
Neste guia de instruções, você usa um aplicativo cliente que se conecta ao Twitter e procura Tweets que têm determinadas hashtags (que você pode definir). Para executar o aplicativo e analisar os tweets usando o Azure streaming Analytics, você deve ter o seguinte:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma conta do [Twitter](https://twitter.com) .
* O aplicativo TwitterWPFClient, que lê o feed do Twitter. Para obter esse aplicativo, baixe o arquivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) do GitHub e, em seguida, descompacte o pacote em uma pasta no seu computador. Se você quiser ver código-fonte e executar o aplicativo em um depurador, você pode obter o código-fonte do [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Criar um hub de eventos para entrada do Streaming Analytics

O aplicativo de exemplo gera eventos e empurra eles para um hub de eventos do Azure. Os Hubs de Eventos do Azure são o método preferencial de ingestão de eventos para Stream Analytics. Para obter mais informações, consulte a [documentação dos Hubs de Evento do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um namespace de hub de eventos e um hub de eventos
Crie um namespace de Hub de eventos e, em seguida, adicione um hub de eventos a esse namespace. Namespaces do hub de evento são usados para agrupar logicamente instâncias de barramento de evento relacionadas. 

1. Entre no Portal do Azure e clique em **Criar um recurso** > **Internet das Coisas** > **Hub de Eventos**. 

2. Na folha **Criar um namespace**, insira um nome de namespace como `<yourname>-socialtwitter-eh-ns`. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione uma assinatura e crie ou escolha um grupo de recursos e clique em **Criar**. 

    ![Criar um namespace do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Quando o namespace acabar a implementação, localize o namespace de hub de eventos na lista de recursos do Azure. 

5. Clique em novo namespace e, na folha do namespace, clique em **+&nbsp;Hub de eventos**. 

    ![Botão Adicionar Hub de Eventos para criar um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nomeie o novo hub de evento `socialtwitter-eh`. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. Você não precisa definir outras opções para o hub de eventos.

    ![Folha para a criação de um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Clique em **Criar**.


### <a name="grant-access-to-the-event-hub"></a>Conceder acesso para o hub de eventos

Antes que um processo possa enviar dados para um hub de eventos, o hub de eventos deve ter uma política que permita o acesso apropriado. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1.  Na folha de namespace de evento, clique em **Hubs de Eventos** e, em seguida, clique no nome do seu novo Hub de Eventos.

2.  Na folha de Hub de Eventos, clique em **Políticas de acesso compartilhado** e depois em **+&nbsp;Adicionar**.

    >[!NOTE]
    >Verifique se você está trabalhando com o hub de eventos, não com o namespace de hub de eventos.

3.  Adicione uma política chamada `socialtwitter-access` e para **Declaração**, selecione **Gerenciar**.

    ![Folha para a criação de uma nova política de acesso de hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Clique em **Criar**.

5.  Depois que a política for implementada, clique na lista de políticas de acesso compartilhado.

6.  Localize a caixa rotulada **CHAVE PRIMÁRIA DA CADEIA DE CONEXÃO** e clique no botão de cópia próximo à cadeia de conexão. 
    
    ![Copiando a chave de cadeia de cadeia de conexão primária da política de acesso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Cole a cadeia de conexão em um editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois que você fizer algumas pequenas modificações.

    A cadeia de conexão tem esta aparência:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observe que a cadeia de conexão contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

    > [!NOTE]
    > Por segurança, partes da cadeia de conexão do exemplo foram removidas.

8.  No editor de texto, remova o `EntityPath` par da cadeia de conexão (não se esqueça de remover o ponto e vírgula anterior). Quando você terminar, a cadeia de conexão terá esta aparência:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter
O aplicativo cliente obtém eventos de tweet diretamente do Twitter. Para fazer isso, ele precisa de permissão para chamar as APIs de Streaming do Twitter. Para configurar essa permissão, você pode criar um aplicativo no Twitter que gere as credenciais exclusivas (por exemplo, um token OAuth). Você pode configurar o aplicativo de cliente para usar essas credenciais ao fazer chamadas à API. 

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Se você ainda não tiver um aplicativo do Twitter que possa ser usado para este guia de instruções, você poderá criar um. Você já deve ter uma conta do Twitter.

> [!NOTE]
> O processo exato no Twitter para criar um aplicativo e obter o token, as chaves e segredos pode mudar. Se essas instruções não corresponderem ao que você vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. Em um navegador da Web, acesse [Twitter para Desenvolvedores](https://developer.twitter.com/en/apps) e selecione **Criar um aplicativo**. Você poderá ver uma mensagem indicando que precisa solicitar uma conta de desenvolvedor do Twitter. Fique à vontade para fazer isso e, depois que seu aplicativo tiver sido aprovado, você deverá ver um email de confirmação. Podem ser necessários vários dias para ser aprovado para uma conta de desenvolvedor.

   ![Confirmação da conta de desenvolvedor do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Confirmação da conta de desenvolvedor do Twitter")

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

2. Na página **Criar um aplicativo**, forneça os detalhes para o novo aplicativo e selecione **Criar seu aplicativo do Twitter**.

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes do aplicativo do Twitter")

3. Na página do aplicativo, selecione a guia **Chaves e Tokens** e copie os valores de **Chave de API do Consumidor** e **Chave Secreta de API do Consumidor**. Além disso, selecione **Criar** em **Token de Acesso e Segredo do Token de Acesso** para gerar os tokens de acesso. Copie os valores do **Token de Acesso** e do **Segredo do Token de Acesso**.

    ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Detalhes do aplicativo do Twitter")

Salve os valores recuperados do aplicativo do Twitter. Você precisará dos valores posteriormente no instruções.

>[!NOTE]
>As chaves e segredos do aplicativo Twitter fornecem acesso à sua conta do Twitter. Trate essas informações como confidenciais, o mesmo faça com sua senha do Twitter. Por exemplo, não insira essas informações em um aplicativo que você forneça a outras pessoas. 


### <a name="configure-the-client-application"></a>Configurar o aplicativo do cliente
Nós criamos um aplicativo de cliente que se conecta aos dados do Twitter por meio das [APIs de Streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto específico de tópicos. O aplicativo usa a ferramenta de código aberto [Sentiment140](http://help.sentiment140.com/), que atribui o seguinte valor de sentimento para cada tweet:

* 0 = negativo
* 2 = neutro
* 4 = positivo

Depois que um valor de sentimento foi atribuído aos eventos de tweet, eles são enviados para o hub de eventos que você criou anteriormente.

Antes do aplicativo ser executado, ele requer certas informações, como as chaves do Twitter e a cadeia de conexão de hub de eventos. Você pode fornecer as informações de configuração das seguintes maneiras:

* Execute o aplicativo e, em seguida, use a interface de usuário do aplicativo para inserir a cadeia de conexão, as chaves e os segredos. Se você fizer isso, as informações de configuração são usadas para a sessão atual, mas não serão salvas.
* Edite o arquivo de configuração do aplicativo e defina os valores de lá. Essa abordagem persiste as informações de configuração, mas isso também significa que essas informações potencialmente confidenciais são armazenadas em texto sem formatação em seu computador.

O procedimento a seguir documenta as duas abordagens. 

1. Verifique se você baixou e descompactou o aplicativo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip), conforme listado nos pré-requisitos.

2. Para definir os valores em tempo de execução (e apenas para a sessão atual), execute o `TwitterWPFClient.exe` aplicativo. Quando o aplicativo solicitar, insira os seguintes valores:

    * A chave do consumidor do Twitter (chave de API).
    * A segredo do consumidor do Twitter (segredo de API).
    * O Token de acesso do Twitter.
    * O Segredo do Token de acesso do Twitter.
    * As informações de cadeia de conexão que você salvou anteriormente. Certifique-se de que você use a cadeia de conexão do qual você removeu o `EntityPath` par chave-valor.
    * As palavras-chave do Twitter que você deseja determinar o sentimento.

   ![Aplicativo TwitterWpfClient em execução, mostrando as configurações obscuras](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Para definir os valores de maneira persistente, use um editor de texto para abrir o arquivo de configuração TwitterWpfClient.exe. Em seguida, no elemento `<appSettings>`, faça isso:

   * Defina `oauth_consumer_key` a chave do consumidor do Twitter (chave de API). 
   * Defina `oauth_consumer_secret` o segredo do consumidor do Twitter (segredo de API).
   * Defina `oauth_token` o Token de acesso do Twitter.
   * Defina `oauth_token_secret` o Segredo do Token de acesso do Twitter.

     Mais adiante no elemento `<appSettings>`, faça essas alterações:

   * Defina `EventHubName` para o nome do hub de evento (ou seja, o valor do caminho da entidade).
   * Defina `EventHubNameConnectionString` a cadeia de conexão. Certifique-se de que você use a cadeia de conexão do qual você removeu o `EntityPath` par chave-valor.

     A `<appSettings>` seção se parece com o seguinte exemplo. (Para maior clareza e segurança, nós encapsulamos algumas linhas e removemos alguns caracteres.)

     ![Arquivo de configuração de aplicativo TwitterWpfClient em um editor de texto, mostrando as chaves e segredos do Twitter e as informações de cadeia de conexão de hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Se você ainda não iniciou o aplicativo, execute o TwitterWpfClient.exe agora. 

5. Selecione o botão iniciar verde para coletar sentimento social. Você vê eventos de Tweet com os valores **CreatedAt**, **Topic** e **SentimentScore** sendo enviados ao hub de eventos.

    ![Aplicativo TwitterWpfClient em execução, mostrando as listagem de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Se você encontrar erros, e você não vir um fluxo de tweets exibido na parte inferior da janela, verifique novamente as chaves e segredos. Além disso, verifique a cadeia de conexão (certifique-se de que ela não inclui o `EntityPath` chave e valor.)


## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em tempo real do Twitter, você pode configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

1. No portal do Azure, clique em **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Selecione o trabalho `socialtwitter-sa-job` e especifique uma assinatura, um grupo de recursos e um local.

    É aconselhável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.

    ![Criando um novo trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Clique em **Criar**.

    O trabalho é criado e o portal exibe detalhes do trabalho.


## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. No trabalho do Stream Analytics, em **Topologia do Trabalho**, no meio da folha de trabalho, selecione **Entradas**. 

2. Na folha **Entradas**, clique em **+&nbsp;Adicionar** e, em seguida, preencha a folha com estes valores:

   * **Alias de entrada**: Use o nome `TwitterStream`. Se você usar um nome diferente, anote-o porque você precisará dele depois.
   * **Tipo de Origem**: Selecione **Fluxo de Dados**.
   * **Origem**: Selecione **hub de eventos**.
   * **Opção de importação**: Selecione **Usar hub de evento da assinatura atual**. 
   * **Namespace de barramento de serviço**: Selecione o namespace de hub de eventos que você criou anteriormente (`<yourname>-socialtwitter-eh-ns`).
   * **Hub de eventos**: Selecione o hub de eventos que você criou anteriormente (`socialtwitter-eh`).
   * **Nome de política do hub de eventos**: Selecione a política de acesso que você criou anteriormente (`socialtwitter-access`).

     ![Criar nova entrada para o trabalho de Streaming Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Clique em **Criar**.


## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Este guia de instruções ajuda você a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, você pode usar uma [Janela em Cascata](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. Feche a folha de **entradas** se ainda não o fez.

2. Na folha **Visão geral**, clique em **Editar Consulta** perto da parte superior direita da caixa de Consulta. Azure lista as entradas e saídas que são configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada como é enviado para a saída.

3. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

3. Na folha **Consulta**, clique nos pontos ao lado da `TwitterStream` entrada e, em seguida, selecione **Dados de exemplo da entrada**.

    ![Opções de menu para usar dados de exemplo para a entrada de trabalho de Streaming Analytics, com "Dados de exemplo de entrada" selecionados](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Isso abrirá uma folha que permite que você especifique quantos dados de exemplo obter definido em termos de quanto tempo necessário para ler o fluxo de entrada.

4. Defina **Minutos** para 3 e, em seguida, clique em **Ok**. 
    
    ![Opções de amostragem de fluxo de entrada, com "3 minutos" selecionados.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    O Azure prova dados do fluxo de entrada de 3 minutos e notifica quando os dados de exemplo estão prontos. (Isso pode levar um tempo.) 

    Os dados de exemplo são armazenados temporariamente e estão disponíveis enquanto a janela de consulta estiver aberta. Se você fechar a janela de consulta, os dados de exemplo serão descartados e você terá que criar um novo conjunto de dados de exemplo. 

5. Altere a consulta no editor de código para a seguinte:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Se não usar `TwitterStream` como o alias para a entrada, substitua o alias para `TwitterStream` na consulta.  

    Essa consulta usa a palavra-chave **TIMESTAMP BY** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não for especificado, a operação em janela será realizada usando a hora em que cada evento chegou ao hub de eventos. Saiba mais na seção “Hora de chegada versus tempo de aplicação” na [Referência de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    A consulta também acessa um carimbo de data/hora para o final de cada janela usando a propriedade **System.Timestamp**.

5. Clique em **Testar**. A consulta é executada em relação aos dados que você amostrou.
    
6. Clique em **Save** (Salvar). Isso salva a consulta como parte do trabalho de Streaming Analytics. (Ele não salva os dados de exemplo.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimente usar diferentes campos do fluxo 

A tabela a seguir lista os campos que fazem parte do fluxo de dados JSON. Fique à vontade para fazer experiências no editor de consultas.

|Propriedade JSON | Definição|
|--- | ---|
|CreatedAt | A hora em que foi criado o tweet|
|Tópico | O tópico que corresponde à palavra-chave especificada|
|SentimentScore | A pontuação de sentimento do Sentiment140|
|Autor | O identificador do Twitter que enviou o tweet|
|Texto | O corpo completo do tweet|


## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora você definiu um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo. A última etapa é definir um coletor de saída para o trabalho.  

Neste guia de instruções, você escreve os eventos de tweet agregados da consulta de trabalho para o armazenamento de BLOBs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure, para Hubs de Eventos ou Power BI, dependendo das suas necessidades de aplicativo.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Na seção **Topologia do Trabalho**, clique na caixa **Saída**. 

2. Na folha **Saídas**, clique em **+&nbsp;Adicionar** e, em seguida, preencha a folha com estes valores:

   * **Alias de saída**: Use o nome `TwitterStream-Output`. 
   * **Coletor**: selecione **Armazenamento de blobs**.
   * **Opções de importação**: Selecione **Usar armazenamento de blobs da assinatura atual**.
   * **Conta de armazenamento**. Selecione **Criar uma nova conta de armazenamento.**
   * **Conta de armazenamento** (segunda caixa). Digite `YOURNAMEsa`, onde `YOURNAME` é o seu nome ou outra cadeia de caracteres exclusiva. O nome pode ter apenas letras minúsculas e números e deve ser exclusivo no Azure. 
   * **Contêiner**. Digite `socialtwitter`.
     O nome da conta de armazenamento e o nome do contêiner são usados juntos para fornecer um URI para o armazenamento de blobs, como este: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Folha "Nova saída" para trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Clique em **Criar**. 

    Azure cria a conta de armazenamento e gera uma chave automaticamente. 

5. Feche a folha **Saídas**. 


## <a name="start-the-job"></a>Iniciar o trabalho

Uma entrada de trabalho, uma consulta e uma saída são especificadas. Você está pronto para iniciar o trabalho do Stream Analytics.

1. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

2. Na folha do trabalho, clique em **Iniciar**.

    ![Iniciar o trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Na folha **Iniciar trabalho**, para **Hora de início de trabalho**, selecione **Agora** e, em seguida, clique em **Iniciar**. 

    ![Folha "Começar trabalho" para trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    O Azure notifica você quando o trabalho for iniciado e, na folha de trabalho, o status é exibido como **Em execução**.

    ![Trabalho em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Exibir saída para análise sentimento

Depois que o trabalho tiver sido iniciado e estiver processando o fluxo do Twitter em tempo real, escolha como você deseja exibir a saída para análise de sentimento.

Use uma ferramenta como o [Azure Storage Explorer](https://storageexplorer.com/) ou o [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. Daqui, você pode usar o [Power BI](https://powerbi.com/) para estender seu aplicativo para incluir um painel personalizado como o demonstrado na seguinte captura de tela:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Criar outra consulta para identificar os tópicos mais populares

Outra consulta que você pode usar para entender o sentimento do Twitter baseia-se em uma [Janela Deslizante](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Para identificar os tópicos mais populares, procuraremos tópicos que ultrapassem um valor limite de menções em determinado período de tempo.

Para os fins deste "como", você verifica os tópicos que são mencionados mais de 20 vezes nos últimos 5 segundos.

1. Na folha de trabalho, clique em **Parar** para interromper o trabalho. 

2. Na seção **Trabalho de Topologia**, clique na caixa **Saída**. 

3. Mude a consulta para o seguinte:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Clique em **Save** (Salvar).

5. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

6. Clique em **Iniciar** para reiniciar o trabalho usando a nova consulta.


## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
