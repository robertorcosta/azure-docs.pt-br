---
title: Entrada/saída do serviço web
description: Conheça os módulos de serviço web no azure Machine Learning designer (visualização)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462081"
---
# <a name="web-service-inputoutput"></a>Entrada/saída do serviço web

Este artigo descreve o módulo **de entrada do Serviço Web** e o módulo de saída do Serviço **Web** no designer de machine learning do Azure (visualização).

**O** módulo de entrada do Serviço Web só pode se conectar à porta de entrada com o tipo **DataFrameDirectory**. E o módulo **de saída do Serviço Web** só pode ser conectado a partir da porta de saída com o tipo **DataFrameDirectory**. Os dois módulos podem ser encontrados na árvore do módulo, na categoria **Web Service.** 

**O** módulo de entrada do Serviço Web é usado para indicar onde os dados do usuário entram no pipeline e o módulo de saída do Serviço **Web** é usado para indicar onde os dados do usuário são retornados em um Pipeline de inferência em tempo real.

## <a name="how-to-use-web-service-inputoutput"></a>Como usar a entrada/saída do Web Service

- Quando você criar um pipeline de inferência em tempo real a partir do seu pipeline de treinamento, o módulo de entrada de **serviço web** e de saída de serviço **web** será automaticamente adicionado para mostrar onde os dados do usuário entram no pipeline e onde os dados são devolvidos. 

    Saiba mais sobre [a criação de um pipeline de inferência em tempo real](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Gerar automaticamente o pipeline de inferência em tempo real é um processo de melhor esforço baseado em regras, não há garantia para a correção. Você pode adicionar ou remover manualmente módulos **de entrada/saída do Serviço Web** para satisfazer seus requisitos. Certifique-se de que há pelo menos um módulo **de entrada de serviço web** e um módulo de saída de serviço **web** em seu pipeline de inferência em tempo real. Se você tiver vários módulos **de entrada** de serviço web ou saída de serviço **web,** certifique-se de que eles tenham nomes exclusivos, que você pode inserir o nome no painel direito do módulo.

- Você também pode criar manualmente um pipeline de inferência em tempo real adicionando módulos **de entrada** de serviço web e saída de serviço **web** ao seu pipeline não submetido.

    > [!NOTE]
    >  O tipo de tubulação será determinado na primeira vez que você enviá-lo. Portanto, certifique-se de adicionar o módulo de entrada de **serviço web** e a saída de serviço web antes **de** enviar pela primeira vez se quiser criar um pipeline de inferência em tempo real.

   O exemplo abaixo mostra como criar manualmente o pipeline de inferência em tempo real a partir do módulo **Execute Python Script.** 

   ![Exemplo](media/module/web-service-input-output-example.png)
   
   Depois de enviar o pipeline e a execução for concluída com sucesso, você poderá implantar o ponto final em tempo real.
   
   > [!NOTE]
   >  No exemplo acima, **Enter Data Manualmente** fornece o esquema de dados para entrada de serviço web e é necessário para implantar o ponto final em tempo real. Geralmente, você deve sempre conectar um módulo ou conjunto de dados à porta que a **Entrada do Serviço Web** está conectada para fornecer o esquema de dados.
   
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [como implantar o endpoint em tempo real](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.