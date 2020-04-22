---
title: Unidades administrativas solução de problemas e FAQ - Diretório Ativo do Azure | Microsoft Docs
description: Investigue unidades administrativas para conceder permissões com escopo restrito no Azure Active Directory.
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
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684849"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Unidades administrativas azure AD: Solução de problemas e perguntas frequentes

Para obter mais controle administrativo granular no Azure Active Directory (Azure AD), você pode atribuir usuários a uma função AD do Azure com um escopo limitado a uma ou mais unidades administrativas (AUs). Para obter a amostra de scripts PowerShell para tarefas comuns, consulte [Trabalhe com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Por que não consigo criar uma unidade administrativa?**

**A:** Apenas um *administrador global* ou administrador *de funções privilegiadas* pode criar uma unidade administrativa no Azure AD. Verifique se o usuário que está tentando criar a unidade administrativa é designado para a função *administrador global* ou administrador *de funções privilegiadas.*

**P: Adicionei um grupo à unidade administrativa. Por que os membros do grupo ainda não estão aparecendo lá?**

**A:** Quando você adiciona um grupo à unidade administrativa, isso não resulta em todos os membros do grupo sendo adicionados a ele. Os usuários devem ser diretamente designados para a unidade administrativa.

**P: Acabei de adicionar (ou removi) um membro da unidade administrativa. Por que o membro não aparece (ou ainda aparece) na interface do usuário?**

**A:** Às vezes, o processamento do acréscimo ou remoção de um ou mais membros da unidade administrativa pode levar alguns minutos para ser refletido na página das **unidades administrativas.** Alternativamente, você pode ir diretamente às propriedades do recurso associado e ver se a ação foi concluída. Para obter mais informações sobre usuários e grupos em UA, consulte [Listar unidades administrativas para um usuário](roles-admin-units-add-manage-users.md) e Listar unidades [administrativas para um grupo](roles-admin-units-add-manage-groups.md).

**P: Eu sou um administrador de senha delegado em uma unidade administrativa. Por que não consigo redefinir a senha de um usuário específico?**

**A:** Como administrador de uma unidade administrativa, você pode redefinir senhas apenas para usuários que são atribuídos à sua unidade administrativa. Certifique-se de que o usuário cuja redefinição de senha está falhando pertence à unidade administrativa à qual você foi designado. Se o usuário pertence à mesma unidade administrativa, mas ainda não pode redefinir sua senha, verifique as funções atribuídas ao usuário. 

Para evitar uma elevação do privilégio, um administrador administrativo com escopo de unidade não pode redefinir a senha de um usuário que é atribuído a uma função com um escopo de toda a organização.

**P: Por que as unidades administrativas são necessárias? Não poderíamos ter usado grupos de segurança como forma de definir um escopo?**

**A:** Os grupos de segurança têm um modelo de finalidade e autorização existente. Um *administrador de usuário,* por exemplo, pode gerenciar a adesão de todos os grupos de segurança na organização Azure AD. A função pode usar grupos para gerenciar o acesso a aplicativos como o Salesforce. Um *administrador de usuário* não deve ser capaz de gerenciar o modelo de delegação em si, o que seria o resultado se grupos de segurança fossem estendidos para apoiar cenários de "agrupamento de recursos". As unidades administrativas, como unidades organizacionais no Windows Server Active Directory, destinam-se a fornecer uma maneira de escopo da administração de uma ampla gama de objetos de diretório. Os próprios grupos de segurança podem ser membros de escopos de recursos. Usar grupos de segurança para definir o conjunto de grupos de segurança que um administrador pode gerenciar pode se tornar confuso.

**P: O que significa adicionar um grupo a uma unidade administrativa?**

**A:** A adição de um grupo a uma unidade administrativa traz o próprio grupo para o escopo de gestão de qualquer *Administrador de Usuário* que também esteja escopo para aquela unidade administrativa. Os administradores de usuários da unidade administrativa podem gerenciar o nome e a adesão do próprio grupo. Ele não concede ao *Administrador do Usuário* as permissões da unidade administrativa para gerenciar os usuários do grupo (por exemplo, para redefinir suas senhas). Para conceder ao *Administrador usuário* a capacidade de gerenciar os usuários, os usuários devem ser membros diretos da unidade administrativa.

**P: Um recurso (usuário ou grupo) pode ser membro de mais de uma unidade administrativa?**

**A:** Sim, um recurso pode ser membro de mais de uma unidade administrativa. O recurso pode ser gerenciado por todos os administradores de toda a organização e com escopo de unidade administrativa que tenham permissões sobre o recurso.

**P: As unidades administrativas estão disponíveis nas organizações B2C?**

**A:** Não, as unidades administrativas não estão disponíveis para organizações B2C.

**P: As unidades administrativas aninhadas são suportadas?**

**A:** Não, unidades administrativas aninhadas não são suportadas.

**P: As unidades administrativas são suportadas no PowerShell e na API do Gráfico?**

**A:** Sim. Você encontrará suporte para unidades administrativas na [documentação cmdlet do PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) e [scripts de amostra](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview). 

Encontre suporte para o [tipo de recurso administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) no Microsoft Graph.

## <a name="next-steps"></a>Próximas etapas

- [Restringir o escopo para funções usando unidades administrativas](directory-administrative-units.md)
- [Gerenciar unidades administrativas](roles-admin-units-manage.md)
