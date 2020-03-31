---
title: Gerentes de administração de aplicativos de delegados - Azure AD | Microsoft Docs
description: Conceder permissões para gerenciamento de acesso a aplicativos no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253033"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegue permissões de registro de aplicativos no Azure Active Directory

Este artigo descreve como usar permissões concedidas por funções personalizadas no Azure Active Directory (Azure AD) para atender às necessidades de gerenciamento de aplicativos. No Azure AD, você pode delegar permissões de criação e gerenciamento de aplicativos das seguintes maneiras:

- [Restringir quem pode criar aplicativos](#restrict-who-can-create-applications) e gerenciar os aplicativos que eles criam. Por padrão no Azure AD, todos os usuários podem registrar registros de aplicativos e gerenciar todos os aspectos dos aplicativos que criam. Isso pode ser restrito apenas a permitir que pessoas selecionadas essa permissão.
- [Atribuindo um ou mais proprietários a um aplicativo](#assign-application-owners). Esta é uma maneira simples de conceder a alguém a capacidade de gerenciar todos os aspectos da configuração do Azure AD para um aplicativo específico.
- [Atribuindo uma função administrativa incorporada](#assign-built-in-application-admin-roles) que concede acesso para gerenciar a configuração no Azure AD para todos os aplicativos. Esta é a maneira recomendada de conceder aos especialistas em TI acesso para gerenciar permissões amplas de configuração de aplicativos sem conceder acesso para gerenciar outras partes do Azure AD não relacionadas à configuração do aplicativo.
- [Criando uma função personalizada](#create-and-assign-a-custom-role-preview) definindo permissões muito específicas e atribuindo-as a alguém, seja para o escopo de um único aplicativo como um proprietário limitado, ou no escopo do diretório (todos os aplicativos) como um administrador limitado.

É importante considerar a concessão de acesso usando um dos métodos acima por duas razões. Primeiro, delegar a capacidade de executar tarefas administrativas reduz a sobrecarga do administrador global. Em segundo lugar, o uso de permissões limitadas melhora sua postura de segurança e reduz o potencial de acesso não autorizado. Problemas de delegação e diretrizes gerais são discutidos em [Delegar a administração no Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Restringir quem pode criar aplicativos

Por padrão no Azure AD, todos os usuários podem registrar registros de aplicativos e gerenciar todos os aspectos dos aplicativos que criam. Todos também têm a capacidade de consentir com aplicativos que acessam dados da empresa em seu nome. Você pode optar por conceder essas permissões seletivamente definindo os switches globais para 'Não' e adicionando os usuários selecionados à função Desenvolvedor de aplicativos.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Para desativar a capacidade padrão de criar registros de aplicativos ou consentimento para aplicativos

1. Faça login na sua organização Azure AD com uma conta que elegível para o papel de administrador Global em sua organização Azure AD.
1. Definir um ou ambos os seguintes:

    - Na [página configurações](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)do Usuário para sua organização, definido como **os Usuários podem registrar a** configuração de aplicativos como No. Isso desativará a capacidade padrão dos usuários de criar registros de aplicativos.
    - Nas [configurações do usuário para aplicativos corporativos,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)definido saquear os Usuários podem **consentir com os aplicativos que acessam os dados da empresa em sua** configuração de nome para No. Isso desativará a capacidade padrão de os usuários consentirem com os aplicativos que acessam os dados da empresa em seu nome.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Conceda permissões individuais para criar e consentir com aplicativos quando a capacidade padrão estiver desativada

Atribuir a função de desenvolvedor de aplicativos para conceder a capacidade de criar registros de aplicativos quando os **Usuários podem registrar** a configuração de aplicativos é definida como No. Essa função também concede permissão para consentimento em seu próprio nome quando os **Usuários podem consentir com aplicativos que acessam dados da empresa em sua** configuração de nome é definido como No. Como um comportamento do sistema, quando um usuário cria um novo registro de aplicativo, ele é automaticamente adicionado como o primeiro proprietário. As permissões de propriedade dão ao usuário a capacidade de gerenciar todos os aspectos de um registro de aplicativo ou aplicativo corporativo que ele possui.

## <a name="assign-application-owners"></a>Atribuir proprietários de aplicativos

Atribuir proprietários é uma maneira simples de conceder a capacidade de gerenciar todos os aspectos da configuração do Azure AD para um registro específico de aplicativos ou aplicativo corporativo. Como um comportamento do sistema, quando um usuário cria um novo registro de aplicativo, ele é automaticamente adicionado como o primeiro proprietário. As permissões de propriedade dão ao usuário a capacidade de gerenciar todos os aspectos de um registro de aplicativo ou aplicativo corporativo que ele possui. O proprietário original pode ser removido e proprietários adicionais podem ser adicionados.

### <a name="enterprise-application-owners"></a>Proprietários de aplicativos corporativos

Como proprietário, um usuário pode gerenciar a configuração específica da organização do aplicativo corporativo, como a configuração de login único, provisionamento e atribuições do usuário. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários podem gerenciar apenas os aplicativos corporativos que possuem.

Em alguns casos, os aplicativos corporativos criados a partir da galeria de aplicativos incluem tanto um aplicativo corporativo quanto um registro de aplicativos. Quando isso é verdade, adicionar um proprietário ao aplicativo corporativo adiciona automaticamente o proprietário ao registro de solicitação correspondente como proprietário.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Para atribuir um proprietário a um aplicativo corporativo

1. Faça login na [organização Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que elegível para o administrador de aplicativos de aplicativos ou administrador de aplicativos na nuvem para a organização.
1. Na [página](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) de registros do App para a organização, selecione um aplicativo para abrir a página Visão Geral para o aplicativo.
1. Selecione **Proprietários** para ver a lista de proprietários do aplicativo.
1. Selecione **Adicionar** para selecionar um ou mais proprietários para adicionar ao aplicativo.

> [!IMPORTANT]
> Usuários e diretores de serviços podem ser proprietários de registros de aplicativos. Apenas os usuários podem ser proprietários de aplicativos corporativos. Os grupos não podem ser designados como proprietários de nenhum dos dois.
>
> Os proprietários podem adicionar credenciais a um aplicativo e usar essas credenciais para se passar pela identidade do aplicativo. O aplicativo pode ter mais permissões do que o proprietário, e, portanto, seria uma elevação de privilégio sobre o que o proprietário tem acesso como usuário ou diretor de serviço. Um proprietário de aplicativo poderia potencialmente criar ou atualizar usuários ou outros objetos enquanto se passava pelo aplicativo, dependendo das permissões do aplicativo.

## <a name="assign-built-in-application-admin-roles"></a>Atribuir funções de admin de aplicativos incorporadas

O Azure AD tem um conjunto de funções de administrador incorporadas para conceder acesso à configuração de gerenciamento no Azure AD para todos os aplicativos. Essas funções são a maneira recomendada de conceder aos especialistas em TI acesso para gerenciar permissões amplas de configuração de aplicativos sem conceder acesso para gerenciar outras partes do Azure AD não relacionadas à configuração do aplicativo.

- Administrador de Aplicativos: usuários nesta função podem criar e gerenciar todos os aspectos de aplicativos corporativos, registros de aplicativo e as configurações de proxy de aplicativo. Essa função também concede a capacidade de consentir com permissões delegadas e permissões de aplicativos, excluindo o Microsoft Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativos ou aplicativos corporativos.
- Administrador de Aplicativos de Nuvem: os usuários nesta função têm as mesmas permissões que a função de administrador do aplicativo, exceto a capacidade de gerenciar o proxy de aplicativo. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativos ou aplicativos corporativos.

Para obter mais informações e visualizar a descrição dessas funções, consulte [Funções Disponíveis](directory-assign-admin-roles.md#available-roles).

Siga as instruções nas [funções Atribuir aos usuários com o](../fundamentals/active-directory-users-assign-role-azure-portal.md) guia De como atribuir o administrador de aplicativos do aplicativo ou do administrador de aplicativos na nuvem.

> [!IMPORTANT]
> Administradores de aplicativos e administradores de aplicativos em nuvem podem adicionar credenciais a um aplicativo e usar essas credenciais para se passar pela identidade do aplicativo. O aplicativo pode ter permissões que são uma elevação do privilégio sobre as permissões da função de admin. Um admin nesta função poderia potencialmente criar ou atualizar usuários ou outros objetos enquanto se passava pelo aplicativo, dependendo das permissões do aplicativo.
> Nenhuma dessas funções fornece a capacidade de gerenciar configurações de Acesso Condicional.

## <a name="create-and-assign-a-custom-role-preview"></a>Criar e atribuir uma função personalizada (visualização)

Criar funções personalizadas e atribuir funções personalizadas são etapas separadas:

- [Crie uma definição de *função* personalizada](roles-create-custom.md) e adicione [permissões a ela a partir de uma lista predefinida](roles-custom-available-permissions.md). Estas são as mesmas permissões usadas nas funções incorporadas.
- [Crie uma *atribuição de função* ](roles-assign-powershell.md) para atribuir a função personalizada.

Essa separação permite que você crie uma definição de função única e, em seguida, atribua-a muitas vezes em *diferentes escopos*. Uma função personalizada pode ser atribuída no escopo de toda a organização, ou pode ser atribuída no escopo se um único objeto Azure AD. Um exemplo de escopo de objeto é um único registro de aplicativo. Usando diferentes escopos, a mesma definição de função pode ser atribuída a Sally em todos os registros de aplicativos na organização e, em seguida, à Naveen apenas no registro do aplicativo Contoso Expense Reports.

Dicas ao criar e usar funções personalizadas para delegar o gerenciamento de aplicativos:
- Funções personalizadas só concedem acesso nas lâminas de registro de aplicativos mais atuais do portal Azure AD. Eles não concedem acesso nas lâminas de registros de aplicativos legados.
- As funções personalizadas não concedem acesso ao portal Azure AD quando a configuração de usuário "Restringir o acesso ao portal de administração Azure AD" é definida como Sim.
- Registros de aplicativos que o usuário tem acesso a usar atribuições de função só aparecem na guia 'Todos os aplicativos' na página de registro do App. Eles não aparecem na guia 'Aplicativos próprios'.

Para obter mais informações sobre os fundamentos das funções personalizadas, consulte a [visão geral das funções personalizadas,](roles-custom-overview.md)bem como como [criar uma função personalizada](roles-create-custom.md) e como atribuir [uma função.](roles-assign-powershell.md)

## <a name="next-steps"></a>Próximas etapas

- [Subtipos e permissões de registro de aplicativos](roles-custom-available-permissions.md)
- [Referência da função de administrador do Azure AD](directory-assign-admin-roles.md)
