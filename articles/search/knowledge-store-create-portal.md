---
title: Criar um repositório de conhecimento no portal do Azure
titleSuffix: Azure Cognitive Search
description: Use o assistente de Importação de dados para criar um repositório de conhecimento para persistir conteúdo enriquecido. Conecte-se a um repositório de conhecimento para obter análise de outros aplicativos ou enviar conteúdo enriquecido para processos downstream.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: d714e913d5e03233ed3ffcaaebca6eb989a56bd7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790042"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Início Rápido: Criar um repositório de conhecimento da Pesquisa Cognitiva do Azure no portal do Azure

> [!Note]
> O armazenamento de dados de conhecimento está em versão prévia e não é destinado a uso em produção. O portal do Azure e a [API REST do Search versão 2019-05-06-Preview](search-api-preview.md) fornecem esse recurso. Não há suporte para SDK do .NET no momento.
>

O repositório de conhecimento é um recurso da Pesquisa Cognitiva do Azure que persiste a saída de um pipeline de habilidades cognitivas para análise posterior ou outro processamento downstream. 

Um pipeline aceita imagens e texto não estruturado como conteúdo bruto, aplica IA por meio dos Serviços Cognitivos (como processamento de imagem e linguagem natural) e cria conteúdo enriquecido (novas estruturas e informações) como saída. Um dos artefatos físicos criados por um pipeline é um [repositório de conhecimento](knowledge-store-concept-intro.md), que pode ser acessado por meio de ferramentas para analisar e explorar o conteúdo.

Neste início rápido, você combinará serviços e dados na nuvem do Azure para criar um repositório de conhecimento. Quando tudo estiver configurado, você executará o assistente **Importar dados** no portal para reunir tudo. O resultado final é um conteúdo original gerado por IA que você pode exibir no portal ([Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md)).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este início rápido usa a Pesquisa Cognitiva do Azure, o Armazenamento de Blobs do Azure e os [Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/) para IA. 

Como a carga de trabalho é muito pequena, os Serviços Cognitivos são acionados nos bastidores para fornecer processamento gratuito para até 20 transações diárias quando invocados pela Pesquisa Cognitiva do Azure. Desde que use os dados de exemplo que fornecemos, você pode ignorar a criação ou anexação de um recurso dos Serviços Cognitivas.

1. [Baixe HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Esses são dados de resenhas de hotel salvos em um arquivo CSV (originário do Kaggle.com) que contêm 19 resenhas de clientes de um hotel. 

1. [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [localize uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) na assinatura atual. Você usará o armazenamento do Azure para o conteúdo bruto a ser importado e para o repositório de conhecimento que é o resultado final.

   Há dois requisitos para esta conta:

   + Escolha a mesma região que a Pesquisa Cognitiva do Azure. 
   
   + Escolha o tipo de conta StorageV2 (uso geral V2). 

1. Abra as páginas dos serviços Blob e crie um contêiner.  

1. Clique em **Carregar**.

    ![Carregar os dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as resenhas de hotéis")

1. Carregue o arquivo **HotelReviews-Free.csv** que você baixou na primeira etapa.

    ![Criar o contêiner de Blob do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de Blob do Azure")

1. Você quase terminou de trabalhar com esse recurso, mas antes de sair dessas páginas, use um link no painel de navegação esquerdo para abrir a página **Chaves de Acesso**. Obtenha uma cadeia de conexão para recuperar dados do armazenamento de Blobs. A cadeia de conexão é semelhante ao seguinte exemplo: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. [Crie um serviço da Pesquisa Cognitiva do Azure](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na mesma assinatura. É possível usar um serviço gratuito para este início rápido.

Agora, você está pronto prosseguir para assistente de Importação de dados.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de Importação de dados

Na página de Visão geral do serviço de pesquisa, clique em **Importar dados** na barra de comandos para criar um repositório de conhecimento em quatro etapas.

  ![Comando Importar de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

1. Em **Conectar-se aos seus dados**, escolha **Armazenamento de Blobs do Azure** e selecione a conta e o contêiner criados. 
1. Para o **Nome**, insira `hotel-reviews-ds`.
1. Para **Modo de análise**, selecione **Texto delimitado** e, em seguido, marque a caixa de seleção **A Primeira Linha Contém Cabeçalho**. Verifique se o **Caractere delimitador** é uma vírgula (,).
1. Insira a **Cadeia de Conexão** do serviço de armazenamento salva em uma etapa anterior.
1. Para **Nome do contêiner**, insira `hotel-reviews`.
1. Clique em **Avançar: Adicionar enriquecimento de IA (opcional)** .

      ![Criar um objeto de fonte de dados](media/knowledge-store-create-portal/hotel-reviews-ds.png "Criar um objeto de fonte de dados")

1. Continue para a próxima página.

### <a name="step-2-add-cognitive-skills"></a>Etapa 2: Adicionar habilidades cognitivas

Nesta etapa do assistente, você criará um conjunto de habilidades com aprimoramentos de habilidades cognitivas. As habilidades usadas neste modelo extrairão frases-chave e detectarão o idioma e o sentimento. Em uma etapa posterior, esses aprimoramentos serão “projetados” em um repositório de conhecimento como tabelas do Azure.

1. Expanda **Anexar Serviços Cognitivos**. **Gratuito (Aprimoramentos limitados)** é selecionado por padrão. É possível usar esse recurso porque o número de registros em HotelReviews-Free.csv é 19 e esse recurso gratuito permite até 20 transações por dia.
1. Expanda **Adicionar habilidades cognitivas**.
1. Em **Nome do conjunto de habilidades**, insira `hotel-reviews-ss`.
1. Em **Campo de dados de origem**, selecione **reviews_text*.
1. Para **Nível de granularidade do aprimoramento**, selecione **Páginas (5 mil partes de caracteres)**
1. Selecione estas habilidades cognitivas:
    + **Extraia frases-chave**
    + **Detectar o idioma**
    + **Detectar sentimento**

      ![Criar um conjunto de habilidades](media/knowledge-store-create-portal/hotel-reviews-ss.png "Criar um conjunto de habilidades")

1. Expanda **Salvar aprimoramentos no repositório de conhecimento**.
1. Insira a **Cadeia de Conexão da conta de armazenamento** salva em uma etapa anterior.
1. Selecione estas **projeções de tabela do Azure**:
    + **Documentos**
    + **Páginas**
    + **Frases-chave**

    ![Configurar um repositório de conhecimento](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurar um repositório de conhecimento")

1. Continue para a próxima página.

### <a name="step-3-configure-the-index"></a>Etapa 3: Configurar o índice

Nesta etapa do assistente, você configurá um índice para consultas de pesquisa de texto completo opcionais. O assistente criará um exemplo de sua fonte de dados para inferir campos e tipos de dados. Só é necessário selecionar os atributos do seu comportamento desejado. Por exemplo, o atributo **Recuperável** permitirá que o serviço de pesquisa retorne um valor de campo enquanto o **Pesquisável** habilitará a pesquisa de texto completo no campo.

1. Em **Nome do índice**, insira `hotel-reviews-idx`.
1. Para atributos, faça estas seleções:
    + Selecione **Recuperável** para todos os campos.
    + Selecione **Filtrável** e **Com faceta** para estes campos: *Sentimento*, *Idioma*, *Palavras-chave*
    + Selecione **Pesquisável** para estes campos: *cidade*, *nome*, *reviews_text*, *idioma*, *Palavras-chave*

    O índice deve ser semelhante à imagem a seguir. Como a lista é longa, nem todos os campos estão visíveis na imagem.

    ![Configurar um índice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurar um índice")

1. Continue para a próxima página.

### <a name="step-4-configure-the-indexer"></a>Etapa 4: Configurar o indexador

Nesta etapa do assistente, você configurará um indexador que reunirá a fonte de dados, o conjunto de habilidades e o índice definidos nas etapas anteriores do assistente.

1. Para **Nome**, insira `hotel-reviews-idxr`.
1. Para **Agenda**, mantenha o padrão **Uma vez**.
1. Clique em **Enviar** para executar o indexador. A extração de dados, a indexação e a aplicação de habilidades cognitivas acontecem nesta etapa.

## <a name="monitor-status"></a>Monitorar status

A indexação de habilidades cognitivas leva mais tempo para ser concluída em comparação à indexação típica baseada em texto. O assistente deverá abrir a lista Indexador na página de visão geral, de modo que você possa acompanhar o progresso. Para a autonavegação, acesse a página Visão Geral e clique em **Indexadores**.

No portal do Azure, também é possível monitorar o log de atividades de Notificações para obter um link de status de **notificação da Pesquisa Cognitiva do Azure** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprimorou seus dados usando os serviços cognitivos e projetou os resultados em um repositório de conhecimento, é possível usar o Gerenciador de Armazenamento ou o Power BI para explorar seu conjunto de dados aprimorado.

Você pode exibir o conteúdo no Gerenciador de Armazenamento ou ir um passo adiante com o Power BI para obter informações sobre a visualização.

> [!div class="nextstepaction"]
> [Exibir com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)
> [Conectar-se ao Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Se desejar repetir esse exercício ou experimentar um passo a passo de aprimoramento de IA diferente, exclua o indexador *hotel-reviews-idx*. A exclusão do indexador redefine o contador de transações diárias gratuito para zero para o processamento dos Serviços Cognitivos.
