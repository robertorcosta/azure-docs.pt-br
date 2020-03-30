---
title: Operar dispositivos offline – Azure IoT Edge | Microsoft Docs
description: Saiba como módulos e dispositivos IoT Edge podem operar sem conexão à Internet por longos períodos e como o IoT Edge pode permitir que dispositivos IoT regulares operem offline também.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236069"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Entenda os recursos off-line estendidos para dispositivos, módulos e dispositivos infantis ioT edge

O Azure IoT Edge suporta operações offline estendidas em seus dispositivos IoT Edge e permite operações off-line em dispositivos crianças não IoT Edge também. Desde que um dispositivo IoT Edge tenha tido uma oportunidade de se conectar ao IoT Hub, esse dispositivo e qualquer dispositivo infantil pode continuar a funcionar com conexão intermitente ou sem internet.

## <a name="how-it-works"></a>Como ele funciona

Quando um dispositivo do IoT Edge entra em modo offline, o hub do IoT Edge assume três funções. Primeiro, ele armazena todas as mensagens que estão no sentido upstream e as salva até que o dispositivo se reconecte. Em segundo lugar, ele atua em nome do Hub IoT para autenticar dispositivos filho e módulos para que eles possam continuar a operar. Em terceiro lugar, ele permite a comunicação entre os dispositivos filho que normalmente passariam pelo Hub IoT.

O exemplo a seguir mostra como um cenário de IoT Edge opera no modo offline:

1. **Configurar dispositivos**

   Dispositivos do IoT Edge automaticamente têm recursos offline habilitados. Para estender essa funcionalidade para outros dispositivos de IoT, você precisa declarar uma relação de pai-filho entre os dispositivos no Hub IoT. Em seguida, você configura os dispositivos filho para confiar em seu dispositivo pai atribuído e encaminha as comunicações dispositivo-nuvem através do pai como um gateway.

2. **Sincronizar com o IoT Hub**

   Pelo menos uma vez após a instalação do runtime do IoT Edge, o dispositivo do IoT Edge precisa estar online para sincronizar com o Hub IoT. Nessa sincronização, o dispositivo do IoT Edge obtém detalhes sobre todos os dispositivos filho atribuídos a ele. O dispositivo do IoT Edge também atualiza seu cache local com segurança para permitir operações offline e recupera as configurações para o armazenamento local de mensagens de telemetria.

3. **Vá offline**

   Enquanto estiver desconectado do Hub IoT, o dispositivo do IoT Edge, seus módulos implantados e quaisquer dispositivos de IoT filho poderão operar indefinidamente. Os módulos e dispositivos filho podem ser iniciados e reiniciados ao autenticarem com o hub do IoT Edge enquanto estiverem offline. A telemetria upstream associada para o Hub IoT é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT filho é mantida por meio de métodos diretos ou mensagens.

4. **Reconecte e resincronize com o IoT Hub**

   Quando a conexão com o Hub IoT for restaurada, o dispositivo do IoT Edge será sincronizado novamente. As mensagens armazenadas localmente são entregues no IoT Hub imediatamente, mas dependem da velocidade da conexão, latência do IoT Hub e fatores relacionados. Eles são entregues na mesma ordem em que foram armazenados.

   Todas as diferenças entre as propriedades desejadas e relatadas dos módulos e dispositivos são reconciliadas. O dispositivo do IoT Edge atualiza qualquer alteração de seu conjunto de dispositivos de IoT filho atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

Os recursos off-line estendidos descritos neste artigo estão disponíveis na [versão 1.0.7 ou superior do IoT Edge](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de recursos offline. Os dispositivos do IoT Edge existentes que não têm recursos offline estendidos não podem ser atualizados alterando a versão de runtime, mas devem ser reconfigurados com uma nova identidade do dispositivo do IoT Edge para obter esses recursos.

Somente dispositivos não IoT Edge podem ser adicionados como dispositivos crianças.

Os dispositivos IoT Edge e seus dispositivos filho atribuídos podem funcionar indefinidamente offline após a sincronização inicial e única. No entanto, o armazenamento das mensagens depende do tempo de vida (TTL) e do espaço disponível em disco para armazenar as mensagens.

## <a name="set-up-parent-and-child-devices"></a>Configure dispositivos de pais e filhos

Para que um dispositivo IoT Edge amplie seus recursos offline estendidos para dispositivos IoT crianças, você precisa completar dois passos. Primeiro, declare as relações pai-filho no portal Azure. Em segundo lugar, crie uma relação de confiança entre o dispositivo pai e qualquer dispositivo filho e, em seguida, configure as comunicações dispositivo-nuvem para passar pelo pai como um gateway.

### <a name="assign-child-devices"></a>Atribuir dispositivos filho

Os dispositivos infantis podem ser qualquer dispositivo não IoT Edge registrado no mesmo Hub IoT. Os dispositivos dos pais podem ter vários dispositivos de filho, mas um dispositivo filho só tem um pai. Existem três opções para definir dispositivos crianças em um dispositivo de borda: através do portal Azure, usando o Azure CLI, ou usando o Serviço IoT Hub SDK.

As seções a seguir fornecem exemplos de como você pode declarar a relação pai/filho no IoT Hub para dispositivos IoT existentes. Se você estiver criando novas identidades de dispositivo para seus dispositivos filho, consulte [Authenticate um dispositivo downstream no Azure IoT Hub](how-to-authenticate-downstream-device.md) para obter mais informações.

#### <a name="option-1-iot-hub-portal"></a>Opção 1: Portal IoT Hub

Você pode declarar a relação pai-filho ao criar um novo dispositivo. Ou para dispositivos existentes, você pode declarar o relacionamento a partir da página de detalhes do dispositivo do dispositivo IoT Edge pai ou do dispositivo IoT filho.

   ![Gerenciar dispositivos filho da página de detalhes do dispositivo do IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: `az` Use a ferramenta de linha de comando

Usando a [interface de linha de comando do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) com [extensão IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 ou mais nova), você pode gerenciar as relações dos pais com os subcomandos [de identidade do dispositivo.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) O exemplo abaixo usa uma consulta para atribuir todos os dispositivos não-IoT Edge no hub para serem dispositivos filho de um dispositivo IoT Edge.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

Você pode modificar [a consulta](../iot-hub/iot-hub-devguide-query-language.md) para selecionar um subconjunto diferente de dispositivos. O comando pode levar vários segundos se você especificar um grande conjunto de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: Use O Serviço de Hub IoT SDK

Finalmente, você pode gerenciar as relações com os pais de forma programática usando c#, Java ou Node.js IoT Hub Service SDK. Aqui está um [exemplo de atribuir um dispositivo filho](https://aka.ms/set-child-iot-device-c-sharp) usando o C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configure o dispositivo pai como um gateway

Você pode pensar em uma relação pai/filho como um gateway transparente, onde o dispositivo filho tem sua própria identidade no IoT Hub, mas se comunica através da nuvem através de seu pai. Para uma comunicação segura, o dispositivo filho precisa ser capaz de verificar se o dispositivo pai vem de uma fonte confiável. Caso contrário, terceiros poderiam configurar dispositivos maliciosos para se passar por pais e interceptar comunicações.

Uma maneira de criar essa relação de confiança é descrita em detalhes nos seguintes artigos:

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Conecte um dispositivo downstream (criança) a um gateway Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificar servidores de DNS

Para melhorar a robustez, é altamente recomendável especificar os endereços de servidor DNS usados em seu ambiente. Para definir o servidor DNS para IoT Edge, consulte a resolução do [módulo Edge Agent continuamente relata 'arquivo de configuração vazia' e nenhum módulo é inicial no dispositivo](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) no artigo de solução de problemas.

## <a name="optional-offline-settings"></a>Configurações offline opcionais

Se os dispositivos ficarem offline, o dispositivo pai ioT Edge armazena todas as mensagens dispositivo-nuvem até que a conexão seja restabelecida. O módulo hub IoT Edge gerencia o armazenamento e o encaminhamento de mensagens offline. Para dispositivos que podem ficar offline por longos períodos de tempo, otimize o desempenho configurando duas configurações de hub IoT Edge.

Primeiro, aumente o tempo de configuração ao vivo para que o hub IoT Edge mantenha as mensagens tempo suficiente para que seu dispositivo se reconecte. Em seguida, adicione mais espaço em disco para o armazenamento de mensagens.

### <a name="time-to-live"></a>Vida útil

A configuração de vida útil é a quantidade de tempo (em segundos) que uma mensagem pode esperar para ser entregue antes de expirar. O padrão é 7200 segundos (duas horas). O valor máximo é limitado apenas pelo valor máximo de uma variável inteira, que é de cerca de 2 bilhões.

Essa configuração é uma propriedade desejada do hub do IoT Edge, que é armazenada no gêmeo do módulo. Você pode configurá-lo no portal Azure ou diretamente no manifesto de implantação.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Armazenamento de host para módulos do sistema

As mensagens e as informações do estado do módulo são armazenadas no sistema de arquivos de contêiner local do hub IoT Edge por padrão. Para uma maior confiabilidade, especialmente quando se opera offline, você também pode dedicar armazenamento no dispositivo IoT Edge host. Para obter mais informações, consulte [Dar módulos de acesso ao armazenamento local de um dispositivo](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como configurar um gateway transparente para as conexões entre seus pais e filhos:

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conecte um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
