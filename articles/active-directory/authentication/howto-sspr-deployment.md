---
title: Implantação de redefinição de senha de autoatendimento - Azure Active Directory
description: Estratégia para implementação bem-sucedida da redefinição de senha de autoatendimento do Azure AD
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
ms.openlocfilehash: a7be99959c2ae420cff667491f68c40dfa0862a9
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652397"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset"></a>Planeje uma redefinição de senha de autoatendimento do Azure Active Directory

> [!NOTE]
> Este plano de implantação oferece orientação de planejamento e práticas recomendadas para a implantação do Reset de senha de autoatendimento Azure AD (SSPR). <br>**Se você está procurando a ferramenta SSPR para voltar [https://aka.ms/sspr](https://aka.ms/sspr)para sua conta, vá para **.

[O SSPR (Self-Service Password Reset, redefinição de senha de autoatendimento)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) é um recurso de Azure Active Directory (AD) que permite que os usuários resethem suas senhas sem entrar em contato com a equipe de TI para obter ajuda. Os usuários podem se desbloquear rapidamente e continuar trabalhando não importa onde estejam ou a hora do dia. Ao permitir que os funcionários se desbloqueiem, sua organização pode reduzir o tempo não produtivo e os altos custos de suporte para problemas mais comuns relacionados à senha. 

O SSPR tem os seguintes recursos-chave:

* O autoatendimento permite que os usuários finais resetem suas senhas vencidas ou não expiradas sem entrar em contato com um administrador ou helpdesk para obter suporte.

* [Password Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) permite o gerenciamento de senhas no local e resolução de bloqueio de conta na nuvem.

* Os relatórios de atividades de gerenciamento de senhas fornecem aos administradores informações sobre a redefinição de senhas e a atividade de registro que ocorre em sua organização.

## <a name="learn-about-sspr"></a>Conheça o SSPR

Saiba mais sobre o SSPR. [Veja como funciona: Redefinição de senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Principais benefícios

Os principais benefícios da habilitação do SSPR são:

* **Gerenciar o custo**. O SSPR reduz os custos de suporte de TI, permitindo que os usuários resetem senhas por conta própria. Também reduz o custo do tempo perdido devido à perda de senhas e bloqueios. 

* **Experiência intuitiva do usuário**. Ele fornece um processo intuitivo de registro de usuários único que permite que os usuários redefinir senhas e desbloquear contas sob demanda de qualquer dispositivo ou local. O SSPR permite que os usuários voltem ao trabalho mais rápido e sejam mais produtivos.

* **Flexibilidade e segurança.** O SSPR permite que as empresas acessem a segurança e a flexibilidade que uma plataforma em nuvem oferece. Os administradores podem alterar as configurações para acomodar novos requisitos de segurança e implementar essas alterações para os usuários sem interromper seu login.

* **Auditoria robusta e rastreamento de uso**. Uma organização pode garantir que os sistemas de negócios permaneçam seguros enquanto seus usuários redefinem suas próprias senhas. Registros de auditoria robustos incluem informações de cada etapa do processo de redefinição de senha. Esses logs estão disponíveis em uma API e permitem que o usuário importe os dados para um sistema de verificação de incidentes de segurança e monitoramento de eventos (SIEM).

### <a name="licensing"></a>Licenciamento

O Azure Active Directory é licenciado por usuário, o que significa que cada usuário requer uma licença apropriada para os recursos que usa. Recomendamos licenciamento em grupo para SSPR. 

Para comparar edições e recursos e habilitar o licenciamento em grupo ou com base no usuário, consulte [os requisitos de licenciamento para redefinição de senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Para obter mais informações sobre preços, consulte [os preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Pré-requisitos

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada. Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma conta com privilégios de Administrador Global.


### <a name="training-resources"></a>Recursos de treinamento

| Recursos| Link e Descrição |
| - | - |
| vídeos| [Capacite seus usuários com melhor escalabilidade de TI](https://youtu.be/g9RpRnylxS8) 
| |[O que é redefinição de senha de autoatendimento?](https://youtu.be/hc97Yx5PJiM)|
| |[Implantação de redefinição de senha de autoatendimento](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Como configurar a redefinição de senha de autoatendimento para usuários no Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Como [preparar os usuários para] registrar [suas] informações de segurança para o Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Cursos online|[Gerenciamento de identidades no Diretório Ativo do Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Use o SSPR para oferecer aos seus usuários uma experiência moderna e protegida. Consulte especialmente o módulo "[Gerenciando usuários e grupos de diretórios ativos do Azure](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Cursos pagos da Pluralsight |[As Questões de Gestão de Identidade e Acesso](https://www.pluralsight.com/courses/identity-access-management-issues) Saiba mais sobre iam e questões de segurança para estar ciente em sua organização. Consulte especialmente o módulo "Outros Métodos de Autenticação".|
| |[Começando com o Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Aprenda as melhores práticas para estender os ativos locais para a nuvem de uma maneira que permita autenticação, autorização, criptografia e uma experiência móvel segura. Consulte especialmente o módulo "Configurando recursos avançados do Microsoft Azure Active Directory Premium".
|Tutoriais |[Complete um lançamento piloto de redefinição de senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Habilitando o write-back de senha](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Redefinição de senha do Azure AD a partir da tela de login para windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Perguntas frequentes|[Gerenciamento de senhas frequentemente faz perguntas](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Arquitetura da solução

O exemplo a seguir descreve a arquitetura da solução de redefinição de senha para ambientes híbridos comuns.

![diagrama de arquitetura de solução](./media/howto-sspr-deployment//solutions-architecture.png)

Descrição do fluxo de trabalho

Para redefinir a senha, os usuários vão para o [portal de redefinição de senhas](https://aka.ms/sspr). Eles devem verificar o método de autenticação previamente registrado ou métodos para provar sua identidade. Se eles redefinirem a senha com sucesso, eles iniciarão o processo de redefinição.

* Para usuários somente na nuvem, o SSPR armazena a nova senha no Azure AD. 

* Para usuários híbridos, o SSPR regrava a senha do Active Directory on-prem através do serviço Azure AD Connect. 

Nota: Para usuários que possuem [phs (Password hash synchronization, sincronização de hash)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) desativado, o SSPR armazena as senhas apenas no Diretório Ativo on-prem.

### <a name="best-practices"></a>Práticas recomendadas

Você pode ajudar os usuários a se registrar rapidamente, implantando o SSPR ao lado de outro aplicativo ou serviço popular na organização. Esta ação irá gerar um grande volume de logins e irá impulsionar o registro.

Antes de implantar o SSPR, você pode optar por determinar o número e o custo médio de cada chamada de redefinição de senha. Você pode usar essa implantação de postagem de dados para mostrar o valor que o SSPR está trazendo para a organização.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Habilitar registro combinado para SSPR e MFA

A Microsoft recomenda que as organizações habilitem a experiência de registro combinada para SSPR e autenticação multifatorial. Quando você habilita essa experiência de registro combinada, os usuários só precisam selecionar suas informações de registro uma vez para habilitar ambos os recursos.

A experiência de registro combinada não exige que as organizações habilitem a autenticação multifatorial SSPR e Azure. O registro combinado proporciona às organizações uma melhor experiência do usuário. Para obter mais informações, consulte [registro de informações de segurança combinada (visualização)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva as partes interessadas certas

Quando os projetos de tecnologia falham, eles normalmente o fazem devido a expectativas incompatíveis sobre impacto, resultados e responsabilidades. Para evitar essas armadilhas, [certifique-se de que você está engajando os stakeholders certos](https://aka.ms/deploymentplans) e que os papéis das partes interessadas no projeto são bem compreendidos documentando os stakeholders e suas contribuições e responsabilidades do projeto.

#### <a name="required-administrator-roles"></a>Funções de administrador necessárias


| Papel de Negócios/Persona| Função Azure AD (se necessário) |
| - | - |
| Helpdesk nível 1| Administrador de senha |
| Helpdesk nível 2| Administrador de usuários |
| Administrador sspr| Administrador global |


### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Você deve comunicar proativamente com seus usuários como sua experiência mudará, quando ela mudará e como ganhar suporte se eles tiverem problemas. Revise os [materiais de redefinição de senha do self-service no centro de downloads da Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) para obter ideias sobre como planejar sua estratégia de comunicação com o usuário final.

### <a name="plan-a-pilot"></a>Planeje um piloto

Recomendamos que a configuração inicial do SSPR seja em um ambiente de teste. Comece com um grupo piloto habilitando o SSPR para um subconjunto de usuários em sua organização. Veja [as melhores práticas para um piloto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Para criar um grupo, veja como [criar um grupo e adicionar membros no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Configuração do plano

As seguintes configurações são necessárias para habilitar o SSPR juntamente com os valores recomendados.

| Área | Configuração | Valor |
| --- | --- | --- |
| **Propriedades SSPR** | Redefinição de senha de autoatendimento ativada | **Grupo selecionado** para piloto / **Tudo** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para registrar | Sempre 1 mais do que o necessário para reiniciar |
|   | Métodos de autenticação necessários para reiniciar | Um ou dois |
| **Registro** | Exigir que os usuários se registrem ao entrar | Sim |
|   | Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação | 90 a 180 dias |
| **Notificações** | Notificar os usuários de redefinições de senha | Sim |
|   | Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas | Sim |
| **Personalização** | Personalizar o link do helpdesk | Sim |
|   | E-mail ou URL personalizados | Site de suporte ou endereço de e-mail |
| **Integração local** | Escreva senhas de volta para a AD no local | Sim |
|   | Permitir que os usuários desbloqueiem a conta sem redefinir a senha | Sim |

### <a name="sspr-properties"></a>Propriedades SSPR

Ao habilitar o SSPR, escolha um grupo de segurança apropriado no ambiente piloto.

* Para reforçar o registro SSPR para todos, recomendamos o uso da opção **Tudo.**
* Caso contrário, selecione o grupo de segurança Azure AD ou AD apropriado.

### <a name="authentication-methods"></a>Métodos de autenticação

Quando o SSPR estiver ativado, os usuários só podem redefinir sua senha se tiverem dados presentes nos métodos de autenticação que o administrador habilitou. Os métodos incluem telefone, notificação do aplicativo Authenticator, perguntas de segurança, etc. Para obter mais informações, consulte [Quais são os métodos de autenticação?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

Recomendamos as seguintes configurações do método de autenticação:

* Defina os **métodos de autenticação necessários para registrar-se** em pelo menos um a mais do que o número necessário para redefinir. Permitir autenticações múltiplas dá aos usuários flexibilidade quando precisam redefinir.

* Definir **número de métodos necessários para redefinir** para um nível apropriado à sua organização. Um requer o menor atrito, enquanto dois podem aumentar sua postura de segurança. 

Nota: O usuário deve ter os métodos de autenticação configurados nas [políticas e restrições de senha no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Configurações de registro

Definir **Exigir que os usuários registrem-se** ao fazer login em **Sim**. Essa configuração exige que os usuários se registrem ao fazer login, garantindo que todos os usuários estejam protegidos.

Definir **Número de dias antes que os usuários sejam solicitados a confirmar suas informações de autenticação** para entre **90** e **180** dias, a menos que sua organização tenha uma necessidade de negócios para um prazo mais curto.

### <a name="notifications-settings"></a>Configurações de notificações

Configure os **usuários notificar em redefinições de senha** e o Notificar todos os **administradores quando outros administradores redefinirem sua senha** para **Sim**. Selecionar **Sim** em ambos aumenta a segurança, garantindo que os usuários estejam cientes quando sua senha é redefinida. Ele também garante que todos os admins estejam cientes quando um admin altera uma senha. Se os usuários ou admins receberem uma notificação e não iniciarem a alteração, eles poderão relatar imediatamente um possível problema de segurança.

### <a name="customization-settings"></a>Configurações de personalização

É fundamental personalizar o e-mail ou URL do helpdesk para garantir que os usuários que passam por problemas possam obter ajuda imediatamente. Defina essa opção como um endereço de e-mail comum ou uma página da Web com a qual seus usuários estão familiarizados. 

Para obter mais informações, consulte [Personalizar a funcionalidade do Azure AD para redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Write-back de senha

**O Password Writeback** é habilitado com [o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) e grava redefinições de senha na nuvem de volta a um diretório local existente em tempo real. Para obter mais informações, consulte [O que é Password Writeback?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Recomendamos as seguintes configurações:

* Certifique-se de que **as senhas de gravação de volta para a AD no local** estão definidas como **Sim**. 
* Defina o **Permitir que os usuários desbloqueiem a conta sem redefinir** a senha para **Sim**.

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha.

### <a name="administrator-password-setting"></a>Configuração de senha do administrador

As contas do administrador têm permissões elevadas. A empresa local ou os administradores de domínio não podem redefinir suas senhas através do SSPR. As contas de admin no local têm as seguintes restrições:

* só pode alterar sua senha em seu ambiente on-prem.
* nunca pode usar as perguntas e respostas secretas como um método para redefinir sua senha.

Recomendamos que você não sincronize suas contas de administrador do Active Directory on-prem com o Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com múltiplos sistemas de gerenciamento de identidade

Alguns ambientes possuem múltiplos sistemas de gerenciamento de identidade. Os gerentes de identidade on-premesis, como Oracle AM e SiteMinder, exigem sincronização com a AD para senhas. Você pode fazer isso usando uma ferramenta como o PCNS (Password Change Notification Service, serviço de notificação de alteração de senha) com o Mim (Microsoft Identity Manager). Para encontrar informações sobre este cenário mais complexo, consulte o artigo [Implante o Serviço de Notificação de Alteração de Senha MIM em um controlador de domínio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Teste e suporte de planos

Em cada etapa de sua implantação desde os grupos piloto iniciais até a organização, certifique-se de que os resultados sejam como esperado.

### <a name="plan-testing"></a>Teste de plano

Para garantir que sua implantação funcione como esperado, planeje um conjunto de casos de teste para validar a implementação. Para avaliar os casos de teste, você precisa de um usuário de teste não-administrador com uma senha. Se você precisar criar um usuário, consulte [Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

A tabela a seguir inclui cenários de teste úteis que você pode usar para documentar os resultados esperados de suas organizações com base em suas políticas.
<br>


| caso comercial| Resultados esperados |
| - | - |
| O portal SSPR é acessível dentro da rede corporativa| Determinado por sua organização |
| Portal SSPR é acessível de fora da rede corporativa| Determinado por sua organização |
| Redefinir a senha de usuário do navegador quando o usuário não está habilitado para redefinição de senha| O usuário não pode acessar o fluxo de redefinição de senha |
| Redefinir a senha de usuário do navegador quando o usuário não se registrou para redefinição de senha| O usuário não pode acessar o fluxo de redefinição de senha |
| O usuário faz o cadastro quando é aplicado para fazer o registro de redefinição de senha| Solicita ao usuário que registre informações de segurança |
| O usuário faz o cadastro quando o registro de redefinição de senha está concluído| Solicita ao usuário que registre informações de segurança |
| O portal SSPR é acessível quando o usuário não tem uma licença| É acessível |
| Redefinir a senha de usuário do Windows 10 Azure AD juntou-se ou a tela de bloqueio do azure híbrido aderiu ao dispositivo| O usuário pode redefinir a senha |
| Os dados de registro e uso do SSPR estão disponíveis para os administradores em tempo quase real| Está disponível através de registros de auditoria |

Você também pode consultar [a Concluir um rolo piloto de redefinição de senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). Neste tutorial, você habilitará um lançamento piloto de SSPR em sua organização e testará usando uma conta não-administradora.

### <a name="plan-support"></a>Suporte ao plano

Embora o SSPR não crie normalmente problemas de usuário, é importante preparar a equipe de suporte para lidar com problemas que possam surgir. Embora um administrador possa redefinir a senha para usuários finais através do portal Azure AD, é melhor ajudar a resolver o problema através de um processo de suporte ao autoatendimento.

Para habilitar o sucesso da sua equipe de suporte, você pode criar uma FAQ baseada em perguntas que você recebe de seus usuários. Veja alguns exemplos:

| Cenários| Descrição |
| - | - |
| O usuário não tem nenhum método de autenticação registrado disponível| Um usuário está tentando redefinir sua senha, mas não tem nenhum dos métodos de autenticação que registrou disponível (Exemplo: ele deixou seu celular em casa e não pode acessar o e-mail) |
| O usuário não está recebendo um texto ou chamada em seu escritório ou celular| Um usuário está tentando verificar sua identidade via texto ou chamada, mas não está recebendo um texto/chamada. |
| O usuário não pode acessar o portal de redefinição de senha| Um usuário quer redefinir sua senha, mas não está habilitado para redefinição de senha e não pode acessar a página para atualizar senhas. |
| O usuário não pode definir uma nova senha| Um usuário conclui a verificação durante o fluxo de redefinição de senha, mas não pode definir uma nova senha. |
| O usuário não vê um link de senha de redefinição em um dispositivo Windows 10| Um usuário está tentando redefinir a senha da tela de bloqueio do Windows 10, mas o dispositivo não está unido ao Azure AD, ou a política do dispositivo Intune não está ativada |

### <a name="plan-roll-back"></a>Plano reverter

Para reverter a implantação:

* para um único usuário, remova o usuário do grupo de segurança 

* para um grupo, remova o grupo da configuração SSPR

* Para todos, desabilite o SSPR para o inquilino Azure AD

## <a name="deploy-sspr"></a>Implantar SSPR

Antes de implantar, certifique-se de que você fez o seguinte:

1. Criou e começou a executar seu [plano de comunicação.](#plan-communications)

1. Determinou as [configurações apropriadas](#plan-configuration).

1. Identificou os usuários e grupos para os ambientes [piloto](#plan-a-pilot) e de produção.

1. [Configurações determinadas](#plan-configuration) para registro e autoatendimento.

1. [Regravação de senha configurada](#password-writeback) se você tiver um ambiente híbrido.


**Agora você está pronto para implantar SSPR!**

Consulte [Ativar a redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) para instruções completas passo a passo na configuração das seguintes áreas.

1. [Métodos de autenticação](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Configurações de registro](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Configurações de notificações](#notifications-settings)

1. [Configurações de personalização](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integração local](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Habilite o SSPR no Windows
Para máquinas que executam o Windows 7, 8, 8.1 e 10, você pode [permitir que os usuários redefinir sua senha na tela de login do Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Gerenciar SSPR

O Azure AD pode fornecer informações adicionais sobre o desempenho do SSPR através de auditorias e relatórios.

### <a name="password-management-activity-reports"></a>Relatórios de atividades de gerenciamento de senhas 

Você pode usar relatórios pré-construídos no portal Azure para medir o desempenho do SSPR. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas. Para obter mais informações, consulte [opções de relatórios para gerenciamento de senhas AD do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  Você deve ser [um administrador global](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles), e você deve optar por esses dados a serem coletados para sua organização. Para participar, você deve visitar a guia Relatórios ou os registros de auditoria no Portal Azure pelo menos uma vez. Até lá, os dados não são coletados para sua organização.

Os registros de auditoria para registro e redefinição de senha estão disponíveis por 30 dias. Se a auditoria de segurança dentro de sua empresa exigir maior retenção, os logs precisam ser exportados e consumidos em uma ferramenta SIEM, como [Azure Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)Splunk ou ArcSight.

![Captura de tela de relatórios SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Métodos de autenticação- Uso e Insights

[O uso e os insights](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) permitem que você entenda como os métodos de autenticação de recursos como OZure MFA e SSPR estão funcionando em sua organização. Esse recurso de relatóriofornece à sua organização os meios para entender quais métodos registram e como usá-los.

### <a name="troubleshoot"></a>Solucionar problemas

* Consulte a [redefinição de senha de autoatendimento de Solução de Problemas](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Siga [o gerenciamento de senhas com perguntas frequentes](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Documentação útil

* [Quais são os métodos de autenticação?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Como funciona: Redefinição de senha de autoatendimento do Azure AD?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Personalizar a funcionalidade de Autoatendimento de Redefinição de Senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Políticas e restrições de senha do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [O que é write-back de senha?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Próximas etapas

* Para começar a implantar o SSPR, consulte [Concluir um piloto de redefinição de senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)

* [Considere implementar a proteção por senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Considere implementar o Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)