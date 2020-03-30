---
title: Visão geral do Azure Resource Health
description: Visão geral do Azure Resource Health
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159244"
---
# <a name="resource-health-overview"></a>Visão geral da Saúde dos Recursos
 
O Azure Resource Health ajuda você a diagnosticar e obter suporte para problemas de serviço que afetam seus recursos do Azure. Ele relata a saúde atual e passada de seus recursos.

[O status do Azure](https://status.azure.com) relata problemas de serviço que afetam um amplo conjunto de clientes do Azure. A Resource Health oferece um painel personalizado da saúde de seus recursos. Resource Health mostra todas as vezes que seus recursos estiveram indisponíveis por causa de problemas de serviço do Azure. Esses dados facilitam para você ver se um SLA foi violado.

## <a name="resource-definition-and-health-assessment"></a>Definição de recurso e avaliação de integridade

Um *recurso* é uma instância específica de um serviço Azure, como uma máquina virtual, um aplicativo web ou um banco de dados SQL. A Resource Health conta com sinais de diferentes serviços do Azure para avaliar se um recurso é saudável. Se um recurso não estiver íntegro, o Resource Health analisará informações adicionais para determinar a origem do problema. Ele também relata ações que a Microsoft está tomando para corrigir o problema e identifica coisas que você pode fazer para resolvê-lo.

Para obter mais informações sobre como a saúde é avaliada, consulte a lista de tipos de recursos e verificações de saúde no [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Status de integridade

A integridade de um recurso é exibida como um dos estados a seguir.

### <a name="available"></a>Disponível

*Disponível* significa que não há eventos detectados que afetem a saúde do recurso. Nos casos em que o recurso recuperado de tempo de inatividade não planejado durante as últimas 24 horas, você verá uma notificação "Recentemente resolvida".

![Status de *Disponível* para uma máquina virtual que tenha uma notificação "Recentemente resolvida"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível

*Indisponível* significa que o serviço detectou uma plataforma contínua ou evento não-plataforma que afeta a saúde do recurso.

#### <a name="platform-events"></a>Eventos de plataforma

Os eventos da plataforma são disparados por vários componentes da infraestrutura do Azure. Eles incluem tanto ações programadas (por exemplo, manutenção planejada) quanto incidentes inesperados (por exemplo, uma reinicialização de host não planejada ou hardware de host degradado que é previsto para falhar após uma janela de tempo especificada).

A Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite que você entre em contato com o Microsoft Support mesmo que você não tenha um contrato de suporte ativo.

![Status de *Indisponível* para uma máquina virtual por causa de um evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos de não plataforma

Eventos não-plataforma são desencadeados por ações do usuário. Exemplos incluem parar uma máquina virtual ou atingir o número máximo de conexões ao Azure Cache for Redis.

![Status de "Indisponível" para uma máquina virtual por causa de um evento não-plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown (desconhecido)

*Desconhecido* significa que a Resource Health não recebeu informações sobre o recurso por mais de 10 minutos. Embora este status não seja uma indicação definitiva do estado do recurso, é um importante ponto de dados para solução de problemas.

Se o recurso estiver em execução conforme o esperado, o status do recurso mudará para *Disponível* após alguns minutos.

Se você tiver problemas com o recurso, o estado de saúde *desconhecido* pode significar que um evento na plataforma está afetando o recurso.

![Status de *Desconhecido* para uma máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado

*Degradado* significa que seu recurso detectou uma perda de desempenho, embora ainda esteja disponível para uso.

Diferentes recursos têm seus próprios critérios para quando relatam que estão degradados.

![Status de *Degradado* para uma máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Relatar um status incorreto

Se você acha que o estado de saúde atual está incorreto, você pode nos dizer selecionando **Relatório estado de saúde incorreto**. Nos casos em que um problema do Azure está afetando você, nós encorajamos você a entrar em contato com o suporte da Resource Health.

![Formulário para enviar informações sobre um status incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informações sobre histórico

Você pode acessar até 30 dias de história na seção de história da **Saúde** da Saúde de Recursos.

![Lista de eventos do Resource Health nas últimas duas semanas](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Introdução

Abrir Resource Health para um recurso:

1. Entre no portal do Azure.
2. Procure o recurso.
3. No menu de recursos no painel esquerdo, selecione **Resource Health**.

![Abrir o Resource Health a partir da exibição de recursos](./media/resource-health-overview/from-resource-blade.png)

Também é possível acessar o Resource Health, selecionando **Todos os serviços** e digitando **resource health** na caixa de texto de filtro. No painel **Ajuda + suporte**, selecione [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abrir o Resource Health a partir de "Todos os serviços"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Próximas etapas

Confira essas referências para saber mais sobre a Saúde dos Recursos:
-  [Tipos de recursos e verificações de saúde no Azure Resource Health](resource-health-checks-resource-types.md)
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)
