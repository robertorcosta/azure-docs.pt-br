---
title: Solucionar problemas de análise de vídeo ao vivo no IoT Edge-Azure
description: Este artigo aborda as etapas de solução de problemas para análise de vídeo ao vivo em IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045562"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Solucionar problemas de análise de vídeo ao vivo no IoT Edge

Este artigo aborda as etapas de solução de problemas para análise de vídeo ao vivo em IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

### <a name="diagnostics"></a>Diagnósticos

Como parte da implantação da análise de vídeo ao vivo, você configurará recursos do Azure, como o Hub IoT e o dispositivo IoT Edge. Como uma primeira etapa para diagnosticar problemas sempre garante que a borda seja corretamente configurada seguindo estas instruções:

1. [Executar o comando ' check '](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Verifique sua versão do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Verificar o status do IoT Edge Security Manager e seus logs](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Exibir as mensagens que passam pelo hub de IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Reinicie os contêineres](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Verifique as regras de configuração do firewall e da porta](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Problemas de pré-implantação

Se a infraestrutura de borda estiver correta, você poderá procurar problemas com o arquivo de manifesto de implantação. Para implantar a análise de vídeo ao vivo no módulo IoT Edge no dispositivo de borda junto com qualquer outro módulo IoT, você usará um manifesto de implantação que contém o Hub do Edge, o agente do Edge e outros módulos com suas propriedades. Se o JSON não estiver bem formado, você poderá obter um erro como mostrado abaixo: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Falha ao analisar JSON do arquivo: ' <deployment manifest.json> ' para o argumento ' content ' com a exceção: "dados extras: linha 1, coluna de 101 (char 5325)"

Se você encontrar esse erro, é recomendável verificar se há colchetes ausentes no arquivo JSON ou outros problemas com a estrutura do arquivo. Você pode usar um cliente como o [bloco de notas + + com o plug-in do visualizador JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou uma ferramenta online, como https://jsonformatter.curiousconcept.com/ para validar a estrutura do arquivo.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Implantação – diagnosticar com métodos do grafo de mídia direto 

Depois que a análise de vídeo ao vivo no módulo IoT Edge for implantada corretamente no dispositivo de borda, você poderá criar e executar o grafo de mídia invocando [métodos diretos](direct-methods.md). Você pode usar o portal para executar o diagnóstico do grafo de mídia por meio de métodos diretos:

1. Por meio do portal, vá para o Hub IoT que está conectado ao seu dispositivo de borda.
    1. Uma vez na folha do Hub IoT, procure o >de gerenciamento de dispositivo automático IoT Edge.
    1. Clicar em IoT Edge deve abrir uma lista de dispositivos de borda. Escolha o dispositivo que você deseja diagnosticar.
         
        ![Dispositivos de borda](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Verifique se o código de resposta é 200-OK. Há vários outros códigos de resposta para o [tempo de execução de IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , como:
        1. 400 - A configuração de implantação está malformada ou inválida.
        1. 417-o dispositivo não tem um conjunto de configuração de implantação.
        1. 412 - A versão do esquema na configuração de implantação é inválida.
        1. 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
        1. 500 – ocorreu um erro no runtime do IoT Edge.
    1. Clicar no dispositivo também deve mostrar a lista de módulos de IoT Edge esperados que foram implantados e seu status
    1. Se as colunas "especificado na implantação" e "relatadas pelo dispositivo" indicarem "Sim", você poderá invocar métodos diretos na análise de vídeo ao vivo no módulo IoT Edge. Clique no módulo para levá-lo a uma tela em que você pode verificar as propriedades desejadas e relatadas e pode invocar métodos diretos. 
        1. A verificação nas propriedades relatadas e desejadas ajuda você a entender se as propriedades do módulo foram sincronizadas com a implantação. Se não tiverem, você poderá reiniciar sua borda 
        1. Use o guia de [métodos diretos](direct-methods.md) para chamar alguns métodos, especialmente os simples como GraphTopologyList. O guia também especifica as cargas esperadas de solicitação e resposta e os códigos de erro. Depois que os métodos diretos simples forem bem-sucedidos, você poderá ter certeza de que o módulo de borda de análise de vídeo ao vivo está funcionalmente Ok.
        
        ![Método direto](./media/troubleshoot-how-to/direct-method.png) 
1. Se você receber um código de status 501, verifique se o nome do método direto é preciso. Se o nome do método e a carga da solicitação forem precisos, você deverá obter os resultados junto com o código de êxito = 200. Se a carga de solicitação for imprecisa, você receberá um status = 400 e uma carga de resposta que indica o código de erro e a mensagem que deve ajudar no diagnóstico do problema com a chamada de método direta. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Pós-implantação – diagnostique logs para problemas durante a execução 

Os logs de contêiner para nosso módulo de borda devem ter diagnóstico<!--<todo:add link to diagnostics doc>--> informações que devem ajudar a depurar seus problemas durante o tempo de execução do módulo. Você pode [verificar os logs de contêiner para problemas](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) e autodiagnosticar, no entanto, se todas as verificações acima tiverem sido executadas e você ainda estiver enfrentando problemas, colete logs do dispositivo IOT Edge [com o comando ' pacote de suporte '](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) que pode ser analisado ainda mais pela equipe do Azure. Você pode [entrar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) em contato conosco para obter suporte e enviar os logs coletados.

## <a name="common-error-resolutions"></a>Resoluções de erros comuns

A análise de vídeo ao vivo é implantada como um módulo IoT Edge no dispositivo de borda e funciona de forma colaborativa com os módulos agente e Hub de IoT Edge. Alguns dos erros comuns que você enfrentará com a implantação de análise de vídeo ao vivo serão devidos aos problemas com a infraestrutura de IoT subjacente. Alguns erros comuns que IoT Edge o Agent e o Hub podem ter são:

1. [IOT Edge agente é interrompido após cerca de um minuto](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [O agente de IOT Edge não pode acessar a imagem de um módulo (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. O [módulo do agente do Edge relata ' arquivo de configuração vazio ' e nenhum módulo inicia no dispositivo](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [Falha ao iniciar o Hub de IOT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IOT Edge daemon de segurança falha com um nome de host inválido](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [A análise de vídeo ao vivo ou qualquer outro módulo de IOT Edge personalizado falha ao enviar uma mensagem para o Hub do Edge com erro 404](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [IOT Edge módulo é implantado com êxito e, em seguida, desaparece do dispositivo](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Problemas de script de configuração de borda

Como parte da nossa documentação, fornecemos um script de [configuração](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implantar recursos de borda e de nuvem para começar a usar a borda de análise de vídeo ao vivo. Nesta seção, capturamos erros que você pode enfrentar com o script e como depurá-los.

O script é executado parcialmente criando alguns recursos, mas falha com a seguinte mensagem:

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
1. Verifique se você tem as seguintes extensões instaladas. No momento da elaboração deste guia, a versão para extensões era a seguinte:

    | Extensão | Versão |
    |---|---|
    |azure-cli   |      2.5.1|
    |comando-modules-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |Extensões:    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Se qualquer uma das extensões for mais antiga que os números de versão acima, atualize a extensão para a versão mais recente usando o comando:

    ```
    az extension update --name <Extension name>
    ```

    Por exemplo, `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Problemas de aplicativo de exemplo

Como parte de nossa versão, fornecemos um código de exemplo .NET para que a nossa comunidade de desenvolvedores seja inicializada. Nesta seção, capturamos erros que você pode enfrentar ao executar o código de exemplo e como depurar esses erros.

1. Program.cs falha com o seguinte erro na invocação do método direto:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Verifique se você tem as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) instaladas no ambiente do vs Code e a conexão com a configuração do Hub IOT. (Ctrl + Shift + P e selecione o método do Hub IoT para se conectar à sua assinatura e ao Hub IoT)
1. Verifique se você pode invocar um método direto no módulo de borda por meio de VS Code (por exemplo, chame GraphToplogyList com a seguinte carga {" @apiVersion ": "1,0"}) e você deverá obter a resposta a seguir de volta. 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Se a falha acima falhar, tente o seguinte:
    1. Vá para o prompt de comando no seu dispositivo de borda e digite.
    
    ```
    sudo systemctl restart iotedge
    ```

    Isso reiniciará o dispositivo de borda e todos os módulos. Aguarde alguns minutos e execute o seguinte para confirmar se os módulos estão em execução, antes de tentar usar o DirectMethod novamente.

    ```
    sudo iotedge list
    ```
    1. Se acima também falhar, tente reinicializar sua VM ou computador.
    1. Se tudo falhar, execute o seguinte para obter um arquivo ZIP com todos os [logs relevantes](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) a serem anexados ao [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Se você receber um código de resposta de erro 400, verifique se a carga de invocação do método está bem formada de acordo com o guia de [método direto](direct-methods.md) .
1. Se você receber o código de status 200, ele indica que o Hub está funcionando bem e que a implantação do módulo está correta e responsiva. A próxima etapa é verificar se as configurações do aplicativo são precisas. A configuração do aplicativo consiste nos campos a seguir na appsettings.jsno arquivo. Verifique se DeviceID e ModuleID são precisos. Uma maneira fácil de verificar isso é por meio da seção extensão do Hub IoT do Azure no VSCode. Os valores no appsettings.jsno arquivo e na seção do Hub IoT devem corresponder.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![HUB IOT](./media/troubleshoot-how-to/iot-hub.png)

1. Por fim, verifique se, dentro de appsettings.js, você forneceu a cadeia de conexão do Hub IoT e não a cadeia de conexão do dispositivo do Hub IoT, pois seus [formatos](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) são diferentes.

### <a name="live-video-analytics-working-with-external-modules"></a>Análise de vídeo ao vivo trabalhando com módulos externos

A análise de vídeo ao vivo por meio do processador de extensão HTTP pode estender o grafo de mídia para enviar e receber dados de outros módulos de IoT Edge sobre HTTP usando REST.  Como um [exemplo específico](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) , o grafo de mídia pode enviar quadros de vídeo como imagens para um módulo de inferência externa como Yolo v3 e receber resultados de análise com base em JSON. Nessa topologia, o destino final para os eventos é principalmente o Hub IoT. Em situações em que você não vê os eventos de inferência no Hub, verifique o seguinte:

1. Verifique se o Hub no qual o grafo de mídia está publicando o vs. aquele que você está examinando é o mesmo. Às vezes, à medida que você cria várias implantações, você acabará com vários hubs e poderá verificar por engano o Hub errado em busca de eventos.
1. Verifique por meio de VSCode se o módulo externo está implantado e em execução. Na imagem de exemplo aqui, rtspsim e CV são IoT Edge módulos executando o módulo externo ao lvaEdge.

    ![HUB IOT](./media/troubleshoot-how-to/iot-hub.png)
1. Verifique se você está enviando eventos para o ponto de extremidade correto da URL. O contêiner external AI expõe uma URL e uma porta sobre a qual recebe e retorna os dados de solicitações POST. Essa URL é especificada como uma propriedade de ponto de extremidade: URL para o processador de extensão http. Como visto na [URL de topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) é definido como o parâmetro de URL inferência. Certifique-se de que o valor padrão do parâmetro ( http://yolov3/score) ou o valor transmitido seja preciso e teste se ele está funcionando usando a ondulação.  
    1. Por exemplo, o contêiner Yolo v3 em execução no computador local e o endereço IP do contêiner é 172.17.0.3 (use o Docker inspecionar para localizar o endereço IP).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Resultado retornado:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Se você estiver executando uma ou várias instâncias de um grafo aproveitando o processador de extensão http, deverá ter um filtro de taxa de quadros antes de cada processador de extensão http gerenciar os quadros por segundo (FPS) do feed de vídeo. Em determinadas situações em que a CPU/memória da máquina de borda são altamente utilizadas, você pode perder determinados eventos de inferência. Para resolver isso, defina um valor baixo para a propriedade maximumFps no filtro de taxa de quadros. Você pode defini-lo como 0,5 ("maximumFps": 0,5) em cada instância do grafo e executar novamente para verificar se há eventos de inferência no Hub.
    1. Como alternativa, você também pode obter uma máquina de borda mais potente com CPU e memória maiores.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Vários métodos diretos em paralelo – falha de tempo limite 

A análise de vídeo ao vivo no IoT Edge fornece um modelo de programação baseado em método direto que permite a configuração de várias topologias e várias instâncias de grafo. Como parte da configuração de topologia e de grafo, você invocará várias chamadas de método diretas no módulo de borda. Se você invocar essas várias chamadas de método, especialmente aquelas que iniciam e interrompem os grafos, em paralelo, você pode enfrentar algumas falhas de tempo limite, como abaixo. 

O método de inicialização de assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync gerou uma exceção. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

É recomendável que você não chame métodos diretos de maneira paralela, mas faça isso de maneira sequencial, ou seja,  uma chamada de método direto somente após a conclusão da anterior.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>Coletando logs para enviar um tíquete de suporte

Quando as etapas de solução de problemas autoguiadas não resolvem seus problemas, você deve acessar o portal do Azure e [abrir um tíquete de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Siga as etapas a seguir para coletar os logs relevantes que devem ser adicionados ao tíquete. Você poderá carregar os arquivos de log na guia **detalhes** da solicitação de suporte.

### <a name="support-bundle"></a>Suporte-pacote

Quando você precisa coletar logs de um dispositivo IoT Edge, a maneira mais fácil é usar o `support-bundle` comando. Este comando coleta:

- Logs de módulo
- IoT Edge o Gerenciador de segurança e os logs do mecanismo de contêiner
- Saída JSON de verificação de Iotedge
- Informações de depuração úteis

#### <a name="use-the-iot-edge-security-manager"></a>Usar o Gerenciador de segurança do IoT Edge
 
O Gerenciador de segurança IoT Edge é responsável por operações como inicializar o sistema de IoT Edge na inicialização e nos dispositivos de provisionamento. Se IoT Edge não estiver iniciando, os logs do Gerenciador de segurança poderão fornecer informações úteis. Para exibir logs mais detalhados do Gerenciador de segurança do IoT Edge:

1. Edite as configurações do daemon de IoT Edge no dispositivo do IoT Edge:

    ```
    sudo systemctl edit iotedge.service
    ```

1. Atualize as seguintes linhas:

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Reinicie o daemon de segurança do IoT Edge executando estes comandos:

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Execute o `support-bundle` comando com o sinalizador--Since para especificar quanto tempo do passado você deseja obter logs. Por exemplo, o segundo receberá logs desde as últimas duas horas. Você pode alterar o valor desse sinalizador para incluir logs para um período diferente.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Logs de depuração do LVA

Siga estas etapas para configurar o LVA no módulo IoT Edge para gerar logs de depuração:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Clique no link **definir módulos** no menu superior.

  ![definir módulos do portal do Azure](media/troubleshoot-how-to/set-modules.png)

5. Na seção módulos IoT Edge, localize e clique em **lvaEdge**.
1. Clique em **Opções de criação de contêiner**.
1. Na seção bindings, adicione o seguinte comando:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    Isso associa as pastas de logs entre o dispositivo de borda e o contêiner.

1. Clique no botão **Atualizar**
1. Clique no botão **revisar + criar** na parte inferior da página. Uma validação simples ocorrerá e lançará a mensagem de validação bem-sucedida em uma faixa verde.
1. Clique no botão **Criar**.
1. Em seguida, atualize a **identidade do módulo** "..." para apontar o parâmetro DebugLogsDirectory para apontar para o diretório no qual os logs serão coletados:
    1. Selecione **lvaEdge** na tabela **módulos** .
    1. Clique na **identidade do módulo** conexão. Isso será encontrado na parte superior da página. Isso abrirá um painel editável.
    1. Adicione o seguinte par chave-valor na **chave desejada**:

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Clique em **Save**.

1. Reproduza o problema.
1. Conecte-se à máquina virtual na página do Hub IoT em seu portal.
1. Navegue até a `/var/local/mediaservices/logs` pasta e compacte o conteúdo do compartimento desta pasta e compartilhe-a conosco. (Esses arquivos de log não são destinados para diagnóstico automático. Eles destinam-se à engenharia do Azure de analisar seus problemas.)

1. A coleta de log pode ser interrompida Configurando esse valor na **identidade do módulo** ... para *nulo* novamente. Volte para a página de **identidade do módulo** e atualize o seguinte parâmetro como:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução com origem na nuvem](event-based-video-recording-tutorial.md)
