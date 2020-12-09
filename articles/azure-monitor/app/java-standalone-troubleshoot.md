---
title: Solução de problemas-Azure Monitor Application Insights Java
description: Solução de problemas Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855579"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Solução de problemas Azure Monitor Application Insights Java

Neste artigo, abordamos alguns dos problemas comuns que um usuário pode enfrentar ao instrumentar o aplicativo Java usando o agente Java, juntamente com as etapas para resolver esses problemas.

## <a name="self-diagnostic-log-file"></a>Arquivo de log de autodiagnóstico

Por padrão, Application Insights Java 3,0 produzirá um arquivo de log chamado `applicationinsights.log` no mesmo diretório em que o `applicationinsights-agent-3.0.0.jar` arquivo está localizado.

Esse arquivo de log é o primeiro lugar para verificar se há dicas para quaisquer problemas que você possa estar enfrentando.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Atualizar do SDK do Application Insights Java 2. x

Consulte [Atualizar do SDK 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Atualizar da versão prévia do 3,0

Se estiver atualizando da versão prévia do 3,0, revise todas as [Opções de configuração](./java-standalone-config.md) com cuidado, pois a estrutura JSON foi completamente alterada no lançamento de 3,0 ga.

Essas alterações incluem:

1.  O próprio nome do arquivo de configuração foi alterado de `ApplicationInsights.json` para `applicationinsights.json` .
2.  O `instrumentationSettings` nó não está mais presente. Todo o conteúdo em `instrumentationSettings` é movido para o nível raiz. 
3.  Nós de configuração `sampling` como `jmxMetrics` , `instrumentation` e `heartbeat` são movidos de `preview` para o nível raiz.

## <a name="ssl-certificate-issues"></a>Problemas de certificado SSL

Se você estiver usando o repositório de chaves Java padrão, ele já terá todos os certificados raiz da autoridade de certificação e você não precisará importar nenhum certificado SSL adicional.

Se você estiver usando um repositório de chaves Java personalizado, talvez seja necessário importar os Application Insights de certificados SSL do ponto de extremidade para ele.

### <a name="some-key-terminology"></a>Algumas terminologias-chave:
*Keystore* é um repositório de certificados, chaves públicas e privadas. Normalmente, as distribuições do JDK têm um executável para gerenciá-las – `keytool` .

O exemplo a seguir é um comando simples para importar um certificado SSL para o keystore:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Etapas para baixar e adicionar o certificado SSL:

1.  Abra seu navegador favorito e vá para a `IngestionEndpoint` URL presente na cadeia de conexão usada para instrumentar seu aplicativo, conforme mostrado abaixo

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights cadeia de conexão":::

2.  Clique no ícone ' Exibir informações do site ' (bloquear) no navegador e clique na opção ' certificado ' como mostrado abaixo

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Captura de certificado SSL":::

3.  Vá para a guia detalhes e clique em copiar para arquivo.
4.  Clique no botão Avançar e selecione "base-64 codificado X. 509 (. CER) "e selecione avançar.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="ExportWizard de certificado SSL":::

5.  Especifique o arquivo no qual você deseja salvar o certificado SSL. Finalmente, clique em avançar e em concluir. Você deve ver a mensagem "a exportação foi bem-sucedida".
6.  Quando você tiver o certificado seu tempo para importar o certificado em um repositório de chaves Java. Use o [comando](#some-key-terminology) acima para importar certificados.

> [!WARNING]
> Você precisará repetir essas etapas para obter o novo certificado antes que o certificado atual expire. Você pode encontrar as informações de expiração na guia "detalhes" do pop-up de certificado, conforme mostrado abaixo

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Detalhes do certificado SSL":::
