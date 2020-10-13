---
title: Serviço de provisionamento de dispositivos no Hub IoT do Azure-atestado de certificado X. 509
description: Descreve os conceitos específicos do uso do atestado de certificado X. 509 com o DPS (serviço de provisionamento de dispositivos) e o Hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 911f819343f675ebe0a2604d912e6e26aa646eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533030"
---
# <a name="x509-certificate-attestation"></a>Atestado do certificado X.509

Este artigo fornece uma visão geral dos conceitos envolvidos ao provisionar dispositivos usando o atestado de certificado X. 509. Este artigo é relevante para todas as personas envolvidas na preparação de um dispositivo para implantação.

Os certificados X. 509 podem ser armazenados em um HSM de módulo de segurança de hardware.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar com segurança segredos, como o certificado X. 509, em seus dispositivos em produção.


## <a name="x509-certificates"></a>Certificados X.509

Usar certificados X.509 como um mecanismo de atestado é uma maneira excelente de escalar a produção e simplificar o provisionamento do dispositivo. Os certificados X.509 normalmente são organizados em uma cadeia de certificados de confiança na qual cada certificado na cadeia é assinado pela chave privada do próximo certificado mais alto e assim por diante, terminando em um certificado raiz autoassinado. Essa estruturação estabelece uma cadeia de confiança delegada a partir do certificado raiz gerado por uma AC (autoridade de certificação) raiz confiável através de cada AC intermediária para o certificado "folha" de entidade final instalado em um dispositivo. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](/azure/iot-hub/iot-hub-x509ca-overview). 

Geralmente a cadeia de certificados representa alguma hierarquia física ou lógica associada aos dispositivos. Por exemplo, um fabricante pode:
- emitir um Certificado de Autoridade de Certificação raiz autoassinado
- usar o certificado raiz para gerar um Certificado de Autoridade de Certificação intermediária exclusivo para cada alocador
- usar o certificado de cada alocador para gerar um Certificado de Autoridade de Certificação intermediária exclusivo para cada linha de produção na planta
- e, finalmente, usar o certificado de linha de produção para gerar um certificado de dispositivo único (entidade final) para cada dispositivo fabricado na linha. 

Para saber mais, consulte [Entendimento conceitual de certificados de AC X.509 no setor de IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificado raiz

Um certificado raiz é um certificado X.509 autoassinado que representa uma AC (autoridade de certificação). É o terminal, ou âncora de confiança, da cadeia de certificados. Certificados raiz podem ser autoemitidos por uma organização ou adquiridos de uma autoridade de certificação raiz. Para saber mais, consulte [Obter certificados de AC X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). O certificado raiz também pode ser referenciado como um certificado de AC raiz.

### <a name="intermediate-certificate"></a>Certificado intermediário

Um certificado intermediário é um certificado X.509 que foi assinado pelo certificado raiz (ou por outro certificado intermediário com o certificado raiz em sua cadeia). O último certificado intermediário em uma cadeia é usado para assinar o certificado de folha. Um certificado intermediário também pode ser referenciado como um certificado de AC intermediário.

### <a name="end-entity-leaf-certificate"></a>Certificado "secundário" de entidade final

O certificado de folha, ou certificado de entidade final, identifica o proprietário do certificado. Ele tem o certificado raiz em sua cadeia de certificados, bem como zero ou mais certificados intermediários. O certificado de folha não é usado para assinar outros certificados. Ele identifica exclusivamente o dispositivo para o serviço de provisionamento e, às vezes, são referenciados como um certificado de dispositivo. Durante a autenticação, o dispositivo usa a chave privada associada ao certificado para responder a um desafio de comprovação de posse do serviço.

Os certificados folha usados com uma entrada de [registro individual](./concepts-service.md#individual-enrollment) têm um requisito de que o **nome da entidade** deve ser definido como a ID de registro da entrada de registro individual. Os certificados folha usados com uma entrada do [grupo de registros](./concepts-service.md#enrollment-group) devem ter o **nome da entidade** definido como a ID de dispositivo desejada que será mostrada nos registros de **registro** do dispositivo autenticado no grupo de registro.

Para saber mais, consulte [Autenticação de dispositivos assinados com certificados de AC X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controlando o acesso de dispositivo para o serviço de provisionamento com certificados X.509

O serviço de provisionamento expõe dois tipos de entrada de registro que você pode usar para controlar o acesso de dispositivos que usam o mecanismo de atestado X.509:  

- As entradas de [registro individual](./concepts-service.md#individual-enrollment) são configuradas com o certificado do dispositivo associado a um dispositivo específico. Essas entradas controlam os registros de dispositivos específicos.
- As entradas de [grupo de registros](./concepts-service.md#enrollment-group) são associadas a um certificado de AC intermediário ou raiz. Essas entradas controlam os registros de todos os dispositivos que têm esse certificado raiz ou intermediário em sua cadeia de certificados. 

Quando um dispositivo se conecta ao serviço de provisionamento, o serviço prioriza entradas de registro mais específicas em relação a entradas de registro menos específicas. Ou seja, se existir um registro individual para o dispositivo, o serviço de provisionamento aplicará essa entrada. Se não houver nenhum registro individual para o dispositivo e existir um grupo de registro para o primeiro certificado intermediário na cadeia de certificados do dispositivo, o serviço aplicará essa entrada, e assim por diante para cima na cadeia até a raiz. O serviço aplica a primeira entrada aplicável que encontrar, como:

- Se a primeira entrada de registro encontrada estiver habilitada, o serviço provisionará o dispositivo.
- Se a primeira entrada de registro encontrada estiver desabilitada, o serviço não provisionará o dispositivo.  
- Se não for encontrada nenhuma entrada de registro para qualquer um dos certificados na cadeia de certificados do dispositivo, o serviço não provisionará o dispositivo. 

Esse mecanismo e a estrutura hierárquica de cadeias de certificados oferece flexibilidade eficiente em como você pode controlar o acesso a dispositivos individuais, bem como a grupos de dispositivos. Por exemplo, imagine cinco dispositivos com as seguintes cadeias de certificados: 

- *Dispositivo 1*: certificado raiz -> certificado A -> certificado do dispositivo 1
- *Dispositivo 2*: certificado raiz -> certificado A -> certificado do dispositivo 2
- *Dispositivo 3*: certificado raiz -> certificado A -> certificado do dispositivo 3
- *Dispositivo 4*: certificado raiz -> certificado B -> certificado do dispositivo 4
- *Dispositivo 5*: certificado raiz -> certificado B -> certificado do dispositivo 5

Inicialmente, você pode criar uma única entrada de registro de grupo habilitado para o certificado raiz para habilitar o acesso para todos os cinco dispositivos. Se o certificado B posteriormente for comprometido, você poderá criar uma entrada de grupo de registros desabilitada para o certificado B para impedir que o *Dispositivo 4* e o *Dispositivo 5* se registrem. Se depois ainda o *Dispositivo 3* for comprometido, você poderá criar uma entrada de registro individual desabilitada para este certificado. Isso revoga o acesso para o *Dispositivo 3*, mas ainda permite que o *Dispositivo 1* e o *Dispositivo 2* se registrem.