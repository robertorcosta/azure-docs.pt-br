---
title: Provisionar automaticamente dispositivos Windows com DPS - Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo simulado em seu computador Windows para testar o provisionamento automático de dispositivos para o Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fc051e2a0ebbdae7c62ff8a249747d118d3c2ce4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668685"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Criar e provisionar um dispositivo IoT Edge simulado com um TPM virtual no Windows

Os dispositivos Azure IoT Edge podem ser provisionados automaticamente usando o [Serviço de Provisionamento de Dispositivos,](../iot-dps/index.yml) assim como dispositivos que não estão habilitados para bordas. Se você não estiver familiarizado com o processo de provisionamento automático, revise os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

O DPS suporta atestado de chave simétrica para dispositivos IoT Edge tanto na matrícula individual quanto na matrícula em grupo. Para a inscrição em grupo, se você verificar a opção "is IoT Edge device" seja verdadeira no atestado de chave simétrica, todos os dispositivos registrados nesse grupo de inscrição serão marcados como dispositivos IoT Edge.

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas:

* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um dispositivo simulado em seu computador Windows com um simulado Trusted Platform Module (TPM) para segurança de hardware.
* Crie um registro individual para o dispositivo.
* Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT.

> [!TIP]
> Este artigo descreve o teste de provisionamento automático usando atestado TPM em dispositivos virtuais, mas grande parte dele se aplica ao usar hardware TPM físico também.

## <a name="prerequisites"></a>Pré-requisitos

* Computador de desenvolvimento do Windows. Este artigo usa Windows 10.
* Um Hub IoT ativo.

> [!NOTE]
> O TPM 2.0 é necessário ao usar o atestado TPM com DPS e só pode ser usado para criar matrículas individuais, não em grupo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

> [!TIP]
> Se você estiver usando um dispositivo TPM físico, você precisa determinar a **tecla Endorsement**, que é exclusiva de cada chip TPM e é obtida do fabricante de chips TPM associado a ele. Você pode obter um **ID de registro** exclusivo para o seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.
>
> Siga as instruções do artigo [Como gerenciar as inscrições de dispositivos com o Portal Azure](../iot-dps/how-to-manage-enrollments.md) para criar sua inscrição no DPS e, em seguida, prosseguir com a seção Instalar a seção [de tempo de execução IoT Edge](#install-the-iot-edge-runtime) neste artigo para continuar.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado em sua máquina de desenvolvimento do Windows. Recupere a chave **de ID de registro** e **endosso** para o seu dispositivo e use-as para criar uma entrada de inscrição individual no DPS.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

Escolha o idioma SDK que você deseja usar para criar o dispositivo simulado e siga as etapas até que você crie o registro individual.

Ao criar a inscrição individual, selecione **True** para declarar que o dispositivo TPM simulado em sua máquina de desenvolvimento windows é um **dispositivo IoT Edge**.

> [!TIP]
> Na CLI do Azure, você pode criar uma [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e usar o sinalizador **habilitado** para borda para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

Dispositivo simulado e guias de inscrição individuais:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar o registro individual, salve o valor do **ID de registro**. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor **do escopo do DPS ID**
* O **ID de registro do** dispositivo que você criou

Instale o tempo de execução do IoT Edge no dispositivo que está executando o TPM simulado. Você configurará o tempo de execução do IoT Edge para provisionamento automático, não manual.

> [!TIP]
> Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerenciar contêineres e atualizar o IoT Edge, consulte [Instalar o tempo de execução do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a máquina do Windows está em uma versão suportada, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do moby e o tempo de execução do IoT Edge. O comando é padrão para usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Se sim, reinicie o dispositivo agora. Assim que o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. Use `-Dps` o sinalizador para usar o Serviço de Provisionamento de Dispositivos em vez de provisionamento manual.

   Substitua os valores do espaço reservado para `{scope_id}` e `{registration_id}` com os dados coletados anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo. Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.  

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando a CLI do Azure](how-to-deploy-monitor-cli.md)
