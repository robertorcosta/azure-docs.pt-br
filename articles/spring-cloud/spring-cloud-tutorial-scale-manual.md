---
title: 'Tutorial: Dimensionar um aplicativo no Azure Spring Cloud | Microsoft Docs'
description: Neste tutorial, você aprenderá como dimensionar um aplicativo no Azure Spring Cloud no portal do Azure
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/06/2019
ms.openlocfilehash: bca88cac45e1ba8117eb4e10141e32d621434b86
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038628"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Tutorial: Dimensionar um aplicativo no Azure Spring Cloud

Este tutorial demonstra como dimensionar qualquer aplicativo de microsserviço usando o painel do Azure Spring Cloud no portal do Azure. Escale e reduza verticalmente seu aplicativo modificando o número de vCPUs (CPUs virtuais) e a quantidade de memória. Reduza horizontalmente e expanda modificando o número de instâncias do aplicativo. Quando terminar, você saberá como fazer ajustes manuais rápidos em cada aplicativo em seu serviço. O dimensionamento entra em vigor em segundos e não requer nenhuma alteração de código ou reimplantação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância de serviço do Azure Spring Cloud implantada.  Siga nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar a usar.
* Pelo menos um aplicativo já criado nessa instância de serviço.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navegue até a página Dimensionar no portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Navegue até a página **Visão geral** do Azure Spring Cloud.

1. Acesse a guia **Aplicativos** no título **Configurações** no menu do lado esquerdo.

1. Selecione o aplicativo que deseja dimensionar. Neste exemplo, dimensionaremos o aplicativo denominado “account-service”. Isso deve direcionar você para a página **Visão geral** do aplicativo.

1. Acesse a guia **Dimensionar** no título **Configurações** no menu do lado esquerdo. Você deve ver um formulário com linhas para cada um dos atributos de dimensionamento que mencionamos anteriormente.

## <a name="scale-your-application"></a>Dimensionar o aplicativo

Você pode modificar os atributos de dimensionamento. Tenha as seguintes observações em mente.

* **CPUs**: o número máximo de CPUs permitidas é quatro por instância de aplicativo. O número total de CPUs para um aplicativo será o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Memória/GB**: a quantidade máxima de memória permitida é 8 GB por instância de aplicativo.  A quantidade total de memória para um aplicativo será o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Contagem de instâncias**: você pode expandir até 20 instâncias na camada Standard. Esse valor altera o número de instâncias em execução separadas do aplicativos de microsserviço.

Clique no botão **Salvar** para aplicar suas configurações de dimensionamento.

Depois de alguns segundos, as alterações feitas serão exibidas na página **Visão geral**, com mais detalhes disponíveis na guia **Instâncias do aplicativo**. O dimensionamento não requer nenhuma alteração de código ou reimplantação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como dimensionar manualmente seus aplicativos do Azure Spring Cloud.  Para saber como monitorar o aplicativo, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como monitorar seu aplicativo](spring-cloud-tutorial-distributed-tracing.md)
