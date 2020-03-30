---
title: Use a caça livestream no Azure Sentinel para detectar ameaças| Microsoft Docs
description: Este artigo descreve como usar a caça livestream no Azure Sentinel para acompanhar os dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582119"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Use a caça ao vivo no Azure Sentinel para detectar ameaças

> [!IMPORTANT]
> Caça ao vivo no Azure Sentinel está atualmente em visualização pública e gradualmente rolando para os inquilinos.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Use o livestream de caça para criar sessões interativas que permitem testar consultas recém-criadas à medida que os eventos ocorrem, receba notificações das sessões quando uma correspondência for encontrada e inicie investigações, se necessário. Você pode criar rapidamente uma sessão de livestream usando qualquer consulta do Log Analytics.

- **Teste consultas recém-criadas à medida que os eventos ocorrem**
    
    Você pode testar e ajustar consultas sem conflitos com as regras atuais que estão sendo aplicadas ativamente aos eventos. Depois de confirmar que essas novas consultas funcionam como esperado, é fácil promovê-las a regras de alerta personalizadas, selecionando uma opção que eleva a sessão a um alerta.

- **Seja notificado quando as ameaças ocorrerem**
    
    Você pode comparar os feeds de dados de ameaças com dados de log agregados e ser notificado quando uma correspondência ocorrer. Os feeds de dados de ameaças são fluxos contínuos de dados relacionados a ameaças potenciais ou atuais, de modo que a notificação pode indicar uma ameaça potencial à sua organização. Crie uma sessão de livestream em vez de uma regra de alerta personalizada quando você quiser ser notificado de um problema em potencial sem as despesas gerais de manter uma regra de alerta personalizada.

- **Iniciar investigações**
    
    Se houver uma investigação ativa que envolva um ativo como um host ou usuário, você pode visualizar atividades específicas (ou qualquer) nos dados de log como ocorre nesse ativo. Você pode ser notificado quando essa atividade ocorrer.


## <a name="create-a-livestream-session"></a>Crie uma sessão de livestream

Você pode criar uma sessão de livestream a partir de uma consulta de caça existente ou criar sua sessão do zero.

1. No portal Azure, navegue até a Caça**ao Gerenciamento de** > Ameaças **Sentinelas** > .**Hunting**

2. Para criar uma sessão de livestream a partir de uma consulta de caça:
    
    1. Na guia **Consultas,** localize a consulta de caça a ser usada.
    2. Clique com o botão direito do mouse na consulta e **selecione Adicionar ao livestream**. Por exemplo: 
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream a partir da consulta de caça do Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Para criar uma sessão de livestream do zero: 
    
    1. Selecione a guia **Livestream**
    2. Selecione **Ir para livestream**.
    
4. No painel **livestream:**
    
    - Se você iniciou o livestream a partir de uma consulta, revise a consulta e faça quaisquer alterações que deseja fazer.
    - Se você começou o livestream do zero, crie sua consulta. 

5. Selecione **Reproduzir** na barra de comando.
    
    A barra de status a barra de comando indica se sua sessão livestream está sendo realizada ou pausada. No exemplo a seguir, a sessão está sendo executado:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream da caça Azure Sentinel](./media/livestream/livestream-session.png)

6. Selecione **Salvar** na barra de comando.
    
    A menos que você **selecione Pausa**, a sessão continua a ser executada até que você esteja fora do portal Azure.

## <a name="view-your-livestream-sessions"></a>Veja suas sessões ao vivo

1. No portal Azure, navegue até a guia **Sentinel** > **Threat Management** > **Hunting** > **Livestream.**

2. Selecione a sessão livestream que deseja exibir ou editar. Por exemplo: 
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream a partir da consulta de caça do Azure Sentinel](./media/livestream/livestream-tab.png)
    
    Sua sessão livestream selecionada é aberta para você tocar, pausar, editar e assim por diante.

## <a name="receive-notifications-when-new-events-occur"></a>Receba notificações quando novos eventos ocorrem

Como as notificações livestream para novos eventos usam notificações do portal Azure, você vê essas notificações sempre que usa o portal Azure. Por exemplo: 

![Notificação do portal Azure para livestream](./media/livestream/notification.png)

Selecione a notificação para abrir o painel **Livestream.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevar uma sessão de livestream a um alerta

Você pode promover uma sessão de livestream para um novo alerta selecionando **Elevate para alertar** da barra de comando na sessão de livestream relevante:

> [!div class="mx-imgBorder"]
> ![Elevar a sessão livestream a um alerta](./media/livestream/elevate-to-alert.png)

Essa ação abre o assistente de criação de regras, que está prepovoado com a consulta associada à sessão livestream.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar a caça ao vivo no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use notebooks para executar campanhas de caça automatizadas](notebooks.md)
