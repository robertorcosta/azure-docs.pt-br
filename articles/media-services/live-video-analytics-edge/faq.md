---
title: Análise de vídeo ao vivo em IoT Edge perguntas frequentes – Azure
description: Este tópico fornece respostas para a análise de vídeo ao vivo em IoT Edge perguntas frequentes.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011508"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

Este tópico fornece respostas para a análise de vídeo ao vivo em IoT Edge perguntas frequentes.

## <a name="general"></a>Geral

Quais são as variáveis do sistema que podem ser usadas na definição da topologia do grafo?

|Variável   |Descrição|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Representa um momento no tempo, geralmente expresso como uma data e hora do dia.|
|System. GraphTopologyName   |Representa uma topologia de gráfico de mídia, contém o plano gráfico de um grafo.|
|System. GraphInstanceName|  Representa uma instância de grafo de mídia, mantém valores de parâmetro e faz referência à topologia.|

## <a name="configuration-and-deployment"></a>Configuração e implantação

Posso implantar o módulo de borda de mídia em um dispositivo Windows 10?
    * Sim. Consulte o artigo sobre [contêineres do Linux no Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Capturar da câmera IP e das configurações RTSP

* É necessário usar um SDK especial em meu dispositivo para enviar em um fluxo de vídeo?
    * Não. A análise de vídeo ao vivo no IoT Edge dá suporte à captura de mídia usando o protocolo de streaming de vídeo RTSP (que tem suporte na maioria das câmeras de IP).
* Posso enviar mídia para a análise de vídeo ao vivo em IoT Edge usando RTMP ou Smooth (como um evento de serviços de mídia ao vivo)?
    * Não. LVA só dá suporte a RTSP para capturar vídeo de câmeras IP.
    * Qualquer câmera que dê suporte ao streaming RTSP sobre TCP/HTTP deve funcionar. 
* Posso redefinir ou atualizar a URL de origem RTSP em uma instância de grafo?
    * Sim, quando a instância do grafo está no estado inativo.  
* Há um simulador RTSP disponível para uso durante o teste e o desenvolvimento?
    * Sim. Há um módulo de borda de [simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) disponível para uso nos tutoriais e inícios rápidos para dar suporte ao processo de aprendizado. Esse módulo é fornecido como o melhor esforço e pode nem sempre estar disponível. É altamente recomendável não usar isso por mais de algumas horas. Você deve investir em testes com sua fonte RTSP real antes de fazer planos para uma implantação de produção.
* Há suporte para a descoberta de ONVIF de câmeras IP na borda?
    * Não, não há suporte para a descoberta ONVIF de dispositivos na borda.

## <a name="streaming-and-playback"></a>Streaming e reprodução

* Os ativos registrados no AMS a partir do Edge são reproduzidos usando tecnologias de streaming dos serviços de mídia como HLS ou DASH?
    * Sim. Os ativos gravados podem ser transmitidos como qualquer outro ativo nos serviços de mídia do Azure. Para transmitir o conteúdo, você deve ter um ponto de extremidade de streaming criado e no estado executando. Usar o processo de criação do localizador de streaming padrão fornecerá acesso a um manifesto HLS ou DASH para streaming para qualquer estrutura de Player com capacidade. Para obter detalhes sobre como criar manifestos de publicação HLS ou DASH, consulte [empacotamento dinâmico](../latest/dynamic-packaging-overview.md).
* Posso usar os recursos de proteção de conteúdo padrão e DRM dos serviços de mídia em um ativo Arquivado?
    * Sim. Todos os recursos de DRM e proteção de conteúdo de criptografia dinâmica padrão estão disponíveis para uso nos ativos registrados de um grafo de mídia.
* Quais jogadores posso usar para exibir o conteúdo dos ativos gravados?
   * Há suporte para todos os players padrão compatíveis com o Apple HTTP Live Streaming (HLS) versão 3 ou 4. Além disso, também há suporte para qualquer jogador capaz de ser compatível com reprodução MPEG-DASH.
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
* Quais são os limites de streaming de um ativo de grafo de mídia?
    * O streaming de um ativo gravado ou dinâmico de um grafo de mídia usa a mesma infraestrutura de alta escala e ponto de extremidade de streaming aos quais os serviços de mídia oferecem suporte para transmissão ao vivo e sob demanda para a mídia & entretenimento, OTT e clientes de difusão. Isso significa que você pode habilitar com rapidez e facilidade a CDN do Azure, Verizon ou Akamai para entregar seu conteúdo a um público tão pequeno quanto alguns visualizadores, ou até milhões, dependendo do seu cenário.

    O conteúdo pode ser fornecido usando o Apple HTTP Live Streaming (HLS) ou MPEG-DASH.

## <a name="monitoring-and-metrics"></a>Monitoramento e métricas

* Posso monitorar o grafo de mídia na borda usando a grade de eventos?
    * Não. Não há suporte para a grade de eventos no momento.
* Posso usar Azure Monitor para exibir a integridade, as métricas e o desempenho de meus gráficos de mídia na nuvem ou na borda?
    * Não.
* Há alguma ferramenta para facilitar o monitoramento do módulo IoT Edge de serviços de mídia?
    * O Visual Studio Code dá suporte à extensão "ferramentas de IoT do Azure" que permite monitorar facilmente os pontos de extremidade do módulo LVAEdge. Você pode usar essa ferramenta para iniciar rapidamente o monitoramento do ponto de extremidade interno do Hub IoT para "eventos" e ver as mensagens de inferência que são roteadas do dispositivo de borda para a nuvem. 

    Além disso, você pode usar essa extensão para editar o módulo "myLVAEdge" para modificar as configurações de grafo de mídia.

Para obter mais informações, consulte o artigo [monitoramento e registro em log](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Disponibilidade e cobrança

* Como a LiveVideo Analytics é IoT Edge faturada?
    * Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Introdução – Análise de Vídeo ao vivo no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
