---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471479"
---
O Acesso Condicional permite o controle de acesso de grãos finos por aplicativo. Para usar o Acesso Condicional, você deve ter o Azure AD Premium 1 ou um licenciamento maior aplicado aos usuários que estarão sujeitos às regras de Acesso Condicional.

1. Navegue até os **aplicativos Corporativos - Todas as** páginas de aplicativos e clique em **Azure VPN**.

   - Clique **em Acesso Condicional**.
   - Clique em **Nova política** para abrir o painel **Novo**.
2. No **painel Novo,** navegue até **Atribuições -> Usuários e grupos**. Na guia **Usuários e grupos ->** **Incluir:**

   - Clique em **Selecionar usuários e grupos**.
   - Verifique **usuários e grupos**.
   - Clique **em Selecionar** para selecionar um grupo ou conjunto de usuários a serem afetados pelo MFA.
   - Clique em **Concluído**.

   ![Atribuições](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. No **painel Novo,** navegue até o painel **Access -> Grant:**

   - Clique **em Grant access**.
   - Clique **em Exigir autenticação multifatorial**.
   - Clique **em Exigir todos os controles selecionados**.
   - Clique em **Selecionar**.
   
   ![Acesso a subvenções - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Na seção **Ativar diretiva:**

   - Selecione **Em**.
   - Clique em **Criar**.

   ![Habilitar política](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)