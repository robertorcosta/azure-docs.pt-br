---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77029180"
---
>[!NOTE]
>Esta seção fornece instruções para o [registro de aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo expansível e, em seguida, abra o painel **Registros de aplicativo**. 

    [![Selecione o painel do Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecione o botão **+ Novo registro**.

    [![Selecione o botão Novo registro](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dê um nome amigável para esse registro de aplicativo na caixa **Nome**. 

    1. Na seção **URI de redirecionamento (opcional)** , insira `https://microsoft.com` na caixa de texto.     

    1. Verifique quais contas e locatários são compatíveis com seu aplicativo do Microsoft Azure Active Directory.

    1. Selecione **Registrar**.

    [![Criar painel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. A folha **Autenticação** especifica as configurações de autenticação importantes. 

    1. Adicione os **URIs de redirecionamento** e configure os **Tokens de acesso** selecionando **+ Adicionar uma plataforma**.

    1. Selecione **Sim** para especificar que o aplicativo é um **cliente público**.

    1. Verifique quais contas e locatários são compatíveis com seu aplicativo do Microsoft Azure Active Directory.

    [![Configuração de cliente público](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Depois de selecionar a plataforma apropriada, configure seus **URIs de redirecionamento** e **Tokens de acesso** no painel lateral à direita da interface do usuário.

    1. Os **URIs de Redirecionamento** devem corresponder ao endereço fornecido pela solicitação de autenticação:

        * Para aplicativos hospedados em um ambiente de desenvolvimento local, selecione **Cliente público (móvel e desktop)** . Lembre-se de definir **cliente público** como **Sim**.
        * Para aplicativos de página única hospedados no Serviço de Aplicativo do Azure, selecione **Web**.

    1. Determine se uma **URL de logout** é apropriada.

    1. Habilite o fluxo de concessão implícita marcando **Tokens de acesso** ou **Tokens de ID**.
                
    [![Configurar URIs de redirecionamento](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Clique em **Configurar** e **Salvar**.

1.  Abra o painel **Visão geral** do seu aplicativo registrado e copie os valores das entidades a seguir para um arquivo temporário. Você usará esses valores para configurar seu aplicativo de exemplo nas seções a seguir.

    - **ID do Aplicativo (cliente)**
    - **ID do Diretório (locatário)**

    [![ID do aplicativo do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Abra o painel **Permissões de API** para o registro do aplicativo. Selecione o botão **+ Adicionar uma permissão**. No painel **Solicitar permissões de API**, selecione a guia **APIs que a minha organização usa** e pesquise pelo seguinte:
    
    1. `Azure Digital Twins`. Selecione a **API de Gêmeos Digitais do Azure**.

        [![API de Pesquisa ou Gêmeos Digitais do Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Como alternativa, pesquise `Azure Smart Spaces Service`. Selecione a API **Azure Smart Spaces Service**.

        [![API de Pesquisa para Espaços Inteligentes do Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > A ID e o nome da API do Azure AD que serão exibidos dependerão do locatário:
    > * As contas de locatário e cliente de teste devem pesquisar `Azure Digital Twins`.
    > * Outras contas Microsoft devem pesquisar `Azure Smart Spaces Service`.

1. Qualquer uma das APIs, uma vez selecionada, será exibida como **Gêmeos Digitais do Azure**, no mesmo painel **Solicitar permissões de API**. Selecione a opção suspensa **Leitura** e marque a caixa de seleção **Leitura.Gravação**. Selecione o botão **Adicionar permissões**.

    [![Adicionar permissões de API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Dependendo das configurações da sua organização, talvez você precise seguir etapas adicionais para conceder acesso de administrador a essa API. Entre em contato com seu administrador para obter mais informações. Assim que o acesso de administrador for aprovado, a coluna **Consentimento do Administrador Necessário** no painel de **Permissões da API** exibirá suas permissões. 

    [![Aprovação de consentimento do administrador](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Verifique se **Gêmeos Digitais do Azure** aparece.
