---
title: CRM dinâmico | Mercado Azure
description: Configure o gerenciamento de leads para o Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 524ae203a311d538431205bf8c6498de45aeb4d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280296"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configurar o gerenciamento de leads para o Dynamics CRM Online

Este artigo descreve como configurar o Dynamics CRM Online para processar os leads de vendas.

## <a name="prerequisites"></a>Pré-requisitos

As permissões de usuário a seguir são necessárias para concluir as etapas neste artigo:
- Você precisa ser um administrador na instância do Dynamics CRM Online para instalar uma solução.
- Você precisa ser um administrador de locatários para criar uma nova conta de serviço para serviço de lead.

<a name="install-the-solution"></a>Instalar a solução
--------------------

1.  Baixe a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente.

2.  Abra o Dynamics CRM Online e acesse Configurações.
    >[!NOTE]
    >Caso não veja as opções na próxima captura de tela, é porque você não tem as permissões necessárias.
 
       ![Exibição da configuração do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecione **Importar** e depois selecione a solução que você baixou na etapa 1.
 
    ![Opção de importação do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Conclua a instalação da solução.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar leads em sua instância do Dynamics CRM, você precisa compartilhar uma conta de serviço conosco e configurar permissões para essa conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o **Azure Active Directory** ou o **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos essa opção, porque você tem o benefício adicional de nunca precisar atualizar seu nome de usuário e senha para continuar recebendo leads. Para usar a opção do Azure Active Directory, você fornece a ID do aplicativo, a Chave de aplicativo e a ID do diretório do aplicativo do Active Directory.

Use as etapas a seguir para configurar o Azure Active Directory para o Dynamics CRM.

1.  Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço do Azure Active Directory.

2.  Selecione **Propriedades** e copie o **Id do diretório**. Esta é a sua identificação de conta de inquilino que você precisa usar no Portal de Parceiros na Nuvem.

    ![Obter ID do diretório](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecione **inscrições de aplicativos**e selecione Novo registro de **inscrição**.
4.  Insira o nome do aplicativo.
5.  Para tipo, selecione **Aplicativo Web/API**.
6.  Forneça uma URL. Este campo não é necessário para leads, mas é necessário para criar um aplicativo.
7. Selecione **Criar**.
8.  Agora que seu aplicativo está registrado, selecione **Propriedades** e selecione **copiar o Id do aplicativo**. Você usará essas informações de conexão no Portal do Parceiro na Nuvem.
9.  Em Propriedades, defina o aplicativo como Multilocatário e, em seguida, selecione **Salvar**.

10. Selecione **Chaves** e crie uma nova chave com a Duração definida para *Nunca expira*. Selecione **Salvar** para criar a chave. 
11. No menu Chaves, selecione **Copiar o valor da chave.** Salve uma cópia desse valor, pois você precisará dele para o Portal do Cloud Partner.
    
    ![Dynamics obter chave registrada](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecione **Permissões necessárias** e, em seguida, selecione **Adicionar**. 
13. Selecione **Dynamics CRM Online** como a nova API e marque a permissão para *Acessar CRM Online como usuários da organização*.

14. No Dynamics CRM, vá para os usuários e selecione o menu suspenso "Usuários habilitados" para alternar para **Usuários do aplicativo**.
    
    ![Usuários do aplicativo](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecione **Novo** para criar um novo usuário. Selecione a lista suspensa **USUÁRIO: USUÁRIO DO APLICATIVO**.
    
    ![Adicionar novo usuário do aplicativo](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Em **Novo Usuário**, forneça o nome e o email que você deseja usar com essa conexão. Cole a **ID do aplicativo** para o aplicativo criado no portal do Azure.

     ![Configurar um novo usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Vá para "Configurações de segurança" neste artigo para concluir a configuração de conexão para este usuário.

### <a name="office-365"></a>Office 365

Se você não quiser usar o Azure Active Directory, você pode registrar um novo usuário no *centro de administradores do Microsoft 365*. Será necessário atualizar seu nome de usuário e senha a cada 90 dias para continuar a receber leads.

Use as etapas a seguir para configurar o Office 365 para o Dynamics CRM.

1. Entre no [Centro de Administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione **o azulejo de admin.**

    ![Office Online Admin](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecione **Adicionar um usuário**.

    ![Adicionar um usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Crie um novo usuário para o serviço de gravação do lead. Defina as seguintes configurações:

    -   Forneça uma senha e desmarque a opção "Trocar senha do usuário na primeira conexão".
    -   Selecione "Usuário (sem acesso de administrador)" como a função para o usuário.
    -   Selecione a licença do produto mostrada na próxima captura de tela. Você será cobrado pela licença que você escolher. A solução também funcionará com a licença Básica do Dynamics CRM Online.
    
    ![Configurar permissões de usuário e licença](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Configurações de segurança

A etapa final é permitir que o usuário que você criou grave os leads.

1.  Entre no Dynamics CRM Online.
2.  Em **Configurações**, selecione **Segurança**.
    
    ![Configurações de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecione o usuário que você criou em **Permissões de usuário** e, em seguida, selecione **Gerenciar funções de usuário**. Marque **Gravador de Leads do Microsoft Marketplace** para atribuir a função.

    ![Atribuir função de usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Essa função é criada pela solução que você importou e tem permissões apenas para gravar os leads e acompanhar a versão da solução para garantir a compatibilidade.

4.  Em Segurança, selecione **Funções de segurança** e encontre a função de Gravador de leads do Microsoft Marketplace.
    
    ![Configurar o gravador de leads de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecione a guia **Core Records.Enable** Create/Read/Write for the User Entity UI.

    ![Habilitar criação/leitura/gravação para o usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Conclusão

Conclua a configuração do Dynamics CRM para o gerenciamento de leads, adicionando as informações de conta geradas ao Portal do Cloud Partner. Por exemplo: 

-   **Azure Active Directory** - **ID do aplicativo** (exemplo: *23456052-aaaa-bbbb-8662-1234df56788f*), **ID do diretório** (exemplo: *12345678-8af1-4asf-1234-12234d01db47*) e **Chave de aplicativo** (exemplo: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **URL** (exemplo: *https://contoso.crm4.dynamics.com*), **Nome de usuário** (exemplo: *contoso\@contoso.onmicrosoft.com*) e **Senha** (exemplo: *P\@ssw0rd*).
