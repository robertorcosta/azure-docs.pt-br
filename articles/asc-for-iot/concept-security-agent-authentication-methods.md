---
title: Métodos de autenticação de agentes de segurança
description: Conheça os diferentes métodos de autenticação disponíveis ao usar o azure Security Center para serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d9d51292c3cae9634af917819b558cdfd2fa04b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311512"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação de agentes de segurança

Este artigo explica os diferentes métodos de autenticação que você pode usar com o agente AzureIoTSecurity para autenticar com o IoT Hub.

Para cada dispositivo a bordo do Azure Security Center for IoT no IoT Hub, é necessário um módulo de segurança. Para autenticar o dispositivo, o Azure Security Center para IoT pode usar um dos dois métodos. Escolha o método que funciona melhor para sua solução de IoT existente.

> [!div class="checklist"]
> * Opção SecurityModule
> * Opção de dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente AzureIoTSecurity realizar a autenticação:

- Modo de autenticação **securitymodule**<br>
O agente é autenticado usando a identidade do módulo de segurança independentemente da identidade do dispositivo.
Use este tipo de autenticação se quiser que o agente de segurança use um método de autenticação dedicado através do módulo de segurança (somente chave simétrica).

- **Modo de** autenticação do dispositivo<br>
Neste método, o agente de segurança autentica primeiro com a identidade do dispositivo. Após a autenticação inicial, o Azure Security Center for IoT agent realiza uma chamada **REST** para o IoT Hub usando a API REST com os dados de autenticação do dispositivo. O Azure Security Center para agente IoT solicita então o método de autenticação do módulo de segurança e os dados do IoT Hub. Na etapa final, o Azure Security Center for IoT agent realiza uma autenticação contra o módulo Azure Security Center for IoT.

Use este tipo de autenticação se quiser que o agente de segurança reutilize um método de autenticação de dispositivo existente (certificado auto-assinado ou chave simétrica).

Consulte [os parâmetros de instalação do agente de segurança](#security-agent-installation-parameters) para saber como configurar.

## <a name="authentication-methods-known-limitations"></a>Métodos de autenticação conhecidos limitações

- O modo de autenticação **securitymodule** só suporta autenticação de chave simétrica.
- O certificado assinado por CA não é suportado pelo modo de autenticação **do dispositivo.**

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

[Ao implantar um agente de segurança,](how-to-deploy-agent.md)os detalhes de autenticação devem ser fornecidos como argumentos.
Esses argumentos estão documentados na tabela a seguir.

|Nome do parâmetro Linux | Nome do parâmetro do Windows | Parâmetro de taquigrafia |Descrição|Opções|
|---------------------|---------------|---------|---------------|---------------|
|autenticação-identidade|AutenticaçãoIdentidade|Aui|Identidade de autenticação| **Módulo de segurança** ou **dispositivo**|
|método de autenticação|Authenticationmethod|Aum|Método de autenticação|**SimmetricKey** ou **SelfSignedCertificate**|
|caminho de arquivo|FilePath|f|Caminho completo absoluto para o arquivo que contém o certificado ou a chave simétrica| |
|nome de host|HostName|hn|FQDN do Hub IoT|Exemplo: ContosoIotHub.azure-devices.net|
|id dispositivo|deviceId|Di|Id do Dispositivo|Exemplo: MyDevice1|
|certificado-localização-tipo|CertificateLocationKind|Cl|Localização de armazenamento de certificados|**LocalFile** ou **Loja**|
|

Ao usar o script do agente de segurança de instalação, a configuração a seguir é executada automaticamente. Para editar manualmente a autenticação do agente de segurança, edite o arquivo de configuração.

## <a name="change-authentication-method-after-deployment"></a>Alterar o método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, um arquivo de configuração é criado automaticamente.

Para alterar os métodos de autenticação após a implantação, é necessária a edição manual do arquivo de configuração.

### <a name="c-based-security-agent"></a>Agente de segurança baseado em C#

Editar _autenticação.configuração_ com os seguintes parâmetros:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agente de segurança baseado em C

Editar _LocalConfiguration.json_ com os seguintes parâmetros:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Confira também

- [Visão geral dos agentes de segurança](security-agent-architecture.md)
- [Implantar agente de segurança](how-to-deploy-agent.md)
- [Acessar dados de segurança brutos](how-to-security-data-access.md)
