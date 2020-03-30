---
title: Cenários comuns na gestão de direitos - Azure AD
description: Aprenda os passos de alto nível que você deve seguir para cenários comuns no gerenciamento de direitos do Azure Active Directory.
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
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190544"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Cenários comuns no gerenciamento de direitos azure AD

Existem várias maneiras que você pode configurar o gerenciamento de direitos para sua organização. No entanto, se você está apenas começando, é útil entender os cenários comuns para administradores, proprietários de catálogos, gerentes de pacotes de acesso, aprovadores e solicitantes.

## <a name="delegate"></a>delegado

### <a name="administrator-delegate-management-of-resources"></a>Administrador: Delegar gestão de recursos

1. [Assista ao vídeo: Delegação de TI para gerente de departamento](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegue usuários para catalogar a função de criador](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Criador de catálogo: Delegar gestão de recursos

- [Criar um novo catálogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietário do catálogo: Delegar gestão de recursos

1. [Adicionar co-proprietários ao catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Adicionar recursos ao catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietário do catálogo: Delegar gerenciamento de pacotes de acesso

1. [Assista ao vídeo: Delegação do proprietário do catálogo para acessar o gerenciador de pacotes](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegue os usuários para acessar a função de gerenciador de pacotes](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Governe o acesso para usuários em sua organização

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Gerenciador de pacotes de acesso: Permita que os funcionários da sua organização solicitem acesso aos recursos

1. [Criar um pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipes, aplicativos ou sites SharePoint para acessar o pacote](entitlement-management-access-package-create.md#resource-roles)
1. [Adicione uma política de solicitação para permitir que os usuários em seu diretório solicitem acesso](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Especificar configurações de expiração](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Solicitante: Solicite acesso aos recursos

1. [Faça login no portal Meu Acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Encontre o pacote de acesso
1. [Solicitar acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovador: Aprovar solicitações de recursos

1. [Pedido aberto no portal Meu Acesso](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar solicitação de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Solicitante: Veja os recursos aos os que você já tem acesso

1. [Faça login no portal Meu Acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Exibir pacotes de acesso ativo

## <a name="govern-access-for-users-outside-your-organization"></a>Governe o acesso para usuários fora da sua organização

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrador: Colabore com uma organização de parceiros externos

1. [Leia como funciona o acesso para usuários externos](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Definir configurações para usuários externos](entitlement-management-external-users.md#settings-for-external-users)
1. [Adicione uma conexão à organização externa](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Gerenciador de pacotes de acesso: Colabore com uma organização de parceiros externos

1. [Criar um pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipes, aplicativos ou sites SharePoint para acessar o pacote](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Adicione uma política de solicitação para permitir que usuários que não estão em seu diretório solicitem acesso](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Especificar configurações de expiração](entitlement-management-access-package-create.md#lifecycle)
1. [Copie o link para solicitar o pacote de acesso](entitlement-management-access-package-settings.md)
1. Envie o link para seu parceiro externo para compartilhar com seus usuários

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Solicitante: Solicite acesso aos recursos como usuário externo

1. Encontre o link do pacote de acesso que você recebeu do seu contato
1. [Faça login no portal Meu Acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Solicitar acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovador: Aprovar solicitações de recursos

1. [Pedido aberto no portal Meu Acesso](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar solicitação de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Solicitante: Veja os recursos aos os seus já acessos

1. [Faça login no portal Meu Acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Exibir pacotes de acesso ativo

## <a name="day-to-day-management"></a>Gestão diária

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Gerenciador de pacotes de acesso: atualize os recursos para um projeto

1. [Assista ao vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abra o pacote de acesso
1. [Adicionar ou remover grupos, equipes, aplicativos ou sites do SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Gerenciador de pacotes de acesso: Atualize a duração de um projeto

1. [Assista ao vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abra o pacote de acesso
1. [Abra as configurações do ciclo de vida](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Atualize as configurações de expiração](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gerenciador de pacotes de acesso: atualize como o acesso é aprovado para um projeto

1. [Assista ao vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Abra uma política existente de configurações de solicitação e aprovação](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Atualize as configurações de aprovação](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Gerenciador de pacotes de acesso: atualize as pessoas para um projeto

1. [Assista ao vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Remova usuários que não precisam mais de acesso](entitlement-management-access-package-assignments.md)
1. [Abra uma política existente de configurações de solicitação e aprovação](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Adicionar usuários que precisam de acesso](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Gerenciador de pacotes de acesso: atribua diretamente usuários específicos a um pacote de acesso

1. [Se os usuários precisarem de diferentes configurações do ciclo de vida, adicione uma nova diretiva ao pacote de acesso](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Atribua diretamente usuários específicos ao pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Atribuições e relatórios

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrador: Veja quem tem atribuições em um pacote de acesso

1. Abra um pacote de acesso
1. [Exibir atribuições](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Arquivar relatórios e registros](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrador: Exibir recursos atribuídos aos usuários

1. [Exibir pacotes de acesso para um usuário](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Exibir atribuições de recursos para um usuário](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administração programática

Você também pode gerenciar pacotes de acesso, catálogos, políticas, solicitações e atribuições usando o Microsoft Graph.  Um usuário em uma função apropriada com `EntitlementManagement.ReadWrite.All` um aplicativo que tenha a permissão delegada pode chamar a [API de gerenciamento de direitos](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

## <a name="next-steps"></a>Próximas etapas

- [Delegação e funções](entitlement-management-delegate.md)
- [Solicitação de processos e notificações por e-mail](entitlement-management-process.md)
