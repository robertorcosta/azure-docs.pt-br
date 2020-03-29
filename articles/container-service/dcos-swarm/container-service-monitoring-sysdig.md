---
title: (PRETERIDO) Monitorar um cluster do Serviço de Contêiner do Azure com Sysdig
description: Monitore um cluster do Serviço de Contêiner do Azure com Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a22d48554573e2517b318f6172b759864bf46612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277727"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(PRETERIDO) Monitorar um cluster do Serviço de Contêiner do Azure com Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, implantaremos agentes de Sysdig para todos os nós de agente em seu cluster do Serviço de Contêiner do Azure. Você precisa de uma conta com Sysdig para essa configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implantar](container-service-deployment.md) e [conectar](../container-service-connect.md) um cluster configurado pelo Serviço de Contêiner do Azure. Explorar a [interface do usuário do Marathon](container-service-mesos-marathon-ui.md). Vá [https://app.sysdigcloud.com](https://app.sysdigcloud.com) para configurar uma conta na nuvem do Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig é um serviço de monitoramento que permite monitorar os contêineres no cluster. Sysdig ajuda a solucionar problemas, mas também tem métricas de monitoramentos básicas para CPU, rede, memória e E/S. Com Sysdig, é mais fácil ver quais contêineres estão trabalhando mais ou, essencialmente, usando mais memória e CPU. Esse modo de exibição está na seção "Visão geral", que está atualmente na versão beta. 

![Interface do usuário de Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurar uma implantação de Sysdig com Marathon
Estas etapas mostram como configurar e implantar aplicativos Sysdig no cluster com Marathon. 

Acesse sua UI DC/OS via [http://localhost:80/](http://localhost:80/) Once in the DC/OS UI navegar até o "Universo", que fica no canto inferior esquerdo e, em seguida, procurar por "Sysdig".

![Sysdig no universo de DC/OS](./media/container-service-monitoring-sysdig/sysdig1.png)

Agora, para concluir a configuração, é necessária uma conta de nuvem de Sysdig ou uma conta de avaliação gratuita. Quando estiver conectado ao site de nuvem de Sysdig, clique em seu nome de usuário e, na página, você deverá ver a "Chave de acesso". 

![Chave de API d Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Em seguida, digite sua Chave de Acesso para a configuração de Sysdig no universo de DC/OS. 

![Configuração de Sysdig no Universo de DC/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Agora defina as instâncias como 10000000 para que sempre que um novo nó for adicionado ao cluster, Sysdig implante automaticamente um agente para o novo nó. Essa é uma solução temporária para garantir que Sysdig implante todos os novos agentes no cluster. 

![Configuração de Sysdig nas instâncias de Universo de DC/OS](./media/container-service-monitoring-sysdig/sysdig4.png)

Após instalar o pacote, navegue até a IU de Sysdig e você poderá explorar as diferentes métricas de uso para os contêineres no cluster. 

Você também pode instalar painéis específicos do Mesos e do Marathon por meio do [assistente de novo painel](https://app.sysdigcloud.com/#/dashboards/new).
