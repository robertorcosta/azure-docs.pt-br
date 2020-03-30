---
title: Conecte-se ao servidor FTP
description: Automatize tarefas e fluxos de trabalho que criam, monitoram e gerenciam arquivos em um servidor FTP usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 94ca609d9dc070e6e2b4dc878ecd8dfaf9331ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648165"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando Aplicativos Lógicos do Azure

Com o conector FTP e dos Aplicativos Lógicos do Azure, você pode criar tarefas automatizadas e fluxos de trabalho que criar, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e excluir arquivos.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que obtêm respostas de seu servidor FTP e disponibilizar a saída para outras ações. Use ações de execução nos aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode ter outro uso de ações para usar a saída das ações do FTP. Por exemplo, se você obter arquivos regularmente do servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou do Outlook.com. Se ainda não estiver familiarizado com o Aplicativo Lógico, veja [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Limitações

* O conector FTP suporta apenas FTP explícito sobre SSL (FTPS) e não é compatível com FTPS implícito.

* Por padrão, as ações ftp podem ler ou gravar arquivos de *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, as ações FTP suportam [o reparcelamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **Obter conteúdo de arquivo** usa implicitamente o chunking.

* Os gatilhos FTP não suportam o emaque. Ao solicitar conteúdo de arquivo, os gatilhos selecionam apenas arquivos de 50 MB ou menores. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Use um gatilho FTP que retorna propriedades do arquivo, como **quando um arquivo é adicionado ou modificado (somente propriedades)**.

  * Siga o gatilho com a ação de conteúdo do arquivo FTP **Get,** que lê o arquivo completo e usa implicitamente o chunking.

* Se você tiver um servidor FTP no local, considere criar um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ou usar [conexões Azure App Service Hybrid](../app-service/app-service-hybrid-connections.md), que permitem que você acesse fontes de dados no local sem usar um gateway de dados no local.

## <a name="how-ftp-triggers-work"></a>Como funcionam os gatilhos FTP

O FTP aciona o trabalho pesquisando o sistema de arquivos FTP e procurando por qualquer arquivo que tenha sido alterado desde a última pesquisa. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Ir para **opções** > **preferências** > **transferir edição** > **Edit** > Preservar carimbo de**tempo** > **Desativar** |
| FileZilla | Ir para **Transferir** > **Preservar carimbos de tempo de arquivos** > transferidos**Desativar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Seu endereço de servidor de host FTP e credenciais de conta

  O conector de FTP exige que o servidor FTP seja acessível da internet e configure para operar no modo *passivo*. Suas credenciais permitem que o aplicativo lógico crie uma conexão e acesse sua conta do FTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta FTP. Para iniciar com um gatilho de FTP [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação FTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-ftp"></a>Conectar-se ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Faça login no [portal Azure](https://portal.azure.com)e abra seu aplicativo lógico no Logic App Designer.

1. Para aplicativos de lógica em branco, na caixa de pesquisa, digite `ftp` como seu filtro. Na lista **Triggers,** selecione o gatilho que deseja.

   -ou-

   Para aplicativos lógicos existentes, a última etapa em que você deseja adicionar uma ação, selecione **Nova etapa**e selecione Adicionar **uma ação**. Na caixa de pesquisa, insira `ftp` como o filtro. Na lista **Ações,** selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Forneça suas informações de conexão e selecione **Criar**.

1. Forneça as informações para o gatilho ou ação selecionados e continue construindo o fluxo de trabalho do seu aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Adicionar gatilho FTP

O **gatilho Quando um arquivo é adicionado ou modificado (somente propriedades)** inicia um fluxo de trabalho de aplicativo lógico quando o gatilho detecta que um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deve obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Finalmente, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta diferente no servidor SFTP.

Por exemplo, você pode usar este gatilho para monitorar uma pasta FTP para novos arquivos que descrevem pedidos de clientes. Em seguida, você pode usar uma ação FTP, como **Obter metadados de arquivo** para obter as propriedades para esse novo arquivo e, em seguida, usar o conteúdo do **arquivo** para obter o conteúdo desse arquivo para processamento adicional e armazenar esse pedido em um banco de dados de pedidos.

Aqui está um exemplo que mostra como usar o gatilho **Quando um arquivo é adicionado ou modificado (somente propriedades).**

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos de lógica em branco, na caixa de pesquisa, digite `ftp` como seu filtro. Na lista de gatilhos, selecione este gatilho: **Quando um arquivo for adicionado ou modificado (somente propriedades)**

   ![Encontre e selecione o gatilho FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Forneça os detalhes necessários para sua conexão e, em seguida, selecione **Criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. Para transferir arquivos no formato binário, por exemplo, nos casos em que a codificação é usada, selecione **Transporte Binário**.

   ![Criar conexão com o servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Na caixa **Pasta,** selecione o ícone da pasta para que uma lista seja exibida. Para encontrar a pasta que deseja monitorar para arquivos novos ou**>** editados, selecione a seta de ângulo reto (), navegue até essa pasta e selecione a pasta.

   ![Localize e selecione a pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Sua pasta selecionada aparece na caixa **Pasta**.

   ![A pasta selecionada aparece na propriedade "Pasta"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Neste exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Adicionar ação FTP

A ação **Get file metadata** obtém as propriedades de um arquivo que está no seu servidor FTP e a ação **de conteúdo do arquivo Get** obtém o conteúdo do arquivo com base nas informações sobre esse arquivo no servidor FTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e essas ações para obter o conteúdo do arquivo depois que esse arquivo for adicionado ou editado.

1. o gatilho ou quaisquer outras ações, selecione **Novo passo**.

1. Na caixa de pesquisa, insira `ftp` como o filtro. Na lista de ações, selecione esta ação: **Obtenha metadados de arquivo**

   ![Selecione a ação "Obter metadados de arquivos"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Se você já tiver uma conexão ao seu servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, selecione **Criar**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Depois que a ação **Obter metadados do arquivo** for exibida, clique em dentro da caixa Arquivo **para** que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, em **Obter metadados de arquivo,** selecione a **propriedade Lista de Arquivos Id,** que faz referência à coleção onde o arquivo foi adicionado ou atualizado.

   ![Encontre e selecione a propriedade "Lista de Arquivos Id"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   A **propriedade Lista de Arquivos Id** agora aparece na caixa **Arquivo.**

   ![Propriedade selecionada "Lista de Arquivos Id"](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Agora adicione esta ação FTP: **Obtenha conteúdo de arquivo**

   ![Encontre e selecione a ação "Obter conteúdo de arquivo"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Depois que a ação **Obter conteúdo do arquivo** for exibida, clique em dentro da caixa **Arquivo** para que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, em **Obter metadados de arquivo,** selecione a propriedade **Id,** que faz referência ao arquivo que foi adicionado ou atualizado.

   ![Encontre e selecione a propriedade "Id"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   A **propriedade Id** agora aparece na **caixa Arquivo.**

   ![Propriedade "Id" selecionada](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Salve seu aplicativo lógico.

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para verificar se o fluxo de trabalho retorna o conteúdo esperado, adicione outra ação que lhe envie o conteúdo do arquivo carregado ou atualizado.

1. Em **ação de conteúdo do arquivo Get,** adicione uma ação que pode enviar o conteúdo do arquivo. Este exemplo adiciona a ação **Enviar um e-mail** para o Office 365 Outlook.

   ![Adicione uma ação para o envio de e-mails](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Depois que a ação aparecer, forneça as informações e inclua as propriedades que deseja testar. Por exemplo, inclua a propriedade **De conteúdo de arquivo,** que aparece na lista de conteúdo dinâmico após selecionar **Ver mais** na seção Obter conteúdo **de arquivo.**

   ![Fornecer informações sobre a ação de email](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Salve seu aplicativo lógico. Para executar e acionar o aplicativo lógico, na barra de ferramentas, selecione **Executar**e, em seguida, adicione um arquivo à pasta FTP que seu aplicativo lógico agora monitora.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como gatilhos, ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/ftpconnector/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
