---
title: Excluir serviços de domínio do diretório ativo do Azure | Microsoft Docs
description: Saiba como desativar ou excluir um domínio gerenciado do Azure Active Directory Services usando o portal Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655476"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Exclua um domínio gerenciado do Azure Active Directory Domain Services usando o portal Azure

Se você não precisar mais de um domínio gerenciado, poderá excluir uma instância do Azure Active Directory Domain Services (Azure AD DS). Não há opção de desativar ou desativar temporariamente um domínio gerenciado pelo Azure AD DS. Excluir o domínio gerenciado do Azure AD DS não exclui ou afeta negativamente o inquilino Azure AD. Este artigo mostra como usar o portal Azure para excluir um domínio gerenciado pelo Azure AD DS.

> [!WARNING]
> **A exclusão é permanente e não pode ser revertida.**
> Quando você exclui um domínio gerenciado pelo Azure AD DS, ocorrem as seguintes etapas:
>   * Controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
>   * Os dados em um domínio gerenciado serão excluídos permanentemente. Esses dados incluem OUs personalizados, GPOs, registros dns personalizados, diretores de serviço, GMSAs, etc. que você criou.
>   * Os computadores que ingressaram no domínio gerenciado perdem sua relação de confiança com o domínio e precisam ser retirados do domínio.
>       * Você não pode entrar nessas máquinas usando credenciais corporativas. Em vez disso, você deve usar as credenciais de administrador local para a máquina.

## <a name="delete-the-managed-domain"></a>Exclua o domínio gerenciado

Para excluir um domínio gerenciado pelo Azure AD DS, complete as seguintes etapas:

1. No portal Azure, procure e selecione **Azure AD Domain Services**.
1. Selecione o nome do seu domínio gerenciado pelo Azure AD DS, como *aaddscontoso.com*.
1. Na página **Visão Geral**, selecione **Excluir**. Para confirmar a exclusão, digite novamente o nome de domínio do domínio gerenciado e selecione **Excluir**.

Pode levar de 15 a 20 minutos ou mais para excluir o domínio gerenciado pelo Azure AD DS.

## <a name="next-steps"></a>Próximas etapas

Considere [compartilhar feedback][feedback] para os recursos que você gostaria de ver no Azure AD DS.

Se você quiser começar com o Azure AD DS novamente, consulte [Criar e configurar uma instância de serviços de domínio do diretório ativo do Azure][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
