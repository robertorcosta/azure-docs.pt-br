---
title: (PRETERIDO) Gerenciar cluster de DC/OS do Azure com a API REST Marathon
description: Implante contêineres em cluster DC/OS do Serviço de Contêiner do Azure usando a API REST do Marathon.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277778"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(PRETERIDO) Gerenciamento de contêiner de DC/sistema operacional por meio da API REST do Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS fornece um ambiente de implantação e dimensionamento de cargas de trabalho clusterizadas e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o DC/OS, há uma estrutura que gerencia o agendamento e a execução das cargas de trabalho de computação. Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento lhe ajuda a começar a criar e dimensionar implantações de contêiner usando a API REST do Marathon. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de trabalhar nos exemplos, você precisará de um cluster DC/OS configurado no Serviço de Contêiner do Azure. Você também precisa ter conectividade remota com esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir:

* [Como implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md)
* [Conexão a um cluster do Serviço de Contêiner do Azure](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Acessar as APIs de DC/sistema operacional
Depois de conectado ao cluster Azure Container Service, você pode acessar as APIs DC/OS e REST relacionadas através de http:\//localhost:local-port. Os exemplos neste documento pressupõem que você crie um túnel na porta 80. Por exemplo, os pontos finais da Maratona podem\/ser alcançados em URIs começando com http: /localhost/marathon/v2/. 

Para saber mais sobre as várias APIs, confira a documentação da Mesosphere para a [API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e a [API Chronos](https://mesos.github.io/chronos/docs/api.html) e a documentação do Apache para a [API do Agendador do Mesos](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Coletar informações do DC/OS e do Marathon
Antes de implantar contêineres no cluster DC/OS, colete algumas informações sobre esse cluster, como os nomes e o status dos agentes DC/OS. Para fazer isso, confira o ponto de extremidade `master/slaves` da API REST DC/OS. Se tudo correr bem, a consulta retornará uma lista de agentes DC/OS e várias propriedades para cada um deles.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, use o ponto de extremidade `/apps` do Marathon para verificar as implantações atuais do Marathon para o cluster DC/OS. Se esse for um cluster novo, você verá uma matriz vazia para os aplicativos.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implantar um contêiner formatado pelo Docker
Você implanta os contêineres formatados pelo Docker por meio da API REST do Marathon usando um arquivo JSON que descreve a implantação pretendida. O exemplo a seguir implanta um contêiner Nginx para um agente privado no cluster. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implantar um contêiner Docker formatado, armazene o arquivo JSON em um local acessível. Em seguida, para implantar o contêiner, execute o comando a seguir. Especifique o nome do arquivo JSON (neste exemplo, `marathon.json`).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

A saída deverá ser semelhante a esta:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se você consultar o Marathon em busca de aplicativos, esse novo aplicativo aparecerá na saída.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Alcançar o contêiner

Você pode verificar se o Nginx está em execução em um contêiner em um dos agentes privados no cluster. Para localizar o host e a porta em que o contêiner está sendo executado, consulte o Marathon para as tarefas em execução: 

```bash
curl localhost/marathon/v2/tasks
```

Localize o valor do `host` na saída (um endereço IP semelhante a `10.32.0.x`) e o valor de `ports`.


Agora faça uma conexão de terminal SSH (e não uma conexão por túnel) para o FQDN de gerenciamento do cluster. Uma vez conectado, faça a solicitação a seguir substituindo os valores corretos de `host` e `ports`:

```bash
curl http://host:ports
```

A saída do servidor Nginx deverá ser semelhante à seguinte:

![Nginx do contêiner](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Dimensionar seus contêineres
Você pode usar a API do Marathon para expandir ou reduzir horizontalmente as implantações de aplicativos. No exemplo anterior, você implantou uma instância de um aplicativo. Vamos expandir essa saída para três instâncias de um aplicativo. Para fazer isso, crie um arquivo JSON usando o texto JSON a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

De sua conexão por túnel, execute o comando a seguir para escalar horizontalmente o aplicativo.

> [!NOTE]
> O URI é\/http: /localhost/marathon/v2/apps/ seguido do ID do aplicativo para escala. Se você estiver usando a amostra nginx fornecida aqui,\/o URI estaria http: /localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto de extremidade do Marathon para aplicativos. Você vê que agora há três contêineres Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Comandos equivalentes do PowerShell
Você pode executar essas mesmas ações usando comandos do PowerShell em um sistema Windows.

Para saber mais sobre o cluster DC/OS, como nomes e status de agentes, execute o comando a seguir:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Você implanta os contêineres formatados pelo Docker por meio do Marathon usando um arquivo JSON que descreve a implementação planejada. O exemplo a seguir implanta o contêiner Nginx, associando a porta 80 do agente DC/OS à porta 80 do contêiner.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implantar um contêiner Docker formatado, armazene o arquivo JSON em um local acessível. Em seguida, para implantar o contêiner, execute o comando a seguir. Especifique o caminho para o arquivo JSON (neste exemplo, `marathon.json`).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Você também pode usar a API do Marathon para expandir ou reduzir horizontalmente as implantações de aplicativos. No exemplo anterior, você implantou uma instância de um aplicativo. Vamos expandir essa saída para três instâncias de um aplicativo. Para fazer isso, crie um arquivo JSON usando o texto JSON a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para escalar horizontalmente o aplicativo:

> [!NOTE]
> O URI é\/http: /localhost/marathon/v2/apps/ seguido do ID do aplicativo para escala. Se você estiver usando a amostra nginx fornecida aqui, o URI estaria http:\//localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Próximas etapas
* [Leia mais sobre os pontos de extremidade HTTP Mesos](https://mesos.apache.org/documentation/latest/endpoints/)
* [Leia mais sobre a API REST do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)

