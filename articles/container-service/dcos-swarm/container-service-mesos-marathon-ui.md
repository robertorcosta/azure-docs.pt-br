---
title: (PRETERIDO) Gerenciar cluster de DC/OS do Azure com interface do usuário do Marathon
description: Implante contêineres para um serviço de cluster do Serviço de Contêiner do Azure usando a interface do usuário da Web do Marathon.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b251096915506c3c7a4eebf45b6a03e24779a3d8
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277795"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(PRETERIDO) Gerenciar um cluster de DC/OS do Serviço de Contêiner do Azure por meio da interface do usuário da Web do Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS fornece um ambiente de implantação e dimensionamento de cargas de trabalho clusterizadas e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o DC/OS, há uma estrutura que gerencia o agendamento e a execução das cargas de trabalho de computação.

Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento descreve como você começar a implantar contêineres com o Marathon. 


## <a name="prerequisites"></a>Pré-requisitos
Antes de trabalhar nos exemplos, você precisará de um cluster DC/OS configurado no Serviço de Contêiner do Azure. Você também precisa ter conectividade remota com esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir:

* [Implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md)
* [Conectar a um cluster do Serviço de Contêiner do Azure](../container-service-connect.md)

> [!NOTE]
> Este artigo pressupõe que você está realizando acesso por túnel ao cluster DC/OS por meio de sua porta local 80.
>

## <a name="explore-the-dcos-ui"></a>Explorar a interface do usuário do DC/OS
Com um túnel Secure Shell (SSH) [estabelecido](../container-service-connect.md), navegue até http:\//localhost/. Isso carrega a interface do usuário da Web do DC/OS e mostrará informações sobre o cluster, como recursos usados, agentes ativos e serviços em execução.

![Interface do usuário do DC/OS](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorar a interface do usuário do Marathon
Para ver a interface do usuário do amMarathon, navegue até http:\//localhost/Marathon. Nessa tela, você pode iniciar um novo contêiner ou outro aplicativo no cluster DC/OS do Serviço de Contêiner do Azure. Você também pode ver informações sobre a execução de aplicativos e de contêineres.  

![Interface do usuário do Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Implantar um contêiner formatado pelo Docker
Para implantar um novo contêiner usando o Marathon, clique em **Criar Aplicativo** e insira as informações a seguir nas guias do formulário:

| Campo | Valor |
| --- | --- |
| ID |nginx |
| Memória | 32 |
| Imagem |nginx |
| Rede |Com ponte |
| Porta de host |80 |
| Protocolo |TCP |

![Interface do usuário do novo aplicativo – geral](./media/container-service-mesos-marathon-ui/dcos4.png)

![Interface do usuário do novo aplicativo – contêiner do Docker](./media/container-service-mesos-marathon-ui/dcos5.png)

![Interface do usuário do novo aplicativo - descoberta de serviço e portas](./media/container-service-mesos-marathon-ui/dcos6.png)

Se você deseja mapear estaticamente a porta do contêiner para uma porta no agente, precisa usar o Modo JSON. Para fazer isso, alterne o assistente de Novo Aplicativo para **Modo JSON** usando o botão de alternância. Em seguida, insira a configuração a seguir na seção `portMappings` da definição de aplicativo. Este exemplo associa a porta 80 do contêiner à porta 80 do agente DC/OS. Depois de fazer essa alteração, você pode remover o assistente do Modo JSON.

```none
"hostPort": 80,
```

![Interface do usuário do novo aplicativo – exemplo de porta 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Se você quiser habilitar as verificações de integridade, defina um caminho na guia **Verificações de Integridade**.

![Verificações de integridade da interface do usuário do novo aplicativo](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

O cluster DC/OS é implantado com um conjunto de agentes privados e públicos. Para que o cluster possa acessar aplicativos da Internet, você precisa implantar os aplicativos em um agente público. Para fazer isso, selecione a guia **Opcional** do assistente de Novo Aplicativo e insira **slave_public** nas **Funções de Recurso Aceitas**.

Em seguida, clique em **Criar Aplicativo**.

![Interface do usuário do novo aplicativo - configuração do agente público](./media/container-service-mesos-marathon-ui/dcos14.png)

Novamente na página principal do Marathon, você poderá ver o status da implantação para o contêiner. Inicialmente, você vê um status de **Implantando**. Depois de uma implantação bem-sucedida, o status é alterado para **Executando**.

![Interface do usuário da página principal do Marathon – status da implantação do contêiner](./media/container-service-mesos-marathon-ui/dcos7.png)

Ao alternar de volta para a interface do usuário da Web do DC/so (http:\//localhost/), você verá que uma tarefa (nesse caso, um contêiner formatado pelo Docker) está em execução no cluster DC/OS.

![Interface do usuário Web DC/OS – tarefa em execução no cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

Para ver o nó de cluster no qual a tarefa está em execução, clique na guia **Nós**.

![Interface do usuário Web DC/OS - nó do cluster de tarefa](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Alcançar o contêiner

Neste exemplo, o aplicativo está em execução em um nó de agente público. Você alcança o aplicativo da Internet, navegando até o agente FQDN do cluster: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, em que:

* **PREFIXODEDNS** é o prefixo de DNS que você forneceu ao implantar o cluster.
* **REGIÃO** é a região em que o grupo de recursos está localizado.

    ![Nginx da Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Próximos passos
* [Trabalhar com DC/SO e a API do Marathon](container-service-mesos-marathon-rest.md)

* Aprofunde seus conhecimentos sobre o Serviço de Contêiner do Azure com o Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
