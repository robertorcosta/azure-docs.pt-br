---
title: Use reremo holográfico e renderização remota em Unidade
description: Como a visualização holográfica remoting pode ser usada em combinação com a renderização remota do Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681201"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Use reremo holográfico e renderização remota em Unidade

[Remoting holográfico](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) e renderização remota azure são mutuamente exclusivos dentro de um aplicativo. Como tal, [o modo de jogo Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) também não está disponível.

Para cada execução do editor Unity apenas um dos dois pode ser usado. Para usar o outro, reinicie unity primeiro.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Use o modo de reprodução Unity para visualizar no Hololens 2

 O modo de reprodução de unidade ainda pode ser usado, por exemplo, para testar a interface do ui do aplicativo. No entanto, é vital que o ARR nunca seja inicializado. Caso contrário, ele vai cair.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Use um fone de ouvido WMR VR para visualizar na área de trabalho

Se um fone de ouvido VR de realidade mista do Windows estiver presente, ele pode ser usado para visualizar dentro do Unity. Neste caso, é bom inicializar o ARR, porém não será possível conectar-se a uma sessão enquanto o fone de ouvido WMR for usado.
