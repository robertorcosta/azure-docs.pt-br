---
title: Configurar uma instância e autenticação (Portal)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço gêmeos do Azure digital usando o portal do Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73b7171b89b26926992e95f77e376e7bb7731eff
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408234"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (Portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo percorre essas etapas manualmente, uma a uma, usando o portal do Azure. O portal do Azure é um console unificado baseado na Web que fornece uma alternativa para as ferramentas de linha de comando.
* Para percorrer essas etapas manualmente usando a CLI, consulte a versão da CLI deste artigo: [*como configurar uma instância e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para executar uma configuração automatizada usando um exemplo de script de implantação, consulte a versão com script deste artigo: [*como: configurar uma instância e autenticação (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
 
Em seguida, faça logon no [portal do Azure](https://ms.portal.azure.com/) com suas credenciais.

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: requisitos de permissão

Para poder concluir todas as etapas neste artigo, você precisa ser classificado como um proprietário em sua assinatura do Azure. 

Você pode verificar seu nível de permissão na [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure (você pode usar este link ou procurar *assinaturas* com a barra de pesquisa do Portal). Procure o nome da assinatura que você está usando e exiba sua função na coluna *minha função* . Se você for um proprietário, esse valor será *proprietário*:

:::image type="content" source="media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Exibição da página assinaturas no portal do Azure, mostrando o usuário como proprietário" lightbox="media/how-to-set-up-instance/portal/subscriptions-role.png":::

Se você achar que o valor é *colaborador* ou algo diferente do *proprietário*, você pode proceder de uma das seguintes maneiras:
* Entre em contato com o proprietário da assinatura e solicite que o proprietário conclua as etapas neste artigo em seu nome
* Entre em contato com o proprietário da assinatura ou com a função de administrador de acesso do usuário na assinatura e solicite que eles elevem você ao proprietário na assinatura para que você tenha as permissões para continuar. Se isso é apropriado depende de sua organização e sua função dentro dela.

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância de gêmeos digital do Azure

Nesta seção, você **criará uma nova instância do Azure digital gêmeos** usando o portal do Azure.

Depois de fazer logon no [portal do Azure](https://ms.portal.azure.com/), comece selecionando _criar um recurso_ no menu de home page de serviços do Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selecionando "criar um recurso" na home page da portal do Azure":::

Pesquise *gêmeos digital do Azure* na caixa de pesquisa e escolha o serviço **gêmeos (versão prévia) do Azure digital** dos resultados. Selecione o botão _criar_ para criar uma nova instância do serviço.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Selecionando ' criar ' na página do serviço de gêmeos digital do Azure":::

Na página *criar recurso* a seguir, preencha os valores fornecidos abaixo:
* **Assinatura**: a assinatura do Azure que você está usando
  - **Grupo de recursos**: um grupo de recursos no qual implantar a instância. Se você ainda não tiver um grupo de recursos existente em mente, poderá criar um aqui selecionando o link *criar novo* e inserindo um nome para um novo grupo de recursos
* **Local**: uma região do Azure digital gêmeos habilitada para a implantação. Para obter mais detalhes sobre o suporte regional, visite [*produtos do Azure disponíveis por região (Azure digital gêmeos)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nome do recurso**: um nome para sua instância de gêmeos digital do Azure. O nome da nova instância deve ser exclusivo na região da sua assinatura (ou seja, se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome que você escolher, será solicitado que você escolha um nome diferente).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Preenchendo os valores descritos para criar um recurso de gêmeos digital do Azure":::

Quando terminar, selecione _revisar + criar_. Isso levará você a uma página de resumo, na qual você pode examinar os detalhes da instância inseridos e clicar em _criar_. 

### <a name="verify-success"></a>Verificar êxito

Depois de *enviar por*Push, você pode exibir o status da implantação da sua instância em suas notificações do Azure ao longo da barra de ícones do Portal. A notificação indicará quando a implantação foi bem-sucedida e você poderá selecionar o botão _ir para o recurso_ para exibir a instância criada.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Exibição de notificações do Azure mostrando uma implantação bem-sucedida e realçando o botão ' ir para recurso '":::

Como alternativa, se a implantação falhar, a notificação indicará o porquê. Observe o aviso da mensagem de erro e tente criar a instância novamente.

>[!TIP]
>Depois que sua instância for criada, você poderá retornar para sua página a qualquer momento pesquisando o nome da sua instância na barra de pesquisa portal do Azure.

Na página *visão geral* da instância, anote seu *nome*, *grupo de recursos*e *nome do host*. Esses são os valores importantes que podem ser necessários à medida que você continuar trabalhando com sua instância de gêmeos digital do Azure. Se outros usuários estiverem programando em relação à instância, você deverá compartilhar esses valores com eles.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Realçando os valores importantes da página de visão geral da instância":::

Agora você tem uma instância de gêmeos digital do Azure pronta para uso. Em seguida, você dará as permissões apropriadas de usuário do Azure para gerenciá-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso do usuário

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Primeiro, abra a página da instância do gêmeos digital do Azure no portal do Azure. No menu da instância, selecione *controle de acesso (iam)*. Selecione o botão *Adicionar* em *Adicionar uma atribuição de função*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selecionando para adicionar uma atribuição de função da página ' controle de acesso (IAM) '":::

Na página *Adicionar atribuição de função* a seguir, preencha os valores (deve ser concluído por um proprietário da assinatura do Azure):
* **Função**: selecione *proprietário do gêmeos digital do Azure (versão prévia)* no menu suspenso
* **Atribuir acesso a**: selecione *usuário, grupo ou entidade de serviço do Azure ad* no menu suspenso
* **Selecione**: Procure o nome ou endereço de email do usuário a ser atribuído. Quando você seleciona o resultado, o usuário aparecerá em uma seção de *Membros selecionados* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Preenchendo os campos listados na caixa de diálogo ' Adicionar atribuição de função '":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Quando tiver terminado de inserir os detalhes, clique no botão *salvar* .

### <a name="verify-success"></a>Verificar êxito

Você pode exibir a atribuição de função que configurou sob o *controle de acesso (iam) > atribuições de função*. O usuário deve aparecer na lista com uma função do proprietário do *gêmeos digital do Azure (versão prévia)*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Exibição das atribuições de função para uma instância do gêmeos digital do Azure no portal do Azure":::

Agora você tem uma instância de gêmeos digital do Azure pronta para uso e atribuiu permissões para gerenciá-la. Em seguida, você configurará permissões para um aplicativo cliente acessá-lo.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurar permissões de acesso para aplicativos cliente

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Comece navegando até [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) na portal do Azure (você pode usar este link ou encontrá-lo com a barra de pesquisa do Portal). Selecione *registros de aplicativo* no menu serviço e *+ novo registro*.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Na página *registrar um aplicativo* a seguir, preencha os valores solicitados:
* **Nome**: um nome de exibição do aplicativo do Azure ad a ser associado ao registro
* **Tipos de conta com suporte**: selecione *contas neste diretório organizacional somente (somente diretório padrão-locatário único)*
* **URI de redirecionamento**: uma *URL de resposta do aplicativo do Azure ad* para o aplicativo do Azure AD. Você pode usar o `http://localhost` .

Quando tiver terminado, pressione o botão *registrar* .

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Exibição da página ' registrar um aplicativo ' com os valores descritos preenchidos":::

Quando a configuração do registro for concluída, o portal irá redirecioná-lo para sua página de detalhes.

### <a name="provide-azure-digital-twins-api-permission"></a>Fornecer permissão de API de gêmeos digital do Azure

Em seguida, configure o registro de aplicativo que você criou com permissões de linha de base para as APIs do Azure digital gêmeos.

Na página do portal para o registro do aplicativo, selecione *permissões de API* no menu. Na página permissões a seguir, clique no botão *+ Adicionar uma permissão* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Exibição do registro do aplicativo no portal do Azure, realçando a opção de menu ' permissões de API ' e o botão ' + adicionar uma permissão '":::

Na página *solicitar permissões de API* que segue, alterne para a guia *APIs que minha organização usa* e pesquise *gêmeos do Azure digital*. Selecione *Azure digital gêmeos* nos resultados da pesquisa para continuar com a atribuição de permissões para as APIs do gêmeos digital do Azure.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Exibição da página ' solicitar permissões de API ' no resultado da pesquisa mostrando o Azure digital gêmeos":::

Em seguida, você selecionará quais permissões conceder para essas APIs. Expanda a permissão **Read (1)** e marque a caixa que diz *Read. Write* para conceder a esse aplicativo o leitor de registro e permissões de gravador.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Exibição da página ' solicitar permissões de API ' selecionando as permissões ' Read. Write ' para as APIs do Azure digital gêmeos":::

Clique em *adicionar permissões* quando terminar.

### <a name="verify-success"></a>Verificar êxito

De volta à página *permissões de API* , verifique se agora há uma entrada para o Azure digital gêmeos que reflete as permissões de leitura/gravação:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Exibição do portal das permissões de API para o registro do aplicativo do Azure AD, mostrando "acesso de leitura/gravação" para o gêmeos digital do Azure":::

Você também pode verificar a conexão com o gêmeos digital do Azure dentro domanifest.jsdo registro do aplicativo *em*, que foi atualizado automaticamente com as informações do gêmeos digital do Azure quando você adicionou as permissões de API.

Para fazer isso, selecione *manifesto* no menu para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure esses campos em `requiredResourceAccess` . Os valores devem corresponder aos da captura de tela abaixo:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Exibição do portal do manifesto para o registro do aplicativo do Azure AD. Aninhado em ' requiredResourceAccess ', há um valor de ' resourceAppId ' de 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 e um valor de ' resourceAccess > ID ' de 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Coletar valores importantes

Em seguida, selecione *visão geral* na barra de menus para ver os detalhes do registro do aplicativo:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Exibição do portal dos valores importantes para o registro do aplicativo":::

Anote a ID do *aplicativo (cliente)* e a *ID do diretório (locatário)* mostradas **na página.** Esses valores serão necessários posteriormente para [autenticar um aplicativo cliente em relação às APIs do gêmeos digital do Azure](how-to-authenticate-client.md). Se você não for a pessoa que vai escrever código para tais aplicativos, deverá compartilhar esses valores com a pessoa que será.

### <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

Veja como conectar seu aplicativo cliente à sua instância escrevendo o código de autenticação do aplicativo cliente:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)