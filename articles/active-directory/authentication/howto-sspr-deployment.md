---
title: Implantação de redefinição de senha de autoatendimento-Azure Active Directory
description: Estratégia para a implementação bem-sucedida da redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785a8a031a10232a37b235711ba919fdc1df35d3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061396"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset"></a>Planejar uma Azure Active Directory redefinição de senha de autoatendimento

> [!NOTE]
> Este plano de implantação oferece diretrizes de planejamento e práticas recomendadas para implantar a SSPR (redefinição de senha de autoatendimento) do Azure AD. <br>**Se você estiver procurando a ferramenta SSPR para voltar à sua conta, vá para [https://aka.ms/sspr](https://aka.ms/sspr)** .

O [SSPR (autoatendimento de redefinição de senha)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) é um recurso Azure Active Directory (AD) que permite que os usuários redefinam suas senhas sem entrar em contato com a equipe de ti para obter ajuda. Os usuários podem rapidamente se desbloquear e continuar trabalhando independentemente de onde estiverem ou hora do dia. Ao permitir que os funcionários se desbloqueiem, sua organização pode reduzir o tempo não produtivo e os altos custos de suporte para os problemas mais comuns relacionados a senhas. 

O SSPR tem os seguintes recursos principais:

* O autoatendimento permite que os usuários finais redefinam suas senhas expiradas ou não expiradas sem entrar em contato com um administrador ou assistência técnica para obter suporte.

* O [write-back de senha](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) permite o gerenciamento de senhas locais e a resolução de bloqueio de conta por meio da nuvem.

* Os relatórios de atividade de gerenciamento de senhas oferecem aos administradores informações sobre a redefinição de senha e a atividade de registro que ocorrem em sua organização.

## <a name="learn-about-sspr"></a>Saiba mais sobre o SSPR

Saiba mais sobre o SSPR. Veja [como funciona: redefinição de senha de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Principais benefícios

Os principais benefícios da habilitação de SSPR são:

* **Gerencie o custo**. O SSPR reduz os custos de suporte de ti, permitindo que os usuários redefinam senhas por conta própria. Ele também reduz o custo do tempo perdido devido a senhas perdidas e bloqueios. 

* **Experiência de usuário intuitiva**. Ele fornece um processo de registro de usuário único intuitivo que permite que os usuários redefinam senhas e desbloqueiem contas sob demanda de qualquer dispositivo ou local. O SSPR permite que os usuários voltem a funcionar mais rapidamente e sejam mais produtivos.

* **Flexibilidade e segurança**. O SSPR permite que as empresas acessem a segurança e a flexibilidade que uma plataforma de nuvem fornece. Os administradores podem alterar as configurações para acomodar novos requisitos de segurança e reverter essas alterações para os usuários sem interromper sua entrada.

* **Auditoria e acompanhamento de uso robustos**. Uma organização pode garantir que os sistemas de negócios permaneçam seguros enquanto seus usuários redefinem suas próprias senhas. Os logs de auditoria robustos incluem informações de cada etapa do processo de redefinição de senha. Esses logs estão disponíveis a partir de uma API e permitem que o usuário importe os dados para um sistema de escolha do SIEM (monitoramento de eventos e incidentes de segurança).

### <a name="licensing"></a>Licenciamento

Azure Active Directory é licenciado por usuário, o que significa que cada usuário requer uma licença apropriada para os recursos que eles usam. Recomendamos o licenciamento baseado em grupo para SSPR. 

Para comparar edições e recursos e habilitar o licenciamento baseado em grupo ou usuário, consulte [requisitos de licenciamento para redefinição de senha de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Para obter mais informações sobre preços, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Prerequisites

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada. Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma conta com privilégios de Administrador Global.


### <a name="training-resources"></a>Recursos de treinamento

| Recursos| Link e descrição |
| - | - |
| vídeos| [Capacite seus usuários com melhor escalabilidade de ti](https://youtu.be/g9RpRnylxS8) 
| |[O que é redefinição de senha de autoatendimento?](https://youtu.be/hc97Yx5PJiM)|
| |[Implantando a redefinição de senha de autoatendimento](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Como configurar a redefinição de senha de autoatendimento para usuários no Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Como [preparar usuários para] registrar [suas] informações de segurança para Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Cursos online|[Gerenciando identidades no Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Use o SSPR para dar aos seus usuários uma experiência moderna e protegida. Consulte especialmente o módulo "[gerenciando Azure Active Directory usuários e grupos](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Cursos pagos da Pluralsight |[Os problemas de gerenciamento de identidade e acesso](https://www.pluralsight.com/courses/identity-access-management-issues) Saiba mais sobre os problemas IAM e de segurança para estar atento em sua organização. Consulte especialmente o módulo "outros métodos de autenticação".|
| |[Introdução com o Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Conheça as práticas recomendadas para estender ativos locais para a nuvem de uma maneira que permita autenticação, autorização, criptografia e uma experiência móvel protegida. Consulte especialmente o módulo "configurando recursos avançados do Microsoft Azure Active Directory Premium".
|Tutoriais |[Concluir uma distribuição do piloto de redefinição de senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Habilitar o write-back de senha](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Redefinição de senha do Azure AD na tela de logon para Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Perguntas frequentes|[Perguntas frequentes sobre o gerenciamento de senhas](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Arquitetura da solução

O exemplo a seguir descreve a arquitetura da solução de redefinição de senha para ambientes híbridos comuns.

![diagrama da arquitetura da solução](./media/howto-sspr-deployment//solutions-architecture.png)

Descrição do fluxo de trabalho

Para redefinir a senha, os usuários vão para o [portal de redefinição de senha](https://aka.ms/sspr). Eles devem verificar o método ou métodos de autenticação registrados anteriormente para provar sua identidade. Se eles redefinirem a senha com êxito, eles iniciarão o processo de redefinição.

* Para usuários somente de nuvem, o SSPR armazena a nova senha no Azure AD. 

* Para usuários híbridos, o SSPR grava novamente a senha para o Active Directory local por meio do serviço Azure AD Connect. 

Observação: para os usuários que têm a [sincronização de hash de senha (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) desabilitada, o SSPR armazena as senhas somente no local Active Directory.

### <a name="best-practices"></a>Práticas recomendadas

Você pode ajudar os usuários a se registrarem rapidamente implantando SSPR junto com outro aplicativo ou serviço popular na organização. Essa ação gerará um grande volume de entradas e irá orientar o registro.

Antes de implantar o SSPR, você pode optar por determinar o número e o custo médio de cada chamada de redefinição de senha. Você pode usar esses dados após a implantação para mostrar o valor que o SSPR está trazendo para a organização.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Habilitar o registro combinado para SSPR e MFA

A Microsoft recomenda que as organizações habilitem a experiência de registro combinada para SSPR e autenticação multifator. Quando você habilita essa experiência de registro combinada, os usuários só precisam selecionar suas informações de registro uma vez para habilitar os dois recursos.

A experiência de registro combinada não exige que as organizações habilitem o SSPR e a autenticação multifator do Azure. O registro combinado fornece às organizações uma melhor experiência do usuário. Para obter mais informações, consulte [registro de informações de segurança combinadas (versão prévia)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [certifique-se de que você esteja participando dos participantes certos](https://aka.ms/deploymentplans) e que as funções de Stakeholder no projeto sejam bem compreendidas ao documentar os participantes e sua entrada e responsabilidades de projeto.

#### <a name="required-administrator-roles"></a>Funções de administrador necessárias


| Função corporativa/persona| Função do Azure AD (se necessário) |
| - | - |
| Suporte técnico nível 1| Administrador de senha |
| Suporte técnico nível 2| Administrador de usuários |
| Administrador do SSPR| Administrador global |


### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Você deve se comunicar proativamente com seus usuários como sua experiência será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas. Examine os [materiais de distribuição de redefinição de senha de autoatendimento no centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) para obter ideias sobre como planejar sua estratégia de comunicação do usuário final.

### <a name="plan-a-pilot"></a>Planejar um piloto

Recomendamos que a configuração inicial do SSPR esteja em um ambiente de teste. Comece com um grupo piloto habilitando o SSPR para um subconjunto de usuários em sua organização. Consulte [as práticas recomendadas para um piloto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Para criar um grupo, consulte como [criar um grupo e adicionar membros em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Configuração do plano

As configurações a seguir são necessárias para habilitar o SSPR juntamente com os valores recomendados.

| Área | Configuração | Valor |
| --- | --- | --- |
| **Propriedades de SSPR** | Redefinição de senha de autoatendimento habilitada | Grupo **selecionado** para o piloto/ **tudo** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para registrar | Sempre 1 mais do que o necessário para redefinir |
|   | Métodos de autenticação necessários para redefinir | Um ou dois |
| **Registro** | Exigir que os usuários se registrem ao entrar | Sim |
|   | Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação | 90 – 180 dias |
| **Notificações** | Notificar os usuários de redefinições de senha | Sim |
|   | Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas | Sim |
| **Personalização** | Personalizar link de assistência técnica | Sim |
|   | URL ou email de assistência técnica personalizado | Site de suporte ou endereço de email |
| **Integração local** | Write-back de senhas para o AD local | Sim |
|   | Permitir que os usuários desbloqueiem a conta sem redefinir a senha | Sim |

### <a name="sspr-properties"></a>Propriedades de SSPR

Ao habilitar o SSPR, escolha um grupo de segurança apropriado no ambiente piloto.

* Para impor o registro SSPR para todos, é recomendável usar a opção **All** .
* Caso contrário, selecione o grupo de segurança do Azure AD ou do AD apropriado.

### <a name="authentication-methods"></a>Métodos de autenticação

Quando o SSPR estiver habilitado, os usuários só poderão redefinir sua senha se tiverem dados presentes nos métodos de autenticação que o administrador tiver habilitado. Os métodos incluem telefone, notificação de aplicativo autenticador, perguntas de segurança, etc. Para obter mais informações, consulte [o que são métodos de autenticação?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

Recomendamos as seguintes configurações do método de autenticação:

* Defina os **métodos de autenticação necessários para se registrar** em pelo menos um maior que o número necessário para a redefinição. Permitir várias autenticações dá aos usuários flexibilidade quando eles precisam ser redefinidos.

* Defina o **número de métodos necessários para redefinir** para um nível apropriado para sua organização. Uma delas requer o menor conflito, enquanto duas podem aumentar sua postura de segurança. 

Observação: o usuário deve ter os métodos de autenticação configurados nas [diretivas de senha e restrições no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Configurações de registro

Defina **exigir que os usuários se registrem ao entrar** em **Sim**. Essa configuração exige que os usuários se registrem ao entrar, garantindo que todos os usuários estejam protegidos.

Defina **o número de dias antes que os usuários sejam solicitados a reconfirmar suas informações de autenticação** entre **90** e **180** dias, a menos que sua organização tenha uma necessidade comercial por um período de tempo menor.

### <a name="notifications-settings"></a>Configurações de notificações

Configure os **usuários de notificação em redefinições de senha** e **notifique todos os administradores quando outros administradores redefinirem sua senha** como **Sim**. A seleção de **Sim** em ambos aumenta a segurança, garantindo que os usuários saibam quando sua senha é redefinida. Ele também garante que todos os administradores saibam quando um administrador altera uma senha. Se os usuários ou administradores receberem uma notificação e eles não tiverem iniciado a alteração, eles poderão relatar imediatamente um possível problema de segurança.

### <a name="customization-settings"></a>Configurações de personalização

É fundamental personalizar o email ou a URL da assistência técnica para garantir que os usuários que tenham problemas possam obter ajuda imediatamente. Defina essa opção como um endereço de email comum da assistência técnica ou página da Web com a qual os usuários estão familiarizados. 

Para obter mais informações, consulte [Personalizar a funcionalidade do Azure ad para redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Write-back de senha

O **write-back de senha** é habilitado com [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) e grava redefinições de senha na nuvem de volta para um diretório local existente em tempo real. Para obter mais informações, consulte [o que é Write-back de senha?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Recomendamos as seguintes configurações:

* Verifique se a **gravação de senhas no AD local** está definida como **Sim**. 
* Defina a **opção permitir que os usuários desbloqueiem a conta sem redefinir a senha** como **Sim**.

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha.

### <a name="administrator-password-setting"></a>Configuração de senha de administrador

As contas de administrador têm permissões elevadas. Os administradores de domínio ou corporativos locais não podem redefinir suas senhas por meio de SSPR. As contas de administrador local têm as seguintes restrições:

* Só é possível alterar sua senha em seu ambiente local.
* Nunca pode usar as perguntas e respostas secretas como um método para redefinir sua senha.

Recomendamos que você não sincronize suas contas de administrador de Active Directory local com o Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com vários sistemas de gerenciamento de identidade

Alguns ambientes têm vários sistemas de gerenciamento de identidade. Os gerenciadores de identidade instalação, como Oracle AM e SiteMinder, exigem sincronização com o AD para senhas. Você pode fazer isso usando uma ferramenta como o serviço de notificação de alteração de senha (PCNS) com o MIM (Microsoft Identity Manager). Para encontrar informações sobre esse cenário mais complexo, consulte o artigo [implantar o serviço de notificação de alteração de senha do mim em um controlador de domínio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Teste e suporte do plano

Em cada estágio de sua implantação de grupos piloto iniciais por toda a organização, verifique se os resultados são os esperados.

### <a name="plan-testing"></a>Teste de plano

Para garantir que sua implantação funcione conforme o esperado, planeje um conjunto de casos de teste para validar a implementação. Para avaliar os casos de teste, você precisa de um usuário de teste que não seja administrador com uma senha. Se você precisar criar um usuário, consulte [Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

A tabela a seguir inclui cenários de teste úteis que você pode usar para documentar os resultados esperados de suas organizações com base em suas políticas.
<br>


| Caso comercial| Resultados esperados |
| - | - |
| O portal do SSPR pode ser acessado de dentro da rede corporativa| Determinado pela sua organização |
| O portal do SSPR pode ser acessado de fora da rede corporativa| Determinado pela sua organização |
| Redefinir a senha do usuário do navegador quando o usuário não estiver habilitado para redefinição de senha| O usuário não consegue acessar o fluxo de redefinição de senha |
| Redefinir a senha do usuário do navegador quando o usuário não tiver se registrado para redefinição de senha| O usuário não consegue acessar o fluxo de redefinição de senha |
| O usuário entra quando imposto para fazer o registro de redefinição de senha| Solicita que o usuário registre informações de segurança |
| O usuário entra quando o registro de redefinição de senha é concluído| Solicita que o usuário registre informações de segurança |
| O portal do SSPR é acessível quando o usuário não tem uma licença| Está acessível |
| Redefinir a senha do usuário da tela de bloqueio do dispositivo ingressado no Azure AD do Windows 10 ou híbrido do Azure AD| O usuário pode redefinir a senha |
| Os dados de registro e de uso do SSPR estão disponíveis para os administradores quase em tempo real| Está disponível por meio de logs de auditoria |

Você também pode consultar [concluir um roll-out do piloto de redefinição de senha de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). Neste tutorial, você habilitará uma distribuição piloto de SSPR em sua organização e testará usando uma conta que não seja de administrador.

### <a name="plan-support"></a>Planejar suporte

Embora o SSPR normalmente não crie problemas de usuário, é importante preparar a equipe de suporte para lidar com problemas que possam surgir. Embora um administrador possa redefinir a senha para os usuários finais por meio do portal do AD do Azure, é melhor ajudar a resolver o problema por meio de um processo de suporte de autoatendimento.

Para habilitar o sucesso da sua equipe de suporte, você pode criar perguntas frequentes com base em perguntas que você recebe de seus usuários. Veja alguns exemplos:

| Cenários| DESCRIÇÃO |
| - | - |
| O usuário não tem nenhum método de autenticação registrado disponível| Um usuário está tentando redefinir sua senha, mas não tem nenhum dos métodos de autenticação que eles registraram disponíveis (exemplo: eles deixaram seu telefone celular em casa e não podem acessar o email) |
| O usuário não está recebendo um texto ou uma chamada em seu escritório ou telefone celular| Um usuário está tentando verificar sua identidade por meio de texto ou chamada, mas não está recebendo um texto/chamada. |
| O usuário não pode acessar o portal de redefinição de senha| Um usuário deseja redefinir sua senha, mas não está habilitado para redefinição de senha e não pode acessar a página para atualizar senhas. |
| O usuário não pode definir uma nova senha| Um usuário conclui a verificação durante o fluxo de redefinição de senha, mas não pode definir uma nova senha. |
| O usuário não vê um link Redefinir senha em um dispositivo Windows 10| Um usuário está tentando redefinir a senha da tela de bloqueio do Windows 10, mas o dispositivo não está associado ao Azure AD ou a política de dispositivo do Intune não está habilitada |

### <a name="plan-roll-back"></a>Reversão do plano

Para reverter a implantação:

* para um único usuário, remova o usuário do grupo de segurança 

* para um grupo, remova o grupo da configuração do SSPR

* Para todos, desabilite SSPR para o locatário do Azure AD

## <a name="deploy-sspr"></a>Implantar SSPR

Antes de implantar, verifique se você fez o seguinte:

1. Criado e iniciado executando seu [plano de comunicação](#plan-communications).

1. Determinado as [definições de configuração](#plan-configuration)apropriadas.

1. Identificados os usuários e grupos para os ambientes [piloto](#plan-a-pilot) e de produção.

1. [Determinadas definições de configuração](#plan-configuration) para registro e autoatendimento.

1. [Configure o Write-back de senha](#password-writeback) se você tiver um ambiente híbrido.


**Agora você está pronto para implantar o SSPR!**

Consulte [habilitar a redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) para obter instruções passo a passo completas sobre como configurar as áreas a seguir.

1. [Métodos de autenticação](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Configurações de registro](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Configurações de notificações](#notifications-settings)

1. [Configurações de personalização](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integração local](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Habilitar SSPR no Windows
Para computadores que executam o Windows 7, 8, 8,1 e 10, você pode [permitir que os usuários redefinam sua senha na tela de logon do Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Gerenciar SSPR

O Azure AD pode fornecer informações adicionais sobre o desempenho do SSPR por meio de auditorias e relatórios.

### <a name="password-management-activity-reports"></a>Relatórios de atividade de gerenciamento de senhas 

Você pode usar relatórios predefinidos em portal do Azure para medir o desempenho do SSPR. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas. Para obter mais informações, consulte [Opções de relatório para o gerenciamento de senhas do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  Você deve ser [um administrador global](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)e deve aceitar que esses dados sejam coletados para sua organização. Para aceitar, você deve visitar a guia relatório ou os logs de auditoria no portal do Azure pelo menos uma vez. Até lá, os dados não são coletados para sua organização.

Os logs de auditoria para registro e redefinição de senha estão disponíveis por 30 dias. Se a auditoria de segurança na sua empresa exigir maior retenção, os logs precisarão ser exportados e consumidos em uma ferramenta SIEM, como o [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk ou ArcSight.

![Captura de tela de relatório do SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Métodos de autenticação-uso e informações

O [uso e as informações](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) permitem que você entenda como os métodos de autenticação para recursos como o Azure MFA e o SSPR estão trabalhando em sua organização. Essa funcionalidade de relatório fornece à sua organização os meios para entender quais métodos se registram e como usá-los.

### <a name="troubleshoot"></a>Solucionar problemas

* Consulte [solucionar problemas de redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Siga as perguntas frequentes [sobre o gerenciamento de senhas](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Documentação útil

* [Quais são os métodos de autenticação?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Como funciona: redefinição de senha de autoatendimento do Azure AD?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Personalizar a funcionalidade do Azure AD para redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Políticas e restrições de senha do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [O que é write-back de senha?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Próximas etapas

* Para começar a implantar o SSPR, consulte [concluir uma distribuição do piloto de redefinição de senha de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)

* [Considere implementar a proteção de senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Considere implementar o bloqueio inteligente do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)