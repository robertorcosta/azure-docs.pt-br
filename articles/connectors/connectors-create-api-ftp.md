---
title: Conectar-se ao servidor FTP-aplicativo lógico do Azure
description: Crie, monitore e gerencie arquivos em um servidor FTP com os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547484"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando Aplicativos Lógicos do Azure

Com o conector FTP e dos Aplicativos Lógicos do Azure, você pode criar tarefas automatizadas e fluxos de trabalho que criar, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e excluir arquivos.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que obtêm respostas de seu servidor FTP e disponibilizar a saída para outras ações. Use ações de execução nos aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode ter outro uso de ações para usar a saída das ações do FTP. Por exemplo, se você obter arquivos regularmente do servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou do Outlook.com. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limites

* O conector de FTP dá suporte apenas a FTPS (FTP explícito sobre SSL) e não é compatível com FTPS implícita.

* Por padrão, as ações de FTP podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, as ações de FTP dão suporte a [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do arquivo** usa implicitamente o agrupamento.

* Os gatilhos de FTP não dão suporte a agrupamentos. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Use um gatilho de FTP que retorne Propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação FTP **Get file Content** , que lê o arquivo completo e usa o agrupamento implicitamente.

## <a name="how-ftp-triggers-work"></a>Como funcionam os gatilhos de FTP

Os gatilhos de FTP funcionam sondando o sistema de arquivos FTP e procurando qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Vá para **opções** > **preferências** > **transferir** > **editar**  >  **Preservar o carimbo de hora** > **desabilitar** |
| FileZilla | Vá para **transferir** > **preservar os carimbos de hora de arquivos transferidos** > **desabilitar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Seu endereço de servidor de host FTP e credenciais de conta

  O conector de FTP exige que o servidor FTP seja acessível da internet e configure para operar no modo *passivo*. Suas credenciais permitem que o aplicativo lógico crie uma conexão e acesse sua conta do FTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta FTP. Para iniciar com um gatilho de FTP [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação FTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-ftp"></a>Conectar-se ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "ftp" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**e, em seguida, selecione **Adicionar uma ação**. Na caixa de pesquisa, insira “ftp” como filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho FTP: quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico ao detectar quando um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deve obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta no servidor SFTP.

**Exemplo empresarial**: você pode usar esse gatilho para monitorar uma pasta de FTP para novos arquivos que descrevem pedidos de clientes. Você pode, então, usar uma ação de FTP como **Obter conteúdo do arquivo**, para que possa obter o conteúdo da ordem para mais processamento e armazenar essa ordem em um banco de dados de ordens.

Aqui está um exemplo que mostra esse gatilho: **quando um arquivo é adicionado ou modificado**

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "ftp" como filtro. Na a lista de gatilhos, selecione este gatilho: **quando um campo é adicionado ou modificado - FTP**

   ![Localize e selecione o gatilho de FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. Para transferir arquivos em formato binário, por exemplo, onde e quando a codificação é usada, selecione **transporte binário**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Ao lado da **Pasta**, escolha o ícone da pasta, para que a lista apareça. Para localizar a pasta que você deseja monitorar arquivos novos ou editados, selecione a seta de ângulo direito ( **>** ), navegue até essa pasta e, em seguida, selecione a pasta.

   ![Localize e selecione a pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder.png)  

   Sua pasta selecionada aparece na caixa **Pasta**.

   ![Pasta selecionada](./media/connectors-create-api-ftp/selected-folder.png)  

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Neste exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Ação de FTP: obter o conteúdo

Esta ação obtém o conteúdo de um arquivo em um servidor FTP quando esse arquivo é adicionado ou atualizado. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma ação que obtém o conteúdo do arquivo depois que esse arquivo é adicionado ou editado.

Aqui está um exemplo que mostra esta ação: **obter conteúdo**

1. No gatilho ou quaisquer outras ações, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira “ftp” como filtro. Na lista de ações, selecione esta ação: **Obter conteúdo do arquivo - FTP**

   ![Selecionar ação FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Se você já tiver uma conexão ao seu servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, escolha **Criar**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Após a ação **Obter conteúdo do arquivo** abrir, clique dentro da caixa do **Arquivo** para que apareça na lista de conteúdo dinâmica. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, selecione a propriedade **Conteúdo do Arquivo**, que tem o conteúdo do arquivo adicionado ou atualizado.  

   ![Localize e selecione o arquivo](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   A propriedade do **Conteúdo do Arquivo** aparece agora na caixa **Arquivo**.

   ![Propriedade "Conteúdo do arquivo" selecionada](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Salve seu aplicativo lógico. Para testar seu fluxo de trabalho, adicione um arquivo para a pasta FTP que seu aplicativo lógico agora monitora.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/ftpconnector/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)