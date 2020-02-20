---
title: Instalar certificados no dispositivo-Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste e saiba como instalá-los em um dispositivo Azure IoT Edge para preparar a implantação de produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9f715a6ab43206d02107f6335e9b4c0bb4266e0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470999"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Instalar certificados de produção em um dispositivo IoT Edge

Todos os dispositivos IoT Edge usam certificados para criar conexões seguras entre o tempo de execução e os módulos em execução no dispositivo.
IoT Edge dispositivos funcionando como gateways usam esses mesmos certificados para se conectar aos dispositivos downstream também.

Quando você instala o IoT Edge e provisiona o dispositivo pela primeira vez, o dispositivo é configurado com certificados temporários para que você possa testar o serviço.
Esses certificados temporários expiram em 90 dias ou podem ser redefinidos reiniciando o computador.
Quando estiver pronto para mover seus dispositivos para um cenário de produção ou se desejar criar um cenário de gateway, você precisará fornecer seus próprios certificados.
Este artigo demonstra as etapas para instalar certificados em seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e suas funções em um cenário de IoT Edge, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

>[!NOTE]
>O termo "autoridade de certificação raiz" usado em todo este artigo refere-se ao certificado público de autoridade superior da cadeia de certificados para sua solução de IoT. Você não precisa usar a raiz do certificado de uma autoridade de certificação agregada ou a raiz da autoridade de certificação de sua organização. Em muitos casos, é, na verdade, um certificado público de CA intermediário.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Um dispositivo IoT Edge, em execução no [Windows](how-to-install-iot-edge-windows.md) ou no [Linux](how-to-install-iot-edge-linux.md).
* Ter um certificado de AC (autoridade de certificação) raiz, autoassinado ou comprado de uma autoridade de certificação comercial confiável, como Baltimore, Verisign, DigiCert ou GlobalSign.

Se você ainda não tiver uma autoridade de certificação raiz, mas quiser experimentar IoT Edge recursos que exigem certificados de produção (como cenários de gateway), poderá [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Criar certificados de produção

Você deve usar sua própria autoridade de certificação para criar os seguintes arquivos:

* AC raiz
* Certificado de autoridade de certificação de dispositivo
* Chave privada da AC do dispositivo

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificação mais alta para uma organização. É a mais alta autoridade de certificação para o cenário de IoT Edge, que o IoT Edge módulo de Hub, os módulos de usuário e os dispositivos downstream usam para estabelecer a confiança entre si.

Para ver um exemplo desses certificados, examine os scripts que criam certificados de demonstração em [gerenciando certificados de AC de teste para exemplos e tutoriais](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Instalar certificados no dispositivo

Instale a cadeia de certificados no dispositivo IoT Edge e configure o tempo de execução IoT Edge para fazer referência aos novos certificados.

Por exemplo, se você usou os scripts de exemplo para [criar certificados de demonstração](how-to-create-test-certificates.md), os três arquivos que você precisa copiar para o dispositivo IOT Edge são os seguintes:

* Certificado de autoridade de certificação do dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada da AC do dispositivo: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* AC raiz: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copie os três arquivos de certificado e de chave em seu dispositivo IoT Edge.

   Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.  Se você tiver gerado os certificados no próprio dispositivo IoT Edge, poderá ignorar esta etapa e usar o caminho para o diretório de trabalho.

2. Abra o artigo de configuração do daemon de segurança do IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Defina as propriedades do **certificado** no arquivo config. YAML como o caminho completo para o certificado e os arquivos de chave no dispositivo IOT Edge. Remova o caractere de `#` antes das propriedades do certificado para remover os comentários das quatro linhas. Verifique se a linha **certificados:** não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços. Por exemplo:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

4. Em dispositivos Linux, verifique se o usuário **iotedge** tem permissões de leitura para o diretório que contém os certificados.

5. Se você tiver usado outros certificados para IoT Edge no dispositivo antes, exclua os arquivos nos dois diretórios a seguir antes de iniciar ou reiniciar IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

A instalação de certificados em um dispositivo IoT Edge é uma etapa necessária antes de implantar sua solução em produção. Saiba mais sobre como [se preparar para implantar sua solução de IOT Edge em produção](production-checklist.md).
