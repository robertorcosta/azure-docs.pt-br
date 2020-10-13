---
title: Provisionar com certificados X. 509-Azure IoT Edge | Microsoft Docs
description: Após a instalação, provisione um dispositivo IoT Edge com seus certificados de identidade do dispositivo e autentique-o no Hub IoT
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 8cfb7c5a0821bd030252a105b98b1c138b9ef820
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979377"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Configurar um dispositivo Azure IoT Edge com autenticação de certificado X. 509

Este artigo fornece as etapas para registrar um novo dispositivo IoT Edge no Hub IoT e configurar o dispositivo para autenticar com certificados X. 509.

As etapas neste artigo percorrem um processo chamado provisionamento manual, no qual você conecta cada dispositivo ao Hub IoT manualmente. A alternativa é o provisionamento automático usando o serviço de provisionamento de dispositivos no Hub IoT, que é útil quando você tem muitos dispositivos para provisionar.

<!--TODO: Add auto-provision info/links-->

Para o provisionamento manual, você tem duas opções para autenticar dispositivos IoT Edge:

* **Chave simétrica**: quando você cria uma nova identidade de dispositivo no Hub IOT, o serviço cria duas chaves. Você coloca uma das chaves no dispositivo e apresenta a chave para o Hub IoT durante a autenticação.

  Esse método de autenticação é mais rápido para começar, mas não tão seguro.

* **X. 509 auto-assinado**: você cria dois certificados de identidade x. 509 e os coloca no dispositivo. Ao criar uma nova identidade de dispositivo no Hub IoT, você fornece impressões digitais de ambos os certificados. Quando o dispositivo é autenticado no Hub IoT, ele apresenta seus certificados e o Hub IoT pode verificar se eles correspondem às impressões digitais.

  Esse método de autenticação é mais seguro e recomendado para cenários de produção.

Este artigo percorre o processo de registro e provisionamento com a autenticação de certificado X. 509. Se você quiser saber como configurar um dispositivo com chaves simétricas, consulte [configurar um dispositivo Azure IOT Edge com autenticação de chave simétrica](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as etapas neste artigo, você deve ter um dispositivo com o IoT Edge Runtime instalado. Caso contrário, siga as etapas em [instalar ou desinstalar o Azure IOT Edge Runtime](how-to-install-iot-edge.md).

O provisionamento manual com certificados X. 509 requer IoT Edge versão 1.0.10 ou mais recente.

## <a name="create-certificates-and-thumbprints"></a>Criar certificados e impressões digitais



<!-- TODO -->

## <a name="register-a-new-device"></a>Registrar um novo dispositivo

Cada dispositivo que se conecta a um hub IoT tem uma ID de dispositivo que é usada para controlar as comunicações da nuvem para o dispositivo ou do dispositivo para a nuvem. Você configura um dispositivo com suas informações de conexão que incluem o nome de host do Hub IoT, a ID do dispositivo e as informações que o dispositivo usa para autenticar no Hub IoT.

Para a autenticação de certificado X. 509, essas informações são fornecidas na forma de *impressões digitais* obtidas dos certificados de identidade do dispositivo. Essas impressões digitais são dadas ao Hub IoT no momento do registro do dispositivo para que o serviço possa reconhecer o dispositivo quando ele se conectar.

Você pode usar várias ferramentas para registrar um novo dispositivo IoT Edge no Hub IoT e carregar suas impressões digitais de certificado. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos do portal do Azure

Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo do IoT Edge no portal do Azure

No hub IoT do portal do Azure, os dispositivos do IoT Edge são criados e gerenciados separadamente dos dispositivos de IoT que não são habilitados para a borda.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

1. No painel esquerdo, selecione **IOT Edge** no menu e, em seguida, selecione **Adicionar um dispositivo de IOT Edge**.

   ![Adicionar um dispositivo de IoT Edge da portal do Azure](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na página **criar um dispositivo** , forneça as seguintes informações:

   * Crie uma ID de dispositivo descritiva. Anote essa ID do dispositivo, pois você a usará na próxima seção.
   * Selecione **X.509 autoassinado** como o tipo de autenticação.
   * Forneça as impressões digitais do certificado de identidade primário e secundário. Os valores de impressão digital são caracteres 40-hex para hashes SHA-1 ou caracteres 64-hex para hashes SHA-256.

1. Clique em **Salvar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Exibir dispositivos do IoT Edge no portal do Azure

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos da CLI do Azure

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.70 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Esse comando inclui vários parâmetros:

* `--device-id` ou `-d` : forneça um nome descritivo que seja exclusivo para o Hub IOT. Anote essa ID do dispositivo, pois você a usará na próxima seção.
* `hub-name` ou `-n` : forneça o nome do seu hub IOT.
* `--edge-enabled` ou `--ee` : declare que o dispositivo é um dispositivo IOT Edge.
* `--auth-method` ou `--am` : declare o tipo de autorização que o dispositivo vai usar. Nesse caso, estamos usando as impressões digitais do certificado X. 509.
* `--primary-thumbprint` ou `--ptp` : forneça uma impressão digital do certificado X. 509 para usar como chave primária.
* `--secondary-thumbprint` ou `--stp` : forneça uma impressão digital do certificado X. 509 para usar como uma chave secundária.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para exibir todos os dispositivos no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Adicione o sinalizador `--edge-enabled` ou `--ee` para listar somente IOT Edge dispositivos em seu hub IOT.

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

--- 

## <a name="configure-an-iot-edge-device"></a>Configurar um dispositivo do IoT Edge

Depois que o dispositivo IoT Edge tiver uma identidade no Hub IoT, você precisará configurar o dispositivo com sua identidade de nuvem, bem como seus certificados de identidade.

Em um dispositivo Linux, você fornece essas informações editando um arquivo config. YAML. Em um dispositivo Windows, você fornece essas informações executando um script do PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

1. No dispositivo IoT Edge, abra o arquivo de configuração.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Localize a seção Configurações de provisionamento do arquivo. 

1. Comente a **configuração de provisionamento manual usando uma seção de cadeia de conexão** .

1. Remova a marca de comentário da **configuração de provisionamento manual usando uma seção do certificado de identidade X. 509** . Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Atualize os seguintes campos:

   * **iothub_hostname**: nome do host do Hub IOT ao qual o dispositivo se conectará. Por exemplo, `{IoT hub name}.azure-devices.net`.
   * **DEVICE_ID**: a ID que você forneceu quando registrou o dispositivo.
   * **identity_cert**: URI para um certificado de identidade no dispositivo. Por exemplo, `file:///path/identity_certificate.pem`.
   * **identity_pk**: URI para o arquivo de chave privada para o certificado de identidade fornecido. Por exemplo, `file:///path/identity_key.pem`.

1. Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

1. Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. No dispositivo IoT Edge, execute o PowerShell como administrador.

2. Use o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de execução de IOT Edge em seu computador.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Se você estiver usando contêineres do Linux, adicione o `-ContainerOs` parâmetro ao sinalizador. Seja consistente com a opção de contêiner que você escolheu com o `Deploy-IoTEdge` comando que você executou anteriormente.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Se você baixou o script IoTEdgeSecurityDaemon.ps1 em seu dispositivo para uma instalação de versão específica ou offline, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça as seguintes informações:

   * **IotHubHostName**: nome do host do Hub IOT ao qual o dispositivo se conectará. Por exemplo, `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: a ID que você forneceu quando registrou o dispositivo.
   * **X509IdentityCertificate**: caminho absoluto para um certificado de identidade no dispositivo. Por exemplo, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: caminho absoluto para o arquivo de chave privada para o certificado de identidade fornecido. Por exemplo, `C:\path\identity_key.pem`.

Ao provisionar um dispositivo manualmente, você pode usar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem de contêiner edgeAgent específica e forneça credenciais se ela estiver em um registro privado

Para obter mais informações sobre esses parâmetros adicionais, consulte [scripts do PowerShell para IOT Edge no Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Próximas etapas

Continue a [implantar módulos IOT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos em seu dispositivo.
