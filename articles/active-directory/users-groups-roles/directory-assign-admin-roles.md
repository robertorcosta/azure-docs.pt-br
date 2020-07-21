---
title: Descrições e permissões da função do Azure AD – Azure Active Directory | Microsoft Docs
description: Uma função de administrador pode adicionar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b94d195ea1f31d228505f01c2a77a299e63c0a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518089"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Permissões da função de administrador no Azure Active Directory

Usando o Azure Active Directory (Azure AD), você pode designar administradores limitados para gerenciar tarefas de identidade em funções com privilégios mínimos. Os administradores podem ser atribuídos para os fins de adicionar ou alterar os usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário e gerenciando nomes de domínio. As [permissões de usuário padrão](../fundamentals/users-default-permissions.md) podem ser alteradas somente nas configurações de usuário no Azure AD.

## <a name="limit-use-of-global-administrator"></a>Uso limitado do Administrador global

Os usuários atribuídos à função de Administrador global podem ler e modificar todas as configurações administrativa em sua organização do Azure AD. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de Administrador global para a organização do Azure AD. Apenas Administradores globais e Administradores de funções com privilégios podem delegar funções de administrador. Para reduzir o risco da sua empresa, recomendamos que você atribua essa função ao menor número possível de pessoas na sua organização.

Como uma melhor prática, recomendamos que você atribua essa função a menos de cinco pessoas em sua organização. Caso você tenha mais de cinco administradores atribuídos à função de Administrador global em sua organização, aqui estão algumas maneiras de reduzir seu uso.

### <a name="find-the-role-you-need"></a>Localizar a função necessária

Caso seja frustrante para você encontrar a função de que precisa para uma lista de muitas funções, o Azure AD poderá mostrar subconjuntos das funções com base nas categorias de função. Confira nosso novo filtro **Tipo** para [Funções e administradores do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) para mostrar apenas as funções do tipo selecionado.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Agora existe uma função que não existia quando você atribuiu a função de Administrador global

É possível que uma ou mais funções tenham sido adicionadas ao Azure AD, as quais fornecem permissões mais granulares que não eram uma opção quando você elevou alguns usuários para Administrador global. Com o passar do tempo, estamos distribuindo funções adicionais que realizam tarefas que, anteriormente, apenas a função de Administrador global poderia realizar. Você pode ver isso refletido nas [Funções disponíveis](#available-roles) a seguir.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Exibir e atribuir funções de administrador no Azure Active Directory](directory-manage-roles-portal.md).

> [!Note]
> Se você tiver uma licença do Azure AD Premium P2 e já for um usuário Privileged Identity Management (PIM), todas as tarefas de gerenciamento de função serão executadas no gerenciamento de identidades de privilégio e não no Azure AD.
>
> ![Funções do Azure AD gerenciadas no PIM para usuários que já usam o PIM e que têm uma licença Premium P2](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

### <a name="application-administrator"></a>[Administrador de Aplicativos](#application-administrator-permissions)

Os usuários nessa função podem criar e gerenciar todos os aspectos de aplicativos empresariais, registros dos aplicativos e configurações de proxy de aplicativos. Observe que os usuários atribuídos a essa função não são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

Os Administradores de Aplicativos podem gerenciar credenciais de aplicativo que permitam que eles o representem. Portanto, os usuários atribuídos a essa função só podem gerenciar as credenciais dos aplicativos que não estejam atribuídos a nenhuma função do Azure AD ou daqueles atribuídos somente às seguintes funções de administrador:

* Administrador de aplicativos
* Desenvolvedor de aplicativos
* Administrador de Aplicativos de Nuvem
* Leitores de Diretório

Caso um seja aplicativo atribuído a qualquer outra função que não esteja mencionada acima, o Administrador de Aplicativos não poderá gerenciar as credenciais desse aplicativo.

Essa função também concede a capacidade de _consentimento_ para permissões delegadas e permissões do aplicativo, com a exceção de permissões na API do Microsoft Graph.

> [!IMPORTANT]
> Essa exceção significa que você ainda pode consentir com permissões para _outros_ aplicativos (por exemplo, aplicativos que não sejam da Microsoft ou aplicativos que você tenha registrado), mas não com permissões no próprio Azure AD. Você ainda pode _solicitar_ essas permissões como parte do registro do aplicativo, mas a _concessão_ (ou seja, o consentimento) dessas permissões requer um administrador do Azure AD. Dessa foram, um usuário mal-intencionado não poderá facilmente elevar suas próprias permissões ao, por exemplo, criar e consentir com um aplicativo que possa fazer gravações em todo o diretório e, por meio das permissões do aplicativo, tornar a si mesmo um Administrador global.

### <a name="application-developer"></a>[Desenvolvedor de Aplicativo](#application-developer-permissions)

Os usuários nessa função podem criar registros dos aplicativos quando a configuração "Usuários podem registrar aplicativos" estiver definida como Não. Essa função também concede a permissão de consentir em nome de alguém quando a configuração “Usuários podem consentir em aplicativos acessando dados da empresa em seu nome” estiver definida como Não. Os usuários atribuídos a essa função são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

### <a name="authentication-administrator"></a>[Administrador de autenticação](#authentication-administrator-permissions)

Os usuários com essa função podem definir ou redefinir credenciais que não exijam senha de alguns usuários, além de poderem atualizar as senhas de todos os usuários. Os Administradores de autenticação podem exigir que os usuários que não sejam administradores ou que estejam atribuídos a algumas funções refaçam o registro de credenciais existentes que não usam senhas (por exemplo, MFA ou FIDO) e também possam revogar a **lembrança da MFA no dispositivo**, o qual solicitará a MFA na próxima entrada. Essas ações se aplicam apenas a usuários que não sejam administradores ou àqueles que tenham uma ou mais das seguintes funções atribuídas:

* Administrador de Autenticação
* Leitores de Diretório
* Emissor do Convite ao Convidado
* Leitor do Centro de Mensagens
* Leitor de Relatórios

A função de [Administrador de autenticação privilegiada](#privileged-authentication-administrator) tem permissão para forçar o novo registro e a autenticação multifator para todos os usuários.

> [!IMPORTANT]
> Usuários com essa função podem alterar credenciais de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração das credenciais de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
>
>- Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar que não foram concedidas a Administradores de Autenticação. Por esse caminho, um Administrador de Autenticação pode ser capaz de assumir a identidade de um proprietário de aplicativo e, depois, assumir a identidade de um aplicativo com privilégios, atualizando as credenciais do aplicativo.
>- Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configurações críticas no Azure.
>- Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
>- Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
>- Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

### <a name="azure-devops-administrator"></a>[Administrador do Azure DevOps](#azure-devops-administrator-permissions)

Os usuários com essa função podem gerenciar a política do Azure DevOps para restringir a criação da nova organização do Azure DevOps a um conjunto de usuários ou grupos configuráveis. Os usuários com essa função podem gerenciar essa política por meio de qualquer organização do Azure DevOps que tenha sido apoiada na organização do Azure AD da empresa.

Todas as políticas do Azure DevOps empresarial podem ser gerenciadas por usuários com essa função.

### <a name="azure-information-protection-administrator"></a>[Administrador da Proteção de Informações do Azure](#azure-information-protection-administrator-permissions)

Usuários com essa função têm todas as permissões no serviço de Proteção de Informações do Azure. Esta função pode configurar rótulos para a política da Proteção de Informações do Azure, gerenciar modelos de proteção e ativar a proteção. Esta função não garante permissões de usuário no Identity Protection Center, Privileged Identity Management, Monitorar Integridade de Serviço do Office 365 ou Centro de Segurança e Conformidade do Office 365.

### <a name="b2c-ief-keyset-administrator"></a>[Administrador de Conjunto de Chaves do IEF B2C](#b2c-ief-keyset-administrator-permissions)

O usuário pode criar e gerenciar chaves de política e segredos de criptografia de token, assinaturas de token e criptografia/descriptografia de declaração. Ao adicionar novas chaves a contêineres de chave existentes, esse administrador limitado pode sobrepor segredos conforme necessário e sem afetar os aplicativos existentes. Esse usuário pode ver o conteúdo completo desses segredos e suas datas de validade mesmo após sua criação.

> [!IMPORTANT]
> Essa é uma função confidencial. A função de administrador de conjunto de chaves deve ser cuidadosamente auditada e atribuída com cuidado durante a pré-produção e produção.

### <a name="b2c-ief-policy-administrator"></a>[Administrador de Política do IEF B2C](#b2c-ief-policy-administrator-permissions)

Os usuários com essa função têm a capacidade de criar, ler, atualizar e excluir todas as políticas personalizadas no Azure AD B2C e, assim, têm controle total sobre a Identity Experience Framework na organização Azure AD B2C relevante. Ao editar políticas, esse usuário pode estabelecer federação direta com provedores de identidade externos, alterar o esquema do diretório, alterar todo o conteúdo voltado para o usuário (HTML, CSS, JavaScript), alterar os requisitos para concluir uma autenticação, criar novos usuários, enviar dados do usuário para sistemas externos, incluindo migrações completas, e editar todas as informações do usuário, inclusive campos confidenciais, como senhas e números de telefone. Por outro lado, essa função não pode alterar as chaves de criptografia nem editar os segredos usados para federação na organização.

> [!IMPORTANT]
> O Administrador de Política IEF B2 é uma função altamente confidencial, a qual deve ser atribuída de modo muito limitado para organizações em criação. As atividades feitas por esses usuários devem ser rigorosamente auditadas, especialmente para organizações em criação.

### <a name="billing-administrator"></a>[Administrador de cobrança](#billing-administrator-permissions)

Faz compras, gerencia assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

### <a name="cloud-application-administrator"></a>[Administrador de Aplicativos de Nuvem](#cloud-application-administrator-permissions)

Os usuários nessa função têm as mesmas permissões que a função Administrador de Aplicativos, excluindo a capacidade de gerenciar o proxy de aplicativo. Essa função concede a capacidade de criar e gerenciar todos os aspectos de aplicativos corporativos e os registros do aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo a API do Microsoft Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

Os Administradores de Aplicativos de Nuvem podem gerenciar credenciais de aplicativo que permitam que eles o representem. Portanto, os usuários atribuídos a essa função só podem gerenciar as credenciais dos aplicativos que não estejam atribuídos a nenhuma função do Azure AD ou daqueles atribuídos somente às seguintes funções de administrador:

* Desenvolvedor de aplicativos
* Administrador de Aplicativos de Nuvem
* Leitores de Diretório

Caso um seja aplicativo atribuído a qualquer outra função que não esteja mencionada acima, o Administrador de Aplicativos de Nuvem não poderá gerenciar as credenciais desse aplicativo.

### <a name="cloud-device-administrator"></a>[Administrador de dispositivo de nuvem](#cloud-device-administrator-permissions)

Os usuários nessa função podem habilitar, desabilitar e excluir dispositivos no Azure AD e ler chaves do Windows 10 BitLocker (se houver) no portal do Azure. A função não concede permissões para gerenciar nenhuma outra propriedade no dispositivo.

### <a name="compliance-administrator"></a>[Administrador de conformidade](#compliance-administrator-permissions)

Os usuários com essa função têm permissões para gerenciar recursos relacionados à conformidade no centro de conformidade do Microsoft 365, no centro de administração do Microsoft 365, no Azure e no Centro de Conformidade e Segurança do Office 365. Os destinatários também podem gerenciar todos os recursos no Centro de administração do Exchange e nos Centros de administração do Teams e do Skype for Business e criar tíquetes de suporte para o Azure e o Microsoft 365. Há mais informações disponíveis em [Sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

No | O que ele pode fazer
----- | ----------
[Centro de conformidade do Microsoft 365](https://protection.office.com) | Proteger e gerenciar dados da sua organização em todos os serviços do Microsoft 365<br>Gerenciar alertas de conformidade
[Gerenciador de Conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhar, atribuir e verificar as atividades de conformidade regulatória da sua organização
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar a governança de dados<br>Executar investigação jurídica e de dados<br>Gerenciar solicitação do titular dos dados<br><br>Essa função tem as mesmas permissões que o [Administrador de conformidade de RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no controle de acesso baseado em função do Centro de Conformidade e Segurança do Office 365.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br>Pode exibir todos os relatórios internos em Gerenciamento de Dados

### <a name="compliance-data-administrator"></a>[Administrador de Dados de Conformidade](#compliance-data-administrator-permissions)

Os usuários com essa função têm permissões para monitorar dados no Centro de conformidade do Microsoft 365, no Centro de administração do Microsoft 365 e no Azure. Os usuários também podem monitorar dados de conformidade no Centro de administração do Exchange, no Gerenciador de conformidade e no Centro de administração do Teams e do Skype for Business, além de criar tíquetes de suporte para o Azure e o Microsoft 365.

No | O que ele pode fazer
----- | ----------
[Centro de conformidade do Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas a conformidade em todos os serviços do Microsoft 365<br>Gerenciar alertas de conformidade
[Gerenciador de Conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhar, atribuir e verificar as atividades de conformidade regulatória da sua organização
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar a governança de dados<br>Executar investigação jurídica e de dados<br>Gerenciar solicitação do titular dos dados<br><br>Essa função tem as mesmas permissões que o [Administrador de Dados de Conformidade de RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no controle de acesso baseado em função do Centro de Conformidade e Segurança do Office 365.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br>Pode exibir todos os relatórios internos em Gerenciamento de Dados

### <a name="conditional-access-administrator"></a>[Administrador de acesso condicional](#conditional-access-administrator-permissions)

Usuários com essa função têm a capacidade de gerenciar as configurações de Acesso Condicional do Azure Active Directory.
> [!NOTE]
> Para implantar a política de Acesso Condicional do Exchange ActiveSync no Azure, o usuário também deve ser um Administrador Global.

### <a name="customer-lockbox-access-approver"></a>[Aprovador de acesso do sistema de proteção de dados do cliente](#customer-lockbox-access-approver-permissions)

gerencia [solicitações do Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) em sua organização. O aprovador recebe notificações de solicitações do Sistema de Proteção de Dados do Cliente por email e pode aprovar e negar solicitações do Centro de administração do Microsoft 365. Ele também pode ligar ou desligar o recurso Sistema de Proteção de Dados do Cliente. Somente os administradores globais podem redefinir as senhas das pessoas atribuídas à função acima.

### <a name="desktop-analytics-administrator"></a>[Administrador de Análise de Área de Trabalho](#desktop-analytics-administrator-permissions)


Os usuários com essa função podem gerenciar a Análise de Área de Trabalho e os serviços de Personalização e Política do Office. Para a Análise de Área de Trabalho, isso inclui a capacidade de exibir o inventário de ativos, criar planos de implantação, exibir o status de integridade e de implantação. Para o serviço de Personalização e Política do Office, essa função permite que os usuários gerenciem as políticas do Office.

### <a name="device-administrators"></a>[Administradores de Dispositivos](#device-administrators-permissions)

Essa função está disponível para atribuição apenas como um administrador local adicional em [Configurações do dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os usuários com essa função se tornam administradores de computador local em todos os dispositivos Windows 10 associados ao Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory.

### <a name="directory-readers"></a>[Leitores de Diretório](#directory-readers-permissions)

Os usuários com essa função podem ler informações básicas do diretório. Essa função deve ser usada para:

* Conceder o acesso de leitura a um conjunto específico de usuários convidados em vez de a todos os usuários convidados.
* Conceder a um conjunto específico de usuários não administradores o acesso ao portal do Azure quando a opção “Restringir o acesso ao portal do Azure AD somente para administradores” estiver definida como “Sim”.
* Conceder acesso às entidades de serviço ao diretório no qual Directory.Read.All não seja uma opção.

### <a name="directory-synchronization-accounts"></a>[Contas de Sincronização de Diretório](#directory-synchronization-accounts-permissions)

Não use. Essa função é automaticamente atribuída ao serviço do Azure AD Connect e não tem intenção ou suporte para outros usos.

### <a name="directory-writers"></a>[Gravadores de diretório](#directory-writers-permissions)

Essa é uma função herdada que deve ser atribuída a aplicativos que não tenham suporte em [Estrutura de Consentimento](../develop/quickstart-register-app.md). Ele não deve ser atribuído a nenhum usuário.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Administrador do Dynamics 365/Administrador do CRM](#crm-service-administrator-permissions)

Os usuários com essa função têm permissões globais no Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerenciar tíquete de suporte e monitorar a integridade do serviço. Mais informações em [Usar a função de administrador de serviço para gerenciar sua organização do Azure AD](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Na API do Microsoft Graph e no PowerShell do Azure AD, essa função é identificada como “Administrador de Serviços do Dynamics 365”. É "Administrador do Dynamics 365" no [portal do Azure](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Administrador do Exchange](#exchange-service-administrator-permissions)

Os usuários com essa função têm permissões globais no Microsoft Exchange Online, quando o serviço está presente. Eles também tem a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como “Administrador de Serviços do Exchange”. É "Administrador do Exchange" no [portal do Azure](https://portal.azure.com). É “Administrador do Exchange Online” no [Centro de administração do Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).


### <a name="external-id-user-flow-administrator"></a>[Administrador de fluxo do usuário de ID Externa](#external-id-user-flow-administrator-permissions)

Os usuários com essa função podem criar e gerenciar fluxos de usuário (também chamados de políticas "internas") no portal do Azure. Esses usuários podem personalizar o conteúdo HTML/CSS/JavaScript, alterar os requisitos de MFA, selecionar declarações no token, gerenciar conectores de API e definir configurações de sessão para todos os fluxos de usuário na organização do Azure AD. Por outro lado, essa função não inclui a capacidade de revisar dados do usuário ou fazer alterações nos atributos que estão incluídos no esquema da organização. As alterações nas políticas da estrutura de experiência de identidade (também conhecidas como políticas personalizadas) também estão fora do escopo dessa função.

### <a name="external-id-user-flow-attribute-administrator"></a>[Administrador de atributo de fluxo do usuário de ID Externa](#external-id-user-flow-attribute-administrator-permissions)

Os usuários com essa função adicionam ou excluem atributos personalizados que estão disponíveis para todos os fluxos dos usuários na organização do Azure AD. Assim, eles podem alterar ou adicionar novos elementos ao esquema do usuário final e afetar o comportamento de todos os fluxos dos usuários, podendo resultar indiretamente em alterações nos dados que podem ser solicitados aos usuários finais e, por fim, enviados como declarações aos aplicativos. Essa função não pode editar os fluxos dos usuários.

### <a name="external-identity-provider-administrator"></a>[Administrador do provedor de identidade externa](#external-identity-provider-administrator-permissions)

Esse administrador gerencia a federação entre as organizações do Azure AD e os provedores de identidade externos. Com essa função, os usuários podem adicionar novos provedores de identidade e definir todas as configurações disponíveis (por exemplo, caminho de autenticação, ID de serviço e contêineres de chave atribuídos). Esse usuário pode habilitar a organização do Azure AD a confiar em autenticações de provedores de identidade externos. O impacto sobre experiências do usuário final depende do tipo de organização:

* Organizações do Azure AD para funcionários e parceiros: a adição de uma federação (por exemplo, com o Gmail) afetará imediatamente todos os convites de convidados que ainda não tenham sido resgatados. Consulte [Adicionar o Google como provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Organizações do Azure Active Directory B2C: a adição de uma federação (por exemplo, com o Facebook ou outra organização do Azure AD) não afeta imediatamente os fluxos dos usuários finais até que o provedor de identidade seja adicionado como uma opção em um fluxo dos usuários (também chamada de política interna). Consulte [Configurar uma conta da Microsoft como um provedor de identidade](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) para ver um exemplo. Para alterar os fluxos dos usuários, é preciso ter a função limitada de “Administrador de Fluxos dos Usuários B2C”.

### <a name="global-administrator--company-administrator"></a>[Administrador Global/Administrador de Empresa](#company-administrator-permissions)

Os usuários com essa função têm acesso a todos os recursos administrativos do Azure Active Directory, bem como aos serviços que usam identidades do Azure Active Directory como centro de segurança do Microsoft 365, centro de conformidade do Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. Além disso, os administradores globais podem [elevar seu acesso](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin) para gerenciar todas as assinaturas e grupos de gerenciamento do Azure. Isso permite que os administradores globais obtenham acesso completo a todos os recursos do Azure usando o respectivo locatário do Azure AD. A pessoa que se inscreve na organização do Azure AD se torna um administrador global. Pode haver mais de um administrador global na sua empresa. Administradores globais podem redefinir a senha para qualquer usuário e todos os outros administradores.

> [!NOTE]
> Na API do Microsoft Graph e no PowerShell do Azure AD, essa função é identificada como “Administrador de Empresa”. É "Administrador Global" no [portal do Azure](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Leitor global](#global-reader-permissions)

Os usuários com essa função podem ler configurações e informações administrativas nos serviços do Microsoft 365, mas não podem realizar ações de gerenciamento. O Leitor global é o equivalente à função de somente leitura do Administrador global. Atribua um Leitor global em vez de um Administrador global para planejamentos, auditorias ou investigações. Use o Leitor global junto com outras funções de administrador limitadas, como Administrador do Exchange, para facilitar o trabalho sem a atribuição da função de Administrador global. O Leitor global funciona com Centro de administração do Microsoft 365, Centro de administração do Exchange, Centro de administração do SharePoint, Centro de administração do Teams, Central de segurança, Centro de conformidade, Centro de administração do Azure AD e Centro de administração do gerenciamento de dispositivo.

> [!NOTE]
> A função de Leitor global tem algumas limitações atualmente:
>
>- [Centro de administração do onedrive](https://admin.onedrive.com/) – o centro de administração do onedrive não dá suporte à função de leitor global
>- [Centro de administração do M365](https://admin.microsoft.com/Adminportal/Home#/homepage) – o Leitor global não pode ler solicitações do Sistema de Proteção de Dados do Cliente. Você não encontrará a guia **Solicitações do Sistema de Proteção de Dados do Cliente** em **Suporte** no painel esquerdo do Centro de administração do M365.
>- [Centro de Conformidade e Segurança do Office](https://sip.protection.office.com/homepage) – o Leitor global não pode ler logs de auditoria SCC, fazer pesquisa de conteúdo ou ver a classificação de segurança.
>- [Centro de administração do Teams](https://admin.teams.microsoft.com) – o Leitor global não pode ler o **ciclo de vida do Teams**, **Análises e relatórios**, **Gerenciamento de dispositivo de telefone IP** e **Catálogo de aplicativos**.
>- O [Privileged Access Management (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) não tem suporte à função de Leitor global.
>- [Proteção de Informações do Azure](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) – o Leitor global só tem suporte para [relatórios centrais](https://docs.microsoft.com/azure/information-protection/reports-aip) e quando sua organização do Azure AD não está na [plataforma de rotulagem unificada](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Os recursos a seguir estão em desenvolvimento atualmente.
>

### <a name="groups-administrator"></a>[Administrador de Grupos](#groups-administrator-permissions)

Os usuários com essa função podem criar/gerenciar grupos e suas configurações, como políticas de nomenclatura e de expiração. É importante entender que, ao atribuir um usuário a essa função, eles terão a capacidade de gerenciar todos os grupos da organização em várias cargas de trabalho, como Teams, SharePoint e Yammer, além do Outlook. Além disso, o usuário poderá gerenciar as configurações de vários grupos em vários portais de administração, como o Centro de administração da Microsoft, o portal do Azure, bem como os específicos de cargas de trabalho, como o Teams e Centros de administração do SharePoint.

### <a name="guest-inviter"></a>[Emissor do Convite ao Convidado](#guest-inviter-permissions)

Usuários nessa função podem gerenciar convites de usuários convidados do Azure Active Directory B2B quando a configuração do usuário **Membros podem convidar** estiver definida como Não. Mais informações sobre a colaboração B2B em [Sobre a colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Ela não inclui nenhuma outra permissão.

### <a name="helpdesk-administrator"></a>[Administrador de Assistência Técnica](#helpdesk-administrator-permissions)

Usuários com essa função podem alterar senhas, invalidar tokens de atualização, gerenciar solicitações de serviço e monitorar a integridade do serviço. Invalidar um token de atualização força o usuário a entrar novamente. Os administradores do suporte técnico podem redefinir senhas e invalidar tokens de atualização de outros usuários que não sejam administradores nem atribuídos às seguintes funções apenas:

* Leitores de Diretório
* Emissor do Convite ao Convidado
* Administrador de assistência técnica
* Leitor do Centro de Mensagens
* Leitor de Relatórios

> [!IMPORTANT]
> Usuários com essa função podem alterar senhas de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração da senha de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
>
>- Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar, não concedidas a Administradores de Assistência Técnica. Por esse caminho, um Administrador de Assistência Técnica pode ser capaz de assumir a identidade de um proprietário de aplicativo e, depois, assumir a identidade de um aplicativo com privilégios, atualizando as credenciais do aplicativo.
>- Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou a configurações críticas no Azure.
>- Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
>- Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
>- Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

É possível delegar permissões administrativas sobre subconjuntos de usuários e aplicar políticas a um subconjunto de usuários usando [Unidades administrativas (agora em visualização pública)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).

Essa função era anteriormente chamada de “Administrador de senha” no [portal do Azure](https://portal.azure.com/). O nome “Administrador de assistência técnica” no Azure AD agora corresponde ao seu nome no PowerShell do Azure AD e na API do Microsoft Graph.

### <a name="hybrid-identity-administrator"></a>[Administrador de Identidade Híbrida](#hybrid-identity-administrator-permissions)

Os usuários com essa função podem habilitar, definir e gerenciar serviços e configurações relacionados à habilitação da identidade híbrida no Azure AD. Essa função concede a capacidade de configurar o Azure AD a um dos três métodos de autenticação com suporte, a saber: PHS (Sincronização de hash de senha), PTA (Autenticação de passagem) ou Federação (AD FS ou provedor de federação de terceiros), além de implantar a infraestrutura local relacionada para habilitá-los. A infraestrutura local inclui agentes de provisionamento e de PTA. Essa função concede a capacidade de habilitar o S-SSO (Logon único contínuo) para habilitar a autenticação direta em dispositivos que não usem o Windows 10 ou em computadores que não usem o Windows Server 2016. Além disso, essa função concede a capacidade de ver logs de entrada e acessar a integridade e análise para fins de monitoramento e solução de problemas. 

### <a name="intune-administrator"></a>[Administrador do Intune](#intune-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft Intune Online, quando o serviço está presente. Além disso, essa função contém a capacidade de gerenciar usuários e dispositivos para associar a política, bem como criar e gerenciar grupos. Obtenha mais informações em [Controle de administração baseada em funções (RBAC) com o Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

Essa função pode criar e gerenciar todos os grupos de segurança. Porém, o Administrador do Intune não tem direitos de administrador sobre grupos do Office. Isso significa que ele não pode atualizar os proprietários ou as associações de todos os grupos do Office na organização. Porém, ele pode gerenciar o grupo do Office que ele mesmo criar, o que vem como parte de seus privilégios de usuário final. Portanto, qualquer grupo do Office (não grupos de segurança) que ele cria deve ser incluído em sua cota de 250.

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços do Intune". É "Administrador do Intune" no [portal do Azure](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Administrador do Kaizala](#kaizala-administrator-permissions)

Usuários com essa função têm permissões globais para gerenciar configurações no Microsoft Kaizala, quando o serviço estiver presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Além disso, o usuário pode acessar relatórios relacionados à adoção e ao uso do Kaizala por membros da organização e relatórios comerciais gerados usando as ações do Kaizala.

### <a name="license-administrator"></a>[Administrador de Licenças](#license-administrator-permissions)

Usuários nessa função podem adicionar, remover e atualizar as atribuições de licenças em usuários, grupos (usando o licenciamento baseado em grupo) e gerenciar a localização de uso dos usuários. A função não concede a capacidade de comprar ou gerenciar assinaturas, criar ou gerenciar grupos, ou criar ou gerenciar usuários além do local de uso. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="message-center-privacy-reader"></a>[Leitor de Privacidade do Centro de Mensagens](#message-center-privacy-reader-permissions)

Os usuários com essa função podem monitorar todas as notificações no Centro de Mensagens, incluindo mensagens de privacidade de dados. Os Leitores de Privacidade do Centro de Mensagens recebem notificações por email, incluindo aquelas relacionadas à privacidade dos dados, e podem cancelar a assinatura usando as preferências do Centro de Mensagens. Somente o Administrador global e o Leitor de Privacidade do Centro de Mensagens podem ler mensagens de privacidade de dados. Além disso, essa função tem a capacidade de exibir grupos, domínios e assinaturas. Essa função não tem permissão para exibir, criar ou gerenciar solicitações de serviço.

### <a name="message-center-reader"></a>[Leitor do Centro de Mensagens](#message-center-reader-permissions)

Usuários nessa função podem monitorar notificações e atualizações de integridade de consultoria no [Centro de Mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) da organização em serviços configurados como Exchange, Intune e Microsoft Teams. Os Leitores do Centro de Mensagens recebem por email resumos semanais de postagens, atualizações e podem compartilhar postagens do Centro de Mensagens no Office 365. No Azure AD, os usuários atribuídos a essa função terão acesso somente leitura aos serviços do Azure AD como usuários e grupos. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="modern-commerce-administrator"></a>[Administrador de Comércio Moderno](#modern-commerce-administrator-permissions)

Não use. Essa função é automaticamente atribuída a partir do Comércio e não tem a finalidade ou o suporte para outros usos. Veja detalhes abaixo.

A função de Administrador de Comércio Moderno dá permissão a determinados usuários para acessar o Centro de administração do Microsoft 365 e ver as entradas de navegação à esquerda em **Página Inicial**, **Cobrança** e **Suporte**. O conteúdo disponível nessas áreas é controlado por [funções específicas de comércio](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-mca-roles) atribuídas aos usuários para gerenciar os produtos que eles compraram para si mesmos ou para a sua organização. Isso pode incluir tarefas como pagar faturas ou acessar contas de cobrança e perfis de cobrança. 

Os usuários com a função de Administrador de Comércio Moderno normalmente têm permissões administrativas em outros sistemas de compra da Microsoft, mas não têm funções de Administrador global nem de Administrador de cobrança usadas para acessar o centro de administração. 

**Quando a função de Administrador de Comércio Moderno é atribuída?**

* **Compra por autoatendimento no Centro de administração do Microsoft 365** – a compra por autoatendimento dá aos usuários a oportunidade de experimentar novos produtos comprando-os ou inscrevendo-se neles por conta própria. Esses produtos são gerenciados no centro de administração. Os usuários que fazem uma compra por autoatendimento recebem uma função no sistema de comércio e a função de Administrador de Comércio Moderno, assim, eles podem gerenciar suas compras no centro de administração. Os administradores podem bloquear compras por autoatendimento (para Power BI, Power Apps e Power Automate) por meio do [PowerShell](https://docs.microsoft.com/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell?view=o365-worldwide). Para obter mais informações, consulte [Perguntas frequentes da compra por autoatendimento](https://docs.microsoft.com/microsoft-365/commerce/subscriptions/self-service-purchase-faq?view=o365-worldwide).  
* **Compras no marketplace comercial da Microsoft** – de modo semelhante à compra por autoatendimento, quando um usuário compra um produto ou serviço do Microsoft AppSource ou do Azure Marketplace, a função de Administrador de Comércio Moderno será atribuída caso não haja a função de Administrador global ou de Administrador de cobrança. Em alguns casos, os usuários podem ser impedidos de fazer essas compras. Para obter mais informações, consulte [Marketplace comercial da Microsoft](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase).
* **Propostas da Microsoft** – uma proposta é uma oferta formal enviada pela Microsoft para a sua organização comprar produtos e serviços da Microsoft. Quando a pessoa que estiver aceitando a proposta não tiver a função de Administrador global ou de Administrador de cobrança no Azure AD, ela receberá uma função específica de comércio para concluir a proposta, além da função de Administrador de Comércio Moderno para acessar o centro de administração. Ao acessar o centro de administração, essa pessoa só poderá usar recursos que sejam autorizados por sua função específica de comércio.
* **Funções específicas de comércio** – alguns usuários recebem esse tipo de função. Caso um usuário não seja um Administrador global nem um Administrador de cobrança, ele obterá a função de Administrador de Comércio Moderno para poder acessar o centro de administração.  

Caso a atribuição da função de Administrador de Comércio Moderno de um usuário seja cancelada, ele perderá o acesso ao Centro de administração do Microsoft 365. Caso ele estivesse gerenciando produtos, seja para si próprio ou para a sua organização, ele não poderá mais fazê-lo. Isso pode incluir a atribuição de licenças, a alteração de métodos de pagamento, a cobrança de contas ou mesmo outras tarefas de gerenciamento de assinaturas.

### <a name="network-administrator"></a>[Administrador de Rede](#network-administrator-permissions)

Os usuários com essa função podem revisar as recomendações de arquitetura do perímetro de rede da Microsoft que sejam baseadas na telemetria de rede de seus locais de usuário. O desempenho de rede do Office 365 conta com uma cuidadosa arquitetura de perímetro de rede de clientes comerciais, a qual, geralmente, é específica ao local do usuário. Essa função permite a edição de locais de usuários descobertos e a configuração de parâmetros de rede para esses locais para facilitar medidas de telemetria e recomendações de design aprimoradas
### <a name="office-apps-administrator"></a>[Administrador de Aplicativos do Office](#office-apps-administrator-permissions)

Os usuários com essa função podem gerenciar as configurações de nuvem dos aplicativos do Office 365. Isso inclui o gerenciamento de políticas de nuvem, o gerenciamento de download de autoatendimento, além da capacidade de exibir relatórios relacionados aos aplicativos do Office. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço no centro de administração principal. Os usuários atribuídos a essa função também podem gerenciar a comunicação de novos recursos nos aplicativos do Office. 

### <a name="partner-tier1-support"></a>[Suporte de nível 1 ao parceiro](#partner-tier1-support-permissions)

Não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

### <a name="partner-tier2-support"></a>[Suporte de nível 2 ao parceiro](#partner-tier2-support-permissions)

Não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

### <a name="password-administrator"></a>[Administrador de senha](#password-administrator-permissions)

Os usuários com essa função têm uma capacidade limitada de gerenciamento de senhas. Essa função não concede a capacidade de gerenciar solicitações de serviço nem de monitorar a integridade do serviço. Os Administradores de senha podem redefinir senhas de outros usuários que não sejam administradores ou membros das seguintes funções apenas:

* Leitores de Diretório
* Emissor do Convite ao Convidado
* Administrador de senha

### <a name="power-bi-administrator"></a>[Administrador do Power BI](#power-bi-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Noções básicas sobre a função de administrador do Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> Na API do Microsoft Graph e no PowerShell do Azure AD, essa função é identificada como “Administrador de Serviços do Power BI”. É "Administrador do Power BI" no [portal do Azure](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Administrador do Power Platform](#power-platform-administrator-permissions)

Os usuários com essa função podem criar e gerenciar todos os aspectos de políticas de ambientes, PowerApps, Fluxos, Prevenção de perda de dados. Além disso, os usuários com essa função têm a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço.

### <a name="printer-administrator"></a>[Administrador de impressora](#printer-administrator-permissions)

Os usuários com essa função podem registrar impressoras e gerenciar todos os aspectos de todas as configurações de impressora na Solução de impressão universal da Microsoft, incluindo as configurações do Conector de impressão universal. Eles podem consentir com todas as solicitações de permissão de impressão delegadas. Os Administradores de impressora também têm acesso aos relatórios de impressão.

### <a name="printer-technician"></a>[Técnico de impressora](#printer-technician-permissions)

Os usuários com essa função podem registrar impressoras e gerenciar seus status na Solução de impressão universal da Microsoft. Eles também podem ler todas as informações do conector. Duas importantes tarefas que um Técnico de impressora não pode fazer é definir permissões de usuário em impressoras e compartilhar impressoras.

### <a name="privileged-authentication-administrator"></a>[Administrador de autenticação privilegiada](#privileged-authentication-administrator-permissions)

Os usuários com essa função podem definir ou redefinir credenciais que não usem senha para todos os usuários, inclusive administradores globais, além de poderem atualizar as senhas de todos os usuários. Os Administradores de autenticação privilegiada podem forçar os usuários a se registrarem novamente em relação a uma credencial existente (como MFA ou FIDO) e revogar a opção de “lembrar MFA no dispositivo”, solicitando a MFA na próxima entrada de todos os usuários. A função de [Administrador de autenticação](#authentication-administrator) pode forçar um novo registro e a MFA apenas para não administradores e usuários atribuídos às seguintes funções do Azure AD:

* Administrador de Autenticação
* Leitores de Diretório
* Emissor do Convite ao Convidado
* Leitor do Centro de Mensagens
* Leitor de Relatórios

### <a name="privileged-role-administrator"></a>[Administrador de Função com Privilégios](#privileged-role-administrator-permissions)

Usuários com essa função podem gerenciar as atribuições de função no Azure Active Directory, bem como Azure Active Directory Privileged Identity Management. Além disso, essa função permite o gerenciamento de todos os aspectos do Privileged Identity Management e de unidades administrativas.

> [!IMPORTANT]
> Essa função concede a capacidade de gerenciar atribuições a todas as funções do Azure AD, incluindo a função de Administrador global. Essa função não inclui outras habilidades privilegiadas no Azure AD, como criar ou atualizar usuários. No entanto, os usuários atribuídos a essa função podem conceder a si mesmos ou aos privilégios adicionais de outras pessoas atribuindo funções adicionais.

### <a name="reports-reader"></a>[Leitor de relatórios](#reports-reader-permissions)

Usuários com essa função podem exibir os dados de relatórios de uso e o painel de relatórios no Centro de administração do Microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de entrada e atividades no Azure AD e a dados retornados pela API de relatórios do Microsoft Graph. Um usuário atribuído à função Leitor de Relatórios pode acessar somente o uso relevante e as métricas de adoção. Eles não têm permissões de administrador para definir configurações ou acessar que os centros da administração de produtos específicos como o Exchange. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="search-administrator"></a>[Administrador de pesquisas](#search-administrator-permissions)

Os usuários com essa função têm acesso completo a todos os recursos de gerenciamento da Pesquisa da Microsoft no Centro de administração do Microsoft 365. Além disso, esses usuários podem exibir o centro de mensagens, monitorar a integridade de serviço e criar solicitações de serviço.

### <a name="search-editor"></a>[Editor de pesquisa](#search-editor-permissions)

Os usuários com essa função podem criar, gerenciar e excluir conteúdo para a Pesquisa da Microsoft no Centro de administração do Microsoft 365, inclusive indicadores, perguntas e respostas e locais.

### <a name="security-administrator"></a>[Administrador de segurança](#security-administrator-permissions)

Os usuários com essa função têm permissões para gerenciar recursos relacionados à segurança na Central de segurança do Microsoft 365, no Azure Active Directory Identity Protection, na autenticação do Azure Active Directory, na Proteção de Informações do Azure e no Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

No | O que ele pode fazer
--- | ---
[Central de segurança do Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas a segurança em todos os serviços do Microsoft 365<br>Gerenciar alertas e ameaças de segurança<br>Exibir relatórios
Identity Protection Center | Todas as permissões da função Leitor de Segurança<br>Além disso, a habilidade de executar todas as operações do Centro de Proteção de Identidade, exceto redefinir senhas
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função Leitor de Segurança<br>**Não pode** gerenciar atribuições de função ou configurações do Azure AD
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar políticas de segurança<br>Exibir, investigar e responder a ameaças de segurança<br>Exibir relatórios
Proteção Avançada contra Ameaças do Azure | Monitorar e responder a atividades suspeitas de segurança
Windows Defender ATP e EDR | Atribuir funções<br>Gerenciar grupos de computadores<br>Configurar a detecção de ameaças do ponto de extremidade e a correção automatizada<br>Exibir, investigar e responder a alertas
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo<br>Não pode fazer alterações no Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adicionar administradores, adicionar políticas e configurações, carregar logs e executar ações de governança
[Central de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir políticas de segurança, exibir estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações
[Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365
[Bloqueio inteligente](../authentication/howto-password-smart-lockout.md) | Definir o limite e a duração de bloqueios quando ocorrerem eventos de entrada com falha.
[Proteção por senha](../authentication/concept-password-ban-bad.md) | Configurar a lista personalizada de senhas proibidas ou a proteção de senha local.

### <a name="security-operator"></a>[Operador de segurança](#security-operator-permissions)

Os usuários com essa função podem gerenciar alertas e ter acesso global somente leitura aos recursos relacionados à segurança, incluindo todas as informações da central de segurança do Microsoft 365, do Azure Active Directory, do Identity Protection, do Privileged Identity Management e do Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

No | O que ele pode fazer
--- | ---
[Central de segurança do Microsoft 365](https://protection.office.com) | Todas as permissões da função Leitor de Segurança<br>Exibir, investigar e responder a ameaças de segurança
Identity Protection Center | Todas as permissões da função Leitor de Segurança<br>Além disso, a habilidade de executar todas as operações do Centro de Proteção de Identidade, exceto redefinir senhas
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função Leitor de Segurança
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões da função Leitor de Segurança<br>Exibir, investigar e responder a alertas de segurança
Windows Defender ATP e EDR | Todas as permissões da função Leitor de Segurança<br>Exibir, investigar e responder a alertas de segurança
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Todas as permissões da função Leitor de Segurança
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Todas as permissões da função Leitor de Segurança
[Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

### <a name="security-reader"></a>[Leitor de Segurança](#security-reader-permissions)

Usuários com essa função têm acesso somente leitura global em recurso relacionado à segurança, incluindo todas as informações no centro de segurança do Microsoft 365, no Azure Active Directory, no Identity Protection e no Privileged Identity Management, bem como a capacidade de ler logs de auditoria e relatórios de entrada do Azure Active Directory e no Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

No | O que ele pode fazer
--- | ---
[Central de segurança do Microsoft 365](https://protection.office.com) | Exibir políticas relacionadas à segurança em todos os serviços do Microsoft 365<br>Exibir alertas e ameaças de segurança<br>Exibir relatórios
Identity Protection Center | Ler todos os relatórios de segurança e informações de configurações para recursos de segurança<br><ul><li>Anti-spam<li>Criptografia<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de fluxo de mensagens
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tem acesso somente leitura a todas as informações exibidas no Azure AD Privileged Identity Management: políticas e relatórios para atribuições de função e revisões de segurança do Azure AD.<br>**Não pode** se inscrever no Azure AD Privileged Identity Management nem o alterar. No portal do PIM ou por meio do PowerShell, alguém com essa função poderá ativar funções adicionais (por exemplo, administrador global ou administrador com função com privilégios) se o usuário for elegível para a elas.
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Exibir políticas de segurança<br>Exibir e investigar ameaças de segurança<br>Exibir relatórios
Windows Defender ATP e EDR | Exibir e investigar alertas. Ao ativar o controle de acesso baseado em função no Windows Defender ATP, os usuários com permissões somente leitura, como a função Leitor de segurança do Azure AD, perdem o acesso até que sejam atribuídos a uma função do Windows Defender ATP.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo. Não pode fazer alterações no Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas
[Central de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir recomendações e alertas, exibir políticas de segurança, exibir estados de segurança, mas não pode fazer alterações
[Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

### <a name="service-support-administrator"></a>[Administrador de serviços](#service-support-administrator-permissions)

Usuários com essa função podem abrir solicitações de suporte com a Microsoft para serviços do Azure e do Office 365, e exibir o painel de serviço e o centro de mensagens no [portal do Azure](https://portal.azure.com) e no [centro de administração do Microsoft 365](https://admin.microsoft.com). Obtenha mais informações em [Sobre funções de administrador](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Anteriormente, essa função era chamada de “Administrador de serviços” no [portal do Azure](https://portal.azure.com) e no [centro de administração do Microsoft 365](https://admin.microsoft.com). Nós alteramos o nome para “Administrador de serviços” para um alinhamento com o nome existente na API do Microsoft Graph, na API do Graph do Azure AD e no PowerShell do Azure AD.

### <a name="sharepoint-administrator"></a>[Administrador do SharePoint](#sharepoint-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Obtenha mais informações em [Sobre funções de administrador](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Na API do Microsoft Graph e no PowerShell do Azure AD, essa função é identificada como “Administrador de Serviços do SharePoint”. É "Administrador do SharePoint" no [portal do Azure](https://portal.azure.com).

> [!NOTE]
> Essa função também concede permissões de escopo para a API de Microsoft Graph para Microsoft Intune, permitindo o gerenciamento e a configuração de políticas relacionadas ao SharePoint e aos recursos do OneDrive.

### <a name="skype-for-business--lync-administrator"></a>[Administrador do Lync/Skype for Business](#lync-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft Skype for Business, quando o serviço está presente, além de gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço, além de acessar o centro de administração do Skype for Business e do Teams. A conta também deve ser licenciada para o Teams ou não poderá executar os cmdlets do PowerShell do Teams. Mais informações em [Sobre a função de administrador do Skype for Business](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e informações de licenciamento do Teams em [licenciamento de complemento do Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Na API do Microsoft Graph e no PowerShell do Azure AD, essa função é identificada como “Administrador de Serviços do Lync”. É "Administrador do Skype for Business" no [portal do Azure](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Administrador de Comunicações do Teams](#teams-communications-administrator-permissions)

Usuários nessa função podem gerenciar aspectos da carga de trabalho do Microsoft Teams relacionados a voz e telefonia. Isso inclui as ferramentas de gerenciamento para atribuição de número de telefone, políticas de reuniões e voz e acesso completo ao conjunto de ferramentas de análise de chamada.

### <a name="teams-communications-support-engineer"></a>[Engenheiro de Suporte de Comunicações do Teams](#teams-communications-support-engineer-permissions)

Usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams e Skype for Business usando as ferramentas de solução de problemas de chamada de usuário no centro de administração do Microsoft Teams e Skype for Business. Os usuários nesta função podem exibir informações do registro de chamadas completas para todos os participantes envolvidos. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="teams-communications-support-specialist"></a>[Especialista de Suporte de Comunicações do Teams](#teams-communications-support-specialist-permissions)

Usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams e Skype for Business usando as ferramentas de solução de problemas de chamada de usuário no centro de administração do Microsoft Teams e Skype for Business. Os usuários nessa função só podem exibir detalhes do usuário na chamada para o usuário específico que eles pesquisaram. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="teams-service-administrator"></a>[Administrador de Serviços do Teams](#teams-service-administrator-permissions)

Usuários nessa função podem gerenciar todos os aspectos da carga de trabalho do Microsoft Teams pelo centro de administração do Microsoft Teams e Skype for Business e respectivos módulos do PowerShell. Isso inclui, entre outras áreas, todas as ferramentas de gerenciamento relacionadas a telefonia, mensagens, reuniões e às próprias equipes. Além disso, essa função concede a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço.

### <a name="user-administrator"></a>[Administrador de usuários](#user-administrator-permissions)

Os usuários com essa função podem criar usuários e gerenciar todos os aspectos de usuários com algumas restrições (consulte a tabela) e podem atualizar as políticas de expiração de senha. Além disso, os usuários com essa função podem criar e gerenciar todos os grupos. Essa função também inclui a capacidade de criar e gerenciar exibições de usuários, gerenciar tickets de suporte e monitorar a integridade do serviço. Os administradores de usuários não têm permissão para gerenciar algumas propriedades de usuários na maioria das funções de administrador. O usuário com essa função não tem permissões para gerenciar a MFA. As funções que são exceções a essa restrição estão listadas na tabela a seguir.

| **Permissão** | **O que ele pode fazer** |
| --- | --- |
|Permissões gerais|<p>Criar usuários e grupos</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Gerenciar tíquetes de suporte do Office<p>Atualizar políticas de expiração de senha|
| <p>Em todos os usuários, inclusive todos os administradores</p>|<p>Gerenciar licenças</p><p>Gerenciar todas as propriedades de usuário, exceto o nome Principal do usuário</p>
| Somente em usuários não administradores ou em qualquer um destes procedimentos limitada de funções de administrador:<ul><li>Leitores de Diretório<li>Emissor do Convite ao Convidado<li>Administrador de assistência técnica<li>Leitor do Centro de Mensagens<li>Leitor de Relatórios<li>Administrador de usuários|<p>Excluir e restauração</p><p>Desativar e ativar</p><p>Invalidar Tokens de atualização</p><p>Gerenciar todas as propriedades de usuário, incluindo o nome Principal do usuário</p><p>Redefinir senha</p><p>Atualizar chaves de dispositivo FIDO)</p>|

> [!IMPORTANT]
> Usuários com essa função podem alterar senhas de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração da senha de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
>
>- Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar, não concedida a Administradores de Usuário. Por esse caminho, um Administrador de Usuário pode ser capaz de assumir a identidade de um proprietário de aplicativo e, depois, assumir a identidade de um aplicativo com privilégios, atualizando as credenciais do aplicativo.
>- Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configurações críticas no Azure.
>- Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
>- Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
>- Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

## <a name="role-permissions"></a>Permissões de Função

As tabelas a seguir descrevem as permissões específicas no Azure Active Directory fornecidas a cada função. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Active Directory.

### <a name="application-administrator-permissions"></a>Permissões do Administrador de aplicativos

Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Atualize as propriedades de autenticação do proxy de aplicativo nas entidades de serviço no Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Atualize as URLs internas e externas do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Leia todas as propriedades do proxy de aplicativo. |
| microsoft.directory/applications/applicationProxy/update | Atualize todas as propriedades do proxy de aplicativo. |
| microsoft.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/read | Leia appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/read | Leia as propriedades do grupo de conectores do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/update | Atualize todas as propriedades do grupo de conectores do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/connectorGroups/create | Crie grupos de conectores do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/connectorGroups/delete | Exclua grupos de conectores do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/connectors/everything/read | Leia todas as propriedades do conector do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/connectors/create | Crie conectores do proxy de aplicativo no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/applicationConfiguration/delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="application-developer-permissions"></a>Permissões do Desenvolvedor de aplicativos

Pode criar registros de aplicativos independentemente da configuração “Usuários podem registrar aplicativos”.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Crie aplicativos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/appRoleAssignments/createAsOwner | Crie appRoleAssignments no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Crie oAuth2PermissionGrants no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/servicePrincipals/createAsOwner | Criar servicePrincipals em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |

### <a name="authentication-administrator-permissions"></a>Permissões do Administrador de autenticação

Permitido para exibir, definir e redefinir as informações de método de autenticação para qualquer usuário não administrador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Atualize propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.directory/users/password/update | Atualizar senhas de todos os usuários na organização do Office 365. Consulte a documentação online para obter mais detalhes. |

### <a name="azure-devops-administrator-permissions"></a>Permissões de Administrador do Azure DevOps

Pode gerenciar a política e as configurações da organização do Azure DevOps.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a [descrição da função](#azure-devops-administrator) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Leia e configure o Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Permissões do Administrador da Proteção de Informações do Azure

Pode gerenciar todos os aspectos do serviço de Proteção de Informações do Azure.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a [descrição da função](#) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Permissões do Administrador de Conjunto de Chaves do IEF B2C

Gerencie segredos para federação e criptografia no Identity Experience Framework.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Ler e configurar conjuntos de chaves no Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Permissões do Administrador de Políticas do IEF B2C

Criar e gerenciar políticas de estrutura confiável no Identity Experience Framework.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Ler e configurar políticas personalizadas no Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Permissões de Administrador de cobrança

Pode executar tarefas comuns de relacionadas à cobrança, como atualizar informações de pagamento.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/organization/basic/update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerenciar todos os aspectos de cobrança. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Permissões do Administrador de Aplicativos de Nuvem

Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais, exceto o Proxy de Aplicativo.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Permissões do Administrador de Dispositivo de Nuvem

Acesso completo para gerenciar os dispositivos no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/devices/delete | Exclua dispositivos no Azure Active Directory. |
| microsoft.directory/devices/disable | Desabilite dispositivos no Azure Active Directory. |
| microsoft.directory/devices/enable | Habilite dispositivos no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="company-administrator-permissions"></a>Permissões do Administrador de Empresa

Pode gerenciar todos os aspectos do Azure AD e dos serviços da Microsoft que usam identidades do Azure AD. Essa função também é conhecida como função de Administrador global. 

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar as propriedades padrão em microsoft.aad.cloudAppSecurity. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Criar e excluir administrativeUnits e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/applications/allProperties/allTasks | Criar e excluir aplicativos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Criar e excluir appRoleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/contacts/allProperties/allTasks | Criar e excluir contatos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/contracts/allProperties/allTasks | Criar e excluir contratos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Criar e excluir dispositivos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Criar e excluir DirectoryRoles, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Criar e excluir DirectoryRoleTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/domains/allProperties/allTasks | Criar e excluir Domínios, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/groups/allProperties/allTasks | Criar e excluir Grupos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/groupSettings/allProperties/allTasks | Criar e excluir groupSettings e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Criar e excluir groupSettingTemplates e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Criar e excluir loginTenantBranding e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Criar e excluir oAuth2PermissionGrants e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Criar e excluir organização e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/policies/allProperties/allTasks | Criar e excluir políticas, ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/roleAssignments/allProperties/allTasks | Criar e excluir roleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Criar e excluir roleDefinitions e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Criar e excluir scopedRoleMemberships e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/serviceAction/activateService | Pode executar a ação de serviço Activateservice no Azure Active Directory |
| microsoft.directory/serviceAction/disableDirectoryFeature | Pode executar a ação de serviço Disabledirectoryfeature no Azure Active Directory |
| microsoft.directory/serviceAction/enableDirectoryFeature | Pode executar a ação de serviço Enabledirectoryfeature no Azure Active Directory |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | Pode executar a ação de serviço Getavailableextentionproperties no Azure Active Directory |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Criar e excluir servicePrincipals e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Criar e excluir subscribedSkus e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/users/allProperties/allTasks | Criar e excluir usuários e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Ler todos os recursos em microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerenciar todos os aspectos de cobrança. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerenciar todos os aspectos da Análise de Área de Trabalho. |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Ler securityMessages em microsoft.office365.messageCenter. |
| Microsoft.office365.protectionCenter/allEntities/allTasks | Gerencie todos os aspectos do Centro de proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar as propriedades padrão em microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos do Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Ler todos os recursos em microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Permissões do Administrador de conformidade

Pode ler e gerenciar a configuração de conformidade e relatórios no Azure AD e no Office 365.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="compliance-data-administrator-permissions"></a>Permissões do Administrador de dados de conformidade

Cria e gerencia conteúdo de conformidade.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. Directory. cloudAppSecurity/myentities/tarefas | Ler e configurar o Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="conditional-access-administrator-permissions"></a>Permissões do Administrador de acesso condicional

Pode gerenciar as funcionalidades de acesso condicional.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/conditionalAccess/delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Permissões do Administrador de serviços de CRM

Pode gerenciar todos os aspectos do produto Dynamics 365.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Permissões do Aprovador de acesso do sistema de proteção de dados do cliente

Pode aprovar solicitações de suporte da Microsoft para acessar dados organizacionais do cliente.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |

### <a name="desktop-analytics-administrator-permissions"></a>Permissões do Administrador de Análise de Área de Trabalho

Pode gerenciar a Análise de Área de Trabalho e os serviços de Personalização e Política do Office. Para a Análise de Área de Trabalho, isso inclui a capacidade de exibir o inventário de ativos, criar planos de implantação, exibir o status de integridade e de implantação. Para o serviço de Personalização e Política do Office, essa função permite que os usuários gerenciem as políticas do Office.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerenciar todos os aspectos da Análise de Área de Trabalho. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="device-administrators-permissions"></a>Permissões dos Administradores de dispositivo

Os usuários atribuídos a essa função são adicionados ao grupo Administradores local em dispositivos que ingressaram no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Permissões de Leitores de diretório
Pode ler informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Ler as propriedades básicas em administrativeUnits no Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read | Ler a propriedade Administrativeunits no Azure Active Directory. |
| microsoft.directory/applications/basic/read | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| microsoft.directory/applications/owners/read | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.directory/applications/policies/read | Leia a propriedade applications.policies no Active Directory do Azure. |
| microsoft.directory/contacts/basic/read | Ler as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read | Ler a propriedade Contacts no Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Ler as propriedades básicas sobre os contratos no Azure Active Directory. |
| microsoft.directory/devices/basic/read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.directory/devices/memberOf/read | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read | Ler a propriedade Eligiblemembers no Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read | Ler a propriedade Directoryroles no Azure Active Directory. |
| microsoft.directory/domains/basic/read | Leia as propriedades básicas em domínios no Active Directory do Azure. |
| microsoft.directory/groups/appRoleAssignments/read | Leia a propriedade groups.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/groups/basic/read | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.directory/groups/memberOf/read | Leia a propriedade groups.memberOf no Active Directory do Azure. |
| microsoft.directory/groups/members/read | Leia a propriedade groups.members no Azure Active Directory. |
| microsoft.directory/groups/owners/read | Leia a propriedade groups.owners no Active Directory do Azure. |
| microsoft.directory/groups/settings/read | Leia a propriedade groups.settings no Active Directory do Azure. |
| microsoft.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Leia as propriedades básicas em oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/organization/basic/read | Leia as propriedades básicas da organização no Active Directory do Azure. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade organization.trustedCAsForPasswordlessAuth no Active Directory do Azure. |
| microsoft.directory/roleAssignments/basic/read | Leia as propriedades básicas em roleAssignments no Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read | Leia as propriedades básicas em roleDefinitions no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/users/basic/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

### <a name="directory-synchronization-accounts-permissions"></a>Permissões de Contas de sincronização de diretório

Apenas usado pelo serviço do Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Atualize a propriedade organization.dirSync no Azure Active Directory. |
| microsoft.directory/policies/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/basic/read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.directory/policies/owners/read | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.directory/policies/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/policies/policiesAppliedTo/read | Ler a propriedade policies.policiesAppliedTo no Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Atualizar a propriedade policies.tenantDefault no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Permissões de Gravadores de diretório

Pode ler e gravar informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groupSettings/basic/update | Atualize as propriedades básicas em groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettings/create | Crie groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettings/delete | Exclua groupSettings no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Permissões do Administrador de serviços do Exchange

Pode gerenciar todos os aspectos do produto Exchange.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Ler as páginas de desempenho de rede no Centro de administração do M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="external-id-user-flow-administrator-permissions"></a>Permissões do Administrador de fluxo do usuário de ID Externa

Crie e gerencie todos os aspectos de fluxos dos usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Ler e configurar fluxos dos usuários no Azure Active Directory B2C. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>Permissões do Administrador de atributo de fluxo do usuário de ID Externa

Crie e gerencie o esquema de atributo disponível para todos os fluxos dos usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Ler e configurar atributos de usuário no Azure Active Directory B2C. |

### <a name="external-identity-provider-administrator-permissions"></a>Permissões do Administrador do provedor de identidade externa

Configure provedores de identidade para usar na federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Ler e configurar provedores de identidade no Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Permissões do Leitor global
Pode ler tudo o que um Administrador global pode, mas não pode editar nada.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a [descrição da função](#global-reader) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Ler todos os aspectos de cobrança. |
| microsoft.directory/administrativeUnits/basic/read    | Ler as propriedades básicas em administrativeUnits no Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read    | Ler a propriedade Administrativeunits no Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| microsoft.directory/applications/owners/read    | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.directory/applications/policies/read    | Leia a propriedade applications.policies no Active Directory do Azure. |
| microsoft.directory/contacts/basic/read    | Ler as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read    | Ler a propriedade Contacts no Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Ler as propriedades básicas sobre os contratos no Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.directory/devices/memberOf/read    | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read    | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read    | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read    | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Ler a propriedade Eligiblemembers no Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read    | Ler a propriedade Directoryroles no Azure Active Directory. |
| microsoft.directory/domains/basic/read    | Leia as propriedades básicas em domínios no Active Directory do Azure. |
| microsoft.directory/groups/appRoleAssignments/read    | Leia a propriedade groups.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/groups/basic/read    | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.directory/groups/hiddenMembers/read    | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/memberOf/read    | Leia a propriedade groups.memberOf no Active Directory do Azure. |
| microsoft.directory/groups/members/read    | Leia a propriedade groups.members no Azure Active Directory. |
| microsoft.directory/groups/owners/read    | Leia a propriedade groups.owners no Active Directory do Azure. |
| microsoft.directory/groups/settings/read    | Leia a propriedade groups.settings no Active Directory do Azure. |
| microsoft.directory/groupSettings/basic/read    | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read    | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Leia as propriedades básicas em oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/organization/basic/read    | Leia as propriedades básicas da organização no Active Directory do Azure. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Leia a propriedade organization.trustedCAsForPasswordlessAuth no Active Directory do Azure. |
| microsoft.directory/policies/standard/read    | Ler políticas padrão no Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read    | Leia as propriedades básicas em roleAssignments no Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read    | Leia as propriedades básicas em roleDefinitions no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read    | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read    | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read    | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read    | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read    | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read    | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/users/basic/read    | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.directory/users/directReports/read    | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.directory/users/manager/read    | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.directory/users/memberOf/read    | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/users/ownedDevices/read    | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.directory/users/ownedObjects/read    | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.directory/users/registeredDevices/read    | Leia a propriedade users.registeredDevices no Active Directory do Azure. |
| microsoft.directory/users/strongAuthentication/read    | Ler propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.office365.exchange/allEntities/read    | Ler todos os aspectos do Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Ler securityMessages em microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Ler as páginas de desempenho de rede no Centro de administração do M365. |
| Microsoft.office365.protectionCenter/allEntities/Read    | Ler todos os aspectos do Centro de Proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Ler as todas as propriedades padrão em microsoft.office365.securityComplianceCenter. |
| Microsoft.office365.usageReports/allEntities/Read    | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Ler as propriedades padrão em todos os recursos em microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Permissões do Administrador de grupos
Pode gerenciar todos os aspectos de grupos e as configurações de grupo, como políticas de nomenclatura e de expiração.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/basic/read | Leia as propriedades padrão em Grupos no Azure Active Directory.  |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="guest-inviter-permissions"></a>Permissões do Emissor do convite do convidado
Pode convidar usuários convidados independentemente da configuração “membros podem convidar pessoas”.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/users/basic/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.directory/users/inviteGuest | Convidar usuários convidados no Azure Active Directory. |
| microsoft.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

### <a name="helpdesk-administrator-permissions"></a>Permissões do Administrador de assistência técnica

Pode redefinir senhas para não administradores e Administradores de Assistência Técnica.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="hybrid-identity-administrator-permissions"></a>Permissões do Administrador de identidade híbrida

Habilite, implante, configure, gerencie, monitore e solucione problemas de provisionamento de nuvem e serviços de autenticação. 

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.directory/applications/audience/update  | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory.  |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/applicationTemplates/instantiate | Criar uma instância de aplicativos de galeria por meio de modelos de aplicativo. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Ler e configurar todas as propriedades do serviço de Provisionamento em Nuvem do Azure AD. |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | Gerenciar todos os aspectos do ADFS (Serviços de federação do Active Directory) ou do provedor de federação de terceiros no Azure AD. |
| microsoft.directory/organization/dirSync/update | Atualize a propriedade organization.dirSync no Azure Active Directory. |
| microsoft.directory/passwordHashSync/allProperties/allTasks | Gerenciar todos os aspectos de PHS (Sincronização de hash da senha) no Azure AD. |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | Gerenciar todos os aspectos da PTA (Autenticação de passagem) no Azure AD. |
| microsoft.directory/seamlessSSO/allProperties/allTasks | Gerenciar todos os aspectos de SSO (Logon único) contínuo no Azure AD. |
| microsoft.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Gerenciar todos os aspectos dos trabalhos de sincronização no Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Gerenciar todos os aspectos do esquema de sincronização no Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gerenciar todos os aspectos de credenciais de sincronização no Azure AD. |
| microsoft.directory/servicePrincipals/tag/update | Atualizar a propriedade servicePrincipals.tag no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |


### <a name="intune-service-administrator-permissions"></a>Permissões do Administrador de serviços do Intune

Pode gerenciar todos os aspectos do produto Intune.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contacts/delete | Exclua contatos no Azure Active Directory. |
| microsoft.directory/devices/basic/update | Atualize as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/devices/create | Crie dispositivos no Azure Active Directory. |
| microsoft.directory/devices/delete | Exclua dispositivos no Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/update | Atualize a propriedade registeredowners no Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/update | Atualize a propriedade registeredusers no Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Permissões do Administrador do Kaizala

Pode gerenciar as configurações do Microsoft Kaizala.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler o Centro de administração do Office 365. |

### <a name="license-administrator-permissions"></a>Permissões do Administrador de licenças

Pode gerenciar licenças de produto em usuários e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/usageLocation/update | Atualizar a propriedade users.usageLocation no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="lync-service-administrator-permissions"></a>Permissões do Administrador de serviços do Lync

Pode gerenciar todos os aspectos do produto Skype for Business.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read    | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Permissões do Leitor de Privacidade do Centro de Mensagens

Pode ler postagens do Centro de Mensagens, mensagens de privacidade de dados, grupos, domínios e assinaturas.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Ler securityMessages em microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Permissões do Leitor do Centro de Mensagens
Pode ler as mensagens e as atualizações para sua organização somente no Centro de Mensagens do Office 365. 

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |

### <a name="modern-commerce-administrator-permissions"></a>Permissões do Administrador de Comércio Moderno
Pode gerenciar compras comerciais de uma empresa, um departamento ou uma equipe. 

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.commerce.billing/partners/read | Ler a propriedade de parceiros da cobrança do O365. |
| microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Gerenciar todos os aspectos do Centro de Serviços de Licenciamento por Volume. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e exiba os próprios tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |


### <a name="network-administrator-permissions"></a>Permissões do Administrador de rede
Pode gerenciar os locais de rede e examinar insights sobre o design da rede empresarial de aplicativos de Software como Serviço do Microsoft 365.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Ler as páginas de desempenho de rede no Centro de administração do M365.  |
| microsoft.office365.network/locations/allProperties/allTasks | Ler e configurar as propriedades dos locais de rede para cada local. |

### <a name="office-apps-administrator-permissions"></a>Permissões do Administrador de aplicativos do Office
Pode gerenciar os serviços de nuvem dos aplicativos do Office, incluindo o gerenciamento de políticas e configurações e o gerenciamento da capacidade de selecionar, anular a seleção e publicar o conteúdo do recurso “novidades” nos dispositivos do usuário final.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.userCommunication/allEntities/allTasks | Ler e atualizar a visibilidade das mensagens sobre novidades. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Permissões do Suporte de nível 1 ao parceiro

Não use – não se destina para uso geral.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contacts/delete | Exclua contatos no Azure Active Directory. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/delete | Exclua usuários no Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="partner-tier2-support-permissions"></a>Permissões do Suporte de nível 2 ao parceiro

Não use – não se destina para uso geral.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contacts/delete | Exclua contatos no Azure Active Directory. |
| microsoft.directory/domains/allTasks | Criar e excluir domínios e ler e atualizar propriedades padrão no Azure Active Directory. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/organization/basic/update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/delete | Exclua usuários no Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="password-administrator-permissions"></a>Permissões do Administrador de senha

Pode redefinir senhas para não administradores e administradores de Senha.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Permissões do Administrador de serviços do Power BI

Pode gerenciar todos os aspectos do produto Power BI.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos do Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |


### <a name="power-platform-administrator-permissions"></a>Permissões do Administrador de serviços do Power Platform

Pode criar e gerenciar todos os aspectos do Microsoft Dynamics 365, do PowerApps e do Microsoft Flow. 

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.flow/allEntities/allTasks | Gerenciar todos os aspectos do Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Gerenciar todos os aspectos do PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="printer-administrator-permissions"></a>Permissões do Administrador de impressora

Pode gerenciar todos os aspectos das impressoras e dos conectores de impressoras.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | Criar e excluir impressoras e conectores, além de ler e atualizar todas as propriedades na Impressão Microsoft. |

### <a name="printer-technician-permissions"></a>Permissões do Técnico de impressora

Pode registrar e cancelar o registro de impressoras e atualizar o status da impressora.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Ler todas as propriedades dos conectores na Impressão Microsoft. |
| microsoft.azure.print/printers/allProperties/read | Ler todas as propriedades das impressoras na Impressão Microsoft. |
| microsoft.azure.print/printers/basic/update | Atualizar as propriedades básicas das impressoras na Impressão Microsoft. |
| microsoft.azure.print/printers/register | Registrar impressoras na Impressão Microsoft. |
| microsoft.azure.print/printers/unregister | Cancelar o registro de impressoras na Impressão Microsoft. |

### <a name="privileged-authentication-administrator-permissions"></a>Permissões do Administrador de autenticação privilegiada

Tem permissão para exibir, definir e redefinir as informações de método de autenticação para qualquer usuário (administrador ou não administrador).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Atualize propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.directory/users/password/update | Atualizar senhas de todos os usuários na organização do Office 365. Consulte a documentação online para obter mais detalhes. |

### <a name="privileged-role-administrator-permissions"></a>Permissões do Administrador de funções com privilégios

Pode gerenciar atribuições de função do Azure AD e todos os aspectos do Privileged Identity Management.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Ler e configurar a propriedade servicePrincipals.appRoleAssignedTo no Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Ler e configurar a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Criar e gerenciar unidades administrativas (incluindo membros) |
| microsoft.directory/roleAssignments/allProperties/allTasks | Criar e gerenciar atribuições de função. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Criar e gerenciar definições de função. |

### <a name="reports-reader-permissions"></a>Permissões do Leitor de relatórios

Pode ler relatórios de entrada e de auditoria.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="search-administrator-permissions"></a>Permissões do Administrador de pesquisa

Pode criar e gerenciar todos os aspectos das configurações da Pesquisa da Microsoft.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Criar e excluir todos os recursos, além de ler e atualizar todas as propriedades em microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Permissões do Editor de pesquisa

Pode criar e gerenciar o conteúdo editorial, como bookmarks, P e R, localizações, planta baixa.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Criar e excluir o conteúdo, além de ler e atualizar todas as propriedades em microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Permissões do Administrador de segurança

Pode ler relatórios e informações de segurança e gerenciar a configuração no Azure AD e o Office 365.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.directory/policies/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Atualizar a propriedade policies.tenantDefault no Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| Microsoft.office365.protectionCenter/allEntities/Update | Atualize todos os recursos em microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="security-operator-permissions"></a>Permissões do Operador de segurança

Cria e gerencia eventos de segurança.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Ler e configurar o Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Ler e configurar a Proteção Avançada contra Ameaças do Azure AD. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Ler e configurar o Centro de Conformidade e Segurança. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Ler e configurar a Proteção Avançada contra Ameaças do Windows Defender. |

### <a name="security-reader-permissions"></a>Permissões do Leitor de segurança

Pode ler relatórios e informações de segurança no Azure AD e no Office 365.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="service-support-administrator-permissions"></a>Permissões do Administrador de serviços

Pode ler informações de integridade do serviço e gerenciar os tíquetes de suporte.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Permissões do Administrador de serviços do SharePoint

Pode gerenciar todos os aspectos do serviço SharePoint.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| microsoft.office365.network/performance/allProperties/read | Ler as páginas de desempenho de rede no Centro de administração do M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read    | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Permissões do Administrador de Comunicações do Teams

Pode gerenciar recursos de reuniões e chamadas no serviço do Microsoft Teams.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Permissões do Engenheiro de Suporte de Comunicações do Teams

Pode solucionar problemas de comunicação no Teams usando ferramentas avançadas.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="teams-communications-support-specialist-permissions"></a>Permissões do Especialista de Suporte de Comunicações do Teams

Pode solucionar problemas de comunicação no Teams equipes usando ferramentas básicas.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="teams-service-administrator-permissions"></a>Permissões do Administrador de serviços do Teams

Pode gerenciar o serviço do Microsoft Teams.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| microsoft.office365.network/performance/allProperties/read | Ler as páginas de desempenho de rede no Centro de administração do M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="user-administrator-permissions"></a>Permissões do Administrador de usuários
Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contacts/delete | Exclua contatos no Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/groups/settings/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/create | Crie usuários no Active Directory do Azure. |
| microsoft.directory/users/delete | Exclua usuários no Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="role-template-ids"></a>IDs do modelo de função

As IDs do modelo de função são usadas principalmente pela API do Microsoft Graph ou por usuários do PowerShell.

displayName do Graph | Nome de exibição do portal do Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador de aplicativos | Administrador de aplicativos | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Desenvolvedor de aplicativos | Desenvolvedor de aplicativos | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de Autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrador do Azure DevOps | Administrador do Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Administrador da Proteção de Informações do Azure | Administrador da Proteção de Informações do Azure | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador de Conjunto de Chaves do IEF B2C | Administrador de Conjunto de Chaves do IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de Política do IEF B2C | Administrador de Política do IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de cobrança | Administrador de cobrança | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de Aplicativos de Nuvem | Administrador de aplicativos de nuvem | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de Dispositivo de Nuvem | Administrador de dispositivo em nuvem | 7698a772-787b-4ac8-901f-60d6b08affd2
Administradores de Empresa | Administrador global | 62e90394-69f5-4237-9190-012177145e10
Administrador de conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de Dados de Conformidade | Administrador de dados de conformidade | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrador de acesso condicional | Administrador de acesso condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador de serviços do CRM | Administrador do Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Aprovador de acesso do cofre do cliente | Aprovador de acesso do Sistema de Proteção de Dados do Cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrador de Análise de Área de Trabalho | Administrador de Análise de Área de Trabalho | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administradores de Dispositivo | Administradores de dispositivo | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Ingresso de Dispositivo | Preterido | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gerenciadores de Dispositivo | Preterido | 2b499bcd-da44-4968-8aec-78e1674fa64d
Usuários de Dispositivo | Preterido | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de Diretório | Leitores de diretórios | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Contas de sincronização de diretório | Não exibido porque não deve ser usado | d29b2b05-8046-44ba-8758-1e26182fcf32
Gravadores de diretório | Não exibido porque não deve ser usado | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de serviços do Exchange | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador de Fluxo do Usuário de ID Externa | Administrador de Fluxo do Usuário de ID Externa | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de Atributo de fluxo do usuário de ID Externa | Administrador de Atributo de fluxo do usuário de ID Externa | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador do Provedor de Identidade Externa | Administrador do Provedor de Identidade Externa | be2f45a1-457d-42af-a067-6ec1fa63bc45
Leitor Global | Leitor global | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Administrador de Grupos | Administrador de grupos | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Emissor do Convite ao Convidado | Emissor do convite ao convidado | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador de assistência técnica | Administrador de assistência técnica | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de Identidade Híbrida | Administrador de identidade híbrida | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Administrador de serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador do Kaizala | Administrador do Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de Licenças | Administrador de licenças | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador de serviços do Lync | Administrador do Skype for Business | 75941009-915a-4869-abe7-691bff18279e
Leitor de Privacidade do Centro de Mensagens | Leitor de privacidade do centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Leitor do Centro de Mensagens | Leitor do Centro de Mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Administrador de Comércio Moderno | Administrador de Comércio Moderno | d24aef57-1500-4070-84db-2666f29cf966
Administrador de Rede | Administrador de rede | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Administrador de Aplicativos do Office | Administrador de aplicativos do Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Suporte de camada 1 do parceiro | Não exibido porque não deve ser usado | 4ba39ca4-527c-499a-b93d-d9b492c50246
Suporte de camada 2 do parceiro | Não exibido porque não deve ser usado | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador de senha | Administrador de senha | 966707d0-3269-4727-9be2-8c3a10f19b9d
Administrador de serviços do Power BI | Administrador do Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador do Power Platform | Administrador do Power Platform | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Administrador de Impressora | Administrador de impressora | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Técnico de Impressora | Técnico de impressora | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Administrador de Autenticação Privilegiada | Administrador de autenticação privilegiada | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador de função com privilégios | Administrador de função com privilégios | e8611ab8-c189-46e8-94e1-60213ab1f814
Leitor de Relatórios | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de Pesquisas | Administrador de pesquisas | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de Pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operador de Segurança | Operador de segurança | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Leitor de segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de suporte a serviço | Administrador de suporte a serviço | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviços do SharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de Comunicações do Teams | Administrador de Comunicações do Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de Suporte de Comunicações do Teams | Engenheiro de Suporte de Comunicações do Teams | f70938a0-fc10-4177-9e90-2178f8765737
Especialista em Suporte de Comunicações do Teams | Especialista em Suporte de Comunicações do Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de Serviços do Teams | Administrador de Serviços do Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Usuário | Não exibido porque não pode ser usado | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador da conta de usuário | Administrador de usuários | fe930be7-5e62-47db-91af-98c3a49a38b1
Ingresso no Dispositivo no Local de Trabalho | Preterido | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Eles foram preteridos e serão removidos do AD do Azure no futuro.

* Administrador de Licenças AdHoc
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Criador de Usuário Verificado por Email
* Administrador de caixa de correio
* Ingresso no Dispositivo no Local de Trabalho

## <a name="roles-not-shown-in-the-portal"></a>Funções não exibidas no portal

Nem todas as funções retornadas pelo PowerShell ou pela API do MS Graph ficam visíveis no portal do Azure. A tabela a seguir organiza essas diferenças.

Nome da API | Nome do portal do Azure | Observações
-------- | ------------------- | -------------
Administradores de Empresa | Administrador global | [Nome alterado para mais clareza](directory-assign-admin-roles.md#role-template-ids)
Administrador de serviços do CRM | Administrador do Dynamics 365 | [Reflete a identidade visual atual do produto](directory-assign-admin-roles.md#role-template-ids)
Ingresso de Dispositivo | Preterido | [Documentação de funções preteridas](directory-assign-admin-roles.md#deprecated-roles)
Gerenciadores de Dispositivo | Preterido | [Documentação de funções preteridas](directory-assign-admin-roles.md#deprecated-roles)
Usuários de Dispositivo | Preterido | [Documentação de funções preteridas](directory-assign-admin-roles.md#deprecated-roles)
Contas de sincronização de diretório | Não exibido porque não deve ser usado | [Documentação de Contas de sincronização de diretório](directory-assign-admin-roles.md#directory-synchronization-accounts)
Gravadores de diretório | Não exibido porque não deve ser usado | [Documentação dos gravadores de diretório](directory-assign-admin-roles.md#directory-writers)
Usuário Convidado | Não exibido porque não pode ser usado  | NA
Administrador de serviços do Lync | Administrador do Skype for Business | [Reflete a identidade visual atual do produto](directory-assign-admin-roles.md#role-template-ids)
Suporte ao parceiro de Nível 1 | Não exibido porque não deve ser usado | [Documentação do Suporte de nível 1 ao parceiro](directory-assign-admin-roles.md#partner-tier1-support)
Suporte ao parceiro de Nível 2 | Não exibido porque não deve ser usado | [Documentação do Suporte de nível 2 ao parceiro](directory-assign-admin-roles.md#partner-tier2-support)
Usuário convidado restrito | Não exibido porque não pode ser usado | NA
Usuário | Não exibido porque não pode ser usado | NA
Ingresso no Dispositivo no Local de Trabalho | Preterido | [Documentação de funções preteridas](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir um usuário como um administrador de uma assinatura do Azure, veja [Gerenciar o acesso usando funções do Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, consulte [Noções básicas sobre funções diferentes](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter detalhes sobre a relação entre assinaturas e um locatário do Azure AD, ou para obter instruções para associar ou adicionar uma assinatura, consulte [associar ou adicionar uma assinatura do Azure ao seu locatário de Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
