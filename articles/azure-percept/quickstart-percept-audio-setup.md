---
title: Introdução ao Áudio do Azure Percept
description: Saiba como conectar seu dispositivo do Áudio do Azure Percept ao Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664154"
---
# <a name="azure-percept-audio-setup"></a>Configuração do Áudio do Azure Percept

O Áudio do Azure Percept vem pronto para uso com o Azure Percept DK. Nenhuma configuração especial é necessária.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- Áudio do Azure Percept
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT

## <a name="connecting-your-devices"></a>Como conectar os dispositivos

1. Conecte o dispositivo de áudio do Azure Percept à placa base do Azure Percept DK com o cabo USB micro tipo B para tipo A incluído. Conecte a extremidade micro tipo B do cabo ao SoM de Áudio e a extremidade tipo A à placa base do Percept DK.

1. Ligue o devkit.

    - O LED L01 no SoM de áudio será alterado para verde estável para indicar que o dispositivo está ligado.
    - O LED L02 será alterado para verde piscando para indicar que o SoM de áudio está sendo autenticado.

1. Aguarde até que o processo de autenticação seja concluído, o que pode levar até 3 minutos.

1. Quando vir um dos seguintes indicadores, você estará pronto para começar a criar protótipos:

    - O LED L01 desliga e o L02 fica branco. Isso indica que a autenticação foi concluída e que o devkit ainda não foi configurado com uma palavra-chave.
    - Todos os três LEDs ficam azuis. Isso indica que a autenticação foi concluída e que o devkit foi configurado com uma palavra-chave.

    > [!NOTE]
    > Entrar em contato com o suporte se o seu devkit não se autenticar.

## <a name="next-steps"></a>Próximas etapas

Crie uma [solução de fala sem código](./tutorial-no-code-speech.md).