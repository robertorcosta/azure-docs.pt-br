---
title: Conecte os dados do evento de segurança do Windows ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados de eventos de segurança do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124976"
---
# <a name="connect-windows-security-events"></a>Conectar eventos de segurança do Windows 

O conector De Eventos de Segurança permite transmitir todos os eventos de segurança de seus sistemas Windows (servidores e estações de trabalho, físicos e virtuais) para o seu espaço de trabalho do Azure Sentinel. Isso permite que você visualize eventos de segurança do Windows em seus dashboards, use-os na criação de alertas personalizados e confie neles para melhorar suas investigações, dando-lhe mais informações sobre a rede da sua organização e expandindo suas operações de segurança Capacidades. Você pode selecionar quais eventos transmitir entre os seguintes conjuntos:<a name="event-sets"></a>

- **Todos os eventos** - Todos os eventos de segurança do Windows e AppLocker.
- **Comum** - Um conjunto padrão de eventos para fins de auditoria. Uma trilha completa de auditoria do usuário está incluída neste conjunto. Por exemplo, ele contém tanto o login do usuário quanto os eventos de login do usuário (IDs de evento 4624, 4634). Há também ações de auditoria, como mudanças de grupo de segurança, operações do controlador de domínio chave Kerberos e outros tipos de eventos em consonância com as práticas recomendadas aceitas.

    O conjunto de eventos **Comuns** pode conter alguns tipos de eventos que não são tão comuns.  Isso porque o principal ponto do conjunto **Comum** é reduzir o volume de eventos para um nível mais gerenciável, mantendo ainda a capacidade completa de trilha de auditoria.

- **Mínimo** - Um pequeno conjunto de eventos que podem indicar ameaças potenciais. Este conjunto não contém uma trilha de auditoria completa. Abrange apenas eventos que podem indicar uma violação bem sucedida, e outros eventos importantes que têm taxas de ocorrência muito baixas. Por exemplo, ele contém logons de usuário bem-sucedidos e com falha (IDs de evento 4624, 4625), mas não contém informações de saída de saída (4634) que, embora importantes para auditoria, não são significativas para detecção de violação e tem volume relativamente alto. A maior parte do volume de dados deste conjunto é composta por eventos de login e eventos de criação de processos (ID de evento 4688).

- **Nenhum** - Sem segurança ou eventos do AppLocker. (Esta configuração é usada para desativar o conector.)

    A lista a seguir fornece uma descrição completa dos IDs de eventos Security e App Locker para cada conjunto:

    | Conjunto de eventos | IDs de eventos coletados |
    | --- | --- |
    | **Mínimo** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Comum** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> A coleção de eventos de segurança dentro do contexto de um único espaço de trabalho pode ser configurada a partir do Azure Security Center ou do Azure Sentinel, mas não de ambos. Se você está embarcando no Azure Sentinel em um espaço de trabalho que já está executando o Azure Security Center e está configurado para coletar eventos de segurança, você tem duas opções:
> - Deixe a coleção de Eventos de Segurança no Azure Security Center como está. Você poderá consultar e analisar esses eventos no Azure Sentinel, bem como no Azure Security Center. No entanto, você não será capaz de monitorar o status de conectividade do conector ou alterar sua configuração no Azure Sentinel. Se isso é importante para você, considere a segunda opção.
>
> - [Desativar a coleção de eventos de segurança](../security-center/security-center-enable-data-collection.md) no Azure Security Center e, só então, adicionar o conector de Eventos de Segurança no Azure Sentinel. Assim como na primeira opção, você poderá consultar e analisar eventos tanto no Azure Sentinel quanto no Azure Security Center, mas agora poderá monitorar o status de conectividade do conector ou alterar sua configuração em - e somente no - Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configure o conector de eventos de segurança do Windows

Para coletar seus eventos de segurança do Windows no Azure Sentinel:

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**. Na lista de conectores, clique em Eventos de **Segurança**e, em seguida, no botão **de página de conector Aberto** no canto inferior direito. Em seguida, siga as instruções na tela na guia **Instruções,** conforme descrito no resto desta seção.

1. Verifique se você tem as permissões apropriadas conforme descrito nos **Pré-requisitos**.

1. Baixe e instale o [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (também conhecido como Microsoft Monitoring Agent ou MMA) nas máquinas para as quais você deseja transmitir eventos de segurança para o Azure Sentinel.

    Para máquinas virtuais azure:
    
    1. Clique em **Instalar agente na Máquina Virtual do Azure Windows**e, em seguida, no link que aparece abaixo.
    1. Para cada máquina virtual que você deseja conectar, clique em seu nome na lista que aparece à direita e clique em **Conectar**.

    Para máquinas Windows não-Azure (físicas, virtuais ou virtuais em outra nuvem):

    1. Clique em **Instalar o agente na máquina windows não-Azure**e, em seguida, no link que aparece abaixo.
    1. Clique nos links de download apropriados que aparecem à direita, em **Computadores Windows**.
    1. Usando o arquivo executável baixado, instale o agente nos sistemas Windows de sua escolha e configure-o usando o **ID e chaves** do Workspace que aparecem abaixo dos links de download mencionados acima.

    > [!NOTE]
    >
    > Para permitir que os sistemas Windows sem a conectividade de internet necessária para ainda transmitir eventos para o Azure Sentinel, baixe e instale o **GATEWAY OMS** em uma máquina separada, usando o link no canto inferior direito, para atuar como um proxy.  Você ainda precisará instalar o agente Log Analytics em cada sistema Windows cujos eventos você deseja coletar.
    >
    > Para obter mais informações sobre este cenário, consulte a documentação do [ **gateway Log Analytics** ](../azure-monitor/platform/gateway.md).

    Para obter opções adicionais de instalação e mais detalhes, consulte a documentação do [ **agente Log Analytics** ](../azure-monitor/platform/agent-windows.md).

1. Selecione qual conjunto de eventos[(All, Common ou Minimal)](#event-sets)você deseja transmitir.

1. Clique em **Atualizar**.

1. Para usar o esquema relevante no Log Analytics `SecurityEvent` para eventos de segurança do Windows, digite na janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar cerca de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)

