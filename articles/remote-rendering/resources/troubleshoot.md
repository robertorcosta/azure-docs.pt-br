---
title: Solucionar problemas
description: Informações de solução de problemas para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b86af2ff8fad3793fc47cec9399fd499c1cabba7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681850"
---
# <a name="troubleshoot"></a>Solucionar problemas

Esta página lista os problemas comuns que interferem na renderização remota do Azure e maneiras de resolvê-los.

## <a name="client-cant-connect-to-server"></a>O cliente não pode se conectar ao servidor

Verifique se os firewalls (no dispositivo, dentro de roteadores, etc.) não bloqueiam as seguintes portas:

* **50051 (TCP)** – necessário para a conexão inicial (handshake http)
* **8266 (TCP + UDP)** – necessário para a transferência de dados
* **5000 (TCP)**, **5433 (tcp)**, **8443 (TCP)** – obrigatório para [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Erro ' desconectado: VideoFormatNotAvailable '

Verifique se sua GPU dá suporte à decodificação de vídeo de hardware. Consulte o [PC de desenvolvimento](../overview/system-requirements.md#development-pc).

Se você estiver trabalhando em um laptop com duas GPUs, é possível que a GPU em que você está executando por padrão não forneça a funcionalidade de decodificação de vídeo de hardware. Nesse caso, tente forçar seu aplicativo a usar a outra GPU. Isso geralmente é possível nas configurações de driver de GPU.

## <a name="h265-codec-not-available"></a>Codec H265 não disponível

Há dois motivos pelos quais o servidor pode recusar a conexão com um erro de **codec não disponível** .

**O codec H265 não está instalado:**

Primeiro, certifique-se de instalar as **extensões de vídeo HEVC** conforme mencionado na seção [software](../overview/system-requirements.md#software) dos requisitos do sistema.

Se você ainda encontrar problemas, verifique se a placa gráfica dá suporte a H265 e se você tem o driver de gráficos mais recente instalado. Consulte a seção [PC de desenvolvimento](../overview/system-requirements.md#development-pc) dos requisitos de sistema para obter informações específicas do fornecedor.

**O codec está instalado, mas não pode ser usado:**

O motivo para esse problema é uma configuração de segurança incorreta nas DLLs. Esse problema não é manifestado ao tentar assistir vídeos codificados com H265. A reinstalação do codec também não corrige o problema. Em vez disso, execute as seguintes etapas:

1. Abra um **PowerShell com direitos de administrador** e execute

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Esse comando deve gerar o `InstallLocation` do codec, algo como:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Abrir essa pasta no Windows Explorer
1. Deve haver uma subpasta **x86** e **x64** . Clique com o botão direito do mouse em uma das pastas e escolha **Propriedades**
    1. Selecione a guia **segurança** e clique no botão configurações **avançadas**
    1. Clique em **alterar** para o **proprietário**
    1. Digite **os administradores** no campo de texto
    1. Clique em **verificar nomes** e **OK**
1. Repita as etapas acima para a outra pasta
1. Além disso, repita as etapas acima em cada arquivo DLL dentro de ambas as pastas. Deve haver quatro DLLs completamente.

Para verificar se as configurações agora estão corretas, faça isso para cada uma das quatro DLLs:

1. Selecione **propriedades > segurança > editar**
1. Percorra a lista de todos os **grupos/usuários** e certifique-se de que cada um deles tenha o conjunto de direitos de **leitura & execução** (a marca de seleção na coluna **permitir** deve ser marcada)

## <a name="low-video-quality"></a>Baixa qualidade de vídeo

A qualidade do vídeo pode ser comprometida pela qualidade da rede ou pelo codec de vídeo H265 ausente.

* Consulte as etapas para [identificar problemas de rede](#unstable-holograms).
* Consulte os [requisitos de sistema](../overview/system-requirements.md#development-pc) para instalar o driver de gráficos mais recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>O vídeo gravado com a MRC não reflete a qualidade da experiência ao vivo

Um vídeo pode ser registrado no Hololens por meio da [MRC (captura de realidade misturada)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). No entanto, o vídeo resultante tem uma qualidade pior do que a experiência ao vivo por dois motivos:
* A taxa de quadros de vídeo é limitada a 30 Hz em vez de 60 Hz.
* As imagens de vídeo não passam pela etapa de processamento de [Reprojeção de estágio tardia](../overview/features/late-stage-reprojection.md) , portanto, o vídeo parece ser choppier.

Ambas são limitações inerentes da técnica de gravação.

## <a name="black-screen-after-successful-model-loading"></a>Tela preta após o carregamento bem-sucedido do modelo

Se você estiver conectado ao tempo de execução de processamento e tiver carregado um modelo com êxito, mas só verá uma tela preta posteriormente, isso poderá ter algumas causas distintas.

É recomendável testar as seguintes etapas antes de fazer uma análise mais detalhada:

* O codec H265 está instalado? Embora haja um fallback para o Codec H264, vimos casos em que esse fallback não funcionou corretamente. Consulte os [requisitos de sistema](../overview/system-requirements.md#development-pc) para instalar o driver de gráficos mais recente.
* Ao usar um projeto do Unity, feche o Unity, exclua a *biblioteca* temporária e as pastas *obj* no diretório do projeto e carregue/compile o projeto novamente. Em alguns casos, os dados armazenados em cache fizeram com que o exemplo não funcionasse corretamente por nenhum motivo óbvio.

Se essas duas etapas não ajudarem, será necessário descobrir se os quadros de vídeo são recebidos pelo cliente ou não. Isso pode ser consultado programaticamente, conforme explicado no capítulo [consultas de desempenho no lado do servidor](../overview/features/performance-queries.md) . O `FrameStatistics struct` tem um membro que indica quantos quadros de vídeo foram recebidos. Se esse número for maior que 0 e aumentado ao longo do tempo, o cliente receberá quadros de vídeo reais do servidor. Consequentemente, deve ser um problema no lado do cliente.

### <a name="common-client-side-issues"></a>Problemas comuns no lado do cliente

**O modelo não está dentro do frustum de exibição:**

Em muitos casos, o modelo é exibido corretamente, mas localizado fora do frustum da câmera. Um motivo comum é que o modelo foi exportado com uma tabela dinâmica distante do centro, então ele é recortado pelo plano de recorte distante da câmera. Ele ajuda a consultar a caixa delimitadora do modelo programaticamente e visualizar a caixa com o Unity como uma caixa de linha ou imprimir seus valores no log de depuração.

Além disso, o processo de conversão gera um [arquivo JSON de saída](../how-tos/conversion/get-information.md) junto com o modelo convertido. Para depurar problemas de posicionamento de modelo, vale a pena observar `boundingBox` a entrada na [seção outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

A caixa delimitadora é descrita como `min` uma `max` posição e no espaço 3D, em metros. Portanto, uma coordenada de 1000,0 significa que é 1 Kilometer fora da origem.

Pode haver dois problemas com essa caixa delimitadora que leva a uma geometria invisível:
* **A caixa pode estar longe do centro**, portanto, o objeto é completamente recortado devido ao recorte de plano distante. Os `boundingBox` valores nesse caso teriam a seguinte aparência: `min = [-2000, -5,-5], max = [-1990, 5,5]`, usando um deslocamento grande no eixo x como um exemplo aqui. Para resolver esse tipo de problema, habilite `recenterToOrigin` a opção na [configuração de conversão de modelo](../how-tos/conversion/configure-model-conversion.md).
* **A caixa pode ser centralizada, mas ser ordens de magnitude muito grande**. Isso significa que, embora a câmera seja iniciada no centro do modelo, sua geometria é recortada em todas as direções. Os `boundingBox` valores típicos, nesse caso, teriam `min = [-1000,-1000,-1000], max = [1000,1000,1000]`a seguinte aparência:. O motivo para esse tipo de problema geralmente é uma incompatibilidade de escala de unidade. Para compensar, especifique um [valor de dimensionamento durante a conversão](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) ou marque o modelo de origem com as unidades corretas. O dimensionamento também pode ser aplicado ao nó raiz ao carregar o modelo em tempo de execução.

**O pipeline de renderização do Unity não inclui os ganchos de renderização:**

A renderização remota do Azure conecta-se ao pipeline de renderização do Unity para fazer a composição do quadro com o vídeo e para fazer a Reprojeção. Para verificar se esses ganchos existem, abra a *janela de menu > análise > o depurador de quadros*. Habilite-o e verifique se há duas entradas para `HolographicRemotingCallbackPass` o no pipeline:

![Depurador de quadros do Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>O código do Unity usando a API de renderização remota não é compilado

Alterne o *tipo de compilação* da solução de Unity a ser **depurada**. Ao testar ARR no editor do Unity, a `UNITY_EDITOR` definição só estará disponível em compilações ' depurar '. Observe que isso não está relacionado ao tipo de Build usado para [aplicativos implantados](../quickstarts/deploy-to-hololens.md), onde você deve preferir compilações de ' versão '.

## <a name="unstable-holograms"></a>Hologramas instáveis

Caso os objetos renderizados pareçam estar mudando junto com os movimentos de cabeça, você pode estar encontrando problemas com a *Reprojeção de estágio atrasado* (LSR). Consulte a seção sobre a [Reprojeção de estágio tardia](../overview/features/late-stage-reprojection.md) para obter orientação sobre como abordar essa situação.

Outro motivo para hologramas instáveis (wobbling, distorção, tremulação ou hologramas de salto) pode ser uma conectividade de rede ruim, em particular largura de banda de rede insuficiente ou latência muito alta. Um bom indicador para a qualidade da sua conexão de rede é [performance statistics](../overview/features/performance-queries.md) o valor `ARRServiceStats.VideoFramesReused`de estatísticas de desempenho. Quadros reutilizados indicam situações em que um quadro de vídeo antigo precisava ser reutilizado no lado do cliente porque nenhum novo quadro de vídeo estava disponível, por exemplo, devido à perda de pacotes ou devido a variações na latência de rede. Se `ARRServiceStats.VideoFramesReused` for frequentemente maior que zero, isso indica um problema de rede.

Outro valor a ser examinado `ARRServiceStats.LatencyPoseToReceiveAvg`é. Ele deve estar de forma consistente abaixo de 100 ms. Se você vir valores mais altos, isso indica que você está conectado a um data center que está muito longe.

Para obter uma lista de possíveis mitigações, consulte as [diretrizes para conectividade de rede](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Próximas etapas

* [Requisitos do sistema](../overview/system-requirements.md)
* [Requisitos de rede](../reference/network-requirements.md)
