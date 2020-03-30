---
title: Implantar estações de trabalho gerenciadas pelo Azure - Azure Active Directory
description: Saiba como implantar estações de trabalho seguras e gerenciadas pelo Azure para reduzir o risco de violação devido à configuração errada ou compromisso.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672616"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Implantar uma estação de trabalho segura e gerenciada pelo Azure

Agora que você [entende as estações](concept-azure-managed-workstation.md)de trabalho seguras, é hora de iniciar o processo de implantação. Com essa orientação, você usa perfis definidos para criar uma estação de trabalho mais segura desde o início.

![Implantação de uma estação de trabalho segura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selecione um perfil antes de implantar a solução. Você pode usar vários perfis simultaneamente em uma implantação e atribuí-los com tags ou grupos.

> [!NOTE]
> Aplique qualquer um dos perfis conforme necessário às suas necessidades. Você pode mudar para outro perfil atribuindo-o no Microsoft Intune.

| Perfil | Baixo | Avançado | Alta | Especializada | Seguro | Isolado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Usuário no Azure AD | Sim | Sim | Sim | Sim | Sim | Sim |
| Gerenciado por intune | Sim | Sim | Sim | Sim | Sim | Sim |
| Dispositivo - Azure AD registrado | Sim |  |  |  |  | |   |
| Dispositivo - Azure AD juntou-se |   | Sim | Sim | Sim | Sim | Sim |
| Linha de base de segurança intune aplicada |   | Sim <br> (Aprimorado) | Sim <br> (Alta Segurança) | Sim <br> (NCSC) | Sim <br> (Assegurado) | NA |
| O hardware atende a padrões seguros do Windows 10 |   | Sim | Sim | Sim | Sim | Sim |
| Microsoft Defender ATP ativado |   | Sim  | Sim | Sim | Sim | Sim |
| Remoção dos direitos de admin |   |   | Sim  | Sim | Sim | Sim |
| Implantação usando o Microsoft Autopilot |   |   | Sim  | Sim | Sim | Sim |
| Aplicativos instalados apenas pela Intune |   |   |   | Sim | Sim |Sim |
| URLs restritos à lista aprovada |   |   |   | Sim | Sim |Sim |
| Internet bloqueada (entrada/saída) |   |   |   |  |  |Sim |

> [!NOTE]
> Na estação de trabalho **segura, os dispositivos** de orientação da estação de trabalho serão atribuídos com perfis e políticas. Os usuários não terão as políticas aplicadas diretamente a eles, permitindo que o compartilhamento de dispositivos (dispositivos compartilhados) esteja em vigor. Se uma estação de trabalho segura não for compartilhada em sua implantação ou forem necessárias políticas individuais de usuário, a atribuição dos perfis de política de usuário pode ser atribuída ao usuário e ao dispositivo. 

## <a name="license-requirements"></a>Requisitos de licença

Os conceitos abordados neste guia supõem que você tem o Microsoft 365 Enterprise E5 ou um SKU equivalente. Algumas das recomendações deste guia podem ser implementadas com SKUs mais baixas. Para obter mais informações, consulte [o licenciamento Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Para automatizar o provisionamento de licenças, considere [o licenciamento em grupo](../users-groups-roles/licensing-groups-assign.md) para seus usuários.

## <a name="azure-active-directory-configuration"></a>Configuração do Azure Active Directory

O Azure Active Directory (Azure AD) gerencia usuários, grupos e dispositivos para as estações de trabalho do administrador. Habilite serviços e recursos de identidade com uma [conta de administrador](../users-groups-roles/directory-assign-admin-roles.md).

Quando você cria a conta de administrador de estação de trabalho protegida, você expõe a conta à sua estação de trabalho atual. Certifique-se de usar um dispositivo seguro conhecido para fazer essa configuração inicial e toda a configuração global. Para reduzir a exposição ao ataque pela primeira vez, considere seguir a [orientação para prevenir infecções por malware.](/windows/security/threat-protection/intelligence/prevent-malware-infection)

Exija autenticação multifatorial, pelo menos para seus administradores. Consulte [Implantar MFA baseado em nuvem](../authentication/howto-mfa-getstarted.md) para obter orientação de implementação.

### <a name="azure-ad-users-and-groups"></a>Usuários e grupos de AD do Azure

1. A partir do portal Azure, navegue até os**usuários** >  **do Azure Active Directory** > **Novo usuário**.
1. Crie o administrador do dispositivo seguindo as etapas do [tutorial do usuário criar](/Intune/quickstart-create-user).
1. Digite:

   * **Nome** - Administrador seguro da estação de trabalho
   * **Nome de usuário** - `secure-ws-admin@identityitpro.com`
   * **Função de diretório** - **Administrador limitado** e selecione a função Administrador **intune.**

1. Selecione **Criar**.

Em seguida, você cria dois grupos: usuários de estações de trabalho e dispositivos de estação de trabalho.

A partir do portal Azure, navegue até **o grupo Azure Active Directory** > **Groups** > **.**

1. Para o grupo de usuários da estação de trabalho, você pode querer configurar [o licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para automatizar o provisionamento de licenças aos usuários.
1. Para o grupo de usuários da estação de trabalho, digite:

   * **Tipo de grupo** - Segurança
   * **Nome do grupo** - Usuários de estação de trabalho segura
   * **Tipo de adesão** - Atribuído

1. Adicione o usuário administrador de estação de trabalho seguro:`secure-ws-admin@identityitpro.com`
1. Você pode adicionar quaisquer outros usuários que estejam gerenciando estações de trabalho seguras.
1. Selecione **Criar**.
1. Para o grupo de dispositivos de estação de trabalho, digite:

   * **Tipo de grupo** - Segurança
   * **Nome do grupo** - Estações de Trabalho Seguras
   * **Tipo de adesão** - Atribuído

1. Selecione **Criar**.

### <a name="azure-ad-device-configuration"></a>Configuração do dispositivo Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Especificar quem pode juntar dispositivos ao Azure AD

Configure a configuração de seus dispositivos no Active Directory para permitir que seu grupo de segurança administrativa junte dispositivos ao seu domínio. Para configurar essa configuração no portal Azure:

1. Ir para a**configuração**do**dispositivo** > de diretório ativo do >  **Azure**.
1. Escolha **Os usuários selecionados** **podem juntar dispositivos ao Azure AD**e, em seguida, selecionar o grupo "Usuários de estação de trabalho seguro".

#### <a name="removal-of-local-admin-rights"></a>Remoção dos direitos de admin local

Esse método exige que os usuários das estações de trabalho VIP, DevOps e secure-level não tenham direitos de administrador em suas máquinas. Para configurar essa configuração no portal Azure:

1. Ir para a**configuração**do**dispositivo** > de diretório ativo do >  **Azure**.
1. Selecione **Nenhum** em **Administradores locais adicionais no Azure AD aderidos**aos dispositivos .

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exija autenticação multifatorial para juntar dispositivos

Para fortalecer ainda mais o processo de adesão de dispositivos ao Azure AD:

1. Ir para a**configuração**do**dispositivo** > de diretório ativo do >  **Azure**.
1. Selecione **Sim** em **Exigir Auth Multifatorial para juntar dispositivos**.
1. Selecione **Salvar**.

#### <a name="configure-mobile-device-management"></a>Configurar o gerenciamento de dispositivos móveis

No Portal do Azure:

1. Navegue até **o Azure Active Directory** > **Mobility (MDM e MAM)** > Microsoft**Intune**.
1. Alterar a configuração do **escopo do usuário MDM** para **Todos**.
1. Selecione **Salvar**.

Essas etapas permitem que você gerencie qualquer dispositivo com o Intune. Para obter mais informações, consulte [Intune Quickstart: Configure a inscrição automática para dispositivos Windows 10](/Intune/quickstart-setup-auto-enrollment). Você cria políticas de configuração e conformidade do Intune em um passo futuro.

#### <a name="azure-ad-conditional-access"></a>Acesso Condicional aazure AD

O Azure AD Conditional Access pode ajudar a restringir tarefas administrativas privilegiadas a dispositivos compatíveis. Os membros predefinidos do grupo **Secure Workstation Users** são obrigados a realizar autenticação multifatorial ao fazer login em aplicativos na nuvem. Uma prática recomendada é excluir contas de acesso de emergência da apólice. Para obter mais informações, consulte [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Configuração intune

### <a name="configure-enrollment-status"></a>Configurar o status de matrícula

É importante garantir que sua estação de trabalho segura seja um dispositivo limpo confiável. Ao comprar novos dispositivos, você pode insistir que eles estão configurados de fábrica para [o Windows 10 Pro no modo S](/Windows/deployment/Windows-10-pro-in-s-mode), o que limita a exposição a vulnerabilidades durante o gerenciamento da cadeia de suprimentos. Depois de receber um dispositivo do seu fornecedor, você pode usar o Piloto Automático para alterá-lo do modo S. A orientação a seguir fornece detalhes sobre a aplicação do processo de transformação.

Para garantir que os dispositivos estejam totalmente configurados antes do uso, o Intune fornece um meio de bloquear o uso do dispositivo até que **todos os aplicativos e perfis sejam instalados**.

Do **portal Azure:**

1. Vá para a**inscrição** >  **do Dispositivo Microsoft Intune** > **Configurações** > padrão**da página** > de status**de** > inscrição**do**Windows .
1. Definir **mostrar o progresso da instalação do perfil do aplicativo** para **Sim**.
1. Definir **o uso do dispositivo Block até que todos os aplicativos e perfis sejam instalados** em **Sim**.

### <a name="create-an-autopilot-deployment-profile"></a>Criar um perfil de implantação do Autopilot

Depois de criar um grupo de dispositivos, você deve criar um perfil de implantação para configurar os dispositivos do Piloto Automático.

Em Sintonia no portal Azure:

1. Selecione **a inscrição de** > dispositivos**Perfis** > de implantação**do Windows** > **criam perfil**.
1. Digite:

   * Nome - **Perfil de implantação de estação de trabalho seguro**.
   * Descrição - **Implantação de estações de trabalho seguras**.
   * Defina **Converter todos os dispositivos de destino para Autopilot** como **Sim**. Essa configuração garante que todos os dispositivos na lista sejam registrados no serviço de implantação do Autopilot. Aguarde 48 horas para que o registro seja processado.

1. Selecione **Avançar**.

   * Para **o modo de implantação,** escolha **Auto-implantação (Visualização)**. Dispositivos com este perfil estão associados ao usuário que registra o dispositivo. As credenciais do usuário são necessárias para registrar o dispositivo. É essencial notar que a implantação de um dispositivo no modo **self-deploying** permitirá que você implante laptops em um modelo compartilhado. Nenhuma atribuição de usuário acontecerá até que o dispositivo seja atribuído a um usuário pela primeira vez. Como resultado, quaisquer políticas de usuário como o BitLocker não serão ativadas até que uma atribuição de usuário seja concluída. Para obter mais informações sobre como fazer logon em um dispositivo protegido, consulte [perfis selecionados](/intune/device-profile-assign).
   * O **Join to Azure AD como** caixa deve mostrar a **Azure AD junto u e** ser acinzentado.
   * Selecione seu idioma (Região), padrão de tipo de conta **de**usuário . 

1. Selecione **Avançar**.

   * Selecione uma tag de escopo se você tiver uma pré-configurada.

1. Selecione **Avançar**.
1. Escolher **atribuições** > **Atribuir a** > **grupos selecionados**. Em **Grupos Selecionados para incluir,** escolha **Estações de trabalho seguras**.
1. Selecione **Avançar**.
1. Selecione **Criar** para criar o perfil. O perfil de implantação do Autopilot agora está disponível para ser atribuído aos dispositivos.

A inscrição de dispositivos no Piloto Automático fornece uma experiência diferente do usuário com base no tipo e função do dispositivo. Em nosso exemplo de implantação, ilustramos um modelo onde os dispositivos protegidos são implantados em massa e podem ser compartilhados, mas quando usadopela primeira vez, o dispositivo é atribuído a um usuário. Para obter mais informações, consulte [a inscrição do dispositivo Intune Autopilot](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Configurar a atualização do Windows

Manter o Windows 10 atualizado é uma das coisas mais importantes que você pode fazer. Para manter o Windows em um estado seguro, você implanta um anel de atualização para gerenciar o ritmo que as atualizações são aplicadas às estações de trabalho. 

Essa orientação recomenda que você crie um novo anel de atualização e altere as seguintes configurações padrão:

No Portal do Azure:

1. Vá para as**atualizações** > do **Software Microsoft Intune** > **do Windows 10 Update Rings**.
1. Digite:

   * Nome - **Atualizações da estação de trabalho gerenciada pelo Azure**
   * Canal de manutenção - **Windows Insider - Rápido**
   * Adiamento de atualização de qualidade (dias) - **3**
   * Período de adiamento da atualização de recursos (dias) - **3**
   * Comportamento de atualização automática - **Instalação e reinicialização automática sem controle do usuário final**
   * Bloqueie o usuário de pausar as atualizações do Windows - **Bloquear**
   * Exigir a aprovação do usuário para reiniciar fora do horário de trabalho - **Obrigatório**
   * Permitir que o usuário reinicie (reinicialização engajada) - **Obrigatório**
   * Os usuários de transição para reiniciar engajados após uma reinicialização automática (dias) - **3**
   * Soneca engajado lembrete de reinicialização (dias) - **3**
   * Definir prazo para reinicializações pendentes (dias) - **3**

1. Selecione **Criar**.
1. Na guia **Atribuições,** adicione o grupo **Estações de Trabalho Segura.**

Para obter mais informações sobre as políticas do Windows Update, consulte [Policy CSP - Update](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integração do Windows Defender ATP Intune

O Windows Defender ATP e o Microsoft Intune trabalham juntos para ajudar a evitar falhas de segurança. Eles também podem limitar o impacto das violações. Os recursos ATP fornecem detecção de ameaças em tempo real, bem como permitem auditoria extensiva e registro dos dispositivos de ponto final.

Para configurar a integração do Windows Defender ATP e do Intune, acesse o portal do Azure.

1. Navegue até a**conformidade com** > o**Windows Defender ATP**do **Microsoft Intune** > .
1. Na etapa 1 em **Configuração do Windows Defender ATP,** selecione **Conectar o Windows Defender ATP ao Microsoft Intune no Windows Defender Security Center**.
1. No Central de Segurança do Windows Defender:

   1. Selecione **Configurações** > **Recursos avançados**.
   1. Para **conexão Microsoft Intune,** escolha **Em**.
   1. Selecione **Salvar preferências**.

1. Depois que uma conexão for estabelecida, retorne ao Intune e selecione **Atualizar** na parte superior.
1. Defina **Conectar dispositivos Windows versão 10.0.15063 e superiores ao Windows Defender ATP** como **Ligado**.
1. Selecione **Salvar**.

Para obter mais informações, consulte [Proteção Avançada contra Ameaças do Windows Defender](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Concluir o endurecimento do perfil da estação de trabalho

Para concluir com sucesso o endurecimento da solução, baixe e execute o script apropriado. Encontre os links de download para o nível de **perfil**desejado:

| Perfil | Local de download | Nome de arquivo |
| --- | --- | --- |
| Baixa Segurança | N/D | N/D |
| Segurança aprimorada | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Alta Segurança | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Especializada | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformidade Especializada* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Seguro | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Specialized Compliance é um script que reforça a configuração Especializada fornecida no NCSC Windows10 SecurityBaseline.

Depois que o script é executado com sucesso, você pode fazer atualizações de perfis e políticas em Intune. Os scripts para perfis Avançados e Seguros criam políticas e perfis para você, mas você deve atribuir a diretiva ao grupo de dispositivos **Secure Workstations.**

* Aqui é onde você pode encontrar os perfis de configuração do dispositivo Intune criados pelos scripts: **Azure portal** > **Microsoft Intune** > **Device configuration Profiles** > **Profiles**.
* Aqui é onde você pode encontrar as políticas de conformidade de dispositivos Intune criadas pelos scripts: **Portal Azure** > **Microsoft Intune** > **Device Compliance** > **Policies**.

Para rever as alterações feitas pelos scripts, você pode exportar os perfis. Desta forma, você pode determinar o endurecimento adicional que pode ser necessário conforme descrito na [documentação da SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Execute o script `DeviceConfiguration_Export.ps1` de exportação de dados Intune do [repositório Deconfiguração](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) de Dispositivos GiuHub para exportar todos os perfis intune atuais.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurações adicionais e endurecimento a considerar

Seguindo a orientação aqui, você implantou uma estação de trabalho segura. No entanto, você também deve considerar controles adicionais. Por exemplo: 

* limitar o acesso a navegadores alternativos
* permitir que http de saída
* bloquear sites selecionados
* definir permissões para executar scripts PowerShell personalizados

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Definir regras no provedor de serviços de configuração de firewall (CSP)

Você pode fazer alterações adicionais no gerenciamento das regras de entrada e de saída, conforme necessário para seus pontos finais permitidos e bloqueados. À medida que você continua a endurecer a estação de trabalho segura, você pode afrouxar a restrição que nega todo o tráfego de entrada e saída. Você pode adicionar sites de saída permitidos para incluir sites comuns e confiáveis. Para obter mais informações, consulte [o serviço de configuração do Firewall](/Windows/client-management/mdm/firewall-csp).

O gerenciamento restritivo de tráfego de URL inclui:

* Negar todo o tráfego de entrada - Definir no script de perfil da estação de trabalho Protegido.
* Negar todos os serviços selecionados do Azure e da Microsoft, incluindo o Azure Cloud Shell e a capacidade de permitir a redefinição de senha do Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Se você está procurando fornecer mais granularidade às suas regras de bloqueio, você pode consultar o Projeto Spamhaus que mantém [a Lista de Blocos de Domínio (DBL):](https://www.spamhaus.org/dbl/)um bom recurso para usar como um conjunto avançado de regras para implementar para bloquear sites.

### <a name="manage-local-applications"></a>Gerenciar aplicativos locais

A estação de trabalho segura se move para um estado verdadeiramente endurecido quando os aplicativos locais são removidos, incluindo aplicativos de produtividade. Aqui, você adiciona o Chrome como navegador padrão e usa o Intune para limitar a capacidade do usuário de modificar o navegador e seus plug-ins.

#### <a name="deploy-applications-using-intune"></a>Implantar aplicativos usando o Intune

Em algumas situações, aplicativos como o navegador Google Chrome são necessários na estação de trabalho protegida. O exemplo a seguir fornece instruções para instalar o Chrome em dispositivos no grupo de segurança **Secure Workstations**.

1. Baixe o pacote chrome do instalador offline [para Windows de 64 bits](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrair os arquivos e anotar `GoogleChromeStandaloneEnterprise64.msi` a localização do arquivo.
1. No **portal Azure,** navegue pelos**aplicativos** >  **microsoft intune** > client**apps** > **Add**.
1. Em **tipo de aplicativo,** escolha **Linha de negócios**.
1. Em arquivo de pacote `GoogleChromeStandaloneEnterprise64.msi` do **app,** selecione o arquivo do local extraído e selecione **OK**.
1. Em **informações do App,** forneça uma descrição e um editor. Selecione **OK**.
1. Selecione **Adicionar**.
1. Na guia **Atribuições,** selecione **Disponível para dispositivos inscritos** no **tipo Atribuição**.
1. Em **Grupos Incluídos,** adicione o grupo **Estações de Trabalho Segura.**
1. Selecione **OK** e, em seguida, **Salvar**.

Para obter mais informações sobre a configuração das configurações do Chrome, consulte [Gerenciar o Navegador Chrome com o Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configuração do portal da empresa para aplicativos personalizados

Em um modo seguro, a instalação do aplicativo é restrita ao portal da empresa Intune. No entanto, a instalação do portal requer acesso à Microsoft Store. Em sua solução segura, você pode disponibilizar o portal da empresa para todos os dispositivos através de um modo offline.

Uma cópia gerenciada pelo Intune do Portal da [Empresa](/Intune/store-apps-company-portal-app) lhe dá acesso demanda a ferramentas adicionais que você pode empurrar para os usuários das estações de trabalho protegidas.

Você pode precisar instalar aplicativos com Windows 32 bits ou outros aplicativos cuja implantação exija preparativos especiais. Nesses casos, a [ferramenta de preparação de conteúdo microsoft win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) pode fornecer um arquivo de formato pronto para uso `.intunewin` para instalação.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Acesso Condicional apenas permitindo a capacidade de estação de trabalho garantida para acessar o portal Azure

O Azure AD oferece a capacidade de gerenciar e restringir, quem e o que pode acessar seu portal de gerenciamento de nuvem do Azure. A habilitação do [Acesso Condicional](../conditional-access/overview.md) garantirá que somente sua estação de trabalho segura possa gerenciar ou alterar recursos. É essencial que, ao implantar esse recurso, você considere, se a funcionalidade [de acesso de emergência](../users-groups-roles/directory-emergency-access.md) pode ou deve ser usada apenas para casos extremos e a conta gerenciada através da política.

> [!NOTE]
> Você precisará criar um grupo de usuários e incluir seu usuário de emergência que pode ignorar a política de Acesso Condicional. Para nosso exemplo, temos um grupo de segurança chamado **Emergency BreakGlass**

1. Navegue até o portal >  **Azure****Microsoft Intune** > **Conditional Access - Policies** > **New Policy**.
1. Forneça um **nome** para a apólice.
1. Selecione **Usuários e Grupos** > **Selecione usuários e grupos** 
1. Selecione **Incluir** > **funções de diretório** > Escolha as funções > administrador global, administrador de funções privilegiada, administrador de autenticação privilegiado, administrador de segurança, administrador de conformidade, administrador de acesso condicional, administrador de aplicativos, administrador de aplicativos na nuvem, administrador de serviços intune
1. Selecione **Excluir** > Escolha **usuários e grupos** > Selecione Selecionar usuários **excluídos** > Selecione o grupo **BreakGlass de emergência.**
1. Selecione **aplicativos ou ações em nuvem** > Selecione Todos os aplicativos em **nuvem**
1. Selecione **Condições** > Selecionar **Plataformas de Dispositivos** > Escolha configurar **Sim** > **Selecionar Plataformas de dispositivo escolha** **windows**
1. Selecione **controles de acesso** > Selecionar acesso de **concessão** **Sim** > Escolha exigir que o dispositivo seja marcado **como compatível**. 
1. Selecionar **ativar política** > **em**
 
Este conjunto de políticas garantirá que seus administradores devem usar um dispositivo Windows compatível, que é definido por Intune e WDATP. 

As organizações também devem considerar o bloqueio de protocolos de autenticação legado em seus ambientes. Existem várias maneiras de realizar essa tarefa, para obter mais informações sobre o bloqueio de protocolos de autenticação legado, consulte o artigo, [Como bloquear a autenticação do legado para o Azure AD com acesso condicional](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Use o PowerShell para criar configurações personalizadas

Você também pode usar o PowerShell para ampliar os recursos de gerenciamento de host. Este script de exemplo configura um plano de fundo padrão no host. É um recurso que também está disponível através do portal e perfis do Azure. O script de exemplo serve apenas para ilustrar a funcionalidade powershell.

Você pode precisar configurar alguns controles e configurações personalizados em suas estações de trabalho seguras. Este exemplo altera o plano de fundo da estação de trabalho usando a capacidade do Powershell de identificar facilmente o dispositivo como uma estação de trabalho pronta para uso e segura.

O script [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) do Microsoft Scripting Center permite que o Windows carregue essa [imagem de fundo gratuita e genérica](https://i.imgur.com/OAJ28zO.png) na inicialização.

1. Baixe o script para um dispositivo local.
1. Atualize o clienteXXXX e o local de download da imagem em segundo plano. Em nosso exemplo, substituímos o clienteXXXX por fundos.
1. Navegue > até o portal **Azure****Microsoft Intune** > **Device configuração** > **de scripts** > PowerShell**Adicionar**.
1. Forneça um **nome** para o script e especifique a localização do **script**.
1. Selecione **Configurar**.
   1. Definir **Executar este script usando o logd em credenciais** para **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecionar **atribuições** > **Selecionar grupos**.
   1. Adicione o grupo de segurança **Secure Workstations**.
   1. Selecione **Salvar**.

## <a name="enroll-and-validate-your-first-device"></a>Inscreva-se e valide seu primeiro dispositivo

1. Para registrar seu dispositivo, você precisa das seguintes informações:
   * **Número de série** - encontrado no chassi do dispositivo.
   * **Windows Product ID** - encontrado em **System** > **About** a partir do menu Configurações do Windows.
   * Você pode executar [o Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) para obter um arquivo de hash CSV com todas as informações necessárias para a inscrição do dispositivo.
   
     Execute `Get-WindowsAutoPilotInfo – outputfile device1.csv` para produzir as informações como um arquivo CSV que você pode importar para Intune.

     > [!NOTE]
     > O roteiro requer direitos elevados. Funciona como assinado remotamente. O `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando permite que o script seja executado corretamente.

   * Você pode coletar essas informações fazendo login em um dispositivo 1809 do Windows 10 ou superior. Seu revendedor de hardware também pode fornecer essas informações.
1. No **portal Azure,** vá para **microsoft intune** > **device matrícula** > dispositivos**de inscrição** > **windows - Gerenciar dispositivos windows piloto automático**.
1. Selecione **Importar** e escolha seu arquivo CSV.
1. Adicione o dispositivo ao grupo de segurança **Secure Workstations.**
1. No dispositivo Windows 10 que deseja configurar, vá para **Windows Settings** > **Update & Security** > **Recovery**.
   1. Escolha **Começar** em **Redefinir este PC**.
   1. Siga as instruções para redefinir e reconfigurar o dispositivo com as políticas de perfil e conformidade configuradas.

Depois de configurar o dispositivo, complete uma revisão e verifique a configuração. Confirme se o primeiro dispositivo está configurado corretamente antes de continuar sua implantação.

## <a name="assign-devices"></a>Atribuir dispositivos

Para atribuir dispositivos e usuários, você precisa mapear os [perfis selecionados](/intune/device-profile-assign) para o seu grupo de segurança. Todos os novos usuários que necessitam de permissões para o serviço também devem ser adicionados ao grupo de segurança.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Usando o Sentinel e o Windows Defender ATP para monitorar e responder a incidentes de segurança

Monitorar a implantação segura da estação de trabalho pode ser realizado habilitando [o Sentinel] e utilizando o [gerenciamento de ameaças e vulnerabilidades](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) A orientação não fornecerá uma exaustiva caça de ameaças, mas fornecerá bons esforços de bom senso para monitorar e responder a possíveis incidentes de segurança.

Usaremos **o Azure Sentinel** para: 

* Colete dados do Intune, portal Azure e Azure AD para monitoramento de usuários e dispositivos
* Ajuda a investigar a configuração de atividades suspeitas de configuração de usuários e dispositivos
* E impulsionar a capacidade de explorar investigações de segurança usando WDATP

O monitoramento sentinela requer que os conectores para suas fontes de dados, como o Azure AD, sejam configurados.

1. No **portal Azure,** vá para **Azure Sentinel (Preview)** > Selecione **Adicionar**
1. No **Escolha um espaço de trabalho para adicionar ao Azure Sentinel** Select **Crie um novo espaço de trabalho**
1. Digite:
   * **Espaço de trabalho do Log Analytics** - 'Monitoramento seguro da estação de trabalho'
   * **Assinatura** - Selecione sua assinatura ativa
   * **Grupo de** recursos - selecione o ** Criar novo** > SECURE Workstation RG > **Ok**
   * **Localização** - Selecione o local que é geograficamente mais adequado para sua implantação
   * **Nível de preço** - Selecione **por GB (2018)**
1. Selecione **Ok**.

Em seguida, conectaremos fontes de dados seguras disponíveis da estação de trabalho ao monitoramento.

1. No **portal Azure,** vá para o **espaço de trabalho do Azure Sentinel** > Selecione o espaço de trabalho de monitoramento seguro da **estação de** trabalho
1. Selecione **conectores de dados**
1. Escolha **o Diretório Ativo do Azure** > página do conector aberto > depois de revisar o pré-requisito. Proceder à configuração e selecionar **Conectar** para logs de login azure AD, bem como para logs de auditoria ad do Azure.
1. Escolha a página de > conectar o conector aberto do **Azure** > depois de revisar o pré-requisito. Proceder ao Configure Logs de atividades do Azure > Selecione sua assinatura > Selecionar **Conectar**

Como os dados são coletados pelo Sentinel, você poderá observar a atividade selecionando **o portal Azure,** vá para **a visão geral do Azure Sentinel** 

Usaremos **o Windows Defender ATP (WDATP)** para:

* Observe e monitore continuamente vulnerabilidades e configurações erradas
* Utilize o WDATP para priorizar ameaças dinâmicas na natureza
* Direcionar correlação de vulnerabilidades com alertas de detecção e resposta de ponto final (EDR)
* Use o painel de controle para identificar a vulnerabilidade no nível da máquina durante as investigações
* Empurrar as remediações para Intune

Configure o [painel do Defender ATP](https://securitycenter.windows.com/machines). Usando orientação no [painel de controle de ameaças & de vulnerabilidades](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)visão geral do painel de controle de vulnerabilidades .

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitorando a atividade do aplicativo usando o Microsoft Monitoring Agent (MMA)
A partir da estação de trabalho Especializada, o armário do aplicativo é habilitado para monitorar a atividade do aplicativo em uma estação de trabalho. Para que o monitoramento seja integrado no espaço de trabalho do Log Analytics, um agente de MMA e configuração devem ser seguidos. 

> [!NOTE]
> O perfil da estação de trabalho Especializada contém as políticas de monitoramento do AppLocker. A implantação das políticas é necessária para monitorar a atividade do aplicativo em um cliente

Implante o agente de MMA com o script Intune PowerShell

1. Baixe o [script](https://aka.ms/securedworkstationgit)de configuração para um dispositivo local .
1. Atualize os parâmetros, **$WorkSpaceID** e **$WorkSpaceKey**
1. Navegue > até o portal **Azure****Microsoft Intune** > **Device configuração** > **de scripts** > PowerShell**Adicionar**.
1. Forneça um **nome** para o script e especifique a localização do **script**.
1. Selecione **Configurar**.
   1. Definir **Executar este script usando o logd em credenciais** para **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecionar **atribuições** > **Selecionar grupos**.
   1. Adicione o grupo de segurança **Secure Workstations**.
   1. Selecione **Salvar**.

Em seguida, você deve configurar o Log Analytics para receber os novos logs
1. No **portal Azure,** vá para **o Log Analytics Workspace** > Select - 'Secure Workstation Monitoring'
1. Selecione **configurações avançadas** > **Data** > **Windows Event Logs**
1. Em **Coletar eventos dos logs de eventos a seguir** 
1. Digite:
   * 'Microsoft-Windows-AppLocker/EXE e DLL' > Informações Não **Selecionadas**
   * 'Microsoft-Windows-AppLocker/MSI e Script' > Informações Não **Selecionadas**
   * 'Microsoft-Windows-AppLocker/Implantação de aplicativos empacotado' > Informações **Não** Selecionadas
   * 'Microsoft-Windows-AppLocker/Execução de aplicativo empacotada' > Informações **não** selecionadas
1. Selecione **Salvar**

O registro de aplicativos estará disponível no espaço de trabalho do Log Analytics selecionado.

## <a name="monitoring"></a>Monitoramento

* Saiba como [detectar ameaças com o Azure Sentinel](/azure/sentinel/tutorial-detect-threats)
* [Investigar incidentes com o Azure Sentinel](/azure/sentinel/tutorial-investigate-cases)
* [Configurar respostas de ameaças automatizadas no Azure Sentinel](/azure/sentinel/tutorial-respond-threats-playbook)
* Entenda como revisar sua [pontuação de exposição](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Revisão [recomendação de segurança](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Gerenciar [remediações de](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) segurança
* Gerenciar [detecção e resposta de ponto final](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitore perfis com [monitoramento de perfil intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o Microsoft Intune](/intune/index).
* Entenda [a Azure AD](../index.yml).
* Trabalhe com [o Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Descubra [o Azure Sentinel](/azure/sentinel/)
