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
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521462"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início Rápido: Crie um Front Door para um aplicativo Web global altamente disponível

Este início rápido descreve como criar um perfil do Front Door que fornece alta disponibilidade e alto desempenho para seu aplicativo Web global. 

O cenário descrito neste início rápido inclui duas instâncias de um aplicativo Web em execução em diferentes regiões do Azure. Uma configuração do Front Door baseada em [back-ends ponderados e de mesma prioridade](front-door-routing-methods.md) iguais é criada que ajuda o tráfego do usuário direto ao conjunto mais próximo de back-ends de site executando o aplicativo. O Front Door monitora o aplicativo Web continuamente e oferece failover automático para o próximo back-end disponível quando o site mais próximo não está disponível.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure 
Entre no Portal do Azure em https://portal.azure.com.

## <a name="prerequisites"></a>Pré-requisitos
-Este início rápido requer que você implante duas instâncias de um aplicativo Web em execução em diferentes regiões do Azure (*Leste dos EUA* e *Europa Ocidental*). Ambas as instâncias do aplicativo Web são executadas no modo ativo/ativo, ou seja, qualquer uma delas pode usar o tráfego a qualquer momento, diferentemente de uma configuração ativo/stand-by em que um funciona como um failover.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Web** > **Aplicativo Web**.
2. Em **Aplicativo Web**, insira ou selecione as informações abaixo e insira as configurações padrão quando nenhuma for especificada:

     | Configuração         | Valor     |
     | ---              | ---  |
     | Resource group          | Selecione **Novo** e, em seguida, digite *myResourceGroupFD1* |
     | Nome           | Insira um nome exclusivo para o aplicativo Web  |
     | Pilha de runtime          | Selecionar uma pilha de runtime para o seu aplicativo |
     |      Região  |   Oeste dos EUA        |
     | Plano do Serviço de Aplicativo/Localização         | Selecione **Novo**.  No Plano do Serviço de Aplicativo, insira *myAppServicePlanEastUS*e selecione **OK**.| 
     |SKU e tamanho  | Selecione **Alterar Tamanho**, **Total de ACU 100 Standard S1, 1,75 GB de memória** |
     
3. Selecione **Examinar + criar**.
4. Examine as informações de resumo do aplicativo Web. Selecione **Criar**.
5. Após cerca de 5 minutos, um site padrão será criado quando o aplicativo Web for implantado com êxito.
6. Repita as etapas 1 a 3 para criar um segundo site em uma região do Azure diferente com as seguintes configurações:

     | Configuração         | Valor     |
     | ---              | ---  |
     | Resource group          | Selecione **Novo** e, em seguida, digite *myResourceGroupFD2* |
     | Nome           | Insira um nome exclusivo para o aplicativo Web  |
     | Pilha de runtime          | Selecionar uma pilha de runtime para o seu aplicativo |
     |      Região  |   Europa Ocidental      |
     | Plano do Serviço de Aplicativo/Localização         | Selecione **Novo**.  No Plano do Serviço de Aplicativo, insira *myAppServicePlanWestEurope*e selecione **OK**.|   
     |SKU e tamanho  | Selecione **Alterar Tamanho**, **Total de ACU 100 Standard S1, 1,75 GB de memória** |
    
## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para seu aplicativo
### <a name="a-add-a-frontend-host-for-front-door"></a>a. Adicionar um host de front-end para o Front Door
Crie uma configuração do Front Door que direciona o tráfego do usuário com base na latência mais baixa entre dois back-ends.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Front Door**.
2. Em **Criar um Front Door**, insira ou selecione as seguintes informações e insira as configurações padrão quando nenhuma for especificada:

     | Configuração         | Valor     |
     | ---              | ---  |
     |Subscription  | Selecione a assinatura na qual deseja criar o Front Door.|
     | Resource group          | Selecione **Novo** e, em seguida, digite *myResourceGroupFD0* |
     | Localização do grupo de recursos  |   Centro dos EUA        |
     
     > [!NOTE]
     > Você não precisa criar um grupo de recursos para implantar o Front Door.  Se você também pode escolher um grupo de recursos existente.
     
3. Clique em **Avançar: configuração**.
4. Clique no ícone '+' na placa Front-ends/domínios.  Em **Nome do host**, insira `<Your Initials>frontend`. Esse nome do host precisa ser global exclusivo, mas o Front Door cuidará dessa validação.
5. Clique em **Adicionar**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Adicionar back-end do aplicativo e pools de back-end

Em seguida, você precisará configurar os back-ends/domínios e os back-ends do aplicativo em um pool de back-end para que o Front Door saiba o local em que o aplicativo reside. 

1. Clique no ícone '+' na placa de pools de back-end para adicionar um pool de back-end. No **Nome** do pool de back-end, insira `myBackendPool`.
2. Em seguida, clique em **Adicionar um back-end** para adicionar os sites criados anteriormente.
3. Selecione **Tipo de host de back-end** como 'Serviço de Aplicativo ', a assinatura na qual você criou o site e, em seguida, escolha o primeiro site na lista suspensa **Nome do host de back-end**.
4. Deixe os campos restantes como estão por enquanto e clique em **Adicionar**.
5. Selecione **Tipo de host de back-end** como 'Serviço de Aplicativo ', a assinatura na qual você criou o site e, em seguida, escolha o **segundo** site na lista suspensa **Nome do host de back-end**.
6. Deixe os campos restantes como estão por enquanto e clique em **Adicionar**.
7. É possível optar por atualizar as configurações de Investigações de integridade e de Balanceamento de carga para o pool de back-end, mas os valores padrão também devem funcionar. Em ambos os casos, clique em **Adicionar**.


### <a name="c-add-a-routing-rule"></a>C. Adicionar uma regra de roteamento
1. Por fim, clique no ícone "+" da placa de Regras de roteamento para configurar uma regra de roteamento. Isso é necessário para mapear seu host de front-end para o pool de back-end, que basicamente está configurando se uma solicitação vai para o `myappfrontend.azurefd.net` e, em seguida, encaminha-a para o pool de back-end `myBackendPool`. 
2. Em **Nome**, insira 'LocationRule'.
3. Clique em **Adicionar** para adicionar a regra de roteamento para o Front Door. 
4. Clique em **Examinar e Criar**.
5. Examine as configurações para a criação do Front Door. Clique em **Criar**

>[!WARNING]
> É **necessário** garantir que cada um dos hosts de front-end no seu Front Door tem uma regra de roteamento com um caminho padrão ("/\*") associado a ela. Ou seja, entre todas as suas regras de roteamento, deve haver pelo menos uma regra de roteamento para cada um dos seus hosts de front-end definida no caminho padrão ('/\*'). Não fazer isso poderá fazer o tráfego do seu usuário final não ser roteado corretamente.

## <a name="view-front-door-in-action"></a>Exibir Front Door em ação
Depois de criar um Front Door, levará alguns minutos para a configuração ser implantada globalmente em todos os lugares. Após a conclusão, acesse o host de front-end criado, ou seja, vá para um navegador da Web e pressione a URL `myappfrontend.azurefd.net`. Sua solicitação será roteada automaticamente para o back-end mais próximo de você dos back-ends especificados no pool de back-end. 

### <a name="view-front-door-handle-application-failover"></a>Exibir o failover do aplicativo de identificador do Front Door
Se desejar testar o failover global instantâneo do Front Door em ação, será possível acessar um dos sites criados e interrompê-lo. Com base na configuração de Investigação de integridade definida para o pool de back-end, faremos o failover instantaneamente do tráfego para a implantação do outro site. Também é possível testar o comportamento desabilitando o back-end na configuração do pool de back-end para o Front Door. 

## <a name="clean-up-resources"></a>Limpar os recursos
Quando os grupos de recursos **myResourceGroupFD1**, **myResourceGroupFD2** e **myResourceGroupFD0** não forem mais necessários, exclua-os:

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um Front Door que permite que você direcione o tráfego do usuário para aplicativos Web que requerem alta disponibilidade e o máximo de desempenho. Para saber mais sobre o tráfego de roteamento, leia os [Métodos de roteamento](front-door-routing-methods.md) usados pelo Front Door.
