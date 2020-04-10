---
title: Pré-requisitos para a API de relatórios do Azure Active Directory | Microsoft Docs
description: Aprenda sobre os pré-requisitos para acessar a API de relatório do Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991255"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Pré-requisitos para acessar a API de relatório do Azure Active Directory

As [APIs de relatório do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de linguagens e ferramentas de programação.

A API de relatório usa [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) para autorizar o acesso às APIs da Web.

Para preparar seu acesso à API de relatório, você precisa:

1. [Atribuir funções](#assign-roles)
2. [Requisitos de licença](#license-requirements)
3. [Registrar um aplicativo](#register-an-application)
4. [Permissões de concessão](#grant-permissions)
5. [Reunir definições de configuração](#gather-configuration-settings)

## <a name="assign-roles"></a>Atribuir funções

Para obter acesso aos dados de relatórios por meio da API, você precisa ter uma das seguintes funções atribuídas:

- Leitor de segurança

- Administrador de segurança

- Administrador global

## <a name="license-requirements"></a>Requisitos de licença

Para acessar os relatórios de login de um inquilino, um inquilino azure AD deve ter a licença Azure AD Premium associada. A licença Azure AD Premium P1 (ou acima) é necessária para acessar relatórios de login para qualquer inquilino Azure AD. Alternativamente, se o tipo de diretório for Azure AD B2C, os relatórios de login são acessíveis através da API sem qualquer exigência adicional de licença. 


## <a name="register-an-application"></a>Registrar um aplicativo

O registro é necessário mesmo se você estiver acessando a API de reportagem usando um script. O registro lhe dá um **ID de solicitação,** que é necessário para as chamadas de autorização e permite que seu código receba tokens.

Para configurar seu diretório para acessar a API de relatórios do Microsoft Azure Active Directory, você deve entrar no [Portal do Azure](https://portal.azure.com) com uma conta de administrador do Azure que também é membro da função de diretório **Administrador Global** no seu Locatário do Azure AD.

> [!IMPORTANT]
> Aplicativos em execução sob credenciais com privilégios de administrador podem ser muito poderosos, portanto, certifique-se de manter o ID do aplicativo e as credenciais secretas em um local seguro.
> 

**Para registrar um aplicativo do Microsoft Azure Active Directory:**

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** do painel de navegação à esquerda.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página **Azure Active Directory**, selecione **Registros de aplicativo**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na página de inscrições do **App,** selecione **Novo registro**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. A página **Registrar um aplicativo:**

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Na caixa de texto **Nome**, digite `Reporting API application`.

    b. Para **o tipo de contas suportadas,** selecione Contas somente nesta **organização**.

    c. Na **URL Redirecionar** selecione Caixa `https://localhost`de texto **da Web,** digite .

    d. Selecione **Registrar**. 


## <a name="grant-permissions"></a>Conceder permissões 

Dependendo da API que deseja acessar, você precisa garantir ao seu aplicativo as permissões a seguir:  

| API | Permissão |
| --- | --- |
| Windows Azure Active Directory | Ler dados do diretório |
| Microsoft Graph | Ler todos os dados de log de auditoria |


![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A seção a seguir lista as etapas para ambas as APIs. Se não quiser acessar uma das APIs, você poderá ignorar as etapas relacionadas.

**Para conceder ao seu aplicativo permissões para usar as APIs:**


1. Selecione **permissões de API** e **adicione uma permissão**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **página de permissões da API de solicitação,** localize o Gráfico de Diretório Ativo **do API** **Azure legado.** 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na página **de permissões necessárias,** selecione **Permissões de aplicativo,** expanda diretório de caixa **de** seleção **Directory.ReadAll**.  Selecione **Adicionar Permissões**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na página **Relatórios da API - Permissões de API,** selecione **O consentimento do admin Grant**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: **O Microsoft Graph** é adicionado por padrão durante o registro da API.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Reunir definições de configuração 

Esta seção mostra como obter as seguintes configurações de seu diretório:

- Nome de domínio
- ID do Cliente
- Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatórios. 

### <a name="get-your-domain-name"></a>Obter seu nome de domínio

**Para obter seu nome de domínio:**

1. No [Portal do Azure Microsoft](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página do **Active Directory do Azure**, selecione **Nomes de domínio personalizados**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copie o nome do domínio da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obtenha a ID do cliente do aplicativo

**Para obter a ID do cliente do aplicativo:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecione seu aplicativo na página **Registros de aplicativo**.

3. Na página do aplicativo, navegue até **ID do aplicativo** e selecione **clique para copiar**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Obter seu segredo do cliente do aplicativo
 Evite erros ao tentar acessar logs de auditoria ou fazer login usando a API.

**Para obter seu segredo do cliente do aplicativo:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecione seu aplicativo na página **Registros de aplicativo**.

3.  Selecione **Certificados e Segredos** na página do **aplicativo da API,** na seção **Segredos do Cliente,** clique em **+ Novo segredo do cliente**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na **página adicionar um cliente secreto,** adicione:

    a. Na caixa de texto **Descrição**, digite `Reporting API`.

    b. Como **Expira**, selecione **Em 2 anos**.

    c. Clique em **Save** (Salvar).

    d. Copie o valor da chave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Solucionar problemas de erros na API de relatório

Esta seção lista as mensagens de erro comuns que você pode encontrar ao acessar relatórios de atividade usando a API do Gráfico do Microsoft e as etapas para sua resolução.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Erro: Falha ao obter funções do usuário do Microsoft Graph

 Faça login em sua conta usando ambos os botões de login na UI do Graph Explorer para evitar um erro ao tentar entrar usando o Graph Explorer. 

![Explorador do Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Erro: Falha ao fazer verificação de licença premium do Microsoft Graph 

Se você encontrar essa mensagem de erro ao tentar acessar entradas usando o Explorador do Graph, escolha **Modificar permissões** em sua conta na barra de navegação à esquerda e selecione **Tasks.ReadWrite** e **Directory.Read.All**. 

![Modificar a interface do usuário de permissões](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: O inquilino não é B2C ou o inquilino não tem licença premium

O acesso a relatórios de entrada requer uma licença do Azure Active Directory Premium 1 (P1). Se essa mensagem de erro for exibida quando você acessar as entradas, verifique se o locatário está licenciado com uma licença do Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Erro: As funções permitidas não incluem o Usuário. 

 Evite erros ao tentar acessar logs de auditoria ou fazer login usando a API. Certifique-se de que sua conta faz parte da função **leitora** de segurança ou **leitor de relatórios** no inquilino do Diretório Ativo do Azure.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: O aplicativo não tem a permissão 'Ler dados do diretório' do AAD 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Erro: API do Microsoft Graph ausente 'Leia todos os dados do registro de auditoria'

Siga as etapas nos [Pré-requisitos para acessar a API de relatórios do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja sendo executado com o conjunto certo de permissões. 

## <a name="next-steps"></a>Próximas etapas

* [Obter dados usando a API de Relatório do Azure AD com certificados](tutorial-access-api-with-certificates.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
