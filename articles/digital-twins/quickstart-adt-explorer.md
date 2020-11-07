---
title: Início Rápido – Explorar um cenário de exemplo
titleSuffix: Azure Digital Twins
description: Início Rápido – Usar o exemplo do ADT Explorer para visualizar e explorar um cenário predefinido.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242272"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Início Rápido – Explorar um cenário de exemplo dos Gêmeos Digitais do Azure usando o ADT Explorer

Com os Gêmeos Digitais do Azure, você pode criar modelos dinâmicos de seus ambientes do mundo real e interagir com eles. Isso é feito modelando elementos individuais como **gêmeos digitais** e conectando-os a um **grafo** de conhecimento que pode responder a eventos ao vivo e ser consultado para obter informações.

Neste guia de início rápido, você explorará um grafo dos Gêmeos Digitais do Azure predefinido, com a ajuda de um aplicativo de exemplo chamado [**ADT (Gêmeos Digitais do Azure) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). O ADT Explorer permite que você carregue uma representação digital de um ambiente, exiba imagens visuais dos gêmeos e do grafo que são criadas para representar o ambiente nos Gêmeos Digitais do Azure e execute outras atividades de gerenciamento por meio de uma experiência visual baseada em navegador.

O guia de início rápido contém as seguintes etapas principais:

1. Configurar uma instância dos Gêmeos Digitais do Azure e do ADT Explorer
1. Carregar modelos predefinidos e dados de grafo para construir o cenário de exemplo
1. Explorar o grafo de cenário criado
1. Fazer as alterações no grafo

O grafo de exemplo com o qual você trabalhará representa um edifício com dois andares e duas salas. O grafo terá esta aparência:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Exibição de um grafo formado por quatro nós circulares conectados por setas. Um círculo rotulado como 'Floor1' está conectado por uma seta rotulada como 'contains' a um círculo rotulado como 'Room1'. Um círculo rotulado como 'Floor0' está conectado por uma seta rotulada como 'contains' a um círculo rotulado como 'Room0'. 'Floor1' e 'Floor0' não estão conectados.":::

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma assinatura do Azure para concluir este guia de início rápido. Se não tiver uma, **[crie-a gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** agora mesmo.

Você também precisará do **Node.js** em seu computador. Você pode obter a versão mais recente neste link: [Node.js](https://nodejs.org/).

Por fim, você também precisará baixar o exemplo a ser usado durante o início rápido: o aplicativo de exemplo **ADT Explorer**. Esse exemplo contém o aplicativo que você usa no guia de início rápido para carregar e explorar um cenário dos Gêmeos Digitais do Azure, bem como os arquivos do cenário de exemplo. Para obter o exemplo, navegue até aqui: [ADT (Gêmeos Digitais do Azure) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Clique no botão *Baixar ZIP* para baixar um arquivo *.ZIP* deste código de exemplo no seu computador. Isso baixa uma pasta .ZIP no seu computador como _**Azure_Digital_Twins__ADT__explorer.zip**_. Descompacte a pasta e extraia os arquivos.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurar os Gêmeos Digitais do Azure e o ADT Explorer

A primeira etapa para trabalhar com os Gêmeos Digitais do Azure é **configurar uma instância dos Gêmeos Digitais do Azure**. Depois de criar uma instância do serviço e **configurar suas credenciais** para se autenticar no ADT Explorer, você poderá **se conectar à instância no ADT Explorer** e preenchê-la com os dados de exemplo mais adiante no guia de início rápido.

O restante desta seção orientará você nessas etapas.

### <a name="set-up-azure-digital-twins-instance"></a>Configurar instância dos Gêmeos Digitais do Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

O aplicativo do ADT Explorer usa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da biblioteca `Azure.Identity`) para autenticar os usuários em uma instância dos Gêmeos Digitais do Azure quando você o executa no computador local. Para obter mais informações sobre diferentes maneiras que um aplicativo cliente pode se autenticar com os Gêmeos Digitais do Azure, confira [*Instruções: como gravar um código de autenticação do aplicativo*](how-to-authenticate-client.md).

Com esse tipo de autenticação, o ADT Explorer procura as credenciais no ambiente local, como um logon do Azure em uma [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou no Visual Studio/Visual Studio Code. Isso significa que você deverá **fazer logon no Azure localmente** por meio de um desses mecanismos para configurar as credenciais do aplicativo do ADT Explorer.

Se você já estiver conectado ao Azure por uma dessas formas, vá para a [próxima seção](#run-and-configure-adt-explorer).

Caso contrário, instale a **CLI do Azure** local com estas etapas:
1. Siga o processo descrito [**neste link de instalação**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para concluir a instalação que corresponde ao seu sistema operacional.
2. Abra uma janela do console no computador.
3. Execute `az login` e siga os avisos de autenticação para fazer logon na sua conta do Azure.

Depois que você fizer isso, o ADT Explorer utilizará suas credenciais do Azure automaticamente quando você o executar na próxima seção.

Feche a janela do console de autenticação se desejar ou mantenha-a aberta para usá-la na próxima etapa.

### <a name="run-and-configure-adt-explorer"></a>Executar e configurar o ADT Explorer

Em seguida, execute o aplicativo ADT Explorer e configure-o para sua instância dos Gêmeos Digitais do Azure.

Navegue até a pasta _**Azure_Digital_Twins__ADT__explorer**_ baixada e descompactada. Abra uma janela do console na localização da pasta *Azure_Digital_Twins__ADT__explorer/client/src*.

Execute `npm install` para baixar todas as dependências necessárias.

Em seguida, inicie o aplicativo executando `npm run start`.

Após alguns segundos, uma janela do navegador será aberta e o aplicativo será exibido no navegador.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Clique no botão *Entrar* na parte superior da janela (mostrada na imagem abaixo) para configurar o ADT Explorer para trabalhar com a instância que você configurou. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer realçando o ícone Entrar próximo à parte superior da janela. O ícone mostra uma silhueta simples de uma pessoa sobreposta com uma silhueta de uma chave." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Insira a *URL da instância dos Gêmeos Digitais do Azure* coletada anteriormente na seção [Pré-requisitos](#prerequisites) no formato *https://{nome do host da instância}* .

>[!NOTE]
> Você pode revisitar/editar essas informações a qualquer momento selecionando o mesmo ícone para abrir a caixa Entrar novamente. Ela manterá os valores que você passou.

> [!TIP]
> Se uma mensagem de erro `SignalRService.subscribe` for mostrada quando você se conectar, verifique se a URL dos Gêmeos Digitais do Azure começa com *https://* .

Se você vir uma janela pop-up *Permissões solicitadas* da Microsoft, dê o consentimento para esse aplicativo e aceite para continuar.

## <a name="add-the-sample-data"></a>Adicionar os dados de exemplo

Em seguida, você importará o cenário e o grafo de exemplo para o ADT Explorer. O cenário de exemplo também está localizado na pasta **Azure_Digital_Twins__ADT__explorer** que você baixou anteriormente.

### <a name="models"></a>Modelos

A primeira etapa em uma solução dos Gêmeos Digitais do Azure é definir o vocabulário do seu ambiente. Isso é feito criando [**modelos**](concepts-models.md) personalizados, que descrevem os tipos de entidade que existem em seu ambiente. 

Cada modelo é escrito em uma linguagem semelhante a JSON-LD chamada **DTDL (Digital Twins Definition Language)** e descreve um tipo único de entidade em termos das *propriedades* , da *telemetria* , das *relações* e dos *componentes* dele. Posteriormente, você usará esses modelos como a base para gêmeos digitais que representam instâncias específicas desses tipos.

Normalmente, ao criar um modelo, você executará três etapas:
1. Escrever a definição do modelo (no guia de início rápido, já feito como parte da solução de exemplo)
2. Validá-la para verificar se a sintaxe é precisa (no guia de início rápido, já feito como parte da solução de exemplo)
3. Carregá-la para a instância de Gêmeos Digitais do Azure
 
Neste guia de início rápido, os arquivos de modelo já foram escritos e validados para você e estão incluídos na solução que você baixou. Nesta seção, você carregará dois modelos pré-gravados para a sua instância a fim de definir esses componentes de um ambiente de criação:
* Piso
* Sala

#### <a name="upload-models"></a>Carregar modelos

Na caixa *EXIBIÇÃO DE MODELO* , clique no ícone *Carregar um Modelo*.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Na caixa Exibição de Modelo, o ícone do meio é realçado. Ele mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Na caixa do seletor de arquivo exibida, navegue até a pasta *Azure_Digital_Twins__ADT__explorer/client/examples* no repositório baixado.
2. Selecione *Room.json* e *Floor.json* e clique em OK. (Você poderá carregar os modelos adicionais se desejar, mas eles não serão usados neste guia de início rápido.)
3. Siga a caixa de diálogo pop-up solicitando que você entre na sua conta do Azure.

>[!NOTE]
>Se você vir a seguinte mensagem de erro: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Uma pop-up informando &quot;Erro: erro ao buscar os modelos: ClientAuthError: erro ao abrir a janela pop-up. Isso poderá acontecer se você estiver usando o IE ou se os pop-ups estiverem bloqueados no navegador com um botão Fechar na parte inferior" border="false"::: 
> Tente desabilitar o bloqueador de pop-ups ou usar um navegador diferente.

Agora, o ADT Explorer carregará esses arquivos de modelo na instância dos Gêmeos Digitais do Azure. Eles devem aparecer na caixa *EXIBIÇÃO DE MODELO* , exibindo os nomes amigáveis e as IDs de modelo completo. Você pode clicar nas bolhas de informações do *Modelo de Exibição* para ver o código DTDL por trás deles.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Uma exibição da caixa &quot;Exibição de Modelo&quot; com duas definições de modelo listadas, Andar (dtmi:example:Floor;1) e Cômodo (dtmi:example:Room;1). O ícone &quot; Exibir modelo&quot; mostrando uma letra &quot;i&quot; em um círculo é realçado para cada modelo." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Gêmeos e o grafo de gêmeos

Agora que alguns modelos foram carregados para sua instância dos Gêmeos Digitais do Azure, você pode adicionar [**gêmeos digitais**](concepts-twins-graph.md) que seguem as definições do modelo. 

Os gêmeos digitais representam as entidades reais dentro do seu ambiente de negócios: coisas como sensores em uma fazenda, luzes em um carro ou, neste início rápido, cômodos em um andar de prédio. Você pode criar muitos gêmeos de qualquer tipo de modelo específico (como vários cômodos que usam o modelo *Cômodo* ) e conectá-los a relações em um **grafo de gêmeos** que representa o ambiente completo.

Nesta seção, você carregará gêmeos criados previamente que estão conectados a um grafo criado previamente. O grafo contém dois andares e dois cômodos, conectados no seguinte layout:
* *Floor0*
    - contém *Room0*
* *Floor1*
    - contém *Room1*

#### <a name="import-the-graph"></a>Importar o grafo

Na caixa *EXIBIÇÃO DE GRAFO* , clique no ícone *Importar Grafo*.

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Na caixa Exibição de Grafo, um ícone é realçado. Ele mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/import-graph.png":::

Na caixa do seletor de arquivo, navegue até a pasta *Azure_Digital_Twins__ADT__explorer/client/examples* e escolha o arquivo de planilha _**buildingScenario.xlsx**_. Esse arquivo contém uma descrição do grafo de exemplo. Clique em OK.

Depois de alguns segundos, o ADT Explorer abrirá uma exibição *Importar* mostrando uma visualização do grafo que será carregado.

Para confirmar o upload do grafo, clique no ícone *Salvar* no canto superior direito da *EXIBIÇÃO DE GRAFO* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Como realçar o ícone Salvar no painel Visualização do Grafo" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O ADT Explorer agora usará o arquivo carregado para criar os gêmeos e as relações solicitadas entre eles. Uma caixa de diálogo será exibida para mostrar que o processo foi concluído. Clique em *Fechar*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Caixa de diálogo indicando êxito na importação de grafo. Ela contém a mensagem &quot;Importação bem-sucedida. Quatro gêmeos importados. Duas relações importadas.'" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O grafo agora foi carregado para o ADT Explorer. Para ver o grafo, clique no botão *Executar Consulta* na caixa *EXPLORADOR DO GRAPH* , próximo à parte superior da janela do ADT Explorer. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Um botão com a mensagem &quot;Executar Consulta&quot; próxima à parte superior da janela está realçado" lightbox="media/quickstart-adt-explorer/run-query.png":::

Isso executará a consulta padrão para selecionar e exibir todos os gêmeos digitais. O ADT Explorer recuperará todos os gêmeos e relações do serviço e desenhará o grafo definido por eles na caixa *EXIBIÇÃO DE GRAFO*.

## <a name="explore-the-graph"></a>Explorar o grafo

Agora, você pode ver o grafo carregado do cenário de exemplo:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Exibição da caixa &quot;Exibição de Grafo&quot; com um grafo. Um círculo rotulado como &quot;floor1&quot; está conectado por uma seta rotulada como &quot;contains&quot; a um círculo rotulado como &quot;room1&quot;; um círculo rotulado como &quot;floor0&quot; está conectado por uma seta rotulada como &quot;contains&quot; a um círculo rotulado como &quot;room0&quot;.":::

Os círculos ("nós" de grafo) representam gêmeos digitais e as linhas representam relações. Você verá que o gêmeo *Floor0* contém *Room0* e que o gêmeo *Floor1* contém *Room1*.

Se você estiver usando um mouse, poderá clicar e arrastar partes do grafo para movê-las.

### <a name="view-twin-properties"></a>Exibir propriedades do gêmeo 

Você pode selecionar um gêmeo para ver uma lista das propriedades e dos valores dele na caixa *GERENCIADOR DE PROPRIEDADES*. 

Aqui estão as propriedades de *Room0* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Realce em torno da caixa &quot;Gerenciador de Propriedades&quot; mostrando as propriedades de Room0, incluindo, entre outros, um campo $dtId de &quot;Room0&quot;, um campo Temperatura de 70 e um campo Umidade de 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Observe que *Room0* tem uma temperatura de **70**.

Aqui estão as propriedades de *Room1* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Realce em torno da caixa &quot;Gerenciador de Propriedades&quot; mostrando as propriedades de Room1, incluindo, entre outros, um campo $dtId de &quot;Room1&quot;, um campo Temperatura de 80 e um campo Umidade de 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Observe que *Room1* tem uma temperatura de **80**.

### <a name="query-the-graph"></a>Consultar o grafo

Um dos principais recursos dos Gêmeos Digitais do Azure é a capacidade de [consultar](concepts-query-language.md) o grafo de gêmeos com facilidade e eficiência para responder às perguntas sobre o seu ambiente. 

Uma forma de consultar os gêmeos em seu grafo é pelas *propriedades* deles. A consulta com base nas propriedades para responder a uma variedade de perguntas, incluindo a localização de exceções no ambiente que podem precisar de atenção.

Nesta seção, você executará uma consulta para responder à seguinte pergunta: _**Quais são todos os gêmeos em meu ambiente com temperatura acima de 75?**_

Para ver a resposta, execute a seguinte consulta na caixa *GERENCIADOR DE CONSULTAS* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Lembre-se que, na exibição das propriedades do gêmeo anteriormente, *Room0* tinha uma temperatura de **70** e *Room1* tinha uma temperatura de **80**. Como resultado, somente _**Room1**_ é exibido nos resultados aqui.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultados da consulta de propriedade, mostrando apenas Room1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Também há suporte para outros operadores de comparação ( *<* , *>* , *=* ou *!=* ) dentro da consulta acima. Você pode tentar conectar esses valores diferentes ou propriedades de mesclagem diferentes à consulta para tentar responder às suas perguntas.

## <a name="edit-data-in-the-graph"></a>Editar dados no grafo

Você pode usar o ADT Explorer para editar as propriedades dos gêmeos representados em seu grafo. Nesta seção, **_aumentaremos a temperatura de_ Room0 para 76**.

Para fazer isso, selecione *Room0* , trazendo a lista de propriedades na caixa *GERENCIADOR DE PROPRIEDADES*.

As propriedades nessa lista são editáveis. Selecione o valor de temperatura de **70** para habilitar a inserção de um novo valor. Digite **76** e clique no ícone *Salvar* para atualizar a temperatura para **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="A caixa &quot;Gerenciador de Propriedades&quot; mostrando as propriedades de Room0. O valor de temperatura é uma caixa editável exibindo 76 e há um realce ao redor do ícone Salvar." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Após salvar com êxito, você verá uma janela *Informações de Patch* exibindo o código do patch que foi usado nos bastidores com as [APIs](how-to-use-apis-sdks.md) dos Gêmeos Digitais do Azure para fazer a atualização. Clique em *Fechar*.

### <a name="query-to-see-the-result"></a>Consultar para ver o resultado

Para verificar se o grafo registrou com êxito sua atualização para a temperatura de *Room0* , execute novamente a consulta anterior para **obter todos os gêmeos no ambiente com uma temperatura acima de 75** :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Agora que a temperatura da *Room0* foi alterada de **70** para **76** , os dois gêmeos devem aparecer no resultado.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultados da consulta de propriedade, mostrando Room0 e Room1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Examinar e contextualizar aprendizados

Neste guia de início rápido, você criou uma instância dos Gêmeos Digitais do Azure, conectou-a ao ADT Explorer e a preencheu com um cenário de exemplo. 

Depois, você explorou o grafo...
1. Usando uma consulta para responder a uma pergunta sobre o cenário.
2. Editando uma propriedade em um gêmeo digital.
3. Executando a consulta novamente para ver como a resposta foi alterada em decorrência da sua atualização.

A intenção deste exercício é demonstrar como você pode usar o grafo dos Gêmeos Digitais do Azure para responder a perguntas sobre seu ambiente, mesmo conforme o ambiente continue mudando. 

Embora, neste guia de início rápido, você tenha atualizado a temperatura manualmente, é comum, nos Gêmeos Digitais do Azure, conectar gêmeos digitais a dispositivos IoT reais para que eles recebam atualizações automaticamente, com base nos dados de telemetria. Isso permite criar um grafo ao vivo que sempre reflete o acervo real do seu ambiente e usar consultas para obter informações sobre o que está acontecendo em seu ambiente em tempo real.

## <a name="clean-up-resources"></a>Limpar os recursos

Para concluir o trabalho deste guia de início rápido, primeiro finalize o aplicativo de console em execução. Isso desligará a conexão com o aplicativo ADT Explorer no navegador e você não poderá mais exibir dados dinâmicos no navegador. Feche a guia do navegador.

Se você planeja continuar com os tutoriais dos Gêmeos Digitais do Azure, a instância usada neste guia de início rápido pode ser reutilizada para esses artigos e você não precisa removê-la.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Por fim, exclua as pastas de exemplo do projeto que você baixou no computador local ( _**Azure_Digital_Twins__ADT__explorer**_ ). Talvez seja necessário excluir as versões zipadas e descompactadas.

## <a name="next-steps"></a>Próximas etapas 

Em seguida, prossiga para os tutoriais dos Gêmeos Digitais do Azure para desenvolver o próprio cenário e as ferramentas de interação dos Gêmeos Digitais do Azure.

> [!div class="nextstepaction"]
> [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)