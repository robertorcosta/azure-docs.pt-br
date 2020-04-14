---
title: Gerenciamento de chumbo para dynamics 365 para engajamento do cliente | Mercado Azure
description: Configure o gerenciamento de chumbo para a Dynamics 365 para engajamento do cliente.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5b3e35b6d19905e3c5262dfea3e52511510c9ffe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252686"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Configure o gerenciamento de chumbo para a Dynamics 365 para engajamento do cliente

Este artigo descreve como configurar o Dynamics 365 para engajamento do cliente (anteriormente Dynamics CRM Online), leia mais sobre a mudança [aqui](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) para processar leads de vendas de sua oferta de marketplace. 

>[!Note]
>Essas instruções são específicas para o ambiente microsoft hosted cloud Dynamics 365 para engajamento do cliente. Conectar-se diretamente a um ambiente Dynamics on-prem não é suportado no momento, existem outras opções para você receber leads, como configurar um [ponto final https](./commercial-marketplace-lead-management-instructions-https.md) ou uma tabela [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber leads.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes permissões de usuário são necessárias para completar as etapas deste artigo:

* Você precisa ser um admin em sua instância Dynamics 365 para engajamento do cliente para poder instalar uma solução e seguir essas instruções.
* Você precisa ser um inquilino para criar uma nova conta de serviço para o serviço de lead usado para enviar leads de ofertas de marketplace.
* Você precisa ter acesso ao portal de admin Office 365.
* Você precisa ter acesso ao portal Azure.

## <a name="install-the-solution"></a>Instalar a solução

1.  Baixe a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente no seu computador.

2.  Abra a Dinâmica 365 para engajamento do cliente navegando até a `https://tenant.crm.dynamics.com`URL para a instância Dynamics (como ).

3.  Ajuste as configurações selecionando o ícone de engrenagem e **as configurações avançadas** na barra de navegação superior.
 
    ![Dinâmica - Configurações Avançadas](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Uma vez na página Configurações, acesse o menu Configuração na barra de navegação superior e selecione **Soluções**.

    >[!Note]
    >Se você não ver as opções na próxima captura de tela, então você não tem as permissões necessárias para prosseguir. Entre em contato com um admin em sua dinâmica 365 para a instância de engajamento do cliente.

    ![Dinâmica 365 - Soluções](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Uma vez na página Soluções, **selecione Importar** e navegue até onde você salvou a solução *Microsoft Marketplace Lead Writer* que você baixou na etapa 1.

    ![Dinâmica 365 para Engajamento do Cliente - Importação](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Completar a importação da solução seguindo o assistente de solução Import.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar leads em sua instância Dynamics 365 for Customer Engagement, você deve compartilhar uma conta de serviço conosco e configurar permissões para a conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o **Azure Active Directory** ou o **Office 365**.

>[!Note]
>Com base na opção de autenticação selecionada, você pode pular para as instruções correspondentes com base na sua escolha. Consulte [o Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) ou [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos essa opção porque você tem o benefício adicional de nunca precisar atualizar seu nome de usuário/senha para continuar recebendo leads. Para usar a opção Diretório Ativo do Azure, você fornece o ID do aplicativo, a chave de aplicativo e o ID do diretório a partir do aplicativo Active Directory.

Use as seguintes etapas para configurar o Azure Active Directory for Dynamics 365 para engajamento do cliente.

1. Faça login no [portal Azure](https://portal.azure.com/)e selecione o serviço de diretório ativo do Azure na navegação à esquerda.

2. Selecione **Propriedades** da navegação esquerda do Diretório Ativo do Azure e copie o valor de **ID** do diretório nessa página. Guarde esse valor, pois é o valor *de ID do Diretório* que você precisa fornecer no portal de publicação para receber leads para sua oferta de marketplace.

    ![Diretório Ativo do Azure - Propriedades](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selecione registros do **aplicativo** no Diretório Ativo do Azure e selecione **Novo registro** nessa página.
4. Digite um nome para o nome do aplicativo. Forneça um nome de aplicativo significativo.
5. Em tipos de conta suportados, selecione **Contas em qualquer diretório organizacional**.
6. Em Redirecionar uri, selecione **Web** e `https://contosoapp1/auth`forneça um URI (como ). 
7. Selecione **Registrar**.

    ![Registrar um aplicativo](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Agora que seu aplicativo está registrado, acesse a página de visão geral do aplicativo e copie o valor de ID do **aplicativo (cliente)** nessa página. Guarde esse valor, pois é o valor de *ID do aplicativo (cliente)* que você precisa fornecer no portal de publicação e na Dynamics para receber leads para sua oferta de marketplace.

    ![ID do aplicativo (cliente)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Selecione **Certificados e segredos** da navegação esquerda do aplicativo e selecione **Novo segredo de cliente** nessa página. Digite uma descrição significativa para o segredo do cliente e selecione a opção **Nunca** em Expira. Selecione **Adicionar** para criar o segredo do cliente.

    ![Aplicação - Certificação e Segredos](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Assim que o segredo do cliente for criado com sucesso, **copie o valor secreto do cliente.** Você não poderá recuperar o valor depois de navegar para longe da página. Guarde esse valor, pois é o valor *secreto do Cliente* que você precisa fornecer no portal de publicação para receber leads para sua oferta de marketplace. 
11. Selecione **permissões** de API na navegação esquerda dos aplicativos e selecione **Adicionar uma permissão**.
12. Selecione AS APIs da Microsoft e selecione **O CRM dinâmico** como API.
13. Sob *que tipo de permissões seu aplicativo exige,* certifique-se de que as permissões delegadas estão **selecionadas.** Verifique a permissão para **user_impersonation** *Acessar O Serviço de Dados Comuns como usuários da organização*. Selecione **Adicionar Permissões**.

    ![Adicionar permissões](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Depois de completar as etapas 1-13 no portal Azure, navegue até a sua instância `https://tenant.crm.dynamics.com`Dynamics 365 para Engajamento do Cliente navegando até a URL (como ).
15. Acessando configurações selecionando o ícone de engrenagem e **configurações avançadas** na barra de navegação superior.
16. Uma vez na página Configurações, acesse o menu Configuração na barra de navegação superior e selecione **Segurança**.
17. Uma vez na página Segurança, selecione **Usuários**.  Na página Usuários, selecione a estada "Usuários Habilitados" para mudar para **Usuários do Aplicativo**.
18. Selecione **Novo** para criar um novo usuário. 

    ![Criar um novo usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Em **Novo Usuário,** certifique-se de que o USUÁRIO: USUÁRIO DO APLICATIVO está selecionado. Forneça um nome de usuário, nome completo e endereço de e-mail para o usuário que você deseja usar com essa conexão. Além disso, cole no **ID do aplicativo** para o aplicativo que você criou no portal Azure a partir da etapa 8. Selecione **Salvar e Fechar** para concluir a adição do usuário.

    ![Novo usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Vá para "Configurações de segurança" neste artigo para concluir a configuração de conexão para este usuário.

### <a name="office-365"></a>Office 365

Se você não quiser usar o Azure Active Directory, você pode registrar um novo usuário no *centro de administradores do Microsoft 365*. Será necessário atualizar seu nome de usuário e senha a cada 90 dias para continuar a receber leads.

Use as seguintes etapas para configurar o Office 365 para a Dinâmica 365 para engajamento do cliente.

1. Entre no [Centro de Administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione **Adicionar um usuário**.

    ![Centro de admin microsoft 365 - adicionar um usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Crie um novo usuário para o serviço de gravação do lead. Defina as seguintes configurações:

    * Forneça um nome de usuário
    * Forneça uma senha e desmarque a opção "Faça com que este usuário altere sua senha quando entrar pela primeira vez".
    * Selecione "Usuário (sem acesso de administrador)" como a função para o usuário.
    * Selecione "Plano de engajamento do cliente dinâmico 365" como licença de produto mostrada na próxima captura de tela. Você será cobrado pela licença que você escolher. 

Salve esses valores pois são os valores *de Nome de Usuário e Senha* que você precisa fornecer no portal de publicação para receber leads para sua oferta de marketplace.

![Microsoft 365 admin center - novo usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Configurações de segurança

A etapa final é permitir que o usuário que você criou grave os leads.

1. Abra a Dinâmica 365 para engajamento do cliente navegando até a `https://tenant.crm.dynamics.com`URL para a instância Dynamics (como ).
2. Ajuste as configurações selecionando o ícone de engrenagem e **as configurações avançadas** na barra de navegação superior.
3. Uma vez na página Configurações, acesse o menu Configuração na barra de navegação superior e selecione **Segurança**.
4. Uma vez na página Segurança, selecione **Usuários** e selecione o usuário que você criou na seção Configurar permissões do usuário deste documento e, em seguida, selecione **Gerenciar funções**. 

    ![Gerenciar funções](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Procure o nome da função "Microsoft Marketplace Lead Writer" e selecione-o para atribuir ao usuário a função.

    ![Gerenciar funções de usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Essa função é criada pela solução que você importou e tem permissões apenas para gravar os leads e acompanhar a versão da solução para garantir a compatibilidade.

6. Navegue de volta à página de segurança e selecione **Funções de Segurança**. Procure a função "Microsoft Marketplace Lead Writer" e selecione-a.

    ![Funções de segurança](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Uma vez na função de segurança, selecione a guia **Registros principais.** Procure a entidade do usuário nas configurações de ui e habilite as permissões de criação, leitura e gravação ao Usuário (1/4 círculo amarelo) para essa entidade clicando uma vez em cada um dos círculos correspondentes.

    ![Microsoft Marketplace Lead Writer - Core Records](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Agora navegue até a guia **Personalização.** Pesquise tor a entidade "Trabalho do sistema" e habilite as permissões de Leitura, Gravação e Apêndice para Organização (verde sólido) para essa entidade clicando quatro vezes em cada um dos círculos correspondentes.

    ![Microsoft Marketplace Lead Writer - personalização](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Salvar e fechar**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configure sua oferta para enviar leads para a Dinâmica 365 para engajamento do cliente

Quando estiver pronto para configurar as informações de gerenciamento de chumbo para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página **de configuração oferta** para obter sua oferta.
2. Selecione **Conectar** na seção Gerenciamento de líderes.

    ![Conecte-se ao gerenciamento de líderes](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Na janela pop-up de detalhes da Conexão, selecione **Dinâmica 365 para engajamento do cliente** para o destino principal

    ![Detalhes de conexão - destino principal](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Forneça a URL de ocorrência dinâmica `https://contoso.crm4.dynamics.com` **365,** como .

5. Selecione o método de **Autenticação,** Diretório Ativo do Azure ou Office 365. 
6. Se você selecionou o Azure Active Directory, forneça `23456052-aaaa-bbbb-8662-1234df56788f`o **ID do aplicativo (cliente)** (exemplo: ), **ID do diretório** (exemplo: `12345678-8af1-4asf-1234-12234d01db47`) e **segredo do cliente** (exemplo: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Detalhes de conexão - Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Se você selecionou o Office 365, `contoso@contoso.onmicrosoft.com`forneça o nome `P@ssw0rd`do **Usuário** (exemplo: ) e senha (exemplo: ).

    ![Detalhes da conexão - Nome do usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

8. **E-mail de contato** - Forneça e-mails para pessoas da sua empresa que devem receber notificações por e-mail quando um novo lead é recebido. Você pode fornecer vários e-mails separando-os com ponto e vírgula.
9. Selecione **OK**.

Para ter certeza de que você se conectou com sucesso a um destino de chumbo, clique no botão validar. Se for bem sucedido, você terá uma pista de teste no destino principal.

![Gerenciamento de chumbo - conta de armazenamento de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!Note]
>Você deve terminar de configurar o resto da oferta e publicá-la antes de receber leads para a oferta.
