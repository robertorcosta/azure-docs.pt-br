---
title: Dispositivo de provisão usando atestado de chave simétrica - Azure IoT Edge
description: Use atestado de chave simétrica para testar o provisionamento automático de dispositivos para a zure IoT Edge com serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 139a2cafe137d000b991cbad8b8567e005ffc728
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668667"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Criar e provisionar um dispositivo IoT Edge usando atestado de chave simétrica

Os dispositivos Azure IoT Edge podem ser provisionados automaticamente usando o [Serviço de Provisionamento de Dispositivos,](../iot-dps/index.yml) assim como dispositivos que não estão habilitados para bordas. Se você não estiver familiarizado com o processo de provisionamento automático, revise os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra como criar um serviço de provisionamento de dispositivos cadastramento individual usando atestado de chave simétrica em um dispositivo IoT Edge com as seguintes etapas:

* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um registro individual para o dispositivo.
* Instale o tempo de execução do IoT Edge e conecte-se ao IoT Hub.

O atestado de chave simétrica é uma abordagem simples para autenticar o dispositivo com uma instância do serviço de provisionamento de dispositivos. Esse método de atestado representa uma experiência de "Olá, Mundo" para desenvolvedores que são novos no provisionamento de dispositivos ou não tem requisitos de segurança rígidos. O atestado de dispositivo usando um certificado [TPM](../iot-dps/concepts-tpm-attestation.md) ou [X.509](../iot-dps/concepts-security.md#x509-certificates) é mais seguro e deve ser usado para requisitos de segurança mais rigorosos.

## <a name="prerequisites"></a>Pré-requisitos

* Um Hub ioT ativo
* Um dispositivo físico ou virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolher uma ID de registro exclusiva para o dispositivo

Uma ID de registro exclusiva deve ser definida para identificar cada dispositivo. Você pode usar o endereço MAC, o número de série ou qualquer informação exclusiva do dispositivo.

Neste exemplo, usamos uma combinação de um endereço MAC e um número `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`de série formando a seguinte seqüência para um ID de registro: .

Crie uma ID de registro exclusiva para seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e traço ('-').

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Use o ID de registro do seu dispositivo para criar uma inscrição individual no DPS.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

> [!TIP]
> As matrículas em grupo também são possíveis ao utilizar atestado de chave simétrica e envolvem as mesmas decisões que as matrículas individuais.

1. No [portal Azure,](https://portal.azure.com)navegue até a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Configurações**, selecione **Gerenciar registros**.

1. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   1. Para **mecanismo,** selecione **Chave Simétrica**.

   1. Selecione a caixa de seleção **de chaves de geração** automática.

   1. Forneça o **ID de registro** que você criou para o seu dispositivo.

   1. Forneça um **ID de dispositivo hub IoT** para o seu dispositivo, se quiser. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. Se você não fornecer um ID do dispositivo, o iD de registro é usado.

   1. Selecione **True** para declarar que a inscrição é para um dispositivo IoT Edge. Para uma inscrição em grupo, todos os dispositivos devem ser dispositivos IoT Edge ou nenhum deles pode ser.

   > [!TIP]
   > Na CLI do Azure, você pode criar uma [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e usar o sinalizador **habilitado** para borda para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

   1. Aceite o valor padrão da política de alocação do Serviço de Provisionamento de Dispositivos para **saber como você deseja atribuir dispositivos a hubs** ou escolher um valor diferente específico para essa inscrição.

   1. Escolha o **IoT Hub** vinculado que você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   1. Escolha **como deseja que os dados do dispositivo sejam tratados no reprovisionamento** quando os dispositivos solicitarem provisionamento após a primeira vez.

   1. Adicionar um valor de marca para o **estado inicial do dispositivo gêmeo** se desejar. Você pode usar marcas para grupos de dispositivos de destino para a implantação do módulo. Por exemplo:

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

   1. **Certifique-se de que a entrada** enable está definida como **Ativação**.

   1. Clique em **Salvar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Certifique-se de copiar o valor principal da **chave primária** da sua inscrição para usar ao instalar o tempo de execução do IoT Edge ou se você vai criar chaves de dispositivo para uso com uma inscrição em grupo.

## <a name="derive-a-device-key"></a>Derivar uma chave de dispositivo

> [!NOTE]
> Esta seção só é necessária se usar uma matrícula em grupo.

Cada dispositivo usa sua chave de dispositivo derivada com seu ID de registro exclusivo para executar atestado de chave simétrica com a inscrição durante o provisionamento. Para gerar a chave do dispositivo, use a chave que você copiou da sua inscrição no DPS para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registro exclusivo para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave primária ou secundária da sua matrícula no código do dispositivo.

### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se estiver usando uma estação de trabalho do Linux, você poderá usar openssl para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

Substitua o valor do **REG_ID** pelo ID de registro do seu dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em Windows

Se estiver usando uma estação de trabalho baseada no Windows, você poderá usar o PowerShell para gerar sua chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

Substitua o valor do **REG_ID** pelo ID de registro do seu dispositivo.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor **do escopo do DPS ID**
* O **ID de registro do** dispositivo que você criou
* A **chave primária** que você copiou da matrícula do DPS

> [!TIP]
> Para as matrículas em grupo, você precisa da [chave derivada de](#derive-a-device-key) cada dispositivo em vez da chave de inscrição DPS.

### <a name="linux-device"></a>Dispositivo Linux

Siga as instruções para a arquitetura do seu dispositivo. Certifique-se de configurar o runtime do IoT Edge para provisionamento automático, não manual.

[Instale o tempo de execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

A seção no arquivo de configuração para provisionamento de chaves simétricas é assim:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Substitua os valores `<REGISTRATION_ID>`do `<SYMMETRIC_KEY>` espaço reservado para `<SCOPE_ID>`, e com os dados coletados anteriormente. Certifique-se de que o **provisionamento:** a linha não tem espaço em branco anterior e que os itens aninhados são recuados por dois espaços.

### <a name="windows-device"></a>Dispositivo Windows

Instale o tempo de execução do IoT Edge no dispositivo para o qual você gerou uma chave de dispositivo derivada. Você configurará o tempo de execução do IoT Edge para provisionamento automático, não manual.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerenciar contêineres e atualizar o IoT Edge, consulte [Instalar o tempo de execução do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a máquina do Windows está em uma versão suportada, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do moby e o tempo de execução do IoT Edge. O comando é padrão para usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Se sim, reinicie o dispositivo agora. Assim que o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando é padrão para provisionamento manual com `-Dps` contêineres do Windows, a menos que você use o sinalizador para usar o provisionamento automático.

   Substitua os valores `{registration_id}`do `{symmetric_key}` espaço reservado para `{scope_id}`, e com os dados coletados anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo. Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.

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

Você pode verificar se a inscrição individual que você criou no Serviço de Provisionamento de Dispositivos foi usada. Navegue até a instância do Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes de inscrição para a matrícula individual que você criou. Observe que o status da inscrição é **atribuído** e o ID do dispositivo está listado.

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Aprenda a [implantar e monitorar módulos IoT Edge em escala usando o portal Azure](how-to-deploy-monitor.md) ou usando o [Azure CLI](how-to-deploy-monitor-cli.md).
