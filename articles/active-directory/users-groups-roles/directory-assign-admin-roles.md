---
title: Descrições e permissões de função de administrador - Azure AD | Microsoft Docs
description: Uma função de administrador pode adicionar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/07/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: e097173712693754baab99912301c98ee336f64f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877908"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Permissões da função de administrador no Azure Active Directory

Usando o Azure Active Directory (Azure AD), você pode designar administradores limitados para gerenciar tarefas de identidade em funções menos privilegiadas. Os administradores podem ser designados para fins como adicionar ou alterar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário e gerenciar nomes de domínio. As permissões de usuário padrão podem ser alteradas somente nas configurações do usuário no Azure AD.

## <a name="limit-use-of-global-administrator"></a>Limitar o uso do administrador global

Os usuários que são atribuídos à função de administrador Global podem ler e modificar todas as configurações administrativas da sua organização Azure AD. Por padrão, a pessoa que se inscreve para uma assinatura do Azure é atribuída à função de administrador Global para a organização Azure AD. Somente administradores globais e administradores de funções privilegiadas podem delegar funções de administrador. Para reduzir o risco para o seu negócio, recomendamos que você atribua essa função ao menor número possível de pessoas em sua organização.

Como uma prática recomendada, recomendamos que você atribua esse papel a menos de cinco pessoas em sua organização. Se você tem mais de cinco administradores atribuídos ao papel de Administrador Global em sua organização, aqui estão algumas maneiras de reduzir seu uso.

### <a name="find-the-role-you-need"></a>Encontre o papel que você precisa

Se for frustrante para você encontrar o papel que você precisa fora de uma lista de muitos papéis, o Azure AD pode mostrar-lhe subconjuntos dos papéis com base nas categorias de papéis. Confira nosso novo filtro **tipo** para [funções e administradores do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) para mostrar apenas as funções no tipo selecionado.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Existe um papel agora que não existia quando você atribuiu a função de administrador Global

É possível que uma função ou funções tenham sido adicionadas ao Azure AD que forneça mpermissões mais granulares que não eram uma opção quando você elevou alguns usuários para administrador Global. Com o tempo, estamos implementando funções adicionais que realizam tarefas que só a função de administrador Global poderia fazer antes. Você pode ver isso refletido nas [seguintes funções disponíveis](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Exibir e atribuir funções de administrador no Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

### <a name="application-administrator"></a>[Administrador de aplicativos](#application-administrator-permissions)

Os usuários nessa função podem criar e gerenciar todos os aspectos de aplicativos empresariais, registros dos aplicativos e configurações de proxy de aplicativos. Observe que os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativos ou aplicativos corporativos.

Os administradores de aplicativos podem gerenciar credenciais de aplicativos que lhes permitem se passar pelo aplicativo. Assim, os usuários atribuídos a essa função podem gerenciar as credenciais de aplicativos apenas daqueles aplicativos que não são atribuídos a quaisquer funções AD do Azure ou aqueles atribuídos apenas às seguintes funções de administrador:
* Administrador de aplicativos
* Desenvolvedor de aplicativos
* Administrador de Aplicativos de Nuvem
* Leitores de Diretório

Se um aplicativo for atribuído a qualquer outra função que não seja mencionada acima, então o administrador do aplicativo não poderá gerenciar credenciais desse aplicativo. 
 
Essa função também concede a capacidade de _consentir com_ permissões delegadas e permissões de aplicativos, com exceção das permissões na API do Microsoft Graph.

> [!IMPORTANT]
> Essa exceção significa que você ainda pode consentir com permissões para _outros_ aplicativos (por exemplo, aplicativos de terceiros ou aplicativos que você registrou), mas não para permissões no próprio Azure AD. Você ainda pode _solicitar_ essas permissões como parte do registro do aplicativo, mas _conceder_ (ou seja, consentir) essas permissões requer um administrador ad azure. Isso significa que um usuário mal-intencionado não pode facilmente elevar suas permissões, por exemplo, criando e consentindo com um aplicativo que pode escrever para todo o diretório e através das permissões desse aplicativo se elevam para se tornar um admin global.

### <a name="application-developer"></a>[Desenvolvedor de aplicativos](#application-developer-permissions)

Os usuários nessa função podem criar registros dos aplicativos quando a configuração "Usuários podem registrar aplicativos" estiver definida como Não. Essa função também concede permissão para consentimento em nome de alguém quando a configuração "Os usuários podem consentir com aplicativos que acessam dados da empresa em seu nome" é definida como No. Os usuários atribuídos a essa função são adicionados como proprietários ao criar novos registros de aplicativos ou aplicativos corporativos.

### <a name="authentication-administrator"></a>[Administrador de autenticação](#authentication-administrator-permissions)

A função administrador de autenticação está atualmente em visualização pública. Os usuários com essa função podem definir ou redefinir credenciais não-senha e podem atualizar senhas para todos os usuários. Autenticação Os administradores podem exigir que os usuários se registrem novamente contra a credencial não-senha existente (por exemplo, MFA ou FIDO) e **revoguem o MFA no dispositivo,** que solicita ao MFA o próximo login de usuários que não são administradores ou atribuíram apenas as seguintes funções:

* Administrador de Autenticação
* Leitores de Diretório
* Emissor do Convite ao Convidado
* Leitor do Centro de Mensagens
* Leitor de Relatórios

> [!IMPORTANT]
> Usuários com essa função podem alterar credenciais de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração das credenciais de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
>
>- Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar que não foram concedidas a Administradores de Autenticação. Através desse caminho, um administrador de autenticação pode ser capaz de assumir a identidade de um proprietário de aplicativo e, em seguida, assumir ainda mais a identidade de um aplicativo privilegiado atualizando as credenciais para o aplicativo.
>- Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configurações críticas no Azure.
>- Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
>- Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
>- Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

### <a name="azure-devops-administrator"></a>[Administrador do Azure DevOps](#azure-devops-administrator-permissions)

Os usuários com essa função podem gerenciar a política Azure DevOps para restringir a criação da nova organização Azure DevOps a um conjunto de usuários ou grupos configuráveis. Os usuários nesta função podem gerenciar essa política através de qualquer organização Azure DevOps que tenha o apoio da organização Azure AD da empresa.

Todas as políticas corporativas do Azure DevOps podem ser gerenciadas pelos usuários nesta função.

### <a name="azure-information-protection-administrator"></a>[Administrador de proteção de informações do Azure](#azure-information-protection-administrator-permissions)

Usuários com essa função têm todas as permissões no serviço de Proteção de Informações do Azure. Esta função pode configurar rótulos para a política da Proteção de Informações do Azure, gerenciar modelos de proteção e ativar a proteção. Esta função não garante permissões de usuário no Identity Protection Center, Privileged Identity Management, Monitorar Integridade de Serviço do Office 365 ou Centro de Segurança e Conformidade do Office 365.

### <a name="b2c-user-flow-administrator"></a>[Administrador de fluxo de usuário B2C](#b2c-user-flow-administrator-permissions)

Os usuários com essa função podem criar e gerenciar fluxos de usuário B2C (também chamados de políticas "incorporadas") no portal Azure.Ao criar ou editar fluxos de usuário, esses usuários podem alterar o conteúdo html/CSS/javascript da experiência do usuário, alterar os requisitos de MFA por fluxo de usuário, alterar reivindicações no token e ajustar as configurações de sessão para todas as políticas no inquilino. Por outro lado, essa função não inclui a capacidade de revisar dados do usuário ou fazer alterações nos atributos que estão incluídos no esquema do inquilino.As alterações nas políticas do Identity Experience Framework (também conhecido como Personalizado) também estão fora do escopo dessa função.

### <a name="b2c-user-flow-attribute-administrator"></a>[Administrador de atributo de fluxo de usuário B2C](#b2c-user-flow-attribute-administrator-permissions)

Usuários com essa função adicionam ou excluem atributos personalizados disponíveis para todos os fluxos de usuário no inquilino.Como tal, os usuários com essa função podem alterar ou adicionar novos elementos ao esquema do usuário final e impactar o comportamento de todos os fluxos de usuários e indiretamente resultar em alterações em quais dados podem ser solicitados aos usuários finais e, finalmente, enviados como reivindicações aos aplicativos.Essa função não pode editar fluxos de usuário.

### <a name="b2c-ief-keyset-administrator"></a>[Administrador do conjunto de chaves B2C IEF](#b2c-ief-keyset-administrator-permissions)

O usuário pode criar e gerenciar chaves e segredos de políticas para criptografia de tokens, assinaturas de tokens e solicitar criptografia/descriptografia.Ao adicionar novas chaves aos contêineres-chave existentes, este administrador limitado pode rolar segredos conforme necessário sem afetar os aplicativos existentes.Este usuário pode ver o conteúdo completo desses segredos e suas datas de validade mesmo após sua criação.

> [!IMPORTANT]
> Este é um papel sensível.A função de administrador do keyset deve ser cuidadosamente auditada e atribuída com cuidado durante a pré-produção e produção.

### <a name="b2c-ief-policy-administrator"></a>[Administrador de políticas b2C IEF](#b2c-ief-policy-administrator-permissions)

Os usuários nesta função têm a capacidade de criar, ler, atualizar e excluir todas as políticas personalizadas no Azure AD B2C e, portanto, têm controle total sobre o Identity Experience Framework no inquilino AD B2C relevante do Azure. Ao editar políticas, esse usuário pode estabelecer uma federação direta com provedores de identidade externos, alterar o esquema do diretório, alterar todo o conteúdo voltado para o usuário (HTML, CSS, JavaScript), alterar os requisitos para completar uma autenticação, criar novos usuários, enviar dados de usuário para sistemas externos, incluindo migrações completas, e editar todas as informações do usuário, incluindo campos confidenciais, como senhas e números de telefone. Por outro lado, essa função não pode alterar as chaves de criptografia ou editar os segredos usados para a federação no inquilino.

> [!IMPORTANT]
> O Administrador de Políticas do IEF B2 é um papel altamente sensível que deve ser atribuído de forma muito limitada para os inquilinos em produção.As atividades desses usuários devem ser auditadas de perto, especialmente para os inquilinos em produção.

### <a name="billing-administrator"></a>[Administrador de cobrança](#billing-administrator-permissions)

Faz compras, gerencia assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

### <a name="cloud-application-administrator"></a>[Administrador de aplicativos na nuvem](#cloud-application-administrator-permissions)

Os usuários nessa função têm as mesmas permissões que a função Administrador de Aplicativos, excluindo a capacidade de gerenciar o proxy de aplicativo. Essa função concede a capacidade de criar e gerenciar todos os aspectos de aplicativos corporativos e os registros do aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões de aplicativos excluindo a API do Microsoft Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativos ou aplicativos corporativos.

Os administradores de aplicativos na nuvem podem gerenciar credenciais de aplicativos que lhes permitem se passar pelo aplicativo. Assim, os usuários atribuídos a essa função podem gerenciar as credenciais de aplicativos apenas daqueles aplicativos que não são atribuídos a quaisquer funções AD do Azure ou aqueles atribuídos apenas às seguintes funções de administrador:
* Desenvolvedor de aplicativos
* Administrador de Aplicativos de Nuvem
* Leitores de Diretório

Se um aplicativo for atribuído a qualquer outra função que não seja mencionada acima, então o Administrador de Aplicativos em Nuvem não poderá gerenciar credenciais desse aplicativo.

### <a name="cloud-device-administrator"></a>[Administrador de dispositivos na nuvem](#cloud-device-administrator-permissions)

Os usuários nessa função podem habilitar, desabilitar e excluir dispositivos no Azure AD e ler chaves do Windows 10 BitLocker (se houver) no portal do Azure. A função não concede permissões para gerenciar nenhuma outra propriedade no dispositivo.

### <a name="compliance-administrator"></a>[Administrador de Conformidade](#compliance-administrator-permissions)

Os usuários com essa função têm permissões para gerenciar recursos relacionados à conformidade no centro de conformidade do Microsoft 365, no centro de administração do Microsoft 365, no Azure e no Centro de Conformidade e Segurança do Office 365. Os cessionários também podem gerenciar todos os recursos dentro do centro de administração do Exchange e equipes & centros de administração do Skype for Business e criar bilhetes de suporte para o Azure e o Microsoft 365. Há mais informações disponíveis em [Sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

No | O que ele pode fazer
----- | ----------
[Centro de conformidade do Microsoft 365](https://protection.office.com) | Proteja e gerencie os dados da sua organização em todos os serviços do Microsoft 365<br>Gerenciar alertas de conformidade
[Gerenciador de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhar, atribuir e verificar as atividades de conformidade regulatória da sua organização
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar a governança de dados<br>Executar investigação jurídica e de dados<br>Gerenciar solicitação do titular dos dados<br><br>Essa função tem as mesmas permissões que o [RoleGroup do Administrador de Conformidade](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & controle de acesso baseado em função do Centro de Conformidade.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br>Pode exibir todos os relatórios internos em Gerenciamento de Dados

### <a name="compliance-data-administrator"></a>[Administrador de dados de conformidade](#compliance-data-administrator-permissions)

Os usuários com essa função têm permissões para rastrear dados no centro de conformidade microsoft 365, centro de administradores Microsoft 365 e Azure. Os usuários também podem rastrear dados de conformidade dentro do centro de administração do Exchange, gerente de conformidade e equipes & centro de administração do Skype for Business e criar bilhetes de suporte para o Azure e o Microsoft 365.

No | O que ele pode fazer
----- | ----------
[Centro de conformidade do Microsoft 365](https://protection.office.com) | Monitore políticas relacionadas à conformidade em serviços microsoft 365<br>Gerenciar alertas de conformidade
[Gerenciador de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhar, atribuir e verificar as atividades de conformidade regulatória da sua organização
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar a governança de dados<br>Executar investigação jurídica e de dados<br>Gerenciar solicitação do titular dos dados<br><br>Essa função tem as mesmas permissões que o [Compliance Data Administrator RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & O controle de acesso baseado em função do Centro de Conformidade.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br>Pode exibir todos os relatórios internos em Gerenciamento de Dados

### <a name="conditional-access-administrator"></a>[Administrador de Acesso Condicional](#conditional-access-administrator-permissions)

Os usuários com essa função têm a capacidade de gerenciar as configurações de acesso condicionado ao diretório ativo do Azure.
> [!NOTE]
> Para implantar a política de acesso condicional do Exchange ActiveSync no Azure, o usuário também deve ser um administrador global.

### <a name="customer-lockbox-access-approver"></a>[Aprovador de acesso ao Customer Lockbox](#customer-lockbox-access-approver-permissions)

gerencia [solicitações do Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) em sua organização. O aprovador recebe notificações de solicitações do Sistema de Proteção de Dados do Cliente por email e pode aprovar e negar solicitações do Centro de administração do Microsoft 365. Ele também pode ligar ou desligar o recurso Sistema de Proteção de Dados do Cliente. Somente os administradores globais podem redefinir as senhas das pessoas atribuídas à função acima.

### <a name="desktop-analytics-administrator"></a>[Administrador de Análise de Área de Trabalho](#desktop-analytics-administrator-permissions)


Os usuários nesta função podem gerenciar os serviços desktop analytics e office customization & Policy. Para o Desktop Analytics, isso inclui a capacidade de visualizar o inventário de ativos, criar planos de implantação, visualizar a implantação e o status de saúde. Para o serviço de política de & de personalização do escritório, essa função permite que os usuários gerenciem políticas do Office.

### <a name="device-administrator"></a>[Administrador de Dispositivo](#device-administrators-permissions)

Essa função está disponível para atribuição apenas como um administrador local adicional em [Configurações do dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os usuários com essa função se tornam administradores de computador local em todos os dispositivos Windows 10 associados ao Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory.

### <a name="directory-readers"></a>[Leitores de Diretórios](#directory-readers-permissions)

Os usuários nesta função podem ler informações básicas do diretório. Esta função deve ser usada para:
* Concedendo um conjunto específico de usuários convidados ler acesso em vez de concedê-lo a todos os usuários convidados.
* A concessão de um conjunto específico de usuários não administradores acesso ao portal Azure quando "Restringir o acesso ao portal Azure AD apenas para administradores" é definido como "Sim".
* Conceder aos diretores do serviço acesso ao diretório onde diretório.Read.All não é uma opção.

### <a name="directory-synchronization-accounts"></a>[Contas de sincronização de diretório](#directory-synchronization-accounts-permissions)

Não use. Essa função é automaticamente atribuída ao serviço do Azure AD Connect e não tem intenção ou suporte para outros usos.

### <a name="directory-writers"></a>[Gravadores de diretório](#directory-writers-permissions)

Essa é uma função herdada que deve ser atribuída a aplicativos que não tenham suporte em [Estrutura de Consentimento](../develop/quickstart-register-app.md). Ele não deve ser atribuído a nenhum usuário.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Administrador dinâmico 365 / Administrador de CRM](#crm-service-administrator-permissions)

Os usuários com essa função têm permissões globais no Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerenciar tíquete de suporte e monitorar a integridade do serviço. Mais informações em [usar a função de administrador de serviço para gerenciar seu locatário](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços Dynamics 365". É "Administrador dinâmico 365" no [portal Azure.](https://portal.azure.com)

### <a name="exchange-administrator"></a>[Administrador do Exchange](#exchange-service-administrator-permissions)

Os usuários com essa função têm permissões globais no Microsoft Exchange Online, quando o serviço está presente. Eles também tem a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre office 365 funções de admin](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administrador a Serviço de Troca". É "Administrador de Troca" no [portal Azure.](https://portal.azure.com) É "Exchange Online administrator" no [centro de administração do Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

### <a name="external-identity-provider-administrator"></a>[Administrador do Provedor de Identidade Externa](#external-identity-provider-administrator-permissions)

Este administrador gerencia a federação entre os inquilinos do Azure Active Directory e os provedores de identidade externos.Com essa função, os usuários podem adicionar novos provedores de identidade e configurar todas as configurações disponíveis (por exemplo, caminho de autenticação, ID de serviço, contêineres-chave atribuídos).Esse usuário pode permitir que o inquilino confie em autenticações de provedores de identidade externos.O impacto resultante nas experiências do usuário final depende do tipo de inquilino:

* Inquilinos do Azure Active Directory para funcionários e parceiros: A adição de uma federação (por exemplo, com o Gmail) impactará imediatamente todos os convites de convidados ainda não resgatados. Consulte [Adicionar o Google como um provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Azure Active Directory B2C inquilinos: A adição de uma federação (por exemplo, com o Facebook, ou com outra organização AD do Azure) não afeta imediatamente os fluxos do usuário final até que o provedor de identidade seja adicionado como uma opção em um fluxo de usuário (também chamado de política incorporada). Consulte [Configurar uma conta Microsoft como provedor de identidade](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) para um exemplo.Para alterar os fluxos do usuário, é necessária a função limitada de "Administrador de Fluxo de Usuário B2C".

### <a name="global-administrator--company-administrator"></a>[Administrador Global / Administrador de Empresas](#company-administrator-permissions)

Os usuários com essa função têm acesso a todos os recursos administrativos do Azure Active Directory, bem como aos serviços que usam identidades do Azure Active Directory como centro de segurança do Microsoft 365, centro de conformidade do Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. A pessoa que se inscreve no locatário do Azure Active Directory torna-se um administrador global. Pode haver mais de um administrador global na sua empresa. Administradores globais podem redefinir a senha para qualquer usuário e todos os outros administradores.

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administradora da Empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Leitor global](#global-reader-permissions)

Os usuários nesta função podem ler configurações e informações administrativas nos serviços do Microsoft 365, mas não podem tomar ações de gerenciamento. O leitor global é a contrapartida somente para o administrador global. Atribuir leitor global em vez de administrador global para planejamento, auditorias ou investigações. Use o leitor Global em combinação com outras funções de administração limitadas, como o Administrador de Câmbio, para facilitar o trabalho feito sem a atribuição da função de Administrador Global. O leitor global trabalha com o centro de administração Microsoft 365, centro de administração do Exchange, centro de administração de equipes, centro de segurança, centro de conformidade, centro de administração Azure AD e centro de administração de gerenciamento de dispositivos.

> [!NOTE]
> O papel do leitor global tem algumas limitações agora -
>
>- O centro de admin SharePoint - O centro de admin SharePoint não suporta o papel de leitor Global. Você não verá 'SharePoint' no painel esquerdo em Centros de Administradores no [centro de administradores microsoft 365](https://admin.microsoft.com/Adminportal/Home#/homepage).
>- [Centro de admin OneDrive](https://admin.onedrive.com/) - O centro de admin OneDrive não suporta a função de leitor Global.
>- [Portal Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) - O leitor global não pode ler o modo de provisionamento de um aplicativo corporativo.
>- [Centro de admin M365](https://admin.microsoft.com/Adminportal/Home#/homepage) - O leitor global não pode ler solicitações de caixa de bloqueio do cliente. Você não encontrará a guia **solicitações** do caixa de bloqueio cliente em **Suporte** no painel esquerdo do Centro de Administrador M365.
>- [Centro de segurança M365](https://security.microsoft.com/homepage) - O leitor global não consegue ler rótulos de sensibilidade e retenção. Você não encontrará **etiquetas de sensibilidade, etiquetas** **de retenção**e **guias de análise de rótulos** no painel esquerdo do centro de segurança M365.
>- [Centro de conformidade & segurança](https://sip.protection.office.com/homepage) do Office - O leitor global não pode ler registros de auditoria do SCC, fazer pesquisa de conteúdo ou ver Secure Score.
>- [Central de administração de equipes](https://admin.teams.microsoft.com) - O leitor global não pode ler **O ciclo de vida das equipes,** **relatórios de & de análise,** gerenciamento de **dispositivos telefônicos IP** e catálogo de **aplicativos.**
>- [O PAM (Privileged Access Management, gerenciamento de acesso privilegiado)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) não suporta o papel de leitor Global.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) - O leitor global é suportado apenas [para relatórios centrais,](https://docs.microsoft.com/azure/information-protection/reports-aip) e quando sua organização Azure AD não está na [plataforma de rotulagem unificada.](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Esses recursos estão atualmente em desenvolvimento.
>

### <a name="groups-administrator"></a>[Administrador de grupos](#groups-administrator-permissions)

Os usuários nesta função podem criar/gerenciar grupos e suas configurações, como políticas de nomeação e expiração. É importante entender que atribuir um usuário a essa função dá-lhe a capacidade de gerenciar todos os grupos no inquilino em várias cargas de trabalho, como Teams, SharePoint, Yammer, além do Outlook. Além disso, o usuário poderá gerenciar as configurações de vários grupos em vários portais de administradores, como o Microsoft Admin Center, o portal Azure, bem como os específicos da carga de trabalho, como Teams e SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Emissor do Convite ao Convidado](#guest-inviter-permissions)

Usuários nessa função podem gerenciar convites de usuários convidados do Azure Active Directory B2B quando a configuração do usuário **Membros podem convidar** estiver definida como Não. Mais informações sobre a colaboração B2B em [Sobre a colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Ela não inclui nenhuma outra permissão.

### <a name="helpdesk-administrator"></a>[Administrador de helpdesk](#helpdesk-administrator-permissions)

Usuários com essa função podem alterar senhas, invalidar tokens de atualização, gerenciar solicitações de serviço e monitorar a integridade do serviço. Invalidar um token de atualização força o usuário a entrar novamente. Os administradores do Helpdesk podem redefinir senhas e invalidar tokens de atualização de outros usuários que não são administradores ou atribuídos apenas as seguintes funções:

* Leitores de Diretório
* Emissor do Convite ao Convidado
* Administrador de assistência técnica
* Leitor do Centro de Mensagens
* Leitor de Relatórios

> [!IMPORTANT]
> Usuários com essa função podem alterar senhas de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração da senha de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
>
>- Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar, não concedidas a Administradores de Assistência Técnica. Por esse caminho, um Administrador de Assistência Técnica pode ser capaz de assumir a identidade de um proprietário de aplicativo e, depois, assumir a identidade de um aplicativo com privilégios, atualizando as credenciais do aplicativo.
>- Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configuração crítica no Azure.
>- Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
>- Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
>- Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

Delegar permissões administrativas sobre subconjuntos de usuários e aplicar políticas a um subconjunto de usuários é possível com [Unidades Administrativas (agora em visualização pública)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).

Essa função foi anteriormente chamada de "Administrador de senhas" no [portal Azure](https://portal.azure.com/). O nome "Administrador do Helpdesk" no Azure AD agora corresponde ao seu nome no Azure AD PowerShell e na API do Microsoft Graph.

### <a name="intune-administrator"></a>[Administrador do Intune](#intune-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft Intune Online, quando o serviço está presente. Além disso, essa função contém a capacidade de gerenciar usuários e dispositivos para associar a política, bem como criar e gerenciar grupos. Mais informações no [RBAC (Role-based Administration Control, controle de administração baseado em função) com o Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

Essa função pode criar e gerenciar todos os grupos de segurança. No entanto, o Intune Admin não tem direitos de admin sobre grupos de Escritório. Isso significa que o admin não pode atualizar proprietários ou membros de todos os grupos do Office no inquilino. No entanto, ele pode gerenciar o grupo Office que ele cria que vem como parte de seus privilégios de usuário final. Assim, qualquer grupo de Escritório (não grupo de segurança) que crie deve ser contado contra sua cota de 250.

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administrador a serviço intune". É "Administrador Intune" no [portal Azure.](https://portal.azure.com)

### <a name="kaizala-administrator"></a>[Administrador kaizala](#kaizala-administrator-permissions)

Os usuários com essa função têm permissões globais para gerenciar configurações dentro do Microsoft Kaizala, quando o serviço está presente, bem como a capacidade de gerenciar tickets de suporte e monitorar a saúde do serviço. Além disso, o usuário pode acessar relatórios relacionados à adoção & uso de Kaizala por membros da Organização e relatórios de negócios gerados usando as ações kaizala.

### <a name="license-administrator"></a>[Administrador de Licenças](#license-administrator-permissions)

Usuários nessa função podem adicionar, remover e atualizar as atribuições de licenças em usuários, grupos (usando o licenciamento baseado em grupo) e gerenciar a localização de uso dos usuários. A função não concede a capacidade de comprar ou gerenciar assinaturas, criar ou gerenciar grupos, ou criar ou gerenciar usuários além do local de uso. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="message-center-privacy-reader"></a>[Leitor de privacidade do Centro de Mensagens](#message-center-privacy-reader-permissions)

Os usuários nesta função podem monitorar todas as notificações no Message Center, incluindo mensagens de privacidade de dados. Os leitores de privacidade do Message Center recebem notificações de e-mail, incluindo aquelas relacionadas à privacidade de dados e podem cancelar a assinatura usando preferências de centro de mensagens. Apenas o Administrador Global e o Leitor de Privacidade do Message Center podem ler mensagens de privacidade de dados. Além disso, essa função contém a capacidade de visualizar grupos, domínios e assinaturas. Essa função não tem permissão para visualizar, criar ou gerenciar solicitações de serviço.

### <a name="message-center-reader"></a>[Leitor do Centro de Mensagens](#message-center-reader-permissions)

Usuários nessa função podem monitorar notificações e atualizações de integridade de consultoria no [Centro de Mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) da organização em serviços configurados como Exchange, Intune e Microsoft Teams. Os Leitores do Centro de Mensagens recebem por email resumos semanais de postagens, atualizações e podem compartilhar postagens do Centro de Mensagens no Office 365. No Azure AD, os usuários atribuídos a essa função terão acesso somente leitura aos serviços do Azure AD como usuários e grupos. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="office-apps-administrator"></a>[Administrador de aplicativos de escritório](#office-apps-administrator-permissions)

Os usuários nesta função podem gerenciar as configurações de nuvem dos aplicativos do Office 365. Isso inclui o gerenciamento de políticas na nuvem, o gerenciamento de downloads de autoatendimento e a capacidade de visualizar relatórios relacionados a aplicativos do Office. Além disso, essa função concede a capacidade de gerenciar os tickets de suporte e monitorar a saúde do serviço dentro do principal centro de admin. Os usuários atribuídos a essa função também podem gerenciar a comunicação de novos recursos em aplicativos do Office. 

### <a name="partner-tier1-support"></a>[Suporte ao Nível 1 do parceiro](#partner-tier1-support-permissions)

Não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

### <a name="partner-tier2-support"></a>[Suporte ao Nível 2 do parceiro](#partner-tier2-support-permissions)

Não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

### <a name="password-administrator"></a>[Administrador de senhas](#password-administrator-permissions)

Os usuários com essa função têm capacidade limitada de gerenciar senhas. Essa função não garante a capacidade de gerenciar solicitações de serviços ou monitorar a saúde dos serviços. Os administradores de senhas podem redefinir senhas de outros usuários que não são administradores ou membros das seguintes funções:

* Leitores de Diretório
* Emissor do Convite ao Convidado
* Administrador de senha

### <a name="power-bi-administrator"></a>[Administrador do BI de energia](#power-bi-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Noções básicas sobre a função de administrador do Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços de Power BI". É "Power BI Administrator" no [portal Azure](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Administrador da plataforma de energia](#power-platform-administrator-permissions)

Os usuários nesta função podem criar e gerenciar todos os aspectos de ambientes, PowerApps, Fluxos, políticas de prevenção de perda de dados. Além disso, os usuários com essa função têm a capacidade de gerenciar tickets de suporte e monitorar a saúde do serviço.

### <a name="privileged-authentication-administrator"></a>[Administrador de autenticação privilegiado](#privileged-authentication-administrator-permissions)

Os usuários com essa função podem definir ou redefinir credenciais não-senha para todos os usuários, incluindo administradores globais, e podem atualizar senhas para todos os usuários. Os administradores de autenticação privilegiada podem forçar os usuários a se recadastrarem contra a credencial não-senha existente (por exemplo, MFA, FIDO) e revogar o "lembrar MFA no dispositivo", solicitando o MFA no próximo login de todos os usuários.

### <a name="privileged-role-administrator"></a>[Administrador de funções privilegiado](#privileged-role-administrator-permissions)

Usuários com essa função podem gerenciar as atribuições de função no Azure Active Directory, bem como Azure Active Directory Privileged Identity Management. Além disso, essa função permite a gestão de todos os aspectos da Gestão privilegiada da Identidade e das unidades administrativas.

> [!IMPORTANT]
> Essa função concede a capacidade de gerenciar atribuições para todas as funções do Azure AD, incluindo a função de Administrador Global. Essa função não inclui outras habilidades privilegiadas no Azure AD, como criar ou atualizar usuários. No entanto, os usuários atribuídos a essa função podem conceder a si mesmos ou aos privilégios adicionais de outras pessoas atribuindo funções adicionais.

### <a name="reports-reader"></a>[Leitor de relatórios](#reports-reader-permissions)

Os usuários com essa função podem visualizar os dados de relatórios de uso e o painel de relatórios no centro de administração do Microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de entrada e atividades no Azure AD e a dados retornados pela API de relatórios do Microsoft Graph. Um usuário atribuído à função Leitor de Relatórios pode acessar somente o uso relevante e as métricas de adoção. Eles não têm permissões de administrador para definir configurações ou acessar que os centros da administração de produtos específicos como o Exchange. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="search-administrator"></a>[Administrador de pesquisa](#search-administrator-permissions)

Os usuários nesta função têm acesso total a todos os recursos de gerenciamento do Microsoft Search no centro de administração microsoft 365. Os administradores de pesquisa podem delegar as funções de Administradores de Pesquisa e Editorde Pesquisa aos usuários e criar e gerenciar conteúdo, como marcadores, Q&As e locais. Além disso, esses usuários podem visualizar o centro de mensagens, monitorar a saúde do serviço e criar solicitações de serviços.

### <a name="search-editor"></a>[Editor de pesquisa](#search-editor-permissions)

Os usuários nesta função podem criar, gerenciar e excluir conteúdo para o Microsoft Search no centro de admin microsoft 365, incluindo marcadores, Q&As e locais.

### <a name="security-administrator"></a>[Administrador de segurança](#security-administrator-permissions)

Os usuários com essa função têm permissões para gerenciar recursos relacionados à segurança na central de segurança do Microsoft 365, Azure Active Directory Identity Protection, Proteção de Informações do Azure e Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

No | O que ele pode fazer
--- | ---
[Central de segurança do Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas a segurança em todos os serviços do Microsoft 365<br>Gerenciar alertas e ameaças de segurança<br>Exibir relatórios
Identity Protection Center | Todas as permissões da função Leitor de Segurança<br>Além disso, a habilidade de executar todas as operações do Centro de Proteção de Identidade, exceto redefinir senhas
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função Leitor de Segurança<br>**Não é possível** gerenciar atribuições ou configurações de função Azure AD
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar políticas de segurança<br>Exibir, investigar e responder a ameaças de segurança<br>Exibir relatórios
Proteção Avançada contra Ameaças do Azure | Monitorar e responder a atividades suspeitas de segurança
Windows Defender ATP e EDR | Atribuir funções<br>Gerenciar grupos de computadores<br>Configurar a detecção de ameaças do ponto de extremidade e a correção automatizada<br>Exibir, investigar e responder a alertas
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo<br>Não pode fazer alterações no Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adicionar administradores, adicionar políticas e configurações, carregar logs e executar ações de governança
[Centro de Segurança Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir políticas de segurança, exibir estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações
[Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

### <a name="security-operator"></a>[Operador de segurança](#security-operator-permissions)

Os usuários com essa função podem gerenciar alertas e ter acesso global somente à leitura em recursos relacionados à segurança, incluindo todas as informações no centro de segurança do Microsoft 365, no Azure Active Directory, na Proteção de Identidade, no Gerenciamento de Identidade Privilegiada e no Office 365 Security & Compliance Center. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

No | O que ele pode fazer
--- | ---
[Central de segurança do Microsoft 365](https://protection.office.com) | Todas as permissões da função Leitor de Segurança<br>Visualize, investigue e responda a alertas de ameaças à segurança
Identity Protection Center | Todas as permissões da função Leitor de Segurança<br>Além disso, a habilidade de executar todas as operações do Centro de Proteção de Identidade, exceto redefinir senhas
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função Leitor de Segurança
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões da função Leitor de Segurança<br>Visualize, investigue e responda a alertas de segurança
Windows Defender ATP e EDR | Todas as permissões da função Leitor de Segurança<br>Visualize, investigue e responda a alertas de segurança
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Todas as permissões da função Leitor de Segurança
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Todas as permissões da função Leitor de Segurança
[Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

### <a name="security-reader"></a>[Leitor de segurança](#security-reader-permissions)

Usuários com essa função têm acesso somente leitura global em recurso relacionado à segurança, incluindo todas as informações no centro de segurança do Microsoft 365, no Azure Active Directory, no Identity Protection e no Privileged Identity Management, bem como a capacidade de ler logs de auditoria e relatórios de entrada do Azure Active Directory e no Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

No | O que ele pode fazer
--- | ---
[Central de segurança do Microsoft 365](https://protection.office.com) | Exibir políticas relacionadas à segurança em todos os serviços do Microsoft 365<br>Exibir alertas e ameaças de segurança<br>Exibir relatórios
Identity Protection Center | Ler todos os relatórios de segurança e informações de configurações para recursos de segurança<br><ul><li>Anti-spam<li>Criptografia<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de fluxo de mensagens
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tem acesso somente à leitura de todas as informações em ergiu no Azure AD Privileged Identity Management: Políticas e relatórios para atribuições de função Ad do Azure e revisões de segurança.<br>**Não é possível** se inscrever no Azure AD Privileged Identity Management ou fazer qualquer alteração nele. No portal privileged Identity Management ou via PowerShell, alguém nesta função pode ativar funções adicionais (por exemplo, Administrador Global ou Administrador de Funções Privilegiadas), se o usuário for elegível para elas.
[Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Exibir políticas de segurança<br>Exibir e investigar ameaças de segurança<br>Exibir relatórios
Windows Defender ATP e EDR | Veja e investigue alertas. Quando você ativar o controle de acesso baseado em rpg no Windows Defender ATP, os usuários com permissões somente de leitura, como a função de leitor de segurança do Azure AD, perdem o acesso até que sejam atribuídos a uma função ATP do Windows Defender.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo. Não pode fazer alterações no Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas
[Centro de Segurança Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir recomendações e alertas, exibir políticas de segurança, exibir estados de segurança, mas não pode fazer alterações
[Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

### <a name="service-support-administrator"></a>[Administrador de suporte de serviço](#service-support-administrator-permissions)

Os usuários com essa função podem abrir solicitações de suporte com os serviços Azure e Office 365, e visualiza o painel de serviços e o centro de mensagens no [portal Azure](https://portal.azure.com) e no [centro administrativo Microsoft 365.](https://admin.microsoft.com) Mais informações em [Sobre funções de admin](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Anteriormente, essa função era chamada de "Administrador de Serviços" no [portal Azure](https://portal.azure.com) e [no centro de administração microsoft 365](https://admin.microsoft.com). Renomeamos-o para "Service Support Administrator" para alinhar com o nome de exsiting na API do Microsoft Graph, a Api do Gráfico Azure AD e a Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Administrador do SharePoint](#sharepoint-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de admin](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços SharePoint". É "Administrador do SharePoint" no [portal Azure](https://portal.azure.com).

### <a name="skype-for-business--lync-administrator"></a>[Skype for Business / Lync Administrador](#lync-service-administrator-permissions)

Usuários com essa função têm permissões globais no Microsoft Skype for Business, quando o serviço está presente, além de gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço, além de acessar o centro de administração do Skype for Business e do Teams. A conta também deve ser licenciada para o Teams ou não poderá executar os cmdlets do PowerShell do Teams. Mais informações em [Sobre a função de administrador do Skype for Business](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e informações de licenciamento do Teams em [licenciamento de complemento do Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Na API do Microsoft Graph e no Azure AD PowerShell, essa função é identificada como "Administrador a serviço Lync". É "Administrador do Skype for Business" no [portal do Azure](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Administrador de Comunicações de Equipes](#teams-communications-administrator-permissions)

Usuários nessa função podem gerenciar aspectos da carga de trabalho do Microsoft Teams relacionados a voz e telefonia. Isso inclui as ferramentas de gerenciamento para atribuição de número de telefone, políticas de reuniões e voz e acesso completo ao conjunto de ferramentas de análise de chamada.

### <a name="teams-communications-support-engineer"></a>[Engenheiro de Suporte de Comunicações de Equipes](#teams-communications-support-engineer-permissions)

Usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams e Skype for Business usando as ferramentas de solução de problemas de chamada de usuário no centro de administração do Microsoft Teams e Skype for Business. Os usuários nesta função podem exibir informações do registro de chamadas completas para todos os participantes envolvidos. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="teams-communications-support-specialist"></a>[Especialista em Suporte de Comunicações de Equipes](#teams-communications-support-specialist-permissions)

Usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams e Skype for Business usando as ferramentas de solução de problemas de chamada de usuário no centro de administração do Microsoft Teams e Skype for Business. Os usuários nessa função só podem exibir detalhes do usuário na chamada para o usuário específico que eles pesquisaram. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

### <a name="teams-service-administrator"></a>[Administrador de Serviços do Teams](#teams-service-administrator-permissions)

Usuários nessa função podem gerenciar todos os aspectos da carga de trabalho do Microsoft Teams pelo centro de administração do Microsoft Teams e Skype for Business e respectivos módulos do PowerShell. Isso inclui, entre outras áreas, todas as ferramentas de gerenciamento relacionadas a telefonia, mensagens, reuniões e às próprias equipes. Além disso, essa função concede a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço.

### <a name="user-administrator"></a>[Administrador de usuários](#user-administrator-permissions)

Os usuários com essa função podem criar usuários e gerenciar todos os aspectos dos usuários com algumas restrições (veja abaixo), e podem atualizar políticas de expiração de senhas. Além disso, os usuários com essa função podem criar e gerenciar todos os grupos. Essa função também inclui a capacidade de criar e gerenciar exibições de usuários, gerenciar tickets de suporte e monitorar a integridade do serviço. Os administradores de usuários não têm permissão para gerenciar algumas propriedades de usuário para usuários na maioria das funções de administrador. O usuário com esta função não tem permissões para gerenciar o MFA. Os papéis que são exceções a esta restrição estão listados na tabela a seguir.

| | |
| --- | --- |
|Permissões gerais|<p>Criar usuários e grupos</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Gerenciar tíquetes de suporte do Office<p>Atualizar políticas de expiração de senhas|
|<p>Em todos os usuários, inclusive todos os administradores</p>|<p>Gerenciar licenças</p><p>Gerenciar todas as propriedades de usuário, exceto o nome Principal do usuário</p>
|Somente em usuários não administradores ou em qualquer um destes procedimentos limitada de funções de administrador:<ul><li>Leitores de Diretório<li>Emissor do Convite ao Convidado<li>Administrador de assistência técnica<li>Leitor do Centro de Mensagens<li>Leitor de Relatórios<li>Administrador de usuários|<p>Excluir e restauração</p><p>Desativar e ativar</p><p>Invalidar Tokens de atualização</p><p>Gerenciar todas as propriedades de usuário, incluindo o nome Principal do usuário</p><p>Redefinir senha</p><p>Atualizar chaves de dispositivo FIDO)</p>|

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

### <a name="application-administrator-permissions"></a>Permissões de administrador de aplicativos

Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/Application/appProxyAutenticação/atualização | Atualize as propriedades de autenticação do App Proxy nos princípios do serviço no Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlConfigurações/atualização | Atualize urls internos e externos do aplicativo no Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Leia todas as propriedades do App Proxy. |
| microsoft.directory/applications/applicationProxy/update | Atualize todas as propriedades do App Proxy. |
| microsoft.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/credenciais/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.directory/applications/permissões/atualização | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.directory/appRoleAtribuições/leitura | Leia appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/conectorGrupos/tudo/ler | Leia as propriedades do grupo de conector proxy do aplicativo no Azure Active Directory. |
| microsoft.directory/connectorGroups/tudo/update | Atualize todas as propriedades do grupo de conector proxy do aplicativo no Azure Active Directory. |
| microsoft.directory/connectorGroups/create | Crie grupos de conectorproxy de aplicativos no Azure Active Directory. |
| microsoft.directory/conectorGrupos/exclusão | Exclua grupos de conector proxy de aplicativos no Azure Active Directory. |
| microsoft.directory/conectores/tudo/ler | Leia todas as propriedades do conector proxy do aplicativo no Azure Active Directory. |
| microsoft.directory/conectores/create | Crie conectores proxy de aplicativos no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/básico/atualização | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/applicationConfiguração/exclusão | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/proprietários/atualização | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/policyAplicada-seAplicada/leia | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/audiência/atualização | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipais/autenticação/atualização | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipais/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipais/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipais/credenciais/atualização | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipais/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipais/proprietários/atualização | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/permissões/atualização | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipais/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="application-developer-permissions"></a>Permissões do Desenvolvedor de Aplicativos

Pode criar registros de aplicativos independentes da configuração 'Usuários podem registrar aplicativos'.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Crie aplicativos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/appRoleAssignments/createAsOwner | Crie appRoleAssignments no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Crie oAuth2PermissionGrants no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/servicePrincipais/createAsOwner | Criar servicePrincipals em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |

### <a name="authentication-administrator-permissions"></a>Permissões de administrador de autenticação

Permitido para exibir, definir e redefinir as informações de método de autenticação para qualquer usuário não administrador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Atualize propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários da organização Office 365. Consulte a documentação online para obter mais detalhes. |

### <a name="azure-devops-administrator-permissions"></a>Permissões de administrador do Azure DevOps

Pode gerenciar a política e as configurações da organização Azure DevOps.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte [a descrição da função](#azure-devops-administrator) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Leia e configure DevOps Azure. |

### <a name="azure-information-protection-administrator-permissions"></a>Permissões do administrador de proteção de informações do Azure

Pode gerenciar todos os aspectos do serviço de proteção de informações do Azure.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte [a descrição da função](#) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="b2c-user-flow-administrator-permissions"></a>Permissões do administrador de fluxo do usuário B2C

Crie e gerencie todos os aspectos dos fluxos de usuário.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Leia e configure fluxos de usuário no Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Permissões do administrador de atributo de fluxo de usuário B2C

Crie e gerencie o esquema de atributo disponível para todos os fluxos de usuário.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Leia e configure atributos do usuário no Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Permissões de administrador do conjunto de chaves B2C IEF

Gerenciar segredos para federação e criptografia no Identity Experience Framework.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Leia e configure conjuntos de chaves no Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Permissões do administrador de políticas do B2C IEF

Crie e gerencie políticas de estrutura de confiança no Quadro de Experiência de Identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Leia e configure políticas personalizadas no Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Permissões de administrador de faturamento

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
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Permissões de administrador de aplicativos na nuvem

Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais, exceto o Proxy de Aplicativo.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/credenciais/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.directory/applications/permissões/atualização | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/básico/atualização | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/exclusão | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/proprietários/atualização | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguração/policyAplicada-seAplicada/leia | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/audiência/atualização | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipais/autenticação/atualização | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipais/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipais/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipais/credenciais/atualização | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipais/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipais/proprietários/atualização | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/permissões/atualização | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipais/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Permissões de administrador de dispositivos na nuvem

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

### <a name="company-administrator-permissions"></a>Permissões de administrador da empresa

Pode gerenciar todos os aspectos do Azure AD e dos serviços da Microsoft que usam identidades do Azure AD. Essa função também é conhecida como a função de Administrador Global. 

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar as propriedades padrão em microsoft.aad.cloudAppSecurity. |
| microsoft.diretório/administrativeUnits/allProperties/allTasks | Criar e excluir administrativeUnits e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/applications/allProperties/allTasks | Criar e excluir aplicativos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/appRoleAtribuições/todasPropriedades/todasasTarefas | Criar e excluir appRoleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.diretório/contatos/allProperties/allTasks | Criar e excluir contatos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/contracts/allProperties/allTasks | Criar e excluir contratos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Criar e excluir dispositivos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/diretórioFunes/todasPropriedades/todasasTarefas | Criar e excluir DirectoryRoles, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/diretórioRoleTemplates/allProperties/allTasks | Criar e excluir DirectoryRoleTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/domínios/allProperties/allTasks | Criar e excluir Domínios, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/groups/allProperties/allTasks | Criar e excluir Grupos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/groupConfigurações/todasPropriedades/todas astarefas | Criar e excluir groupSettings e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/groupConfiguraçãoModelos/tudoPropriedades/todasasTarefas | Criar e excluir groupSettingTemplates e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Criar e excluir loginTenantBranding e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Criar e excluir oAuth2PermissionGrants e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Criar e excluir organização e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/policies/allProperties/allTasks | Criar e excluir políticas, ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/roleAssignments/allProperties/allTasks | Criar e excluir roleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.diretório/roleDefinições/tudoPropriedades/todas astarefas | Criar e excluir roleDefinitions e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Criar e excluir scopedRoleMemberships e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/serviceAction/activateService | Pode executar a ação de serviço Activateservice no Azure Active Directory |
| microsoft.diretório/serviceAction/disableDirectoryFeature | Pode executar a ação de serviço Disabledirectoryfeature no Azure Active Directory |
| microsoft.directory/serviceAction/enableDirectoryFeature | Pode executar a ação de serviço Enabledirectoryfeature no Azure Active Directory |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | Pode executar a ação de serviço Getavailableextentionproperties no Azure Active Directory |
| microsoft.diretório/servicePrincipais/allProperties/allTasks | Criar e excluir servicePrincipals e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.directory/subscritoSkus/allProperties/allTasks | Criar e excluir subscribedSkus e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directory/users/allProperties/allTasks | Criar e excluir usuários e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Ler todos os recursos em microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspectos de cobrança do Office 365. |
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

### <a name="compliance-administrator-permissions"></a>Permissões do Administrador de Conformidade

Pode ler e gerenciar a configuração de conformidade e relatórios no Azure AD e no Office 365.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="compliance-data-administrator-permissions"></a>Permissões de administrador de dados de conformidade

Cria e gerencia conteúdo de conformidade.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leia e configure o Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="conditional-access-administrator-permissions"></a>Permissões de administrador de acesso condicional

Pode gerenciar recursos de acesso condicional.

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

### <a name="crm-service-administrator-permissions"></a>Permissões do administrador de serviços do CRM

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

### <a name="customer-lockbox-access-approver-permissions"></a>Permissões do aprovação de acesso do Customer LockBox

Pode aprovar solicitações de suporte da Microsoft para acessar dados organizacionais do cliente.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |

### <a name="desktop-analytics-administrator-permissions"></a>Permissões do administrador do Desktop Analytics

Pode gerenciar os serviços desktop analytics e de personalização de & policy. Para o Desktop Analytics, isso inclui a capacidade de visualizar o inventário de ativos, criar planos de implantação, visualizar a implantação e o status de saúde. Para o serviço de política de & de personalização do escritório, essa função permite que os usuários gerenciem políticas do Office.

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

### <a name="device-administrators-permissions"></a>Permissões de administradores de dispositivos

Os usuários atribuídos a essa função são adicionados ao grupo de administradores locais em dispositivos azure ad..

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Permissões de leitores de diretório
Pode ler informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Ler as propriedades básicas em administrativeUnits no Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read | Ler a propriedade Administrativeunits no Azure Active Directory. |
| microsoft.directory/applications/basic/read | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| microsoft.directory/applications/owners/read | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.directory/applications/policies/read | Leia a propriedade applications.policies no Active Directory do Azure. |
| microsoft.directory/contatos/básico/lido | Ler as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contatos/memberOf/read | Ler a propriedade Contacts no Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Ler as propriedades básicas sobre os contratos no Azure Active Directory. |
| microsoft.directory/devices/basic/read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.directory/devices/memberOf/read | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.directory/devices/registeredProprietários/lidos | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.directory/devices/registeredUsuários/read | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.diretório/diretórioRoles/basic/read | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.directory/diretórioRoles/eligibleMembers/read | Ler a propriedade Eligiblemembers no Azure Active Directory. |
| microsoft.directory/diretórioFunes/membros/leitura | Ler a propriedade Directoryroles no Azure Active Directory. |
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
| microsoft.diretório/roleAssignments/basic/read | Leia as propriedades básicas em roleAssignments no Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read | Leia as propriedades básicas em roleDefinitions no Active Directory do Azure. |
| microsoft.directory/servicePrincipais/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAtribuições/leitura | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipais/básico/lido | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.directory/servicePrincipais/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/oAuth2PermissionGrants/basic/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/servicePrincipais/ownedObjetos/ler | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/proprietários/lido | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/users/basic/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.directory/users/ownedObjetos/ler | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

### <a name="directory-synchronization-accounts-permissions"></a>Permissões de contas de sincronização do diretório

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
| microsoft.directory/servicePrincipais/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAtribuições/leitura | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/audiência/atualização | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.directory/servicePrincipais/autenticação/atualização | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.directory/servicePrincipais/básico/lido | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.directory/servicePrincipais/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.directory/servicePrincipais/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.directory/servicePrincipais/credenciais/atualização | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.directory/servicePrincipais/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/oAuth2PermissionGrants/basic/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/servicePrincipais/proprietários/lido | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/proprietários/atualização | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.directory/servicePrincipais/ownedObjetos/ler | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/permissões/atualização | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.directory/servicePrincipais/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/servicePrincipais/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Permissões de roteiristas de diretório

Pode ler e gravar informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/configurações/atualização | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groupSettings/basic/update | Atualize as propriedades básicas em groupSettings no Azure Active Directory. |
| microsoft.directory/groupSettings/create | Crie groupSettings no Azure Active Directory. |
| microsoft.directory/groupConfigurações/exclusão | Exclua groupSettings no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/asignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Permissões do administrador do serviço de intercâmbio

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
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Permissões do administrador do provedor de identidade externo

Configure provedores de identidade para uso em federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Leia e configure provedores de identidade no Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Permissões do Leitor Global
Pode ler tudo o que um administrador global pode, mas não editar nada.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte [a descrição da função](#global-reader) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Leia todos os aspectos do faturamento do Office 365. |
| microsoft.directory/administrativeUnits/basic/read    | Ler as propriedades básicas em administrativeUnits no Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read    | Ler a propriedade Administrativeunits no Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| microsoft.directory/applications/owners/read    | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.directory/applications/policies/read    | Leia a propriedade applications.policies no Active Directory do Azure. |
| microsoft.directory/contatos/básico/lido    | Ler as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contatos/memberOf/read    | Ler a propriedade Contacts no Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Ler as propriedades básicas sobre os contratos no Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.directory/devices/memberOf/read    | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.directory/devices/registeredProprietários/lidos    | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.directory/devices/registeredUsuários/read    | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.diretório/diretórioRoles/basic/read    | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.directory/diretórioRoles/eligibleMembers/read    | Ler a propriedade Eligiblemembers no Azure Active Directory. |
| microsoft.directory/diretórioFunes/membros/leitura    | Ler a propriedade Directoryroles no Azure Active Directory. |
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
| microsoft.directory/policies/standard/read    | Leia políticas padrão no Azure Active Directory. |
| microsoft.diretório/roleAssignments/basic/read    | Leia as propriedades básicas em roleAssignments no Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read    | Leia as propriedades básicas em roleDefinitions no Active Directory do Azure. |
| microsoft.directory/servicePrincipais/appRoleAssignedTo/read    | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.directory/servicePrincipais/appRoleAtribuições/leitura    | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.directory/servicePrincipais/básico/lido    | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.directory/servicePrincipais/memberOf/read    | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/oAuth2PermissionGrants/basic/read    | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.directory/servicePrincipais/ownedObjetos/ler    | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/proprietários/lido    | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.directory/servicePrincipais/policies/read    | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read    | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read    | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/users/basic/read    | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.directory/users/directReports/read    | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.directory/users/manager/read    | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.directory/users/memberOf/read    | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.directory/users/ownedDevices/read    | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.directory/users/ownedObjetos/ler    | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.directory/users/registeredDevices/read    | Leia a propriedade users.registeredDevices no Active Directory do Azure. |
| microsoft.directory/users/strongAuthentication/read    | Leia propriedades de autenticação fortes como informações de credencial do MFA. |
| microsoft.office365.exchange/allEntities/read    | Leia todos os aspectos do Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Ler securityMessages em microsoft.office365.messageCenter. |
| Microsoft.office365.protectionCenter/allEntities/Read    | Ler todos os aspectos do Centro de Proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Leia todas as propriedades padrão em microsoft.office365.securityComplianceCenter. |
| Microsoft.office365.usageReports/allEntities/Read    | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Leia propriedades padrão em todos os recursos em microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Permissões de administrador de grupos
Pode gerenciar todos os aspectos de grupos e configurações de grupo, como políticas de nomeação e expiração.

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
| microsoft.directory/groups/configurações/atualização | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="guest-inviter-permissions"></a>Permissões de convidados convidados
Pode convidar usuários convidados independentes da configuração "membros podem convidar convidados".

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
| microsoft.directory/users/ownedObjetos/ler | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

### <a name="helpdesk-administrator-permissions"></a>Permissões do administrador do helpdesk

Pode redefinir senhas para não administradores e Administradores de Assistência Técnica.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="intune-service-administrator-permissions"></a>Permissões do administrador de serviços intune

Pode gerenciar todos os aspectos do produto Intune.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/contatos/básico/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contatos/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contatos/exclusão | Exclua contatos no Azure Active Directory. |
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
| microsoft.directory/groups/configurações/atualização | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Permissões do administrador kaizala

Pode gerenciar configurações para Microsoft Kaizala.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia o Centro de Admin Office 365. |

### <a name="license-administrator-permissions"></a>Permissões do Administrador de Licenças

Pode gerenciar licenças de produto em usuários e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/asignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/useLocalização/atualização | Atualizar a propriedade users.usageLocation no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="lync-service-administrator-permissions"></a>Permissões do Administrador de Serviços Lync

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

Pode ler postagens do Message Center, mensagens de privacidade de dados, grupos, domínios e assinaturas.

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

### <a name="office-apps-administrator-permissions"></a>Permissões de administrador de aplicativos de escritório
Pode gerenciar os serviços de nuvem dos aplicativos do Office, incluindo gerenciamento de políticas e configurações, e gerenciar a capacidade de selecionar, desmarcar e publicar conteúdo de recursos "o que há de novo" nos dispositivos do usuário final.

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
| microsoft.office365.userComunicação/allEntities/allTasks | Leia e atualize a visibilidade das novas mensagens. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Permissões de suporte do Partner Tier1

Não use – não se destina para uso geral.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/contatos/básico/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contatos/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contatos/exclusão | Exclua contatos no Azure Active Directory. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/asignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/delete | Exclua usuários no Azure Active Directory. |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="partner-tier2-support-permissions"></a>Permissões de suporte do Partner Tier2

Não use – não se destina para uso geral.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/contatos/básico/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contatos/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contatos/exclusão | Exclua contatos no Azure Active Directory. |
| microsoft.diretório/domínios/todasasTarefas | Criar e excluir domínios e ler e atualizar propriedades padrão no Azure Active Directory. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/organization/basic/update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/asignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/delete | Exclua usuários no Azure Active Directory. |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="password-administrator-permissions"></a>Permissões de administrador de senhas

Pode redefinir senhas para não-administradores e administradores de senhas.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Permissões do administrador de serviço sindérdia do Power BI

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


### <a name="power-platform-administrator-permissions"></a>Permissões de administrador de plataforma de energia

Pode criar e gerenciar todos os aspectos do Microsoft Dynamics 365, PowerApps e Microsoft Flow. 

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.flow/allEntities/allTasks | Gerencie todos os aspectos do Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Gerencie todos os aspectos do PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="privileged-authentication-administrator-permissions"></a>Permissões de administrador de autenticação privilegiada

Permitido visualizar, definir e redefinir informações do método de autenticação para qualquer usuário (admin ou não-admin).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Atualize propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários da organização Office 365. Consulte a documentação online para obter mais detalhes. |

### <a name="privileged-role-administrator-permissions"></a>Permissões de administrador de funções privilegiadas

Pode gerenciar atribuições de função no Azure AD e em todos os aspectos do Gerenciamento de Identidade Privilegiada.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipais/appRoleAssignedTo/allTasks | Leia e configure servicePrincipals.appRoleAssignedPara propriedade no Azure Active Directory. |
| microsoft.directory/servicePrincipais/oAuth2PermissionGrants/allTasks | Leia e configure a propriedade ServicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.diretório/administrativeUnits/allProperties/allTasks | Criar e gerenciar unidades administrativas (incluindo membros) |
| microsoft.diretório/roleAssignments/allProperties/allTasks | Crie e gerencie atribuições de função. |
| microsoft.diretório/roleDefinições/tudoPropriedades/todas astarefas | Crie e gerencie definições de função. |

### <a name="reports-reader-permissions"></a>Reporta permissões do leitor

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

### <a name="search-administrator-permissions"></a>Permissões do administrador de pesquisa

Pode criar e gerenciar todos os aspectos das configurações do Microsoft Search.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Crie e exclua todos os recursos e leia e atualize todas as propriedades no microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Permissões do Editor de Pesquisa

Pode criar e gerenciar o conteúdo editorial, como marcadores, Q e As, locais, piso.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Crie e exclua conteúdo e leia e atualize todas as propriedades no microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Permissões do Administrador de Segurança

Pode ler informações de segurança e relatórios e gerenciar a configuração no Azure AD e no Office 365.

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
| microsoft.directory/servicePrincipais/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| Microsoft.office365.protectionCenter/allEntities/Update | Atualize todos os recursos em microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="security-operator-permissions"></a>Permissões do Operador de Segurança

Cria e gerencia eventos de segurança.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leia e configure o Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia e configure o Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Leia e configure o Security & Compliance Center. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia e configure o Windows Defender Advanced Threat Protection. |

### <a name="security-reader-permissions"></a>Permissões do Leitor de Segurança

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

### <a name="service-support-administrator-permissions"></a>Permissões do administrador de suporte de serviço

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

### <a name="sharepoint-service-administrator-permissions"></a>Permissões do administrador do serviço SharePoint

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
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read    | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Permissões do Administrador de Comunicações de Equipes

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

### <a name="teams-communications-support-engineer-permissions"></a>Permissões de engenheiro de suporte de comunicações de equipes

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

### <a name="teams-communications-support-specialist-permissions"></a>Permissões de especialista em suporte de comunicações de equipes

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

### <a name="teams-service-administrator-permissions"></a>Permissões do administrador de serviços de equipes

Pode gerenciar o serviço do Microsoft Teams.

> [!NOTE]
> Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="user-administrator-permissions"></a>Permissões de administrador de usuário
Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.directory/contatos/básico/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.directory/contatos/create | Crie contatos no Azure Active Directory. |
| microsoft.directory/contatos/exclusão | Exclua contatos no Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| microsoft.directory/groups/create | Crie grupos no Active Directory do Azure. |
| microsoft.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.directory/groups/delete | Exclua grupos no Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.directory/groups/members/update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.directory/groups/configurações/atualização | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.directory/users/asignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.directory/users/basic/update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.directory/users/create | Crie usuários no Active Directory do Azure. |
| microsoft.directory/users/delete | Exclua usuários no Azure Active Directory. |
| microsoft.directory/users/invalidAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.directory/users/password/update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="role-template-ids"></a>IDs de modelo de função

Os IDs do modelo de função são usados principalmente pelos usuários da API do Microsoft Graph ou do PowerShell.

Exibição de gráficonome | Nome de exibição do portal Azure | diretórioRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador de aplicativos | Administrador de aplicativos | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Desenvolvedor de aplicativos | Desenvolvedor de aplicativos | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de Autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrador do Azure DevOps | Administrador do Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Administrador de proteção de informações do Azure | Administrador de Proteção de Informações do Azure | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador de fluxo de usuário B2C | Administrador de fluxo de usuário B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de atributo de fluxo de usuário B2C | Administrador de atributo de fluxo de usuário B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador do conjunto de chaves B2C IEF | Administrador do conjunto de chaves B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de políticas b2C IEF | Administrador de políticas b2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de cobrança | Administrador de cobrança | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de Aplicativos de Nuvem | Administrador de aplicativos de nuvem | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de Dispositivo de Nuvem | Administrador de dispositivo em nuvem | 7698a772-787b-4ac8-901f-60d6b08affd2
Administradores de Empresa | Administrador global | 62e90394-69f5-4237-9190-012177145e10
Administrador de conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de dados de conformidade | Administrador de dados de conformidade | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrador de acesso condicional | Administrador de acesso condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador de serviços do CRM | Administrador da Dinâmica 365 | 44367163-eba1-44c3-98af-f5787879f96a
Aprovador de acesso do cofre do cliente | Aprovador de acesso do Sistema de Proteção de Dados do Cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrador de Análise de Área de Trabalho | Administrador de Análise de Área de Trabalho | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administradores de Dispositivo | Administradores de dispositivos | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Ingresso de Dispositivo | Adesão ao dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gerenciadores de Dispositivo | Gerentes de dispositivos | 2b499bcd-da44-4968-8aec-78e1674fa64d
Usuários de Dispositivo | Usuários do dispositivo | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de Diretório | Leitores de diretórios | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Contas de sincronização de diretório | Contas de sincronização de diretório | d29b2b05-8046-44ba-8758-1e26182fcf32
Gravadores de diretório | Gravadores de diretório | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de serviços do Exchange | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador do Provedor de Identidade Externa | Administrador do Provedor de Identidade Externa | be2f45a1-457d-42af-a067-6ec1fa63bc45
Leitor global | Leitor global | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Administrador de grupos | Administrador de grupos | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Emissor do Convite ao Convidado | Emissor do convite ao convidado | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador de assistência técnica | Administrador de assistência técnica | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador kaizala | Administrador kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de Licenças | Administrador de licenças | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador de serviços do Lync | Administrador do Skype for Business | 75941009-915a-4869-abe7-691bff18279e
Leitor de privacidade do Centro de Mensagens | Leitor de privacidade do centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Leitor do Centro de Mensagens | Leitor de centro de mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Administrador de aplicativos de escritório | Administrador de aplicativos de escritório | 2b745bdf-0803-4d80-aa65-822c4493daac
Suporte de camada 1 do parceiro | Suporte de camada 1 do parceiro | 4ba39ca4-527c-499a-b93d-d9b492c50246
Suporte de camada 2 do parceiro | Suporte de camada 2 do parceiro | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador de senha | Administrador de senha | 966707d0-3269-4727-9be2-8c3a10f19b9d
Administrador de serviços do Power BI | Administrador de BI de energia | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador da plataforma de energia | Administrador de plataforma de energia | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Administrador de autenticação privilegiado | Administrador de autenticação privilegiado | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador de função com privilégios | Administrador de função com privilégios | e8611ab8-c189-46e8-94e1-60213ab1f814
Leitor de Relatórios | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de pesquisa | Administrador de pesquisa | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operador de segurança | Operador de segurança | 5f222b1-57c3-48ba-8ad5-d4759f1fde6f
Leitor de segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de suporte a serviço | Administrador de suporte a serviço | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviços do SharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de Comunicações do Teams | Administrador de Comunicações do Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de Suporte de Comunicações do Teams | Engenheiro de Suporte de Comunicações do Teams | f70938a0-fc10-4177-9e90-2178f8765737
Especialista em Suporte de Comunicações do Teams | Especialista em Suporte de Comunicações do Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de Serviços do Teams | Administrador de Serviços do Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Usuário | Usuário | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador da conta de usuário | Administrador de usuários | fe930be7-5e62-47db-91af-98c3a49a38b1
Ingresso no Dispositivo no Local de Trabalho | Adesão ao dispositivo de local de trabalho | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Eles foram preteridos e serão removidos do AD do Azure no futuro.

* Administrador de Licenças AdHoc
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Criador de Usuário Verificado por Email
* Administrador de caixa de correio
* Ingresso no Dispositivo no Local de Trabalho

## <a name="roles-not-shown-in-the-portal"></a>Papéis não mostrados no portal

Nem todas as funçãos retornadas pela API PowerShell ou MS Graph são visíveis no portal Azure. A tabela a seguir organiza essas diferenças.

Nome da API | Nome do portal Azure | Observações
-------- | ------------------- | -------------
Administradores de Empresa | Administrador global | [Nome alterado para melhor clareza](directory-assign-admin-roles.md#role-template-ids)
Administrador de serviços do CRM | Administrador da Dinâmica 365 | [Reflete a marca atual do produto](directory-assign-admin-roles.md#role-template-ids)
Ingresso de Dispositivo | Preterido | [Documentação de funções depreciadas](directory-assign-admin-roles.md#deprecated-roles)
Gerenciadores de Dispositivo | Preterido | [Documentação de funções depreciadas](directory-assign-admin-roles.md#deprecated-roles)
Usuários de Dispositivo | Preterido | [Documentação de funções depreciadas](directory-assign-admin-roles.md#deprecated-roles)
Contas de sincronização de diretório | Não é mostrado porque não deve ser usado | [Documentação das contas de sincronização do diretório](directory-assign-admin-roles.md#directory-synchronization-accounts)
Gravadores de diretório | Não é mostrado porque não deve ser usado | [Documentação dos Escritores do Diretório](directory-assign-admin-roles.md#directory-writers)
Usuário Convidado | Não é mostrado porque não pode ser usado  | NA
Administrador de serviços do Lync | Administrador do Skype for Business | [Reflete a marca atual do produto](directory-assign-admin-roles.md#role-template-ids)
Suporte ao parceiro de Nível 1 | Não é mostrado porque não deve ser usado | [Documentação de suporte do Parceiro Tier1](directory-assign-admin-roles.md#partner-tier1-support)
Suporte ao parceiro de Nível 2 | Não é mostrado porque não deve ser usado | [Documentação de suporte do Nível 2 do parceiro](directory-assign-admin-roles.md#partner-tier2-support)
Administrador de impressoras | Trabalho em andamento | Trabalho em andamento
Técnico de impressora | Trabalho em andamento | Trabalho em andamento
Usuário convidado restrito | Não é mostrado porque não pode ser usado | NA
Usuário | Não é mostrado porque não pode ser usado | NA
Ingresso no Dispositivo no Local de Trabalho | Preterido | [Documentação de funções depreciadas](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir um usuário como administrador de uma assinatura do Azure, consulte [Gerenciar o acesso usando funções do Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o acesso a recursos é controlado no Microsoft Azure, consulte [Entenda as diferentes funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
