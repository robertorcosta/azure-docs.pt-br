---
title: Usando o serviço conectado active directory (Visual Studio)
description: Adicionar um Active Directory do Azure usando a caixa de diálogo Adicionar Serviços Conectados do Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: d5b6452684757aead684356fd9bb032b90c58c4a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535818"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicione um Diretório Ativo do Azure usando serviços conectados no Visual Studio

Usando o Azure Active Directory (Azure AD), você pode suportar o SSO (Single Sign-On, entrada em ação única) para ASP.NET aplicativos da Web MVC ou autenticação de diretório ativo em serviços de API web. Com a Autenticação do Microsoft Azure Active Directory, os usuários podem usar suas contas no Azure Active Directory para se conectar aos aplicativos Web. As vantagens da Autenticação Azure AD com a API web incluem segurança de dados aprimorada ao expor uma API de um aplicativo web. Com o AD do Azure, você não precisa gerenciar um sistema de autenticação separado com a própria conta e gerenciamento de usuários.

Este artigo e os artigos complementares fornecem detalhes sobre como usar o recurso de Serviço Conectado do Visual Studio para o Active Directory. O recurso está disponível no Visual Studio 2015 e posteriormente.

No momento, o serviço conectado do Active Directory não dá suporte a aplicativos ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Conta do Azure: se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** ou posterior. [Baixe Visual Studio agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Conectar-se ao Azure Active Directory usando a caixa de diálogo Serviços Conectados

1. No Visual Studio, crie ou abra um projeto ASP.NET MVC ou um projeto de ASP.NET Web API. Você pode usar MVC, API Web, Aplicativo de Página Única, Aplicativo de API do Azure, Aplicativo Móvel do Azure e modelos do Serviço Móvel do Azure.

1. Selecione o comando do menu **Projeto > Adicionar serviço conectado... ** ou clique duas vezes no nó **Serviços conectados** encontrado no projeto em Gerenciador de Soluções.

1. Na página **Serviços Conectados**, selecione **Autenticação com o Azure Active Directory**.

    ![Página Serviços Conectados](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na página **Introdução**, selecione **Avançar**. Se você vir erros nessa página, consulte [Diagnosticando erros com o Serviço Conectado do Azure Active Directory](vs-active-directory-error.md).

    ![Página Introdução](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na página **Logon único**, selecione um domínio na lista suspensa **Domínio**. A lista contém todos os domínios acessíveis pelas contas listadas na caixa de diálogo Configurações da conta do Visual Studio (**Configurações de > de arquivo da conta...**). Como alternativa, você pode inserir um nome de domínio se você não encontrar `mydomain.onmicrosoft.com`o que você está procurando, como . Você pode escolher a opção para criar um novo aplicativo do Azure Active Directory ou usar as configurações de um aplicativo do Azure Active Directory existente. Quando terminar, escolha **Avançar**.

    ![Página Logon único](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na página **Acesso ao diretório**, selecione a opção **Ler dados do diretório** conforme desejado. Os desenvolvedores geralmente incluem essa opção.

    ![Página Acesso ao diretório](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione **Concluir** para iniciar as modificações em seu projeto para habilitar a autenticação do Microsoft Azure Active Directory. Durante esse tempo, o Visual Studio mostra progresso:

    ![Progresso do serviço conectado do Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Quando o processo for concluído, o Visual Studio abrirá seu navegador em um dos seguintes artigos, conforme apropriado para o tipo de projeto:

    - [Introdução aos projetos do .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Introdução aos projetos da WebAPI](vs-active-directory-webapi-getting-started.md)

1. Você também pode ver o domínio do Active Directory no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Como o projeto é modificado

Quando você adiciona o serviço conectado ao assistente, o Visual Studio adiciona o Azure Active Directory e referências associadas a seu projeto. Os arquivos de configuração e os arquivos de código em seu projeto também são modificados para adicionar suporte ao AD do Azure. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Confira os seguintes artigos para obter detalhes:

- [O que aconteceu com meu projeto do .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [O que aconteceu com meu projeto da API Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Próximas etapas

- [Cenários de autenticação para Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicionar a opção Entrar com uma Conta da Microsoft a um aplicativo Web ASP.NET](quickstart-v2-aspnet-webapp.md)
