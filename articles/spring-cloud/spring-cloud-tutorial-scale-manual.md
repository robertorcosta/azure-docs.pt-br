---
title: 'Tutorial: Dimensionar um aplicativo no Azure Spring Cloud | Microsoft Docs'
description: Neste tutorial, você aprenderá a dimensionar um aplicativo com o Azure Spring Cloud no portal do Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277470"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Dimensionar um aplicativo no Azure Spring Cloud

Este tutorial demonstra como dimensionar qualquer aplicativo de microsserviço usando o painel do Azure Spring Cloud no portal do Azure.

Escale e reduza verticalmente seu aplicativo modificando o número de vCPUs (CPUs virtuais) e a quantidade de memória. Reduza horizontalmente e expanda seu aplicativo modificando o número de instâncias do aplicativo.

Quando terminar, você saberá como fazer alterações manuais rápidas em cada aplicativo do serviço. O dimensionamento entra em vigor em segundos e não exige nenhuma alteração de código ou reimplantação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância de serviço do Azure Spring Cloud implantada.  Siga nosso [início rápido sobre como implantar um aplicativo por meio da CLI do Azure](spring-cloud-quickstart-launch-app-cli.md) para obter uma introdução.
* Pelo menos um aplicativo já criado na instância de serviço.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navegue até a página Dimensionar no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

1. Vá até a página **Visão geral** do Azure Spring Cloud.

1. Escolha o grupo de recursos que contém o serviço.

1. Selecione a guia **Aplicativos** em **Configurações** no menu do lado esquerdo da página.

1. Selecione o aplicativo que deseja dimensionar. Neste exemplo, selecione o aplicativo chamado **account-service**. Em seguida, você deverá ver a página **Visão Geral** do aplicativo.

1. Acesse a guia **Dimensionar** em **Configurações** no menu do lado esquerdo da página. Você deverá ver opções para os atributos de dimensionamento mostrados na seção a seguir.

## <a name="scale-your-application"></a>Dimensionar o aplicativo

Se você modificar os atributos de dimensionamento, lembre-se das seguintes observações:

* **CPUs**: o número máximo de CPUs por instância de aplicativo é quatro. O número total de CPUs para um aplicativo é o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Memória/GB**: a quantidade máxima de memória por instância de aplicativo é 8 GB. A quantidade total de memória para um aplicativo é o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Contagem de instâncias do aplicativo**: No nível Standard, é possível escalar horizontalmente para um máximo de 20 instâncias. Esse valor altera o número de instâncias em execução separadas do aplicativos de microsserviço.

Selecione **Salvar** para aplicar as configurações de dimensionamento.

![O serviço de escala no portal do Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Depois de alguns segundos, as alterações feitas são exibidas na página **Visão Geral**, com mais detalhes disponíveis na guia **Instâncias do aplicativo**. O dimensionamento não exige nenhuma alteração de código ou reimplantação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como dimensionar manualmente seus aplicativos do Azure Spring Cloud. Para saber como monitorar o aplicativo, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como monitorar um aplicativo](spring-cloud-tutorial-distributed-tracing.md)
