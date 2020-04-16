---
title: Requisitos do sistema
description: Lista os requisitos do sistema para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411125"
---
# <a name="system-requirements"></a>Requisitos do sistema

> [!IMPORTANT]
> No momento, o **Azure Remote Rendering** está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este capítulo lista os requisitos mínimos do sistema para trabalhar com a Renderização Remota (ARR) do *Azure.*

## <a name="development-pc"></a>PC de desenvolvimento

* Windows 10 versão 1903 ou superior.
* Drivers gráficos atualizados.
* Opcional: decodificador de vídeo de hardware H265, se você quiser usar a visualização local de conteúdo renderizado remotamente (por exemplo, no Unity).

> [!IMPORTANT]
> A atualização do Windows nem sempre oferece os mais recentes drivers de GPU, verifique o site do fabricante da GPU para obter os drivers mais recentes:
>
> * [Drivers AMD](https://www.amd.com/en/support)
> * [Drivers Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Drivers NVIDIA](https://www.nvidia.com/Download/index.aspx)

A tabela abaixo lista quais GPUs suportam a decodificação de vídeo de hardware H265.

| Fabricante de GPU | Modelos com suporte |
|-----------|:-----------|
| NVIDIA | Verifique a Matriz de **Suporte NVDEC** [na parte inferior desta página](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Sua GPU precisa de um SIM na coluna **H.265 4:2:0 de 8 bits.** |
| AMD | GPUs com pelo menos a versão 6 do [Decodificador de Vídeo Unificado da](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)AMD . |
| Intel | Skylake e cpUs mais novas |

Mesmo que o codec H265 correto possa ser instalado, as propriedades de segurança nos DLLs de codec podem causar falhas na inicialização do codec. O [guia de solução de problemas](../resources/troubleshoot.md#h265-codec-not-available) descreve as etapas de como resolver esse problema. O problema de DLL só pode ocorrer ao usar o serviço em um aplicativo de desktop, por exemplo, no Unity.

## <a name="devices"></a>Dispositivos

Atualmente, a renderização remota do Azure suporta apenas a área de trabalho **hololens 2** e windows como um dispositivo de destino. Consulte a seção limitações da [plataforma.](../reference/limits.md#platform-limitations)

É importante usar o codec HEVC mais recente, pois as versões mais recentes têm melhorias significativas na latência. Para verificar qual versão está instalada no seu dispositivo:

1. Inicie a **Microsoft Store**.
1. Clique no botão **"..."** no canto superior direito.
1. Selecione **Downloads e Atualizações**.
1. Pesquise na lista as **extensões de vídeo HEVC do fabricante do dispositivo**.
1. Certifique-se de que o codec listado tenha pelo menos a versão **1.0.21821.0**.
1. Clique no botão **Obter atualizações** e aguarde a instalação.

## <a name="network"></a>Rede

Uma conexão de rede estável e de baixa latência é fundamental para uma boa experiência do usuário.

Consulte o capítulo dedicado para [os requisitos da rede](../reference/network-requirements.md).

Para solucionar problemas de rede, consulte o [Guia de Solução de Problemas](../resources/troubleshoot.md#unstable-holograms).

## <a name="software"></a>Software

O seguinte software deve ser instalado:

* A versão mais recente do **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(download)](https://git-scm.com/downloads)
* Opcional: Para visualizar o fluxo de vídeo do servidor em um PC desktop, você precisa do **HEVC Video Extensions** [(link microsoft store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7).

## <a name="unity"></a>Unity

Para desenvolvimento com unity, instale

* Unity 2019.3.1 [(baixar)](https://unity3d.com/get-unity/download)
* Instale estes módulos no Unity:
  * **UWP** – Suporte ao Build da Plataforma Universal do Windows
  * **IL2CPP** – Suporte ao Build do Windows (IL2CPP)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
