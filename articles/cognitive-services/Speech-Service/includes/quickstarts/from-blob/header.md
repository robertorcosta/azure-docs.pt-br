---
title: 'Início Rápido: Reconhecer a fala armazenada no Armazenamento de Blobs – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 495d2a8e38b58eb3ef1494e6a3a33ee1dc32d049
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467525"
---
Neste início rápido, você usará uma API REST para reconhecer uma fala de arquivos em um processo em lote. Um processo em lote executa a transcrição de fala sem nenhuma interação do usuário. Ele fornece um modelo de programação simples, sem a necessidade de gerenciar simultaneidade, modelos de Fala Personalizada ou outros detalhes. Ele envolve opções de controle avançadas e faz uso eficiente dos recursos do Serviço de Fala do Azure.

A [visão geral da transcrição em lote](../../../batch-transcription.md) descreve os detalhes para usar esse recurso. A API detalhada está disponível como um [documento do Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), com o título `Custom Speech transcriptins`. 

O início rápido a seguir descreverá um exemplo de uso.
