---
title: Acoleção de endereços IP do Azure Application Insights | Microsoft Docs
description: Entender como endereços IP e geolocalização são tratados com o Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656510"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocalização e manipulação de endereço IP

Este artigo explica como a pesquisa de geolocalização e o manuseio de endereçoIP ocorrem no Application Insights, juntamente com como modificar o comportamento padrão.

## <a name="default-behavior"></a>Comportamento padrão

Os endereços IP padrão são temporariamente coletados, mas não armazenados no Application Insights. O processo básico é como descrito a seguir:

Os endereços IP são enviados para o Application Insights como parte dos dados de telemetria. Ao chegar ao ponto final de ingestão no Azure, o endereço IP é usado para realizar uma procurar de geolocalização usando [GeoLite2 do MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Os resultados desta pesquisa são usados `client_City`para `client_StateOrProvince` `client_CountryOrRegion`preencher os seguintes campos, . Neste ponto, o endereço IP `0.0.0.0` é descartado `client_IP` e é escrito para o campo.

* Telemetria do navegador: Coletamos temporariamente o endereço IP do remetente. O endereço IP é calculado pelo ponto final da ingestão.
* Telemetria do servidor: O módulo Application Insights coleta temporariamente o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver configurado.

Esse comportamento é por design para ajudar a evitar a coleta desnecessária de dados pessoais. Sempre que possível, recomendamos evitar a coleta de dados pessoais. 

## <a name="overriding-default-behavior"></a>Sobrepondo o comportamento padrão

Embora o comportamento padrão seja minimizar a coleta de dados pessoais, ainda oferecemos a flexibilidade para coletar e armazenar dados de endereço IP. Antes de optar por armazenar quaisquer dados pessoais como endereços IP, recomendamos fortemente verificar se isso não quebra quaisquer requisitos de conformidade ou regulamentos locais aos quais você possa estar sujeito. Para saber mais sobre o tratamento de dados pessoais no Application Insights, consulte as [orientações para dados pessoais.](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)

## <a name="storing-ip-address-data"></a>Armazenamento de dados de endereço IP

Para habilitar a coleta e `DisableIpMasking` o armazenamento de IP, a `true`propriedade do componente Application Insights deve ser definida como . Essa propriedade pode ser definida através de modelos do Azure Resource Manager ou ligando para a API REST. 

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Se você só precisa modificar o comportamento de um único recurso do Application Insights, a maneira mais fácil de conseguir isso é através do portal Azure.  

1. Vá para o recurso do aplicativo Insights >**modelo de exportação de** **configurações** >  

    ![Modelo de exportação](media/ip-collection/export-template.png)

2. Selecione **Implantar**

    ![Botão Implantar destacado em vermelho](media/ip-collection/deploy.png)

3. Selecione **'Editar modelo '** (Se o seu modelo tiver propriedades ou recursos adicionais que não aparecem neste modelo de exemplo, proceda com cautela para garantir que todos os recursos aceitem a implantação do modelo como uma alteração/atualização incremental.)

    ![Editar Modelo](media/ip-collection/edit-template.png)

4. Faça as seguintes alterações no json para o seu recurso e clique **em Salvar**:

    ![Captura de tela adiciona uma comma após "IbizaAIExtension" e adiciona uma nova linha abaixo com "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Se você experimentar um erro que diz: ** _O grupo de recursos está em um local que não é suportado por um ou mais recursos no modelo. Por favor, escolha um grupo de recursos diferente._** Selecione temporariamente um grupo de recursos diferente da parada e, em seguida, selecione novamente seu grupo de recursos original para resolver o erro.

5. Selecione **eu concordo** > **Compra**. 

    ![Editar Modelo](media/ip-collection/purchase.png)

    Neste caso, nada de novo está sendo comprado, estamos apenas atualizando a configuração do recurso de Insights de Aplicativos existente.

6. Uma vez que a implantação esteja completa, novos dados de telemetria serão gravados.

    Se você fosse selecionar e editar modelo novamente, você só veria o modelo padrão e não veria sua propriedade recém-adicionada e seu valor associado. Se você não estiver vendo dados de `"DisableIpMasking": true` endereço IP e quiser confirmar se está definido. Execute o seguinte PowerShell: (Substitua pelo `Fabrikam-dev` nome apropriado do grupo de recursos e recursos.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Uma lista de propriedades será devolvida como resultado. Uma das propriedades `DisableIpMasking: true`deve ser lida. Se você executar o PowerShell antes de implantar a nova propriedade com o Azure Resource Manager, a propriedade não existirá.

### <a name="rest-api"></a>API Rest

A carga [de aPI de resto](https://docs.microsoft.com/rest/api/azure/) para fazer as mesmas modificações é a seguinte:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicializador de telemetria

Se você precisar de `DisableIpMasking` uma alternativa mais flexível do que gravar todos ou parte dos endereços IP, você pode usar um [inicializador de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) para copiar tudo ou parte do IP para um campo personalizado. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / Núcleo ASP.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Se você não `ISupportProperties`puder acessar, verifique e certifique-se de que está executando a versão estável mais recente do Application Insights SDK. `ISupportProperties`destinam-se a altos valores de cardinalidade, enquanto `GlobalProperties` são mais apropriados para valores de baixa cardinalidade, como nome da região, nome do ambiente, etc. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Habilite o inicializador de telemetria para ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Habilite o inicializador de telemetria para ASP.NET Core

Você pode criar seu inicializador de telemetria da mesma forma para ASP.NET Core como ASP.NET mas para habilitar o inicializador, use o seguinte exemplo de referência:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>JavaScript do lado do cliente

Ao contrário dos SDKs do lado do servidor, o Javascript SDK do lado do cliente não calcula o endereço IP. Por padrão, o cálculo do endereço IP para telemetria do lado do cliente é realizado no ponto final de ingestão no Azure após a chegada da telemetria. Isso significa que, se você estiver enviando dados do lado do cliente para um proxy e, em seguida, encaminhando para o ponto final da ingestão, o cálculo do endereço IP pode mostrar o endereço IP do proxy e não o cliente. Se nenhum proxy for usado, isso não deve ser um problema.

Se você deseja calcular o endereço IP diretamente no lado do cliente, você precisará adicionar sua `ai.location.ip` própria lógica personalizada para executar esse cálculo e usar o resultado para definir a tag. Quando `ai.location.ip` definido, o cálculo do endereço IP não é realizado pelo ponto final da ingestão e o endereço IP fornecido é honrado e usado para realizar a busca geográfica. Neste cenário, o endereço IP ainda será zerado por padrão. 

Para reter todo o endereço IP calculado a partir de sua lógica personalizada, você poderia usar `ai.location.ip` um inicializador de telemetria que copiaria os dados de endereço IP fornecidos em um campo personalizado separado. Mas, novamente, ao contrário dos SDKs do lado do servidor, sem depender de bibliotecas de terceiros ou de sua própria lógica personalizada de coleta de IP do lado do cliente, o SDK do lado do cliente não calculará o IP para você.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Veja os resultados do seu inicializador de telemetria

Se você ativar um novo tráfego contra seu site e esperar aproximadamente 2-5 minutos para garantir que ele teve tempo de ser ingerido, você pode executar uma consulta kusto para ver se a coleta de endereços IP está funcionando:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Endereços IP recém-coletados devem aparecer na `customDimensions_client-ip` coluna. A `client-ip` coluna padrão ainda terá todos os 4 octetos zerados ou exibindo apenas os três primeiros octetos, dependendo de como você configurou a coleta de endereços IP no nível do componente. Se você está testando localmente depois de implementar o inicializador de telemetria e o valor que você vê `customDimensions_client-ip` é `::1` esse comportamento esperado. `::1`representa o endereço de loopback no IPv6. É equivalente `127.0.01` ao IPv4 e é o resultado que você verá ao testar no localhost.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a coleta de dados pessoais](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) no Application Insights.

* Saiba mais sobre como funciona a [coleta de endereços IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) no Application Insights. (Este é um post de blog externo mais antigo escrito por um de nossos engenheiros. Ele antecede o comportamento padrão atual onde `0.0.0.0`o endereço IP é registrado como , `ClientIpHeaderTelemetryInitializer`mas ele vai em maior profundidade sobre a mecânica do built-in .)
