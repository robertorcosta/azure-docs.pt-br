---
title: Permissões de função de administrador personalizadas disponíveis - Azure AD | Microsoft Docs
description: Permissões de função de administrador personalizado para delegar o gerenciamento de identidade.
services: active-directory
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
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025159"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Subtipos e permissões de registro de aplicativos no Azure Active Directory

Este artigo contém as permissões de registro de aplicativos disponíveis atualmente para definições de função personalizadas no Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Permissões para gerenciar aplicativos de diretório único

Ao escolher as permissões para sua função personalizada, você tem a opção de conceder acesso para gerenciar apenas aplicativos de diretório único. Os aplicativos de diretório único estão disponíveis apenas para usuários na organização Azure AD onde o aplicativo está registrado. Os aplicativos de diretório único são definidos como tendo **os tipos de conta suportados definidos** como "Contas apenas neste diretório organizacional". Na API do Gráfico, os aplicativos de diretório único têm a propriedade signInAudience definida como "AzureADMyOrg".

Para conceder acesso para gerenciar apenas aplicativos de diretório único, use as permissões abaixo com os aplicativos de **subtipo.myOrganization**. Por exemplo, microsoft.directory/applications.myOrganization/basic/update.

Consulte a [visão geral das funções personalizadas](roles-custom-overview.md) para obter uma explicação do que significa o subtipo, permissão e conjunto de propriedades dos termos gerais. As informações a seguir são específicas para os registros de inscrição.

### <a name="create-and-delete"></a>Criar e excluir

Existem duas permissões disponíveis para a concessão da capacidade de criar registros de aplicativos, cada uma com comportamento diferente:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Atribuir essa permissão resulta na adição do criador como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado contará com a cota de 250 objetos criados pelo criador.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Atribuir essa permissão resulta em que o criador não será adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado não contará com a cota de 250 objetos criados pelo criador. Use essa permissão com cuidado, pois não há nada que impeça o cessionário de criar registros de aplicativos até que a cota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, essa permissão prevalecerá.

Se ambas as permissões forem atribuídas, a permissão /create terá precedência. Embora a permissão /createAsOwner não adicione automaticamente o criador como o primeiro proprietário, os proprietários podem ser especificados durante a criação do registro do aplicativo ao usar APIs do Gráfico ou cmdlets PowerShell.

Crie permissões para conceder acesso ao **comando Novo registro.**

[Essas permissões concedem acesso ao comando do portal Novo Registro](./media/roles-create-custom/new-custom-role.png)

Existem duas permissões disponíveis para a concessão da capacidade de excluir registros de aplicativos:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Concede a capacidade de excluir registros de aplicativos independentemente do subtipo; ou seja, aplicações de inquilino único e multi-inquilino.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Concede a capacidade de excluir registros de aplicativos restritos àqueles que são acessíveis apenas a contas em sua organização ou aplicativos de inquilino único (subtipo myOrganization).

![Essas permissões concedem acesso ao comando Delete app registration](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Ao atribuir uma função que contenha permissões de criação, a atribuição da função deve ser feita no escopo do diretório. Uma permissão de criação atribuída em um escopo de recurso não concede a capacidade de criar registros de aplicativos.

### <a name="read"></a>Ler

Todos os usuários membros da organização podem ler informações de registro de aplicativos por padrão. No entanto, usuários convidados e diretores de serviços de aplicativos não podem. Se você planeja atribuir uma função a um usuário convidado ou aplicativo, você deve incluir as permissões de leitura apropriadas.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Capacidade de ler todas as propriedades de aplicativos de inquilino único e multi-inquilino fora de propriedades que não podem ser lidas em qualquer situação como credenciais.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Concede as mesmas permissões que microsoft.directory/applications/allProperties/read, mas apenas para aplicativos de inquilino único.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Concede a capacidade de ler propriedades dos proprietários em aplicações de inquilino único e multilocatários. Concede acesso a todos os campos na página de proprietários de registro de aplicativos:

![Essas permissões concedem acesso à página dos proprietários de registro de aplicativos](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Concede acesso à leitura de propriedades padrão de registro de aplicativos. Isso inclui propriedades em páginas de registro de aplicativos.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Concede as mesmas permissões que microsoft.directory/applications/standard/read, mas apenas para aplicativos de um único inquilino.

### <a name="update"></a>Atualizar

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Capacidade de atualizar todas as propriedades em aplicativos de diretório único e de vários diretórios.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Concede as mesmas permissões que microsoft.directory/applications/allProperties/update, mas apenas para aplicativos de inquilino único.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Capacidade de atualizar a propriedade de tipo de conta suportada (signInAudience) em aplicativos de diretório único e multidiretório.

![Esta permissão concede acesso à propriedade do tipo de conta suportada pelo registro do aplicativo na página de autenticação](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Concede as mesmas permissões que microsoft.directory/applications/audience/update, mas apenas para aplicativos de inquilino único.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Capacidade de atualizar a URL de resposta, url de saída, fluxo implícito e propriedades de domínio do editor em aplicativos de inquilino único e multilocatários. Concede acesso a todos os campos na página de autenticação do registro do aplicativo, exceto os tipos de conta suportados:

![Concede acesso à autenticação de registro de aplicativos, mas não suportados tipos de conta](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Concede as mesmas permissões que microsoft.directory/applications/authentication/update, mas apenas para aplicativos de inquilino único.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Capacidade de atualizar o nome, logotipo, URL da página inicial, termos de URL de serviço e propriedades de URL de declaração de privacidade em aplicativos de inquilino único e multilocatários. Concede acesso a todos os campos na página de marca de registro de inscrição:

![Esta permissão concede acesso à página de marca de registro de aplicativo](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Concede as mesmas permissões que microsoft.directory/applications/basic/update, mas apenas para aplicativos de inquilino único.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credenciais/update

Capacidade de atualizar os certificados e propriedades de segredos do cliente em aplicativos de inquilino único e multilocatários. Concede acesso a todos os campos nos certificados de registro de inscrição & página de segredos:

![Esta permissão concede acesso aos certificados de registro do aplicativo & página de segredos](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Concede as mesmas permissões que microsoft.directory/applications/credentialss/update, mas apenas para aplicativos de diretório único.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Capacidade de atualizar a propriedade do proprietário em um único inquilino e multi-inquilino. Concede acesso a todos os campos na página de proprietários de registro de aplicativos:

![Essas permissões concedem acesso à página dos proprietários de registro de aplicativos](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Concede as mesmas permissões que microsoft.directory/applications/owners/update, mas apenas para aplicativos de inquilino único.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissões/atualização

Capacidade de atualizar as permissões delegadas, permissões de aplicativos, aplicativos de clientes autorizados, permissões necessárias e conceder propriedades de consentimento em aplicativos de inquilino único e multilocatários. Não concede a capacidade de realizar o consentimento. Concede acesso a todos os campos nas permissões de API de registro de aplicativos e expõe páginas de API:

![Essas permissões concedem acesso à página de permissões de API de registro de aplicativo](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Essas permissões concedem acesso ao registro do aplicativo Exponha uma página de API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissões/update

Concede as mesmas permissões que microsoft.directory/applications/permissões/atualização, mas apenas para aplicativos de inquilino único.

## <a name="required-license-plan"></a>Plano de licença necessário

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- Crie funções personalizadas usando [o portal Azure, o Azure AD PowerShell e a API do Graph](roles-create-custom.md)
- [Exibir as atribuições de uma função personalizada](roles-view-assignments.md)
