---
title: Alterar o nome ou logotipo de um aplicativo corporativo no Azure AD
description: Como alterar o nome ou o logotipo de um aplicativo corporativo personalizado no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138494"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Alterar o nome ou logotipo de um aplicativo corporativo no Azure Active Directory

É fácil alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure AD (Azure Active Directory). Você deve ter as permissões apropriadas para fazer essas alterações, e você deve ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Como alterar o nome ou logotipo de um aplicativo corporativo?

1. Faça login no [portal do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que é um administrador global para o diretório. A página **central do diretório ativo do Azure** é exibida.
2. No painel esquerdo, selecione **Aplicativos Empresariais**. A lista de aplicativos corporativos é exibida.
3. Selecione um aplicativo. A página de visão geral do aplicativo é exibida.
4. No painel visão geral do aplicativo, o título **Gerenciar,** selecione **Propriedades**. A página **Propriedades** é exibida.
5. Se você quiser alterar o nome, selecione a caixa **Nome,** digite o novo nome e **pressione Enter**.
6. Se você quiser alterar o logotipo, encontre o campo **Logo** e selecione o ícone da pasta ao lado da Caixa Selecionar uma caixa **de arquivos,** que está abaixo da imagem atual do logotipo do aplicativo.

   ![Seleção do comando de propriedades](./media/change-name-or-logo-portal/change-logo.png)

   Caso contrário, se você não está alterando o logotipo, vá para o passo 8.
7. No seletor de arquivos, selecione o arquivo que deseja como o novo logotipo. O nome do arquivo aparece na caixa abaixo da imagem do logotipo atual.

   > [!NOTE]
   > O Azure exige que a imagem do logotipo seja um arquivo PNG, e aplica limites na largura, altura e tamanho do arquivo. Os logotipos personalizados devem &times; ter exatamente 215 215 pixels de tamanho e estar em formato PNG. Recomendamos que você use um fundo de cor sólido sem transparência no logotipo do aplicativo para aparecer melhor para os usuários.
8. Selecione **Salvar**. Se você escolheu um novo logotipo, a imagem do campo **Logo** será alterada para refletir o novo arquivo de logotipo.

## <a name="next-steps"></a>Próximas etapas

* [Guia de Início Rápido: exibir os grupos e membros da sua organização no Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
