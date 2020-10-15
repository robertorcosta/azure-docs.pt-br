---
title: Integrar Insights do Time Series com o Monitoramento Remoto - Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar o recurso Time Series Insights para uma solução de monitoramento remoto existente que ainda não inclui Insights de Séries Temporais.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 8b6219c82dc23deb467e87a6866839b8eace8b5d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072638"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrar o Azure Time Series Insights com o Monitoramento Remoto

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização para o gerenciamento de dados de série temporal em escala IoT na nuvem. Você pode usar os Insights de Séries Temporais para armazenar e gerenciar dados de séries temporais, explorar e visualizar eventos simultaneamente, conduzir análises de causa raiz e comparar vários sites e ativos.

O acelerador de solução de monitoramento remoto agora fornece implantação e integração automáticas com o recurso Time Series Insights. Neste tutorial, você aprenderá a configurar o recurso Time Series Insights para uma solução de monitoramento remoto existente que ainda não inclui Insights de Séries Temporais.

> [!NOTE]
> Os Time Series Insights não estão disponíveis atualmente na nuvem do Azure China. As novas implantações do acelerador de solução de monitoramento remoto na nuvem do Azure China usam o Cosmos DB para todo o armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa já ter implantado uma solução de monitoramento remoto:

* [Implantar o acelerador de solução de monitoramento remoto](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado em seu Hub IoT para ser usado para dados de fluxo contínuo no Time Series Insights.

> [!NOTE]
> Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hub IoT do Azure. Cada grupo de consumidores permite até cinco consumidores de saída. Você deve criar um novo grupo de consumidores para cada cinco coletores de saída, e cada grupo pode ter até 32 consumidores.

1. No Portal do Azure, clique no botão do Cloud Shell.

1. Execute o seguinte comando para criar um novo grupo de consumidores. Use o nome do hub IoT em sua implantação do Remote Monitoring e o nome da implantação do Remote Monitoring como o nome do grupo de recursos:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Implantar o Time Series Insights

Em seguida, implante os Insights do Time Series como um recurso adicional em sua solução de monitoramento remoto e conecte-o ao hub da IoT.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso**  >  **Internet das coisas**  >  **Time Series insights**.

    ![Novo Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Para criar seu ambiente do Time Series Insights, use os valores da tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do ambiente | A captura de tela a seguir usa o nome **contorosrmtsi**. Ao concluir esta etapa, escolha seu próprio nome exclusivo. |
    | Subscription | Na lista suspensa, selecione sua assinatura do Azure. |
    | Resource group | **Use existente**. Selecione o nome do seu grupo de recursos existente de Monitoramento Remoto. |
    | Location | Estamos usando **Leste dos EUA**. Crie seu ambiente na mesma região da sua solução de monitoramento remoto, se possível. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Criar Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Clique em **Criar**. Pode levar alguns instantes para o ambiente a ser criado.

## <a name="create-event-source"></a>Criar uma origem de eventos

Crie uma nova fonte de evento para se conectar ao hub IoT. Use o grupo de consumidores criado nas etapas anteriores. O Time Series Insights requer que cada serviço tenha um grupo de consumidores exclusivo que não esteja em uso por outro serviço.

1. Navegue para o seu novo ambiente do Time Series Insights.

1. À esquerda, selecione **Origens do Evento**.

    ![Exibir Origens do Evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**.

    ![Adicionar Origem do Evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar seu hub IoT como uma nova origem do evento, use os valores da tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da origem do evento | A captura de tela a seguir usa o nome **contosorm-iot-hub**. Ao concluir esta etapa, use seu próprio nome exclusivo. |
    | Fonte | **Hub IoT** |
    | Importar opção | **Usar o Hub IoT nas assinaturas disponíveis** |
    | ID da assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Nome do Hub IoT | **contosorma57a6**. Use o nome do seu hub IoT na solução de Monitoramento Remoto. |
    | Nome da política do hub IoT | **iothubowner** Confirme se a política utilizada é uma política de proprietário. |
    | Chave de política do hub IoT | Este campo é preenchido automaticamente. |
    | Grupo de consumidores do hub IoT | **timeseriesinsights** |
    | Formato de serialização do evento | **JSON**     | 
    | Nome da propriedade timestamp | Deixar em branco |

    ![Criar Origem do Evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

## <a name="configure-the-data-access-policy"></a>Configurar a política de acesso a dados

Para garantir que todos os usuários com acesso à sua solução de monitoramento remoto consigam explorar os dados no explorer Insights do Time Series, inclua seu aplicativo e os usuários em diretivas de acesso a dados no portal do Azure. 

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha o grupo de recursos **ContosoRM**.

1. Escolha **contosormtsi** na lista de recursos do Azure.

1. Escolha **Políticas de acesso a dados** para ver a lista atual de atribuições de funções.

1. Escolha **Adicionar** para abrir o painel **Selecionar regra do usuário**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

1. No **função** lista suspensa, selecione uma função, como **leitor** e **Colaborador**.

1. Na lista **Selecionar**, selecione um usuário, grupo ou aplicativo. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Escolha **Salvar** para criar a atribuição de função. Após alguns instantes, a entidade de segurança é atribuída a função nas políticas de acesso de dados.

> [!NOTE]
> Se você precisar conceder acesso a usuários adicionais para o gerenciador do Time Series Insights, use estas etapas para [conceder o acesso a dados](../time-series-insights/concepts-access-policies.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Configurar o Azure Stream Analytics 

A próxima etapa é configurar o microsserviço do Azure Stream Analytics Manager para descontinuar o envio de mensagens para o Cosmos DB e armazená-las apenas em Insights de Séries Temporais. Pule esta etapa se quiser duplicar suas mensagens no Cosmos DB.

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha o grupo de recursos **ContosoRM**.

1. Encontre o Stream Analytics ASA (Azure) o trabalho na lista de recursos de streaming. O nome do recurso começa com **streamingjobs-**.

1. Na parte superior, clique no botão para interromper os trabalhos de streaming do ASA.

1. Edite a consulta ASA e remova as cláusulas **SELECT**, **INTO** e **FROM** que apontam para o fluxo de mensagens no Cosmos DB. Essas cláusulas devem estar na parte inferior da consulta e se parecer com o seguinte exemplo:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Reinicie os jobs de streaming do Azure Stream Analytics.

7. Extraia as alterações mais recentes no microsservidor do gerenciador do Azure Stream Analytics digitando o seguinte comando no prompt de comando:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Configurar o microsserviço de telemetria

Puxe o microsserviço de Telemetria mais recente digitando o seguinte comando no prompt de comando:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcional] * Configurar a interface da web para vincular ao Gerenciador de Time Series Insights

Para visualizar facilmente seus dados no explorador de Insights do Time Series, recomendamos personalizar a interface do usuário para vincular-se facilmente ao ambiente. Para fazer isso, puxe as alterações mais recentes para a UI da Web usando o seguinte comando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Configurar as variáveis de ambiente

Para completar a integração do Time Series Insights, você precisará configurar o ambiente de sua implantação para os microsserviços atualizados.

### <a name="basic-deployments"></a>Implantações básicas

Configure o ambiente da implementação de `basic` para os microsserviços atualizados.

1. No portal do Azure, clique na guia **Azure Active Directory** no painel à esquerda.

1. Clique em **registros de aplicativo**.

1. Pesquise e clique no seu aplicativo **ContosoRM**.

1. Navegue até **configurações**  >  **chaves** e, em seguida, crie uma nova chave para seu aplicativo. Certifique-se de copiar o valor-chave para um local seguro.

1. Faça pull do [arquivo YAML do Docker Compose mais recente](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) do repositório GitHub usando a marca mais recente. 

1. SSH na VM seguindo as etapas descritas em [como criar e usar chaves SSH](../virtual-machines/linux/ssh-from-windows.md).

1. Uma vez conectado, digite `cd /app`.

1. Inclua as seguintes variáveis de ambiente em cada microsserviço no arquivo yaml de composição do docker e no script `env-setup` na VM:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navegue para o **serviço de telemetria** e também edite o arquivo de composição do docker adicionando as mesmas variáveis de ambiente acima.

1. Navegue para o **serviço do gerenciador de ASA** e edite o arquivo de composição do docker adicionando `PCS_TELEMETRY_STORAGE_TYPE`.

1. Reinicie os contêineres docker usando `sudo ./start.sh` da VM.

> [!NOTE]
> A configuração acima de variáveis de ambiente é válida para versões de monitoramento remoto antes de 1.0.2

### <a name="standard-deployments"></a>Implantações padrão

Configurar o ambiente de `standard` implantação para os serviços de micro atualizados acima

1. Na linha de comando, execute `kubectl proxy`. Para mais informações, consulte [acessando a API do Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/#using-kubectl-to-start-a-proxy-server).

1. Abra o console de gerenciamento do Kubernetes.

1. Encontre o mapa de configuração para incluir as novas variáveis de ambiente a seguir para o TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Edite o arquivo yaml do modelo para o pod de serviço de telemetria:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Edite o arquivo yaml de modelo para o pod de serviço do gerenciador ASA:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Próximas etapas

* Para aprender sobre como explorar seus dados e diagnosticar um alerta no explorador do Time Series Insights, consulte nosso tutorial sobre [conduzir uma análise de causa raiz](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Para aprender a explorar e consultar dados no explorador Estatísticas de Tempo, consulte a documentação no [explorador do Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).