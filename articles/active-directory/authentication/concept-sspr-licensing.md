---
title: Redefinição de senha por autoatendimento da licença – Azure Active Directory
description: Conheça a diferença a senha de autoatendimento do Azure Active Directory
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
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393072"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisitos de licenciamento para redefinição de senha de autoatendimento do Azure Active Directory

Para reduzir as chamadas de help desk e a perda de produtividade quando um usuário não pode fazer login em seu dispositivo ou aplicativo, as contas de usuário no Azure Active Directory (Azure AD) podem ser habilitadas para redefinição de senha de autoatendimento (SSPR). Os recursos que compõem o SSPR incluem alteração de senha, redefinição, desbloqueio e regravação para um diretório local. Os recursos básicos do SSPR estão disponíveis para o Office 365 e todos os usuários do Azure AD sem nenhum custo.

Este artigo detalha as diferentes maneiras pelas quais a redefinição de senha de autoatendimento pode ser licenciada e usada. Para obter detalhes específicos sobre preços e faturamento, consulte a [página de preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Comparar edições e recursos

O SSPR é licenciado por usuário. Para manter a conformidade, as organizações são obrigadas a atribuir a licença apropriada aos seus usuários.

A tabela a seguir descreve os diferentes cenários de SSPR para alteração de senha, redefinição ou regravação no local e quais SKUs fornecem o recurso.

| Recurso | AD do Azure Gratuito | Office 365 Business Premium | Microsoft 365 Business | O Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| **Alteração de senha de usuário somente na nuvem**<br />Quando um usuário no Azure AD sabe sua senha e quer mudá-la para algo novo. | ● | ● | ● | ● |
| **Redefinição de senha de usuário somente na nuvem**<br />Quando um usuário no Azure AD esqueceu sua senha e precisa redefini-la. | | ● | ● | ● |
| **Mudança de senha do usuário híbrido ou redefinição com writeback on-prem**<br />Quando um usuário no Azure AD é sincronizado a partir de um diretório local usando o Azure AD Connect deseja alterar ou redefinir sua senha e também escrever a nova senha de volta para on-prem. | | | ● | ● |

> [!WARNING]
> Os planos de licenciamento do Office 365 autônomo não suportam o SSPR com writeback no local. Esses planos de licenciamento do Office 365 exigem que o Azure AD Premium P1, Premium P2 ou Microsoft 365 Business funcionem.

Para obter informações adicionais de licenciamento, incluindo custos, consulte as seguintes páginas:

* [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Mobilidade Empresarial + Segurança](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Negócios da Microsoft 365](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar licenciamento com base em grupo ou usuário

O Azure AD suporta licenciamento em grupo. Os administradores podem atribuir licenças em massa a um grupo de usuários, em vez de atribuí-las uma por vez. Para obter mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um usuário, o administrador deve especificar a propriedade **de localização de uso** no usuário. A atribuição de licenças pode ser feita na seção**Configurações** **do Perfil** > do **Usuário** > no portal Azure. *Ao usar a atribuição de licença de grupo, qualquer usuário sem um local de uso especificado herda o local do diretório.*

## <a name="next-steps"></a>Próximas etapas

Para começar com o SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Habilite a redefinição de senha de autoatendimento (SSPR)](tutorial-enable-sspr.md)
