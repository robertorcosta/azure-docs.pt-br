---
title: Adicionar seu domínio personalizado – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar um domínio personalizado usando o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262146"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Adicionar um nome de domínio personalizado usando o portal do Azure Active Directory

Cada novo inquilino azure AD vem com um nome de domínio inicial, * \<nome de domínio>.onmicrosoft.com*. Você não pode alterar ou excluir o nome de domínio inicial, mas pode adicionar os nomes da sua organização. Adicionar nomes de domínio personalizados ajuda a criar nomes de usuário que são familiares aos seus usuários, como *alain\@contoso.com*.

## <a name="before-you-begin"></a>Antes de começar

Antes de adicionar um nome de domínio personalizado, crie seu nome de domínio com um registrador de domínio. Para um registrador de domínios reconhecido, consulte [Registradores reconhecidos pelo ICANN](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Criar seu diretório no Azure AD

Depois de obter seu nome de domínio, é possível criar seu primeiro diretório do Azure AD. Faça login no portal Azure para o seu diretório, usando uma conta com a função **Proprietário** para a assinatura.

Para criar seu novo diretório, siga as etapas em [Criar um novo locatário para sua organização](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>A pessoa que cria o locatário torna-se automaticamente o administrador global desse locatário. O administrador global pode adicionar mais administradores ao locatário.

Para obter mais informações sobre funções de assinatura, consulte [as funções do Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Se você planeja federar seu AD do Windows Server no local com o Azure AD, então você precisa selecionar **eu planejo configurar este domínio para logon único com o meu Diretório Ativo local** quando você executar a ferramenta Azure AD Connect para sincronizar seus diretórios.
>
> Você também precisa registrar o mesmo nome de domínio selecionado para federação com seu diretório local na etapa **domínio do Azure AD** no assistente. Para ver como é essa configuração, consulte [Verificar o domínio Azure AD selecionado para federação](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tem a ferramenta Azure AD Connect, você pode [baixá-la aqui](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Adicionar seu nome de domínio personalizado ao Azure AD

Depois de criar seu diretório, você pode adicionar seu nome de domínio personalizado.

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

1. Procure e selecione *o Diretório Ativo do Azure em* qualquer página. Em seguida, selecione **Nomes de domínio personalizados** > **Adicionar domínio personalizado**.

    ![Página de nomes de domínio personalizados, com Adicionar domínio personalizado mostrado](media/add-custom-domain/add-custom-domain.png)

1. Em **Nome de domínio Personalizado,** digite o novo nome da sua organização, neste exemplo, *contoso.com*. Selecione **Adicionar domínio**.

    ![Página de nomes de domínio personalizados, com adicionar página de domínio personalizada](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Você deve incluir *.com*, *.net*ou qualquer outra extensão de nível superior para que isso funcione corretamente.

    O domínio não verificado é adicionado. A página **contoso.com** aparece mostrando suas informações de DNS. Salve essas informações. Você precisa dele mais tarde para criar um registro TXT para configurar o DNS.

    ![Página da Contoso com informações sobre entrada de DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Adicionar suas informações de DNS ao registrador de domínios

Depois de adicionar seu nome de domínio personalizado ao Azure AD, você deve retornar ao seu registrador de domínios e adicionar as informações de DNS do Azure AD do seu arquivo TXT copiado. A criação deste registro TXT para seu domínio verifica a propriedade do seu nome de domínio.

Volte ao seu registrador de domínio e crie um novo registro TXT para seu domínio com base nas informações de DNS copiadas. Defina o tempo de vida (TTL) para 3600 segundos (60 minutos), e depois salve o registro.

>[!IMPORTANT]
>Você pode registrar quantos nomes de domínio desejar. No entanto, cada domínio obtém seu próprio registro TXT do Azure AD. Tenha cuidado ao inserir as informações do arquivo TXT no registrador de domínios. Se você inserir as informações erradas ou duplicadas por engano, você terá que esperar até que o TTL acabe (60 minutos) antes de tentar novamente.

## <a name="verify-your-custom-domain-name"></a>Verificar seu nome de domínio personalizado

Depois de registrar seu nome de domínio personalizado, certifique-se de que ele é válido no Azure AD. A propagação do seu registrador de domínio para o Azure AD pode ser instantânea ou pode levar alguns dias, dependendo do seu registrador de domínio.

Para verificar seu nome de domínio personalizado, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador Global para o diretório.

1. Procure e selecione *o Diretório Ativo do Azure* em qualquer página e, em seguida, selecione Nomes de domínio **personalizados**.

1. Em **nomes de domínio personalizados,** selecione o nome de domínio personalizado. Neste exemplo, selecione **contoso.com**.

    ![Página Fabrikam – nomes de domínio personalizado, com contoso realçado](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na página **contoso.com,** **selecione Verificar** para ter certeza de que seu domínio personalizado está devidamente registrado e é válido para a Azure AD.

    ![Página da Contoso com informações sobre entrada de DNS e o botão Verificar](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Depois de verificar seu nome de domínio personalizado, você pode excluir sua verificação txt ou arquivo MX.

## <a name="common-verification-issues"></a>Problemas comuns de verificação

Se o Azure AD não puder verificar um nome de domínio personalizado, tente as sugestões a seguir:

- **Aguarde pelo menos uma hora e tente novamente**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Esse processo pode levar uma hora ou mais.

- **Verifique se o registro DNS está correto.** Volte para o site do registrador de nomes de domínio. Certifique-se de que a entrada está lá e que corresponde às informações de entrada do DNS fornecidas pelo Azure AD.

  Se você não puder atualizar o registro no site do registrador, compartilhe a entrada com alguém que tenha permissões para adicionar a entrada e verificar se está correta.

- **Verifique se o nome de domínio não está em uso em outro diretório.** Um nome de domínio só pode ser verificado em um diretório. Se o nome de domínio for verificado no momento em outro diretório, ele também não poderá ser verificado no novo diretório. Para corrigir esse problema de duplicação, você deve excluir o nome de domínio do diretório antigo. Para obter mais informações sobre a exclusão de nomes de domínio, consulte [Gerenciar nomes de domínio personalizado](../users-groups-roles/domains-manage.md).

- **Confira se você não tem nenhum locatário do Power BI não gerenciado.** Se os usuários tiverem ativado o Power BI por meio de inscrição de autoatendimento e criado um locatário não gerenciado para sua organização, você deverá assumir o gerenciamento como administrador interno ou externo, usando o PowerShell. Para obter mais informações, confira [Controlar um diretório não gerenciado como administrador no Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Próximas etapas

- Adicione outro administrador global ao seu diretório. Para obter mais informações, consulte [Como atribuir funções e administradores](active-directory-users-assign-role-azure-portal.md).

- Adicione usuários ao seu domínio. Para obter mais informações, consulte [Como adicionar ou excluir usuários](add-users-azure-active-directory.md).

- Gerencie suas informações de nome de domínio no Azure AD. Para obter mais informações, confira [Gerenciamento de nomes de domínio personalizado](../users-groups-roles/domains-manage.md).

- Se você tiver versões locais do Windows Server que deseja usar junto com o Azure Active Directory, consulte [Integrar seus diretórios locais ao Azure Active Directory](../connect/active-directory-aadconnect.md).
