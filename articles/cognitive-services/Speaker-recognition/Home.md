---
title: O que é a API de Reconhecimento do Locutor?
titleSuffix: Azure Cognitive Services
description: Verificação do locutor e identificação de locutor com a API de Reconhecimento do Locutor em Serviços Cognitivos.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464910"
---
# <a name="speaker-recognition-api---preview"></a>API de Reconhecimento do Locutor – Versão prévia

APIs de Reconhecimento do Locutor são APIs baseadas em nuvem que fornecem os algoritmos avançados de IA para verificar e identificar o locutor. O Reconhecimento do Locutor é dividido em duas categorias: verificação do locutor e identificação do locutor.

## <a name="speaker-verification"></a>Verificação do locutor

A voz tem características únicas que podem ser associadas a um indivíduo.  Os aplicativos podem usar a voz como um fator adicional para verificação em cenários como call centers e serviços Web.

As APIs de Verificação do Locutor servem como uma ferramenta inteligente para ajudar a verificar os usuários usando suas frases secretas de voz e fala.

### <a name="enrollment"></a>Registro

O registro de verificação do locutor depende do texto, o que significa que os locutores precisam escolher uma frase secreta específica que será usada durante as fases de registro e verificação.

Na fase de registro do locutor, a voz do locutor é registrada dizendo uma frase específica. Os recursos de voz são extraídos para formar uma assinatura de voz exclusiva enquanto a frase escolhida é reconhecida. Juntos, esses dados de registro do locutor são usados para verificar o locutor. Os dados de registro do locutor são armazenados em um sistema seguro. O cliente controla por quanto tempo eles devem ser mantidos. Os clientes podem criar, atualizar e remover dados de registro de locutores específicos por meio de chamadas à API.  Quando a assinatura é excluída, todos os dados de registro do locutor associados à assinatura também são excluídos.

Os clientes devem garantir que receberam as permissões apropriadas dos usuários para verificação do locutor.

### <a name="verification"></a>Verificação

Na fase de verificação, o cliente deve chamar a API de verificação do locutor com a ID associada ao indivíduo a ser verificado.  O serviço extrai os recursos de voz e a frase secreta da gravação de fala de entrada. Em seguida, compara os recursos com os elementos correspondentes dos dados de registro do locutor para o locutor que o cliente está procurando verificar e determina qualquer correspondência.  A resposta retorna "aceitar" ou "rejeitar" com diferentes níveis de confiança.  O cliente então determina como usar os resultados para ajudar a decidir se essa pessoa é o locutor registrado.

O nível de confiança limite deve ser definido com base no cenário e outros fatores de verificação que estão sendo usados. Recomendamos que você teste o nível de confiança e considere a configuração apropriada para cada aplicativo. As APIs não têm como objetivo determinar se o áudio é de uma pessoa viva, uma imitação ou uma gravação de um locutor registrado.

O serviço não guarda a gravação de fala ou os recursos de voz extraídos que são enviados ao serviço durante a fase de verificação.

Para obter mais detalhes sobre a verificação do locutor, consulte a API [Locutor - verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identificação do locutor

Os aplicativos podem usar a voz para identificar "quem está falando", dado um grupo de locutores registrados. As APIs de identificação do locutor podem ser usadas em cenários como produtividade, personalização e transcrição de call center.

### <a name="enrollment"></a>Registro

O registro de identificação do locutor não depende do texto, o que significa que não há restrições sobre o que é dito pelo locutor no áudio. Não há necessidade de uma frase secreta.

Na fase de registro, a voz do locutor é registrada e os recursos de voz são extraídos para formar uma assinatura de voz exclusiva. O áudio e os recursos da fala extraídos são armazenados em um sistema seguro. O cliente controla por quanto tempo eles são mantidos. Os clientes podem criar, atualizar e remover os dados de registro de locutores específicos por meio de chamadas à API. Quando a assinatura é excluída, todos os dados de registro do locutor associados à assinatura também são excluídos.

Os clientes devem garantir que receberam as permissões apropriadas dos usuários para identificação do locutor.

### <a name="identification"></a>Identificação

Na fase de identificação, o serviço de identificação do locutor extrai recursos de voz da gravação de fala de entrada. Em seguida, compara os recursos com os dados de registro da lista especificada de locutores. Ao encontrar uma correspondência com um locutor registrado, a resposta retorna a ID do locutor com um nível de confiança.  Caso contrário, a resposta retorna "rejeitar" quando nenhum locutor corresponder a um locutor registrado.

O nível de confiança limite deve ser definido com base no cenário. Recomendamos que você teste o nível de confiança e considere a configuração apropriada para cada aplicativo. As APIs não têm como objetivo determinar se o áudio é de uma pessoa viva, uma imitação ou uma gravação de um locutor registrado.

O serviço não guarda a gravação de fala ou os recursos de voz extraídos que são enviados ao serviço durante a fase de identificação.

Para obter mais detalhes sobre a identificação do locutor, veja a API [Locutor – Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
