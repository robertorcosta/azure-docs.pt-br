---
title: Práticas recomendadas de segurança de área de trabalho virtual do Windows-Azure
description: Práticas recomendadas para manter o ambiente de área de trabalho virtual do Windows seguro.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5330c427088734b049b2cb4f7735ac0099a52b47
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966669"
---
# <a name="security-best-practices"></a>Melhores práticas de segurança

A área de trabalho virtual do Windows é um serviço de área de trabalho virtual gerenciado que inclui vários recursos de segurança para manter sua organização segura. Em uma implantação de área de trabalho virtual do Windows, a Microsoft gerencia partes dos serviços em nome do cliente. O serviço tem muitos recursos internos de segurança avançada, como o inverso Connect, que reduz o risco envolvido em ter áreas de trabalho remotas acessíveis de qualquer lugar.

Este artigo descreve as etapas adicionais que você pode executar como administrador para manter as implantações de área de trabalho virtual do Windows de seus clientes seguras.

## <a name="security-responsibilities"></a>Responsabilidades de segurança

O que torna os serviços de nuvem diferentes das VDIs (infraestruturas de área de trabalho virtuais) tradicionais locais são como eles lidam com as responsabilidades de segurança. Por exemplo, em um VDI local tradicional, o cliente seria responsável por todos os aspectos da segurança. No entanto, na maioria dos serviços de nuvem, essas responsabilidades são compartilhadas entre o cliente e a empresa.

Quando você usa a área de trabalho virtual do Windows, é importante entender que, embora alguns componentes já sejam protegidos para seu ambiente, você precisará configurar outras áreas para se adequar às necessidades de segurança de sua organização.

Aqui estão as necessidades de segurança que você é responsável em sua implantação de área de trabalho virtual do Windows:

| Necessidade de segurança | O cliente é responsável por isso? |
|---------------|:-------------------------:|
|Identidade|Sim|
|Dispositivos de usuário (móvel e PC)|Sim|
|Segurança do aplicativo|Sim|
|Sistema operacional host da sessão|Sim|
|Configuração de implantação|Sim|
|Controles de rede|Sim|
|Plano de controle de virtualização|Não|
|Hosts físicos|Não|
|Rede física|Não|
|Datacenter físico|Não|

As necessidades de segurança que o cliente não é responsável pelo são manipuladas pela Microsoft.

## <a name="azure-security-best-practices"></a>Práticas recomendadas de segurança do Azure

A área de trabalho virtual do Windows é um serviço no Azure. Para maximizar a segurança de sua implantação de área de trabalho virtual do Windows, certifique-se de proteger também a infraestrutura e o plano de gerenciamento em torno do Azure. Para proteger sua infraestrutura, considere como a área de trabalho virtual do Windows se adapta ao seu ecossistema maior do Azure. Para saber mais sobre o ecossistema do Azure, consulte [padrões e práticas recomendadas de segurança do Azure](../security/fundamentals/best-practices-and-patterns.md).

Esta seção descreve as práticas recomendadas para proteger seu ecossistema do Azure.

### <a name="enable-azure-security-center"></a>Habilitar a Central de Segurança do Azure

Recomendamos que você habilite a central de segurança do Azure Standard para assinaturas, máquinas virtuais, cofres de chaves e contas de armazenamento.

Com a central de segurança do Azure Standard, você pode:

* Gerenciar vulnerabilidades.
* Avalie a conformidade com estruturas comuns como o PCI.
* Fortaleça a segurança geral do seu ambiente.

Para saber mais, confira integrar [sua assinatura do Azure à central de segurança Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Melhorar sua pontuação segura

A pontuação segura fornece recomendações e conselhos de práticas recomendadas para melhorar a segurança geral. Essas recomendações são priorizadas para ajudá-lo a escolher quais são as mais importantes e as opções de correção rápida o ajudam a resolver vulnerabilidades potenciais rapidamente. Essas recomendações também são atualizadas com o passar do tempo, mantendo você atualizado sobre as melhores maneiras de manter a segurança do seu ambiente. Para saber mais, confira [melhorar sua pontuação segura na central de segurança do Azure](../security-center/security-center-secure-score.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Práticas recomendadas de segurança de área de trabalho virtual do Windows

A área de trabalho virtual do Windows tem muitos controles de segurança internos. Nesta seção, você aprenderá sobre os controles de segurança que pode usar para manter seus usuários e dados seguros.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

Exigir a autenticação multifator para todos os usuários e administradores na área de trabalho virtual do Windows melhora a segurança de toda a implantação. Para saber mais, confira [habilitar a autenticação multifator do Azure para área de trabalho virtual do Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Habilitar acesso condicional

Habilitar o [acesso condicional](../active-directory/conditional-access/best-practices.md) permite que você gerencie riscos antes de conceder aos usuários acesso ao seu ambiente de área de trabalho virtual do Windows. Ao decidir a quais usuários conceder acesso, recomendamos que você também considere quem é o usuário, como eles se conectam e qual dispositivo ele está usando.

### <a name="collect-audit-logs"></a>Coletar logs de auditoria

Habilitar a coleta de log de auditoria permite exibir a atividade de usuário e administrador relacionada à área de trabalho virtual do Windows. Alguns exemplos de logs de auditoria de chave são:

-   [Log de Atividades do Azure](../azure-monitor/platform/activity-log-collect.md)
-   [Log de atividades Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Hosts de sessão](../azure-monitor/platform/agent-windows.md)
-   [Log de diagnóstico de área de trabalho virtual do Windows](../virtual-desktop/diagnostics-log-analytics.md)
-   [Logs do Key Vault](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Usar RemoteApps

Ao escolher um modelo de implantação, você pode fornecer aos usuários remotos acesso a áreas de trabalho virtuais inteiras ou apenas selecionar aplicativos. Os aplicativos remotos ou RemoteApps fornecem uma experiência direta à medida que o usuário trabalha com aplicativos em sua área de trabalho virtual. Os RemoteApps reduzem o risco, permitindo que o usuário trabalhe com um subconjunto do computador remoto exposto pelo aplicativo.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorar o uso com Azure Monitor

Monitore o uso e a disponibilidade do serviço de área de trabalho virtual do Windows com o [Azure monitor](https://azure.microsoft.com/services/monitor/). Considere a criação de [alertas de integridade do serviço](../service-health/alerts-activity-log-service-notifications.md) para o serviço de área de trabalho virtual do Windows para receber notificações sempre que houver um evento de impacto de serviço.

## <a name="session-host-security-best-practices"></a>Práticas recomendadas de segurança de host de sessão

Os hosts de sessão são máquinas virtuais que são executadas dentro de uma assinatura do Azure e rede virtual. A segurança geral da implantação de área de trabalho virtual do Windows depende dos controles de segurança que você colocou em seus hosts de sessão. Esta seção descreve as práticas recomendadas para manter seus hosts de sessão seguros.

### <a name="enable-endpoint-protection"></a>Habilitar o Endpoint Protection

Para proteger sua implantação de softwares mal-intencionados conhecidos, é recomendável habilitar o Endpoint Protection em todos os hosts de sessão. Você pode usar o Windows Defender antivírus ou um programa de terceiros. Para saber mais, consulte [o guia de implantação do Windows Defender antivírus em um ambiente de VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus). 

Para soluções de perfil como FSLogix ou outras soluções que montam arquivos VHD, recomendamos a exclusão de extensões de arquivo VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Instalar um produto de detecção e resposta de ponto de extremidade

Recomendamos que você instale um produto EDR (detecção de ponto de extremidade e resposta) para fornecer recursos avançados de detecção e resposta. Para sistemas operacionais de servidor com a [central de segurança do Azure](../security-center/security-center-services.md) habilitada, a instalação de um produto EDR implantará o defender ATP. Para sistemas operacionais cliente, você pode implantar o [defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) ou um produto de terceiros para esses pontos de extremidade.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Habilitar avaliações de ameaças e gerenciamento de vulnerabilidades

Identificar vulnerabilidades de software que existem em sistemas operacionais e aplicativos é essencial para manter seu ambiente seguro. A central de segurança do Azure pode ajudá-lo a identificar pontos de problemas por meio de avaliações de vulnerabilidade para sistemas operacionais de servidor. Você também pode usar o defender ATP, que fornece gerenciamento de ameaças e vulnerabilidades para sistemas operacionais de desktop. Você também pode usar produtos de terceiros se estiver tão inclinado, embora seja recomendável usar a central de segurança do Azure e o defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Corrigir vulnerabilidades de software em seu ambiente

Depois de identificar uma vulnerabilidade, você deve corrigi-la. Isso também se aplica a ambientes virtuais, o que inclui os sistemas operacionais em execução, os aplicativos que são implantados dentro deles e as imagens das quais você cria novos computadores. Siga suas comunicações de notificação de patch do fornecedor e aplique patches em tempo hábil. Recomendamos aplicar patches de suas imagens base mensalmente para garantir que as máquinas implantadas recentemente sejam as mais seguras possíveis.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Estabelecer o máximo de políticas de desconexão e tempo inativo

A entrada de usuários quando eles estão inativos preserva recursos e impede o acesso por usuários não autorizados. Recomendamos que os tempos limite sejam cobrados pela produtividade do usuário, bem como pelo uso de recursos. Para usuários que interagem com aplicativos sem estado, considere políticas mais agressivas que desligam máquinas e preservam recursos. Desconectar aplicativos de longa execução que continuam a ser executados se um usuário estiver ocioso, como uma simulação ou renderização CAD, poderá interromper o trabalho do usuário e poderá até mesmo exigir a reinicialização do computador.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Configurar bloqueios de tela para sessões ociosas

Você pode impedir o acesso indesejado ao sistema Configurando a área de trabalho virtual do Windows para bloquear a tela de um computador durante o tempo ocioso e exigir autenticação para desbloqueá-la.

### <a name="establish-tiered-admin-access"></a>Estabelecer acesso de administrador em camadas

Recomendamos que você não conceda aos usuários acesso de administrador a áreas de trabalho virtuais. Se você precisar de pacotes de software, recomendamos torná-los disponíveis por meio de utilitários de gerenciamento de configuração, como o Microsoft Endpoint Manager. Em um ambiente de várias sessões, recomendamos que você não permita que os usuários instalem o software diretamente.

### <a name="consider-which-users-should-access-which-resources"></a>Considere quais usuários devem acessar quais recursos

Considere os hosts de sessão como uma extensão de sua implantação de desktop existente. Recomendamos que você controle o acesso a recursos de rede da mesma maneira que faria para outras áreas de trabalho em seu ambiente, como usar a segmentação e a filtragem de rede. Por padrão, os hosts de sessão podem se conectar a qualquer recurso na Internet. Há várias maneiras de limitar o tráfego, incluindo o uso do firewall do Azure, de soluções de virtualização de rede ou de proxies. Se você precisar limitar o tráfego, certifique-se de adicionar as regras apropriadas para que a área de trabalho virtual do Windows possa funcionar corretamente.

### <a name="manage-office-pro-plus-security"></a>Gerenciar a segurança do Office Pro Plus

Além de proteger seus hosts de sessão, é importante também proteger os aplicativos em execução dentro deles. O Office Pro Plus é um dos aplicativos mais comuns implantados em hosts de sessão. Para melhorar a segurança de implantação do Office, recomendamos que você use o [Orientador de política de segurança](/DeployOffice/overview-of-security-policy-advisor) para aplicativos Microsoft 365 para empresas. Essa ferramenta identifica as políticas que podem ser aplicadas à sua implantação para obter mais segurança. O Orientador de política de segurança também recomenda políticas com base no impacto de sua segurança e produtividade.

### <a name="other-security-tips-for-session-hosts"></a>Outras dicas de segurança para hosts de sessão

Ao restringir os recursos do sistema operacional, você pode reforçar a segurança de seus hosts de sessão. Aqui estão algumas coisas que você pode fazer:

- Controlar o redirecionamento de dispositivo redirecionando unidades, impressoras e dispositivos USB para o dispositivo local de um usuário em uma sessão de área de trabalho remota. Recomendamos que você avalie os requisitos de segurança e verifique se esses recursos deve ser desabilitado ou não.

- Restrinja o acesso ao Windows Explorer ocultando mapeamentos de unidade locais e remotos. Isso impede que os usuários descubram informações indesejadas sobre a configuração do sistema e os usuários.

- Evite o acesso direto de RDP a hosts de sessão em seu ambiente. Se você precisar de acesso de RDP direto para administração ou solução de problemas, habilite o acesso [just-in-time](../security-center/security-center-just-in-time.md) para limitar a possível superfície de ataque em um host de sessão.

- Conceda permissões limitadas aos usuários quando acessarem sistemas de arquivos locais e remotos. Você pode restringir as permissões, certificando-se de que seus sistemas de arquivos locais e remotos usem listas de controle de acesso com privilégios mínimos. Dessa forma, os usuários só podem acessar o que precisam e não podem alterar ou excluir recursos críticos.

- Impedir que software indesejado seja executado em hosts de sessão. Você pode habilitar o armário de aplicativo para segurança adicional em hosts de sessão, garantindo que somente os aplicativos permitidos possam ser executados no host.

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar a autenticação multifator, consulte [Configurar a autenticação multifator](set-up-mfa.md).