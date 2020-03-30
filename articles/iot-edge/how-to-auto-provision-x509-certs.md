---
title: Provisionar automaticamente dispositivos com DPS usando certificados X.509 - Azure IoT Edge | Microsoft Docs
description: Use certificados X.509 para testar o provisionamento automático de dispositivos para OZure IoT Edge com serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537356"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Criar e provisionar um dispositivo IoT Edge usando certificados X.509

Com o [DPS (Azure IoT Hub Device Provisioning Service, serviço de provisionamento de dispositivos hub) do Azure,](../iot-dps/index.yml)você pode provisionar automaticamente dispositivos IoT Edge usando certificados X.509. Se você não estiver familiarizado com o processo de provisionamento automático, revise os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra como criar um registro do Serviço de Provisionamento de Dispositivos usando certificados X.509 em um dispositivo IoT Edge com as seguintes etapas:

* Gerar certificados e chaves.
* Crie uma inscrição individual para um dispositivo ou uma matrícula em grupo para um conjunto de dispositivos.
* Instale o tempo de execução do IoT Edge e registre o dispositivo no IoT Hub.

Usar certificados X.509 como um mecanismo de atestado é uma maneira excelente de escalar a produção e simplificar o provisionamento do dispositivo. Normalmente, os certificados X.509 são dispostos em uma cadeia de confiança de certificados. Começando com um certificado raiz auto-assinado ou confiável, cada certificado na cadeia assina o próximo certificado inferior. Esse padrão cria uma cadeia de confiança delegada desde o certificado raiz até cada certificado intermediário até o certificado final "folha" instalado em um dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

* Um Hub IoT ativo.
* Um dispositivo físico ou virtual para ser o dispositivo IoT Edge.
* A versão mais recente do [Git](https://git-scm.com/download/) instalada.
* Um exemplo do Serviço de Provisionamento de Dispositivos IoT Hub no Azure, vinculado ao seu hub IoT.
  * Se você não tiver uma instância de serviço de provisionamento de dispositivos, siga as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).
  * Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="generate-device-identity-certificates"></a>Gerar certificados de identidade de dispositivos

O certificado de identidade do dispositivo é um certificado de folha que se conecta através de uma cadeia de certificados de confiança ao certificado de autoridade de certificado X.509 (CA) superior. O certificado de identidade do dispositivo deve ter o nome comum (CN) definido no ID do dispositivo que você deseja que o dispositivo tenha em seu hub IoT.

Os certificados de identidade do dispositivo são usados apenas para provisionar o dispositivo IoT Edge e autenticar o dispositivo com o Azure IoT Hub. Eles não estão assinando certificados, ao contrário dos certificados CA que o dispositivo IoT Edge apresenta a módulos ou dispositivos folha para verificação. Para obter mais informações, consulte [os detalhes de uso do certificado Azure IoT Edge](iot-edge-certs.md).

Depois de criar o certificado de identidade do dispositivo, você deve ter dois arquivos: um arquivo .cer ou .pem que contenha a parte pública do certificado e um arquivo .cer ou .pem com a chave privada do certificado. Se você planeja usar a matrícula em grupo no DPS, você também precisa da parte pública de um certificado de CA intermediário ou raiz na mesma cadeia de certificados de confiança.

### <a name="use-test-certificates"></a>Use certificados de teste

Se você não tem uma autoridade de certificado disponível para criar novas certs de identidade e deseja experimentar esse cenário, o repositório git do Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste. Estes certificados são projetados apenas para testes de desenvolvimento e não devem ser usados na produção.

Para criar certificados de teste, siga as etapas em [Criar certificados de demonstração para testar os recursos do dispositivo IoT Edge](how-to-create-test-certificates.md). Complete as duas seções necessárias para configurar os scripts de geração de certificados e criar um certificado de CA raiz. Em seguida, siga as etapas para criar um certificado de identidade do dispositivo. Quando terminar, você deve ter a seguinte cadeia de certificados e o par de chaves:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Você precisa desses dois certificados no dispositivo IoT Edge. Se você vai usar a matrícula individual no DPS, então você carregará o arquivo .cert.pem. Se você vai usar a matrícula em grupo no DPS, então você também precisa de um certificado CA intermediário ou raiz na mesma cadeia de certificados para carregar. Se você estiver usando certs dedemonstração, use o `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certificado para inscrição em grupo.

## <a name="create-a-dps-individual-enrollment"></a>Crie uma matrícula individual DPS

Use seus certificados e chaves gerados para criar uma inscrição individual no DPS para um único dispositivo IoT Edge. As matrículas individuais levam a parte pública do certificado de identidade de um dispositivo e correspondem ao certificado no dispositivo.

Se você estiver procurando provisionar vários dispositivos IoT Edge, siga as etapas na próxima seção, crie uma inscrição em [grupo DPS](#create-a-dps-group-enrollment).

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

Para obter mais informações sobre inscrições no Serviço de Provisionamento de Dispositivos, consulte [Como gerenciar as inscrições de dispositivos](../iot-dps/how-to-manage-enrollments.md).

1. No [portal Azure,](https://portal.azure.com)navegue até a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Configurações**, selecione **Gerenciar registros**.

1. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   * **Mecanismo**: Selecione **X.509**.

   * **Certificado Primário .pem ou arquivo .cer :** Carregue o arquivo público do certificado de identidade do dispositivo. Se você usou os scripts para gerar um certificado de teste, escolha o seguinte arquivo:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **ID do dispositivo ioT hub**: Forneça um ID para o seu dispositivo, se quiser. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. Se você não fornecer um ID do dispositivo, o nome comum (CN) no certificado X.509 é usado.

   * **Dispositivo IoT Edge :** Selecione **True** para declarar que a inscrição é para um dispositivo IoT Edge.

   * **Selecione os hubs de IoT aos quais este dispositivo pode ser atribuído:** Escolha o hub IoT vinculado ao qual deseja conectar seu dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   * **Dispositivo inicial Estado gêmeo**: Adicione um valor de tag a ser adicionado ao dispositivo gêmeo, se quiser. Você pode usar tags para grupos de destino de dispositivos para implantação automática. Por exemplo: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selecione **Salvar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Continue até instalar a seção [De tempo de execução IoT Edge](#install-the-iot-edge-runtime) para configurar seu dispositivo IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Crie uma inscrição em grupo DPS

Use seus certificados e chaves gerados para criar uma inscrição em grupo no DPS para vários dispositivos IoT Edge. As inscrições em grupo usam um certificado de CA intermediário ou raiz da cadeia de confiança de certificados usado para gerar os certificados de identidade de dispositivos individuais.

Se você estiver procurando provisionar um único dispositivo IoT Edge, siga as etapas da seção anterior, [Crie uma inscrição individual dps](#create-a-dps-individual-enrollment).

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

### <a name="verify-your-root-certificate"></a>Verifique seu certificado raiz

Quando você cria um grupo de inscrição, você tem a opção de usar um certificado verificado. Você pode verificar um certificado com DPS comprovando que você tem propriedade do certificado raiz. Para obter mais informações, consulte [Como fazer o comprovante de posse para certificados DE CA X.509](../iot-dps/how-to-verify-certificates.md).

1. No [portal Azure,](https://portal.azure.com)navegue até a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Selecione **Certificados** no menu à esquerda.

1. Selecione **Adicionar** para adicionar um novo certificado.

1. Digite um nome amigável para o seu certificado e navegue até o arquivo .cer ou .pem que representa a parte pública do seu certificado X.509.

   Se você estiver usando os certificados `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` de demonstração, faça upload do certificado.

1. Selecione **Salvar**.

1. Seu certificado agora deve ser listado na página **Certificados.** Selecione-o para abrir os detalhes do certificado.

1. Selecione **Gerar código de verificação** e copie o código gerado.

1. Se você trouxe seu próprio certificado ca ou está usando os certificados de demonstração, você pode usar a ferramenta de verificação fornecida no repositório IoT Edge para verificar o comprovante de posse. A ferramenta de verificação usa seu certificado CA para assinar um novo certificado que tem o código de verificação fornecido como o nome do assunto.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na mesma página de detalhes do certificado no portal Azure, faça upload do certificado de verificação recém-gerado.

1. Selecione **Verificar**.

### <a name="create-enrollment-group"></a>Criar grupo de inscrição

Para obter mais informações sobre inscrições no Serviço de Provisionamento de Dispositivos, consulte [Como gerenciar as inscrições de dispositivos](../iot-dps/how-to-manage-enrollments.md).

1. No [portal Azure,](https://portal.azure.com)navegue até a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Configurações**, selecione **Gerenciar registros**.

1. Selecione **Adicionar grupo de inscrição** e preencha as seguintes etapas para configurar a inscrição:

   * **Nome do grupo**: Forneça um nome memorável para a matrícula deste grupo.

   * **Tipo de atestado**: Certificado de **seleção**.

   * **Dispositivo IoT Edge**: Selecione **True**. Para uma inscrição em grupo, todos os dispositivos devem ser dispositivos IoT Edge ou nenhum deles pode ser.

   * **Tipo de certificado**: Selecione **o certificado CA** se você tiver um certificado CA verificado armazenado com DPS ou Certificado **Intermediário** se você quiser carregar um novo arquivo para apenas esta inscrição.

   * **Certificado principal**: Se você escolheu o certificado CA na última seção, escolha seu certificado na lista de isato. Se você escolheu o certificado intermediário, carregue o arquivo público de um certificado ca na cadeia de confiança de certificados que foi usado para gerar os certificados de identidade do dispositivo.

   * **Selecione os hubs de IoT aos quais este dispositivo pode ser atribuído:** Escolha o hub IoT vinculado ao qual deseja conectar seu dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   * **Dispositivo inicial Estado gêmeo**: Adicione um valor de tag a ser adicionado ao dispositivo gêmeo, se quiser. Você pode usar tags para grupos de destino de dispositivos para implantação automática. Por exemplo: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selecione **Salvar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Continue até a próxima seção para configurar seu dispositivo IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

O provisionamento X.509 com DPS só é suportado na versão 1.0.9 do IoT Edge ou mais recente.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor **do escopo do DPS ID.** Você pode recuperar esse valor na página de visão geral da instância DPS no portal Azure.
* O arquivo de certificado de identidade do dispositivo no dispositivo.
* O arquivo de chave de identidade do dispositivo no dispositivo.
* Um ID de registro opcional (retirado do nome comum no certificado de identidade do dispositivo, se não for fornecido).

### <a name="linux-device"></a>Dispositivo Linux

Use o link a seguir para instalar o tempo de execução do Azure IoT Edge em seu dispositivo, usando os comandos apropriados para a arquitetura do dispositivo. Quando você chegar à seção sobre a configuração do daemon de segurança, configure o tempo de execução do IoT Edge para x.509 de provisionamento automático, não manual. Você deve ter todas as informações e arquivos de certificado que você precisa depois de completar as seções anteriores deste artigo.

[Instale o tempo de execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

Quando você adiciona o certificado X.509 e as informações-chave ao arquivo config.yaml, os caminhos devem ser fornecidos como URIs de arquivo. Por exemplo: 

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

A seção no arquivo de configuração do provisionamento automático X.509 é assim:

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Substitua os valores `identity_cert` `identity_pk` do espaço reservado para `scope_id`, com o ID de escopo da instância DPS e as URIs para os locais de arquivo cert e chave em seu dispositivo. Forneça `registration_id` um dispositivo se quiser, ou deixe esta linha comentada para registrar o dispositivo com o nome CN do certificado de identidade.

Sempre reinicie o daemon de segurança depois de atualizar o arquivo config.yaml.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Dispositivo Windows

Instale o tempo de execução do IoT Edge no dispositivo para o qual você gerou o certificado de identidade e a chave de identidade. Você configurará o tempo de execução do IoT Edge para provisionamento automático, não manual.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerenciar contêineres e atualizar o IoT Edge, consulte [Instalar o tempo de execução do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a máquina do Windows está em uma versão suportada, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do moby e o tempo de execução do IoT Edge. O comando é padrão para usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Se sim, reinicie o dispositivo agora. Assim que o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando é padrão para provisionamento `-Dps` manual, a menos que você use o sinalizador para usar provisionamento automático.

   Substitua os valores `{identity cert path}`do `{identity key path}` espaço reservado para `{scope_id}`, e com os valores apropriados da sua instância DPS e os caminhos de arquivo em seu dispositivo. Se você quiser especificar o `-RegistrationId {registration_id}` ID de registro, inclua também, substituindo o espaço reservado conforme apropriado.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >O arquivo config.yaml armazena seu certificado e informações-chave como URIs de arquivo. No entanto, o comando Initialize-IoTEdge lida com essa etapa de formatação para você, para que você possa fornecer o caminho absoluto para o certificado e arquivos-chave em seu dispositivo.

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo.

Você pode verificar se a inscrição individual que você criou no Serviço de Provisionamento de Dispositivos foi usada. Navegue até a instância do Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes de inscrição para a matrícula individual que você criou. Observe que o status da inscrição é **atribuído** e o ID do dispositivo está listado.

Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.

### <a name="linux-device"></a>Dispositivo Linux

Verifique o status do serviço do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os registros de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Módulos de execução da lista.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registros de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Aprenda a [implantar e monitorar módulos IoT Edge em escala usando o portal Azure](how-to-deploy-monitor.md) ou usando o [Azure CLI](how-to-deploy-monitor-cli.md).
