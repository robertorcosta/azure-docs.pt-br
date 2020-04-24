---
title: Introdução à autenticação na Automação do Azure
description: Este artigo fornece uma visão geral da segurança de automação e dos diferentes métodos de autenticação disponíveis para Contas de automação na Automação do Azure.
keywords: segurança de automação, automação segura; autenticação de automação
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114523"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introdução à autenticação na Automação do Azure

A Automação do Azure permite automatizar tarefas em relação a recursos no Azure, locais e com outros provedores de nuvem, como AWS (Amazon Web Services). Para que um runbook execute as ações necessárias, ele deve ter permissões para acessar os recursos com segurança e apenas com os direitos mínimos necessários na assinatura.

Este artigo abordará os vários cenários de autenticação com suporte da automação do Azure e como começar com base no ambiente ou nos ambientes que você precisa gerenciar.  

## <a name="automation-account-overview"></a>Visão geral da Conta de Automação

Ao iniciar a Automação do Azure pela primeira vez, você deve criar pelo menos uma conta de Automação. As contas de Automação permitem isolar seus recursos de Automação (runbooks, ativos, configurações) dos recursos contidos em outras contas de Automação. Você pode usar contas de Automação para separar os recursos em ambientes lógicos separados. Por exemplo, você pode usar uma conta para desenvolvimento, outra para produção e outra para seu ambiente local. Uma conta de Automação do Azure é diferente de sua conta da Microsoft ou de contas criadas em sua assinatura do Azure.

Os recursos de Automação para cada conta de Automação estão associados a uma única região do Azure, mas as contas de Automação podem gerenciar todos os recursos em sua assinatura. O principal motivo para criar contas de Automação em regiões diferentes seria se você tiver políticas que exijam que dados e recursos sejam isolados em uma região específica.

Todas as tarefas que podem ser executadas em relação a recursos usando o Azure Resource Manager e os cmdlets do Azure na Automação do Azure devem ser autenticadas no Azure usando a autenticação baseada em credenciais de identidade organizacional do Azure Active Directory. As contas Executar como na automação do Azure fornecem autenticação para gerenciar recursos no Azure usando os cmdlets do Azure. Quando você cria uma conta Executar como, ela cria um novo usuário de entidade de serviço no Azure Active Directory (AD) e atribui a função colaborador a esse usuário no nível de assinatura. Para runbooks que usam Hybrid Runbook Workers em máquinas virtuais do Azure, você pode usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de contas Executar como para autenticar para recursos do Azure.

A entidade de serviço para uma conta Executar como não tem permissões para ler o Azure AD por padrão. Se desejar adicionar permissões para ler ou gerenciar o Azure AD, você precisará conceder as permissões na entidade de serviço sob permissões de **API**. Para saber mais, consulte [adicionar permissões para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

O controle de acesso baseado em função está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de usuário do Azure AD e uma conta Executar como e autenticar essa entidade de serviço. Leia o [artigo Controle de acesso baseado em função da Automação do Azure](automation-role-based-access-control.md) para obter mais informações que o ajudarão a desenvolver seu modelo para gerenciar permissões de Automação.  

Os runbooks em execução em um Hybrid Runbook Worker no data center ou em relação aos serviços de computação em outros ambientes de nuvem, como AWS, não podem usar o mesmo método que normalmente é usado para autenticação de runbooks nos recursos do Azure. Isso ocorre porque esses recursos estão em execução fora do Azure e, assim, exigirão suas próprias credenciais de segurança definidas na Automação para se autenticarem em recursos que acessarão localmente. Para obter mais informações sobre a autenticação de runbook com runbook Workers, consulte [autenticar runbooks para Hybrid runbook Workers](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Próximas etapas

* [Crie uma conta de automação do portal do Azure](automation-create-standalone-account.md).

* [Crie uma conta de automação usando Azure Resource Manager modelo](automation-create-account-template.md).

* [Autenticar com Amazon Web Services (AWS)](automation-config-aws-account.md).
