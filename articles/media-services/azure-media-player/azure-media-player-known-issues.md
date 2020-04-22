---
title: Azure Media Player Problemas conhecidos
description: O lançamento atual tem os seguintes problemas conhecidos.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727210"
---
# <a name="known-issues"></a>Problemas conhecidos #

A versão atual tem os seguintes problemas conhecidos:

## <a name="azure-media-player"></a>Player de Mídia do Azure ##

- Codificadores configurados incorretamente podem causar problemas com a reprodução
- Fluxos com carimbos superiores a 2^53 podem ter problemas de reprodução.
  - Mitigação: Use vídeo de 90 kHz e escalas de tempo de áudio de 44,1 kHz
- Não há reprodução automática em dispositivos móveis sem interação do usuário. Está bloqueada pela plataforma.
- Procurar descontinuidades próximas pode causar falha na reprodução.
- O download de grandes apresentações pode fazer com que a IA seja bloqueada.
- Não é possível reproduzir automaticamente a mesma fonte após o término da apresentação.
  - Para reproduzir uma fonte depois de terminar, é necessário definir a fonte novamente.
- Manifestos vazios podem causar problemas com o jogador.
  - Esse problema pode ocorrer ao iniciar uma transmissão ao vivo e não são encontrados pedaços suficientes no manifesto.
- Posição de reprodução talvez fora da barra de busca da UI.
- O pedido de eventos não é consistente em todos os técnicos.
- A propriedade tamponada não é consistente entre os técnicos.
- nativeControlsForTouch deve ser falso (padrão) para evitar que "Objeto não suporta propriedade ou método 'setControls'"
- Cartazes agora devem ser urls absolutos
- Problemas estéticos menores podem ocorrer na ui ao usar o modo de alto contraste do dispositivo
- URLs contendo "%" ou "+" na seqüência totalmente decodificada podem ter problemas para definir a fonte

## <a name="ad-insertion"></a>Inserção de anúncios ##

- Os anúncios podem ter problemas sendo inseridos (sob demanda ou ao vivo) quando um bloqueador de anúncios é instalado no navegador
- Os dispositivos móveis podem ter problemas para reproduzir anúncios.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Na janela DVR de conteúdo live, quando o conteúdo termina, a linha do tempo continuará a crescer até buscar a área ou chegar ao final da apresentação.
- Apresentações ao vivo no Firefox com MSE habilitado tem alguns problemas
- Os ativos que são de áudio ou vídeo só não serão reencontrados através da tecnologia AzureHtml5JS.
  - Se você quiser reproduzir ativos sem áudio ou vídeo, você pode fazê-lo inserindo áudio ou vídeo em branco usando a [ferramenta Azure Media Services Explorer](https://aka.ms/amse)
    - Instruções sobre como inserir áudio silencioso podem ser encontradas [aqui](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Piscando ##

- O conteúdo do AES não é reexibido na versão flash 30.0.0.134 devido a um bug na lógica de cache da Adobe. A Adobe corrigiu o problema e o liberou em 30.0.0.154
- Falhas tecnológicas e http (como 404 tempos de rede), o jogador levará mais tempo para se recuperar do que outros técnicos.
- Clique na área de vídeo com a tecnologia flashSS não reproduzirá/pausar o player.
- Se o usuário tiver o Flash instalado, mas não der permissão para carregá-lo no site, pode ocorrer uma fiação infinita. Isso porque o jogador acha que o plugin está instalado e disponível e acha que o plugin está executando o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plugin até que o usuário aceite o prompt para permitir o plugin. Isso pode ocorrer em todos os navegadores.  

## <a name="silverlight"></a>Silverlight ##

- Recursos ausentes
- Falhas tecnológicas e http (como 404 tempos de rede), o jogador levará mais tempo para se recuperar do que outros técnicos.
- Safari e Firefox na reprodução do Mac `"http://` `https://` com Silverlight requer definição explícita ou para a fonte.
- Se uma API está faltando para esta tecnologia, ela geralmente retorna nula.
- Se o usuário tiver o Flash instalado, mas não der permissão para carregá-lo no site, pode ocorrer uma fiação infinita. Isso porque o jogador acha que o plugin está instalado e disponível e acha que o plugin está executando o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plugin até que o usuário aceite o prompt para permitir o plugin. Isso pode ocorrer em todos os navegadores.  

## <a name="native-html5"></a>NATIVO HTML5 ##

- Html5 tech está enviando apenas canplaythrough event para a primeira fonte de conjunto.
- Esta tecnologia só suporta o que o navegador implementou.  Algumas características podem estar faltando nesta tecnologia.  
- Se uma API está faltando para esta tecnologia, ela geralmente retorna nula.
- Há problemas com Legendas e Legendas nesta tecnologia. Eles podem ou não estar disponíveis ou visualizar nesta tecnologia.
- Ter largura de banda limitada no cenário técnico HLS/Html5 resulta em reprodução de áudio sem vídeo.

### <a name="microsoft"></a>Microsoft ###

- A reprodução do IE8 não funciona atualmente devido à incompatibilidade com o ECMAScript 5
- Em IE e algumas versões do Edge, a tela cheia não pode ser inserida guiando o botão e selecionando-o ou usando a tecla f/f.

## <a name="google"></a>Google ##

- Vários perfis de codificação no mesmo manifesto têm alguns problemas de reprodução no Chrome e não é recomendado.
- O Chrome não pode reproduzir o HE-AAC com o AzureHtml5JS. Siga detalhes sobre o [rastreador de bugs](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A partir do Chrome v58, o conteúdo widevine deve ser carregado/reproduzido através do protocolo https:// caso contrário, a reprodução falhará.

## <a name="mozilla"></a>Mozilla ##

- O switch de fluxo de áudio requer que os fluxos de áudio tenham os mesmos dados privados de codec ao usar o AzureHtml5JS. A plataforma Firefox requer isso.

## <a name="apple"></a>Maçã ##

- O Safari on Mac geralmente permite o modo Power Saver por padrão com a configuração "Stop plug-ins to save power", que bloqueia plugins como Flash e Silverlight quando eles acreditam que não é a favor do usuário. Este bloco não bloqueia as capacidades existentes do plugin. Dada a tecnologia padrãoOrder, isso pode causar problemas ao tentar reproduzir
  - Atenuação 1: Se o reprodutor de vídeo estiver 'frente e centro' (dentro de um limite de 3000 x 3000 pixels começando no canto superior esquerdo do documento), ele ainda deve ser reiniciado.
  - Mitigação 2: Mude a tecnologiaOrder for Safari para ser ["azureHtml5JS", "html5"]. Essa mitigação tem implicação de não obter todos os recursos disponíveis na tecnologia FlashSS.
- O conteúdo playReady via Silverlight pode ter problemas de reprodução no Safari.
- O conteúdo de Token AES e restrito não reproduziu usando dispositivos Android e iOS e antigos.
  - Para alcançar esse cenário, um proxy deve ser adicionado ao seu serviço.
- A skin padrão para iOS Phone aparece.
- A reprodução do iPhone sempre ocorre na tela cheia do leitor nativo.
  - Os recursos, incluindo legendas, podem não persistir nesta reprodução não-inline.
- Quando a apresentação ao vivo terminar, os dispositivos iOS não encerrarão adequadamente a apresentação.
- O iOS não permite recursos de DVR.
- O switch de fluxo de áudio do iOS só pode ser feito através da IU do player nativo do iOS e requer que os fluxos de áudio tenham os mesmos dados privados de codec
- Versões mais antigas do Safari podem potencialmente ter problemas para reproduzir transmissões ao vivo.

## <a name="older-android"></a>Android mais antigo ##

- O conteúdo de Token AES e restrito não reproduziu usando dispositivos Android e iOS e antigos.
  - Para alcançar esse cenário, um proxy deve ser adicionado ao seu serviço.

## <a name="next-steps"></a>Próximas etapas ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)