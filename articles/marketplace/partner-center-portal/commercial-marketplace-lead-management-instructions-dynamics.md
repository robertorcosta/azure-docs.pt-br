---
title: Gerenciamento de leads para o compromisso com o cliente do Dynamics 365-Microsoft Commercial Marketplace
description: Saiba como configurar o compromisso com o cliente do Dynamics 365 para gerenciar clientes potenciais do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 985b3258eb0b957242d529945f32ed9704a91e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790993"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Configurar o gerenciamento de leads para o compromisso do cliente do Dynamics 365

Este artigo descreve como configurar o engajamento do cliente do Dynamics 365 (anteriormente denominado Dynamics CRM Online). Leia mais sobre a alteração em [Configurar a autenticação baseada em servidor com o envolvimento do cliente e o SharePoint Online](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) para processar os leads de vendas de sua oferta do Marketplace comercial.

>[!NOTE]
>Essas instruções são específicas para o ambiente de nuvem hospedado pela Microsoft para o compromisso com o cliente do Dynamics 365. Atualmente, não há suporte para a conexão direta com um ambiente do Dynamics local. Há outras opções para você receber clientes potenciais, como configurar um ponto de [extremidade https](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Pré-requisitos

As seguintes permissões de usuário são necessárias para concluir as etapas neste artigo:

* Direitos de administrador em sua instância do engajamento do cliente do Dynamics 365 para poder instalar uma solução.
* Direitos de administrador de locatários para criar uma nova conta de serviço para o serviço de Lead usado para enviar clientes potenciais de ofertas do Marketplace comercial.
* Acesso ao portal de administração do Office 365.
* Acesso ao portal do Azure.

## <a name="install-the-solution"></a>Instalar a solução

1. Baixe a [solução de gravador de Microsoft Marketplace Lead](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)e salve-a localmente no computador.

1. Abra o compromisso do cliente do Dynamics 365 acessando a URL para sua instância do `https://tenant.crm.dynamics.com`Dynamics, como.

1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Configurações avançadas**.
 
    ![Item de menu de configurações avançadas do Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Na página **configurações** , abra o menu **configurações** na barra superior e selecione **soluções**.

    >[!NOTE]
    >Se você não vir as opções na tela a seguir, não terá as permissões necessárias para continuar. Contate um administrador em sua instância do engajamento do cliente do Dynamics 365.

    ![Opção de soluções do Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Na página **soluções** , selecione **importar** e vá para onde você salvou a solução de **gravador de Microsoft Marketplace Lead** que você baixou na etapa 1.

    ![Botão Importar](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Conclua a importação da solução seguindo o assistente de importação de solução.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar clientes potenciais em sua instância do engajamento do cliente do Dynamics 365, você deve compartilhar uma conta de serviço com a Microsoft e configurar permissões para a conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o Azure Active Directory ou o Office 365.

>[!NOTE]
>Pule para as instruções correspondentes com base na opção de autenticação selecionada. Consulte [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) ou [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos essa opção porque você nunca precisa atualizar seu nome de usuário ou senha para continuar a obter clientes potenciais. Para usar a opção Azure Active Directory, forneça a ID do aplicativo, a chave do aplicativo e a ID do diretório do seu aplicativo Active Directory.

Para configurar o Azure Active Directory para o compromisso com o cliente do Dynamics 365:

1. Entre no [portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**.

1. Selecione **Propriedades**e copie o valor da **ID de diretório** na página Propriedades do **diretório** . Salve esse valor porque você precisará fornecê-lo no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

    ![Azure Active Directory item de menu Propriedades](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selecione **registros de aplicativo** no painel de Azure Active Directory esquerdo e, em seguida, selecione **novo registro** na página.
1. Insira um nome significativo para o nome do aplicativo.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.
1. Em **URI de redirecionamento (opcional)**, selecione **Web** e insira um URI `https://contosoapp1/auth`, como. 
1. Selecione **Registrar**.

    ![Registrar uma página de aplicativo](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Agora que seu aplicativo está registrado, acesse a página de visão geral do aplicativo. Copie o valor da **ID do aplicativo (cliente)** nessa página. Salve esse valor porque você precisará fornecê-lo no portal de publicação e no Dynamics 365 para receber clientes potenciais para sua oferta do Marketplace.

    ![Caixa de ID do aplicativo (cliente)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selecione **certificados & segredos** no painel esquerdo do aplicativo e selecione o botão **novo segredo do cliente** . Insira uma descrição significativa para o segredo do cliente e selecione a opção **nunca** em **expirar**. Selecione **Adicionar** para criar o segredo do cliente.

    ![Item de menu certificados & segredos](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Assim que o segredo do cliente for criado com êxito, copie o valor do **segredo do cliente** . Você não poderá recuperar o valor depois de sair da página. Salve esse valor porque você precisará fornecê-lo no portal de publicação para receber clientes potenciais para sua oferta do Marketplace. 
1. Selecione **permissões de API** no painel esquerdo do aplicativo e, em seguida, selecione **+ Adicionar uma permissão**.
1. Selecione **APIs da Microsoft**e, em seguida, selecione **Dynamics CRM** como a API.
1. Em **que tipo de permissões seu aplicativo requer?**, verifique se **permissões delegadas** está selecionada. 
1. Em **permissão**, marque a caixa de seleção **user_impersonation** para **Common Data Service de acesso como usuários da organização**. Em seguida, selecione **Adicionar permissões**.

    ![Botão adicionar permissões](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Depois de concluir as etapas 1 a 14 na portal do Azure, vá para a instância do engajamento do cliente do Dynamics 365 indo para a URL `https://tenant.crm.dynamics.com`, como.
1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Configurações avançadas**.
1. Na página **configurações** , abra o menu **configurações** na barra superior e selecione **segurança**.
1. Na página **segurança** , selecione **usuários**. Na página **usuários** , selecione a lista suspensa **usuários habilitados** e, em seguida, selecione **usuários do aplicativo**.
1. Selecione **Novo** para criar um novo usuário. 

    ![Criar um novo usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. No painel **novo usuário** , verifique se usuário **: usuário do aplicativo** está selecionado. Forneça um nome de usuário, um nomes completo e um endereço de email para aquele que você deseja usar com essa conexão. Além disso, Cole a **ID do aplicativo** para o aplicativo que você criou no portal do Azure da etapa 8. Selecione **salvar & fechar** para concluir a adição do usuário.

    ![Novo painel de usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Vá para a seção "configurações de segurança" neste artigo para concluir a configuração da conexão para esse usuário.

### <a name="office-365"></a>Office 365

Se você não quiser usar Azure Active Directory, poderá registrar um novo usuário no centro de administração do Microsoft 365. Você será solicitado a atualizar seu nome de usuário e senha a cada 90 dias para continuar a obter clientes potenciais.

Para configurar o compromisso do cliente do Office 365 para Dynamics 365:

1. Entre no [Centro de Administração do Microsoft 365](https://admin.microsoft.com).

1. Selecione **Adicionar um usuário**.

    ![Microsoft 365 o centro de administração adicionar uma opção de usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Crie um novo usuário para o serviço de gravação do lead. Defina as seguintes configurações:

    * Insira um nome de usuário.
    * Insira uma senha e desmarque a opção **tornar este usuário alterar sua senha ao entrar pela primeira vez** .
    * Selecione **usuário (sem acesso de administrador)** como a função para o usuário.
    * Selecione **plano de envolvimento do cliente do Dynamics 365** como a licença do produto, conforme mostrado na tela a seguir. Você será cobrado pela licença que você escolher. 

Salve esses valores porque você precisará fornecer os valores de **nome de usuário** e **senha** no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

![Painel novo usuário do centro de administração do Microsoft 365](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Configurações de segurança

A etapa final é habilitar o usuário que você criou para escrever os leads.

1. Abra o compromisso do cliente do Dynamics 365 acessando a URL para sua instância do `https://tenant.crm.dynamics.com`Dynamics, como.
1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Configurações avançadas**.
1. Na página **configurações** , abra o menu **configurações** na barra superior e selecione **segurança**.
1. Na página **segurança** , selecione **usuários** e selecione o usuário que você criou na seção "configurar permissões de usuário" deste documento. Em seguida, selecione **gerenciar funções**. 

    ![Guia gerenciar funções](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Procure o nome da função **Microsoft Marketplace gravador de cliente potencial**e selecione-o para atribuir ao usuário a função.

    ![Painel gerenciar funções de usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Essa função é criada pela solução que você importou e tem permissões apenas para gravar os leads e acompanhar a versão da solução para garantir a compatibilidade.

1. Volte para a página **segurança** e selecione funções de **segurança**. Procure a função **Microsoft Marketplace gravador de cliente potencial**e selecione-a.

    ![Painel funções de segurança](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Na função de segurança, selecione a guia **registros principais** . procure o item de **configurações da interface do usuário da entidade de usuários** . Habilite as permissões criar, ler e gravar para o usuário (1/4 círculo amarelo) para essa entidade clicando uma vez em cada um dos círculos correspondentes.

    ![Guia de registros principais do gravador de leads Microsoft Marketplace](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Na guia **personalização** , procure o item de **trabalho do sistema** . Habilite as permissões ler, gravar e acrescentar para a organização (círculos verdes sólidos) para essa entidade clicando quatro vezes em cada um dos círculos correspondentes.

    ![Guia de personalização do gravador de Lead Microsoft Marketplace](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selecione **Salvar e fechar**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Configurar sua oferta para enviar clientes potenciais ao engajamento do cliente do Dynamics 365 

Para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação:

1. Vá para a página de **instalação da oferta** de sua oferta.
1. Selecione **conectar** na seção **Gerenciamento de leads** .

    ![Botão conectar da seção Gerenciamento de leads](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

1. Na janela pop-up detalhes da conexão, selecione o **compromisso do cliente do Dynamics 365** para o destino do cliente potencial.

    ![Caixa destino do cliente potencial](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Insira a **URL** para a instância do Dynamics 365, como `https://contoso.crm4.dynamics.com`.

1. Selecione o método de **autenticação**, seja Azure Active Directory ou o Office 365. 
1. Se você selecionou **Azure Active Directory**, insira a **ID do aplicativo (cliente)** (por `23456052-aaaa-bbbb-8662-1234df56788f`exemplo,), a **ID** do diretório `12345678-8af1-4asf-1234-12234d01db47`(por exemplo,) e o **segredo** do `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`cliente (por exemplo,).

    ![Autenticação com Azure Active Directory selecionado](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Se você tiver selecionado **Office 365**, insira **o nome de usuário** (por `contoso@contoso.onmicrosoft.com`exemplo,) e a **senha** ( `P@ssw0rd`por exemplo,).

    ![Caixa nome de usuário do Office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Para **email de contato**, insira os endereços de email para as pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode inserir vários endereços de email separando-os com pontos-e-vírgulas.
1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione o botão **validar** . Se for bem-sucedido, você terá um líder de teste no destino do cliente potencial.

![Caixa de email de contato](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.
