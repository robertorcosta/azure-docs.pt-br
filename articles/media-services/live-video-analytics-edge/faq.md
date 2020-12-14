---
title: Análise de vídeo ao vivo em IoT Edge perguntas frequentes – Azure
description: Este tópico fornece respostas para a análise de vídeo ao vivo em IoT Edge perguntas frequentes.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401569"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

Este tópico fornece respostas para a análise de vídeo ao vivo em IoT Edge perguntas frequentes.

## <a name="general"></a>Geral

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Quais são as variáveis do sistema que podem ser usadas na definição da topologia do grafo?

|Variável   |Descrição|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Representa um instante em hora UTC, normalmente expresso como uma data e hora do dia (representação básica yyyyMMddTHHmmssZ).|
|System. PreciseDateTime|Representa uma instância de data e hora UTC no formato compatível com o arquivo ISO8601 com milissegundos (representação básica AAAAMMDDThhmmss. fffZ).|
|System. GraphTopologyName   |Representa uma topologia de gráfico de mídia, contém o plano gráfico de um grafo.|
|System. GraphInstanceName|  Representa uma instância de grafo de mídia, mantém valores de parâmetro e faz referência à topologia.|

## <a name="configuration-and-deployment"></a>Configuração e implantação

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Posso implantar o módulo de borda de mídia em um dispositivo Windows 10?

Sim. Consulte o artigo sobre [contêineres do Linux no Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Capturar da câmera IP e das configurações RTSP

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>É necessário usar um SDK especial em meu dispositivo para enviar em um fluxo de vídeo?

Não. A análise de vídeo ao vivo no IoT Edge dá suporte à captura de mídia usando o protocolo de streaming de vídeo RTSP (que tem suporte na maioria das câmeras de IP).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Posso enviar mídia para a análise de vídeo ao vivo em IoT Edge usando RTMP ou Smooth (como um evento de serviços de mídia ao vivo)?

* Não. A análise de vídeo ao vivo só dá suporte a RTSP para capturar vídeo de câmeras IP.
* Qualquer câmera que dê suporte ao streaming RTSP sobre TCP/HTTP deve funcionar. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Posso redefinir ou atualizar a URL de origem RTSP em uma instância de grafo?

Sim, quando a instância do grafo está no estado inativo.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Há um simulador RTSP disponível para uso durante o teste e o desenvolvimento?

Sim. Há um módulo de borda de [simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) disponível para uso nos tutoriais e inícios rápidos para dar suporte ao processo de aprendizado. Esse módulo é fornecido como o melhor esforço e pode nem sempre estar disponível. É altamente recomendável não usar isso por mais de algumas horas. Você deve investir em testes com sua fonte RTSP real antes de fazer planos para uma implantação de produção.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Há suporte para a descoberta de ONVIF de câmeras IP na borda?

Não, não há suporte para a descoberta ONVIF de dispositivos na borda.

## <a name="streaming-and-playback"></a>Streaming e reprodução

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Os ativos registrados no AMS a partir do Edge são reproduzidos usando tecnologias de streaming dos serviços de mídia como HLS ou DASH?

Sim. Os ativos gravados podem ser transmitidos como qualquer outro ativo nos serviços de mídia do Azure. Para transmitir o conteúdo, você deve ter um ponto de extremidade de streaming criado e no estado executando. Usar o processo de criação do localizador de streaming padrão fornecerá acesso a um manifesto HLS ou DASH para streaming para qualquer estrutura de Player com capacidade. Para obter detalhes sobre como criar manifestos de publicação HLS ou DASH, consulte [empacotamento dinâmico](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Posso usar os recursos de proteção de conteúdo padrão e DRM dos serviços de mídia em um ativo Arquivado?

Sim. Todos os recursos de DRM e proteção de conteúdo de criptografia dinâmica padrão estão disponíveis para uso nos ativos registrados de um grafo de mídia.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Quais jogadores posso usar para exibir o conteúdo dos ativos gravados?

Há suporte para todos os players padrão compatíveis com o Apple HTTP Live Streaming (HLS) versão 3 ou 4. Além disso, também há suporte para qualquer jogador capaz de ser compatível com reprodução MPEG-DASH.

Os players recomendados para teste incluem:

* [Player de Mídia do Azure](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [HTTP Live Streaming nativo da Apple](https://developer.apple.com/streaming/)
* Edge, Chrome ou Safari compilado no player de vídeo HTML5
* Jogadores comerciais que dão suporte à reprodução de HLS ou DASH

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Quais são os limites de streaming de um ativo de grafo de mídia?

O streaming de um ativo gravado ou dinâmico de um grafo de mídia usa a mesma infraestrutura de alta escala e ponto de extremidade de streaming aos quais os serviços de mídia oferecem suporte para transmissão ao vivo e sob demanda para a mídia & entretenimento, OTT e clientes de difusão. Isso significa que você pode habilitar com rapidez e facilidade a CDN do Azure, Verizon ou Akamai para entregar seu conteúdo a um público tão pequeno quanto alguns visualizadores, ou até milhões, dependendo do seu cenário.

O conteúdo pode ser fornecido usando o Apple HTTP Live Streaming (HLS) ou MPEG-DASH.

## <a name="design-your-ai-model"></a>Projete seu modelo de ia 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Tenho vários modelos de ia encapsulados em um contêiner do Docker. Como devo usá-los com a análise de vídeo ao vivo? 

As soluções são diferentes dependendo do protocolo de comunicação usado pelo servidor inferência para se comunicar com a análise de vídeo ao vivo. Abaixo estão algumas maneiras de fazer isso.

#### <a name="http-protocol"></a>Protocolo HTTP:

* Contêiner único (lvaExtension único):  

   No servidor do inferência, você pode usar uma única porta, mas diferentes pontos de extremidade para modelos de ia diferentes. Por exemplo, para um exemplo de Python, você pode usar diferentes `route` s por modelo como: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Em seguida, em sua implantação de análise de vídeo ao vivo, ao criar uma instância de grafos, defina a URL do servidor de inferência para cada instância como: 

   1ª instância: URL do servidor de inferência =`http://lvaExtension:44000/score/face_detection`<br/>
   2ª instância: URL do servidor de inferência =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Como alternativa, você também pode expor seus modelos de ia em portas diferentes e chamá-los ao instanciar grafos.  

* Vários contêineres: 

   Cada contêiner é implantado com um nome diferente. Atualmente, no conjunto de documentação de análise de vídeo ao vivo, mostramos como implantar uma extensão com o nome: **lvaExtension**. Agora você pode desenvolver dois contêineres diferentes. Cada contêiner tem a mesma interface HTTP (ou seja, o mesmo `/score` ponto de extremidade). Implante esses dois contêineres com nomes diferentes e certifique-se de que ambos estejam escutando em **portas diferentes**. 

   Por exemplo, um contêiner com o nome `lvaExtension1` está ouvindo a porta `44000` , outro contêiner com o nome `lvaExtension2` está ouvindo a porta `44001` . 

   Em sua topologia de análise de vídeo ao vivo, você instancia dois grafos com diferentes URLs de inferência como: 

   Primeira instância: URL do servidor de inferência = `http://lvaExtension1:44001/score`    
   Segunda instância: URL do servidor de inferência = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>Protocolo GRPC: 

Com o módulo de análise de vídeo ao vivo 1,0, ao usar um protocolo gRPC, a única maneira seria se o servidor gRPC expunha modelos de ia diferentes por meio de portas diferentes. Neste [exemplo](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json), há uma única porta, 44000, que está expondo todos os modelos Yolo. Em teoria, o servidor Yolo gRPC poderia ser reescrito para expor alguns modelos às 44000, outros em 45000,... 

Com o módulo de análise de vídeo ao vivo 2,0, uma nova propriedade é adicionada ao nó de extensão gRPC. Essa propriedade é chamada **extensionConfiguration** , que é uma cadeia de caracteres opcional que pode ser usada como parte do contrato gRPC. Quando você tiver vários modelos de ia empacotados em um único servidor de inferência, não será necessário expor um nó para todos os modelos de ia. Em vez disso, para uma instância de grafo, o provedor de extensão (você) pode definir como selecionar os diferentes modelos de ia usando a propriedade **extensionConfiguration** e durante a execução, a análise de vídeo ao vivo passará essa cadeia de caracteres para o servidor inferência, que pode usar isso para invocar o modelo de ia desejado. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Estou criando um servidor gRPC em um modelo de ia e desejo dar suporte ao uso por várias instâncias de câmeras/gráficos. Como devo criar meu servidor? 

 Em primeiro lugar, certifique-se de que o servidor possa lidar com mais de uma solicitação por vez. Ou certifique-se de que o servidor funciona em threads paralelos. 

Por exemplo, em um dos [exemplos de GRPC de análise de vídeo ao vivo](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py), há um número padrão de canais paralelos definido. Consulte: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Na instanciação do servidor gRPC acima, o servidor pode abrir apenas três canais por câmera (por exemplo, por instância de topologia de grafo) de cada vez. Você não deve tentar conectar mais de três instâncias ao servidor. Se você tentar abrir mais de três canais, as solicitações estarão pendentes até que um existente seja descartada.  

Acima, a implementação do gRPC Server é usada em nossos exemplos do Python. Os desenvolvedores podem implementar seus próprios servidores ou, na implementação padrão acima, podem aumentar o número de trabalho definido para o número de câmeras usadas para obter o feed de vídeo. 

Para configurar e usar várias câmeras, os desenvolvedores podem instanciar várias instâncias de topologia de grafo, em que cada instância aponta para o mesmo servidor de inferência ou outro (por exemplo, servidor mencionado no parágrafo acima). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Quero ser capaz de receber vários quadros de upstream antes de tomar uma decisão de inferência. Como posso habilitar isso? 

[Os exemplos padrão](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) atuais funcionam no modo "sem estado". Esses exemplos não estão mantendo o estado das chamadas anteriores e até mesmo quem chamou (o que significa que várias instâncias de topologia podem chamar o mesmo servidor de inferência, e o servidor não será capaz de distinguir quem está chamando e estado por chamador) 

#### <a name="http-protocol"></a>Protocolo HTTP

Ao usar o protocolo HTTP: 

Para manter o estado, cada chamador (instância de topologia do grafo) chamará o servidor inferência com o parâmetro de consulta HTTP exclusivo para o chamador. Por exemplo, endereço URL do servidor de inferência para  

primeira instância da topologia = `http://lvaExtension:44000/score?id=1`<br/>
2ª instância de topologia = `http://lvaExtension:44000/score?id=2`

… 

No lado do servidor, a rota de Pontuação saberá quem está chamando. Se ID = 1, ele poderá manter o estado separadamente para esse chamador (instância de topologia do grafo). Em seguida, você pode manter os quadros de vídeo recebidos em um buffer (por exemplo, matriz ou um dicionário com a chave DateTime, e o valor é o quadro) e, em seguida, você pode definir o servidor para processar (inferir) depois que os quadros x forem recebidos. 

#### <a name="grpc-protocol"></a>Protocolo GRPC 

Ao usar o protocolo gRPC: 

Com uma extensão gRPC, cada sessão é para um único feed de câmera, portanto, não é necessário fornecer uma ID. Agora, com a propriedade extensionConfiguration, você pode armazenar os quadros de vídeo em um buffer e definir o servidor para processar (inferir) depois que os quadros x forem recebidos. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Todos os ProcessMediaStreams em um contêiner específico executam o mesmo modelo de ia? 

Não.  

Iniciar/parar chamadas do usuário final em uma instância de grafo constitui uma sessão ou talvez haja uma câmera desconectar/reconectar. O objetivo é persistir uma sessão se a câmera estiver transmitindo vídeo. 

* Duas câmeras que enviam vídeo para processamento, criam duas sessões. 
* Uma câmera que vai para um grafo que tem dois nós gRPCExtension cria duas sessões. 

Cada sessão é uma conexão Full duplex entre a análise de vídeo ao vivo e o servidor gRPC e cada sessão pode ter um modelo/pipeline diferente. 

> [!NOTE]
> No caso de uma câmera desconectar/reconectar (com a câmera ficando offline por um período além dos limites de tolerância), a análise de vídeo ao vivo abrirá uma nova sessão com o servidor gRPC. Não há nenhum requisito para o servidor controlar o estado entre essas sessões. 

A análise de vídeo ao vivo também adicionou suporte a várias extensões de gRPC para uma única câmera em uma instância de grafo. Você poderá usar essas extensões gRPC para executar o processamento de ia em sequência ou em paralelo ou até mesmo ter uma combinação de ambos. 

> [!NOTE]
> Ter várias extensões executadas em paralelo afetará os recursos de hardware e você terá que se lembrar ao escolher o hardware que atenderá às suas necessidades computacionais. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Qual é o número máximo de ProcessMediaStreams simultâneas? 

Não há limite de que a análise de vídeo ao vivo se aplica.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Como devo decidir se meu servidor inferência deve usar CPU ou GPU ou qualquer outro acelerador de hardware? 

Isso é completamente dependente de quão complexo o modelo de ia é desenvolvido e como o desenvolvedor deseja usar os aceleradores de CPU e de hardware. Ao desenvolver o modelo de ia, os desenvolvedores podem especificar quais recursos devem ser usados pelo modelo para executar as ações. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Como fazer armazenar imagens com caixas delimitadas após o processamento? 

Hoje, estamos fornecendo coordenadas de caixa delimitadora apenas como mensagens de inferência. Os desenvolvedores podem criar um fluxo de MJPEG personalizado que pode usar essas mensagens e sobrepor as caixas delimitadas nos quadros de vídeo.  

## <a name="grpc-compatibility"></a>compatibilidade do gRPC 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Como saber quais são os campos obrigatórios para o descritor de fluxo de mídia? 

Qualquer valor de campo que não for fornecido receberá um padrão [conforme especificado por gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

A análise de vídeo ao vivo usa a versão **Proto3** do idioma do buffer de protocolo. Todos os dados de buffer de protocolo usados por contratos de análise de vídeo ao vivo estão disponíveis nos arquivos de buffer de protocolo [definidos aqui](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Como devo garantir que estou usando os arquivos mais recentes de buffer de protocolo? 

Os arquivos de buffer de protocolo mais recentes podem ser [obtidos aqui](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Sempre que atualizarmos os arquivos de contrato, eles serão exibidos neste local. Embora não haja nenhum plano imediato para atualizar os arquivos de protocolo, procure o nome do pacote na parte superior dos arquivos para saber a versão. E deve ser: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Todas as atualizações desses arquivos aumentarão o "v-Value" no final do nome. 

> [!NOTE]
> Como a análise de vídeo ao vivo usa a versão Proto3 da linguagem, os campos são opcionais e isso o torna compatível com versões anteriores e posteriores. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Quais recursos do gRPC estão disponíveis para uso com a análise de vídeo ao vivo? Quais recursos são obrigatórios e quais são opcionais? 

Qualquer recurso de gRPC do servidor pode ser usado, desde que o contrato protobuf seja atendido. 

## <a name="monitoring-and-metrics"></a>Monitoramento e métricas

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Posso monitorar o grafo de mídia na borda usando a grade de eventos?

Sim. Você pode consumir as métricas de Prometheus e publicá-las na grade de eventos. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Posso usar Azure Monitor para exibir a integridade, as métricas e o desempenho de meus gráficos de mídia na nuvem ou na borda?

Sim. Com suporte. Saiba mais sobre [como usar métricas de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Há alguma ferramenta para facilitar o monitoramento do módulo IoT Edge de serviços de mídia?

O Visual Studio Code dá suporte à extensão "ferramentas de IoT do Azure" que permite monitorar facilmente os pontos de extremidade do módulo LVAEdge. Você pode usar essa ferramenta para iniciar rapidamente o monitoramento do ponto de extremidade interno do Hub IoT para "eventos" e ver as mensagens de inferência que são roteadas do dispositivo de borda para a nuvem. 

Além disso, você pode usar essa extensão para editar o módulo "myLVAEdge" para modificar as configurações de grafo de mídia.

Para obter mais informações, consulte o artigo [monitoramento e registro em log](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Disponibilidade e cobrança

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Como a análise de vídeo ao vivo em IoT Edge cobrada?

Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Introdução – Análise de Vídeo ao vivo no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
