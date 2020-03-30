---
title: Gerenciar certificados de dispositivo - Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste, instale e gerencie-os em um dispositivo Azure IoT Edge para se preparar para a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539202"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gerenciar certificados em um dispositivo IoT Edge

Todos os dispositivos IoT Edge usam certificados para criar conexões seguras entre o tempo de execução e quaisquer módulos em execução no dispositivo. Os dispositivos IoT Edge que funcionam como gateways também usam esses mesmos certificados para se conectar em seus dispositivos a jusante.

## <a name="install-production-certificates"></a>Instalar certificados de produção

Quando você instala o IoT Edge pela primeira vez e provisiona seu dispositivo, o dispositivo é configurado com certificados temporários para que você possa testar o serviço.
Esses certificados temporários expiram em 90 dias, ou podem ser reiniciados reiniciando sua máquina.
Uma vez que você esteja pronto para mover seus dispositivos para um cenário de produção, ou você deseja criar um cenário de gateway, você precisa fornecer seus próprios certificados.
Este artigo demonstra as etapas para instalar certificados em seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e suas funções em um cenário de IoT Edge, consulte [Entenda como o Azure IoT Edge usa certificados](iot-edge-certs.md).

>[!NOTE]
>O termo "CA raiz" usado ao longo deste artigo refere-se ao certificado público de maior autoridade da cadeia de certificados para sua solução de IoT. Você não precisa usar a raiz do certificado de uma autoridade de certificado sindicalizado, ou a raiz da autoridade de certificado de sua organização. Em muitos casos, é na verdade um certificado público de AC intermediário.

### <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo IoT Edge, em execução no [Windows](how-to-install-iot-edge-windows.md) ou [Linux](how-to-install-iot-edge-linux.md).
* Tenha um certificado de autoridade de certificado raiz (CA), auto-assinado ou comprado de uma autoridade confiável de certificados comerciais como Baltimore, Verisign, DigiCert ou GlobalSign.

Se você ainda não tem uma autoridade de certificado raiz, mas deseja experimentar recursos do IoT Edge que requerem certificados de produção (como cenários de gateway) você pode [criar certificados de demonstração para testar os recursos do dispositivo IoT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Criar certificados de produção

Você deve usar sua própria autoridade de certificado para criar os seguintes arquivos:

* AC Raiz
* Certificado de autoridade de certificação de dispositivo
* Chave privada do dispositivo CA

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificado mais alta para uma organização. É a autoridade de certificado mais alta para o cenário ioT Edge, que o módulo hub IoT Edge, módulos de usuário e quaisquer dispositivos a jusante usam para estabelecer confiança entre si.

Para ver um exemplo desses certificados, revise os scripts que criam certificados de demonstração no [gerenciamento de certificados ca de teste para amostras e tutoriais](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instale certificados no dispositivo

Instale sua cadeia de certificados no dispositivo IoT Edge e configure o tempo de execução do IoT Edge para fazer referência aos novos certificados.

Por exemplo, se você usou os scripts de exemplo para [criar certificados de demonstração,](how-to-create-test-certificates.md)copie os seguintes arquivos no dispositivo IoT-Edge:

* Certificado de CA do dispositivo:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada do dispositivo CA:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Raiz CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copie os três certificados e os arquivos-chave no seu dispositivo IoT Edge.

   Você pode usar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo Secure [copy](https://www.ssh.com/ssh/scp/) para mover os arquivos do certificado.  Se você gerou os certificados no próprio dispositivo IoT Edge, você pode pular essa etapa e usar o caminho para o diretório de trabalho.

1. Abra o artigo de configuração do daemon de segurança do IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Defina as propriedades do **certificado** no arquivo config.yaml para o caminho completo para o certificado e os arquivos-chave no dispositivo IoT Edge. Remova `#` o caractere antes que o certificado seja descomentado das quatro linhas. Certifique-se de que os **certificados:** a linha não tem espaço em branco anterior e que os itens aninhados são recuados por dois espaços. Por exemplo: 

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

1. Nos dispositivos Linux, certifique-se de que o **iotedge** do usuário tenha permissões de leitura para o diretório que possui os certificados.

1. Se você já usou outros certificados para IoT Edge no dispositivo antes, exclua os arquivos nos dois diretórios a seguir antes de iniciar ou reiniciar o IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` e`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Personalize a vida útil do certificado

O IoT Edge gera automaticamente certificados no dispositivo em vários casos, incluindo:

* Se você não fornecer seus próprios certificados de produção quando instalar e fornecer ioT Edge, o gerenciador de segurança IoT Edge automaticamente gerará um **certificado CA do dispositivo**. Este certificado auto-assinado destina-se apenas a cenários de desenvolvimento e teste, não à produção. Este certificado expira após 90 dias.
* O gerenciador de segurança IoT Edge também gera um **certificado ca de carga de trabalho** assinado pelo certificado CA do dispositivo

Para obter mais informações sobre a função dos diferentes certificados em um dispositivo IoT Edge, consulte [Entenda como o Azure IoT Edge usa certificados](iot-edge-certs.md).

Para esses dois certificados gerados automaticamente, você tem a opção de definir o **auto_generated_ca_lifetime_days** sinalizador em config.yaml para configurar o número de dias para a vida útil dos certificados.

>[!NOTE]
>Há um terceiro certificado gerado automaticamente que o gerenciador de segurança IoT Edge cria, o **certificado de servidor do hub IoT Edge**. Este certificado sempre tem um prazo de 90 dias, mas é automaticamente renovado antes de expirar. O valor **auto_generated_ca_lifetime_days** não afeta este certificado.

Para configurar o vencimento do certificado para algo diferente dos 90 dias padrão, adicione o valor em dias à seção de **certificados** do arquivo config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Se você forneceu certificados CA do seu próprio dispositivo, então esse valor ainda se aplica ao certificado ca de carga de trabalho, desde que o valor vitalício que você definiu seja menor do que a vida útil do certificado CA do dispositivo.

Depois de especificar o sinalizador no arquivo config.yaml, tome as seguintes etapas:

1. Exclua o `hsm` conteúdo da pasta.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Reinicie o serviço IoT Edge.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confirme a configuração vitalícia.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Verifique a saída da prontidão de **produção: verificação de certificados,** que lista o número de dias até que os certificados CA do dispositivo gerado automaticamente expirem.

## <a name="next-steps"></a>Próximas etapas

A instalação de certificados em um dispositivo IoT Edge é um passo necessário antes de implantar sua solução na produção. Saiba mais sobre como [se preparar para implantar sua solução IoT Edge na produção.](production-checklist.md)
