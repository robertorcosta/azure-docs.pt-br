---
title: Solucionar problemas
description: Informações de solução de problemas do Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b6cb9c70de27e40c62d6a7adeece5cb39554c090
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844550"
---
# <a name="troubleshoot"></a>Solucionar problemas

Esta página lista os problemas comuns que interferem no Azure Remote Rendering e maneiras de resolvê-los.

## <a name="cant-link-storage-account-to-arr-account"></a>Não é possível vincular a conta de armazenamento à conta do ARR

Às vezes, durante a [vinculação de uma conta de armazenamento](../how-tos/create-an-account.md#link-storage-accounts), a conta do Remote Rendering não é listada. Para corrigir esse problema, vá para a conta do ARR no portal do Azure e selecione **Identidade** no grupo **Configurações** à esquerda. Verifique se **Status** está definido como **Ativo**.
![Depurador de quadros do Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>O cliente não pode se conectar ao servidor

Verifique se os firewalls (no dispositivo, dentro de roteadores etc.) não bloqueiam as seguintes portas:

* **50051 (TCP)** – necessária para a conexão inicial (handshake HTTP)
* **8266 (TCP + UDP)** – necessária para transferência de dados
* **5000 (TCP)** , **5433 (TCP)** , **8443 (TCP)** – necessárias para o [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Erro 'Desconectado: VideoFormatNotAvailable'

Verifique se sua GPU é compatível com a decodificação de vídeo por hardware. Confira [PC de Desenvolvimento](../overview/system-requirements.md#development-pc).

Se você estiver trabalhando em um laptop com duas GPUs, é possível que a GPU em que você está executando por padrão não forneça a funcionalidade de decodificação de vídeo por hardware. Se esse for o caso, tente forçar seu aplicativo a usar a outra GPU. Isso geralmente é possível nas configurações do driver da GPU.

## <a name="h265-codec-not-available"></a>Codec H265 não disponível

Há dois motivos pelos quais o servidor pode recusar a conexão com um erro **codec não disponível**.

**O codec H265 não está instalado:**

Primeiro, instale as **Extensões de Vídeo HEVC** conforme mencionado na seção [Software](../overview/system-requirements.md#software) dos requisitos do sistema.

Se você ainda enfrentar problemas, verifique se a placa de vídeo dá suporte a H265 e se você tem o driver de vídeo mais recente instalado. Confira a seção [PC de desenvolvimento](../overview/system-requirements.md#development-pc) dos requisitos do sistema para obter informações específicas do fornecedor.

**O codec está instalado, mas não pode ser usado:**

O motivo para esse problema é uma configuração de segurança incorreta nas DLLs. Esse problema não se manifesta ao tentar assistir vídeos codificados com H265. A reinstalação do codec também não corrige o problema. Em vez disso, execute as seguintes etapas:

1. Abra um **PowerShell com direitos de administrador** e execute

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Esse comando deve gerar o `InstallLocation` do codec, algo como:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Abra essa pasta no Windows Explorer
1. Deve haver uma subpasta **x86** e outra **x64**. Clique com o botão direito do mouse em uma das pastas e escolha **Propriedades**
    1. Selecione a guia **Segurança** e clique no botão de configurações **Avançado**
    1. Clique em **Alterar** para o **Proprietário**
    1. Digite **Administradores** no campo de texto
    1. Clique em **Verificar Nomes** e em **OK**
1. Repita as etapas acima para a outra pasta
1. Além disso, repita as etapas acima em cada arquivo DLL dentro de ambas as pastas. Deve haver ao todo quatro DLLs.

Para verificar se as configurações agora estão corretas, faça isso para cada uma das quatro DLLs:

1. Selecione **Propriedades > Segurança > Editar**
1. Percorra a lista de todos os **Grupos/Usuários** e verifique se cada um deles tem o direito de **Leitura e Execução** definido (a marca de seleção na coluna **Permitir** precisa estar marcada)

## <a name="low-video-quality"></a>Qualidade do vídeo baixa

A qualidade do vídeo pode ser comprometida pela qualidade da rede ou pela ausência do codec de vídeo H265.

* Confira as etapas para [identificar problemas de rede](#unstable-holograms).
* Confira os [requisitos do sistema](../overview/system-requirements.md#development-pc) para instalar o driver de vídeo mais recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>O vídeo gravado com a MRC não reflete a qualidade da experiência ao vivo

Um vídeo pode ser registrado no Hololens por meio da [MRC (Captura de Realidade Misturada)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). No entanto, o vídeo resultante tem uma qualidade pior do que a experiência ao vivo por dois motivos:
* A taxa de quadros de vídeo é limitada a 30 Hz em vez de 60 Hz.
* As imagens de vídeo não passam pela etapa de processamento de [reprojeção de fase tardia](../overview/features/late-stage-reprojection.md), portanto, o vídeo tem menos fluidez.

Ambas são limitações inerentes da técnica de gravação.

## <a name="black-screen-after-successful-model-loading"></a>Tela preta após o carregamento bem-sucedido do modelo

Se você estiver conectado ao runtime de processamento e tiver carregado um modelo com êxito, mas vir apenas uma tela preta posteriormente, isso poderá ser devido a algumas causas distintas.

É recomendável testar as seguintes possibilidades antes de fazer uma análise mais detalhada:

* O codec H265 está instalado? Embora deva haver um fallback para o codec H264, vimos casos em que esse fallback não funcionou corretamente. Confira os [requisitos do sistema](../overview/system-requirements.md#development-pc) para instalar o driver de vídeo mais recente.
* Ao usar um projeto do Unity, feche o Unity, exclua as pastas temporárias *library* e *obj* no diretório do projeto e carregue/compile o projeto novamente. Em alguns casos, os dados armazenados em cache fizeram com que o exemplo não funcionasse corretamente, sem nenhum motivo óbvio para isso.

Se essas duas etapas não ajudarem, será necessário descobrir se os quadros de vídeo são recebidos pelo cliente ou não. Isso pode ser consultado programaticamente, conforme explicado no capítulo [Consultas de desempenho do lado do servidor](../overview/features/performance-queries.md). O `FrameStatistics struct` tem um membro que indica quantos quadros de vídeo foram recebidos. Se esse número for maior que 0 e estiver aumentando ao longo do tempo, o cliente receberá quadros de vídeo reais do servidor. Consequentemente, é com certeza um problema no lado do cliente.

### <a name="common-client-side-issues"></a>Problemas comuns do lado do cliente

**O modelo excede os limites da VM selecionada, especificamente o número máximo de polígonos:**

Confira as [limitações de tamanho de VM](../reference/limits.md#overall-number-of-polygons) específicas.

**O modelo não está dentro do volume de exibição:**

Em muitos casos, o modelo é exibido corretamente, mas localizado fora do volume da câmera. Um motivo comum é que o modelo foi exportado com um eixo afastado do centro, de modo que ele é recortado pelo plano de recorte distante da câmera. É útil consultar programaticamente a caixa delimitadora do modelo e visualizar a caixa com o Unity como uma caixa de linha ou, ainda, imprimir os valores dela no log de depuração.

Além disso, o processo de conversão gera um [arquivo JSON de saída](../how-tos/conversion/get-information.md) junto com o modelo convertido. Para depurar problemas de posicionamento de modelo, vale a pena examinar a entrada `boundingBox` na [seção outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

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

A caixa delimitadora é descrita como uma posição `min` e `max` no espaço 3D, em metros. Portanto, uma coordenada de 1000,0 significa que ela está a um quilômetro de distância da origem.

Pode haver dois problemas com essa caixa delimitadora que resultam em uma geometria invisível:
* **A caixa pode ser muito afastada do centro**, portanto, o objeto é completamente recortado devido ao recorte de plano distante. Os valores de `boundingBox`, nesse caso, teriam a seguinte aparência: `min = [-2000, -5,-5], max = [-1990, 5,5]`, usando um deslocamento grande no eixo x como um exemplo aqui. Para resolver esse tipo de problema, habilite a opção `recenterToOrigin` na [configuração de conversão de modelo](../how-tos/conversion/configure-model-conversion.md).
* **A caixa pode estar centralizada, ser grande demais com uma diferença de várias ordens de magnitude**. Isso significa que, embora a câmera seja iniciada no centro do modelo, a geometria dele é recortada em todas as direções. Os valores de `boundingBox` típicos, nesse caso, seriam semelhantes a estes: `min = [-1000,-1000,-1000], max = [1000,1000,1000]`. O motivo para esse tipo de problema geralmente é uma incompatibilidade de escala de unidade. Para compensar, especifique um [valor de dimensionamento durante a conversão](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) ou marque o modelo de origem com as unidades corretas. O dimensionamento também pode ser aplicado ao nó raiz ao carregar o modelo em runtime.

**O pipeline de renderização do Unity não inclui os ganchos de renderização:**

O Azure Remote Rendering conecta-se ao pipeline de renderização do Unity para fazer a composição do quadro com o vídeo e para fazer a reprojeção. Para verificar se esses ganchos existem, abra o menu *Janela > Análise > Depurador de quadros*. Habilite-o e verifique se há duas entradas para o `HolographicRemotingCallbackPass` no pipeline:

![Depurador de quadros do Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>O código do Unity usando a API do Remote Rendering não é compilado

### <a name="use-debug-when-compiling-for-unity-editor"></a>Usar Depurar ao compilar para o Editor do Unity

Alterne o *tipo de build* da solução do Unity para **Depuração**. Ao testar ARR no editor do Unity, a definição `UNITY_EDITOR` só está disponível em builds de 'Depuração'. Observe que isso não está relacionado ao tipo de build usado para [aplicativos implantados](../quickstarts/deploy-to-hololens.md), em que você deve preferir os builds de "Versão".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Falhas de compilação ao compilar amostras do Unity para o HoloLens 2

Vimos falhas falsas ao tentar compilar exemplos do Unity (início rápido, ShowCaseApp etc.) para o HoloLens 2. O Visual Studio reclama sobre não conseguir copiar alguns arquivos, embora eles estejam lá. Se você encontrar esse problema:
* Remova todos os arquivos temporários do Unity do projeto e tente novamente. Ou seja, feche o Unity, exclua as pastas temporárias *library* e *obj* no diretório do projeto e carregue/compile o projeto novamente.
* Verifique se os projetos estão localizados em um diretório no disco com caminho razoavelmente curto, pois a etapa de cópia às vezes parece ter problemas com nomes de arquivo longos.
* Se isso não ajudar, pode ser que o MS Sense interfira na etapa de cópia. Para configurar uma exceção, execute este comando do Registro por meio da linha de comando (requer direitos de administrador):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Hologramas instáveis

Caso os objetos renderizados pareçam estar se movendo em conjunto com os movimentos de cabeça, você talvez esteja enfrentando problemas com LSR (*reprojeção de fase tardia*). Veja a seção sobre [Reprojeção de fase tardia](../overview/features/late-stage-reprojection.md) para obter diretrizes sobre como abordar essa situação.

Outro motivo para hologramas instáveis (com oscilações, distorções, tremulações ou saltos) pode ser uma conectividade de rede ruim, em particular uma largura de banda de rede insuficiente ou uma latência muito alta. Um bom indicador para a qualidade da sua conexão de rede é o valor `ARRServiceStats.VideoFramesReused` das [estatísticas de desempenho](../overview/features/performance-queries.md). Quadros reutilizados indicam situações em que um quadro de vídeo antigo precisou ser reutilizado no lado do cliente porque nenhum novo quadro de vídeo estava disponível, por exemplo, devido à perda de pacotes ou devido a variações na latência de rede. Se `ARRServiceStats.VideoFramesReused` é frequentemente maior que zero, isso indica um problema de rede.

Outro valor a ser examinado é `ARRServiceStats.LatencyPoseToReceiveAvg`. Ele deve estar consistentemente abaixo de 100 ms. Se você vir valores mais altos, isso indicará que você está conectado a um data center que está muito longe.

Para obter uma lista de possíveis mitigações, confira as [diretrizes para conectividade de rede](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Próximas etapas

* [Requisitos do sistema](../overview/system-requirements.md)
* [Requisitos de rede](../reference/network-requirements.md)
