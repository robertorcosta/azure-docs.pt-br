---
title: Solucionando problemas para os testes de disponibilidade do Azure Application Insights | Microsoft Docs
description: Solucionando problemas com testes web no Azure Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: f135aa6c0a4a55f8a42fd858572cc811e25b27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671096"
---
# <a name="troubleshooting"></a>Solução de problemas

Este artigo irá ajudá-lo a solucionar problemas comuns que podem ocorrer ao usar o monitoramento de disponibilidade.

## <a name="ssltls-errors"></a>Erros de SSL/TLS

|Mensagem de sintoma/erro| Possíveis causas|
|--------|------|
|Não foi possível criar o Canal Seguro SSL/TLS  | Versão SSL. Apenas o TLS 1.0, 1.1 e 1.2 são suportados. **O SSLv3 não é suportado.**
|TLSv1.2 Camada de registro: Alerta (Nível: Fatal, Descrição: Bad Record MAC)| Consulte stackexchange para [obter mais informações](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL que está falhando é para um CDN (Content Delivery Network) | Isso pode ser causado por uma configuração errada em seu CDN |  

### <a name="possible-workaround"></a>Possível solução de soluções

* Se as URLs que estão enfrentando o problema são sempre para recursos dependentes, recomenda-se desativar **solicitações dependentes de análise** para o teste web.

## <a name="test-fails-only-from-certain-locations"></a>Teste falha apenas em certos locais

|Mensagem de sintoma/erro| Possíveis causas|
|----|---------|
|Uma tentativa de conexão falhou porque a parte conectada não respondeu adequadamente após um período de tempo  | Agentes de teste em certos locais estão sendo bloqueados por um firewall.|
|    |O redirecionamento de certos endereços IP está ocorrendo via (Balanceadores de carga, gerentes de tráfego geo, Azure Express Route.) 
|    |Se usar o Azure ExpressRoute, existem cenários em que os pacotes podem ser descartados nos casos em [que ocorre o Roteamento Assimétrico](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Falha de teste com um erro de violação de protocolo

|Mensagem de sintoma/erro| Possíveis causas| Resoluções possíveis |
|----|---------|-----|
|O servidor cometeu uma violação de protocolo. Section=ResponseHeader Detail=CR deve ser seguido por LF | Isso ocorre quando cabeçalhos malformados são detectados. Especificamente, alguns cabeçalhos podem não estar usando CRLF para indicar o fim da linha, o que viola a especificação HTTP. O Application Insights impõe essa especificação HTTP e falha nas respostas com cabeçalhos mal formados.| a. Entre em contato com o provedor de host do site/ provedor de CDN para corrigir os servidores defeituosos. <br> b. Caso as solicitações com falha sejam recursos (por exemplo, arquivos de estilo, imagens, scripts), você pode considerar desativar a análise de solicitações dependentes. Tenha em mente que, se você fizer isso, perderá a capacidade de monitorar a disponibilidade desses arquivos).

> [!NOTE]
> A URL pode não falhar em navegadores que têm uma validação reduzida dos cabeçalhos HTTP. Consulte esta postagem de blog para obter uma explicação detalhada do problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Perguntas comuns para solução de problemas

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Site parece certo, mas vejo falhas de teste? Por que Application Insights está me alerta?

   * Seu teste tem **solicitações dependentes de Parse** habilitadas? Isso resulta em uma verificação rigorosa de recursos como scripts, imagens etc. Esses tipos de falhas podem não ser perceptíveis em um navegador. Verifique todas as imagens, scripts, folhas de estilos e outros arquivos carregados pela página. Se algum deles falhar, o teste é relatado como falho, mesmo que a página principal html seja carregada sem problemas. Para dessensibilizar o teste para tais falhas de recurso, basta desmarcar as Solicitações Dependentes de Parse da configuração do teste.

   * Para reduzir as chances de ruído de blips de rede transitórias etc., certifique-se de que as repetições para a configuração de falhas de teste sejam verificadas. Você também pode testar em mais locais e gerenciar adequadamente o limite de regra de alerta para evitar problemas específicos de local que estão causando alertas desnecessários.

   * Clique em qualquer um dos pontos vermelhos com a experiência de disponibilidade ou qualquer falha de disponibilidade do Gerenciador de pesquisa para ver os detalhes do motivo pelo relatamos a falha. O resultado do teste, juntamente com a telemetria do lado do servidor correlacionado (se habilitado) deve ajudar a entender por que o teste falhou. Causas comuns dos problemas transitórios são problemas de rede ou conexão.

   * O tempo limite acabou? Podemos cancelar testes após 2 minutos. Se seu ping ou teste de várias etapas demorar mais do que dois minutos, relataremos como falha. Considere dividir o teste em várias partes que podem ser concluídas em durações menores.

   * Todos os locais relataram falha ou apenas alguns deles? Se apenas alguns relataram falhas, pode ser devido a problemas de rede de CDN. Novamente, clicando nos pontos vermelhos deve ajudar a entender por que o local relatou falhas.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Eu não recebi um email quando o alerta disparou ou foi resolvido ou ambos?

Verifique a configuração de alertas clássicos para confirmar que seu e-mail está listado diretamente, ou você está usando uma lista de distribuição está configurada para receber notificações. Se for, em seguida, verifique a configuração da lista de distribuição para confirmar que ela possa receber e-mails externos. Também verifique se o administrador do sistema pode ter quaisquer políticas configuradas que podem causar esse problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Não recebi a notificação de webhook?

Verifique se o aplicativo recebe a notificação de webhook está disponível e processa com êxito as solicitações de webhook. Consulte [isso](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) para obter mais informações.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha de teste intermitente com um erro de violação do protocolo?

O erro ("violação de protocolo... CR deve ser seguido por LF"), indica um problema com o servidor (ou dependências). Isso acontece quando cabeçalhos malformados são definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, alguns cabeçalhos podem não estar usando CRLF para indicar o fim da linha, o que viola a especificação HTTP e, portanto, falha na validação no nível .NET WebRequest. Inspecione a resposta aos cabeçalhos pontuais, que podem estar violando.

> [!NOTE]
> A URL pode não falhar em navegadores que têm uma validação reduzida dos cabeçalhos HTTP. Consulte esta postagem de blog para obter uma explicação detalhada do problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Não vejo nenhuma telemetria relacionada ao lado do servidor para diagnosticar falhas nos testes?*

Se você o Application Insights está configurado para seu aplicativo do lado do servidor, talvez seja porque a [amostragem](../../azure-monitor/app/sampling.md) está em operação. Selecione um resultado de disponibilidade diferente.

### <a name="can-i-call-code-from-my-web-test"></a>Posso chamar o código através do meu teste na Web?

Não. As etapas do teste devem estar no arquivo .webtest. E não é possível chamar outros testes da Web nem usar loops. Porém, há vários plug-ins que podem ser úteis.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Há diferença entre "testes na Web" e "testes de disponibilidade"?

Os dois termos podem ser consultados de modo intercambiável. Testes de disponibilidade é um termo mais genérico que inclui os testes de ping de URL individuais, além dos testes na Web de várias etapas.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Eu gostaria de usar testes de disponibilidade em nosso servidor interno que é executado por trás de um firewall.

   Há duas soluções possíveis:

   * Configure o firewall para permitir as solicitações de entrada dos [endereços IP de nossos agentes de teste da Web](../../azure-monitor/app/ip-addresses.md).
   * Escreva seu próprio código para testar periodicamente o servidor interno. Execute o código como um processo em segundo plano em um servidor de teste por trás do firewall. O processo de teste pode enviar seus resultados para o Application Insights usando a API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK principal. Isso requer que o servidor de teste tenha acesso de saída para o ponto de extremidade de ingestão do Application Insights, mas é um risco de segurança muito menor do que a alternativa de permitir as solicitações de entrada. Os resultados aparecerão nas lâminas de testes web de disponibilidade, embora a experiência seja ligeiramente simplificada do que está disponível para testes criados através do portal. Testes de disponibilidade personalizados também aparecerão como resultados de disponibilidade em Analytics, Search e Metrics.

### <a name="uploading-a-multi-step-web-test-fails"></a>Falha de carregamento de um teste na Web de várias etapas

Alguns motivos para isso acontecer:
   * Há um limite de tamanho de 300 K.
   * Não há suporte para loops.
   * Não há suporte para referências a outros testes na Web.
   * Não há suporte para fontes de dados.

### <a name="my-multi-step-test-doesnt-complete"></a>O teste de várias etapas não foi concluído

Há um limite de 100 solicitações por teste. Além disso, o teste será interrompido se for executado por mais de dois minutos.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Como executar um teste com certificados de cliente

Não há suporte para esse recurso no momento.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta seção só se aplica aos alertas clássicos e ajudará você a otimizar suas notificações de alerta para fazer com que somente os destinatários desejados recebam notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e a nova experiência de alertas, confira o [artigo sobre visão geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alertas clássicos.

* Para alertas de falhas de X entre Y locais, a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de administrador/coadministrador.  Basicamente _todos_ os administradores da _assinatura_ receberão notificações.

* Para obter alertas sobre métricas de disponibilidade, a opção caixa de seleção **em massa/grupo,** se habilitada, envia aos usuários com funções de proprietário, contribuinte ou leitor na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura do recurso do Application Insights fazem parte do escopo e receberão notificações. 

> [!NOTE]
> Se você estiver usando a opção de caixa de seleção **em massa/grupo** e desabilitá-la, não poderá reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real caso precise notificar usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários que tenham qualquer uma das funções de proprietário/colaborador/leitor (não combinadas como uma única opção).

## <a name="next-steps"></a>Próximas etapas

* [Testes web em várias etapas](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)
