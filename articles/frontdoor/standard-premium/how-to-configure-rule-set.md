---
title: 'Azure front door: configurar conjunto de regras de porta frontal'
description: Este artigo fornece orientação sobre como configurar um conjunto de regras.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: e2fe475b171a99ec27ed162511db289891066e00
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098329"
---
# <a name="configure-a-rule-set"></a>Configurar um conjunto de regras

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Este tutorial mostra como criar um conjunto de regras e seu primeiro conjunto de regras no portal do Azure. 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Configure o conjunto de regras usando o Portal.
> - Excluir conjunto de regras do seu perfil de AFD usando o portal

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Antes de concluir as etapas deste tutorial, você deve primeiro criar um padrão do Azure front door/Premium. Para obter mais informações, consulte [início rápido: criar um perfil padrão/Premium da porta do Azure](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Configurar conjunto de regras no portal do Azure

1. Em seu perfil de porta frontal, selecione **conjunto de regras** localizado em **configurações**. Selecione **Adicionar** e dê a ele um nome de conjunto de regras.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Captura de tela da página de aterrissagem do conjunto de regras.":::
    
1. Selecione **Adicionar regra** para criar sua primeira regra. Dê a ele um nome de regra. Em seguida, selecione **Adicionar condição** ou **Adicionar ação** para definir sua regra. Você pode adicionar até 10 condições e 5 ações para uma regra. Neste exemplo, usamos a variável de servidor para adicionar um cabeçalho de resposta 8Geo-Country * para solicitações que incluem *contoso* na URL.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Captura de tela da página de configuração do conjunto de regras.":::
    
    > [!NOTE]
    > * Para excluir uma condição ou ação de uma regra, use a lixeira no lado direito da condição ou ação específica.
    > * Para criar uma regra que se aplica a todo o tráfego de entrada, não especifique nenhuma condição.
    > * Para parar de avaliar as regras restantes se uma regra específica for atendida, marque **parar de avaliar a regra restante**. Se essa opção estiver marcada e todas as regras restantes no conjunto de regras não serão executadas, independentemente se as condições de correspondência forem atendidas.  

1. Você pode determinar a prioridade das regras em seu conjunto de regras usando os botões de seta para mover as regras para maior ou menor em prioridade. A lista está em ordem crescente, portanto, a regra mais importante é listada primeiro.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Captura de tela da prioridade do conjunto de regras." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Depois de criar uma ou mais regras, selecione **salvar** para concluir a criação do conjunto de regras.

1. Agora associe o conjunto de regras a uma rota para que ele possa entrar em vigor. Você pode associar as regras definidas por meio da página conjunto de regras ou pode ir para o Gerenciador de pontos de extremidade para criar a associação.
 
    **Página conjunto de regras**: 
    
    1. Selecione o conjunto de regras a ser associado.
    
    1. Selecione o link não *associado* .
     

    1. Em seguida, na folha **associar uma rota** , selecione o ponto de extremidade e a rota que você deseja associar ao conjunto de regras. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Captura de tela da página criar uma rota.":::    
        
    1. Clique em *Avançar* para alterar as ordens do conjunto de regras se houver vários conjuntos de regras na rota selecionada. O conjunto de regras será executado de cima para baixo. Você pode alterar os pedidos selecionando o conjunto de regras e movê-lo para cima ou para baixo. Em seguida, selecione *associar*.
    
        > [!Note]
        > Você só pode associar um conjunto de regras a uma única rota nesta página. Para associar um conjunto de regras a várias rotas, use o Gerenciador de pontos de extremidade.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Captura de tela de pedidos de conjuntos de regras.":::
    
    1. O conjunto de regras agora está associado a uma rota. Você pode examinar o cabeçalho de resposta e ver se o país geográfico foi adicionado.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Captura de tela da regra associada a uma rota.":::

   **Gerenciador de pontos de extremidade**: 
    
    1. Vá para o Gerenciador de pontos de extremidade, selecione o ponto de extremidade que você deseja associar ao conjunto de regras.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Captura de tela da seleção de ponto de extremidade no Gerenciador de pontos de extremidade" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Clique em *Editar ponto de extremidade*  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Captura de tela de selecionar Editar ponto de extremidade no Gerenciador de pontos de extremidade." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Clique na rota. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Captura de tela de seleção de uma rota.":::
    
    1. Na folha *Atualizar roteiro* , em *regras*, selecione os conjuntos de regras que você deseja associar à rota da lista suspensa. Em seguida, você pode alterar os pedidos movendo a regra configurar para cima e para baixo. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Captura de tela da página atualizar uma rota.":::
    
    1. Em seguida, selecione *Atualizar* ou *Adicionar* para concluir a associação.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Excluir um conjunto de regras do seu perfil de porta de recepção do Azure

Nas etapas anteriores, você configurou e associou um conjunto de regras à sua rota. Se você não quiser mais o conjunto de regras associado à sua porta frontal, poderá remover o conjunto de regras concluindo as seguintes etapas:

1. Vá para a **página conjunto de regras** em **configurações** para desassociar o conjunto de regras de todas as rotas associadas.

1. Expanda para a rota, clique nos três pontos selecione *Editar a rota*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Captura de tela da rota expandida no conjunto de regras.":::

1. Vá para a seção regras na página rota, selecione o conjunto de regras e selecione o botão *excluir* . 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Captura de tela da página atualizar rota para excluir um conjunto de regras." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Selecione *Atualizar* e o conjunto de regras será desassociado da rota.

1. Repita as etapas de 2-5 para desassociar outras rotas que estão associadas a esse conjunto de regras até que o status de rotas seja exibido como não *associado*.

1. Para o conjunto de regras que não está *associado*, você pode excluir o conjunto de regras clicando nos três pontos à direita e selecionando *excluir*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Captura de tela de como excluir um conjunto de regras.":::

1. O conjunto de regras agora é excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

* Criar um conjunto de regras
* Associe um conjunto de regras à sua rota de AFD.
* Excluir um conjunto de regras do seu perfil de AFD

Para saber como adicionar cabeçalhos de segurança com o conjunto de regras, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Cabeçalhos de segurança com regras definidas]()
