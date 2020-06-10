---
title: Serviço de Reconhecimento do Locutor do Azure
titleSuffix: Azure Cognitive Services
description: Os serviços cognitivas do Azure Reconhecimento do Locutor fornecem algoritmos que verificam e identificam alto-falantes por suas características de voz exclusivas Reconhecimento do Locutor é usado para responder à pergunta "quem está falando?".
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: cc19844c6407a83233c70048e9eb258a742036fb
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84635162"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>O que é o serviço de Reconhecimento do Locutor do Azure?

O serviço de Reconhecimento do Locutor fornece algoritmos que verificam e identificam os alto-falantes por suas características de voz exclusivas. Reconhecimento do Locutor é usado para responder à pergunta "quem está falando?". Você fornece dados de treinamento de áudio para um único palestrante, que cria um perfil de registro com base nas características exclusivas da voz do orador. Em seguida, você pode fazer uma verificação cruzada das amostras de voz de áudio em relação a esse perfil para verificar se o orador é a mesma pessoa (verificação do orador) ou fazer uma verificação cruzada de exemplos de voz de áudio em um *grupo* de perfis de palestrantes registrados, para ver se ele corresponde a qualquer perfil no grupo (identificação do orador). Por outro lado, o [Diarization do orador](batch-transcription.md#speaker-separation-diarization) agrupa segmentos de áudio por palestrante em uma operação em lote.

## <a name="speaker-verification"></a>Verificação do locutor

Verificação do Locutor simplifica o processo de verificar a identidade de um palestrante registrado com senhas ou entrada de voz de forma livre. Ele pode ser usado para verificar pessoas de compromissos de clientes seguros e sem interrupções em uma ampla gama de soluções, desde a verificação de identidade do cliente em call centers até o acesso a recursos sem contato.

### <a name="how-does-speaker-verification-work"></a>Como funciona Verificação do Locutor?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Verificação do Locutor fluxograma.":::

A verificação do orador pode ser dependente de texto ou independente de texto. A verificação **dependente de texto** significa que os alto-falantes precisam escolher a mesma senha a ser usada durante as fases de registro e de verificação. A verificação **independente de texto** significa que os alto-falantes podem falar em idioma diário nas frases de registro e verificação.

Para a verificação **dependente de texto** , a voz do orador é registrada dizendo uma frase secreta de um conjunto de frases predefinidas. Os recursos de voz são extraídos da gravação de áudio para formar uma assinatura de voz exclusiva, enquanto a frase secreta escolhida também é reconhecida. Juntos, a assinatura de voz e a frase secreta são usadas para verificar o orador. 

A verificação **independente de texto** não tem restrições sobre o que o orador diz durante o registro ou na amostra de áudio a ser verificada, pois ele só extrai recursos de voz para pontuar a similaridade. 

As APIs não se destinam a determinar se o áudio é de uma pessoa em tempo real ou de um imitação/gravação de um palestrante registrado. 

## <a name="speaker-identification"></a>Identificação do locutor

Identificação do Locutor é usado para determinar a identidade de um orador desconhecido dentro de um grupo de alto-falantes registrados. Identificação do Locutor permite que você Informate a fala para palestrantes individuais e desbloqueie o valor de cenários com vários alto-falantes, como:

* Soluções de suporte para produtividade de reunião remota 
* Criar a personalização de dispositivos de vários usuários

### <a name="how-does-speaker-identification-work"></a>Como funciona Identificação do Locutor?

O registro para a identificação do orador é **independente de texto**, o que significa que não há restrições sobre o que o orador diz no áudio. Semelhante à Verificação do Locutor, na fase de registro, a voz do orador é registrada e os recursos de voz são extraídos para formar uma assinatura de voz exclusiva. Na fase de identificação, o exemplo de voz de entrada é comparado a uma lista especificada de vozes registradas (até 50 em cada solicitação).

## <a name="data-security-and-privacy"></a>Segurança e privacidade de dados

Os dados de registro do orador são armazenados em um sistema protegido, incluindo o áudio de fala para o registro e os recursos de assinatura de voz. O áudio de fala para registro é usado somente quando o algoritmo é atualizado e os recursos precisam ser extraídos novamente. O serviço não retém a gravação de fala ou os recursos de voz extraídos que são enviados para o serviço durante a fase de reconhecimento. 

Você controla por quanto tempo os dados devem ser retidos. Você pode criar, atualizar e excluir dados de registro para palestrantes individuais por meio de chamadas à API. Quando a assinatura é excluída, todos os dados de registro do locutor associados à assinatura também são excluídos. 

Assim como em todos os recursos de serviços cognitivas, os desenvolvedores que usam o serviço de Reconhecimento do Locutor devem estar cientes das políticas da Microsoft nos dados do cliente. Você deve garantir que recebeu as permissões apropriadas dos usuários para Reconhecimento do Locutor. Para obter mais informações, consulte a [página de serviços cognitivas](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   na central de confiabilidade da Microsoft. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> * Conclua o [artigo reconhecimento do locutor noções básicas](speaker-recognition-basics.md) para obter uma execução de padrões de design comuns que você pode usar em seus aplicativos.
> * Consulte o [tutorial em vídeo](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) para verificação de alto-falante independente de texto.
