---
title: Especificações de hardware do Azure Kinect DK
description: Entenda os componentes, as especificações e os recursos do Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specs, hardware, DK, capabilities, depth, color, RGB, IMU, microphone, array, depth
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: dbe4cac64e324c1188ec5a01b4da3935a5d5b479
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371300"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Especificações de hardware do Azure Kinect DK

Este artigo fornece detalhes sobre como o hardware do Azure Kinect integra a tecnologia de sensor mais recente da Microsoft a um só acessório conectado por USB.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Termos

Estes termos abreviados são usados neste artigo.

- NFOV (modo de profundidade do campo de visão estreito)
- WFOV (modo de profundidade do campo de visão amplo)
- FOV (campo de visão)
- FPS (quadros por segundo)
- IMU (Unidade de Medida Inercial)
- FoI (campo de interesse)

## <a name="product-dimensions-and-weight"></a>Dimensões e peso do produto

O dispositivo Azure Kinect consiste nas dimensões de tamanho e peso a seguir.

- **Dimensões**: 103 x 39 x 126 mm
- **Peso**: 440 g

![Dimensões do Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Ambiente operacional

O Azure Kinect DK destina-se aos desenvolvedores e às empresas que operam nas seguintes condições de ambiente:

- **Temperatura**: 10-25 ⁰C
- **Umidade**: umidade relativa de 8-90% (sem condensação)

> [!NOTE]
> O uso fora das condições de ambiente pode causar falha ou funcionamento incorreto do dispositivo. Essas condições de ambiente são aplicáveis ao ambiente imediatamente em torno do dispositivo em todas as condições operacionais. Quando o dispositivo é usado com um compartimento externo, o controle de temperatura ativa e/ou outras soluções de resfriamento são recomendados para garantir que o dispositivo seja mantido dentro desses intervalos. O design do dispositivo apresenta um canal de resfriamento entre a seção frontal e a parte traseira. Ao implementar o dispositivo, verifique se esse canal de resfriamento não é obstruído.

Vejas as [informações de segurança](https://support.microsoft.com/help/4023454/safety-information) adicionais do produto.

## <a name="depth-camera-supported-operating-modes"></a>Modos de operação compatíveis com a câmera de profundidade

O Azure Kinect DK integra uma câmera de profundidade com ToF (tempo de voo) de 1 megapixel projetada pela Microsoft usando o [sensor de imagem apresentado na ISSCC 2018](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). A câmera de profundidade dá suporte aos modos indicados abaixo:

 | Mode            | Resolução | FoI       | FPS                | Intervalo operacional* | Tempo de exposição |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV descompartimentalizado   | 640 x 576    | 75° x 65°   | 0, 5, 15, 30       | 0,5 – 3,86 m       | 12,8 ms        |
| NFOV 2x2 compartimentalizado (SW) | 320 x 288    | 75° x 65°   | 0, 5, 15, 30       | 0,5 – 5,46 m       | 12,8 ms        |
| WFOV 2x2 compartimentalizado | 512 x 512    | 120° x 120° | 0, 5, 15, 30       | 0,25 – 2,88 m      | 12,8 ms        |
| WFOV descompartimentalizado   | 1\.024 x 1.024  | 120° x 120° | 0, 5, 15           | 0,25 – 2,21 m      | 20,3 ms        |
| IR passivo      | 1\.024 x 1.024  | N/D       | 0, 5, 15, 30       | N/D              | 1,6 ms         |

\*Reflectividade de 15% a 95% a 850 nm, 2,2 μW/cm<sup>2</sup>/nm, desvio padrão de erro aleatório ≤ 17 mm, erro sistemático típico < 11 mm + 0,1% de distância sem interferência de múltiplos caminhos. Profundidade fornecida fora do intervalo indicado, dependendo da reflectividade do objeto.

## <a name="color-camera-supported-operating-modes"></a>Modos de operação compatíveis com a câmera em cores

O Azure Kinect DK inclui um sensor de obturador OV12A10 CMOS de 12 MP. Os modos de operação nativos são listados abaixo:

|             Resolução de câmera RGB (HxV)  |          Taxa de proporção  |          Opções de formato   |          FPS (taxas de quadros)  |          FOV nominal (HxV) (pós-processado)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3\.840 x 2.160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       2560 x 1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1\.920 x 1.080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1\.280 x 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90° x 59°                              |
|       4\.096 x 3.072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90° x 74,3°                            |
|       2048 x 1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90° x 74,3°                            |

A câmera RGB é compatível com a classe de vídeo USB e pode ser usada sem o SDK do Sensor. O espaço de cores da câmera RGB: Intervalo completo de BT.601 [0..255]. 

> [!NOTE]
> O SDK do Sensor pode fornecer imagens coloridas no formato de pixel BGRA. Esse não é um modo nativo compatível com o dispositivo e causa carga de CPU adicional quando usado. A CPU do host é usada para a conversão de imagens MJPEG recebidas do dispositivo.

## <a name="rgb-camera-exposure-time-values"></a>Valores temporais de exposição da câmera RGB

Veja abaixo o mapeamento dos valores aceitáveis de exposição manual da câmera RGB:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250.000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1\.000.000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Tempo bruto do sensor de profundidade

Modo de profundidade | IR <br>Pulsos | Pulso <br>Largura  | Idle <br>Períodos| Tempo ocioso | Exposição <br> Hora
-|-|-|-|-|-
NFOV descompartimentalizado <br>  NFOV 2xx compartimentalizado <br> WFOV 2x2 compartimentalizado | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV descompartimentalizado                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Campo de visão da câmera

A próxima imagem mostra a profundidade e o campo de visão da câmera RGB ou os ângulos "vistos" pelos sensores. Este diagrama mostra a câmera RGB em um modo 4:3.

![FOV da câmera](./media/resources/hardware-specs-media/camera-fov.png)

Essa imagem demonstra o campo de visão da câmera, como visto de frente a uma distância de 2.000 mm.

![Parte frontal do FOV da câmera](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Quando a profundidade está no modo NFOV, a câmera RGB tem uma melhor sobreposição de pixel em resoluções de 4:3 do que de 16:9.

## <a name="motion-sensor-imu"></a>Sensor de movimento (IMU)

A IMU (Unidade de Medida Inercial) inserida é um LSM6DSMUS e inclui um acelerômetro e um giroscópio. É feita a amostragem simultânea do acelerômetro e do giroscópio a 1,6 kHz. As amostras são relatadas para o host a 208 Hz.

## <a name="microphone-array"></a>Matriz de microfone

O Azure Kinect DK insere uma matriz circular de sete microfones de alta qualidade que é identificada como um dispositivo USB 2.0 de classe de áudio padrão. Todos os sete canais podem ser acessados. As especificações de desempenho são:

- Sensibilidade: -22 dBFS (94 dB SPL, 1 kHz)
- Relação sinal/ruído > 65 dB
- Ponto de sobrecarga acústica: 116 dB

![Estrutura do microfone](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

O Azure Kinect DK é um dispositivo composto USB3 que expõe os seguintes pontos de extremidade de hardware para o sistema operacional:

A ID do fornecedor é 0x045E (Microsoft). Para obter a ID do Produto, confira a tabela abaixo:

|    Interface USB        |    IP do PNP    |     Observações            |
|-------------------------|--------------|----------------------|
|    Hub da geração 1 USB3.1    |    0x097A    |    O hub principal    |
|    Hub USB2.0         |    0x097B    |    HS USB          |
|    Câmera de profundidade       |    0x097C    |    USB3.0            |
|    Câmera em cores       |    0x097D    |    USB3.0            |
|    Microfones        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Indicadores

O dispositivo tem um indicador de streaming da câmera na frente do dispositivo que pode ser desabilitado de maneira programática com o SDK do Sensor.

O LED de status atrás do dispositivo indica o estado do dispositivo:

| Quando a luz está     | Isso significa que                                                   |
|-----------------------|------------------------------------------------------------|
| Branca e contínua           | O dispositivo está ligado e funcionando corretamente.                         |
| Branca e piscando        | O dispositivo está ligado, mas não tem uma conexão de dados USB 3.0.   |
| Âmbar e piscando        | O dispositivo não tem energia suficiente para operar.               |
| Âmbar e piscando em branco  | Atualização ou recuperação de firmware em andamento                    |

## <a name="power-device"></a>Ligar o dispositivo

O dispositivo pode ser alimentado de duas maneiras:

1. Usando a alimentação na caixa. Os dados são conectados por um cabo USB Tipo C ao Tipo A separado.
2. Usando um cabo Tipo C ao Tipo A para energia e dados.

Um cabo Tipo C ao Tipo A não está incluído no Azure Kinect DK.

> [!NOTE]
> - O cabo de alimentação na caixa é um Tipo A USB para o conector de cilindro individual. Use a alimentação de parede fornecida com este cabo. O dispositivo tem a capacidade de gerar mais energia do que duas portas USB Tipo A padrão podem fornecer.
> - Os cabos USB são importantes e recomendamos usar cabos de alta qualidade e verificar a funcionalidade antes de implantar a unidade remotamente.

> [!TIP]
> Para escolher um bom cabo Tipo C ao Tipo C:
> - O [cabo certificado para USB](https://www.usb.org/products) precisa dar suporte a energia e dados.
> - Um cabo passivo deve ter menos de 1,5 m de comprimento. Se houver necessidade de mais capacidade, use um cabo ativo. 
> - O cabo precisa dar suporte a, no mínimo, >1,5 A. Caso contrário, você precisará conectar uma fonte de energia externa.

Verificar o cabo:

- Conecte o dispositivo por meio do cabo ao computador host.
- Confirme se todos os dispositivos são enumerados corretamente no Gerenciador de Dispositivos do Windows. As câmeras de profundidade e RGB devem ser exibidas conforme mostrado no exemplo abaixo.

  ![Azure Kinect DK no Gerenciador de Dispositivos](./media/resources/hardware-specs-media/device-manager.png)

- Confirme se o cabo pode fazer uma transmissão confiável em todos os sensores no Azure Kinect Viewer, com as seguintes configurações:

  - Câmera de profundidade: NFOV descompartimentalizado
  - Câmera RGB: 2160p
  - Microfones e IMU habilitados

## <a name="what-does-the-light-mean"></a>O que a luz significa?

O indicador de energia é um LED na parte posterior do Azure Kinect DK. A cor do LED muda de acordo com o status do dispositivo.

![A imagem mostra a parte posterior do Azure Kinect DK. Há três textos explicativos numerados: um para um indicador LED e, abaixo dele, dois para os cabos.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Esta figura rotula os seguintes componentes:

1. Indicador de energia
1. Cabo de alimentação (conectado à fonte de alimentação)
1. Cabo de dados USB-C (conectado ao computador)

Verifique se os cabos estão conectados conforme mostrado. Em seguida, verifique a tabela a seguir para saber o que os vários estados da luz de energia indicam.

|Quando a luz está: |Isso significa que: |E você deve: |
| ---| --- | --- |
|Branca e contínua |O dispositivo está ligado e funcionando corretamente. |Usar o dispositivo. |
|Desligada |O dispositivo não está conectado ao computador. |Verifique se o cabo do conector de energia redondo está conectado ao dispositivo e ao adaptador de energia USB.<br /><br />Verifique se o cabo USB-C está conectado ao dispositivo e ao computador. |
|Branca e piscando |O dispositivo está ligado, mas não tem uma conexão de dados USB 3.0. |Verifique se o cabo do conector de energia redondo está conectado ao dispositivo e ao adaptador de energia USB.<br /><br />Verifique se o cabo USB-C está conectado ao dispositivo e a uma porta USB 3.0 ao computador.<br /><br />Conecte o dispositivo a outra porta USB 3.0 no computador.<br /><br />No computador, abra o Gerenciador de Dispositivos (**Iniciar** > **Painel de Controle** > **Gerenciador de Dispositivos**) e verifique se o computador tem um controlador de host USB 3.0 compatível. |
|Âmbar e piscando |O dispositivo não tem energia suficiente para operar. |Verifique se o cabo do conector de energia redondo está conectado ao dispositivo e ao adaptador de energia USB.<br /><br />Verifique se o cabo USB-C está conectado ao dispositivo e ao computador. |
|Âmbar e piscando em branco |O dispositivo está ligado e está recebendo uma atualização de firmware ou o dispositivo está restaurando as configurações de fábrica. |Aguarde até a luz do indicador de energia ficar branca e contínua. Para obter mais informações, confira [Restaurar o Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Consumo de energia

O Azure Kinect DK consome até 5,9 W; o consumo de energia específico depende do caso de uso.

## <a name="calibration"></a>Calibragem

O Azure Kinect DK é calibrado na fábrica. Os parâmetros de calibragem dos sensores visual e inercial podem ser consultados de maneira programática por meio do SDK do Sensor.

## <a name="device-recovery"></a>Recuperação do dispositivo

O firmware do dispositivo pode ser restaurado para o firmware original com o botão abaixo do pino de bloqueio.

![Botão de recuperação do Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Para recuperar o dispositivo, confira as [instruções aqui](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Próximas etapas

- [Usar o SDK do Sensor do Azure Kinect](about-sensor-sdk.md)
- [Configurar o hardware](set-up-azure-kinect-dk.md)
