---
title: Entrada/saída do serviço Web
description: Saiba mais sobre os módulos de serviço Web no designer de Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462081"
---
# <a name="web-service-inputoutput"></a>Entrada/saída do serviço Web

Este artigo descreve o módulo **entrada do serviço Web** e o módulo **saída do serviço Web** no designer de Azure Machine Learning (versão prévia).

O módulo **entrada do serviço Web** só pode se conectar à porta de entrada com o tipo **DataFrameDirectory**. E o módulo **saída do serviço Web** só pode ser conectado da porta de saída com o tipo **DataFrameDirectory**. Os dois módulos podem ser encontrados na árvore de módulo, em categoria de **serviço Web** . 

O módulo **entrada do serviço Web** é usado para indicar onde os dados do usuário entram no pipeline e o módulo **saída do serviço Web** é usado para indicar onde os dados do usuário são retornados em um pipeline de inferência em tempo real.

## <a name="how-to-use-web-service-inputoutput"></a>Como usar entrada/saída do serviço Web

- Quando você cria um pipeline de inferência em tempo real de seu pipeline de treinamento, o módulo **entrada de serviço Web** e **saída do serviço Web** será adicionado automaticamente para mostrar onde os dados do usuário entram no pipeline e onde os dados são retornados. 

    Saiba mais sobre como [criar um pipeline de inferência em tempo real](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Gerar automaticamente o pipeline de inferência em tempo real é um processo de melhor esforço baseado em regras, não há nenhuma garantia para a exatidão. Você pode adicionar ou remover manualmente módulos de **entrada/saída do serviço Web** para atender às suas necessidades. Verifique se há pelo menos um módulo de **entrada do serviço Web** e um módulo **saída do serviço Web** em seu pipeline de inferência em tempo real. Se você tiver vários módulos de **entrada de serviço Web** ou de **saída de serviço Web** , verifique se eles têm nomes exclusivos, que você pode inserir o nome no painel direito do módulo.

- Você também pode criar manualmente um pipeline de inferência em tempo real adicionando módulos de entrada do serviço **Web** e de **saída do serviço Web** ao seu pipeline não enviado.

    > [!NOTE]
    >  O tipo de pipeline será determinado na primeira vez que você o enviar. Portanto, certifique-se de adicionar o módulo **entrada de serviço Web** e **saída de serviço Web** antes de enviar pela primeira vez se você quiser criar um pipeline de inferência em tempo real.

   O exemplo abaixo mostra como criar manualmente o pipeline de inferência em tempo real do módulo **Executar script Python** . 

   ![Exemplo](media/module/web-service-input-output-example.png)
   
   Depois que você enviar o pipeline e a execução for concluída com êxito, você poderá implantar o ponto de extremidade em tempo real.
   
   > [!NOTE]
   >  No exemplo acima, **inserir dados manualmente** fornece o esquema de dados para entrada do serviço Web e é necessário para implantar o ponto de extremidade em tempo real. Em geral, você sempre deve conectar um módulo ou conjunto de dados à porta que a **entrada do serviço Web** está conectada para fornecer o esquema de dados.
   
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [implantar o ponto de extremidade em tempo real](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.