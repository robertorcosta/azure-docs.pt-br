---
title: Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web | Microsoft Docs
description: Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670025"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitore a disponibilidade de qualquer site

Depois de implantar seu aplicativo/site web, você pode configurar testes recorrentes para monitorar a disponibilidade e a capacidade de resposta. [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele pode alertá-lo se sua aplicação não está respondendo, ou se ele responde muito lentamente.

Você pode configurar testes de disponibilidade para qualquer ponto de extremidade HTTP ou HTTPS que for acessível da Internet pública. Você não precisa fazer nenhuma alteração no site que você está testando. Na verdade, nem precisa ser um site que você possui. Você pode testar a disponibilidade de uma API REST da qual seu serviço depende.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Existem três tipos de testes de disponibilidade:

* [Teste de ping de URL](#create-a-url-ping-test): um teste simples que você pode criar no Portal do Azure.
* [Teste web em várias etapas](availability-multistep.md): Uma gravação de uma seqüência de solicitações web, que pode ser reproduzida para testar cenários mais complexos. Testes web em várias etapas são criados no Visual Studio Enterprise e enviados para o portal para execução.
* [Testes de disponibilidade de faixa personalizados](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): Se você `TrackAvailability()` decidir criar um aplicativo personalizado para executar testes de disponibilidade, o método pode ser usado para enviar os resultados para o Application Insights.

**Você pode criar até 100 testes de disponibilidade por recurso Application Insights.**

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, primeiro você precisa criar um recurso do Application Insights. Se você já criou um recurso, vá para a próxima seção para [criar um teste de ping de URL](#create-a-url-ping-test).

No portal do Azure, selecione **Criar um recurso** > **Developer Tools** > **Application Insights** e [criar um recurso Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping de URL

O nome "teste de ping de URL" é um pouco errado. Para ficar claro, este teste não está fazendo uso do ICMP (Internet Control Message Protocol) para verificar a disponibilidade do seu site. Em vez disso, ele usa a funcionalidade de solicitação HTTP mais avançada para validar se um ponto final está respondendo. Ele também mede o desempenho associado a essa resposta, e adiciona a capacidade de definir critérios de sucesso personalizados, juntamente com recursos mais avançados, como analisar solicitações dependentes e permitir repetições.

Para criar sua primeira solicitação de disponibilidade, abra o painel Disponibilidade e selecione **Criar teste**.

![Preencha pelo menos o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Crie um teste

|Configuração| Explicação
|----|----|----|
|**Url** |  A URL pode ser qualquer página da web que você deseja testar, mas ela deve estar visível na Internet pública. A URL pode incluir uma cadeia de consulta. Por exemplo, você pode utilizar um pouco seu banco de dados. Se a URL for resolvida para um redirecionamento, nós a seguiremos, até um máximo de 10 redirecionamentos.|
|**Parse solicitações dependentes**| O teste solicita imagens, scripts, arquivos de estilo e outros arquivos que fazem parte da página da Web em teste. O tempo de resposta gravado inclui o tempo necessário para obter esses arquivos. O teste falha se qualquer um desses recursos não puder ser baixado com sucesso dentro do tempo para todo o teste. Se a opção não estiver marcada, o teste solicitará apenas o arquivo na URL especificada. Habilitar essa opção resulta em uma verificação mais rigorosa. O teste pode falhar para os casos, o que pode não ser perceptível ao navegar manualmente pelo site.
|**Habilitar tentativas de repetição**|quando o teste falha, ele é repetido após um curto intervalo. Uma falha só será relatada se três tentativas sucessivas falharem. Testes subsequentes são então executados com a frequência de teste normal. A repetição é suspensa temporariamente até o próximo sucesso. Essa regra é aplicada independentemente em cada local de teste. **Recomendamos esta opção.** Em média, aproximadamente 80% das falhas desaparecem na repetição.|
|**Freqüência de teste**| define a frequência com que o teste é executado em cada localização de teste. Com uma frequência padrão de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.|
|**Locais de teste**| São os lugares de onde nossos servidores enviam solicitações web para sua URL. **Nosso número mínimo de locais de teste recomendados é de cinco,** a fim de garantir que você pode distinguir problemas em seu site de problemas de rede. Você pode selecionar até 16 locais.

**Se sua URL não estiver visível da internet pública, você pode optar por abrir seletivamente seu firewall para permitir apenas as transações de teste através**de . Para saber mais sobre as exceções de firewall para nossos agentes de teste de disponibilidade, consulte o [guia de endereçoIP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Recomendamos fortemente testes de vários locais com **um mínimo de cinco locais.** Isso é para evitar alarmes falsos que podem resultar de problemas temporários com um local específico. Além disso, descobrimos que a configuração ideal é que o **número de locais de teste seja igual ao limiar de localização do alerta + 2**.

### <a name="success-criteria"></a>Critérios de sucesso

|Configuração| Explicação
|----|----|----|
| **Tempo de teste** |diminua esse valor para ser alertado sobre respostas lentas. O teste é considerado uma falha se as respostas de seu site não são recebidas dentro desse período. Se você tiver selecionado **Analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes devem ter sido recebidos dentro desse período.|
| **Resposta HTTP** | o código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.|
| **Correspondência de conteúdo** | Uma corda, como "Bem-vindo!" Faremos o teste que uma correspondência exata de maiúsculas e minúsculas ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo. **Apenas caracteres em inglês são suportados com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Configuração| Explicação
|----|----|----|
|**Quase em tempo real (Visualização)** | Recomendamos o uso de alertas quase em tempo real. A configuração desse tipo de alerta é feita após a criação do teste de disponibilidade.  |
|**Clássico** | Não recomendamos mais o uso de alertas clássicos para novos testes de disponibilidade.|
|**Limiar de localização de alerta**|é recomendável um mínimo de 3/5 locais. A relação ideal entre o limiar de localização do alerta e o número de locais de teste é o número **de locais** = de ponto de alerta**de locais de teste - 2, com um mínimo de cinco locais de teste.**|

## <a name="see-your-availability-test-results"></a>Ver os resultados de teste de disponibilidade

Os resultados dos testes de disponibilidade podem ser visualizados com visualizações de gráficos de linha e dispersão.

Após alguns minutos, clique **em Atualizar** para ver os resultados do teste.

![exibição Linha](./media/monitor-web-app-availability/availability-refresh-002.png)

A exibição de dispersão mostra amostras dos resultados do teste que têm detalhes de etapa de teste de diagnóstico neles. O mecanismo de teste armazena detalhes de diagnóstico para testes com falhas. Para testes bem-sucedidos, detalhes de diagnóstico são armazenados para um subconjunto das execuções. Repassar sobre qualquer um dos pontos verde/vermelho para ver o teste, nome do teste e localização.

![exibição Linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste específico e uma localização, ou reduza o período de tempo para ver mais resultados em todo o período de tempo de interesse. Use o Search Explorer para ver os resultados de todas as execuções ou usar consultas de análise para executar relatórios personalizados sobre esses dados.

## <a name="inspect-and-edit-tests"></a>Como inspecionar e editar testes

Para editar, desativar temporariamente ou excluir um teste clique nas elipses ao lado de um nome de teste. Pode levar até 20 minutos para que as alterações de configuração se propagarem para todos os agentes de teste após uma alteração ser feita.

![Veja os detalhes do teste. Editar e desativar um teste web](./media/monitor-web-app-availability/edit.png)

Você talvez queira desabilitar testes de disponibilidade ou as regras de alerta associadas a eles, enquanto você estiver fazendo a manutenção de seu serviço.

## <a name="if-you-see-failures"></a>Se você encontrar falhas

Clique em um ponto vermelho.

![Clique em um ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

De um resultado do teste de disponibilidade, você pode ver os detalhes de transações em todos os componentes. Aqui, você pode ver:

* Inspecionar a resposta recebida do servidor.
* Diagnosticar falha na telemetria do lado do servidor correlacionado coletada durante o processamento do teste de disponibilidade com falha.
* Registrar um problema ou um item de trabalho no Git ou no Azure Boards para controlar o problema. O bug conterá um link para este evento.
* Abrir o resultado do teste na Web no Visual Studio.

Saiba mais sobre a experiência de diagnóstico de transação de ponta a ponta [aqui](../../azure-monitor/app/transaction-diagnostics.md).

Clique na linha de exceção para ver os detalhes da exceção do lado servidor que causou a falha no teste de disponibilidade sintético. Você também pode obter o [instantâneo de depuração](../../azure-monitor/app/snapshot-debugger.md) para diagnóstico mais rico em nível de código.

![Diagnóstico do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados brutos, você também pode visualizar duas métricas de disponibilidade principais no [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Disponibilidade: percentual dos testes que foram bem-sucedidos, em todas as execuções de teste.
2. Duração do teste: duração média em todas as execuções de teste.

## <a name="automation"></a>Automação

* [Use os scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) , que é chamado quando um alerta é gerado.

## <a name="troubleshooting"></a>Solução de problemas

Artigo dedicado [para solução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Próximas etapas

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes na Web com diversas etapas](availability-multistep.md)


