---
title: Recomendações de segurança para IoT do Azure | Microsoft Docs
description: Este artigo resume etapas adicionais para garantir a segurança em sua solução Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048452"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recomendações de segurança para implantação de Internet das Coisas (IoT) do Azure

Este artigo contém recomendações de segurança para IoT. A implementação dessas recomendações ajudará você a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do provedor de serviços, leia [responsabilidades compartilhadas para computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)

Algumas das recomendações incluídas neste artigo podem ser monitoradas automaticamente pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa na proteção de seus recursos no Azure. Ele analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, fornece recomendações sobre como lidar com eles.

- Para obter mais informações sobre as recomendações do Azure Security Center, consulte [recomendações de segurança no Azure Security Center](../security-center/security-center-recommendations.md).
- Para obter informações sobre o Azure Security Center, consulte [o Centro de Segurança do Azure?](../security-center/security-center-intro.md)

## <a name="general"></a>Geral

| Recomendação | Comentários | Suportado pela ASC |
|-|----|--|
| Mantenha-se atualizado | Use as versões mais recentes de plataformas suportadas, linguagens de programação, protocolos e frameworks. | - |
| Mantenha as chaves de autenticação seguras | Mantenha os IDs do dispositivo e suas chaves de autenticação fisicamente seguros após a implantação. Isso evitará uma máscara de dispositivo malicioso como um dispositivo registrado. | - |
| Use sdks do dispositivo quando possível | Os SDKs do dispositivo implementam uma variedade de recursos de segurança, como criptografia, autenticação e assim por diante, para ajudá-lo a desenvolver um aplicativo de dispositivo robusto e seguro. Consulte [Entender e usar SDKs do Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) para obter mais informações. | - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso 

| Recomendação | Comentários | Suportado pela ASC |
|-|----|--|
| Defina o controle de acesso para o hub | [Entenda e defina o tipo de acesso](iot-security-deployment.md#securing-the-cloud) que cada componente terá em sua solução IoT Hub, com base na funcionalidade. As permissões permitidas são *Leitura de Registro,* *RegistroReadWrite,* *ServiceConnect*e *DeviceConnect*. As políticas de acesso compartilhado padrão [em seu hub ioT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) também podem ajudar a definir as permissões para cada componente com base em sua função. | - |
| Defina o controle de acesso para serviços back-end | Os dados ingeridos pela sua solução IoT Hub podem ser consumidos por outros serviços do Azure, como [Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [Stream Analytics,](https://docs.microsoft.com/azure/stream-analytics/) [App Service,](https://docs.microsoft.com/azure/app-service/) [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)e [Blob storage.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Certifique-se de entender e permitir permissões de acesso apropriadas conforme documentado para esses serviços. | - |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Suportado pela ASC |
|-|----|--|
| Autenticação segura do dispositivo | Garanta uma comunicação segura entre seus dispositivos e seu hub ioT, usando [uma chave de identidade ou um token de segurança exclusivo,](iot-security-deployment.md#iot-hub-security-tokens)ou um certificado [X.509 no dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) para cada dispositivo. Use o método apropriado para [usar tokens de segurança com base no protocolo escolhido (MQTT, AMQP ou HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Comunicação segura do dispositivo | O IoT Hub protege a conexão com os dispositivos usando o padrão TLS (Transport Layer Security, segurança da camada de transporte), suportando as versões 1.2 e 1.0. Use [o TLS 1.2](https://tools.ietf.org/html/rfc5246) para garantir a máxima segurança. | - |
| Proteger comunicações de serviço | O IoT Hub fornece pontos finais para se conectar a serviços de back-end, como [o Azure Storage](/azure/storage/) ou [o Event Hubs,](/azure/event-hubs) usando apenas o protocolo TLS, e nenhum ponto final é exposto em um canal não criptografado. Uma vez que esses dados cheguem a esses serviços de back-end para armazenamento ou análise, certifique-se de empregar métodos de segurança e criptografia apropriados para esse serviço e proteger informações confidenciais no back-end. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Suportado pela ASC |
|-|----|--|
| Proteja o acesso aos seus dispositivos | Mantenha as portas de hardware em seus dispositivos ao mínimo para evitar acesso indesejado. Além disso, crie mecanismos para prevenir ou detectar adulteração física do dispositivo. Leia as melhores práticas de segurança de [IoT](iot-security-best-practices.md) para obter detalhes. | - |
| Construa hardware seguro | Incorpore recursos de segurança, como armazenamento criptografado ou TPM (Trusted Platform Module, módulo de plataforma confiável), para manter os dispositivos e a infra-estrutura mais seguros. Mantenha o sistema operacional do dispositivo e os drivers atualizados para as versões mais recentes e, se o espaço permitir, instale recursos antivírus e antimalware. Leia [a arquitetura de segurança de IoT](iot-security-architecture.md) para entender como isso pode ajudar a mitigar várias ameaças à segurança. | - |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários | Suportado pela ASC |
|-|----|--|
| Monitore o acesso não autorizado aos seus dispositivos |  Use o recurso de registro do sistema operacional do dispositivo para monitorar quaisquer falhas de segurança ou adulteração física do dispositivo ou de suas portas. | - |
| Monitore sua solução de IoT a partir da nuvem | Monitore a saúde geral da sua solução IoT Hub usando as [métricas no Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Configure diagnósticos | Observe de perto suas operações registrando eventos em sua solução e, em seguida, enviando os registros de diagnóstico para o Azure Monitor para obter visibilidade sobre o desempenho. Leia [monitorar e diagnosticar problemas em seu hub de IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) para obter mais informações. | - |

## <a name="next-steps"></a>Próximas etapas

Para cenários avançados envolvendo IoT do Azure, talvez seja necessário considerar requisitos adicionais de segurança. Consulte [a arquitetura de segurança ioT](iot-security-architecture.md) para obter mais orientação.

