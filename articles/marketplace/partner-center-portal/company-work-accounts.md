---
title: Contas de trabalho da empresa e Centro de Parceiros
description: Como verificar se sua empresa tem uma conta de trabalho configurada com a Microsoft, criar uma nova conta de trabalho ou configurar várias contas de trabalho para usar com o Partner Center.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c4e7427d87c5f88d8c686b867ef88ceb05f28286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281418"
---
# <a name="company-work-accounts-and-partner-center"></a>Contas de trabalho da empresa e Centro de Parceiros

O Partner Center usa contas de trabalho da empresa, também conhecidas como inquilinos do Azure Active Directory (AD), para gerenciar o acesso à conta para vários usuários, permissões de controle, grupos de host e aplicativos e manter dados de perfil. Ao vincular o domínio da conta de e-mail de trabalho da sua empresa à sua conta do Partner Center, os funcionários da sua empresa podem entrar no Partner Center para gerenciar ofertas de marketplace usando seus próprios nomes de usuário e senhas de conta de trabalho.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verifique se sua empresa já tem uma conta de trabalho

Se sua empresa assinou um serviço de nuvem da Microsoft, como Azure, Microsoft Intune ou Office 365, então você já tem um domínio de conta de e-mail de trabalho (também referido como um inquilino do Azure Active Directory) que pode ser usado com o Partner Center.

Siga estas etapas para verificar:
1. Faça login no portal de administradores https://portal.azure.comdo Azure em .
2. Selecione O Diretório Ativo do **Azure** no menu de navegação à esquerda e, em seguida, selecione **Nomes de domínio personalizados**.
3. Caso você já tenha uma conta corporativa, seu nome de domínio estará listado.

Se sua empresa ainda não possui uma conta de trabalho, uma será criada para você durante o processo de inscrição do Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Configure várias contas de trabalho

Antes de decidir usar uma conta de trabalho existente, considere quantos usuários na conta de trabalho precisarão acessar a Partner Center. Se você tem usuários na conta de trabalho que não precisarão acessar o Partner Center, você pode querer considerar a criação de várias contas de trabalho, de modo que apenas aqueles usuários que precisarão acessar o Partner Center sejam representados em uma determinada conta.

## <a name="create-a-new-work-account"></a>Crie uma nova conta de trabalho

Para criar uma nova conta de trabalho para sua empresa, siga os passos abaixo. Você pode precisar solicitar ajuda de quem tiver permissões administrativas na conta Microsoft Azure da sua empresa.

1. Faça login no [portal Microsoft Azure](https://portal.azure.com).
2. No menu de navegação à esquerda, selecione os -> **usuários**do **Diretório Ativo do Azure**.
3. Selecione **Novo usuário** e crie uma nova conta de trabalho do Azure inserindo um nome e endereço de e-mail. Certifique-se de que a **função Diretório** está definida como **Usuário** e selecione a caixa de seleção Mostrar **senha** na parte inferior para visualizar e anotar a senha gerada automaticamente.
4. Selecione **Criar** para salvar o novo usuário.

O endereço de e-mail da conta de usuário deve ser um nome de domínio verificado em seu diretório. Você pode listar todos os domínios verificados em seu diretório selecionando**nomes de domínio personalizados** do Diretório ->  **Ativo do Azure**no menu de navegação à esquerda.

Para saber mais sobre como adicionar domínios personalizados no Azure Active Directory, consulte [Adicionar ou associar um domínio no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Solucionar problemas no login de e-mail de trabalho

Se você está tendo problemas para entrar em sua conta de trabalho (também conhecido como seu inquilino Azure AD), encontre o cenário no diagrama abaixo que melhor corresponda à sua situação e siga os passos recomendados.

![Diagrama para solucionar problemas de login de conta de trabalho](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Próximas etapas

- [Gerencie sua conta de Mercado Comercial no Partner Center](./manage-account.md) 
