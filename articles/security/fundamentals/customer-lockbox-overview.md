---
title: Cofre de clientes para Microsoft Azure
description: Visão geral técnica do Customer Lockbox para o Microsoft Azure, que fornece controle sobre o acesso ao provedor de nuvem quando a Microsoft pode precisar acessar os dados do cliente.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561962"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Cofre de clientes para Microsoft Azure

> [!NOTE]
> Para usar esse recurso, sua organização deve ter [um plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Desenvolvedor**.

O Customer Lockbox for Microsoft Azure fornece uma interface para que os clientes revisem e aprovem ou rejeitem solicitações de acesso a dados do cliente. Ele é usado em casos em que um engenheiro da Microsoft precisa acessar dados de clientes durante uma solicitação de suporte.

Este artigo abrange como as solicitações do Customer Lockbox são iniciadas, rastreadas e armazenadas para avaliações e auditorias posteriores.

O Customer Lockbox agora está disponível e atualmente habilitado para acesso remoto à área de trabalho a máquinas virtuais.

## <a name="workflow"></a>Fluxo de trabalho

As etapas a seguir descrevem um fluxo de trabalho típico para uma solicitação do Customer Lockbox.

1. Alguém em uma organização tem um problema com sua carga de trabalho Azure.

2. Depois que essa pessoa resolve o problema, mas não consegue corrigi-lo, eles abrem um bilhete de suporte do [portal Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). O bilhete é atribuído a um engenheiro de suporte ao cliente do Azure.

3. Um engenheiro de suporte do Azure analisa a solicitação de serviço e determina os próximos passos para resolver o problema.

4. Se o engenheiro de suporte não puder solucionar problemas usando ferramentas padrão e telemetria, o próximo passo é solicitar permissões elevadas usando um serviço de acesso Just-In-Time (JIT). Esta solicitação pode ser do engenheiro de suporte original. Ou, pode ser de um engenheiro diferente porque o problema é escalonado para a equipe Azure DevOps.

5. Depois que a solicitação de acesso é enviada pelo Engenheiro Azure, o serviço Just-In-Time avalia a solicitação levando em conta fatores como:
    - O escopo do recurso
    - Se o solicitante é uma identidade isolada ou usando autenticação multifatorial
    - Níveis de permissões

    Com base na regra JIT, essa solicitação também pode incluir uma aprovação dos Aprovadores Internos da Microsoft. Por exemplo, o aprovador pode ser o lead de suporte ao cliente ou o Gerente de DevOps.

6. Quando a solicitação requer acesso direto aos dados do cliente, uma solicitação do Customer Lockbox é iniciada. Por exemplo, acesso remoto à área de trabalho à máquina virtual de um cliente.

    A solicitação está agora em um estado **notificado pelo cliente,** aguardando a aprovação do cliente antes de conceder acesso.

7. Na organização do cliente, o usuário que tem a [função Proprietário](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) para a assinatura do Azure recebe um e-mail da Microsoft, para notificá-lo sobre a solicitação de acesso pendente. Para solicitações do Customer Lockbox, essa pessoa é a aprovadora designada.

    Email de exemplo:

    ![Azure Customer Lockbox - notificação por e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. A notificação por e-mail fornece um link para a lâmina **do Caixa de Clientes** no portal Azure. Usando este link, o aprovador designado entra no portal do Azure para visualizar quaisquer solicitações pendentes que sua organização tenha para o Customer Lockbox:

    ![Azure Customer Lockbox - landing page](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   A solicitação permanece na fila do cliente por quatro dias. Após esse período, a solicitação de acesso expira automaticamente e nenhum acesso é concedido aos engenheiros da Microsoft.

9. Para obter os detalhes da solicitação pendente, o aprovador designado pode selecionar a solicitação de lockbox de **Solicitações Pendentes**:

    ![Azure Customer Lockbox - veja a solicitação pendente](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. O aprovador designado também pode selecionar o **ID de solicitação de serviço** para visualizar a solicitação de ticket de suporte criada pelo usuário original. Essas informações fornecem contexto para o motivo pelo qual o Suporte microsoft está engajado e o histórico do problema relatado. Por exemplo: 

    ![Azure Customer Lockbox - veja a solicitação do bilhete de suporte](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Após a revisão da solicitação, o aprovador designado seleciona **Aprovar** ou **Negar**:

    ![Caixa de bloqueio do cliente do Azure - selecione Aprovar ou Negar](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Como resultado da seleção:
    - **Aprovação**: O acesso é concedido ao engenheiro da Microsoft. O acesso é concedido por um período padrão de oito horas.
    - **Negar**: O pedido de acesso elevado pelo engenheiro da Microsoft é rejeitado e nenhuma ação adicional é tomada.

Para fins de auditoria, as ações tomadas neste fluxo de trabalho são registradas nos [registros de solicitação do Customer Lockbox](#auditing-logs).

## <a name="auditing-logs"></a>Logs de auditoria

Os registros do Customer Lockbox são armazenados em registros de atividades. No portal Azure, selecione **Registros de atividades** para visualizar informações de auditoria relacionadas às solicitações do Customer Lockbox. Você pode filtrar para ações específicas, tais como:
- **Negar solicitação de lockbox**
- **Criar solicitação de caixa de bloqueio**
- **Aprovar solicitação de lockbox**
- **Expiração da solicitação de lockbox**

Por exemplo:

![Azure Customer Lockbox - registros de atividades](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Serviços e cenários suportados em disponibilidade geral

Os seguintes serviços e cenários estão atualmente em disponibilidade geral para o Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Acesso remoto de desktop a máquinas virtuais

O Customer Lockbox está atualmente habilitado para solicitações remotas de acesso à área de trabalho para máquinas virtuais. As seguintes cargas de trabalho são suportadas:
- Plataforma como serviço (PaaS) - Azure Cloud Services (função web e função do trabalhador)
- Infra-estrutura como serviço (IaaS) - Windows e Linux (somente o Azure Resource Manager)
- Conjunto de escala de máquina virtual - Windows e Linux

> [!NOTE]
> As instâncias IaaS Classic não são suportadas pelo Customer Lockbox. Se você tiver cargas de trabalho em execução em instâncias IaaS Classic, recomendamos que você as migre dos modelos de implantação classic para resource manager. Para obter instruções, consulte [a migração suportada pela plataforma de recursos IaaS do classic para o Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Registros de auditoria detalhados

Para cenários que envolvem acesso remoto à área de trabalho, você pode usar registros de eventos do Windows para rever as ações tomadas pelo engenheiro da Microsoft. Considere usar o Azure Security Center para coletar seus registros de eventos e copiar os dados para o seu espaço de trabalho para análise. Para obter mais informações, consulte [coleta de dados no Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Serviços e cenários suportados na visualização

Os seguintes serviços estão agora em pré-visualização para o Customer Lockbox:

- Armazenamento do Azure

- BD SQL do Azure

- Azure Data Explorer

- Máquinas virtuais (agora também cobrindo acesso a depósitos de memória e discos gerenciados)

- Transferências por assinatura do Azure

Para habilitar o Customer Lockbox para essas ofertas de pré-visualização para sua organização, inscreva-se no [Customer Lockbox para Visualização Pública do Azure](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Exclusões

As solicitações do Customer Lockbox não são acionadas nos seguintes cenários de suporte de engenharia:

- Um engenheiro da Microsoft precisa fazer uma atividade que fique fora dos procedimentos operacionais padrão. Por exemplo, recuperar ou restaurar serviços em cenários inesperados ou imprevisíveis.

- Um engenheiro da Microsoft acessa a plataforma Azure como parte da solução de problemas e, inadvertidamente, tem acesso aos dados dos clientes. Por exemplo, a Equipe de Rede do Azure executa a solução de problemas que resulta em uma captura de pacote em um dispositivo de rede. No entanto, se o cliente criptografou os dados enquanto estava em trânsito, o engenheiro não poderá ler os dados.

## <a name="next-steps"></a>Próximas etapas

O Customer Lockbox está disponível automaticamente para todos os clientes que possuem um [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Desenvolvedor**.

Quando você tem um plano de suporte elegível, nenhuma ação é necessária por você para ativar o Customer Lockbox. As solicitações do Customer Lockbox são iniciadas por um engenheiro da Microsoft se essa ação for necessária para progredir um bilhete de suporte que é arquivado de alguém da sua organização.
