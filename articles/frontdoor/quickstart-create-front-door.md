---
title: 'Início Rápido: Criar o perfil do Front Door para alta disponibilidade de aplicativos'
description: Este artigo de início rápido descreve como criar um Front Door para seu aplicativo Web global altamente disponível e de alto desempenho.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 67a4f9eb3290ba09a2c19325464cf7ad224856e7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184509"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início Rápido: Crie um Front Door para um aplicativo Web global altamente disponível

Este início rápido descreve como criar um perfil do Front Door que fornece alta disponibilidade e alto desempenho para seu aplicativo Web global. 

O cenário descrito neste início rápido inclui duas instâncias de um aplicativo Web em execução em diferentes regiões do Azure. Uma configuração do Front Door baseada em [back-ends ponderados e de mesma prioridade](front-door-routing-methods.md) iguais é criada que ajuda o tráfego do usuário direto ao conjunto mais próximo de back-ends de site executando o aplicativo. O Front Door monitora o aplicativo Web continuamente e oferece failover automático para o próximo back-end disponível quando o site mais próximo não está disponível.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure 
Entre no Portal do Azure em https://portal.azure.com.

## <a name="prerequisites"></a>Pré-requisitos
-Este início rápido requer que você implante duas instâncias de um aplicativo Web em execução em diferentes regiões do Azure (*Leste dos EUA* e *Europa Ocidental*). Ambas as instâncias do aplicativo Web são executadas no modo ativo/ativo, ou seja, qualquer uma delas pode usar o tráfego a qualquer momento, diferentemente de uma configuração ativo/stand-by em que um funciona como um failover.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Web** > **Aplicativo Web** > **Criar**.
2. Em **Aplicativo Web**, insira ou selecione as informações abaixo e insira as configurações padrão quando nenhuma for especificada:

     | Configuração         | Valor     |
     | ---              | ---  |
     | NOME           | Insira um nome exclusivo para o aplicativo Web  |
     | Resource group          | Selecione **Novo** e, em seguida, digite *myResourceGroupFD1* |
     | Plano do Serviço de Aplicativo/Localização         | Selecione **Novo**.  No Plano do Serviço de Aplicativo, insira *myAppServicePlanEastUS*e selecione **OK**. 
     |      Location  |   Leste dos EUA        |
    |||

3. Selecione **Criar**.
4. Um site da Web padrão é criado quando o aplicativo Web é implantado com êxito.
5. Repita as etapas 1 a 3 para criar um segundo site em uma região do Azure diferente com as seguintes configurações:

     | Configuração         | Valor     |
     | ---              | ---  |
     | NOME           | Insira um nome exclusivo para o aplicativo Web  |
     | Resource group          | Selecione **Novo** e, em seguida, digite *myResourceGroupFD2* |
     | Plano do Serviço de Aplicativo/Localização         | Selecione **Novo**.  No Plano do Serviço de Aplicativo, insira *myAppServicePlanWestEurope*e selecione **OK**. 
     |      Location  |   Europa Ocidental      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para seu aplicativo
### <a name="a-add-a-frontend-host-for-front-door"></a>a. Adicionar um host de front-end para o Front Door
Crie uma configuração do Front Door que direciona o tráfego do usuário com base na latência mais baixa entre dois back-ends.

1. No lado superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Front Door** > **Criar**.
2. Em **Criar um Front Door**, comece adicionando as informações básicas e ofereça uma assinatura na qual você deseja que o Front Door seja configurado. Da mesma forma, como acontece com qualquer outro recurso do Azure, também será necessário fornecer um ResourceGroup e uma região do Grupo de Recursos se você estiver criando um novo. Por fim, é necessário oferecer um nome para o Front Door.
3. Após o preenchimento das informações básicas, a primeira etapa que precisa ser definida é o **host de front-end** para a configuração. O resultado deve ser um nome de domínio válido como `myappfrontend.azurefd.net`. Esse nome do host deve ser globalmente único, mas o Front Door se encarregará dessa validação. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Adicionar back-end do aplicativo e pools de back-end

Em seguida, será necessário configurar os back-ends do aplicativo em um pool de back-end para que o Front Door saiba onde seu aplicativo reside. 

1. Clique no ícone "+" para adicionar um pool de back-end e, em seguida, especifique um **nome** para seu pool de back-end, digamos `myBackendPool`.
2. Em seguida, clique em Adicionar back-ends para adicionar seus sites criados anteriormente.
3. Selecione **Tipo de host de destino** como "Serviço de Aplicativo", selecione a assinatura na qual você criou o site e, em seguida, escolha o primeiro site no **Nome do host de destino**, ou seja, *myAppServicePlanEastUS.azurewebsites.net*.
4. Deixe os campos restantes como estão por enquanto e clique em **Adicionar**.
5. Repita as etapas 2 a 4 para adicionar o outro site, ou seja, *myAppServicePlanWestEurope.azurewebsites.net*
6. É possível optar por atualizar as configurações de Investigações de integridade e de Balanceamento de carga para o pool de back-end, mas os valores padrão também devem funcionar. Clique em **Adicionar**.


### <a name="c-add-a-routing-rule"></a>C. Adicionar uma regra de roteamento
Por fim, clique no ícone "+" nas regras de roteamento para configurar uma regra de roteamento. Isso é necessário para mapear seu host de front-end para o pool de back-end, que basicamente está configurando se uma solicitação vai para o `myappfrontend.azurefd.net` e, em seguida, encaminha-a para o pool de back-end `myBackendPool`. Clique em **Adicionar** para adicionar a regra de roteamento para o Front Door. Agora você deve estar pronto para criar o Front Door; portanto, clique em **Examinar e criar**.

>[!WARNING]
> É **necessário** garantir que cada um dos hosts de front-end no seu Front Door tem uma regra de roteamento com um caminho padrão ("/\*") associado a ela. Ou seja, entre todas as suas regras de roteamento, deve haver pelo menos uma regra de roteamento para cada um dos seus hosts de front-end definida no caminho padrão ('/\*'). Não fazer isso poderá fazer o tráfego do seu usuário final não ser roteado corretamente.

## <a name="view-front-door-in-action"></a>Exibir Front Door em ação
Depois de criar um Front Door, levará alguns minutos para a configuração ser implantada globalmente em todos os lugares. Após a conclusão, acesse o host de front-end criado, ou seja, vá para um navegador da Web e pressione a URL `myappfrontend.azurefd.net`. Sua solicitação será roteada automaticamente para o back-end mais próximo de você dos back-ends especificados no pool de back-end. 

### <a name="view-front-door-handle-application-failover"></a>Exibir o failover do aplicativo de identificador do Front Door
Se desejar testar o failover global instantâneo do Front Door em ação, será possível acessar um dos sites criados e interrompê-lo. Com base na configuração de Investigação de integridade definida para o pool de back-end, faremos o failover instantaneamente do tráfego para a implantação do outro site. Também é possível testar o comportamento desabilitando o back-end na configuração do pool de back-end para o Front Door. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua os grupos de recursos, os aplicativos Web e todos os recursos relacionados.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um Front Door que permite que você direcione o tráfego do usuário para aplicativos Web que requerem alta disponibilidade e o máximo de desempenho. Para saber mais sobre o tráfego de roteamento, leia os [Métodos de roteamento](front-door-routing-methods.md) usados pelo Front Door.