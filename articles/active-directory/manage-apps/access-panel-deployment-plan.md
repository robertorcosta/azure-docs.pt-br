---
title: Planejar uma implantação do Azure Active Directory meus aplicativos
description: Diretrizes sobre a implantação de Azure Active Directory meus aplicativos
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: 209760b51e0f3ab931fa43ef88406f15c75ac079
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832532"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>Planejar uma implantação do Azure Active Directory meus aplicativos

Azure Active Directory (AD do Azure) meus aplicativos é um portal baseado na Web que ajuda a reduzir os custos de suporte, aumentar a produtividade e a segurança e reduzir a frustração do usuário. O sistema inclui relatórios detalhados que controlam quando você acessa o sistema e notifica os administradores de uso indevido ou abuso. Para saber mais sobre como usar meus aplicativos de uma perspectiva do usuário final, consulte [a ajuda do portal de meus aplicativos](../user-help/my-apps-portal-end-user-access.md).

Usando meus aplicativos do Azure AD, você pode:

* Descubra e acesse todos os recursos conectados ao Azure AD de sua empresa, como aplicativos
* Solicitar acesso a novos aplicativos e grupos
* Gerenciar o acesso a esses recursos para outras pessoas
* Gerenciar redefinições de senha de autoatendimento e configurações de autenticação multifator do Azure AD
* Gerenciar seus dispositivos

Ele também permite que os administradores gerenciem:

* Termos de serviço
* Organizações
* Análises de acesso


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Benefícios da integração de meus aplicativos do Azure AD

Os meus aplicativos do Azure AD beneficiam as empresas das seguintes maneiras:

**Fornece uma experiência de usuário intuitiva**: meus aplicativos fornece uma única plataforma para todos os seus aplicativos conectados ao SSO (logon único) do Azure. Você tem um portal unificado para encontrar configurações existentes e novos recursos, como gerenciamento de grupo e redefinição de senha de autoatendimento, conforme eles são adicionados. A experiência intuitiva permite que os usuários retornem para o trabalho mais rápido e sejam mais produtivos, ao mesmo tempo em que reduzem sua frustração.

**Aumenta a produtividade**: todos os aplicativos de usuário em meus aplicativos têm o SSO habilitado. Habilitar o SSO em aplicativos empresariais e Microsoft 365 cria uma experiência de entrada superior ao reduzir ou eliminar prompts de entrada adicionais. Meus aplicativos usam associação dinâmica e de autoatendimento e melhoram a segurança geral do seu sistema de identidade. Meus aplicativos garantem que as pessoas certas gerenciam o acesso aos aplicativos. Meus aplicativos servem como uma página de aterrissagem coerente para que você encontre recursos rapidamente e continue as tarefas de trabalho.

**Gerencia custos**: habilitar meus aplicativos com o Azure AD pode ajudar com o dibenefício das infraestruturas locais. Ele reduz os custos de suporte fornecendo um portal consistente para localizar todos os seus aplicativos, solicitar acesso a recursos e gerenciar contas.

**Aumenta a flexibilidade e a segurança**: meus aplicativos oferece acesso à segurança e à flexibilidade que uma plataforma de nuvem fornece. Os administradores podem facilmente alterar as configurações para aplicativos e recursos e podem acomodar novos requisitos de segurança sem afetar os usuários.

**Permite o acompanhamento robusto da auditoria e do uso**: o rastreamento de auditoria e uso para todos os recursos do usuário permite que você saiba quando os usuários estão usando seus recursos e garante que você possa avaliar a segurança.

### <a name="licensing-considerations"></a>Considerações sobre licenciamento

Meus aplicativos é gratuito e não requer licenças para usar em um nível básico. No entanto, o número de objetos em seu diretório e os recursos adicionais que você deseja implantar podem exigir licenças adicionais. Alguns cenários comuns do AD do Azure que têm requisitos de licenciamento incluem os seguintes recursos de segurança:

* [Autenticação multifator do Azure AD](../authentication/concept-mfa-howitworks.md)
* [Associação baseada em grupo](../fundamentals/active-directory-manage-groups.md)
* [Redefinição de senha de autoatendimento](../authentication/tutorial-enable-sspr.md)
* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)

Consulte o [Guia de licenciamento completo do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Pré-requisitos para implantar meus aplicativos do Azure AD

Conclua os seguintes pré-requisitos antes de iniciar este projeto:

* [Integrar SSO de aplicativo](./plan-sso-deployment.md)
* [Gerenciar a infraestrutura de usuário e grupo do Azure AD](../fundamentals/active-directory-manage-groups.md)

## <a name="plan-azure-ad-my-apps-deployment"></a>Planejar a implantação de meus aplicativos do Azure AD

A tabela a seguir descreve os principais casos de uso para uma implantação de meus aplicativos:

| Área| Descrição |
| - | - |
| Acesso| Meu Portal de aplicativos é acessível de dispositivos corporativos e pessoais na rede corporativa. |
|Acesso | Meu Portal de aplicativos é acessível de dispositivos corporativos fora da rede corporativa. |
| Auditoria| Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias. |
| Governança| O ciclo de vida das atribuições de usuário para aplicativos e grupos conectados ao Azure AD é definido e monitorado. |
| Segurança| O acesso aos recursos é controlado por meio de atribuições de usuário e de grupo. Somente usuários autorizados podem gerenciar o acesso a recursos. |
| Desempenho| As linhas do tempo de propagação de atribuição de acesso são documentadas e monitoradas. |
| Experiência de usuário| Os usuários estão cientes dos recursos de meus aplicativos e de como usá-los.|
| Experiência de usuário| Os usuários podem gerenciar seu acesso a aplicativos e grupos.|
| Experiência de usuário| Os usuários podem gerenciar suas contas. |
| Experiência de usuário| Os usuários estão cientes da compatibilidade do navegador. |
| Suporte| Os usuários podem encontrar suporte para meus problemas de aplicativos. |


> [!TIP]
> Meus aplicativos podem ser usados com URLs internas da empresa durante o uso remoto do proxy de aplicativo. Para saber mais, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Práticas recomendadas para implantar meus aplicativos do Azure AD

A funcionalidade de meus aplicativos pode ser habilitada gradualmente. Recomendamos a seguinte ordem de implantação:

1. Meus aplicativos
   * Inicializador de aplicativo
   * Gerenciamento de aplicativo de autoatendimento
   * integração do Microsoft 365

1. Descoberta de aplicativo de autoatendimento
   * Redefinição de senha de autoatendimento
   * Configurações de autenticação multifator
   * Gerenciamento de dispositivo
   * Termos de uso
   * Gerenciar organizações

1. Meus Grupos
   * Gerenciamento de grupo de autoatendimento
1. Análises de acesso
   * Gerenciamento de análise de acesso

Começar com meus aplicativos apresenta os usuários ao portal como um local comum para acessar recursos. A adição da descoberta de aplicativos de autoatendimento se baseia na experiência de meus aplicativos. Meus grupos e revisões de acesso se baseiam nos recursos de autoatendimento.

### <a name="plan-configurations-for-azure-my-apps"></a>Planejar configurações para meus aplicativos do Azure

A tabela a seguir lista várias configurações importantes de meus aplicativos e os valores típicos que você pode usar:

| Configuração| Valores típicos |
| - | - |
| Determinar os grupos piloto| Identifique o grupo de segurança do Azure AD a ser usado e verifique se todos os membros do piloto fazem parte do grupo. |
| Determine o grupo ou grupos a serem habilitados para produção.| Identifique os grupos de segurança do Azure AD ou os grupos de Active Directory sincronizados com o Azure AD, a serem usados. Verifique se todos os membros do piloto fazem parte do grupo. |
| Permitir que os usuários usem o SSO para determinados tipos de aplicativos| SSO Federado, OAuth, SSO de senha, proxy de aplicativo |
| Permitir que os usuários usem a redefinição de senha de autoatendimento | Sim |
| Permitir que os usuários usem a autenticação multifator| Sim |
| Permitir que os usuários usem o gerenciamento de grupo de autoatendimento para determinados tipos de grupos| Grupos de segurança, grupos de Microsoft 365 |
| Permitir que os usuários usem o gerenciamento de aplicativo de autoatendimento| Sim |
| Permitir que os usuários usem as revisões de acesso| Sim |

### <a name="plan-consent-strategy"></a>Planejar estratégia de consentimento

Os usuários ou administradores devem consentir os termos de uso e as políticas de privacidade do aplicativo. Se possível, dadas suas regras de negócio, use o consentimento do administrador, o que oferece aos usuários uma experiência melhor.

Para usar o consentimento do administrador, você deve ser um administrador global da organização e os aplicativos devem ser:

* Registrado em sua organização
* Registrado em outra organização do Azure AD e consentiu anteriormente por pelo menos um usuário

Para obter mais informações, consulte [Configurar a maneira como os usuários finais concordam com um aplicativo no Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md) e se as funções de Stakeholder no projeto são bem compreendidas.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe de forma proativa aos usuários como e quando sua experiência será alterada e como obter suporte, se necessário.

Embora meus aplicativos normalmente não criem problemas de usuário, é importante se preparar. Crie guias e uma lista de todos os recursos para sua equipe de suporte antes do início.

#### <a name="communications-templates"></a>Modelos de comunicação

A Microsoft fornece [modelos personalizáveis para emails e outras comunicações](https://aka.ms/APTemplates) para meus aplicativos. Você pode adaptar esses ativos para uso em outros canais de comunicação, conforme apropriado para sua cultura corporativa.

## <a name="plan-your-sso-configuration"></a>Planejar sua configuração de SSO

Quando um usuário entra em um aplicativo, ele passa por um processo de autenticação e precisa provar quem eles estão. Sem o SSO, uma senha é armazenada no aplicativo e o usuário precisa saber essa senha. Com o SSO, as credenciais dos usuários são passadas para o aplicativo, portanto, não precisam reinserir senhas para cada aplicativo.

Para iniciar aplicativos em meus aplicativos, o SSO deve estar habilitado. O Azure AD dá suporte a várias opções de SSO. Para saber mais, consulte [Opções de logon único no Azure ad](sso-options.md).

> [!NOTE]
> Para saber mais sobre como usar o Azure AD como um provedor de identidade para um aplicativo, consulte a [série de guias de início rápido sobre o gerenciamento de aplicativos](view-applications-portal.md).

Para obter a melhor experiência com a página meus aplicativos, comece com a integração de aplicativos em nuvem que estão disponíveis para SSO Federado. O SSO Federado permite que os usuários tenham uma experiência consistente com um único clique em seu aplicativo iniciando superfícies e tende a ser mais robusto no controle de configuração.

Use o SSO Federado com o Azure AD (OpenID Connect/SAML) quando um aplicativo oferecer suporte a ele, em vez de SSO baseado em senha e ADFS.

Para obter mais informações sobre como implantar e configurar seus aplicativos SaaS, consulte o [plano de implantação de SSO do SaaS](./plan-sso-deployment.md).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planejar a implantação da extensão do navegador meus aplicativos

Quando os usuários entram em aplicativos de SSO baseados em senha, eles precisam instalar e usar a extensão de entrada segura dos meus aplicativos. A extensão executa um script que transmite a senha para o formulário de entrada do aplicativo. Os usuários são solicitados a instalar a extensão quando iniciam o aplicativo SSO baseado em senha pela primeira vez. Mais informações sobre a extensão podem ser encontradas nesta documentação sobre como [instalar a extensão do navegador de meus aplicativos]().

Se você precisar integrar aplicativos SSO baseados em senha, defina um mecanismo para implantar a extensão em escala com os [navegadores com suporte](../user-help/my-apps-portal-end-user-access.md). As opções incluem:

* [Política de Grupo para o Internet Explorer]()
* [Configuration Manager para o Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [Download e configuração controlados pelo usuário para Chrome, Firefox, Microsoft Edge ou IE](../user-help/my-apps-portal-end-user-access.md)

Os usuários que não usam aplicativos SSO baseados em senha também se beneficiam da extensão. Esses benefícios incluem a capacidade de iniciar qualquer aplicativo de sua barra de pesquisa, localizar acesso a aplicativos usados recentemente e ter um link para a página meus aplicativos.

#### <a name="plan-for-mobile-access"></a>Planejar o acesso móvel

Um navegador protegido com a política do Intune (Microsoft Edge ou Intune Managed Browser) é necessário para usuários móveis que iniciam aplicativos de SSO baseados em senha. Um navegador protegido por política permite a transferência da senha salva para o aplicativo. O Microsoft Edge ou o navegador gerenciado fornece um conjunto de recursos de proteção de dados da Web. Você também pode usar o Microsoft Edge para cenários empresariais em dispositivos iOS e Android. O Microsoft Edge dá suporte aos mesmos cenários de gerenciamento que o Intune Managed Browser e melhora a experiência do usuário. Saiba mais: [gerenciar o acesso à Web usando um navegador Microsoft Intune protegido por política](/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planejar a implantação de meus aplicativos

A base de meus aplicativos é o portal do inicializador de aplicativos, que os usuários acessam em [https://myapps.microsoft.com](https://myapps.microsoft.com/) . A página meus aplicativos fornece aos usuários um único lugar para iniciar seu trabalho e obter seus aplicativos necessários. Aqui, os usuários encontram uma lista de todos os aplicativos aos quais têm acesso de logon único. 

> [!NOTE]
> Os mesmos aplicativos serão mostrados no iniciador do aplicativo Microsoft 365.

Planeje a ordem na qual você adicionará aplicativos ao iniciador meus aplicativos e decida se irá distribuí-los gradualmente ou todos de uma vez. Para fazer isso, crie um inventário de aplicativos listando o tipo de autenticação e todas as integrações de SSO existentes para cada aplicativo.

#### <a name="add-applications-to-the-my-apps-panel"></a>Adicionar aplicativos ao painel meus aplicativos

Qualquer aplicativo habilitado para SSO do Azure AD pode ser adicionado ao inicializador meus aplicativos. Outros aplicativos são adicionados usando a opção de SSO vinculado. Você pode configurar um bloco de aplicativo que se vincule à URL do seu aplicativo Web existente. O SSO vinculado permite que você comece a direcionar usuários para o portal meus aplicativos sem migrar todos os aplicativos para o SSO do Azure AD. Você pode migrar gradualmente para os aplicativos configurados pelo SSO do Azure AD sem interromper a experiência dos usuários.

#### <a name="use-my-apps-collections"></a>Usar coleções de meus aplicativos

Por padrão, todos os aplicativos são listados juntos em uma única página. Mas você pode usar coleções para agrupar aplicativos relacionados e apresentá-los em uma guia separada, facilitando sua localização. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicativos para funções de trabalho específicas, tarefas, projetos e assim por diante. Para obter informações, consulte [como usar coleções de meus aplicativos](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planejar se deseja usar meus aplicativos ou um portal existente

Os usuários podem já ter um aplicativo ou portal diferente de meus aplicativos. Nesse caso, decida se deseja dar suporte a ambos os portais ou usar apenas um.

Se um portal existente já estiver sendo usado como um ponto de partida para os usuários, você poderá integrar a funcionalidade de aplicativos usando as URLs de acesso do usuário. As URLs de acesso do usuário funcionam como links diretos para os aplicativos disponíveis no portal meus aplicativos. Essas URLs podem ser inseridas em qualquer site existente. Quando um usuário seleciona o link, ele abre o aplicativo no portal meus aplicativos.

Você pode encontrar a propriedade URL de acesso do usuário na área **Propriedades** do aplicativo na portal do Azure.

![Uma captura de tela do painel de aplicativos](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planejar descoberta e acesso de aplicativos de autoatendimento

Depois que um conjunto principal de aplicativos é implantado na página meus aplicativos de um usuário, você deve habilitar os recursos de gerenciamento de aplicativo de autoatendimento. A descoberta de aplicativos de autoatendimento permite que os usuários:

* Encontre novos aplicativos para adicionar aos meus aplicativos. 
* Adicione aplicativos opcionais que eles podem não saber que precisam durante a instalação.

Os fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para acessar aplicativos. Os usuários que são aprovadores receberão notificações no portal meus aplicativos quando houver uma solicitação pendente de acesso ao aplicativo.

## <a name="plan-self-service-group-membership"></a>Planejar Associação de grupo de autoatendimento 

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos de Microsoft 365 no Azure AD. O proprietário do grupo pode aprovar ou negar solicitações de associação e delegar o controle de associação de grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas para email.

Para planejar a associação de grupo de autoatendimento, determine se você permitirá que todos os usuários em sua organização criem e gerenciem grupos ou apenas um subconjunto de usuários. Se você estiver permitindo um subconjunto de usuários, precisará configurar um grupo ao qual essas pessoas são adicionadas. Consulte [Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory](../enterprise-users/groups-self-service-management.md) para obter detalhes sobre como habilitar esses cenários.

## <a name="plan-reporting-and-auditing"></a>Planejar relatórios e auditoria

O Azure AD fornece [relatórios que oferecem informações técnicas e de negócios](../reports-monitoring/overview-reports.md). Trabalhe com seus proprietários de aplicativos técnicos e de negócios para assumir a propriedade desses relatórios e consumi-los regularmente. A tabela a seguir fornece alguns exemplos de cenários de relatório típicos.

| Exemplo | gerenciar riscos| Aumentar a produtividade| Governança e conformidade |
|  - |- | - | - |
| Tipos de relatório|  Permissões e uso do aplicativo| Atividade de provisionamento de conta| Revisar quem está acessando os aplicativos |
| Ações potenciais| Acesso de auditoria; revogar permissões| Corrigir quaisquer erros de provisionamento| Revogar acesso |

O Azure AD mantém a maioria dos dados de auditoria por 30 dias. Os dados estão disponíveis por meio do portal de administração do Azure ou da API para que você faça o download em seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os logs de auditoria para acesso ao aplicativo estão disponíveis por 30 dias. Se a auditoria de segurança em sua empresa exigir maior retenção, os logs precisarão ser exportados para uma ferramenta SIEM (Event Information and Management, gerenciamento de informações de segurança), como Splunk ou ArcSight.

Para backups de auditoria, relatórios e recuperação de desastres, documente a frequência necessária de download, o que é o sistema de destino e quem é responsável por gerenciar cada backup. Talvez você não precise de backups de auditoria e de relatórios separados. O backup de recuperação de desastre deve ser uma entidade separada.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Implantar aplicativos no painel meus aplicativos dos usuários

Depois que um aplicativo tiver sido configurado para SSO, os grupos receberão acesso. Os usuários nos grupos atribuídos terão acesso, e eles verão o aplicativo em meus aplicativos e no iniciador do aplicativo Microsoft 365.

Consulte [atribuir usuários e grupos a um aplicativo no Active Directory](./assign-user-or-group-access-portal.md).

Se, durante o teste ou a implantação, você quiser adicionar os grupos, mas ainda não permitir que os aplicativos sejam mostrados em meus aplicativos, consulte [ocultar um aplicativo da experiência do usuário em Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>Implantar aplicativos Microsoft 365 em meus aplicativos

Para aplicativos Microsoft 365, os usuários recebem uma cópia do Office com base em licenças atribuídas a eles. Um pré-requisito para o acesso aos aplicativos do Office é que os usuários recebam as licenças corretas vinculadas aos aplicativos do Office. Quando você atribui uma licença a um usuário, ele verá automaticamente os aplicativos associados à licença na página meus aplicativos e no iniciador do aplicativo Microsoft 365.

Se você quiser ocultar um conjunto de aplicativos do Office de usuários, há uma opção para ocultar aplicativos do portal meus aplicativos e, ao mesmo tempo, permitir o acesso do portal de Microsoft 365. Saiba mais: [ocultar um aplicativo da experiência do usuário em Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>Implantar recursos de autoatendimento de aplicativo

O acesso de aplicativo de autoatendimento permite que os usuários autodescubram e solicitem acesso a aplicativos. Os usuários têm a liberdade de acessar os aplicativos de que precisam sem passar por um grupo de ti a cada vez para solicitar acesso. Quando um usuário solicita acesso e é aprovado, seja automaticamente ou manualmente por um proprietário de aplicativo, eles são adicionados a um grupo no back-end. O relatório está habilitado em quem solicitou, aprovou ou removeu o acesso e oferece controle sobre o gerenciamento das funções atribuídas.

Você pode delegar a aprovação de solicitações de acesso do aplicativo para os aprovadores de negócios. O aprovador de negócios pode definir as senhas de acesso do aplicativo na página meus aplicativos do aprovador de negócios.

Saiba mais: [como usar o acesso ao aplicativo de autoatendimento](access-panel-manage-self-service-access.md).

## <a name="validate-your-deployment"></a>Validar sua implantação

Verifique se a implantação de meus aplicativos foi exaustivamente testada e se um plano de reversão está em vigor.

Os testes a seguir devem ser conduzidos com dispositivos de propriedade corporativa e dispositivos pessoais. Esses casos de teste também devem refletir seus casos de uso de negócios. A seguir estão alguns casos com base nos requisitos de negócios de exemplo neste documento e em cenários técnicos típicos. Adicione outras pessoas específicas às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Exemplos de casos de teste de acesso de SSO de aplicativo:


| Caso comercial| Resultado esperado |
| - | -|
| O usuário entra no portal meus aplicativos| O usuário pode entrar e ver seus aplicativos |
| O usuário inicia um aplicativo SSO Federado| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo de SSO de senha pela primeira vez| O usuário precisa instalar a extensão meus aplicativos |
| O usuário inicia um aplicativo de SSO de senha uma hora subsequente| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo do portal Microsoft 365| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo do Managed Browser| O usuário é conectado automaticamente ao aplicativo |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exemplos de casos de teste de recursos de autoatendimento do aplicativo

| Caso comercial| Resultado esperado |
| - | - |
| O usuário pode gerenciar a associação ao aplicativo| O usuário pode adicionar/remover membros que têm acesso ao aplicativo |
| O usuário pode editar o aplicativo| O usuário pode editar a descrição e as credenciais do aplicativo para aplicativos de SSO de senha |

### <a name="rollback-steps"></a>Etapas de reversão

É importante planejar o que fazer se a implantação não funcionar conforme o planejado. Se a configuração de SSO falhar durante a implantação, você deve entender como [solucionar problemas de SSO](../hybrid/tshoot-connect-sso.md) e reduzir o impacto para os usuários. Em circunstâncias extremas, talvez seja necessário [reverter o SSO](../manage-apps/plan-sso-deployment.md#rollback-process).


## <a name="manage-your-implementation"></a>Gerenciar sua implementação

Use a função menos privilegiada para realizar uma tarefa necessária dentro do Azure Active Directory. [Examine as diferentes funções que estão disponíveis](../roles/permissions-reference.md) e escolha a correta para resolver suas necessidades para cada pessoa para este aplicativo. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Personas| Funções| Função do Azure AD  |
| - | -| -|
| Administrador de assistência técnica| Suporte da camada 1| Nenhum |
| Administrador de identidade| Configurar e depurar quando os problemas afetam o Azure AD| Administrador global |
| Administrador do aplicativo| Atestado de usuário no aplicativo, configuração de usuários com permissões| Nenhum |
| Administradores de infraestrutura| Proprietário da substituição do certificado| Administrador global |
| Proprietário da empresa/participante| Atestado de usuário no aplicativo, configuração de usuários com permissões| Nenhum |

Você pode usar [Privileged Identity Management](../privileged-identity-management/pim-configure.md) para gerenciar suas funções para fornecer auditoria adicional, controle e revisão de acesso para usuários com permissões de diretório.

## <a name="next-steps"></a>Próximas etapas
[Planejar uma implantação da autenticação multifator do Azure AD](../authentication/howto-mfa-getstarted.md)
