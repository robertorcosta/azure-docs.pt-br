---
title: Personalizar a interface do usuário da solução de monitoramento remoto - Azure | Microsoft Docs
description: Este artigo fornece informações sobre como acessar o código-fonte para a interface do usuário do acelerador da solução de Monitoramento Remoto e fazer algumas personalizações.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "68608011"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalizar o acelerador da solução de Monitoramento Remoto

Este artigo fornece informações sobre como acessar o código-fonte e personalizar a interface do usuário do acelerador da solução de Monitoramento Remoto.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do usuário

O código da interface do usuário do acelerador de solução de Monitoramento Remoto é implementado usando a estrutura React.js. Você pode encontrar o código-fonte no repositório [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) do GitHub.

Para fazer alterações na interface do usuário, você pode executar uma cópia dela localmente. Para concluir ações, como recuperar a telemetria, a cópia local se conecta a uma instância implantada da solução.

As etapas a seguir descrevem o processo de configurar um ambiente local para o desenvolvimento da interface do usuário:

1. Implante uma instância **básica** do acelerador de solução usando a CLI **pcs**. Anote o nome da sua implantação e as credenciais fornecidas para a máquina virtual. Para saber mais, veja [Implantar usando a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Para habilitar o acesso SSH à máquina virtual que hospeda os micros serviços na sua solução, use o portal do Azure Cloud Shell. Por exemplo:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Habilite somente o acesso de SSH durante o desenvolvimento e teste. Se você ativar o SSH, [você deve desativá-lo assim que terminar de usá-lo](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Use o portal do Azure Cloud Shell para encontrar o nome e o endereço IP público da sua máquina virtual. Por exemplo:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Use o SSH para se conectar à sua máquina virtual. Use o endereço IP da etapa anterior e as credenciais fornecidas quando você executou **pcs** para implementar a solução. O `ssh` comando está disponível no Azure Cloud Shell.

1. Para permitir que o UX local se conecta, execute os seguintes comandos no shell bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver que o comando foi concluído e o site da Web for iniciado, você poderá se desconectar da máquina virtual.

1. Em sua cópia local do repositório [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui), edite o arquivo **.env** para adicionar a URL da sua solução implantada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Em um prompt de comando, navegue até a cópia local da pasta `azure-iot-pcs-remote-monitoring-webui`.

1. Para instalar as bibliotecas necessárias e executar a UI localmente, execute os seguintes comandos:

    ```cmd/sh
    npm install
    npm start
    ```

1. O comando anterior executa a interface do usuário localmente em http: \/ /localhost: 3000/Dashboard. Você pode editar o código enquanto o site está em execução e vê-lo ser atualizado dinamicamente.

## <a name="customize-the-layout"></a>Personalizar o layout

Cada página na solução de Monitoramento Remoto é composta de um conjunto de controles, conhecido como *painéis* no código-fonte. A página **painel** é composta de cinco painéis: visão geral, mapa, alertas, telemetria e análise. Você pode encontrar o código-fonte que define cada página e seus painéis no repositório [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) do GitHub. Por exemplo, o código que define a página **Painel**, seu layout e os painéis na página está localizado na pasta [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Como os painéis gerenciam seus próprios layout e dimensionamento, você pode modificar facilmente o layout de uma página. Faça as seguintes alterações no elemento **PageContent** no arquivo `src/components/pages/dashboard/dashboard.js` para:

* Troque as posições do mapa e dos painéis de telemetria.
* Altere as larguras relativas dos painéis de mapa e análise.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Alterar layout do painel](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Você também pode adicionar várias instâncias do mesmo painel, ou várias versões, se você [duplicar e personalizar um painel](#duplicate-and-customize-an-existing-control). O exemplo a seguir mostra como adicionar duas instâncias do painel de telemetria. Para fazer essas alterações, edite o arquivo `src/components/pages/dashboard/dashboard.js`:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Você pode exibir telemetria diferente em cada painel:

![Vários painéis de telemetria](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicar e personalizar um controle existente

As etapas a seguir descrevem como duplicar um painel existente, modificá-lo e usar a versão modificada. As etapas usam o painel **alertas** como um exemplo:

1. Na sua cópia local do repositório, faça uma cópia da pasta **alertas** na pasta `src/components/pages/dashboard/panels`. Nomeie a nova cópia **cust_alerts**.

1. No arquivo **alertsPanel.js** na pasta **cust_alerts**, edite o nome da classe como **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adicione a seguinte linha ao arquivo `src/components/pages/dashboard/panels/index.js` :

    ```javascript
    export * from './cust_alerts';
    ```

1. Substituir `alertsPanel` por `CustAlertsPanel` no arquivo `src/components/pages/dashboard/dashboard.js`:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Agora você substituiu o painel de **alertas** original por uma cópia chamada **CustAlerts**. Esta cópia é igual à original. Agora você pode modificar a cópia. Por exemplo, para alterar a ordenação das colunas no painel **alertas**:

1. Abra o arquivo `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` .

1. Modifique as definições de coluna conforme mostrado no seguinte snippet de código:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

A captura de tela a seguir mostra a nova versão do painel **alertas**:

![Painel de alertas atualizado](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalizar o gráfico de telemetria

Os arquivos na pasta `src/components/pages/dashboard/panels/telemtry` definem o gráfico de telemetria na página **Dashboard**. A interface do usuário recupera a telemetria de back-end da solução no arquivo `src/services/telemetryService.js`. As etapas a seguir mostram como alterar o período de tempo exibido no gráfico de telemetria de 15 a 5 minutos:

1. No arquivo `src/services/telemetryService.js`, localize a função chamada **getTelemetryByDeviceIdP15M**. Faça uma cópia dessa função e modifique a cópia da seguinte maneira:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Para usar essa nova função para popular o gráfico de telemetria, abra o arquivo `src/components/pages/dashboard/dashboard.js`. Localize a linha que inicia o fluxo de telemetria e modifique-o da seguinte maneira:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

O gráfico de telemetria agora mostra os cinco minutos de dados de telemetria:

![Gráfico de telemetria mostrando um dia](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adicionar um novo KPI

A página **Painel** exibe KPIs no painel **Analytics**. Esses KPIs são calculados no arquivo `src/components/pages/dashboard/dashboard.js`. Os KPIs são renderizados pelo arquivo `src/components/pages/dashboard/panels/analytics/analyticsPanel.js`. As etapas a seguir descrevem como calcular e processar um novo valor de KPI na página **Painel**. O exemplo mostrado é para adicionar uma nova alteração percentual ao KPI de alarmes de aviso:

1. Abra o arquivo `src/components/pages/dashboard/dashboard.js` . Modifique o objeto **initialState** para incluir uma propriedade **warningAlertsChange** da seguinte forma:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Modifique o objeto **currentAlertsStats** para incluir **totalWarningCount** como uma propriedade:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Calcule o novo KPI. Encontre o cálculo da contagem de alertas críticos. Duplique o código e modifique a cópia da seguinte maneira:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Inclua o novo KPI **warningAlertsChange** no fluxo do KPI:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Incluir as novas **warningAlertsChange** KPI nos dados de estado usados para renderizar a interface do usuário:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Atualize os dados passados para o painel de KPIs:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Agora você terminou as alterações no arquivo `src/components/pages/dashboard/dashboard.js`. As etapas a seguir descrevem as alterações a fazer no arquivo `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` para exibir o novo KPI:

1. Modifique a linha de código a seguir para recuperar o novo valor de KPI da seguinte maneira:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modifique a marcação para exibir o novo valor do KPI da seguinte maneira:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

A página **Painel** agora exibe o novo valor de KPI:

![KPI de aviso](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalizar o mapa

Consulte a página [Personalizar mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) no GitHub para obter detalhes sobre os componentes de mapa na solução.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e de visualizações na solução de Monitoramento Remoto, você pode editar o código. Os repositórios do GitHub relevantes são:

* [O microsserviço de configuração para soluções do Azure IoT (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [O microsserviço de configuração para soluções do Azure IoT (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Interface do usuário da Web do Monitoramento Remoto de PCS do Azure IoT](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a interface do usuário da Web no acelerador de solução de Monitoramento Remoto. Para saber mais sobre como personalizar a interface do usuário, consulte os seguintes artigos:

* [Adicione uma página personalizada à interface da Web da web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-page.md)
* [Adicione um serviço personalizado à interface do usuário da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-service.md)
* [Adicionar uma grade personalizada à interface da Web da web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-grid.md)
* [Adicionar um desdobrável personalizado à interface de usuário da web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Adicionar um painel personalizado ao dashboard na interface do usuário da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-panel.md)

Para obter mais informações conceituais sobre o acelerador da solução de Monitoramento Remoto, veja [Arquitetura de Monitoramento Remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar os microsserviços de solução de monitoramento remoto, consulte [Personalizar e reimplantar um microsserviço](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
