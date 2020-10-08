---
title: Solucionar problemas de análise de vídeo ao vivo no IoT Edge-Azure
description: Este artigo aborda as etapas de solução de problemas para análise de vídeo ao vivo em IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823189"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Solucionar problemas de análise de vídeo ao vivo no IoT Edge

Este artigo aborda as etapas de solução de problemas do LVA (análise de vídeo ao vivo) no Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

### <a name="diagnostics"></a>Diagnósticos

Como parte de sua implantação de análise de vídeo ao vivo, você configura recursos do Azure, como o Hub IoT e dispositivos IoT Edge. Como uma primeira etapa para diagnosticar problemas, sempre verifique se o dispositivo de borda está configurado corretamente seguindo estas instruções:

1. [Execute o `check` comando](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Verifique sua versão do IOT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Verifique o status do Gerenciador de segurança IOT Edge e seus logs](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Exiba as mensagens que estão passando pelo hub de IOT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Reinicie os contêineres](../../iot-edge/troubleshoot.md#restart-containers).
1. [Verifique as regras de configuração do firewall e da porta](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemas de pré-implantação

Se a infraestrutura de borda estiver correta, você poderá procurar problemas com o arquivo de manifesto de implantação. Para implantar o módulo análise de vídeo ao vivo no IoT Edge no dispositivo IoT Edge junto com qualquer outro módulo IoT, use um manifesto de implantação que contenha o Hub de IoT Edge, o agente do IoT Edge e outros módulos e suas propriedades. Se o código JSON não estiver bem formado, você poderá receber o seguinte erro: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Falha ao analisar JSON do arquivo: ' <deployment manifest.json> ' para o argumento ' content ' com a exceção: "dados extras: linha 1, coluna de 101 (char 5325)"

Se você encontrar esse erro, recomendamos que verifique o JSON quanto a falta de colchetes ou outros problemas com a estrutura do arquivo. Para validar a estrutura do arquivo, você pode usar um cliente, como [bloco de notas + + com plug-in do visualizador JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou uma ferramenta online, como o [formatador JSON & o validador](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Durante a implantação: diagnosticar com métodos do grafo de mídia direto 

Depois que a análise de vídeo ao vivo no módulo IoT Edge for implantada corretamente no dispositivo IoT Edge, você poderá criar e executar o grafo de mídia invocando [métodos diretos](direct-methods.md). Você pode usar o portal do Azure para executar um diagnóstico do grafo de mídia por meio de métodos diretos:

1. No portal do Azure, vá para o Hub IoT que está conectado ao seu dispositivo de IoT Edge.

1. Procure **Gerenciamento de dispositivo automático**e, em seguida, selecione **IOT Edge**.  

1. Na lista de dispositivos de borda, selecione o dispositivo que você deseja diagnosticar.  
         
    ![Captura de tela da portal do Azure exibir uma lista de dispositivos de borda](./media/troubleshoot-how-to/lva-sample-device.png)

1. Verifique se o código de resposta é *200-OK*. Outros códigos de resposta para o [tempo de execução de IOT Edge](../../iot-edge/iot-edge-runtime.md) incluem:
    * 400 - A configuração de implantação está malformada ou inválida.
    * 417-o dispositivo não tem um conjunto de configuração de implantação.
    * 412 - A versão do esquema na configuração de implantação é inválida.
    * 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
    * 500 – ocorreu um erro no runtime do IoT Edge.

1. Se você receber um código de status 501, verifique se o nome do método direto é preciso. Se o nome do método e a carga da solicitação forem precisos, você deverá obter resultados juntamente com o código de êxito = 200. Se a carga de solicitação for imprecisa, você receberá um status = 400 e uma carga de resposta que indica o código de erro e a mensagem que deve ajudar no diagnóstico do problema com a chamada de método direta.
    * A verificação nas propriedades relatadas e desejadas pode ajudá-lo a entender se as propriedades do módulo foram sincronizadas com a implantação. Se eles não tiverem sido, você poderá reiniciar o dispositivo IoT Edge. 
    * Use o guia de [métodos diretos](direct-methods.md) para chamar alguns métodos, especialmente os simples, como GraphTopologyList. O guia também especifica as cargas esperadas de solicitação e resposta e os códigos de erro. Depois que os métodos diretos simples forem bem-sucedidos, você poderá ter certeza de que o módulo de IoT Edge de análise de vídeo ao vivo está funcionalmente OK.
        
       ![Captura de tela do painel "método direto" para o módulo IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Se o **especificado na implantação** e **relatado por colunas de dispositivo** indicar *Sim*, você poderá invocar métodos diretos na análise de vídeo ao vivo no módulo IOT Edge. Selecione o módulo para ir para uma página em que você pode verificar as propriedades desejadas e relatadas e invocar métodos diretos. Lembre-se do seguinte: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Pós-implantação: diagnosticar logs para problemas durante a execução 

Os logs de contêiner para seu módulo de IoT Edge devem conter informações de diagnóstico para ajudar a depurar seus problemas durante o tempo de execução do módulo. Você pode [verificar os logs de contêiner para problemas](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) e diagnosticar o problema automaticamente. 

Se você executou todas as verificações anteriores e ainda estiver encontrando problemas, reúna os logs do dispositivo IoT Edge [com o `support bundle` comando](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) para análise adicional da equipe do Azure. Você pode [entrar em contato conosco](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para obter suporte e enviar os logs coletados.

## <a name="common-error-resolutions"></a>Resoluções de erros comuns

A análise de vídeo ao vivo é implantada como um módulo IoT Edge no dispositivo IoT Edge e funciona de forma colaborativa com os módulos agente e Hub do IoT Edge. Alguns dos erros comuns que você encontrará com a implantação de análise de vídeo ao vivo são causados por problemas com a infraestrutura de IoT subjacente. Os erros incluem:

* [O agente de IOT Edge é interrompido após cerca de um minuto](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [O agente de IOT Edge não pode acessar a imagem de um módulo (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [O módulo agente de IOT Edge relata "arquivo de configuração vazio" e nenhum módulo inicia no dispositivo](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Falha ao iniciar o Hub de IOT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [O daemon de segurança IOT Edge falha com um nome de host inválido](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [A análise de vídeo ao vivo ou qualquer outro módulo de IOT Edge personalizado falha ao enviar uma mensagem para o Hub do Edge com erro 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [O módulo IOT Edge é implantado com êxito e desaparece do dispositivo](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Problemas de script de instalação do Edge

Como parte de nossa documentação, fornecemos um [script de instalação](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implantar recursos de borda e de nuvem e começar a usar a borda de análise de vídeo ao vivo. Esta seção apresenta alguns erros de script que você pode encontrar, juntamente com soluções para depurá-los.

Problema: o script é executado, parcialmente criando alguns recursos, mas ele falha com a seguinte mensagem:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Para resolver este problema:

1. Execute o comando a seguir:

    ```
    az --version
    ```
1. Verifique se você tem as seguintes extensões instaladas. A partir da publicação deste artigo, as extensões e suas versões são:

    | Extensão | Versão |
    |---|---|
    |azure-cli   |      2.5.1|
    |comando-modules-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Se você tiver uma extensão instalada cuja versão é anterior ao número de versão listado aqui, atualize a extensão usando o seguinte comando:

    ```
    az extension update --name <Extension name>
    ```

    Por exemplo, você pode executar `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Problemas de aplicativo de exemplo

Como parte de nossa versão, fornecemos um código de exemplo .NET para ajudar a fazer com que nossa comunidade de desenvolvedores seja inicializada. Esta seção apresenta alguns erros que você pode encontrar ao executar o código de exemplo, juntamente com soluções para depurá-los.

Problema: Program.cs falha com o seguinte erro na invocação do método direto:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Verifique se você tem as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) instaladas em seu ambiente de Visual Studio Code e se você configurou a conexão com o Hub IOT. Para fazer isso, selecione CTRL + SHIFT + P e escolha **selecionar método de Hub IOT**.

1. Verifique se você pode invocar um método direto no módulo de IoT Edge por meio de Visual Studio Code. Por exemplo, chame GraphTopologyList com a seguinte carga { &nbsp; " @apiVersion ": "1,0"}. Você deve receber a seguinte resposta: 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Captura de tela da resposta no Visual Studio Code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Se a solução anterior falhar, tente o seguinte:

    a. Vá para o prompt de comando em seu dispositivo IoT Edge e execute o seguinte comando:
    
      ```
      sudo systemctl restart iotedge
      ```

      Esse comando reinicia o dispositivo IoT Edge e todos os módulos. Aguarde alguns minutos e, em seguida, antes de tentar usar o método direto novamente, confirme se os módulos estão em execução executando o seguinte comando:

      ```
      sudo iotedge list
      ```

    b. Se a abordagem anterior também falhar, tente reinicializar sua máquina virtual ou computador.

    c. Se todas as abordagens falharem, execute o seguinte comando para obter um arquivo compactado com todos os [logs relevantes](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)e anexe-o a um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Se você receber um código de resposta de erro *400* , verifique se a carga de invocação do método está bem formada, de acordo com o guia de [métodos diretos](direct-methods.md) .
1. Se você receber um código de status *200* , ele indica que o Hub está funcionando bem e que a implantação do módulo está correta e responsiva. 

1. Verifique se a configuração do aplicativo é precisa. A configuração do aplicativo consiste nos campos a seguir na *appsettings.jsno* arquivo. Verifique se a DeviceID e a ModuleID são precisas. Uma maneira fácil de verificar é ir para a seção extensão do Hub IoT do Azure em Visual Studio Code. Os valores na seção *appsettings.jsno* arquivo e no Hub IOT devem corresponder.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. Na *appsettings.jsno* arquivo, verifique se você forneceu a cadeia de conexão do Hub IOT e *não* a cadeia de conexão do dispositivo do Hub IOT, pois os formatos da [cadeia de conexão são diferentes](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Análise de vídeo ao vivo trabalhando com módulos externos

A análise de vídeo ao vivo por meio do processador de extensão HTTP pode estender o grafo de mídia para enviar e receber dados de outros módulos IoT Edge por HTTP usando REST. Como um [exemplo específico](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension), o grafo de mídia pode enviar quadros de vídeo como imagens para um módulo de inferência externa, como Yolo v3 e receber resultados de análise baseada em JSON. Nessa topologia, o destino para os eventos é principalmente o Hub IoT. Em situações em que você não vê os eventos de inferência no Hub, verifique o seguinte:

* Verifique se o Hub no qual o grafo de mídia está sendo publicado e o Hub que você está examinando são os mesmos. À medida que você cria várias implantações, pode acabar com vários hubs e verificar erroneamente o Hub errado em busca de eventos.
* Em Visual Studio Code, verifique se o módulo externo está implantado e em execução. Na imagem de exemplo aqui, rtspsim e CV são IoT Edge módulos em execução externa ao módulo lvaEdge.

    ![Captura de tela que exibe o status de execução dos módulos no Hub IoT do Azure.](./media/troubleshoot-how-to/iot-hub.png)

* Verifique se você está enviando eventos para o ponto de extremidade correto da URL. O contêiner external AI expõe uma URL e uma porta por meio da qual recebe e retorna os dados de solicitações POST. Essa URL é especificada como uma `endpoint: url` propriedade para o processador de extensão http. Como visto na [URL de topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), o ponto de extremidade é definido como o parâmetro de URL inferência. Certifique-se de que o valor padrão para o parâmetro ou o valor passado seja preciso. Você pode testar para ver se ele está funcionando usando a URL do cliente (ondulação).  

    Por exemplo, aqui está um contêiner Yolo v3 que está sendo executado no computador local com um endereço IP de 172.17.0.3. Use o Docker inspecionar para localizar o endereço IP.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Resultado retornado:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Se você estiver executando uma ou mais instâncias de um grafo que usa o processador de extensão HTTP, deverá ter um filtro de taxa de quadros antes de cada processador de extensão HTTP gerenciar a taxa de quadros por segundo (FPS) do feed de vídeo. 

   Em determinadas situações, em que a CPU ou a memória do computador de borda é altamente utilizada, você pode perder determinados eventos de inferência. Para resolver esse problema, defina um valor baixo para a propriedade maximumFps no filtro de taxa de quadros. Você pode defini-lo como 0,5 ("maximumFps": 0,5) em cada instância do grafo e, em seguida, executar novamente a instância para verificar se há eventos de inferência no Hub.

   Como alternativa, você pode obter uma máquina de borda mais potente com CPU e memória maiores.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Vários métodos diretos em paralelo – falha de tempo limite 

A análise de vídeo ao vivo no IoT Edge fornece um modelo de programação baseado em método direto que permite que você configure várias topologias e várias instâncias de grafo. Como parte da configuração de topologia e de grafo, você invoca várias chamadas de método direto no módulo IoT Edge. Se você invocar essas várias chamadas de método em paralelo, especialmente aquelas que iniciam e param os grafos, você pode enfrentar uma falha de tempo limite como a seguinte: 

O método de inicialização de assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync gerou uma exceção. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

É recomendável que você *não* Chame métodos diretos em paralelo. Chamá-los em sequência (ou seja, fazer uma chamada de método direto somente após a conclusão da anterior).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Coletar logs para enviar um tíquete de suporte

Quando as etapas de solução de problemas autoguiadas não resolvem o problema, acesse o portal do Azure e [abra um tíquete de suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Os logs podem conter informações de identificação pessoal (PII), como seu endereço IP. Todas as cópias locais dos logs serão excluídas assim que terminarmos de examiná-las e fechar o tíquete de suporte.  

Para coletar os logs relevantes que devem ser adicionados ao tíquete, siga as instruções nas próximas seções. Você pode carregar os arquivos de log no painel de **detalhes** da solicitação de suporte.

### <a name="use-the-support-bundle-command"></a>Usar o comando support-Bundle

Quando você precisa coletar logs de um dispositivo IoT Edge, a maneira mais fácil é usar o `support-bundle` comando. Este comando coleta:

- Logs de módulo
- IoT Edge o Gerenciador de segurança e os logs do mecanismo de contêiner
- Saída JSON de verificação de Iotedge
- Informações de depuração úteis

1. Execute o `support-bundle` comando com o sinalizador *--Since* para especificar quanto tempo você deseja que os seus logs cubram. Por exemplo, o segundo receberá logs das últimas duas horas. Você pode alterar o valor desse sinalizador para incluir logs para períodos diferentes.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Esse comando cria um arquivo chamado *support_bundle.zip* no diretório em que você executou o comando. 
   
1. Anexe o arquivo de *support_bundle.zip* ao tíquete de suporte.

### <a name="live-video-analytics-debug-logs"></a>Logs de depuração da análise de vídeo ao vivo

Para configurar a análise de vídeo ao vivo no módulo IoT Edge para gerar logs de depuração, faça o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com)e vá para o Hub IOT.
1. No painel esquerdo, selecione **IOT Edge**.
1. Na lista de dispositivos, selecione a ID do dispositivo de destino.
1. Na parte superior do painel, selecione **definir módulos**.

   ![Captura de tela do botão "definir módulos" no portal do Azure.](media/troubleshoot-how-to/set-modules.png)

1. Na seção **módulos IOT Edge** , procure e selecione **lvaEdge**.
1. Selecione **Opções de criação de contêiner**.
1. Na seção **bindings** , adicione o seguinte comando:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Esse comando associa as pastas de logs entre o dispositivo de borda e o contêiner. Se você quiser coletar os logs em um local diferente, use o comando a seguir, substituindo **$LOG _LOCATION_ON_EDGE_DEVICE** pelo local que você deseja usar: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selecione **Atualizar**.
1. Selecione **Examinar + criar**. Uma mensagem de validação bem-sucedida é postada em uma faixa verde.
1. Selecione **Criar**.
1. Atualize a **identidade do módulo** "atualizar" para apontar para o parâmetro DebugLogsDirectory, que aponta para o diretório no qual os logs são coletados:

    a. Na tabela **modules** , selecione **lvaEdge**.  
    b. Na parte superior do painel, selecione **Identity do módulo**10. Um painel editável é aberto.  
    c. Em **chave desejada**, adicione o seguinte par de chave/valor:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Esse comando associa as pastas de logs entre o dispositivo de borda e o contêiner. Se você quiser coletar os logs em um local diferente no dispositivo:
    > 1. Crie uma associação para o local do log de depuração na seção de **associações** , substituindo o **_LOG_LOCATION_ON_EDGE_DEVICE de $DEBUG** e **$debug _LOG_LOCATION** pelo local desejado: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Use o comando a seguir, substituindo **$DEBUG _LOG_LOCATION** pelo local usado na etapa anterior:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Clique em **Salvar**.

1. Reproduza o problema.
1. Conecte-se à máquina virtual na página do **Hub IOT** no Portal.
1. Compacte todos os arquivos na pasta *debugLogs*

   > [!NOTE]
   > Esses arquivos de log não são destinados para diagnóstico automático. Eles destinam-se à equipe de engenharia do Azure a analisar seus problemas.

   a. No comando a seguir, certifique-se de substituir **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** pelo local dos logs de depuração no dispositivo de borda que você configurou anteriormente.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Anexe o arquivo de *debugLogs.zip* ao tíquete de suporte.

1. Você pode parar a coleta de log definindo o valor em **Identity do módulo** "/" para *NULL*. Volte para a página de **identidade do módulo** e atualize o seguinte parâmetro como:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução com origem na nuvem](event-based-video-recording-tutorial.md)
