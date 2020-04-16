---
title: Solucionando problemas unidades administrativas e FAQ - Azure Active Directory | Microsoft Docs
description: Investigar unidades administrativas para delegação de permissões com escopo restrito no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428141"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Solução de problemas e perguntas frequentes para unidades administrativas no Azure Active Directory

Para obter mais controle administrativo granular no Azure Active Directory (Azure AD), você pode atribuir usuários a uma função AD do Azure com um escopo limitado a uma ou mais unidades administrativas (AUs). Você pode encontrar scripts de amostra https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0PowerShell para tarefas comuns em .

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Q: Eu não sou capaz de criar uma unidade administrativa**

**A:** Apenas um administrador global ou administrador de funções privilegiadas pode criar uma unidade administrativa no Azure AD. Verifique se o usuário que tenta criar a unidade administrativa é atribuído à função Administrador Global ou administrador de funções privilegiadas.

**P: Adicionei um grupo à unidade administrativa, mas os membros do grupo ainda não estão aparecendo na unidade administrativa**

**A:** Quando você adiciona um grupo à unidade administrativa, isso não resulta em adicionar todos os membros do grupo à unidade administrativa. Os usuários devem ser diretamente designados para a unidade administrativa.

**P: Acabei de adicionar / removi um membro da unidade administrativa e ele ainda está aparecendo na UI**

**A:** Às vezes, o processamento da adição ou remoção de um ou mais membros da unidade administrativa pode levar alguns minutos para refletir na página das **unidades administrativas.** Você pode optar por esperar alguns minutos para que ele reflita sob as unidades administrativas. Alternativamente, você pode ir diretamente às propriedades do recurso associado e ver se a ação foi concluída. Para obter mais informações sobre usuários e grupos em UA, consulte [Listar unidades administrativas para um usuário](roles-admin-units-add-manage-users.md) e Listar unidades [administrativas para um grupo](roles-admin-units-add-manage-groups.md).

**P: Como administrador de senha delegada em uma unidade administrativa, não posso redefinir a senha de um usuário específico**

**A:** Um administrador atribuído sobre uma unidade administrativa você pode redefinir a senha apenas para usuários atribuídos à sua unidade administrativa. Certifique-se de que o usuário para o qual a redefinição de senha está falhando pertence às unidades administrativas sobre as quais você foi atribuído a função. Se o usuário pertence à mesma unidade administrativa e ainda não pode redefinir a senha do usuário, verifique as funções atribuídas ao usuário. Para evitar uma elevação do privilégio, um administrador com escopo de unidade administrativa não pode redefinir a senha de um usuário atribuído a uma função com um escopo de toda a organização.

**P: Por que as unidades administrativas são necessárias? Não poderíamos ter usado grupos de segurança como forma de definir um escopo?**

**A:** Os grupos de segurança têm um modelo de finalidade e autorização existente. Um administrador de usuário, por exemplo, pode gerenciar a adesão de todos os grupos de segurança na organização Azure AD, como usar grupos para gerenciar o acesso a aplicativos como o Salesforce. Um administrador de usuário não deve ter a capacidade de gerenciar o próprio modelo de delegação, o que seria o resultado se grupos de segurança fossem estendidos para apoiar cenários de "agrupamento de recursos". As unidades administrativas, como unidades organizacionais no Diretório Ativo do Servidor Windows, destinam-se a fornecer uma maneira de escopo da administração de uma ampla gama de objetos de diretório. Os próprios grupos de segurança podem ser membros de escopos de recursos. Usar grupos de segurança para definir o conjunto de grupos de segurança que um administrador pode gerenciar pode se tornar confuso.

**P: O que significa adicionar um grupo a uma unidade administrativa?**

**A:** Adicionar um grupo a uma unidade administrativa traz o próprio grupo para o escopo de gerenciamento de qualquer administrador de Usuário que também esteja escopo para essa unidade administrativa. Os administraçãos da unidade administrativa podem gerenciar o nome e a adesão do próprio grupo. Ele não concede ao Usuário admin para a unidade administrativa qualquer permissão para gerenciar os usuários do grupo (por exemplo, redefinir suas senhas). Para conceder ao Administrador usuário a capacidade de gerenciar os usuários, os usuários devem ser membros diretos da unidade administrativa.

**P: Um recurso (usuário ou grupo) pode ser membro de mais de uma unidade administrativa?**

**A:** Sim, um recurso pode ser membro de mais de uma unidade administrativa. O recurso pode ser gerenciado por todos os admins de abrangência de unidade administrativa e de toda a organização que possuem permissões sobre o recurso.

**P: As unidades administrativas estão disponíveis nas organizações B2C?**

**A:** Não, as unidades administrativas não estão disponíveis para organizações B2C.

**P: As unidades administrativas aninhadas são suportadas?**

**A:** Unidades administrativas aninhadas não são suportadas.

**P: As unidades administrativas são suportadas em PowerShell e API graph?**

**A:** Sim. O suporte para unidades administrativas existe na [documentação do cmdlet do PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) e [em scripts de amostra,](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)e o suporte está no Gráfico Microsoft para o [tipo de recurso administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Próximas etapas

- [Unidades administrativas para restringir o escopo para a visão geral das funções](directory-administrative-units.md)
- [Gerenciar unidades administrativas](roles-admin-units-manage.md)