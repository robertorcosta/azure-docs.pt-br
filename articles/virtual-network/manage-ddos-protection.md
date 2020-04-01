---
title: Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure
titlesuffix: Azure Virtual Network
description: Saiba como usar a telemetria da Proteção contra DDoS do Azure Standard no Azure Monitor para mitigar um ataque.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 95a70a1d0c4367adb1dd276bff1b1eb20caafc59
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473371"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure

Saiba como habilitar e desabilitar a proteção contra DDoS (ataque de negação de serviço distribuído) e usar a telemetria para atenuar ataques contra DDoS com a Proteção contra DDoS Standard do Azure. A Proteção contra DDoS Standard protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicativo que têm um [endereço IP público](virtual-network-public-ip-address.md) do Azure atribuído a eles. Para saber mais sobre a Proteção contra DDoS Standard e suas funcionalidades, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md).

Antes de concluir qualquer etapa neste tutorial, faça logon no portal do Azure em https://portal.azure.com com uma conta atribuída à função [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que as ações adequadas atribuídas estão listadas em [Permissões](#permissions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano. O plano de proteção contra DDoS também está associado a uma assinatura, que você seleciona durante a criação do plano. O Plano de Proteção DDoS funciona em todas as regiões e assinaturas. Exemplo - você pode criar o plano na região Leste-EUA e vincular a #1 de assinatura em seu inquilino. O mesmo plano pode ser vinculado a redes virtuais de outras assinaturas em diferentes regiões, através do seu inquilino. A assinatura do plano está associada a gerar a fatura mensal recorrente para o plano, bem como encargos excedentes, caso o número de endereços IP públicos protegidos exceder 100. Para obter mais informações sobre os preços de DDoS, consulte [Detalhes do preço](https://azure.microsoft.com/pricing/details/ddos-protection/).

A criação de mais de um plano não é necessária para a maioria das organizações. O plano não pode ser movido entre as assinaturas. Se você quiser alterar a assinatura de um plano, você precisa [excluir o plano existente](#work-with-ddos-protection-plans) e criar um novo.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Pesquise por *DDoS*. Selecione **Plano de proteção contra DDoS** quando aparecer nos resultados da pesquisa.
3. Selecione **Criar**.
4. Insira ou selecione seus próprios valores, ou insira ou selecione os seguintes valores de exemplo e, em seguida, selecione **Criar**:

    |Configuração        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | myDdosProtectionPlan                              |
    |Subscription   | Selecione sua assinatura.                         |
    |Resource group | Selecione **Criar novo** e insira *myResourceGroup* |
    |Location       | Leste dos EUA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Habilitar DDoS para uma nova rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira ou selecione seus próprios valores, ou insira ou selecione os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Criar**:

    | Configuração         | Valor                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Subscription    | Selecione sua assinatura.                                    |
    | Resource group  | Selecione **Usar o existente**e, em seguida, selecione **myResourceGroup** |
    | Location        | Leste dos EUA                                                      |
    | Proteção contra DDoS | Selecione **Padrão** e, em seguida, em **Proteção contra DDoS**, selecione **myDdosProtectionPlan**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.|

Você não pode mover uma rede virtual para outro grupo de recursos ou assinatura quando o DDoS padrão está habilitado para a rede virtual. Se você precisar mover uma rede virtual com um padrão de DDoS habilitado, desabilite o padrão de DDoS primeiro, mova a rede virtual e, em seguida, habilite padrão de DDoS. Após a movimentação, os limites da política ajustados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Habilitar DDoS para uma rede virtual existente

1. Crie um plano de proteção contra DDoS completando as etapas em [Criar um plano de proteção contra DDoS](#create-a-ddos-protection-plan), se você não tiver um plano de proteção contra DDoS existente.
2. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
3. Insira o nome da rede virtual para a qual você deseja habilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
4. Selecione **Proteção contra DDoS**, em **Configurações**.
5. Selecione **Padrão**. Em **Plano de proteção contra DDoS**, selecione um plano de proteção contra DDoS existente ou o plano que você criou na etapa 1 e, em seguida, selecione **Salvar**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.

**Comandos** 
- Azure CLI: [az network ddos-protection create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Desabilitar DDoS para uma rede virtual

1. Insira o nome da rede virtual a qual você deseja desabilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
2. Selecione **Proteção contra DDoS**, em **Configurações**.
3. Selecione **Básico** em **Plano de proteção contra DDoS** e, em seguida, selecione **Salvar**.

**Comandos** 
- Azure CLI: [az rede ddos-protection delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com Planos de proteção contra DDoS

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *DDoS* na caixa **Filtro**. Selecione **Plano de proteção contra DDoS** quando aparecer nos resultados.
3. Selecione o plano de proteção que você deseja exibir na lista.
4. Todas as redes virtuais associadas ao plano são listadas.
5. Se você quiser excluir um plano, primeiro você deve desassociar todas as redes virtuais dele. Para desassociar um plano de uma rede virtual, consulte [Desabilitar DDoS para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurar alertas para métricas de proteção contra DDoS

Selecione uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque usando a configuração de alerta do Azure Monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta:

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **Métricas** em **Serviços Compartilhados**.
4. Insira ou selecione seus próprios valores, ou insira os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Ok**:

    |Configuração                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | myDdosAlert                                                                                        |
    |Subscription             | Selecione a assinatura que contém o endereço IP público para o qual você deseja receber alertas.        |
    |Resource group           | Selecione o grupo de recursos que contém o endereço IP público para o qual você deseja receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o qual você deseja receber alertas. DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio de Gerenciador de Recursos (não clássico) listado em [Rede Virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), exceto para os Ambientes de Serviço de Aplicativo do Azure e o Gateway de VPN do Azure. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Métrica                   | Sob ataque DDoS ou não                                                                            |
    |Limite                | 1 - **1** significa que você está sob ataque. **0** significa que você não está sob ataque.                         |
    |Período                   | Selecione o valor que você escolher.                                                                   |
    |Notificar por email         | Marque a caixa de seleção                                                                                  |
    |Administrador adicional | Insira seu endereço de email se você não é um proprietário de email, colaborador ou leitor para a assinatura. |

    Em poucos minutos de detecção de ataque, você receberá um email de métricas de Azure Monitor que é semelhante a figura a seguir:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque de DDoS para validar seu alerta, consulte [Validar detecção de DDoS](#validate-ddos-detection).

Você também pode aprender mais sobre [configuração de webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criação de alertas.

## <a name="use-ddos-protection-telemetry"></a>Usar telemetria da proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não vê a telemetria antes nem depois de um ataque ser mitigado.

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **Métricas,** em **SERVIÇOS COMPARTILHADOS**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público para o qual você deseja a telemetria.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja a telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo da tela. Quando selecionadas, essas métricas são mostradas no **Gráfico de Métricas do Azure Monitor** na tela de visão geral.
7. Selecione o tipo **de agregação** como **Max**

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marcação em cada métrica, da seguinte maneira:

- **Nome de marcação removido** (por exemplo, **Pacotes de Entrada Removidos por DDoS**): o número de pacotes removidos pelo sistema de proteção contra DDoS.
- **Nome de marcação encaminhado** (por exemplo, **Pacotes de Entrada Encaminhados por DDoS**): o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca** (por exemplo: **Pacotes de Entrada de DDoS**): o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

Para simular um ataque de DDoS para validar a telemetria, consulte [Validar detecção de DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Exibir políticas de mitigação de DDoS

A Proteção contra DDoS padrão aplica TCP SYN, TCP e UDP (três políticas de mitigação ajustadas automaticamente) para cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode visualizar os limiares de diretiva selecionando os **pacotes TCP de entrada para ativar a mitigação DDoS** e **pacotes UDP de entrada para ativar métricas de mitigação DDoS** com o tipo **de agregação** como 'Max', como mostrado na imagem a seguir:

![Exibir políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limites da política são configurados automaticamente por meio da nossa criação de perfil de tráfego de rede baseada em aprendizado de máquina do Azure. Somente quando o limite da política for excedido, ocorre a mitigação de DDoS para o endereço IP sob ataque.

## <a name="configure-ddos-attack-analytics"></a>Configurar a análise de ataque de DDoS
O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configurar relatórios de mitigação de ataque de DDoS
Relatórios de mitigação de ataque usam os dados do protocolo Netflow agregados para fornecer informações detalhadas sobre o ataque em seu recurso. Sempre que um recurso IP público estiver sob ataque, a geração de relatórios começará assim que a atenuação for iniciada. Também será gerado um relatório de incremental a cada 5 minutos e um relatório de pós-atenuação para todo o período de mitigação. Isso é para garantir que, no caso de o ataque de DDoS continuar por mais tempo, você poderá exibir o instantâneo mais recentes do relatório de atenuação a cada 5 minutos e um resumo completo quando a mitigação de ataque estiver concluída. 

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações**, selecione **Configurações de diagnóstico**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationReports** e, em seguida, selecione tantas opções a seguir quanto você precisar:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [Arquivar os logs de diagnóstico](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [Transmitir os logs de diagnóstico para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para o Log Analytics**: Grava logs no serviço Azure Monitor. Para saber mais sobre essa opção, consulte [Coletar logs para uso nos registros do Monitor do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Os relatórios de mitigação incrementais e pós-ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas de tráfego
- Motivo para pacotes ignorados
- Protocolos envolvidos
- Principais 10 regiões ou países de origem
- Principais 10 ASNs de origem

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurar logs de fluxo de mitigação de ataque de DDoS
Os Logs de Fluxo de Mitigação de Ataque permitem examinar o tráfego ignorado, o tráfego encaminhado e outros datapoints interessante durante um ataque de DDoS ativo quase em tempo real. Você pode ingerir o fluxo constante desses dados em seus sistemas SIEM por meio do hub de eventos para o monitoramento quase em tempo real, realizar ações possíveis e atender à necessidade de suas operações de defesa. 

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações**, selecione **Configurações de diagnóstico**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationFlowLogs** e, em seguida, selecione tantas opções a seguir quanto você precisar:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [Arquivar os logs de diagnóstico](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [Transmitir os logs de diagnóstico para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para o Log Analytics**: Grava logs no serviço Azure Monitor. Para saber mais sobre essa opção, consulte [Coletar logs para uso nos registros do Monitor do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Para exibir os dados de logs de fluxo no painel de análise do Azure, você pode importar o painel de exemplo do https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Os logs de fluxo terão os seguintes campos: 
- IP de origem
- IP de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Ação executada durante a mitigação

A análise de ataques só funcionará se o DDoS Protection Standard estiver habilitado na rede virtual do endereço IP público. 

## <a name="validate-ddos-detection"></a>Validar detecção de DDoS

A Microsoft firmou parceria com [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que você pode gerar tráfego contra endereços IP públicos com a Proteção contra DDoS habilitada para fins de simulação. A simulação do BreakPoint Cloud permitirá que você:

- Valide como a Proteção contra DDoS do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS
- Otimize o processo de resposta a incidentes durante ataques de DDoS
- Documente a conformidade de DDoS
- Treine suas equipes de segurança de rede

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Exibir alertas de proteção DDoS no Azure Security Center

O Azure Security Center fornece uma lista de alertas de [segurança,](/azure/security-center/security-center-managing-and-responding-alerts)com informações para ajudar a investigar e corrigir problemas. Com esse recurso, você tem uma visão unificada dos alertas, incluindo alertas relacionados ao ataque DDoS e as ações tomadas para mitigar o ataque em pouco tempo.
Existem dois alertas específicos que você verá para qualquer detecção e mitigação de ataque DDoS:

- **Ataque DDoS detectado para IP público**: Esse alerta é gerado quando o serviço de proteção DDoS detecta que um de seus endereços IP públicos é alvo de um ataque DDoS.
- **Ataque DDoS mitigado para IP público**: Este alerta é gerado quando um ataque ao endereço IP público foi mitigado.
Para ver os alertas, abra o **Security Center** no portal Azure. Em **Proteção contra Ameaças,** selecione **alertas de segurança**. A captura de tela a seguir mostra um exemplo dos alertas de ataque DDoS.

![Alerta DDoS no Centro de Segurança Do Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geográfica e de ameaças e etapas de remediação.

## <a name="permissions"></a>Permissões

Para trabalhar com os planos de proteção DDoS, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Ler um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Excluir um plano de proteção contra DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Participar de um plano de proteção DDoS              |

Para habilitar a proteção DDoS a uma rede virtual, sua conta deve também ser atribuída a ações [apropriadas para redes virtuais](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Próximas etapas

- Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais