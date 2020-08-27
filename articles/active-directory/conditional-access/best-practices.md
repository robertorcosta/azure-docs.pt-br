---
title: Práticas recomendadas para acesso condicional no Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as coisas que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5536c3c427e5b6225d81d649722d8af48c23091
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948446"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Práticas recomendadas para acesso condicional no Azure Active Directory

Com o [acesso condicional do Azure Active Directory (AD do Azure)](./overview.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Este artigo fornece informações sobre:

- O que você deve saber 
- O que você deve evitar fazer ao configurar políticas de acesso condicional. 

Este artigo pressupõe que você esteja familiarizado com os conceitos e a terminologia descrita em [o que é o acesso condicional no Azure Active Directory?](./overview.md)

## <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma política funcionar?

Ao criar uma nova política, não há usuários, grupos, aplicativos ou controles de acesso selecionados.

![Aplicativos na nuvem](./media/best-practices/02.png)

Para fazer sua política funcionar, você precisa configurar:

| O que           | Como                                  | Por que |
| :--            | :--                                  | :-- |
| **Aplicativos de nuvem** |Selecione um ou mais aplicativos.  | O objetivo de uma política de acesso condicional é permitir que você controle como os usuários autorizados podem acessar os aplicativos de nuvem.|
| **Usuários e grupos** | Selecione pelo menos um usuário ou grupo que esteja autorizado a acessar seus aplicativos de nuvem selecionados. | Uma política de acesso condicional que não tem usuários e grupos atribuídos nunca é disparada. |
| **Controles de acesso** | Selecione pelo menos um controle de acesso. | Se suas condições forem satisfeitas, o processador de política precisará saber o que fazer. |

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="how-are-conditional-access-policies-applied"></a>Como as políticas de acesso condicional são aplicadas?

Mais de uma política de acesso condicional pode ser aplicada quando você acessa um aplicativo de nuvem. Nesse caso, todas as políticas que se aplicam devem ser atendidas. Por exemplo, se uma política exigir a MFA (autenticação multifator) e outra exigir um dispositivo em conformidade, você deverá concluir a MFA e usar um dispositivo compatível. 

Todas as políticas são impostas em duas fases:

- Fase 1: coletar detalhes da sessão 
   - Colete detalhes da sessão, como a localização do usuário e a identidade do dispositivo que serão necessários para a avaliação da política. 
   - Durante essa fase, os usuários poderão ver um prompt de certificado se a conformidade do dispositivo fizer parte de suas políticas de acesso condicional. Esse prompt pode ocorrer para aplicativos de navegador quando o sistema operacional do dispositivo não é o Windows 10. 
   - A fase 1 da avaliação de política ocorre para políticas e políticas habilitadas no [modo somente de relatório](concept-conditional-access-report-only.md).
- Fase 2: imposição 
   - Use os detalhes da sessão coletados na fase 1 para identificar os requisitos que não foram atendidos. 
   - Se houver uma política configurada para bloquear o acesso, com o controle de concessão de bloqueio, a imposição será interrompida aqui e o usuário será bloqueado. 
   - O usuário será solicitado a concluir os requisitos adicionais de controle de concessão que não foram atendidos durante a fase 1 na seguinte ordem, até que a política seja satisfeita:  
      - Autenticação multifator 
      - Aplicativo cliente aprovado/política de proteção de aplicativo 
      - Dispositivo gerenciado (ingresso em conformidade ou Azure AD híbrido) 
      - Termos de uso 
      - Controles personalizados  
      - Depois que os controles de concessão forem satisfeitos, aplique os controles de sessão (aplicativo imposto, Microsoft Cloud App Security e tempo de vida do token) 
   - A fase 2 da avaliação de política ocorre para todas as políticas habilitadas. 

### <a name="how-are-assignments-evaluated"></a>Como as atribuições são avaliadas?

Todas as atribuições são avaliadas com **AND** lógicos. Se você tiver mais de uma atribuição configurada, todas as atribuições deverão ser atendidas para disparar uma política.  

Se for necessário configurar uma condição de local que se aplique a todas as conexões feitas de fora da rede da organização:

- Incluindo **Todos os locais**
- Excluir **Todos os IPs confiáveis**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>O que fazer se você estiver bloqueado no portal de administração do Azure Active Directory?

Se você estiver bloqueado no portal do AD do Azure devido a uma configuração incorreta em uma política de acesso condicional:

- Verifique se existem outros administradores em sua organização que ainda não estão bloqueados. Um administrador com acesso ao portal do Azure pode desabilitar a política que está afetando sua entrada. 
- Se nenhum dos administradores da sua organização puder atualizar a política, será necessário enviar uma solicitação de suporte. O suporte da Microsoft pode revisar e atualizar políticas de acesso condicional que estão impedindo o acesso.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se você tiver políticas configuradas no portal clássico do Azure e no portal do Azure?  

Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se você tiver políticas no Portal do Intune Silverlight e no Portal do Azure?

Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver várias políticas configuradas para o mesmo usuário?  

Para cada entrada, o Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes que o acesso seja concedido ao usuário. Bloquear o acesso supera todas as outras definições de configuração. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>O acesso condicional funciona com o Exchange ActiveSync?

Sim, você pode usar o Exchange ActiveSync em uma política de acesso condicional.

Alguns aplicativos de nuvem, como o SharePoint Online e o Exchange Online, também oferecem suporte a protocolos de autenticação herdados. Quando um aplicativo cliente pode usar um protocolo de autenticação herdado para acessar um aplicativo de nuvem, o Azure AD não pode impor uma política de acesso condicional nessa tentativa de acesso. Para impedir que um aplicativo de cliente ignore a imposição de políticas, verifique se é possível habilitar a autenticação moderna apenas nos aplicativos de nuvem afetados.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Como você deve configurar o acesso condicional com os aplicativos do Office 365?

Como os aplicativos do Office 365 são interconectados, é recomendável atribuir aplicativos comumente usados ao criar políticas.

Aplicativos comuns interconectados incluem Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online e Office 365 Yammer.

É importante para as políticas que exigem interações com o usuário, como a autenticação multifator, quando o acesso é controlado no início de uma sessão ou tarefa. Caso contrário, os usuários não poderão concluir algumas tarefas em um aplicativo. Por exemplo, se você precisar de autenticação multifator em dispositivos não gerenciados para acessar o SharePoint, mas não para email, os usuários que trabalham em seu email não poderão anexar arquivos do SharePoint a uma mensagem. Mais informações podem ser encontradas no artigo [quais são as dependências de serviço em Azure Active Directory acesso condicional?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>O que você deve evitar

A estrutura de acesso condicional fornece uma excelente flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que é necessário examinar cuidadosamente cada política de configuração, antes de liberá-la, para evitar resultados indesejáveis. Nesse contexto, preste atenção especial às atribuições que afetam conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

Em seu ambiente, evite as configurações a seguir:

**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.
- **Exigir dispositivo compatível** - Para usuários que ainda não registraram seus dispositivos, esta política bloqueia todo acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política impedirá você voltar ao Portal do Azure para alterar a política.
- **Exigir ingresso no domínio** - Esta política de bloqueio de acesso também tem o potencial para bloquear o acesso de todos os usuários em sua organização se você ainda não tiver um dispositivo ingressado no domínio.
- **Exigir política de proteção de aplicativo** -este acesso ao bloqueio de política também tem o potencial de bloquear o acesso para todos os usuários em sua organização se você não tiver uma política do Intune. Se você for um administrador sem um aplicativo cliente que tenha uma política de proteção de aplicativo do Intune, essa política o bloqueará de voltar para portais como o Intune e o Azure.

**Para todos os usuários, todos os aplicativos de nuvem, todas as plataformas de dispositivo:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.

## <a name="how-should-you-deploy-a-new-policy"></a>Como você deve implantar uma nova política?

Como uma primeira etapa, é necessário avaliar a política, utilizando uma [ferramenta E-Se](what-if-tool.md).

Quando novas políticas estiverem prontas para o seu ambiente, implante-as em fases:

1. Aplique uma política a um conjunto de usuários pequeno e verifique se o comportamento é conforme esperado. 
1. Quando você expande uma política para incluir mais usuários. Continue excluindo todos os administradores da política para garantir que eles ainda tenham acesso e possam atualizar uma política se uma alteração for necessária.
1. Aplique uma política a todos os usuários somente se for necessário. 

Como melhor prática, crie uma conta de usuário que seja:

- Dedicado à administração de política 
- Excluído de todas as suas políticas

## <a name="policy-migration"></a>Migração de política

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.
- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   
- Você pode gerenciar todas as políticas de acesso condicional em um local central.
- O Portal Clássico do Azure foi desativado.   

Para obter mais informações, consulte [Migrar políticas clássicas no portal do Azure](policy-migration.md).

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber:

- Como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](../authentication/tutorial-enable-azure-mfa.md).
- Como planejar suas políticas de acesso condicional, consulte [como planejar sua implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md).