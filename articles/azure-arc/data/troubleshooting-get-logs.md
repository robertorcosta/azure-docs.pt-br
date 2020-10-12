---
title: Obter logs para solucionar problemas do controlador de dados habilitado para o Azure Arc
description: Obtenha logs de serviço para solucionar problemas do controlador de dados habilitado para Arc do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933206"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Obter logs de serviços de dados habilitados para o Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para recuperar os logs de serviços de dados habilitados para o Azure Arc, você precisará da ferramenta CLI de dados do Azure. [Instruções de instalação](./install-client-tools.md)

Você precisará ser capaz de fazer logon no serviço do controlador de serviços de dados habilitado para Arc do Azure como administrador.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Obter logs de serviços de dados habilitados para o Azure Arc

Você pode obter os logs de serviços de dados habilitados para o Azure ARC em todos os pods ou pods específicos para fins de solução de problemas.  Você pode fazer isso usando ferramentas de kubernetes padrão, como o `kubectl logs` comando ou neste artigo, você usará a ferramenta CLI de dados do Azure, que facilita a obtenção de todos os logs de uma vez.

Primeiro, verifique se você está conectado ao controlador de dados.

```console
azdata login
```

Em seguida, execute o seguinte comando para despejar os logs:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Os arquivos de log serão criados no diretório de trabalho atual por padrão em um subdiretório chamado ' logs '.  Você pode gerar os arquivos de log para um diretório diferente usando o `--target-folder` parâmetro.

Você pode optar por compactar os arquivos omitindo o `--skip-compress` parâmetro.

Você pode disparar e incluir despejos de memória omitindo o `--exclude-dumps` , mas isso não é recomendado, a menos que suporte da Microsoft tenha solicitado os despejos de memória.  Fazer um despejo de memória requer que a configuração do controlador de dados `allowDumps` esteja definida como `true` a hora da criação do controlador de dados.

Opcionalmente, você pode optar por filtrar para coletar logs para apenas um pod ( `--pod` ) ou contêiner ( `--container` ) específico por nome.

Você também pode optar por filtrar para coletar logs de um recurso personalizado específico, passando o `--resource-kind` e o `--resource-name` parâmetro.  O `resource-kind` valor do parâmetro deve ser um dos nomes de definição de recurso personalizados que podem ser recuperados pelo comando `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Exemplo de hierarquia de pastas.  Observe que a hierarquia de pastas é organizada pelo nome do pod e, em seguida, pelo contêiner e pela hierarquia de diretório no contêiner.

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```