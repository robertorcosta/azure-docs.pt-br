---
title: Solucionar problemas
description: Informações de solução de problemas para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679979"
---
# <a name="troubleshoot"></a>Solucionar problemas

Esta página lista problemas comuns que interferem na renderização remota do Azure e maneiras de resolvê-los.

## <a name="client-cant-connect-to-server"></a>O cliente não pode se conectar ao servidor

Certifique-se de que seus firewalls (no dispositivo, nos roteadores internos, etc.) não bloqueiem as seguintes portas:

* **50051 (TCP)** - necessário para conexão inicial (aperto de mão HTTP)
* **8266 (TCP+UDP)** - necessário para transferência de dados
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - necessário para [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Erro 'Desconectado: VideoFormatNotAvailable'

Verifique se sua GPU suporta a decodificação de vídeo de hardware. Consulte [Development PC](../overview/system-requirements.md#development-pc).

Se você está trabalhando em um laptop com duas GPUs, é possível que a GPU em que você está executando por padrão, não forneça a funcionalidade de decodificação de vídeo de hardware. Se assim for, tente forçar seu aplicativo a usar a outra GPU. Isso é frequentemente possível nas configurações do driver da GPU.

## <a name="h265-codec-not-available"></a>Codec H265 não disponível

Existem duas razões pelas quais o servidor pode se recusar a se conectar com um erro **codec não disponível.**

**O codec H265 não está instalado:**

Primeiro certifique-se de instalar as **extensões de vídeo HEVC** como mencionado na seção [Software](../overview/system-requirements.md#software) dos requisitos do sistema.

Se você ainda encontrar problemas, certifique-se de que sua placa gráfica suporta H265 e você tenha o driver gráfico mais recente instalado. Consulte a seção [Development PC](../overview/system-requirements.md#development-pc) dos requisitos do sistema para obter informações específicas do fornecedor.

**O codec está instalado, mas não pode ser usado:**

A razão para este problema é uma configuração de segurança incorreta nos DLLs. Esse problema não se manifesta ao tentar assistir vídeos codificados com H265. Reinstalar o codec também não resolve o problema. Em vez disso, execute as seguintes etapas:

1. Abra um **PowerShell com direitos de admin** e execute

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Esse comando deve `InstallLocation` produzir o do codec, algo como:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Abra essa pasta no Windows Explorer
1. Deve haver uma **subpasta x86** e **uma x64.** Clique com o botão direito do mouse em uma das pastas e escolha **Propriedades**
    1. Selecione a guia **Segurança** e clique no botão **Configurações Avançadas**
    1. Clique **em Alterar** para o **Proprietário**
    1. Digite **administradores** no campo de texto
    1. Clique **em Verificar nomes** e **OK**
1. Repita as etapas acima para a outra pasta
1. Repita também as etapas acima em cada arquivo DLL dentro de ambas as pastas. Deve haver quatro DLLs ao todo.

Para verificar se as configurações estão agora corretas, faça isso para cada um dos quatro DLLs:

1. Selecionar **propriedades > edição de > de segurança**
1. Verifique a lista de todos os **Grupos /Usuários** e certifique-se de que cada um tenha o conjunto direito **Read & Execute** (a marca de seleção na coluna **permitir** deve ser marcada)

## <a name="low-video-quality"></a>Baixa qualidade de vídeo

A qualidade do vídeo pode ser comprometida tanto pela qualidade da rede quanto pelo codec de vídeo H265 ausente.

* Veja as etapas para [identificar problemas de rede](#unstable-holograms).
* Consulte os [requisitos](../overview/system-requirements.md#development-pc) do sistema para instalar o driver gráfico mais recente.

## <a name="black-screen-after-successful-model-loading"></a>Tela preta após carregamento de modelo bem-sucedido

Se você estiver conectado ao tempo de execução de renderização e carregado um modelo com sucesso, mas só ver uma tela preta depois, então isso pode ter algumas causas distintas.

Recomendamos testar as seguintes coisas antes de fazer uma análise mais aprofundada:

* O codec H265 está instalado? Embora deva haver um recuo no codec H264, vimos casos em que esse recuo não funcionou corretamente. Consulte os [requisitos](../overview/system-requirements.md#development-pc) do sistema para instalar o driver gráfico mais recente.
* Ao usar um projeto Unity, feche o Unity, exclua a *biblioteca* temporária e as pastas *obj* no diretório do projeto e carregue/construa o projeto novamente. Em alguns casos, os dados armazenados em cache fizeram com que a amostra não funcionasse corretamente sem motivo óbvio.

Se essas duas etapas não ajudaram, é necessário descobrir se os quadros de vídeo são recebidos pelo cliente ou não. Isso pode ser questionado programáticamente conforme explicado no capítulo de consultas de desempenho do lado do [servidor.](../overview/features/performance-queries.md) O `FrameStatistics struct` tem um membro que indica quantos quadros de vídeo foram recebidos. Se esse número for maior que 0 e aumentar ao longo do tempo, o cliente receberá quadros de vídeo reais do servidor. Consequentemente, deve ser um problema do lado do cliente.

### <a name="common-client-side-issues"></a>Problemas comuns do lado do cliente

**O modelo não está dentro da vista frustum:**

Em muitos casos, o modelo é exibido corretamente, mas localizado fora da câmera frustum. Uma razão comum é que o modelo foi exportado com um pivô muito fora do centro, por isso é cortado pelo plano de recorte distante da câmera. Ele ajuda a consultar a caixa delimitadora do modelo de forma programática e visualizar a caixa com unity como uma caixa de linha ou imprimir seus valores para o log de depuração.

**O pipeline de renderização Unity não inclui os ganchos de renderização:**

A azure Remote Rendering ganchos no pipeline de renderização Unity para fazer a composição do quadro com o vídeo, e para fazer a reprojeção. Para verificar se esses ganchos existem, abra o menu *> Depurador de > depuração de análise de*> quadro de janelas . Habilitá-lo e certifique-se `HolographicRemotingCallbackPass` de que há duas entradas para o no pipeline:

![Depurador de quadro de unidade](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>O código de unidade usando a API de renderização remota não compila

Mude o tipo de *compilação* da solução Unity para **Debug**. Ao testar arr no editor `UNITY_EDITOR` Unity, a definição só está disponível nas compilações 'Debug'. Observe que isso não está relacionado com o tipo de compilação usado para [aplicativos implantados,](../quickstarts/deploy-to-hololens.md)onde você deve preferir compilações 'Release'.

## <a name="unstable-holograms"></a>Hologramas instáveis

No caso de objetos renderizados parecerem estar se movendo junto com os movimentos da cabeça, você pode estar encontrando problemas com *a Reprojeção tardia* (LSR). Consulte a seção sobre [Reprojeção de Estágio Tardio](../overview/features/late-stage-reprojection.md) para obter orientações sobre como abordar tal situação.

Outra razão para hologramas instáveis (oscilação, deformada, tremor ou hologramas de salto) pode ser a conectividade de rede ruim, em particular a largura de banda insuficiente da rede, ou a latência muito alta. Um bom indicador para a qualidade da sua `ARRServiceStats.VideoFramesReused`conexão de rede é o valor das [estatísticas de desempenho.](../overview/features/performance-queries.md) Quadros reutilizados indicam situações em que um quadro de vídeo antigo precisava ser reutilizado no lado do cliente porque não havia nenhum novo quadro de vídeo disponível – por exemplo, devido à perda de pacotes ou por causa de variações na latência da rede. Se `ARRServiceStats.VideoFramesReused` for freqüentemente maior que zero, isso indica um problema de rede.

Outro valor a `ARRServiceStats.LatencyPoseToReceiveAvg`ser olhado é . Deve ser consistentemente abaixo de 100 ms. Se você ver valores mais altos, isso indica que você está conectado a um data center que está muito longe.

Para obter uma lista de possíveis mitigações, consulte as [diretrizes para a conectividade de rede.](../reference/network-requirements.md#guidelines-for-network-connectivity)

## <a name="next-steps"></a>Próximas etapas

* [Requisitos do sistema](../overview/system-requirements.md)
* [Requisitos de rede](../reference/network-requirements.md)
