---
title: 'Início Rápido: Veja a lista de aplicativos que estão usando seu locatário do Azure AD (Azure Active Directory) para gerenciamento de identidades'
description: Neste guia de início rápido, use o portal do Azure para ver a lista de aplicativos que estão registrados para usar seu locatário do Azure AD (Azure Active Directory) para o gerenciamento de identidades.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249cdbef6214eb8c66da841d9eae817bcf794996
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055385"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Início Rápido: Veja a lista de aplicativos que estão usando seu locatário do Azure AD (Azure Active Directory) para gerenciamento de identidades

Comece a usar o Azure AD como seu sistema de IAM (Gerenciamento de Identidades e Acesso) para os aplicativos que sua organização usa. Neste guia de início rápido, você verá os aplicativos, também conhecidos como apps, que já estão configurados para usar o locatário do Azure AD como seu provedor de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Para ver os aplicativos que foram registrados em seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Recomendamos usar um ambiente que não seja de produção para testar as etapas neste início rápido.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Localizar a lista de aplicativos em seu locatário

Os aplicativos registrados com seu locatário do Azure AD são visíveis na seção **Aplicativos empresariais** do portal do Azure.

Para ver os aplicativos registrados em seu locatário:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No painel de navegação à esquerda, escolha **Azure Active Directory**.
3. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.
4. No menu suspenso **Tipo de Aplicativo**, selecione **Todos os Aplicativos** e escolha **Aplicar**. Uma amostra aleatória de seus aplicativos de locatário é exibida.
5. Para exibir mais aplicativos, selecione **Carregar mais** na parte inferior da lista. Se houver diversos aplicativos em seu locatário, poderá ser mais fácil pesquisar determinado aplicativo, em vez de percorrer a lista. A pesquisa de um aplicativo específico será abordada posteriormente neste guia de início rápido.

## <a name="select-viewing-options"></a>Selecionar opções de exibição

Selecionar as opções de acordo com o que você está procurando.

1. É possível exibir os aplicativos por **Tipo de Aplicativo**, **Status do Aplicativo** e **Visibilidade do Aplicativo**.
2. Em **Tipo de Aplicativo**, escolha uma destas opções:
    - **Aplicativos Empresariais** mostra os aplicativos que não são da Microsoft.
    - **Aplicativos da Microsoft** mostra aplicativos da Microsoft.
    - **Todos os aplicativos** mostra aplicativos da Microsoft e de outros.
3. Em **Status do Aplicativo**, escolha **Qualquer um**, **Desabilitado** ou **Habilitado**. A opção **Qualquer um** inclui aplicativos habilitados e desabilitados.
4. Em **Visibilidade do Aplicativo**, escolha **Qualquer um** ou **Oculto**. A opção **Oculto** mostra os aplicativos que estão no locatário, mas não estão visíveis para os usuários.
5. Depois de escolher as opções desejadas, selecione **Aplicar**.

## <a name="search-for-an-application"></a>Pesquisar um aplicativo

Para pesquisar aplicativo específico:

1. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e escolha **Aplicar**.
2. Insira o nome do aplicativo que você deseja encontrar. Se o aplicativo tiver sido adicionado ao locatário do Azure AD, ele será exibido nos resultados da pesquisa. Este exemplo mostra que o GitHub não foi adicionado aos aplicativos de locatário.
    ![O exemplo mostra que um aplicativo não foi adicionado ao locatário](media/view-applications-portal/search-for-tenant-application.png)
3. Tente inserir as primeiras letras do nome de um aplicativo. Este exemplo mostra todos os aplicativos que começam com **Vendas**.
    ![O exemplo mostra todos os aplicativos que começam com vendas](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como exibir os aplicativos no locatário do Azure AD. Você aprendeu como filtrar a lista de aplicativos por tipo de aplicativo, status e visibilidade. Você também aprendeu a procurar determinado aplicativo.

- [Adicionar um aplicativo](add-application-portal.md)
- [Configurar um aplicativo](add-application-portal-configure.md)
- [Configurar logon único](add-application-portal-setup-sso.md)
- [Excluir um aplicativo](delete-application-portal.md)
