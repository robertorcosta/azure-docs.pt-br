---
title: Redefinição de senha por autoatendimento da licença – Azure Active Directory
description: Saiba mais sobre a diferença Azure Active Directory requisitos de licenciamento de redefinição de senha de autoatendimento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393072"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisitos de licenciamento para Azure Active Directory redefinição de senha de autoatendimento

Para reduzir as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo, as contas de usuário no Azure Active Directory (Azure AD) podem ser habilitadas para SSPR (redefinição de senha de autoatendimento). Os recursos que compõem SSPR incluem alteração de senha, redefinição, desbloqueio e Write-back para um diretório local. Os recursos básicos do SSPR estão disponíveis para o Office 365 e todos os usuários do Azure AD sem nenhum custo.

Este artigo detalha as diferentes maneiras pelas quais a redefinição de senha de autoatendimento pode ser licenciada e usada. Para obter detalhes específicos sobre preços e cobrança, consulte a [página de preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Comparar edições e recursos

O SSPR é licenciado por usuário. Para manter a conformidade, as organizações são obrigadas a atribuir a licença apropriada aos seus usuários.

A tabela a seguir descreve os diferentes cenários de SSPR para alteração de senha, redefinição ou Write-back local e quais SKUs fornecem o recurso.

| Recurso | AD do Azure Gratuito | Office 365 Business Premium | Microsoft 365 Business | O Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| **Alteração de senha de usuário somente em nuvem**<br />Quando um usuário no Azure AD sabe sua senha e deseja alterá-la para algo novo. | ● | ● | ● | ● |
| **Redefinição de senha de usuário somente em nuvem**<br />Quando um usuário no Azure AD esqueceu sua senha e precisa redefini-la. | | ● | ● | ● |
| **Alteração ou redefinição de senha de usuário híbrido com write-back local**<br />Quando um usuário no Azure AD é sincronizado de um diretório local usando Azure AD Connect deseja alterar ou redefinir sua senha e também gravar a nova senha no local. | | | ● | ● |

> [!WARNING]
> Os planos de licenciamento do Office 365 autônomos não dão suporte a SSPR com write-back local. Esses planos de licenciamento do Office 365 exigem Azure AD Premium P1, Premium P2 ou Microsoft 365 Business para que essa funcionalidade funcione.

Para obter informações adicionais sobre licenciamento, incluindo custos, consulte as seguintes páginas:

* [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar licenciamento com base em grupo ou usuário

O Azure AD dá suporte ao licenciamento baseado em grupo. Os administradores podem atribuir licenças em massa a um grupo de usuários, em vez de atribuí-las uma por vez. Para obter mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador deve especificar a propriedade **local de uso** no usuário. A atribuição de licenças pode ser feita na seção**configurações** de**perfil** > do **usuário** > no portal do Azure. *Ao usar a atribuição de licença de grupo, qualquer usuário sem um local de uso especificado herda o local do diretório.*

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: habilitar redefinição de senha de autoatendimento (SSPR)](tutorial-enable-sspr.md)
