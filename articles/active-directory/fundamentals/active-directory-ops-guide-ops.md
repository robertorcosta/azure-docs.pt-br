---
title: Referência do guia de operações gerais do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve tomar para garantir operações gerais
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422942"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referência do guia de operações gerais do Azure Active Directory

Esta seção do guia de referência de [operações azure AD](active-directory-ops-guide-intro.md) descreve as verificações e ações que você deve tomar para otimizar as operações gerais do Azure Active Directory (Azure AD).

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar com o tempo. As organizações devem avaliar continuamente suas práticas operacionais à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

O gerenciamento do Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de implantação. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Melhorias de unidade na pontuação segura de identidade | Equipe de Operações InfoSec |
| Manter servidores ad connect do Azure | Equipe de Operações IAM |
| Executar e triagem regularmente relatórios IdFix | Equipe de Operações IAM |
| Triage Azure AD conecta alertas de saúde para sincronização e FS AD | Equipe de Operações IAM |
| Se não estiver usando o Azure AD Connect Health, o cliente tem processos e ferramentas equivalentes para monitorar a infra-estrutura personalizada | Equipe de Operações IAM |
| Se não estiver usando AD FS, então o cliente tem processos e ferramentas equivalentes para monitorar a infra-estrutura personalizada | Equipe de Operações IAM |
| Monitor alogs híbridos: conectores proxy do aplicativo AZure AD | Equipe de Operações IAM |
| Monitor a logs híbridos: agentes de autenticação de passagem | Equipe de Operações IAM |
| Monitor de registros híbridos: serviço de regravação de senha | Equipe de Operações IAM |
| Monitor Hybrid Logs: Gateway de proteção por senha no local | Equipe de Operações IAM |
| Monitor alogs híbridos: extensão NPS Do Azure MFA (se aplicável) | Equipe de Operações IAM |

Ao revisar sua lista, você pode achar que precisa atribuir um proprietário para tarefas que estão faltando a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhados com as recomendações acima.

#### <a name="owners-recommended-reading"></a>Proprietários recomendaram leitura

- [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governança no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Gestão híbrida

### <a name="recent-versions-of-on-premises-components"></a>Versões recentes de componentes no local

Ter as versões mais atualizadas dos componentes locais fornece ao cliente todas as atualizações de segurança mais recentes, melhorias de desempenho, bem como funcionalidades que podem ajudar a simplificar ainda mais o ambiente. A maioria dos componentes tem uma configuração de atualização automática, que automatizará o processo de atualização.

Esses componentes incluem:

- Azure AD Connect
- Conectores do Proxy de Aplicativo do Azure AD
- Agentes de autenticação ad pass-through do Azure
- Azure AD Conecta Agentes de Saúde

A menos que um tenha sido estabelecido, você deve definir um processo para atualizar esses componentes e confiar no recurso de upgrade automático sempre que possível. Se você encontrar componentes com seis ou mais meses de atraso, você deve atualizar o mais rápido possível.

#### <a name="hybrid-management-recommended-reading"></a>Leitura recomendada de gestão híbrida

- [Azure AD Connect: atualização automática](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Entenda os conectores proxy do aplicativo Azure AD | Atualizações automáticas](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health alerta linha de base

As organizações devem implantar [o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) para monitoramento e emissão de relatórios do Azure AD Connect e do AD FS. O Azure AD Connect e o AD FS são componentes críticos que podem quebrar o gerenciamento e a autenticação do ciclo de vida e, portanto, levar a paralisações. O Azure AD Connect Health ajuda a monitorar e obter insights sobre sua infra-estrutura de identidade no local, garantindo assim a confiabilidade do seu ambiente.

![Arquitetura Azure AD Connect Heath](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Ao monitorar a saúde do seu ambiente, você deve abordar imediatamente quaisquer alertas de alta gravidade, seguidos de alertas de menor gravidade.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Leitura recomendada do Azure AD Connect Health

- [Instalação do Agente do Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Registros de agentes no local

Alguns serviços de gerenciamento de identidade e acesso exigem agentes locais para habilitar cenários híbridos. Exemplos incluem redefinição de senha, autenticação de passagem (PTA), proxy de aplicativo Ad do Azure e extensão NPS do Azure MFA. É fundamental que a equipe de operações monitore a saúde desses componentes arquivando e analisando os registros dos agentes componentes usando soluções como System Center Operations Manager ou SIEM. É igualmente importante que sua equipe de operações infosec ou help desk entenda como solucionar problemas de erros.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Os registros de agentes no local recomendam a leitura

- [Solucionar problemas de Proxy de Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Solução de problemas de redefinição de senha por autoatendimento – Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Entenda os conectores proxy do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: Autenticação de passagem de solução de problemas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Solucionar problemas códigos de erro para a extensão Azure MFA NPS](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Gestão de agentes no local

A adoção de melhores práticas pode ajudar na ótima operação dos agentes locais. Considere as seguintes práticas recomendadas:

- Vários conectores proxy do aplicativo Azure AD por grupo de conectores são recomendados para fornecer balanceamento de carga perfeito e alta disponibilidade, evitando pontos únicos de falha ao acessar os aplicativos proxy. Se atualmente você tiver apenas um conector em um grupo de conectores que lida com aplicativos em produção, você deve implantar pelo menos dois conectores para redundância.
- Criar e usar um grupo de conector proxy de aplicativo para fins de depuração pode ser útil para solucionar problemas e ao embarcar novos aplicativos no local. Também recomendamos a instalação de ferramentas de rede, como o Analisador de Mensagens e o Fiddler nas máquinas de conectores.
- Vários agentes de autenticação de passagem são recomendados para fornecer balanceamento de carga perfeito e alta disponibilidade, evitando um único ponto de falha durante o fluxo de autenticação. Certifique-se de implantar pelo menos dois agentes de autenticação de passagem para redundância.

#### <a name="on-premises-agents-management-recommended-reading"></a>A gestão de agentes no local recomendou a leitura

- [Entenda os conectores proxy do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Autenticação de passagem ad do Azure AD - quickstart](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Gestão em escala

### <a name="identity-secure-score"></a>Pontuação da segurança de identidade

A [pontuação de segurança de identidade](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) fornece uma medida quantificável da postura de segurança de sua organização. É fundamental revisar e abordar constantemente os achados relatados e se esforçar para ter a maior pontuação possível. A classificação ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade
- Planejar aprimoramentos de segurança de identidade
- Examinar o sucesso das suas melhorias

![Classificação de segurança](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se a sua organização atualmente não possui um programa para monitorar as alterações no Score de Segurança de Identidade, recomenda-se que você implemente um plano e designe os proprietários para monitorar e impulsionar ações de melhoria. As organizações devem remediar ações de melhoria com impacto de pontuação superior a 30 o mais rápido possível.

### <a name="notifications"></a>Notificações

A Microsoft envia comunicações de e-mail aos administradores para notificar várias alterações no serviço, atualizações de configuração necessárias e erros que requerem intervenção administrativa. É importante que os clientes definam os endereços de e-mail de notificação para que as notificações sejam enviadas aos membros da equipe adequada que possam reconhecer e agir sobre todas as notificações. Recomendamos que você adicione vários destinatários ao [Centro de Mensagens office 365](https://docs.microsoft.com/office365/admin/manage/message-center) e solicite que as notificações (incluindo notificações do Azure AD Connect Health) sejam enviadas para uma lista de distribuição ou caixa de correio compartilhada. Se você tiver apenas uma conta de administração global com um endereço de e-mail, certifique-se de configurar pelo menos duas contas com capacidade de e-mail.

Existem dois endereços "From" usados <o365mc@email2.microsoft.com>pelo Azure AD: , que envia notificações do Centro de Mensagens do Office 365; e <azure-noreply@microsoft.com>, que envia notificações relacionadas a:

- [Revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Proteção de identidade Azure AD](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Gerenciamento de identidades com privilégios do AD do Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Notificação de certificado expirando do Aplicativo Enterprise](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Notificações do serviço de provisionamento do Aplicativo Enterprise

Consulte a tabela a seguir para saber o tipo de notificações enviadas e onde verificar:

| Fonte de notificação | O que é enviado | Onde verificar |
|:-|:-|:-|
| Contato técnico | Erros de sincronização | Portal Azure - lâmina de propriedades |
| Centro de Mensagens do Office 365 | Avisos de incidente e degradação de Serviços de Identidade e serviços de back-end da O365 | Portal do Escritório |
| Identity Protection Weekly Digest | Identity Protection Digest | Lâmina de proteção de identidade Azure AD |
| Azure AD Connect Health | Notificações de alerta | Portal Azure - Azure AD Connect Health blade |
| Notificações de aplicativos corporativos | Notificações quando os certificados estão prestes a expirar e erros de provisionamento | Portal Azure - Lâmina de aplicativo corporativo (cada aplicativo tem sua própria configuração de endereço de e-mail) |

#### <a name="notifications-recommended-reading"></a>Notificações recomendadas de leitura

- [Altere o endereço da sua organização, contato técnico e muito mais - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Área de superfície operacional

### <a name="ad-fs-lockdown"></a>Bloqueio aD FS

As organizações, que configuram aplicativos para autenticar diretamente no Azure AD, se beneficiam do [bloqueio inteligente Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Se você usar OD FS no Windows Server 2012 R2, implemente a [proteção de bloqueio extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)AD FS . Se você usar OD FS no Windows Server 2016 ou posterior, implemente [o bloqueio inteligente extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). No mínimo, recomendamos que você habilite o bloqueio extranet para conter o risco de ataques de força bruta contra o Active Directory no local. No entanto, se você tem AD FS no Windows 2016 ou superior, você também deve habilitar o bloqueio inteligente extranet que ajudará a mitigar ataques [de spray de senha.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Se o AD FS for usado apenas para a federação Azure AD, existem alguns pontos finais que podem ser desligados para minimizar a área da superfície de ataque. Por exemplo, se o AD FS for usado apenas para o Azure AD, você deve desativar os pontos finais do WS-Trust que não sejam os pontos finais habilitados para **o usuáriomixado** e **o transporte do Windows**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Acesso a máquinas com componentes de identidade no local

As organizações devem bloquear o acesso às máquinas com componentes híbridos no local da mesma forma que seu domínio local. Por exemplo, um operador de backup ou administrador Hyper-V não deve ser capaz de fazer login no Azure AD Connect Server para alterar regras.

O modelo de nível administrativo do Active Directory foi projetado para proteger sistemas de identidade usando um conjunto de zonas de buffer entre o controle total do ambiente (Nível 0) e os ativos de estação de trabalho de alto risco que os invasores frequentemente comprometem. ![Diagrama que mostra as três camadas do modelo de Camadas](./media/active-directory-ops-guide/active-directory-ops-img18.png)

O modelo de [nível](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) é composto por três níveis e inclui apenas contas administrativas, não contas de usuário padrão.

- **Nível 0** - Controle Direto das identidades das empresas no ambiente. A Camada 0 inclui contas, grupos e outros recursos que têm controle administrativo direto ou indireto sobre as florestas, domínios ou controladores de domínio do Active Directory, e todos os ativos que eles contêm. A sensibilidade da segurança de todos os ativos de Camada 0 é equivalente, pois todos estão controlando uns aos outros.
- **Nível 1** - Controle de servidores e aplicativos corporativos. Entre os ativos de Camada 1 estão sistemas operacionais de servidor, serviços de nuvem e aplicativos corporativos. Contas de administrador de Camada 1 têm controle administrativo sobre uma quantidade considerável de conteúdo corporativo hospedada nesses ativos. Uma função de exemplo comum é a de administradores de servidor, que proporciona a esses sistemas operacionais a capacidade de afetar todos os serviços corporativos.
- **Nível 2** - Controle de estações de trabalho e dispositivos de usuário. Contas de administrador de Camada 2 têm controle administrativo sobre uma quantidade considerável de conteúdo corporativo hospedada em estações de trabalho e dispositivos do usuário. Entre os exemplos estão Suporte técnico e administradores de suporte ao computador, pois podem afetar a integridade de quase todos os dados do usuário.

Bloqueie o acesso a componentes de identidade no local, como os serviços Azure AD Connect, AD FS e SQL da mesma forma que você faz para controladores de domínio.

## <a name="summary"></a>Resumo

Há sete aspectos para uma infra-estrutura de identidade segura. Esta lista irá ajudá-lo a encontrar as ações que você deve tomar para otimizar as operações do Azure Active Directory (Azure AD).

- Designe proprietários para tarefas-chave.
- Automatize o processo de atualização para componentes híbridos no local.
- Implantar o Azure AD Connect Health para monitoramento e emissão de relatórios do Azure AD Connect e do AD FS.
- Monitore a saúde dos componentes híbridos no local, arquivando e analisando os registros dos agentes componentes usando o System Center Operations Manager ou uma solução SIEM.
- Implemente melhorias de segurança medindo sua postura de segurança com o Score de segurança de identidade.
- Bloqueie a AD FS.
- Bloqueie o acesso a máquinas com componentes de identidade no local.

## <a name="next-steps"></a>Próximas etapas

Consulte os [planos de implantação do Azure AD](active-directory-deployment-plans.md) para obter detalhes de implementação sobre quaisquer recursos que você não tenha implantado.
