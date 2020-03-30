---
title: Autorizar o acesso a bolhas e filas usando o Active Directory
titleSuffix: Azure Storage
description: Autorize o acesso a blobs e filas do Azure usando o Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255360"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorize o acesso a bolhas e filas usando o Azure Active Directory

O Azure Storage suporta o uso do Azure Active Directory (Azure AD) para autorizar solicitações de armazenamento Blob e Fila. Com o Azure AD, você pode usar o RBAC (Role-Based Access Control, controle de acesso baseado em função) para conceder permissões a um diretor de segurança, que pode ser um usuário, grupo ou diretor de serviço de aplicativo. O principal de segurança é autenticado pelo Azure AD para retornar um token OAuth 2.0. O token pode então ser usado para autorizar uma solicitação contra o armazenamento Blob ou Fila.

Autorizar solicitações contra o Azure Storage com o Azure AD fornece segurança superior e facilidade de uso sobre a autorização de chave compartilhada. A Microsoft recomenda o uso da autorização do Azure AD com seus aplicativos blob e fila, quando possível, para minimizar possíveis vulnerabilidades de segurança inerentes à Chave Compartilhada.

A autorização com o Azure AD está disponível para todas as contas de armazenamento de uso geral e Blob em todas as regiões públicas e nuvens nacionais. Somente as contas de armazenamento criadas com o modelo de implantação do Azure Resource Manager suportam a autorização do Azure AD.

Além disso, o armazenamento Blob suporta a criação de assinaturas de acesso compartilhado (SAS) assinadas com credenciais AD do Azure. Para obter mais informações, consulte [Grant acesso limitado a dados com assinaturas de acesso compartilhada.](storage-sas-overview.md)

O Azure Files suporta autorização com AD (preview) ou Azure AD DS (GA) sobre SMB apenas para VMs com domínio. Para saber mais sobre o uso de AD (preview) ou Azure AD DS (GA) sobre SMB para Arquivos Azure, consulte [Visão geral do suporte de autenticação baseado em identidade do Azure Files para acesso a SMB](../files/storage-files-active-directory-overview.md).

A autorização com o Azure AD não é suportada para armazenamento de tabela do Azure. Use a chave compartilhada para autorizar solicitações de armazenamento de tabelas.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral do Azure AD para blobs e filas

Quando um principal de segurança (um usuário, grupo ou aplicativo) tenta acessar um recurso de blob ou fila, a solicitação deve ser autorizada, a menos que seja uma bolha disponível para acesso anônimo. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. Primeiro, a identidade do diretor de segurança é autenticada e um token OAuth 2.0 é devolvido. Em seguida, o token é passado como parte de uma solicitação ao serviço Blob ou Queue e usado pelo serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um aplicativo solicite um token de acesso OAuth 2.0 em tempo de execução. Se um aplicativo estiver sendo executado dentro de uma entidade Azure, como uma VM do Azure, um conjunto de escalade máquina virtual ou um aplicativo Funções do Azure, ele poderá usar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para acessar blobs ou filas. Para saber como autorizar solicitações feitas por uma identidade gerenciada para o serviço Azure Blob ou Fila, consulte [Autorizar o acesso a bolhas e filas com o Azure Active Directory e gerenciar identidades para recursos do Azure](storage-auth-aad-msi.md).

A etapa de autorização exige que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Storage fornece funções RBAC que englobam conjuntos comuns de permissões para dados de blob e fila. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre como atribuir funções RBAC para o Azure Storage, consulte [Gerenciar direitos de acesso a dados de armazenamento com RBAC](storage-auth-aad-rbac.md).

Aplicativos nativos e aplicativos web que fazem solicitações para o serviço Azure Blob ou Queue também podem autorizar o acesso com o Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar solicitações de dados de blob ou fila, consulte [Autorizar o acesso ao Azure Storage com o Azure AD a partir de um aplicativo de armazenamento Azure](storage-auth-aad-app.md).

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções RBAC para direitos de acesso

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões usadas para acessar dados de blob e fila. Você também pode definir funções personalizadas para acesso a dados de blob e fila.

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Um diretor de segurança do Azure AD pode ser um usuário, um grupo, um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Funções de RBAC incorporadas para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função RBAC incorporada a um diretor de segurança, consulte um dos seguintes artigos:

- [Conceder acesso a dados de blob e fila do Azure com RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com RBAC usando PowerShell](storage-auth-aad-rbac-powershell.md)

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar funções personalizadas para o Controle de Acesso baseado em funções do Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter detalhes sobre as permissões necessárias para ligar para operações específicas de serviço Blob ou Queue, consulte [Permissões para chamar as operações de dados blob e fila](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Escopo de recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Acessar dados com uma conta AD do Azure

O acesso a dados de blob ou fila através do portal Azure, PowerShell ou Azure CLI pode ser autorizado usando a conta Azure AD do usuário ou usando as chaves de acesso da conta (autorização de chave compartilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso aos dados do portal Azure

O portal Azure pode usar sua conta Azure AD ou as chaves de acesso da conta para acessar dados de blob e fila em uma conta de armazenamento do Azure. Qual esquema de autorização o portal Azure usa depende das funções RBAC que são atribuídas a você.

Quando você tenta acessar dados de blob ou fila, o portal Azure primeiro verifica se você recebeu uma função RBAC com **microsoft.Storage/storageAccounts/listkeys/action**. Se você tiver sido designado para uma função com essa ação, então o portal Azure usa a chave da conta para acessar dados de blob e fila via autorização de chave compartilhada. Se você não tiver sido designado para um papel com essa ação, então o portal Azure tentará acessar dados usando sua conta Azure AD.

Para acessar dados de blob ou fila do portal Azure usando sua conta Azure AD, você precisa de permissões para acessar dados de blob e fila, e também precisa de permissões para navegar pelos recursos da conta de armazenamento no portal Dozure. As funções incorporadas fornecidas pelo Azure Storage concedem acesso a recursos de blob e fila, mas não concedem permissões aos recursos da conta de armazenamento. Por essa razão, o acesso ao portal também requer a atribuição de uma função de Gerenciador de Recursos do Azure, como a função [Reader,](../../role-based-access-control/built-in-roles.md#reader) escopo para o nível da conta de armazenamento ou superior. A função **Reader** concede as permissões mais restritas, mas outra função de Gerenciador de Recursos do Azure que concede acesso aos recursos de gerenciamento de contas de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões aos usuários para acesso a dados no portal Azure com uma conta AD do Azure, consulte [o acesso de Grant ao Blob do Azure e os dados de fila com o RBAC no portal do Azure.](storage-auth-aad-rbac-portal.md)

O portal Azure indica qual esquema de autorização está em uso quando você navega para um contêiner ou fila. Para obter mais informações sobre o acesso aos dados no portal, consulte [Use o portal Azure para acessar blob ou dados de fila](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso a dados da PowerShell ou a Azure CLI

Aclie CLI e PowerShell suportam a assinatura com credenciais Azure AD. Depois de fazer login, sua sessão será executada essas credenciais. Para saber mais, consulte [os comandos Run Azure CLI ou PowerShell com credenciais Azure AD para acessar dados de blob ou fila](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- [Autorize o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
- [Autorize com o Azure Active Directory a partir de um aplicativo para acesso a bolhas e filas](storage-auth-aad-app.md)
- [Suporte ao Azure Storage para controle de acesso baseado no Azure Active Directory geralmente disponível](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
