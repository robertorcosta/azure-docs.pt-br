---
title: Gestão de unidades administrativas (preview) - Azure AD | Microsoft Docs
description: Usando unidades administrativas para delegação mais granular de permissões no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028423"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gerenciamento de unidades administrativas no Azure Active Directory (pré-visualização)

Este artigo descreve unidades administrativas no Azure Active Directory (Azure AD). Uma unidade administrativa é um recurso Azure AD que pode ser um contêiner para outros recursos azure AD. Nesta versão de pré-visualização, esses recursos podem ser somente os usuários. Por exemplo, um admin de conta de usuário com escopo de unidade administrativa pode atualizar informações de perfil, redefinir senhas e atribuir licenças para usuários apenas em sua unidade administrativa.

Você pode usar unidades administrativas para delegar permissões administrativas sobre subconjuntos de usuários e aplicar políticas a um subconjunto de usuários. Você pode usar unidades administrativas para delegar permissões a administradores regionais ou para definir políticas em um nível granular.

## <a name="deployment-scenario"></a>Cenário de implantação

Unidades administrativas podem ser úteis em organizações com divisões independentes. Considere o exemplo de uma grande universidade que é composta por muitas escolas autônomas (School of Business, School of Engineering, e assim por diante) que cada uma tem seus próprios administradores de TI que controlam o acesso, gerenciam usuários e definem políticas para sua escola. Um administrador central poderia criar uma unidade administrativa para a Escola de Negócios e povoá-la apenas com os alunos e funcionários da escola de negócios. Em seguida, o administrador central pode adicionar a equipe de TI da escola de negócios a uma função escopo que concede permissões administrativas sobre apenas usuários azure AD na unidade administrativa da escola de negócios.

## <a name="license-requirements"></a>Requisitos de licença

Para usar unidades administrativas é necessário uma licença Azure Active Directory Premium para cada administrador de unidade administrativa. Para obter mais informações, consulte [Getting started with Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Gerenciando unidades administrativas

Nesta versão de pré-visualização, a única maneira de criar e gerenciar unidades administrativas é usar o Módulo de Diretório Ativo do Azure para cmdlets Windows PowerShell, conforme descrito em [Trabalhar com Unidades Administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre os requisitos de software e a instalação do módulo Azure AD e para obter informações de referência sobre os cmdlets do Módulo Ad Do Azure para gerenciamento de unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, consulte [PowerShell do Azure Active Directory](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Próximas etapas

[Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md)
