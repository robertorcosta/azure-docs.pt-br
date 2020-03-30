---
title: Como consultar a pesquisa cognitiva do Azure a partir de aplicativos de energia
titleSuffix: Azure Cognitive Search
description: Orientação passo a passo sobre como criar conector personalizado para a Pesquisa Cognitiva e como visualizá-lo a partir de um Power App
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385105"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Como consultar um índice de pesquisa cognitiva de power apps

Este documento mostra como criar um conector personalizado do Power Apps para que você possa recuperar os resultados da pesquisa a partir de um índice de pesquisa. Ele também mostra como emitir uma consulta de pesquisa e visualizar os resultados de um Power App. 

## <a name="prerequisites"></a>Pré-requisitos
*    Acesso à conta do Power Apps com a capacidade de criar conectores personalizados.
*    Presumimos que você já criou um Índice de Pesquisa Do Azure.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Crie um conector personalizado para consultar o Azure Search

Existem dois passos principais para ter um PowerApp que mostra os resultados da Pesquisa Cognitiva do Azure. Primeiro, vamos criar um conector que possa consultar o índice de pesquisa. Na [próxima seção](#visualize-results-from-the-custom-connector) atualizaremos seu aplicativo Power Apps para visualizar os resultados retornados pelo conector.

1. Vá para [make.powerapps.com](http://make.powerapps.com) e **Faça Login**.

1. Procure **Data** > **conectores personalizados de** dados
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu de conector personalizado" border="true":::

1. Clique **em + Novo conector personalizado** e selecione Criar em **branco**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Criar a partir do menu em branco" border="true":::

1. Dê um nome ao seu conector personalizado. (ou seja, *AzureSearchQuery),* e clique em **Continuar**. Isso trará um assistente para criar seu novo conector.

1. Insira informações na Página Geral.

    - Cor de fundo do ícone (por exemplo, #007ee5)
    - Descrição (por exemplo, "Um conector para a pesquisa cognitiva do Azure")
    - No Host, você precisará inserir o URL do `<yourservicename>.search.windows.net`seu serviço de pesquisa (por exemplo, )
    - Para URL base, basta digitar "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Diálogo geral de informações" border="true":::

1. Na página de segurança, defina a *chave de API* como **o tipo de autenticação,** defina o rótulo de parâmetro e os campos de nome do parâmetro como *api-key*. Para **a localização de Parâmetro,** selecione *Cabeçalho* como mostrado abaixo.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opção de tipo de autenticação" border="true":::

1. Na Página Definições, selecione **+ Nova ação** para criar uma ação que consultará o índice. Digite o valor "Consulta" para o resumo e o nome do ID da operação. Digite uma descrição como *"Consulta o índice de pesquisa"*.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Novas opções de ação" border="true":::


1. Pressione o **botão + Importar do** botão de amostra para definir os parâmetros e os cabeçalhos. Em seguida, você definirá a solicitação de consulta.  

    * Selecione o verbo`GET`
    * Para a URL, digite uma consulta de exemplo para o seu índice de pesquisa, por exemplo:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-version=2019-05-06-Preview
    

    **Os Power Apps** usarão a sintaxe para extrair parâmetros da consulta. Observe que definimos explicitamente o campo de pesquisa. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importar da amostra" border="false":::

1.  Clique **em Importar** para preencher automaticamente a caixa de diálogo Solicitar.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importação do diálogo amostral" border="false":::


1. Complete a configuração dos metadados do parâmetro clicando **no ...** símbolo ao lado de cada um dos parâmetros.

    - Para *pesquisa* `*` : Defina como o **valor padrão,** defina **como** *falso* e defina a **visibilidade** como *nenhuma*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadados de parâmetrode pesquisa" border="true":::

    - Para *a versão api*: `2019-05-06-Preview` Defina como o valor **padrão,** defina a **visibilidade** como interna e definida **como** *True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadados do parâmetro de versão" border="true":::

    - Da mesma forma, para *api-key,* defina-a conforme **necessário,** com **visibilidade** *interna.* Digite a chave API do serviço de pesquisa como o **valor padrão**.
    
    Depois de fazer essas alterações, alterne para a exibição **do Editor de Swagger.** Na seção parâmetros, você deve ver a seguinte configuração:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Na seção Resposta, clique em **"Adicionar resposta padrão"**. Isso é fundamental porque ajudará os **Power Apps** a entender o esquema da resposta. Cole uma resposta amostral.

    > [!TIP] 
    > Há um limite de caracteres para a resposta JSON que você pode inserir, então você pode querer simplificar o JSON para que ele antes de colá-lo. O importante esquema de aspecto/formato da resposta. Os valores reais na resposta amostral são menos importantes e podem ser simplificados para reduzir a contagem de caracteres.
    

1.    Clique no botão **Criar conector** no canto superior direito da tela antes de testá-lo.

1.  Na Página de teste, clique em **+ Nova conexão**e digite a tecla de consulta do serviço de pesquisa como o valor *para aapi-key*.

    Esta etapa pode levá-lo para fora do assistente e para a página Conexões. Você pode querer voltar ao editor de Conexões Personalizadas para realmente testar a conexão. Vá para **o conector personalizado** > Selecione o conector recém-criado > *...* > **Exibir propriedades** > **Edit** > **4. Teste** para voltar à página de teste.

1.    Agora clique em Testar a **operação** para ter certeza de que você está recebendo resultados do seu índice. Se você teve sucesso, você deve ver um status de 200, e no corpo da resposta você deve ver JSON que descreve seus resultados de pesquisa.




## <a name="visualize-results-from-the-custom-connector"></a>Visualize os resultados do conector personalizado
O objetivo deste tutorial não é mostrar como criar experiências de usuário extravagantes com aplicativos de energia, para que o layout de Interface do Usuário seja minimalista. Vamos criar um PowerApp com uma caixa de pesquisa, um botão de pesquisa e exibir os resultados em um controle de galeria.  O PowerApp se conectará ao nosso conector personalizado recentemente criado para obter os dados do Azure Search.

1. Crie um novo Power App. Vá para a seção **Aplicativos,** clique em **+ Novo aplicativo**e selecione **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Criar aplicativo de tela" border="true":::

1. Selecione o tipo de aplicativo que você gostaria. Para este tutorial crie um **aplicativo em branco** com o layout do **telefone**. O **Power Apps Studio** será exibido.

1. Uma vez no estúdio, selecione a guia **Fontes de dados** e clique no novo Conector que você acabou de criar. No nosso caso, chama-se *AzureSearchQuery*. Clique **em Adicionar uma conexão**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="conector de conexão" border="true":::

    Agora, *o AzureSearchQuery* é uma fonte de dados que está disponível para ser usada a partir de seu aplicativo.
    
1. Navegue até a **guia Inserir,** para que possamos adicionar alguns controles à nossa forma.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserir controles" border="true":::

1.  Insira os seguintes elementos:
    -   Um rótulo de texto com o valor "Consulta:"
    -   Um elemento de entrada de texto (chamá-lo *de txtQuery*, valor padrão: "*")
    -   Um botão com o texto "Pesquisar" 
    -   Uma Galeria Vertical chamada (chame de *galeriaResultados*)
    
    Seu formulário deve ser mais ou menos assim:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layout de controles" border="true":::

1. Para fazer com que o **botão Pesquisar** uma consulta, selecione o botão e cole a seguinte ação a seguir para assumir **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Botão onselect" border="true":::
 
    Essa ação fará com que o botão atualize uma nova coleção chamada *azResult* com o resultado da consulta de pesquisa, usando o texto na caixa de texto *txtQuery* como o termo consulta.
    
1.  Como próximo passo, vamos vincular a galeria vertical que criamos à coleção *azResult.* Selecione o controle da galeria e execute as seguintes ações no painel de propriedades.

    -  Defina **dataSource** *para azResult*.
    
    -  Selecione um **layout** que funcione para você com base no tipo de dados do seu índice. Neste caso, usamos o *Título, legenda e* layout do corpo.
    
    -  **Editar campos**e selecionar os campos que você deseja visualizar.

    Uma vez que fornecemos um resultado amostral quando definimos o conector, o aplicativo está ciente dos campos disponíveis em seu índice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campos de galeria" border="true":::   
 
1.  Pressione **F5** para visualizar o aplicativo.  

    Lembre-se que os campos podem ser definidos como valores calculados.      
    Por exemplo, a configuração usando o layout *"Imagem, Título e Legenda"* e especificando a função *Imagem* como a `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`concatenação do caminho raiz dos dados e do nome do arquivo (por exemplo, ) produzirá o resultado abaixo.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Aplicativo final" border="true":::        

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e treinamento online, consulte [Power Apps Learning Catalog](https://docs.microsoft.com/powerapps/learning-catalog/get-started).

