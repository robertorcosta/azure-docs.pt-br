---
title: Perguntas Frequentes do Application Insights do Azure | Microsoft Docs
description: Perguntas frequentes sobre o Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.openlocfilehash: 55a096cd4971664e55bb2cfd17f9f8927d7c32f5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899514"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Perguntas Frequentes

## <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar:*

* [Aplicativo .NET](asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Aplicativo Web Java](java-troubleshoot.md)

*Não recebo qualquer valor de meu servidor*

* [Definir exceções de firewall](ip-addresses.md)
* [Configurar um servidor ASP.NET](monitor-performance-live-website-now.md)
* [Configurar um servidor Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>É possível usar o Application Insights com...?

* [Aplicativos Web em um servidor IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure](azure-vm-vmss-apps.md)
* [Aplicativos Web em um servidor IIS : em uma VM ou local](asp-net.md)
* [Aplicativos Web Java](java-get-started.md)
* [Aplicativos do Node.js](nodejs.md)
* [Aplicativos Web no Azure](azure-web-apps.md)
* [Serviços de Nuvem no Azure](cloudservices.md)
* [Servidores de aplicativo executando em Docker](docker.md)
* [Aplicativos Web de página única](javascript.md)
* [SharePoint](sharepoint.md)
* [Aplicativo da área de trabalho do Windows](windows-desktop.md)
* [Outras plataformas](platforms.md)

## <a name="is-it-free"></a>É gratuito?

Sim, para uso experimental. No plano de preço básico, seu aplicativo pode enviar uma determinada margem de dados por mês gratuitamente. A margem gratuita é grande o suficiente para cobrir o desenvolvimento e publicação de um aplicativo para um número reduzido de usuários. Você pode definir um limite para impedir que mais de uma determinada quantidade de dados seja processada.

Volumes maiores de telemetria são cobrados pelo Gb. Fornecemos algumas dicas sobre como [limitar seus encargos](pricing.md).

O plano Empresarial incorre em uma encargo para cada dia em que cada nó do servidor Web envia telemetria. É adequado se você quiser usar a Exportação Contínua em grande escala.

[Leia o plano de preço](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-does-it-cost"></a>Quanto custa?

* Abra a **página Uso e custos estimados** em um recurso do Application Insights. Há um gráfico de uso recente. Você pode definir um limite de volume de dados, se desejar.
* Abra a [Folha de Cobrança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver suas contas em todos os recursos.

## <a name="q14"></a>O que o Application Insights modifica no meu projeto?
Os detalhes dependem do tipo de projeto. Para um aplicativo Web:

* Adiciona estes arquivos ao seu projeto:

  * ApplicationInsights.config.
  * ai.js
* Instala estes pacotes NuGet:

  * *API do Application Insights* - a API principal
  * *API do Application Insights para Aplicativos Web* - usada para enviar telemetria do servidor
  * *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria do cliente

    Os pacotes incluem os seguintes assemblies:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:

  * Web.config
  * packages.config
* (Somente novos projetos – se você [adicionar Application insights a um projeto existente][start], precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar de versões anteriores do SDK?
Consulte as [notas de versão](release-notes.md) para o SDK adequado ao seu tipo de aplicativo.

## <a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?
No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.

## <a name="what-is-status-monitor"></a>O que é Status Monitor?

Um aplicativo da área de trabalho que você pode usar no servidor Web do IIS para ajudar a configurar o Application Insights em aplicativos Web. Ele não coleta telemetria: você pode interrompê-lo quando não estiver configurando um aplicativo. 

[Saiba mais](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Qual a telemetria coletada pelo Application Insights?

A partir dos aplicativos Web do servidor:

* Solicitações HTTP
* [Dependências](asp-net-dependencies.md). Chamadas para: Banco de Dados SQL; chamadas HTTP para serviços externos; tabela, armazenamento de blobs, fila e BD Cosmos do Azure. 
* [Exceções](asp-net-exceptions.md) e rastreamentos de pilha.
* [Contadores de desempenho](performance-counters.md) -se você [usar status monitor](monitor-performance-live-website-now.md), [monitoramento do Azure para serviços de aplicativos](azure-web-apps.md), [monitoramento do Azure para VM ou conjunto de dimensionamento de máquinas virtuais](azure-vm-vmss-apps.md)ou o [gravador Application insights coletado](java-collectd.md).
* [Eventos e métricas personalizados](api-custom-events-metrics.md) que você codifica.
* [Logs de Rastreamento](asp-net-trace-logs.md) se você configurar o coletor apropriado.

A partir das [páginas da Web do cliente](javascript.md):

* [Contagens de exibição de página](usage-overview.md)
* [Chamadas AJAX](asp-net-dependencies.md) Solicitações feitas a partir de um script em execução.
* Dados de carregamento de exibição de página
* Contagens de sessão e usuários
* [IDs de Usuário Autenticado](api-custom-events-metrics.md#authenticated-users)

A partir de outras fontes, se você configurá-las:

* [Diagnóstico do Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Importar no Analytics](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Eu posso filtrar ou modificar alguma telemetria?

Sim, no servidor você pode gravar:

* Processador de Telemetria para filtrar ou adicionar propriedades a itens de telemetria selecionados antes de serem enviados do seu aplicativo.
* Inicializador de Telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais sobre [ASP.NET](api-filtering-sampling.md) ou [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como a cidade, o país/região e outros dados de localização geográfica são calculados?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente Web usando [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: coletamos o endereço IP do remetente.
* Telemetria do Servidor: o módulo Application Insights coleta o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver configurado.
* Para saber mais sobre como o endereço IP e os dados de localização geográfica são coletados no Application Insights consulte este [artigo](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


É possível configurar o `ClientIpHeaderTelemetryInitializer` para coletar o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, ele é movido por um proxy, balanceador de carga ou CDN para `X-Originating-IP`. [Saiba mais](https://apmtips.com/blog/2016/07/05/client-ip-address/).

É possível [usar o Power BI](export-power-bi.md ) para exibir sua telemetria de solicitação em um mapa.


## <a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?
Dê uma olhada na [retenção e na privacidade dos dados][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>É possível enviar dados pessoais na Telemetria?

Isso é possível se o seu código envia tais dados. Isso também pode acontecer se as variáveis nos rastreamentos de pilha incluírem dados pessoais. Sua equipe de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais sejam devidamente tratados. [Saiba mais sobre privacidade e retenção de dados ](data-retention-privacy.md).

**Todos** os octetos do endereço web do cliente são sempre definidos como 0 depois que os atributos de localização geográfica são pesquisados.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Minha chave de instrumentação está visível na minha origem da página da Web. 

* Essa é uma prática comum em soluções de monitoramento.
* Ele não pode ser usado para roubar seus dados.
* Ele pode ser usado para distorcer seus dados ou disparar alertas.
* Não temos conhecimento se algum cliente teve tais problemas.

Você pode:

* Use duas chaves de instrumentação separadas (recursos de Application Insights separados) para dados de cliente e servidor. Ou
* Gravar um proxy que execute no seu servidor e o cliente Web envie dados através desse proxy.

## <a name="post"></a>Como eu vejo dados de POST na pesquisa de Diagnóstico?
Nós não registramos dados de POST automaticamente, mas você pode usar uma chamada TrackTrace: colocar os dados no parâmetro de mensagem. Esse parâmetro tem um limite de tamanho maior do que os limites nas propriedades de cadeia de caracteres, embora nele você não possa aplicar filtros.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Devo usar recursos do Application Insights simples ou múltiplos?

Use um recurso único para todos os componentes ou funções em um único sistema de negócios. Use recursos separados para desenvolvimento, teste e versões de lançamento e para aplicativos independentes.

* [Consulte a discussão aqui](separate-resources.md)
* [Exemplo : serviço de nuvem com funções Web e funções de trabalho](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como faço para alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](separate-resources.md)
* [Exemplo : serviço de nuvem com funções Web e funções de trabalho](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Quais são as contagens de Sessão e Usuário?

* O SDK do JavaScript define um cookie de usuário no cliente Web para identificar os usuários que retornam e, um cookie de sessão para atividades de grupo.
* Se não houver nenhum script do lado do cliente, você poderá [definir cookies no server](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um usuário real usar seu site em diferentes navegadores, usar navegação em modo privado/incógnito ou usar diferentes computadores, então, eles serão contados mais de uma vez.
* Para identificar um usuário conectado entre navegadores e computadores, adicione uma chamada a [setAuthenticatedUserContext()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Eu habilitei tudo no Application Insights?
| O que você deverá ver | Como obter isso | Por que você deseja isso |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes da Web](monitor-web-app-availability.md) |Tenha certeza que o aplicativo Web está ativo |
| Desempenho do aplicativo para servidores: tempos de resposta... |[Adicionar o Application Insights ao seu projeto](asp-net.md) ou [Instalar o AI Status Monitor no servidor](monitor-performance-live-website-now.md) (ou escreva seu próprio código para [rastrear dependências](api-custom-events-metrics.md#trackdependency)) |Detectar problemas de desempenho |
| Telemetria de dependência |[Instalar o AI Status Monitor no servidor](monitor-performance-live-website-now.md) |Diagnosticar problemas com bancos de dados ou outros componentes externos |
| Obter rastreamentos de pilha por meio de exceções |[Inserir chamadas TrackException em seu código](asp-net-exceptions.md) (mas alguns são informados automaticamente) |Detectar e diagnosticar exceções |
| Pesquisar rastreamentos de log |[Adicionar um adaptador de registro em log](asp-net-trace-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de uso do cliente: modos de exibição de página, sessões,... |[Inicializador de JavaScript em páginas da Web](javascript.md) |Analíticas de uso |
| Métricas de cliente personalizadas |[Rastreando chamadas em páginas da Web](api-custom-events-metrics.md) |Aprimorar a experiência do usuário |
| Métricas de servidor personalizadas |[Rastreando chamadas no servidor](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens nos gráficos de Pesquisa e Métricas são desiguais?

A [Amostragem](sampling.md) reduz o número de itens de telemetria (solicitações, eventos personalizados e, assim por diante) que são realmente enviados de seu aplicativo para o portal. Em Pesquisa, você visualiza o número de itens realmente recebidos. Nos gráficos de métrica que exibem uma contagem de eventos, você visualiza o número de eventos originais que ocorreu. 

Cada item transmitido carrega uma propriedade `itemCount` que mostra quantos eventos originais esse item representa. Para observar a amostragem em operação, é possível executar essa consulta no Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automação

### <a name="configuring-application-insights"></a>Configurando o Application Insights

Você pode [gravar scripts do PowerShell](powershell.md) usando o Azure Resource Monitor para:

* Criar e atualizar recursos do Application Insights.
* Definir o plano de preços.
* Obter a chave de instrumentação.
* Adicionar um alerta de métrica.
* Adicionar um teste de disponibilidade.

Não é possível configurar um relatório do Metric Explorer ou configurar a exportação contínua.

### <a name="querying-the-telemetry"></a>Consultar a telemetria

Use a [API REST](https://dev.applicationinsights.io/) para executar consultas do [Analytics](analytics.md).

## <a name="how-can-i-set-an-alert-on-an-event"></a>Como configurar um alerta em um evento?

Os alertas do Azure são somente em métricas. Crie uma métrica personalizada que cruze um limite de valor sempre que o evento ocorrer. Em seguida, configure um alerta na métrica. Observe que: você receberá uma notificação sempre que a métrica cruzar o limite em qualquer direção; você não receberá uma notificação até o primeiro cruzamento, não importando se o valor inicial é alto ou baixo; sempre haverá uma latência de alguns minutos.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Há encargos de transferência de dados entre um aplicativo Web e o Application Insights?

* Se seu aplicativo Web do Azure estiver hospedado em um data center, onde há um ponto de extremidade de coleta do Application Insights, não haverá cobrança. 
* Se não houver um ponto de extremidade de coleta no data center do host, então, a telemetria do seu aplicativo incorrerá em [Encargos de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isso não depende de onde seu recurso Application Insights está hospedado. Depende apenas da distribuição de nossos pontos de extremidade.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>É possível enviar telemetria para o portal do Application Insights?

É recomendável usar nossos SDKs e usar a [API de SDK](api-custom-events-metrics.md). Existem variantes do SDK para várias [plataformas](platforms.md). Esses SDKs tratam buffer, compressão, limitação, repetições e, assim por diante. No entanto, o [esquema de ingestão](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e o [protocolo de ponto de extremidade](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

## <a name="can-i-monitor-an-intranet-web-server"></a>É possível monitorar um servidor Web de intranet?

Sim, mas você precisará permitir o tráfego para nossos serviços por exceções de firewall ou redirecionamentos de proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Examine nossa lista de serviços e endereços IP [aqui](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Exceção de firewall

Permita que o servidor Web envie telemetria para os pontos de extremidade. 

### <a name="gateway-redirect"></a>Redirecionamento de gateway

Encaminhe o tráfego de seu servidor para um gateway na sua intranet substituindo pontos de extremidade na sua configuração.
Se essas propriedades de “Ponto de extremidade” não estiverem presentes na sua configuração, essas classes usarão os valores padrão mostrados abaixo no exemplo ApplicationInsights.config. 

Seu gateway deve rotear o tráfego para o endereço básico do nosso ponto de extremidade. Em sua configuração, substitua os valores padrão por `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemplo ApplicationInsights.config com pontos de extremidade padrão:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Observe que ApplicationIdProvider está disponível a partir do v2.6.0_

### <a name="proxy-passthrough"></a>Passagem de proxy

A passagem de proxy pode ser obtida com a configuração de um nível de máquina ou proxy de nível de aplicativo.
Para obter mais informações, consulte o artigo do dotnet em [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exemplo de Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>É possível executar testes na Web de Disponibilidade em um servidor de intranet?

Nossos [testes na Web](monitor-web-app-availability.md) executam em pontos de presença distribuídos no mundo inteiro. Existem duas soluções:

* Porta de firewall : permitir solicitações para seu servidor da [lista mutável e longa dos agentes de teste na Web](ip-addresses.md).
* Grave seu próprio código para enviar solicitações periódicas ao seu servidor de dentro de sua intranet. Você pode executar testes na Web do Visual Studio para essa finalidade. O testador pode enviar os resultados ao Application Insights usando a API TrackAvailability().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo demora para coletar a telemetria?

A maioria dos dados do Application Insights tem uma latência inferior a cinco minutos. Alguns dados podem demorar mais; normalmente, arquivos de log maiores. Para saber mais, confira [SLA do Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Mais respostas
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
