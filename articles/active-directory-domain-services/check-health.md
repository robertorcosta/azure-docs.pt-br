---
title: Verifique a saúde dos serviços de domínio do Diretório Ativo do Azure | Microsoft Docs
description: Saiba como verificar a saúde de um domínio gerenciado do Azure Active Directory Domain Services (Azure AD DS) e entenda as mensagens de status usando o portal Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655633"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Verifique a saúde de um domínio gerenciado do Azure Active Directory Domain Services

O Azure Active Directory Domain Services (Azure AD DS) executa algumas tarefas em segundo plano para manter o domínio gerenciado saudável e atualizado. Essas tarefas incluem fazer backups, aplicar atualizações de segurança e sincronizar dados do Azure AD. Se houver problemas com o domínio gerenciado pelo Azure AD DS, essas tarefas podem não ser concluídas com sucesso. Para revisar e resolver quaisquer problemas, você pode verificar o estado de saúde de um domínio gerenciado pelo Azure AD DS usando o portal Azure.

Este artigo mostra como visualizar o estado de saúde do Azure AD DS e entender as informações ou alertas mostrados.

## <a name="view-the-health-status"></a>Veja o estado de saúde

O status de saúde de um domínio gerenciado pelo Azure AD DS é visualizado usando o portal Azure. Informações sobre o último tempo de backup e sincronização com o Azure AD podem ser vistas, juntamente com quaisquer alertas que indiquem um problema com a saúde do domínio gerenciado. Para visualizar o estado de saúde de um domínio gerenciado pelo Azure AD DS, complete as seguintes etapas:

1. No portal Azure, procure e selecione **Azure AD Domain Services**.
1. Selecione o domínio gerenciado pelo Azure AD DS, como *aaddscontoso.com*.
1. No lado esquerdo da janela de recursos Azure AD DS, selecione **Saúde**. O exemplo a seguir mostra um domínio gerenciado pelo Azure AD DS saudável e o status do último backup e sincronização AD do Azure:

    ![Visão geral da página de saúde no portal Azure mostrando o status dos Serviços de Domínio do Diretório Ativo do Azure](./media/check-health/health-page.png)

O último carimbo de tempo *avaliado* da página de saúde mostra quando o domínio gerenciado pelo Azure AD DS foi verificado pela última vez. A saúde de um domínio gerenciado pelo Azure AD DS é avaliada a cada hora. Se você fizer qualquer alteração em um domínio gerenciado pelo Azure AD DS, aguarde até o próximo ciclo de avaliação para visualizar o estado de saúde atualizado.

O status no canto superior direito indica a saúde geral do domínio gerenciado pelo Azure AD DS. O status fatores todos os alertas existentes em seu domínio. A tabela a seguir detalha os indicadores de status disponíveis:

| Status | ícone | Explicação |
| --- | :----: | --- |
| Executando | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | O domínio gerenciado pelo Azure AD DS está funcionando corretamente e não tem nenhum alerta crítico ou de alerta. O domínio pode ter alertas informativos. |
| Precisa de atenção (aviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Não há alertas críticos no domínio gerenciado pelo Azure AD DS, mas há um ou mais alertas de alerta que devem ser endereçados. |
| Precisa de atenção (crítica) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Existem um ou mais alertas críticos no domínio gerenciado pelo Azure AD DS que devem ser endereçados. Você também pode ter alertas e/ou informativos. |
| Implantando | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | O domínio Azure AD DS está sendo implantado. |

## <a name="understand-monitors-and-alerts"></a>Entenda monitores e alertas

O estado de saúde de um domínio gerenciado pelo Azure AD DS mostra dois tipos de informações - *monitores*e *alertas*. Os monitores mostram o tempo em que as tarefas de fundo do núcleo foram concluídas. Os alertas fornecem informações ou sugestões para melhorar a estabilidade do domínio gerenciado.

### <a name="monitors"></a>Monitores

Monitores são áreas de um domínio gerenciado pelo Azure AD DS que são verificados regularmente. Se houver quaisquer alertas ativos para o domínio gerenciado pelo Azure AD DS, isso pode fazer com que um dos monitores reporte um problema. Atualmente, o Azure AD Domain Services possui monitores para as seguintes áreas:

* Backup
* Sincronização com o Azure AD

#### <a name="backup-monitor"></a>Monitor de backup

O monitor de backup verifica se os backups regulares automatizados do domínio gerenciado pelo Azure AD DS são executados com sucesso. A tabela a seguir detalha o status disponível do monitor de backup:

| Valor de detalhes | Explicação |
| --- | --- |
| Nunca fez backup | Este estado é normal para novos domínios gerenciados pelo Azure AD DS. O primeiro backup deve ser criado 24 horas após a implantação do domínio gerenciado pelo Azure AD DS. Se esse status persistir, [abra uma solicitação de suporte do Azure][azure-support]. |
| Último backup feito de 1 a 14 dias atrás | Este intervalo de tempo é o status esperado para o monitor de backup. Os backups regulares automatizados devem ocorrer neste período. |
| O último backup foi feito há mais de 14 dias. | Um tempo maior que duas semanas indica que há um problema com os backups regulares automatizados. Alertas críticos ativos podem impedir que o domínio gerenciado pelo Azure AD DS seja feito backup. Resolva quaisquer alertas ativos para o domínio gerenciado pelo Azure AD DS. Se o monitor de backup não atualizar o status para relatar um backup recente, [abra uma solicitação de suporte do Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronização com monitor Ad do Azure

Um domínio gerenciado pelo Azure AD DS sincroniza regularmente com o Azure Active Directory. O número de usuários e objetos de grupo e o número de alterações feitas no diretório Azure AD desde a última sincronização, afeta o tempo necessário para sincronizar. Se o domínio gerenciado pelo Azure AD DS foi sincronizado pela última vez há mais de três dias, verifique e resolva quaisquer alertas ativos. Se o monitor de sincronização não atualizar o status para mostrar uma sincronização recente após endereçar quaisquer alertas ativos, [abra uma solicitação de suporte do Azure][azure-support].

### <a name="alerts"></a>Alertas

Os alertas são gerados para problemas em um domínio gerenciado pelo Azure AD DS que precisam ser endereçados para que o serviço seja executado corretamente. Cada alerta explica o problema e dá uma URL que delineia etapas específicas para resolver o problema. Para obter mais informações sobre os possíveis alertas e suas resoluções, consulte [alertas de solução de problemas](troubleshoot-alerts.md).

Os alertas de estado de saúde são categorizados nos seguintes níveis de gravidade:

 * **Alertas críticos** são problemas que afetam severamente o domínio gerenciado pelo Azure AD DS. Esses alertas devem ser resolvidos imediatamente. A plataforma Azure não pode monitorar, gerenciar, corrigir e sincronizar o domínio gerenciado até que os problemas sejam resolvidos.
 * **Alertas de** alerta notificam-no de problemas que podem afetar as operações de domínio gerenciadas pelo Azure AD DS se o problema persistir. Esses alertas também oferecem recomendações para proteger o domínio gerenciado.
 * **Alertas informativos** são notificações que não afetam negativamente o domínio gerenciado pelo Azure AD DS. Os alertas informativos fornecem algumas informações sobre o que está acontecendo no domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os alertas mostrados na página de status de saúde, consulte [Resolver alertas sobre seu domínio gerenciado][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
