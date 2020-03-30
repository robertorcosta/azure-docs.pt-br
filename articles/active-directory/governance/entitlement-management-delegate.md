---
title: Delegação e funções na gestão de direitos - Azure AD
description: Aprenda a delegar a governança de acesso dos administradores de TI aos gerentes de departamento e gerentes de projeto para que eles possam gerenciar o próprio acesso.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261834"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegação e funções na gestão de direitos azure AD

Por padrão, os administradores globais e os administradores de usuários podem criar e gerenciar todos os aspectos do gerenciamento de direitos AD do Azure. No entanto, os usuários nessas funções podem não conhecer todas as situações em que os pacotes de acesso são necessários. Normalmente são usuários dentro dos respectivos departamentos, equipes ou projetos que sabem com quem estão colaborando, usando quais recursos e por quanto tempo. Em vez de conceder permissões irrestritas aos não-administradores, você pode conceder aos usuários as menores permissões necessárias para realizar seu trabalho e evitar criar direitos de acesso conflitantes ou inadequados.

Este vídeo fornece uma visão geral de como delegar o controle de acesso do administrador de TI para usuários que não são administradores.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exemplo de delegado

Para entender como você pode delegar o acesso à governança na gestão de direitos, ajuda a considerar um exemplo. Suponha que sua organização tenha o seguinte administrador e gerentes.

![Delegado do administrador de TI para os gerentes](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Como administradora de TI, Hana tem contatos em cada departamento -- Mamta em Marketing, Mark in Finance e Joe in Legal, que são responsáveis pelos recursos de seu departamento e conteúdo crítico de negócios.

Com a gestão de direitos, você pode delegar o acesso de governança a esses não-administradores porque eles são os únicos que sabem quais usuários precisam de acesso, por quanto tempo e a quais recursos. Isso garante que as pessoas certas estejam gerenciando o acesso para seus departamentos.

Aqui está uma maneira que Hana poderia delegar o acesso à governança para os departamentos de marketing, finanças e jurídico.

1. Hana cria um novo grupo de segurança Azure AD, e adiciona Mamta, Mark e Joe como membros do grupo.

1. Hana adiciona esse grupo ao papel de criadores de catálogos.

    Mamta, Mark e Joe agora podem criar catálogos para seus departamentos, adicionar recursos que seus departamentos precisam, e fazer mais delegação dentro do catálogo.

    Note que Mamta, Mark e Joe não podem ver os catálogos um do outro.

1. A Mamta cria um catálogo **de Marketing,** que é um contêiner de recursos.

1. Mamta adiciona os recursos que seu departamento de marketing possui para este catálogo.

1. Mamta pode adicionar mais pessoas de seu departamento como proprietários de catálogo para este catálogo. Isso ajuda a compartilhar as responsabilidades de gerenciamento do catálogo.

1. A Mamta ainda pode delegar a criação e gestão de pacotes de acesso no catálogo de Marketing aos gerentes de projetos do departamento de Marketing. Ela pode fazer isso atribuindo-os à função de gerenciador de pacotes de acesso. Um gerenciador de pacotes de acesso pode criar e gerenciar pacotes de acesso. 

O diagrama a seguir mostra catálogos com recursos para os departamentos de marketing, finanças e jurídico. Usando esses catálogos, os gerentes de projeto podem criar pacotes de acesso para suas equipes ou projetos.

![Exemplo de delegado de gestão de direitos](./media/entitlement-management-delegate/elm-delegate.png)

Após a delegação, o departamento de marketing pode ter funções semelhantes à tabela a seguir.

| Usuário | Função de trabalho | Função de AD do Azure | Função de gestão de direitos |
| --- | --- | --- | --- |
| Hana | Administrador de TI | Administrador global ou administrador de usuário |  |
| Mamta | Gerente de marketing | Usuário | Criador de catálogos e proprietário de catálogos |
| Roberto | Líder de marketing | Usuário | Proprietário do catálogo |
| Jessica | Gerente de projetos de marketing | Usuário | Gerenciador de pacotes de acesso |

## <a name="entitlement-management-roles"></a>Funções de gestão de direitos

A gestão de direitos tem as seguintes funções específicas para a gestão de direitos.

| Função de gestão de direitos | Descrição |
| --- | --- |
| Criador de catálogo | Criar e gerenciar catálogos. Normalmente, um administrador de TI que não é um administrador Global ou um proprietário de recursos para uma coleção de recursos. A pessoa que cria um catálogo automaticamente se torna a primeira proprietária do catálogo e pode adicionar proprietários adicionais de catálogo. Um criador de catálogonão pode gerenciar ou ver catálogos que eles não possuem e não podem adicionar recursos que não possuem a um catálogo. Se o criador do catálogo precisar gerenciar outro catálogo ou adicionar recursos que não possui, ele pode solicitar ser co-proprietário desse catálogo ou recurso. |
| Proprietário do catálogo | Editar e gerenciar catálogos existentes. Normalmente, um administrador de TI ou proprietários de recursos, ou um usuário que o proprietário do catálogo designou. |
| Gerenciador de pacotes de acesso | Editar e gerenciar todos os pacotes de acesso existentes dentro de um catálogo. |

Além disso, um aprovador designado e um solicitante de um pacote de acesso também têm direitos, embora não sejam funções.

| Right | Descrição |
| --- | --- |
| Aprovador | Autorizado por uma política de aprovar ou negar solicitações de acesso a pacotes, embora eles não possam alterar as definições do pacote de acesso. |
| Solicitante | Autorizado por uma política de um pacote de acesso para solicitar esse pacote de acesso. |

A tabela a seguir lista as tarefas que as funções de gerenciamento de direitos podem executar.

| Tarefa | Admin | Criador de catálogo | Proprietário do catálogo | Gerenciador de pacotes de acesso |
| --- | :---: | :---: | :---: | :---: |
| [Delegue a um criador de catálogo](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Adicionar uma organização conectada](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Adicionar um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Adicionar um proprietário de catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Excluir um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegar a um gerente de pacote de acesso](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Remova um gerenciador de pacotes de acesso](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Crie um novo pacote de acesso em um catálogo](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Alterar funções de recurso em um pacote de acesso](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Criar e editar políticas](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Atribuir diretamente um usuário a um pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Veja quem tem uma atribuição a um pacote de acesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Exibir as solicitações de um pacote de acesso](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Exibir os erros de entrega de uma solicitação](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Reprocessar uma solicitação](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Cancelar uma solicitação pendente](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ocultar um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Excluir um pacote de acesso](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um administrador global pode adicionar ou remover qualquer grupo (grupos de segurança criados na nuvem ou grupos do Office 365 criados na nuvem), aplicativo ou site SharePoint Online em um catálogo. Um administrador de usuário pode adicionar ou remover qualquer grupo ou aplicativo em um catálogo.

Para um usuário que não é um administrador Global ou um administrador de usuário, para adicionar grupos, aplicativos ou sites SharePoint Online a um catálogo, esse usuário deve ter *tanto* a função de diretório Ad do Azure quanto a função de gerenciamento de direitos do proprietário do catálogo. A tabela a seguir lista as combinações de papéis necessárias para adicionar recursos a um catálogo. Para remover recursos de um catálogo, você deve ter as mesmas funções.

| Função do diretório do Azure Active Directory | Função de gestão de direitos | Pode adicionar grupo de segurança | Pode adicionar grupo Office 365 | Pode adicionar aplicativo | Pode adicionar o site SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrador global](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrador de usuários](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrador intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrador do Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador de serviços de equipes](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador do SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrador de aplicativos](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| [Administrador de aplicativos em nuvem](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| Usuário | Proprietário do catálogo | Só se o dono do grupo | Só se o dono do grupo | Somente se o proprietário do aplicativo |  |

Para determinar a função menos privilegiada para uma tarefa, você também pode referenciar [funções de administrador por tarefa de administração no Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Próximas etapas

- [Delegar governança de acesso aos criadores de catálogos](entitlement-management-delegate-catalog.md)
- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
