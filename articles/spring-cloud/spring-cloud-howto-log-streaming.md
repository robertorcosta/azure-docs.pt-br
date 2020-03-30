---
title: Transmitir por streaming logs dos aplicativos do Azure Spring Cloud em tempo real
description: Como usar o log streaming para visualizar os logs de aplicativos instantaneamente
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192193"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Transmitir por streaming logs dos aplicativos do Azure Spring Cloud em tempo real
O Azure Spring Cloud permite o log streaming no Azure CLI para obter registros de consoles de aplicativos em tempo real para solução de problemas. Você também pode [analisar registros e métricas com configurações de diagnóstico](./diagnostic-services.md).

## <a name="prerequisites"></a>Pré-requisitos

* Instale [a extensão Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) para Spring Cloud, versão mínima 0.2.0 .
* Um exemplo de Nuvem de **Primavera do Azure** com um aplicativo em execução, por exemplo, [o aplicativo Spring Cloud](./spring-cloud-quickstart-launch-app-cli.md).

> [!NOTE]
>  A extensão ASC CLI é atualizada da versão 0.2.0 para 0.2.1. Essa alteração afeta a sintaxe do `az spring-cloud app log tail`comando para streaming de `az spring-cloud app logs`log: , que é substituído por: . O comando: `az spring-cloud app log tail` será preterido em um futuro lançamento. Se você estiver usando a versão 0.2.0, você pode atualizar para 0.2.1. Primeiro, remova a versão antiga `az extension remove -n spring-cloud`com o comando: .  Em seguida, instale 0.2.1 pelo comando: `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Use cli para tail logs

Para evitar especificar repetidamente o nome do grupo de recursos e da instância de serviço, defina o nome do grupo de recursos padrão e o nome do cluster.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Nos exemplos a seguir, o grupo de recursos e o nome do serviço serão omitidos nos comandos.

### <a name="tail-log-for-app-with-single-instance"></a>Registro de cauda para aplicativo com instância única
Se um aplicativo chamado auth-service tiver apenas uma instância, você poderá visualizar o registro da instância do aplicativo com o seguinte comando:
```
az spring-cloud app logs -n auth-service
```
Isso retornará os registros:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Registro de cauda para aplicativo com várias instâncias
Se existirem várias `auth-service`instâncias para o aplicativo nomeado, você poderá visualizar o registro de ocorrência usando a `-i/--instance` opção. 

Primeiro, você pode obter os nomes de instância do aplicativo com o comando seguinte.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Com resultados:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Em seguida, você pode transmitir logs `-i/--instance` de uma instância de aplicativo com a opção:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Você também pode obter detalhes das instâncias do aplicativo no portal Azure.  Depois de selecionar **Aplicativos** no painel de navegação à esquerda do serviço Azure Spring Cloud, selecione **App Instances**.

### <a name="continuously-stream-new-logs"></a>Fluxo contínuo de novos registros
Por padrão, `az spring-cloud ap log tail` imprime apenas os logs existentes transmitidos para o console do aplicativo e, em seguida, sai. Se você quiser transmitir novos logs, adicione -f (--follow):  

```
az spring-cloud app logs -n auth-service -f
``` 
Para verificar todas as opções de registro suportadas:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Próximas etapas

* [Analisar registros e métricas com configurações de diagnóstico](./diagnostic-services.md)

 





