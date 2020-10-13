---
title: Configurar uma instância e uma autenticação (com script)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço de gêmeos digital do Azure, executando um script de implantação automatizada
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 83741f5bc55eb222b379a274ef403f766553b21f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328620"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (com script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo conclui essas etapas executando um exemplo de [ **script de implantação automatizado** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) que simplifica o processo. 
* Para exibir as etapas manuais da CLI em que o script é executado nos bastidores, consulte a versão da CLI deste artigo: [*como: configurar uma instância e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para exibir as etapas manuais de acordo com o portal do Azure, consulte a versão do portal deste artigo: [*como configurar uma instância e autenticação (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="prerequisites-download-the-script"></a>Pré-requisitos: baixar o script

O script de exemplo é escrito no PowerShell. Ele faz parte dos [**exemplos de gêmeos digitais do Azure**](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), que você pode baixar em seu computador navegando até esse link de exemplo e selecionando o botão *baixar zip* abaixo do título.

Isso baixará o projeto de exemplo em seu computador como _**Azure_Digital_Twins_samples.zip**_. Navegue até a pasta em seu computador e descompacte-a para extrair os arquivos.

Na pasta descompactada, o script de implantação está localizado em _Azure_Digital_Twins_samples > scripts > **deploy.ps1** _.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Executar o script de implantação

Este artigo usa um exemplo de código de gêmeos digital do Azure para implantar uma instância do gêmeos digital do Azure e a autenticação necessária semi-automaticamente. Ele também pode ser usado como um ponto de partida para escrever suas próprias interações com script.

Aqui estão as etapas para executar o script de implantação no Cloud Shell.
1. Vá para uma janela de [Azure cloud Shell](https://shell.azure.com/) em seu navegador. Entre usando este comando:
    ```azurecli
    az login
    ```
    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure. Caso contrário, abra uma página de navegador em *https://aka.ms/devicelogin* e insira o código de autorização exibido no terminal.
 
2. Na barra de ícones Cloud Shell, verifique se o Cloud Shell está configurado para executar a versão do PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell&quot;:::

1. Selecione o ícone &quot;carregar/baixar arquivos" e escolha "carregar".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell&quot;:::

1. Selecione o ícone &quot;carregar/baixar arquivos":::

    Navegue até o arquivo de _**deploy.ps1**_ em seu computador (em _Azure_Digital_Twins_samples scripts de > > **deploy.ps1** _) e pressione "abrir". Isso carregará o arquivo para Cloud Shell para que você possa executá-lo na janela Cloud Shell.

4. Execute o script enviando o `./deploy.ps1` comando na janela Cloud Shell. (Lembre-se de que para colar em Cloud Shell, você pode usar **Ctrl + Shift + v** no Windows e Linux ou **cmd + shift + v** no MacOS. Você também pode usar o menu de clique com o botão direito do mouse.)

    ```azurecli
    ./deploy.ps1
    ```

    À medida que o script é executado por meio das etapas de configuração automatizada, você será solicitado a passar os seguintes valores:
    * Para a instância: a *ID* da assinatura do Azure a ser usada
    * Para a instância: um *local* onde você gostaria de implantar a instância. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Para a instância: um nome de *grupo de recursos* . Você pode usar um grupo de recursos existente ou inserir um novo nome de um para criar.
    * Para a instância: um *nome* para sua instância de gêmeos digital do Azure. O nome da nova instância deve ser exclusivo na região da sua assinatura (ou seja, se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome que você escolher, será solicitado que você escolha um nome diferente).
    * Para o registro do aplicativo: um *nome de exibição do aplicativo do Azure ad* a ser associado ao registro. Esse registro de aplicativo é onde você configura permissões de acesso para as [APIs do Azure digital gêmeos](how-to-use-apis-sdks.md). Posteriormente, o aplicativo cliente será autenticado no registro do aplicativo e, como resultado, receberá as permissões de acesso configuradas para as APIs.
    * Para o registro do aplicativo: uma *URL de resposta do aplicativo do Azure ad* para o aplicativo do Azure AD. Use `http://localhost`. O script irá configurar um URI de *cliente público/nativo (mobile & Desktop)* para ele.

O script criará uma instância de gêmeos digital do Azure, atribuirá ao usuário do Azure a função de *proprietário do gêmeos digital do Azure (versão prévia)* na instância e configurará um registro de aplicativo do Azure ad para o aplicativo cliente usar.

>[!NOTE]
>Atualmente, há um **problema conhecido** com a instalação com script, em que alguns usuários (especialmente usuários nas [contas pessoais da Microsoft (MSAS)](https://account.microsoft.com/account)) podem achar que a **atribuição de função ao _proprietário do gêmeos digital do Azure (versão prévia)_ não foi criada**.
>
>Você pode verificar a atribuição de função com a seção [*verificar atribuição de função de usuário*](#verify-user-role-assignment) posteriormente neste artigo e, se necessário, configurar a atribuição de função manualmente usando o [portal do Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions) ou a [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Para obter mais detalhes sobre esse problema, consulte solução de problemas [*: problemas conhecidos no Azure digital gêmeos*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

Aqui está um trecho do log de saída do script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell&quot;:::

1. Selecione o ícone &quot;carregar/baixar arquivos" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Se o script for concluído com êxito, a impressão final dirá `Deployment completed successfully` . Caso contrário, resolva a mensagem de erro e execute o script novamente. Ele ignorará as etapas que você já concluiu e começará a solicitar a entrada novamente no ponto em que você parou.

Após a conclusão do script, agora você tem uma instância do gêmeos digital do Azure pronta para ir com permissões para gerenciá-la e configurou permissões para um aplicativo cliente acessá-la.

> [!NOTE]
> O script atribui atualmente a função de gerenciamento necessária no Azure digital gêmeos (*proprietário do gêmeos digital do Azure (versão prévia)*) para o mesmo usuário que executa o script de Cloud Shell. Se você precisar atribuir essa função a outra pessoa que gerenciará a instância, poderá fazer isso agora por meio do portal do Azure ([instruções](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) ou da CLI ([instruções](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Coletar valores importantes

Há vários valores importantes dos recursos configurados pelo script que talvez sejam necessários à medida que você continuar trabalhando com sua instância de gêmeos digital do Azure. Nesta seção, você usará o [portal do Azure](https://portal.azure.com) para coletar esses valores. Você deve salvá-los em um local seguro ou retornar a esta seção para encontrá-los mais tarde quando precisar deles.

Se outros usuários estiverem programando em relação à instância, você também deverá compartilhar esses valores com eles.

### <a name="collect-instance-values"></a>Coletar valores de instância

Na [portal do Azure](https://portal.azure.com), localize sua instância do gêmeos digital do Azure pesquisando o nome da sua instância na barra de pesquisa do Portal.

A seleção será aberta na página *visão geral* da instância. Anote seu *nome*, *grupo de recursos*e *nome do host*. Talvez você precise deles mais tarde para identificar e conectar-se à sua instância do.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell&quot;:::

1. Selecione o ícone &quot;carregar/baixar arquivos":::

### <a name="collect-app-registration-values"></a>Coletar valores de registro do aplicativo 

Há dois valores importantes do registro do aplicativo que serão necessários mais tarde para [gravar o código de autenticação do aplicativo cliente para as APIs do gêmeos digital do Azure](how-to-authenticate-client.md). 

Para encontrá-los, siga [este link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para navegar até a página Visão geral do registro de aplicativo do Azure AD na portal do Azure. Esta página mostra todos os registros de aplicativo que foram criados em sua assinatura.

Você deve ver o registro do aplicativo que acabou de criar nesta lista. Selecione-o para abrir seus detalhes:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell&quot;:::

1. Selecione o ícone &quot;carregar/baixar arquivos":::

Anote a ID do *aplicativo (cliente)* e a *ID do diretório (locatário)* mostradas **na página.** Se você não for a pessoa que vai escrever código para aplicativos cliente, você precisará compartilhar esses valores com a pessoa que será.

## <a name="verify-success"></a>Verificar êxito

Se você quiser verificar a criação de seus recursos e as permissões configuradas pelo script, você pode vê-los no [portal do Azure](https://portal.azure.com).

Se não for possível verificar o êxito de qualquer etapa, repita a etapa. Você pode executar as etapas individualmente usando as instruções [portal do Azure](how-to-set-up-instance-portal.md) ou [CLI](how-to-set-up-instance-cli.md) .

### <a name="verify-instance"></a>Verificar instância

Para verificar se a instância foi criada, vá para a [página gêmeos digital do Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) na portal do Azure. Você pode acessar esta página por conta própria procurando por *gêmeos do Azure digital* na barra de pesquisa do Portal.

Esta página lista todas as suas instâncias de gêmeos digitais do Azure. Procure o nome da instância recém-criada na lista.

Se a verificação não tiver sido bem-sucedida, você poderá repetir a criação de uma instância usando o [portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) ou a [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="verify-user-role-assignment"></a>Verificar atribuição de função de usuário

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Lembre-se de que o script atribui atualmente essa função necessária ao mesmo usuário que executa o script de Cloud Shell. Se você precisar atribuir essa função a outra pessoa que gerenciará a instância, poderá fazer isso agora por meio do portal do Azure ([instruções](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) ou da CLI ([instruções](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Se a verificação não tiver sido bem-sucedida, você também poderá refazer sua própria atribuição de função usando o [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) ou a [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

### <a name="verify-app-registration"></a>Verificar registro do aplicativo

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Em seguida, verifique se as configurações de permissões do gêmeos digital do Azure foram definidas corretamente no registro. Para fazer isso, selecione *manifesto* na barra de menus para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure esses campos em `requiredResourceAccess` . Os valores devem corresponder aos da captura de tela abaixo:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

Se uma ou ambas as etapas de verificação não forem bem-sucedidas, tente criar o registro do aplicativo novamente usando as instruções do [portal](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) ou da [CLI](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) .

## <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

Teste as chamadas de API REST individuais em sua instância usando os comandos da CLI do Azure digital gêmeos: 
* [referência de AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*Como usar a CLI dos Gêmeos Digitais do Azure*](how-to-use-cli.md)

Ou então, consulte Como conectar seu aplicativo cliente à sua instância escrevendo o código de autenticação do aplicativo cliente:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)
