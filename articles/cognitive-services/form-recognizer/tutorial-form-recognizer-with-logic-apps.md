---
title: 'Tutorial: Usar o Reconhecimento de Formulários com Aplicativos Lógicos do Azure para analisar faturas – Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você usará o Reconhecimento de Formulários com Aplicativos Lógicos do Azure para criar um fluxo de trabalho que automatiza o processo de treinar um modelo e testá-lo usando dados de exemplo.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: bf6efbdbe19be28c005e2081c99827734ef10174
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177001"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Tutorial: Usar o Reconhecimento de Formulários com Aplicativos Lógicos do Azure para analisar faturas

Neste tutorial, você cria um fluxo de trabalho em Aplicativos Lógicos do Azure que usa o Reconhecimento de Formulários, um serviço que faz parte do pacote de Serviços Cognitivos do Azure, para extrair dados de faturas. Use o Reconhecimento de Formulários para treinar primeiro um modelo usando um conjunto de dados de exemplo e, em seguida, testar o modelo usando outro conjunto de dados. Os dados de exemplo usados neste tutorial são armazenados em contêineres do Azure Storage Blob.

Este tutorial abrange:

> [!div class="checklist"]
> * Solicitar acesso ao Reconhecimento de Formulários
> * Criar um contêiner do Azure Storage Blob
> * Carregar dados de exemplo no contêiner de blob do Azure
> * Criar um Aplicativo Lógico do Azure
> * Configurar o aplicativo lógico para usar um recurso do Reconhecimento de Formulários
> * Testar o fluxo de trabalho executando o aplicativo lógico

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/).

## <a name="request-access-for-form-recognizer"></a>Solicitar acesso ao Reconhecimento de Formulários

O Reconhecimento de Formulários está disponível em uma versão prévia de acesso limitado. Para obter acesso à versão prévia, preencha e envie o formulário de [Solicitação de acesso ao Reconhecimento de Formulários](https://aka.ms/FormRecognizerRequestAccess). Depois que sua solicitação for aprovada pela equipe dos Serviços Cognitivos do Azure, você receberá um email com instruções para acessar o serviço.

## <a name="understand-the-invoice-to-be-analyzed"></a>Entender a fatura a ser analisada

O conjunto de dados de exemplo que usamos para treinar o modelo e testar o modelo está disponível como um arquivo .zip do [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Baixe e extraia o arquivo .zip e abra um arquivo PDF de fatura na pasta **/Train**. Observe como ele tem uma tabela com o número da fatura, a data da fatura etc. 

> [!div class="mx-imgBorder"]
> ![Exemplo de fatura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Neste tutorial, aprendemos a extrair as informações de tais tabelas para um formato JSON usando um fluxo de trabalho criado usando Aplicativos Lógicos do Azure e o Reconhecimento de Formulários.

## <a name="create-an-azure-storage-blob-container"></a>Criar um contêiner do Azure Storage Blob

Você usa esse contêiner para carregar dados de exemplo necessários para treinar o modelo.

1. Siga as instruções em [Criar uma conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md) para criar uma conta de armazenamento. Use **formrecostorage** como o nome da conta de armazenamento.
1. Siga as instruções em [Criar um contêiner de blob do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) para criar um contêiner na conta de Armazenamento do Azure. Use **formrecocontainer** como o nome do contêiner. Defina o nível de acesso público para **Contêiner (acesso de leitura anônimo para contêineres e blobs)** .

    > [!div class="mx-imgBorder"]
    > ![Criar contêiner de blobs](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Carregar dados de exemplo no contêiner de blob do Azure

Baixe os dados de exemplo disponíveis no [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Extraia os dados para uma pasta local e carregue o conteúdo da pasta **/Train** para o **formrecocontainer** que você criou anteriormente. Siga as instruções em [Carregar um blob de blocos](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) para carregar dados para um contêiner.

Copie a URL do contêiner. Você precisará delas mais tarde neste tutorial. Se você criou a conta de armazenamento e o contêiner com os mesmos nomes listados neste tutorial, a URL será *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/* .

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do Reconhecimento de Formulários

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

Você pode usar os Aplicativos Lógicos do Azure para automatizar e orquestrar tarefas e fluxos de trabalho. Neste tutorial, você cria um aplicativo lógico que é disparado recebendo uma fatura que você deseja analisar como um anexo de email. Neste fluxo de trabalho, você executa as seguintes tarefas:
* Configure o aplicativo lógico para ser disparado automaticamente quando você receber um email com uma fatura anexada.
* Configure o aplicativo lógico para usar uma operação do **Modelo de Treinamento** do Reconhecimento de Formulários para treinar um modelo usando os dados de exemplo que você carregou no armazenamento de blobs do Azure.
* Configure o aplicativo lógico para usar um operação de **Analisar o Formulário** do Reconhecimento de Formulários para usar o modelo que você já treinou. Esse componente analisará a fatura que você fornecer a esse aplicativo lógico com base no modelo treinado anteriormente.

Vamos começar! Siga estas etapas para configurar seu fluxo de trabalho.

1. No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

1. Em **Criar aplicativo lógico**, forneça os detalhes sobre seu aplicativo lógico, conforme mostrado aqui. Quando terminar, selecione **Criar**.

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **Nome** | <*logic-app-name*> | O nome do aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses(`(`, `)`) e pontos (`.`). Este exemplo usa "My-First-Logic-App". |
   | **Assinatura** | <*Azure-subscription-name*> | Seu nome da assinatura do Azure |
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O nome do [grupo de recursos do Azure](./../../azure-resource-manager/resource-group-overview.md) usado para organizar os recursos relacionados. Este exemplo usa "My-First-LA-RG". |
   | **Localidade** | <*Azure-region*> | A região em que as informações de seu aplicativo lógico serão armazenadas. Este exemplo usa "Leste dos EUA". |
   | **Log Analytics** | Desativar | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Depois que o Azure implanta seu aplicativo, na barra de ferramentas do Azure, selecione **Notificações** > **Ir para o recurso** para seu aplicativo lógico implantado. Ou você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O Designer de Aplicativos Lógicos é exibido e mostra uma página com um vídeo de introdução e os gatilhos normalmente usados. Em **Modelos**, selecione **Aplicativo lógico em branco**.

   > [!div class="mx-imgBorder"]
   > ![Selecionar o modelo de aplicativo lógico em branco](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Configurar o aplicativo lógico para disparar o fluxo de trabalho quando um email chegar

Neste tutorial, você disparará o fluxo de trabalho quando um email for recebido com uma fatura anexada. Para este tutorial, escolhemos o Office 365 como o serviço de email, mas você pode usar qualquer outro provedor de email que queira.

1. Nas guias, selecione Todos, selecione **Office 365 Outlook** e, em **Gatilhos**, selecione **Quando um novo email chegar**.

    ![Disparar o aplicativo lógico por meio de um email de entrada](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Na caixa **Office 365 Outlook**, clique em **Entrar** e insira os detalhes para fazer logon em uma conta do Office 365.

1. Na caixa de diálogo seguinte, execute as seguintes etapas.
    1. Selecione a pasta que deve ser monitorada para qualquer novo email.
    1. Para **Tem anexos**, selecione **Sim**. Isso garante que apenas os emails com anexos disparem o fluxo de trabalho.
    1. Para **Incluir anexos**, selecione **Sim**. Isso garante que o conteúdo do anexo seja usado no processamento downstream.

        > [!div class="mx-imgBorder"]
        > ![Configurar o gatilho de email do aplicativo lógico](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Clique em **Salvar** na barra de ferramentas na parte superior.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Configurar o aplicativo lógico para usar a operação do Modelo de Treinamento do Reconhecimento de Formulários

Antes de usar o serviço de Reconhecimento de Formulários para analisar faturas, você precisa treinar um modelo fornecendo a ele alguns dados de notas fiscais de exemplo que o modelo pode analisar e aprender.

1. Selecione **Nova etapa** e, em **Escolher uma ação**, pesquise **Reconhecimento de Formulários**. Nos resultados que aparecem, selecione **Reconhecimento de Formulários** e, em seguida, nas ações disponíveis para o Reconhecimento de Formulários, selecione **Treinar Modelo**.

    > [!div class="mx-imgBorder"]
    > ![Treinar um modelo do Reconhecimento de Formulários](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Na caixa de diálogo Reconhecimento de Formulários, informe um nome para a conexão e insira a URL do ponto de extremidade e a chave que você recuperou para o recurso Reconhecimento de Formulários.

    > [!div class="mx-imgBorder"]
    > ![Nome da conexão para o Reconhecimento de Formulários](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Clique em **Criar**.

1. Na caixa de diálogo **Treinar Modelo**, para **Origem**, insira a URL para o contêiner em que você carregou os dados de exemplo.

    > [!div class="mx-imgBorder"]
    > ![Contêiner de armazenamento para faturas de exemplo](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Clique em **Salvar** na barra de ferramentas na parte superior.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Configurar o aplicativo lógico para usar a operação Analisar Formulário do Reconhecimento de Formulários

Nesta seção, você adiciona a operação **Analisar Formulário** ao fluxo de trabalho. Esta operação usa o modelo já treinado para analisar uma nova fatura fornecida para o aplicativo lógico.

1. Selecione **Nova etapa** e, em **Escolher uma ação**, pesquise **Reconhecimento de Formulários**. Nos resultados que aparecem, selecione **Reconhecimento de Formulários** e, em seguida, nas ações disponíveis para o Reconhecimento de Formulários, selecione **Analisar Formulário**.

    > [!div class="mx-imgBorder"]
    > ![Analisar um Modelo de Reconhecimento de Formulários](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Na caixa de diálogo **Analisar Formulário**, faça o seguinte:

    1. Clique na caixa de texto **ID do Modelo** e, na caixa de diálogo que é aberta, na **Guia de Conteúdo Dinâmico**, selecione **modelId**. Ao fazer isso, você fornece ao aplicativo de fluxo a ID do modelo que você treinou na última seção.

        > [!div class="mx-imgBorder"]
        > ![Usar ModelID para o Reconhecimento de Formulários](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Clique na caixa de texto **Documento** e, na caixa de diálogo que é aberta, na guia **Conteúdo Dinâmico**, selecione **Conteúdo de Anexos**. Ao fazer isso, você configura o fluxo para usar o arquivo de fatura de exemplo que está anexado ao email enviado para disparar o fluxo de trabalho.

        > [!div class="mx-imgBorder"]
        > ![Usar anexo de email para analisar faturas](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Clique em **Salvar** na barra de ferramentas na parte superior.

### <a name="extract-the-table-information-from-the-invoice"></a>Extraia as informações da tabela da fatura

Nesta seção, configuramos o aplicativo lógico para extrair as informações da tabela dentro das faturas.

1. Selecione **Adicionar uma ação** e, em **Escolher uma ação**, pesquise **Compor** e, nas ações disponíveis, selecione **Compor** novamente.
    ![Extrair as informações da tabela da fatura](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Na caixa de diálogo **Compor**, clique na caixa de texto **Entradas** e, na caixa de diálogo exibida, selecione **tabelas**.

    > [!div class="mx-imgBorder"]
    > ![Extrair as informações da tabela da fatura](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Clique em **Save** (Salvar).

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para testar o aplicativo lógico, use as notas fiscais de exemplo na pasta **/Test** do conjunto de dados de exemplo que você baixou do [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Execute as seguintes etapas:

1. No designer de Aplicativos Lógicos do Azure para seu aplicativo, selecione **Executar** na barra de ferramentas na parte superior. O fluxo de trabalho está ativo agora e aguarda receber um email com a fatura anexada.
1. Envie um email com uma fatura de exemplo anexada ao endereço de email que você forneceu ao criar o aplicativo lógico. Verifique se o email foi entregue à pasta que você informou ao configurar o aplicativo lógico.
1. Assim que o email é entregue à pasta, o designer de Aplicativos Lógicos mostra uma tela com o progresso de cada estágio. Na captura de tela abaixo, você verá que um email com anexo é recebido e o fluxo de trabalho está em andamento.

    > [!div class="mx-imgBorder"]
    > ![Iniciar o fluxo de trabalho enviando um email](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Depois que todos os estágios do fluxo de trabalho terminarem de ser executados, o designer de Aplicativos Lógicos mostrará uma caixa de seleção verde em cada estágio. Na janela do designer, selecione **Para cada 2** e, em seguida, selecione **Compor**.

    > [!div class="mx-imgBorder"]
    > ![Fluxo de trabalho concluído](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Na caixa **SAÍDAS**, copie a saída e cole-a em qualquer editor de texto.

1. Compare a saída JSON com a fatura de exemplo que você enviou como um anexo no email. Verifique se os dados JSON correspondem aos dados na tabela dentro da fatura.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Este tutorial foi concluído com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configura um fluxo de trabalho de Aplicativos Lógicos do Azure para usar o Reconhecimento de Formulários para treinar um modelo e extrair o conteúdo de uma fatura. Em seguida, saiba como criar um conjunto de dados de treinamento para que você possa criar um cenário semelhante com seus próprios formulários.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](build-training-data-set.md)