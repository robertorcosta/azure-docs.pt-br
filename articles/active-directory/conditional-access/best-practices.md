---
title: Melhores práticas para acesso condicional no Diretório Ativo do Azure | Microsoft Docs
description: Saiba mais sobre as coisas que você deve saber e o que você deve evitar fazer ao configurar políticas de Acesso Condicional.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295350"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Melhores práticas para acesso condicional no Diretório Ativo do Azure

Com [o Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos na nuvem. Este artigo fornece informações sobre:

- O que você deve saber 
- O que você deve evitar fazer ao configurar políticas de acesso condicional. 

Este artigo assume que você está familiarizado com os conceitos e a terminologia descrita no [What is Conditional Access in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma política funcionar?

Ao criar uma nova política, não há usuários, grupos, aplicativos ou controles de acesso selecionados.

![Aplicativos na nuvem](./media/best-practices/02.png)

Para que a política funcione, você deve configurar:

| O que           | Como                                  | Porque |
| :--            | :--                                  | :-- |
| **Aplicativos em nuvem** |Selecione um ou mais aplicativos.  | O objetivo de uma política de Acesso Condicional é permitir que você controle como os usuários autorizados podem acessar aplicativos na nuvem.|
| **Usuários e grupos** | Selecione pelo menos um usuário ou grupo autorizado para acessar os aplicativos na nuvem selecionados. | Uma política de acesso condicional que não tenha usuários e grupos atribuídos nunca é acionada. |
| **Controles de acesso** | Selecione pelo menos um controle de acesso. | Se as condições forem atendidas, o processador de política precisará saber o que fazer. |

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="how-are-conditional-access-policies-applied"></a>Como as políticas de acesso condicional são aplicadas?

Mais de uma política de acesso condicional pode ser aplicada quando você acessa um aplicativo em nuvem. Nesse caso, todas as políticas que se aplicam devem ser atendidas. Por exemplo, se uma política requer autenticação multifatorial (MFA) e outra requer um dispositivo compatível, você deve completar o MFA e usar um dispositivo compatível. 

Todas as políticas são impostas em duas fases:

- Fase 1: 
   - Coleta de detalhes: Reúna detalhes para identificar políticas que já estariam satisfeitas.
   - Durante esta fase, os usuários podem ver um aviso de certificado se a conformidade com o dispositivo faz parte de suas políticas de Acesso Condicional. Esse alerta pode ocorrer para aplicativos do navegador quando o sistema operacional do dispositivo não é o Windows 10.
   - A fase 1 da avaliação de políticas ocorre para todas as políticas e políticas habilitadas no [modo somente relatório](concept-conditional-access-report-only.md).
- Fase 2:
   - Execução: Levando em conta os detalhes coletados na fase 1, solicite ao usuário que satisfaça quaisquer requisitos adicionais que não tenham sido atendidos.
   - Aplique resultados na sessão. 
   - A fase 2 da avaliação da política ocorre para todas as políticas habilitadas.

### <a name="how-are-assignments-evaluated"></a>Como as atribuições são avaliadas?

Todas as atribuições são avaliadas com **AND** lógicos. Se você tiver mais de uma atribuição configurada, todas as atribuições deverão ser atendidas para disparar uma política.  

Se for necessário configurar uma condição de local que se aplique a todas as conexões feitas de fora da rede da organização:

- Incluindo **Todos os locais**
- Excluir **Todos os IPs confiáveis**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>O que fazer se você estiver bloqueado no portal de administração do Azure Active Directory?

Se você estiver bloqueado fora do portal Azure AD devido a uma configuração incorreta em uma diretiva de acesso condicional:

- Verifique se existem outros administradores em sua organização que ainda não estão bloqueados. Um administrador com acesso ao Portal do Azure pode desabilitar a política que está afetando sua entrada. 
- Se nenhum dos administradores da sua organização puder atualizar a política, será necessário enviar uma solicitação de suporte. O suporte da Microsoft pode rever e atualizar políticas de acesso condicional que estão impedindo o acesso.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se você tiver políticas configuradas no portal clássico do Azure e no portal do Azure?  

Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se você tiver políticas no Portal do Intune Silverlight e no Portal do Azure?

Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver várias políticas configuradas para o mesmo usuário?  

Para cada entrada, o Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes que o acesso seja concedido ao usuário. Bloquear o acesso supera todas as outras definições de configuração. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>O Acesso Condicional funciona com o Exchange ActiveSync?

Sim, você pode usar o Exchange ActiveSync em uma política de acesso condicional.

Alguns aplicativos em nuvem como O SharePoint Online e o Exchange Online também suportam protocolos de autenticação legados. Quando um aplicativo cliente pode usar um protocolo de autenticação legado para acessar um aplicativo em nuvem, o Azure AD não pode impor uma política de Acesso Condicional nesta tentativa de acesso. Para impedir que um aplicativo de cliente ignore a imposição de políticas, verifique se é possível habilitar a autenticação moderna apenas nos aplicativos de nuvem afetados.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Como você deve configurar o Acesso Condicional com aplicativos do Office 365?

Como os aplicativos do Office 365 estão interconectados, recomendamos atribuir aplicativos comumente usados em conjunto ao criar políticas.

Os aplicativos interconectados comuns incluem Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online e Office 365 Yammer.

É importante para políticas que requerem interações do usuário, como autenticação multifatorial, quando o acesso é controlado no início de uma sessão ou tarefa. Se você não fizer isso, os usuários não serão capazes de concluir algumas tarefas dentro de um aplicativo. Por exemplo, se você exigir autenticação multifatorial em dispositivos não gerenciados para acessar o SharePoint, mas não para e-mail, os usuários que trabalham em seu e-mail não poderão anexar arquivos SharePoint a uma mensagem. Mais informações podem ser encontradas no artigo, Quais são as [dependências do serviço no Azure Active Directory Conditional Access?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>O que você deve evitar

A estrutura de acesso condicional fornece uma grande flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que é necessário examinar cuidadosamente cada política de configuração, antes de liberá-la, para evitar resultados indesejáveis. Nesse contexto, preste atenção especial às atribuições que afetam conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

Em seu ambiente, evite as configurações a seguir:

**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.
- **Exigir dispositivo compatível** - Para usuários que ainda não registraram seus dispositivos, esta política bloqueia todo acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política impedirá você voltar ao Portal do Azure para alterar a política.
- **Exigir ingresso no domínio** - Esta política de bloqueio de acesso também tem o potencial para bloquear o acesso de todos os usuários em sua organização se você ainda não tiver um dispositivo ingressado no domínio.
- **Exigir política de proteção de aplicativos** - Esse acesso a bloqueio de políticas também tem o potencial de bloquear o acesso de todos os usuários da sua organização se você não tiver uma política Intune. Se você é um administrador sem um aplicativo cliente que tenha uma política de proteção de aplicativos Intune, essa política impede que você volte a entrar em portais como Intune e Azure.

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
- Você pode gerenciar todas as suas políticas de acesso condicional em um local central.
- O Portal Clássico do Azure foi desativado.   

Para obter mais informações, consulte [Migrar políticas clássicas no portal do Azure](policy-migration.md).

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber:

- Como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicionado ao diretório ativo do Azure](app-based-mfa.md).
- Como planejar suas políticas de acesso condicional, veja [como planejar sua implantação de Acesso Condicional no Azure Active Directory](plan-conditional-access.md).
