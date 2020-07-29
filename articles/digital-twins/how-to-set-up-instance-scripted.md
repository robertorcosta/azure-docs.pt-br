---
title: Configurar uma instância e uma autenticação (com script)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço gêmeos do Azure digital, incluindo a autenticação adequada. Versão com script.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 522096b921faf34130f0c37f727d89c7bf95c530
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337901"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (com script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo conclui essas etapas executando um exemplo de [ **script de implantação automatizado** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) que simplifica o processo. Para exibir as etapas manuais que o script executa nos bastidores, consulte a versão manual deste artigo: [*como: configurar uma instância e autenticação (manual)*](how-to-set-up-instance-manual.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="run-the-deployment-script"></a>Executar o script de implantação

Este artigo usa um exemplo de código de gêmeos digital do Azure para implantar uma instância do gêmeos digital do Azure e a autenticação necessária semi-automaticamente. Ele também pode ser usado como um ponto de partida para escrever suas próprias interações com script.

O script de exemplo é escrito no PowerShell. Ele faz parte dos [exemplos de gêmeos digitais do Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), que você pode baixar em seu computador navegando até esse link de exemplo e selecionando o botão *baixar zip* abaixo do título.

Na pasta de exemplo baixada, o script de implantação está localizado em _Azure_Digital_Twins_samples.zip > scripts > **deploy.ps1** _.

Aqui estão as etapas para executar o script de implantação no Cloud Shell.
1. Vá para uma janela de [Azure cloud Shell](https://shell.azure.com/) em seu navegador. Entre usando este comando:
    ```azurecli-interactive
    az login
    ```
    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure. Caso contrário, abra uma página de navegador em *https://aka.ms/devicelogin* e insira o código de autorização exibido no terminal.
 
2. Depois de entrar, procure a barra de ícones da janela de Cloud Shell. Selecione o ícone "carregar/baixar arquivos" e escolha "carregar".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção da opção carregar":::

    Navegue até o arquivo de _**deploy.ps1**_ em seu computador e pressione "abrir". Isso carregará o arquivo para Cloud Shell para que você possa executá-lo na janela Cloud Shell.

3. Execute o script enviando o `./deploy.ps1` comando na janela Cloud Shell. À medida que o script é executado por meio das etapas de configuração automatizada, você será solicitado a passar os seguintes valores:
    * Para a instância: a *ID* da assinatura do Azure a ser usada
    * Para a instância: um *local* onde você gostaria de implantar a instância. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Para a instância: um nome de *grupo de recursos* . Você pode usar um grupo de recursos existente ou inserir um novo nome de um para criar.
    * Para a instância: um *nome* para sua instância de gêmeos digital do Azure. O nome da nova instância deve ser exclusivo na região da sua assinatura (ou seja, se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome que você escolher, será solicitado que você escolha um nome diferente).
    * Para o registro do aplicativo: um *nome de exibição do aplicativo do Azure ad* a ser associado ao registro. Esse registro de aplicativo é onde você configura permissões de acesso para as [APIs do Azure digital gêmeos](how-to-use-apis-sdks.md). Posteriormente, o aplicativo cliente será autenticado no registro do aplicativo e, como resultado, receberá as permissões de acesso configuradas para as APIs.
    * Para o registro do aplicativo: uma *URL de resposta do aplicativo do Azure ad* para o aplicativo do Azure AD. Você pode usar o `http://localhost` .

O script criará uma instância de gêmeos digital do Azure, atribuirá ao usuário do Azure a função de *proprietário do gêmeos digital do Azure (versão prévia)* na instância e configurará um registro de aplicativo do Azure ad para o aplicativo cliente usar.

Aqui está um trecho do log de saída do script:

:::image type="content" source="media/how-to-set-up-instance/deployment-script-output.png" alt-text="Janela Cloud Shell mostrando o log de entrada e saída por meio da execução do script de implantação" lightbox="media/how-to-set-up-instance/deployment-script-output.png":::

Se o script for concluído com êxito, a impressão final dirá `Deployment completed successfully` . Caso contrário, resolva a mensagem de erro e execute o script novamente. Ele ignorará as etapas que você já concluiu e começará a solicitar a entrada novamente no ponto em que você parou.

Após a conclusão do script, agora você terá uma instância do gêmeos digital do Azure pronta para começar e as permissões configuradas para gerenciá-lo.

## <a name="collect-important-values"></a>Coletar valores importantes

Há dois valores importantes do registro do aplicativo que serão necessários mais tarde para [autenticar um aplicativo cliente em relação às APIs do gêmeos digital do Azure](how-to-authenticate-client.md). 

Para encontrá-los, siga [este link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para navegar até a página Visão geral do registro de aplicativo do Azure AD na portal do Azure. Esta página mostra todos os registros de aplicativo que foram criados em sua assinatura.

Você deve ver o registro do aplicativo que acabou de criar nesta lista. Selecione-o para abrir seus detalhes:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Exibição do portal dos valores importantes para o registro do aplicativo":::

Anote a ID do *aplicativo (cliente)* e a *ID do diretório (locatário)* mostradas **na página.** Se você não for a pessoa que vai escrever código para aplicativos cliente, você precisará compartilhar esses valores com a pessoa que será.

## <a name="verify-success"></a>Verificar êxito

Se você quiser verificar a criação de seus recursos e as permissões configuradas pelo script, você pode vê-los no [portal do Azure](https://portal.azure.com).

### <a name="verify-instance"></a>Verificar instância

Para verificar se a instância foi criada, vá para a [página gêmeos digital do Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) na portal do Azure. Esta página lista todas as suas instâncias de gêmeos digitais do Azure. Procure o nome da instância recém-criada na lista.

### <a name="verify-user-role-assignment"></a>Verificar atribuição de função de usuário

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

### <a name="verify-app-registration"></a>Verificar registro do aplicativo

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Primeiro, verifique se as configurações de permissões do gêmeos digital do Azure foram definidas corretamente no registro. Para fazer isso, selecione *manifesto* na barra de menus para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure esses campos em `requiredResourceAccess` . Os valores devem corresponder aos da captura de tela abaixo:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

Veja como conectar seu aplicativo cliente à sua instância escrevendo o código de autenticação do aplicativo cliente:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)
