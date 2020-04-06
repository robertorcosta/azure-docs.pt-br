---
title: Considerações de implantação para autenticação multifatorial do Azure
description: Conheça as considerações de implantação e a estratégia para a implementação bem-sucedida da Autenticação Multifatorial do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a70c6ae3ebc7f5b39550508594bd4d4907e68a67
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667335"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Planeje uma implantação de autenticação multifatorial do Azure

As pessoas estão se conectando aos recursos organizacionais em cenários cada vez mais complicados. As pessoas se conectam a partir de dispositivos pessoais e públicos de propriedade da organização dentro e fora da rede corporativa usando telefones inteligentes, tablets, PCs e laptops, muitas vezes em várias plataformas. Neste mundo sempre conectado, multi-dispositivos e multiplataforma, a segurança das contas de usuário é mais importante do que nunca. As senhas, não importa sua complexidade, usadas em dispositivos, redes e plataformas não são mais suficientes para garantir a segurança da conta de usuário, especialmente quando os usuários tendem a reutilizar senhas entre contas. Phishing sofisticado e outros ataques de engenharia social podem resultar em nomes de usuário e senhas sendo postados e vendidos na dark web.

[O Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) ajuda a proteger o acesso a dados e aplicativos. Ele fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar [o Acesso Condicional](../conditional-access/overview.md) para fazer com que a solução se adeque às suas necessidades específicas.

Este guia de implantação mostra como planejar e, em seguida, testar um lançamento de Autenticação Multifatorial do Azure.

Para ver rapidamente a Autenticação Multifatorial do Azure em ação e, em seguida, voltar a entender considerações adicionais de implantação:

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Microsoft Azure](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implantação da Autenticação Multifatorial do Azure, existem itens pré-requisitos que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| **Ambiente de** identidade somente na nuvem com autenticação moderna | **Sem tarefas adicionais de pré-requisitos** |
| **Cenários de** identidade híbrida | [O Azure AD Connect](../hybrid/whatis-hybrid-identity.md) é implantado e as identidades dos usuários são sincronizadas ou federadas com os serviços de domínio do Active Directory no local com o Azure Active Directory. |
| Aplicativos legados no local publicados para acesso à nuvem | O [Proxy de aplicativo](../manage-apps/application-proxy.md) Azure AD é implantado. |
| Usando O Azure MFA com autenticação RADIUS | Um [NPS (Network Policy Server, servidor de diretiva de rede)](howto-mfa-nps-extension.md) é implantado. |
| Os usuários têm o Microsoft Office 2010 ou anterior, ou o Apple Mail para iOS 11 ou anterior | Atualize para [o Microsoft Office 2013 ou posterior](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e apple mail para iOS 12 ou posterior. O Acesso Condicional não é suportado por protocolos de autenticação legados. |

## <a name="plan-user-rollout"></a>Implantação do usuário do plano

Seu plano de implantação do MFA deve incluir uma implantação piloto seguida de ondas de implantação que estejam dentro de sua capacidade de suporte. Inicie seu rollout aplicando suas políticas de Acesso Condicional a um pequeno grupo de usuários piloto. Depois de avaliar o efeito sobre os usuários piloto, o processo usado e os comportamentos de registro, você pode adicionar mais grupos à política ou adicionar mais usuários aos grupos existentes.

### <a name="user-communications"></a>Comunicações com o usuário

É fundamental informar os usuários, em comunicações planejadas, sobre as próximas alterações, os requisitos de registro do Azure MFA e quaisquer ações necessárias do usuário. Recomendamos que as comunicações sejam desenvolvidas em conjunto com representantes de dentro de sua organização, como departamentos de Comunicação, Gestão de Mudanças ou Recursos Humanos.

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates) e [documentação do usuário final](../user-help/security-info-setup-signin.md) para ajudar a elaborar suas comunicações. Você pode enviar [https://myprofile.microsoft.com](https://myprofile.microsoft.com) os usuários para se registrar diretamente selecionando os links **de Informações de Segurança** nessa página.

## <a name="deployment-considerations"></a>Considerações de implantação

A autenticação multifatorial do Azure é implantada aplicando políticas com acesso condicional. Uma [política de acesso condicional](../conditional-access/overview.md) pode exigir que os usuários realizem autenticação multifatorial quando determinados critérios são atendidos, tais como:

* Todos os usuários, um usuário específico, membro de um grupo ou função atribuída
* Aplicativo em nuvem específico sendo acessado
* Plataforma de dispositivos
* Estado do dispositivo
* Localização da rede ou endereço IP geolocalizado
* Aplicativos cliente
* Risco de login (requer proteção de identidade)
* Dispositivo em conformidade
* Dispositivo adicionado ao Azure AD híbrido
* Aplicação do cliente aprovado

Use os pôsteres e modelos de e-mail personalizáveis em [materiais de distribuição de autenticação multifatorial](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) para implementar a autenticação multifatorial à sua organização.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Habilitar Autenticação Multifator com acesso condicional

As políticas de acesso condicional aplicam o registro, exigindo que os usuários não registrados completem o registro no primeiro login, uma importante consideração de segurança.

[O Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribui tanto para uma política de registro quanto para políticas automatizadas de detecção e remediação de riscos para a história de Autenticação Multifatorial do Azure. As políticas podem ser criadas para forçar alterações de senha quando há uma ameaça de identidade comprometida ou requerem MFA quando um login é considerado arriscado pelos [seguintes eventos:](../reports-monitoring/concept-risk-events.md)

* Credenciais vazadas
* Entradas de endereços IP anônimos
* Viagem impossível a locais atípicos
* Entradas de locais desconhecidos
* Entradas de dispositivos infectados
* Logins de endereços IP com atividades suspeitas

Algumas das detecções de risco detectadas pelo Azure Active Directory Identity Protection ocorrem em tempo real e algumas requerem processamento off-line. Os administradores podem optar por bloquear usuários que exibem comportamentos de risco e remediar manualmente, exigir uma alteração de senha ou exigir uma autenticação multifatorial como parte de suas políticas de Acesso Condicional.

## <a name="define-network-locations"></a>Definir locais da rede

Recomendamos que as organizações usem o Acesso Condicional para definir sua rede usando [locais nomeados.](../conditional-access/location-condition.md#named-locations) Se sua organização estiver usando proteção de identidade, considere usar políticas baseadas em riscos em vez de locais nomeados.

### <a name="configuring-a-named-location"></a>Configuração de um local nomeado

1. Abra **o Diretório Ativo do Azure** no portal Azure
2. selecione **Segurança**
3. Em **Gerenciar,** escolha **locais nomeados**
4. Selecione **novo local**
5. No campo **Nome,** forneça um nome significativo
6. Selecione se você está definindo o local usando *faixas IP* ou *Países/Regiões*
   1. Se estiver usando *faixas ip*
      1. Decida se *marca como local confiável.* Conectar de um local confiável diminui o risco de entrada do usuário. Só marque esse local como confiável se você souber que os intervalos de IP inseridos são estabelecidos e confiáveis em sua organização.
      2. Especificar as faixas de IP
   2. Se usar *Países/Regiões*
      1. Expanda o menu suspenso e selecione os países ou regiões que deseja definir para este local nomeado.
      2. Decida se *deve incluir áreas desconhecidas.* Áreas desconhecidas são endereços IP que não podem ser mapeados para um país/região.
7. Selecione **Criar**

## <a name="plan-authentication-methods"></a>Métodos de autenticação de planos

Os administradores podem escolher os [métodos de autenticação](../authentication/concept-authentication-methods.md) que desejam disponibilizar para os usuários. É importante permitir mais do que um único método de autenticação para que os usuários tenham um método de backup disponível no caso de seu método principal não estiver disponível. Os seguintes métodos estão disponíveis para os administradores habilitarem:

### <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

Uma notificação push é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel. O usuário visualiza a notificação e seleciona **Aprovar** para concluir a verificação. As notificações push através de um aplicativo móvel fornecem a opção menos intrusiva para os usuários. Eles também são a opção mais confiável e segura porque eles usam uma conexão de dados em vez de telefonia.

> [!NOTE]
> Se sua organização tem funcionários trabalhando ou viajando para a China, a notificação através do método **de aplicativo móvel** em dispositivos **Android** não funciona naquele país. Métodos alternativos devem ser disponibilizados para esses usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

Um aplicativo móvel como o aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de entrada. A opção de aplicativo móvel pode ser usada se o telefone tem ou não um sinal de dados ou celular.

### <a name="call-to-phone"></a>Ligue para o telefone

Uma chamada de voz automatizada é feita ao usuário. O usuário atende a chamada **#** e pressiona no teclado do telefone para aprovar sua autenticação. Chamada para telefone é um ótimo método de backup para notificação ou código de verificação de um aplicativo móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

Uma mensagem de texto que contém um código de verificação é enviada ao usuário, o usuário é solicitado a inserir o código de verificação na interface de login.

### <a name="choose-verification-options"></a>Escolher opções de verificação

1. Navegue até **do Azure Active Directory**, **Usuários**, **Autenticação Multifator**.

   ![Acessando o portal de Autenticação Multifator na folha de usuários do Azure Active Directory no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Na nova guia que abre navegação nas **configurações de serviço**.
1. Em **opções de verificação**, verifique todas as caixas para os métodos disponíveis aos usuários.

   ![Configurar métodos de verificação na guia configurações do serviço de Autenticação Multifator](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Salvar**.
1. Feche a guia **de configurações de serviço.**

## <a name="plan-registration-policy"></a>Política de registro de planos

Os administradores devem determinar como os usuários registrarão seus métodos. As organizações devem [habilitar a nova experiência de registro combinada](howto-registration-mfa-sspr-combined.md) para o Azure MFA e o reset de senha de autoatendimento (SSPR). O SSPR permite que os usuários reconfigurem sua senha de forma segura usando os mesmos métodos que usam para autenticação multifatorial. Recomendamos esse registro combinado, atualmente em pré-visualização pública, pois é uma ótima experiência para os usuários, com a capacidade de se cadastrar uma vez para ambos os serviços. Habilitar os mesmos métodos para SSPR e Azure MFA permitirá que seus usuários sejam registrados para usar ambos os recursos.

### <a name="registration-with-identity-protection"></a>Registro com Proteção de Identidade

Se sua organização estiver usando o Azure Active Directory Identity Protection, [configure a política de registro do MFA](../identity-protection/howto-mfa-policy.md) para solicitar que seus usuários registrem na próxima vez que eles fizerem login de forma interativa.

### <a name="registration-without-identity-protection"></a>Registro sem Proteção de Identidade

Se sua organização não tiver licenças que habilitam a Proteção de Identidade, os usuários serão solicitados a registrar na próxima vez que o MFA for exigido no login. Os usuários não podem ser registrados no MFA se não usarem aplicativos protegidos com MFA. É importante que todos os usuários se registrem para que os maus atores não possam adivinhar a senha de um usuário e se registrar em seu nome, assumindo efetivamente o controle da conta.

#### <a name="enforcing-registration"></a>Fiscalização de registro

Usando as seguintes etapas, uma política de acesso condicional pode forçar os usuários a se registrarem para autenticação multifatorial

1. Crie um grupo, adicione todos os usuários não registrados no momento.
2. Usando o Acesso Condicional, imponha a autenticação multifatorial para este grupo para acesso a todos os recursos.
3. Periodicamente, reavalie a adesão ao grupo e remova os usuários que se registraram no grupo.

Você pode identificar usuários Azure MFA registrados e não registrados com comandos PowerShell que dependem do [módulo MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificar usuários registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar usuários não registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários de MFA por usuário para MFA baseado em Acesso Condicional

Se seus usuários foram habilitados usando a autenticação multifatorial do Azure ativada e aplicada, o PowerShell a seguir pode ajudá-lo a fazer a conversão para autenticação multifatorial baseada no Acesso Condicional.

Execute este PowerShell em uma janela `.PS1` ISE ou salve como um arquivo para ser executado localmente.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Recentemente mudamos o comportamento e o script PowerShell acima em conformidade. Anteriormente, o script salvava os métodos mfa, desativava o MFA e restaurava os métodos. Isso não é mais necessário agora que o comportamento padrão para desabilitação não limpa os métodos.

## <a name="plan-conditional-access-policies"></a>Planejar políticas de acesso condicional

Para planejar sua estratégia de política de acesso condicional, que determinará quando o MFA e outros controles são necessários, consulte [O que é acesso condicional no Diretório Ativo do Azure?](../conditional-access/overview.md).

É importante que você evite ser inadvertidamente bloqueado fora do seu inquilino Azure AD. Você pode mitigar o impacto dessa falta inadvertida de acesso administrativo criando duas ou mais contas de [acesso de emergência em seu inquilino](../users-groups-roles/directory-emergency-access.md) e excluindo-as de sua política de Acesso Condicional.

### <a name="create-conditional-access-policy"></a>Criar política de Acesso Condicional

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
   ![Crie uma política de acesso condicional para habilitar o MFA para usuários do portal Azure em grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. .Forneça um nome significativo para a política.
1. Em **usuários e grupos:**
   * Na guia **Incluir**, selecione o botão de opção **Todos os usuários**
   * Na guia **Excluir,** verifique a caixa para **Usuários e grupos e** escolha suas contas de acesso de emergência.
   * Clique em **Concluído**.
1. Em **aplicativos cloud,** selecione o botão de rádio **All cloud apps.**
   * OPCIONALMENTE: Na guia **Excluir**, escolha os aplicativos de nuvem que sua organização não exige o MFA.
   * Clique em **Concluído**.
1. Na seção **Condições:**
   * OPCIONALMENTE: Se você tiver habilitado o Azure Identity Protection, será possível optar por avaliar o risco de entrada como parte da política.
   * OPCIONALMENTE: Se você tiver configurado locais confiáveis ou localizações nomeadas, será possível especificar para incluir ou excluir esses locais da política.
1. Em **Grant,** certifique-se de que o botão de rádio **de acesso grant** esteja selecionado.
    * Verifique a caixa para **exigir autenticação multifatorial**.
    * Clique em **Selecionar**.
1. Pule a seção **sessão.**
1. Defina o **alternar de diretiva Ativar** para **.**
1. Clique em **Criar**.

## <a name="plan-integration-with-on-premises-systems"></a>Planejar a integração com sistemas locais

Alguns aplicativos legados e locais que não autenticam diretamente contra o Azure AD exigem etapas adicionais para usar o MFA, incluindo:

* Aplicativos legados no local, que precisarão usar proxy do aplicativo.
* Aplicativos RADIUS no local, que precisarão usar adaptador MFA com servidor NPS.
* Aplicações AD FS no local, que precisarão usar adaptador MFA com AD FS 2016 ou mais novos.

Aplicativos que autenticam diretamente com o Azure AD e possuem autenticação moderna (WS-Fed, SAML, OAuth, OpenID Connect) podem fazer uso das políticas de Acesso Condicional diretamente.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Use o Azure MFA com proxy de aplicativo Azure AD

Os aplicativos que residem no local podem ser publicados no seu inquilino Azure AD via [Azure AD Application Proxy](../manage-apps/application-proxy.md) e podem tirar proveito da Autenticação Multifatorial do Azure se estiverem configurados para usar a pré-autenticação do Azure AD.

Esses aplicativos estão sujeitos a políticas de acesso condicional que aplicam a autenticação multifatorial do Azure, assim como qualquer outro aplicativo integrado ao Azure AD.

Da mesma forma, se a autenticação multifatorial do Azure for aplicada para todos os logins de usuário, os aplicativos locais publicados com o Azure AD Application Proxy serão protegidos.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrando a autenticação multifatorial do Azure com o servidor de diretiva de rede

A extensão do Servidor de Políticas de Rede (NPS) para o Azure MFA adiciona recursos MFA baseados em nuvem à sua infraestrutura de autenticação usando os seus servidores existentes. Com a extensão NPS, você pode adicionar a chamada telefônica, mensagem de texto ou verificação de aplicativo de telefone ao fluxo de autenticação existente. Essa integração tem as seguintes limitações:

* Com o protocolo CHAPv2, apenas notificações push do aplicativo autenticador e chamada de voz são suportadas.
* As políticas de acesso condicional não podem ser aplicadas.

A extensão NPS atua como um adaptador entre o RADIUS e o Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para proteger as [conexões](howto-mfa-nps-extension-rdg.md) [VPN,](howto-mfa-nps-extension-vpn.md)Remote Desktop Gateway ou outros aplicativos capazes de RADIUS. Os usuários que se cadastrarem no Azure MFA neste ambiente serão desafiados para todas as tentativas de autenticação, a falta de políticas de Acesso Condicional significa que o MFA é sempre necessário.

#### <a name="implementing-your-nps-server"></a>Implementando seu servidor NPS

Se você tiver uma instância NPS implantada e em uso já, faça referência [integrar sua infra-estrutura NPS existente com a autenticação multifatorial do Azure](howto-mfa-nps-extension.md). Se você estiver configurando nps pela primeira vez, consulte [O NPS (Network Policy Server, servidor de](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) políticas de rede) para obter instruções. A orientação de solução de problemas pode ser encontrada no artigo [Resolver mensagens de erro da extensão NPS para autenticação multifatorial do Azure](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Prepare NPS para usuários que não estão inscritos no MFA

Escolha o que acontece quando usuários que não estão inscritos no MFA tentam autenticar. Use a `REQUIRE_USER_MATCH` configuração de `HKLM\Software\Microsoft\AzureMFA` registro no caminho do registro para controlar o comportamento do recurso. Esta configuração tem uma única opção de configuração.

| Chave | Valor | Padrão |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERDADEIRO / FALSO | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não estiver inscrito na MFA. Os efeitos da alteração desta configuração estão listados na tabela abaixo.

| Configurações | Status do Usuário MFA | Efeitos |
| --- | --- | --- |
| A chave não existe | Não matriculados | Desafio MFA não tem sucesso |
| Valor definido como True / not set | Não matriculados | Desafio MFA não tem sucesso |
| Conjunto de chaves para Falso | Não matriculados | Autenticação sem MFA |
| Conjunto de chaves para Falso ou Verdadeiro | Registrado | Deve autenticar com MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integre-se aos Serviços da Federação de Diretórios Ativos

Se sua organização for federada com o Azure AD, você pode usar [a Autenticação Multifatorial do Azure para proteger os recursos do FS AD,](multi-factor-authentication-get-started-adfs.md)tanto no local quanto na nuvem. O Azure MFA permite reduzir senhas e fornecer uma maneira mais segura de autenticar. Começando com o Windows Server 2016, agora você pode configurar o Azure MFA para autenticação primária.

Ao contrário do AD FS no Windows Server 2012 R2, o adaptador AD FS 2016 Azure MFA integra-se diretamente ao Azure AD e não requer um servidor Azure MFA no local. O adaptador Azure MFA é incorporado ao Windows Server 2016, e não há necessidade de uma instalação adicional.

Ao usar o Azure MFA com OD FS 2016 e o aplicativo-alvo estiver sujeito à política de Acesso Condicional, existem considerações adicionais:

* O Conditional Access está disponível quando o aplicativo é uma parte que depende do Azure AD, federado com AD FS 2016 ou mais novo.
* O Acesso Condicional não está disponível quando a aplicação é uma parte que depende do AD FS 2016 ou AD FS 2019 e é gerenciada ou federada com AD FS 2016 ou AD FS 2019.
* O Conditional Access também não está disponível quando o AD FS 2016 ou o AD FS 2019 for configurado para usar o Azure MFA como o método de autenticação principal.

#### <a name="ad-fs-logging"></a>Registro de AD FS

O registro padrão do AD FS 2016 e de 2019 no Registro de Segurança do Windows e no log de ad min do FS AD, contém informações sobre solicitações de autenticação e seu sucesso ou falha. Os dados de registro de eventos dentro desses eventos indicarão se o Azure MFA foi usado. Por exemplo, um ID de evento de auditoria aD FS 1200 pode conter:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerenciar certificados

Em cada servidor AD FS, no computador local My Store, haverá um certificado Azure MFA auto-assinado intitulado OU=Microsoft AD FS Azure MFA, que contém a data de expiração do certificado. Verifique o período de validade deste certificado em cada servidor AD FS para determinar a data de validade.

Se o prazo de validade de seus certificados estiver próximo do vencimento, [gere e verifique um novo certificado MFA em cada servidor AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

A orientação a seguir detalha como gerenciar os certificados MFA do Azure em seus servidores AD FS. Quando você configura O AD FS com OZure MFA, os certificados gerados através do `New-AdfsAzureMfaTenantCertificate` cmdlet PowerShell são válidos por dois anos. Renovar e instalar os certificados renovados antes do vencimento para interrupções vazias no serviço MFA.

## <a name="implement-your-plan"></a>Implemente seu plano

Agora que você planejou sua solução, você pode implementar seguindo as etapas abaixo:

1. Atender a quaisquer pré-requisitos necessários
   1. Implantar [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para quaisquer cenários híbridos
   1. Implantar [proxy de aplicativo AD do Azure](../manage-apps/application-proxy.md) para qualquer aplicativo local publicado para acesso à nuvem
   1. Implantar [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Certifique-se de que os usuários tenham atualizado para versões suportadas do Microsoft Office com autenticação moderna ativada
1. Configure [os métodos de autenticação](#choose-verification-options) escolhidos
1. Defina os [locais de rede nomeados](../conditional-access/location-condition.md#named-locations)
1. Selecione grupos para começar a implantar o MFA.
1. Configure suas [políticas de acesso condicional](#create-conditional-access-policy)
1. Configure sua política de registro de MFA
   1. [MFA e SSPR combinados](howto-registration-mfa-sspr-combined.md)
   1. Com [proteção de identidade](../identity-protection/howto-mfa-policy.md)
1. Envie comunicações de usuários e obtenha que os usuários se inscrevam em[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Acompanhe quem está matriculado](#identify-non-registered-users)

> [!TIP]
> Usuários de nuvem do governo podem se inscrever em[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gerenciar sua solução

Relatórios para Azure MFA

A autenticação multifatorial do Azure fornece relatórios através do portal Azure:

| Relatório | Location | Descrição |
| --- | --- | --- |
| Alertas de fraudes e uso | Microsoft Azure AD > Entradas | Fornece informações sobre o uso geral, resumo do usuário e detalhes do usuário, assim como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |

## <a name="troubleshoot-mfa-issues"></a>Solucionar problemas de MFA

Encontre soluções para problemas comuns com o Azure MFA no [artigo de autenticação multifatorial do Azure Troubles no](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) Microsoft Support Center.

## <a name="next-steps"></a>Próximas etapas

Para ver a autenticação multifatorial do Azure em ação, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Microsoft Azure](tutorial-enable-azure-mfa.md)
