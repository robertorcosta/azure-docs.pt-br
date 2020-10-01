---
title: 'Tutorial: Criar política do WAF para Azure Front Door – portal do Azure'
description: Neste tutorial, você aprenderá a criar uma política de WAF (Firewall do Aplicativo Web) usando o portal do Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602210"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutorial: Criar uma política de Firewall do Aplicativo Web no Azure Front Door usando o portal do Azure

Este tutorial mostrará como criar uma política básica de WAF (Firewall de Aplicativo Web) do Azure e aplicá-la a um host de front-end no Azure Front Door.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associá-la a um host de front-end
> * Configurar regras de WAF

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Crie um perfil de Front Door](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de Firewall do Aplicativo Web

Primeiro, crie uma política básica de WAF com DRS (Conjunto de Regras Padrão) no portal. 

1. No canto superior esquerdo da tela, selecione **Criar um recurso**> pesquise **WAF**> selecione **Firewall do aplicativo Web (versão prévia)** > selecione **Criar**.
2. Na guia **Noções básicas** da página **Criar uma política de WAF**, insira ou selecione as seguintes informações, aceite os padrões das configurações restantes e selecione **Examinar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription            |Selecione o nome da assinatura do Front Door.|
    | Resource group          |Selecione o nome do grupo de recursos do Front Door.|
    | Nome de política             |Insira um nome exclusivo para a política de WAF.|

   ![Criar uma política de WAF](../media/waf-front-door-create-portal/basic.png)

3. Na guia **Associação** da página **Criar uma política de WAF**, selecione **Adicionar um host de front-end**, insira as configurações a seguir e clique em **Adicionar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Front Door              | Selecione o nome de perfil do Front Door.|
    | Host de front-end           | Selecione o nome do host do Front Door e clique em **Adicionar**.|
    
    > [!NOTE]
    > Se o host de front-end estiver associado a uma política de WAF, aparecerá esmaecido. Primeiro, é preciso remover o host de front-end da política associada. Em seguida, associá-lo novamente à nova política de WAF.
1. Selecione **Examinar + criar**e **Criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurar regras de Firewall do Aplicativo Web (opcional)

### <a name="change-mode"></a>Alterar o modo

Ao criar uma política de WAF, por padrão, ela fica em modo de **Detecção**. No modo de **Detecção**, o WAF não bloqueia as solicitações. Em vez disso, as solicitações que cumprem as regras de WAF são registradas em logs do WAF.
Para ver o WAF na prática, altere as configurações de modo de **Detecção** para **Prevenção**. No modo de **Prevenção**, as solicitações que cumprem as regras definidas no DRS (Conjunto de Regras Padrão) são bloqueadas e registradas em logs do WAF.

 ![Alterar o modo de política WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regras personalizadas

É possível criar uma regra personalizada selecionando **Adicionar regra personalizada** na seção **Regras personalizadas**. Desse modo, você iniciará a página de configuração de regras personalizadas. Veja abaixo um exemplo de como configurar uma regra personalizada para bloquear uma solicitação se a cadeia de caracteres de consulta contiver **blockme**.

![Regras personalizadas](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>DRS (Conjunto de Regras Padrão)

O Conjunto de Regras Padrão gerenciado pelo Azure é habilitado por padrão. Para desabilitar uma regra individual em um grupo de regras, expanda as regras nesse grupo, marque a **caixa de seleção** ao lado do número da regra e clique em **Desabilitar** na guia acima. Para alterar os tipos de ações para regras individuais no grupo de regras, marque a caixa de seleção ao lado do número da regra e clique em **Alterar ação** na guia acima.

 ![Alterar conjunto de regras de WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Remova o grupo de recursos e todos os recursos relacionados quando eles não forem mais necessários.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure Front Door](../../frontdoor/front-door-overview.md)