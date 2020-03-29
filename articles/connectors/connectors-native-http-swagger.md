---
title: Conecte-se aos pontos finais REST dos aplicativos de lógica do Azure
description: Monitore os pontos finais do REST em tarefas, processos e fluxos de trabalho automatizados usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787362"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chame pontos finais do REST usando aplicativos de lógica do Azure

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector INCORPORADO HTTP + Swagger, você pode automatizar fluxos de trabalho que regularmente chamam qualquer ponto final REST através de um arquivo [Swagger,](https://swagger.io) construindo aplicativos lógicos. O gatilho e a ação HTTP + Swagger funcionam da mesma forma que o [gatilho e a ação HTTP,](connectors-native-http.md) mas fornecem uma melhor experiência no Logic App Designer, expondo a estrutura e as saídas da API descritas pelo arquivo Swagger. Para implementar um gatilho de votação, siga o padrão de votação descrito em [Criar APIs personalizadas para chamar outras APIs, serviços e sistemas de aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O arquivo URL para o arquivo Swagger (não OpenAPI) que descreve o ponto final do REST de destino

  Normalmente, o ponto final REST deve atender a esses critérios para que o conector funcione:

  * O arquivo Swagger deve ser hospedado em uma URL HTTPS acessível publicamente.

  * O arquivo Swagger deve ter [o CORS (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) ativado.

  Para fazer referência a um arquivo Swagger que não esteja hospedado ou que não atenda aos requisitos de segurança e origem cruzada, você pode [carregar o arquivo Swagger em um contêiner blob em uma conta de armazenamento do Azure](#host-swagger)e ativar o CORS nessa conta de armazenamento para que você possa referenciar o arquivo.

  Os exemplos neste tópico utilizam a [API Face serviços cognitivos,](https://docs.microsoft.com/azure/cognitive-services/face/overview)que requer uma [conta de Serviços Cognitivos e chave de acesso](../cognitive-services/cognitive-services-apis-create-account.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico de onde você deseja chamar o ponto final do destino. Para começar com o gatilho HTTP + Swagger, [crie um aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP + Swagger, inicie seu aplicativo lógico com qualquer gatilho que você quiser. Este exemplo usa o gatilho HTTP + Swagger como o primeiro passo.

## <a name="add-an-http--swagger-trigger"></a>Adicione um gatilho HTTP + Swagger

Este gatilho incorporado envia uma solicitação HTTP para uma URL para um arquivo Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo desse arquivo.

1. Faça login no [portal Azure](https://portal.azure.com). Abra seu aplicativo de lógica em branco no Logic App Designer.

1. No designer, na caixa de pesquisa, digite "swagger" como seu filtro. Na lista **Triggers,** selecione o gatilho **HTTP + Swagger.**

   ![Selecione http + gatilho swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Na caixa **URL DO SWAGGER ENDPOINT,** digite a URL do arquivo Swagger e selecione **Next**.

   Este exemplo usa a URL Swagger que está localizada na região oeste dos EUA para a [API face serviços cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Digite URL para ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o designer mostrar as operações descritas pelo arquivo Swagger, selecione a operação que deseja usar.

   ![Operações no arquivo Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros de gatilho, que variam de acordo com a operação selecionada, que você deseja incluir na chamada de ponto final. Configure a recorrência de quantas vezes você deseja que o gatilho chame o ponto final.

   Este exemplo renomeia o gatilho para "HTTP + Swagger trigger: Face - Detect" para que a etapa tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="add-an-http--swagger-action"></a>Adicione uma ação HTTP + Swagger

Essa ação incorporada faz uma solicitação HTTP para o arquivo URL para o arquivo Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo desse arquivo.

1. Faça login no [portal Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

1. a etapa onde você deseja adicionar a ação HTTP + Swagger, selecione **Novo passo**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. No designer, na caixa de pesquisa, digite "swagger" como seu filtro. Na lista **Ações,** selecione a ação **HTTP + Swagger.**

    ![Selecionar ação de HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Na caixa **URL DO SWAGGER ENDPOINT,** digite a URL do arquivo Swagger e selecione **Next**.

   Este exemplo usa a URL Swagger que está localizada na região oeste dos EUA para a [API face serviços cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Digite URL para ponto final de Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o designer mostrar as operações descritas pelo arquivo Swagger, selecione a operação que deseja usar.

   ![Operações no arquivo Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação, que variam de acordo com a operação selecionada, que você deseja incluir na chamada de ponto final.

   Este exemplo não tem parâmetros, mas renomeia a ação para "HTTP + Swagger action: Face - Identify" para que a etapa tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Host Swagger no armazenamento Azure

Você pode referenciar um arquivo Swagger que não está hospedado ou que não atende aos requisitos de segurança e origem cruzada, carregando esse arquivo para o contêiner blob em uma conta de armazenamento Do Zure e habilitando o CORS nessa conta de armazenamento. Para criar, configurar e armazenar arquivos Swagger no Azure Storage, siga estas etapas:

1. [Crie uma conta de armazenamento Azure.](../storage/common/storage-create-storage-account.md)

1. Agora habilite o CORS para a bolha. No menu da sua conta de armazenamento, selecione **CORS**. Na guia **de serviço Blob,** especifique esses valores e selecione **Salvar**.

   | Propriedade | Valor |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora este exemplo use o [portal Azure,](https://portal.azure.com)você pode usar uma ferramenta como [o Azure Storage Explorer](https://storageexplorer.com/)ou configurar automaticamente essa configuração usando este script [PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)de amostra .

1. [Crie um recipiente blob](../storage/blobs/storage-quickstart-blobs-portal.md). No painel **Visão geral** do contêiner, selecione Alterar nível **de acesso**. Na lista **de nível de acesso público,** selecione **Blob (acesso de leitura anônima apenas para blobs)** e selecione **OK**.

1. [Faça o upload do arquivo Swagger para o recipiente blob,](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)seja através do [portal Azure](https://portal.azure.com) ou [do Azure Storage Explorer](https://storageexplorer.com/).

1. Para referenciar o arquivo no recipiente blob, use um link HTTPS que siga esse formato, que é sensível a maiúsculas e minúsculas:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referência de conector

Aqui estão mais informações sobre as saídas de um gatilho ou ação HTTP + Swagger. A chamada HTTP + Swagger retorna esta informação:

| Nome da propriedade | Type | Descrição |
|---------------|------|-------------|
| headers | objeto | Os cabeçalhos da solicitação |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo a partir da solicitação |
| código de status | INT | O código de status da solicitação |
|||

| Código de status | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceita |
| 400 | Solicitação incorreta |
| 401 | Não Autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
