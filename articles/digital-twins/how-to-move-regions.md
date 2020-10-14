---
title: Mover instância para uma região do Azure diferente
titleSuffix: Azure Digital Twins
description: Consulte como mover uma instância de gêmeos digital do Azure de uma região do Azure para outra.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 3c7f9ed9558adc9d129d1df767a05aff1fa4c66c
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047379"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Mover uma instância do gêmeos digital do Azure para uma região diferente do Azure

Se você precisar mover sua instância do gêmeos digital do Azure de uma região para outra, o processo atual será **recriar os recursos na nova região**e, em seguida, excluir os recursos originais. No final desse processo, você trabalhará com uma nova instância de gêmeos digital do Azure que é idêntica à primeira, exceto para o local atualizado.

Este artigo fornece orientação sobre como fazer uma movimentação completa, copiando tudo o que será necessário para que a nova instância corresponda ao original.

Este processo inclui as seguintes etapas:
1. Preparar: Baixe seus modelos, gêmeos e grafo originais.
2. Mover: Crie uma nova instância de gêmeos digital do Azure em uma nova região.
3. Mover: repopular a nova instância de gêmeos digital do Azure.
    - Carregar modelos, gêmeos e grafo originais.
    - Recriar pontos de extremidade e rotas.
    - Vincule novamente os recursos conectados.
4. Limpar recursos de origem: exclua a instância original.

## <a name="prerequisites"></a>Pré-requisitos

Antes de tentar recriar sua instância do gêmeos digital do Azure, é uma boa ideia passar pelos componentes da sua instância original e ter uma ideia clara de todas as partes que precisarão ser recriadas.

Aqui estão algumas perguntas que talvez você queira considerar:
* Quais são os **modelos** carregados na minha instância? Quantas existem?
* Quais são os **gêmeos** na minha instância? Quantas existem?
* Qual é a forma geral do **grafo** na minha instância? Quantas relações existem?
* Quais **pontos de extremidade** eu tenho em minha instância?
* Quais **rotas** tenho em minha instância? Eles têm filtros?
* Onde a minha instância **se conecta a outros serviços do Azure**? Alguns pontos de integração comuns incluem...
    - Grade de eventos, Hub de eventos ou barramento de serviço
    - Funções do Azure
    - Aplicativos Lógicos
    - Time Series Insights
    - Mapas do Azure
    - Serviço de provisionamento de dispositivos (DPS)
* Quais outros **aplicativos pessoais ou da empresa** tenho que se conectem à minha instância?

Você pode coletar essas informações usando o [portal do Azure](https://portal.azure.com), [APIs e SDKs do gêmeos digital do Azure](how-to-use-apis-sdks.md), [comandos da CLI do gêmeos digital do Azure](how-to-use-cli.md)ou o exemplo do [Explorer do Azure digital gêmeos (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Preparar

Nesta seção, você se preparará para recriar sua instância **baixando seus modelos, gêmeos e grafo originais** de sua instância original. Este artigo faz isso usando o exemplo do [Gerenciador de gêmeos (ADT) do Azure digital](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

>[!NOTE]
>Talvez você já tenha arquivos que contenham os modelos e/ou o grafo em sua instância. Nesse caso, não é necessário baixar tudo novamente — apenas as partes que você está perdendo ou as coisas que podem ter mudado desde que você carregou originalmente esses arquivos (como gêmeos que podem ter sido atualizados com novos dados).

### <a name="limitations-of-adt-explorer"></a>Limitações do ADT Explorer

O [exemplo do Gerenciador do gêmeos (ADT) do Azure digital](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) é um exemplo de aplicativo cliente que dá suporte a uma representação visual do grafo e fornece interação visual com sua instância. Este artigo mostra como usá-lo para baixar e, posteriormente, carregar novamente, seus modelos, gêmeos e grafos.

Observe, no entanto, que esse é um **exemplo** e não uma ferramenta completa. Ele não foi testado com estresse e não foi criado para lidar com grafos de um grande tamanho. Consequentemente, tenha em mente as seguintes limitações de exemplo prontas para uso:
* No momento, o exemplo só foi testado em tamanhos de grafo de até 1000 nós e 2000 relações
* O exemplo não dá suporte à repetição no caso de falhas intermitentes
* O exemplo não notificará, necessariamente, o usuário se os dados carregados estiverem incompletos
* O exemplo não trata erros resultantes de grafos muito grandes que excedem os recursos disponíveis, como a memória

Se o exemplo não for capaz de lidar com o tamanho do grafo, você poderá exportar e importar o grafo usando outras ferramentas de desenvolvedor de gêmeos digitais do Azure:
* [Comandos da CLI do gêmeos digital do Azure](how-to-use-cli.md)
* [SDKs e APIs do Azure digital gêmeos](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Configurar o aplicativo ADT Explorer

Para prosseguir com o ADT Explorer, primeiro Baixe o código do aplicativo de exemplo e configure-o para ser executado em seu computador. 

Navegue até o exemplo aqui: [Gerenciador de gêmeos digital do Azure (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Clique no botão *Baixar o ZIP* para baixar um arquivo *.ZIP* deste código de exemplo em seu computador como _**ADT_Explorer.zip**_. Descompacte o arquivo.

Em seguida, configure as permissões para que o ADT Explorer seja executado em seu computador. Para fazer isso, siga as etapas na seção [*set ADT Explorer Permissions*](quickstart-adt-explorer.md#set-adt-explorer-permissions) do início rápido do Azure digital gêmeos.

Por fim, execute e configure o ADT Explorer para se conectar à instância original do Azure digital gêmeos. Siga as etapas na seção [*executar e configurar o ADT Explorer*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) do guia de início rápido.

Agora você deve ter o aplicativo de exemplo do ADT Explorer em execução em um navegador em seu computador. O exemplo deve ser conectado à instância original do Azure digital gêmeos.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/explorer-blank.png":::

Para verificar a conexão, você pode clicar no botão *Executar consulta* para executar a consulta padrão que exibe todas as gêmeos e relações no grafo na caixa *Gerenciador de gráficos* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/run-query.png":::

Você pode deixar o ADT Explorer em execução, pois você o usará novamente mais tarde neste artigo para carregar novamente esses itens na nova instância na região de destino.

### <a name="download-models-twins-and-graph"></a>Baixar modelos, gêmeos e grafo

Em seguida, baixe os modelos, o gêmeos e o grafo em sua solução para seu computador.

Para baixar todos eles de uma só vez, primeiro verifique se o grafo completo está aparecendo na caixa de *exibição de gráfico* (você pode fazer isso executando novamente a consulta padrão de `SELECT * FROM digitaltwins` na caixa *Gerenciador de consultas* ).
 
Em seguida, clique no ícone *Exportar grafo* na caixa *exibição do gráfico* .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/export-graph.png":::

Isso habilitará um link de *Download* no *modo de exibição de gráfico*. Selecione-o para baixar uma representação baseada em JSON do resultado da consulta, incluindo seus modelos, gêmeos e relações. Isso deve baixar um arquivo *. JSON* em seu computador.

>[!NOTE]
>Se o arquivo baixado parecer ter uma extensão de arquivo diferente, tente editar a extensão diretamente e alterá-la para *. JSON*.

## <a name="move"></a>Mover

Em seguida, você concluirá a "movimentação" de sua instância criando uma nova instância na região de destino e preenchendo-a com os dados e componentes da sua instância original.

### <a name="create-a-new-instance"></a>Criar uma nova instância

Primeiro, **crie uma nova instância do Azure digital gêmeos em sua região de destino**. Para fazer isso, siga as etapas em [*como: configurar uma instância e autenticação*](how-to-set-up-instance-portal.md), mantendo esses ponteiros em mente:
* Você pode manter o mesmo nome para a nova instância **se** ela estiver em um grupo de recursos diferente. Se você precisar usar o mesmo grupo de recursos que contém sua instância original, sua nova instância precisará de seu próprio nome distinto.
* Insira a nova região de destino quando for solicitada uma localização.
* Você **não precisa** recriar o registro do aplicativo. Sua nova instância pode reutilizar o mesmo registro de aplicativo que você já tem.
    - Se estiver usando o artigo de instalação com [script](how-to-set-up-instance-scripted.md) , você poderá inserir novamente os detalhes do registro do aplicativo existente em vez de inserir um novo nome quando solicitado.
    - Se estiver usando o [portal](how-to-set-up-instance-portal.md) manual ou os artigos de instalação da [CLI](how-to-set-up-instance-cli.md) , você poderá parar após a *criação da instância do Azure digital gêmeos* e configurar as etapas de *permissões de acesso do usuário* . Não é necessário continuar com a *configuração de permissões de acesso para aplicativos cliente*.

Quando isso for concluído, você precisará do **nome de host** da sua nova instância para continuar a configurá-lo com seus dados. Se você não fez uma observação sobre isso durante a instalação, você pode seguir [estas instruções](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) para obtê-las agora na portal do Azure.

### <a name="repopulate-old-instance"></a>Repopular instância antiga

Em seguida, você irá configurar a nova instância para que ela seja uma cópia do original.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Carregar modelos, gêmeos e grafo originais usando o ADT Explorer

Nesta seção, você pode carregar novamente seus modelos, gêmeos e grafo para a nova instância. Se você não tiver modelos, gêmeos ou grafos em sua instância original ou não quiser movê-los para a nova instância, poderá pular para a [próxima seção](#recreate-endpoints-and-routes).

Caso contrário, para continuar, retorne à janela do navegador executando o **ADT Explorer** e siga as etapas abaixo.

##### <a name="connect-to-the-new-instance"></a>Conectar-se à nova instância

Atualmente, o ADT Explorer está conectado à instância original do Azure digital gêmeos. Alterne a conexão para apontar para a nova instância pressionando o botão *entrar* na parte superior da janela. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/sign-in.png":::

Como você está reutilizando o registro do aplicativo, você só precisa substituir a *URL ADT*. Altere esse valor para que ele leia *https://{nova instância hostname}*.

Clique em *conectar*. Você pode ser solicitado a fazer logon novamente com suas credenciais do Azure e/ou conceder a este consentimento de aplicativo para sua instância.

##### <a name="upload-models-twins-and-graph"></a>Carregar modelos, gêmeos e grafo

Em seguida, carregue os componentes da solução que você baixou anteriormente para sua nova instância.

Para carregar seus **modelos, gêmeos e grafo**, clique no ícone *importar grafo* na caixa de *exibição de gráfico* . Esta opção carregará todos os três componentes de uma vez (mesmo modelos que não estão sendo usados atualmente no grafo).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/import-graph.png":::

Na caixa seletor de arquivo, navegue até o grafo baixado. Selecione o arquivo Graph *. JSON* e clique em *abrir*.

Depois de alguns segundos, o ADT Explorer abrirá uma exibição *Importar* mostrando uma visualização do grafo que será carregado.

Para confirmar o upload do grafo, clique no ícone *Salvar* no canto superior direito da *EXIBIÇÃO DE GRAFO*:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Agora, o ADT Explorer carregará seus modelos e o grafo (incluindo o gêmeos e as relações) para sua nova instância do Azure digital gêmeos. Você deverá ver uma mensagem de êxito indicando quantos modelos, gêmeos e relações foram carregados:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Para verificar se tudo foi carregado com êxito, clique no botão *Executar consulta* na caixa *Gerenciador de gráficos* para executar a consulta padrão que exibe todas as gêmeos e relações no grafo. Isso também atualizará a lista de modelos no *modo de exibição de modelo*.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/run-query.png":::

Você deve ver seu grafo com todas as suas gêmeos e relações exibidas na caixa *Explorador do Graph* . Você também deve ver seus modelos listados na caixa *exibição de modelo* .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/post-upload.png":::

Isso confirma que seus modelos, gêmeos e grafo foram carregados novamente para a nova instância na região de destino.

#### <a name="recreate-endpoints-and-routes"></a>Recriar pontos de extremidade e rotas

Se você tiver **pontos de extremidade e/ou rotas** em sua instância original, precisará recriá-los em sua nova instância. Se você não tiver pontos de extremidade ou rotas em sua instância original ou não quiser movê-los para a nova instância, poderá pular para a [próxima seção](#re-link-connected-resources).

Caso contrário, continue, siga as etapas em [*como gerenciar pontos de extremidade e rotas*](how-to-manage-routes-portal.md) usando a nova instância, mantendo esses ponteiros em mente: 
* Você **não precisa** recriar a grade de eventos, o Hub de eventos ou o recurso do barramento de serviço que você está usando para o ponto de extremidade (seções de*pré-requisito* nas instruções do ponto de extremidade). Você só precisa recriar o ponto de extremidade na instância do gêmeos digital do Azure.
* Você pode reutilizar os **nomes**de ponto de extremidade e de rota, já que eles estão no escopo de uma instância diferente.
* Lembre-se de adicionar todos os **filtros** necessários às rotas que você criar.

#### <a name="re-link-connected-resources"></a>Vincular novamente os recursos conectados

Se tiver outros aplicativos ou recursos do Azure conectados à instância original do Azure digital gêmeos, você precisará editar a conexão para que ela alcance sua nova instância. Isso pode incluir outros serviços do Azure ou aplicativos pessoais ou da empresa que você configurou para trabalhar com o gêmeos digital do Azure.

Se você não tiver outros recursos conectados à sua instância original ou não quiser movê-los para a nova instância, poderá pular para a [próxima seção](#verify).

Caso contrário, para continuar, considere os recursos conectados em seu cenário. Você não precisa excluir e recriar os recursos conectados; em vez disso, você só precisa editar os pontos onde eles se conectam a uma instância do gêmeos digital do Azure por meio de seu **nome de host**e atualizá-lo para usar o nome do host da nova instância em vez do original.

Os recursos exatos que você precisa editar dependem do seu cenário, mas aqui estão alguns pontos de integração comuns:
* Azure Functions. Se você tiver uma função do Azure cujo código inclui o nome do host da instância original, deverá atualizar esse valor para o nome de host da nova instância e publicar novamente a função.
* Grade de eventos, hubs de eventos ou barramento de serviço
* Aplicativos Lógicos
* Time Series Insights
* Mapas do Azure
* Serviço de provisionamento de dispositivos (DPS)
* Aplicativos pessoais ou da empresa fora do Azure, como o **aplicativo cliente** criado no [*tutorial: codificar um aplicativo cliente*](tutorial-code.md), que se conecta à instância e chamar as APIs do Azure digital gêmeos

Depois de concluir essa etapa, sua nova instância na região de destino deverá ser uma cópia da instância original.

## <a name="verify"></a>Verificar

Para verificar se a nova instância foi configurada corretamente, você pode usar as seguintes ferramentas:
* O [**portal do Azure**](https://portal.azure.com) (bom para verificar se a nova instância existe e está na região de destino correta; também é bom para verificar pontos de extremidade e rotas e conexões com outros serviços do Azure)
* Os [ **comandos da CLI** do Azure digital gêmeos](how-to-use-cli.md) (bom para verificar se a nova instância existe e está na região de destino correta; também pode ser usado para verificar os dados da instância)
* [**ADT Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (bom para verificar dados de instância, como modelos, gêmeos e grafo)
* Os [SDKs e APIs do gêmeos digital do Azure](how-to-use-apis-sdks.md) (bom para verificar dados de instância, como modelos, gêmeos e grafo; também é bom para verificar pontos de extremidade e rotas)

Você também pode tentar executar qualquer aplicativo personalizado ou fluxos de ponta a ponta que você tenha executado com sua instância original, para ajudá-lo a verificar se eles estão trabalhando corretamente com a nova instância.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Agora que a nova instância está configurada na região de destino com uma cópia dos dados e das conexões da instância original, você pode **excluir a instância original**.

Você pode fazer isso no [portal do Azure](https://portal.azure.com), com a [CLI](how-to-use-cli.md)ou com as APIs do [plano de controle](how-to-use-apis-sdks.md#overview-control-plane-apis).

Para excluir a instância usando o portal do Azure, [abra o portal](https://portal.azure.com) em uma janela do navegador e navegue até a instância original do Azure digital gêmeos pesquisando seu nome na barra de pesquisa do Portal.

Pressione o botão *excluir* e siga os prompts para concluir a exclusão.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela.":::