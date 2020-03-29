---
title: Documentação do SDK dos Dispositivos de Fala
titleSuffix: Azure Cognitive Services
description: As notas de versão fornecem um registro de atualizações, melhorias, correções de bugs e alterações no SDK dos dispositivos de fala. Este artigo é atualizado a cada versão do Speech Devices SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371613"
---
# <a name="release-notes-speech-devices-sdk"></a>Notas de versão: Speech Devices SDK

As seções a seguir listam as alterações nas versões mais recentes.

## <a name="speech-devices-sdk-190"></a>Dispositivos de fala SDK 1.9.0:

- Binários iniciais para [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) são fornecidos.
- Roobo v1 agora usa Maven para o Speech SDK
- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.9.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>Dispositivos de fala SDK 1.7.0:

- O Linux ARM agora é suportado.
- Binários iniciais para [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) são fornecidos (Linux ARM64).
- Os usuários `AudioConfig.fromDefaultMicrophoneInput()` do `AudioConfig.fromMicrophoneInput(deviceName)` Windows podem usar ou especificar o microfone a ser usado.
- O tamanho da biblioteca foi otimizado.
- Suporte para reconhecimento de vários turnos usando o mesmo objeto de reconhecimento de voz/intenção.
- Corrigir enforcamento ocasional que ocorreria durante a interrupção do reconhecimento.
- Os aplicativos de amostra agora contêm um arquivo sample participants.properties para demonstrar o formato do arquivo.
- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.7.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Dispositivos de fala SDK 1.6.0:

- Suporte [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) no Windows e Linux com [aplicativo de amostra](https://aka.ms/sdsdk-download) comum
- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.6.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Dispositivos de fala SDK 1.5.1:

- Inclua [transcrição de conversação](conversation-transcription-service.md) no aplicativo de amostra.
- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.5.1. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Dispositivos de fala SDK 1.5.0: lançamento 2019-Maio

- Speech Devices SDK agora é GA e não é mais uma visualização fechada.
- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.5.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).
- A nova tecnologia de palavras-chave traz melhorias significativas na qualidade, veja Breaking Changes.
- Novo pipeline de processamento de áudio para melhor reconhecimento de campo distante.

**Alterações da falha**

- Devido à nova tecnologia de palavras-chave, todas as palavras-chave devem ser recriadas em nosso portal de palavras-chave melhorado. Para remover totalmente palavras-chave antigas do dispositivo, desinstale o aplicativo antigo.
  - adb desinstalar com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Dispositivos de fala SDK 1.4.0: lançamento 2019-Apr

- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.4.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Dispositivos de fala SDK 1.3.1: Lançamento 2019-Mar

- Atualizei o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.3.1. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).
- Manuseio atualizado de palavras-chave, consulte Quebrando Alterações.
- O aplicativo de exemplo adiciona escolha de idioma tanto para reconhecimento de fala quanto para tradução.

**Alterações da falha**

- [A instalação de uma palavra-chave](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) foi simplificada, agora faz parte do aplicativo e não precisa de instalação separada no dispositivo.
- O reconhecimento da palavra-chave mudou, e dois eventos são suportados.
  - `RecognizingKeyword,`indica que o resultado da fala contém (texto de palavra-chave não verificado).
  - `RecognizedKeyword`, indica que o reconhecimento de palavras-chave completou reconhecendo a palavra-chave dada.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0: versão 2018-Nov

- Atualizado o [SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.1.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).
- A precisão de reconhecimento de fala de campo distante foi aprimorada com nosso algoritmo de processamento de áudio aprimorado.
- O aplicativo de exemplo adicionou suporte ao reconhecimento de fala chinês.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Dispositivos de fala SDK 1.0.1: Versão 2018-Out

- O componente do [SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) foi atualizado para a versão 1.0.1. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).
- A precisão do reconhecimento de fala será aprimorada com nosso algoritmo de processamento de áudio aprimorada
- Um bug da sessão de áudio de reconhecimento contínuo é corrigido.

**Alterações da falha**

- Com essa versão, várias alterações significativas foram introduzidas. Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes das APIs.
- Os arquivos de modelo KWS não são compatíveis com Dispositivos de Fala SDK 1.0.1. Os arquivos de palavras-chave existentes serão excluídos depois que os novos arquivos de palavras-chave forem gravados no dispositivo.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Dispositivos de fala SDK 0.5.0: versão 2018-Ago

- Melhoria da precisão do reconhecimento de fala pela correção de um bug no código de processamento de áudio.
- O componente do [SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) foi atualizado para a versão 0.5.0. Para obter mais informações, consulte [notas de versão](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Dispositivos de fala SDK 0.2.12733: lançamento 2018-Maio

A primeira visualização pública do SDK dos Dispositivos de Fala dos Serviços Cognitivos.
