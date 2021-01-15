---
title: Solução de problemas Azure Monitor Application Insights para Java
description: Saiba como solucionar problemas do agente Java para Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2876abd3749c9e56cef462e41b8268135f82cd12
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232209"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guia de solução de problemas: Azure Monitor Application Insights para Java

Neste artigo, abordamos alguns dos problemas comuns que você pode enfrentar ao instrumentar um aplicativo Java usando o agente Java para Application Insights. Também abordaremos as etapas para resolver esses problemas. Application Insights é um recurso do serviço de plataforma Azure Monitor.

## <a name="check-the-self-diagnostic-log-file"></a>Verificar o arquivo de log de autodiagnóstico

Por padrão, o agente do Java 3,0 para Application Insights produz um arquivo de log chamado `applicationinsights.log` no mesmo diretório que contém o `applicationinsights-agent-3.0.1.jar` arquivo.

Esse arquivo de log é o primeiro lugar para verificar se há dicas para quaisquer problemas que você esteja enfrentando.

## <a name="jvm-fails-to-start"></a>Falha na inicialização da JVM

Se a JVM não iniciar com "erro ao abrir o arquivo zip ou o manifesto JAR ausente", tente baixar novamente o arquivo JAR do agente porque ele pode ter sido corrompido durante a transferência de arquivos.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Atualizar do SDK Application Insights Java 2. x

Se você já estiver usando o SDK Application Insights Java 2. x em seu aplicativo, você poderá continuar a usá-lo. O agente do Java 3,0 irá detectá-lo. Para obter mais informações, consulte [Atualizar do SDK do Java 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Atualizar de Application Insights visualização do Java 3,0

Se você estiver atualizando do agente de visualização do Java 3,0, revise todas as [Opções de configuração](./java-standalone-config.md) com cuidado. A estrutura JSON foi completamente alterada na versão GA (disponibilidade geral) de 3,0.

Essas alterações incluem:

-  O nome do arquivo de configuração foi alterado de `ApplicationInsights.json` para `applicationinsights.json` .
-  O `instrumentationSettings` nó não está mais presente. Todo o conteúdo em `instrumentationSettings` é movido para o nível raiz. 
-  Nós de configuração como `sampling` , `jmxMetrics` , `instrumentation` e `heartbeat` são movidos de `preview` para o nível raiz.

## <a name="some-logging-is-not-auto-collected"></a>Algum registro em log não é coletado automaticamente

O registro em log só será capturado se ele atender primeiro ao limite configurado da estrutura de registro em log e o segundo também atender ao limite configurado Application Insights.

A melhor maneira de saber se uma determinada instrução de log atende ao limite configurado da estrutura de registro em log é confirmar que ela está aparecendo no log normal do aplicativo (por exemplo, arquivo ou console).

Consulte a [configuração de log coletada automaticamente](./java-standalone-config.md#auto-collected-logging) para obter mais detalhes.

## <a name="import-ssl-certificates"></a>Importar certificados SSL

Se você estiver usando o repositório de chaves Java padrão, ele já terá todos os certificados raiz da autoridade de certificação. Você não deve precisar importar mais certificados SSL.

Se você estiver usando um repositório de chaves Java personalizado, talvez seja necessário importar os Application Insights de certificados SSL do ponto de extremidade para ele.

### <a name="key-terminology"></a>Terminologia principal
Um *keystore* é um repositório de certificados, chaves públicas e chaves privadas. Normalmente, as distribuições do Java Development Kit têm um executável para gerenciá-las: `keytool` .

O exemplo a seguir é um comando simples para importar um certificado SSL para o keystore:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Etapas para baixar e adicionar um certificado SSL

1.  Abra seu navegador favorito e vá para a `IngestionEndpoint` URL presente na cadeia de conexão que é usada para instrumentar seu aplicativo.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Captura de tela que mostra uma cadeia de conexão Application Insights.":::

2.  Selecione o ícone **Exibir informações do site** (bloquear) no navegador e, em seguida, selecione a opção **certificado** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Captura de tela da opção de certificado nas informações do site.":::

3.  Vá para a guia **detalhes** e selecione **copiar para arquivo**.
4.  Selecione o botão **Avançar** , selecione **X. 509 codificado em base-64 (. CER)** e, em seguida, selecione **Avançar** novamente.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Captura de tela do assistente para exportação de certificados, com um formato selecionado.":::

5.  Especifique o arquivo no qual você deseja salvar o certificado SSL. Em seguida, selecione **próxima**  >  **conclusão**. Você deve ver uma mensagem "a exportação foi bem-sucedida".
6.  Depois de ter o certificado, é hora de importar o certificado para um repositório de chaves Java. Use o [comando anterior](#key-terminology) para importar certificados.

> [!WARNING]
> Você precisará repetir essas etapas para obter o novo certificado antes que o certificado atual expire. Você pode encontrar as informações de expiração na guia **detalhes** da caixa de diálogo **certificado** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Captura de tela que mostra os detalhes do certificado SSL.":::
