---
title: Instalar o Azure IoT Edge no Windows | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: kgremban
ms.openlocfilehash: 80ce962ac6977fcce2455c8e2ef29af448a44075
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133155"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o runtime do Azure IoT Edge no Windows

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre o runtime do IoT Edge, consulte [Reconhecer o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge no sistema Windows x64 (AMD/Intel) usando contêineres Windows.

> [!NOTE]
> Um problema conhecido do sistema operacional Windows impede a transição para estados de energia de sono e hibernação quando módulos IoT Edge (contêineres Windows Nano Server isolados de processo) estão sendo executados. Este problema afeta a vida útil da bateria no dispositivo.
>
> Como solução de solução, `Stop-Service iotedge` use o comando para impedir a execução de módulos IoT Edge antes de usar esses estados de energia.

O uso de contêineres do Linux no sistema Windows não é uma configuração de produção com suporte ou recomendada para o Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste. Para saber mais, consulte [Use IoT Edge no Windows para executar contêineres Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte [as versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para verificar se o dispositivo Windows pode dar suporte ao IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação.

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

IoT Edge para Windows requer windows versão 1809/build 17763, que é a mais recente [compilação de suporte a longo prazo](https://docs.microsoft.com/windows/release-information/)do Windows . Para obter suporte ao Windows SKU, veja o que é suportado com base em se você está se preparando para cenários de produção ou cenários de desenvolvimento e teste:

* **Produção**: Para obter as informações mais recentes sobre quais sistemas operacionais são suportados atualmente para cenários de produção, consulte [sistemas suportados pelo Azure IoT Edge](support.md#operating-systems).
* **Desenvolvimento e teste**: Para cenários de desenvolvimento e teste, o Azure IoT Edge com contêineres windows pode ser instalado em qualquer versão do Windows 10 ou Windows Server 2019 que suporte o recurso de contêineres.

Os dispositivos IoT Core devem incluir o recurso opcional IoT Core Windows Containers para suportar o tempo de execução do IoT Edge. Use o seguinte comando em uma [sessão remota do PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) para verificar se os contêineres do Windows são suportados no seu dispositivo:

```powershell
Get-Service vmcompute
```

Se o serviço estiver presente, você deve obter uma resposta bem-sucedida com o status do serviço listado como **em execução**. Se `vmcompute` o serviço não for encontrado, então seu dispositivo não atenderá aos requisitos do IoT Edge. Entre em contato com seu provedor de hardware para perguntar sobre o suporte para este recurso.

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contêiner

O Azure IoT Edge depende de um mecanismo de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, use o mecanismo Moby incluído no script de instalação para executar contêineres do Windows no dispositivo Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge em um novo dispositivo

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um script do PowerShell baixa e instala o daemon de segurança do Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro dos dois módulos de runtime, o agente do IoT Edge, que permite implantações remotas de outros módulos.

>[!TIP]
>Para dispositivos IoT Core, recomendamos executar os comandos de instalação usando uma sessão RemotePowerShell. Para obter mais informações, consulte [Usando o PowerShell para Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando você instala o runtime do IoT Edge pela primeira vez em um dispositivo, é necessário provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo IoT Edge pode ser provisionado manualmente usando uma seqüência de conexão de dispositivo fornecida pelo IoT Hub. Ou, você pode usar o DPS (Device Provisioning Service, serviço de provisionamento de dispositivos) para provisionar automaticamente dispositivos, o que é útil quando você tem muitos dispositivos para configurar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado.

As seções a seguir descrevem os casos de uso comuns e os parâmetros do script de instalação do IoT Edge em um novo dispositivo.

### <a name="option-1-install-and-manually-provision"></a>Opção 1: instalar e provisionar manualmente

Nesta primeira opção, você fornece uma **seqüência de conexão de dispositivo** gerada pelo IoT Hub para fornecer o dispositivo.

Este exemplo demonstra uma instalação manual com contêineres do Windows:

1. Se você ainda não tiver, registre um novo dispositivo IoT Edge e recupere a **seqüência de conexão do dispositivo**. Copie a seqüência de conexões a ser usada mais tarde nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Portal Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Código visual do estúdio](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Use uma sessão do AMD64 do PowerShell para instalar o IoT Edge e não o PowerShell (x86). Se não tiver certeza de qual tipo de sessão você está usando, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a máquina do Windows está em uma versão suportada, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do moby e o tempo de execução do IoT Edge. O comando é padrão para usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Se sim, reinicie o dispositivo agora. Assim que o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a seqüência de conexão do dispositivo que você recuperou na etapa 1. A seqüência de conexão do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub.

   A seqüência de conexões do dispositivo tem o seguinte formato e não deve incluir aspas:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use as etapas em [Verificar a instalação bem sucedida](#verify-successful-installation) para verificar o status do IoT Edge em seu dispositivo.

Ao instalar e provisionar um dispositivo manualmente, é possível usar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, pule adiante para saber sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: instalar e provisionar automaticamente

Nesta segunda opção, você provisiona o dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Forneça o **ID** do Escopo a partir de uma instância de serviço de provisionamento de dispositivos, juntamente com quaisquer outras informações específicas do seu [mecanismo de atestado](../iot-dps/concepts-security.md#attestation-mechanism)preferido:

* [Criar e provisionar um dispositivo IoT Edge simulado com um TPM virtual no Windows](how-to-auto-provision-simulated-device-windows.md)
* [Criar e provisionar um dispositivo IoT Edge simulado usando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e provisionar um dispositivo IoT Edge usando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Ao instalar e provisionar um dispositivo automaticamente, você pode usar parâmetros adicionais para modificar a instalação, incluindo:

* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, continue lendo este artigo ou ignore para saber mais sobre [Todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Instalação de versão offline ou específica

Durante a instalação, dois arquivos são baixados:

* A cabine Microsoft Azure IoT Edge, que contém o daemon de segurança IoT Edge (iotedged), moby container engine e Moby CLI.
* Visual C++ redistributable package (VC runtime) MSI

Se o dispositivo estiver offline durante a instalação ou se você quiser instalar uma versão específica do IoT Edge, você pode baixar um ou ambos os arquivos com antecedência para o dispositivo. Na hora de instalar, aponte o script de instalação para o diretório que contém os arquivos baixados. O instalador verifica esse diretório primeiro e, em seguida, apenas baixa componentes que não são encontrados. Se todos os arquivos estiverem disponíveis offline, você pode instalar sem conexão com a internet.

Para obter os arquivos de instalação mais recentes do IoT Edge, juntamente com as versões anteriores, consulte [as versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Para instalar com componentes `-OfflineInstallationPath` offline, use o parâmetro como parte do comando Deploy-IoTEdge e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um arquivo chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Começando com a versão IoT Edge 1.0.9-rc4, há dois arquivos .cab disponíveis para uso, um para dispositivos AMD64 e um para ARM32. Baixe o arquivo correto para o seu dispositivo e, em seguida, renomeie o arquivo para remover o sufixo de arquitetura.

O `Deploy-IoTEdge` comando instala os componentes ioT Edge e, `Initialize-IoTEdge` em seguida, você precisa continuar com o comando para provisionar o dispositivo com seu ID e conexão do dispositivo IoT Hub. Execute o comando diretamente e forneça uma seqüência de conexão do IoT Hub ou use um dos links da seção anterior para aprender como fornecer automaticamente dispositivos com o Serviço de Provisionamento de Dispositivos.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

Você também pode usar o parâmetro de caminho de instalação offline com o comando Update-IoTEdge.

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Verifique o status do serviço do IoT Edge. Deve ser listado como funcionando.  

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando. Se você acabou de terminar de instalar o tempo de execução do IoT Edge, poderá ver uma lista de erros do tempo entre a execução **do Deploy-IoTEdge** e **o Initialize-IoTEdge**. Esses erros são esperados, pois o serviço está tentando iniciar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Execute uma verificação automatizada para verificar os erros de configuração e rede mais comuns.

```powershell
iotedge check
```

Módulos de execução da lista. Após uma nova instalação, o único módulo que você deve ver em execução é **edgeAgent**. Depois [de implantar módulos IoT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **edgeHub,** também começará no dispositivo.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerenciar contêineres de módulo

O serviço IoT Edge requer um motor de contêiner funcionando em seu dispositivo. Quando você implanta um módulo em um dispositivo, o tempo de execução do IoT Edge usa o mecanismo de contêiner para puxar a imagem do contêiner de um registro na nuvem. O serviço IoT Edge permite que você interaja com seus módulos e recupere logs, mas às vezes você pode querer usar o mecanismo de contêiner para interagir com o próprio contêiner.

Para obter mais informações sobre os conceitos dos [módulos, consulte Entenda os módulos Azure IoT Edge](iot-edge-modules.md).

Se você estiver executando contêineres windows em seu dispositivo Windows IoT Edge, então a instalação do IoT Edge incluiu o mecanismo de contêiner Moby. O motor Moby foi baseado nos mesmos padrões do Docker, e foi projetado para funcionar em paralelo na mesma máquina que docker desktop. Por essa razão, se você quiser atingir contêineres gerenciados pelo motor Moby, você tem que especificamente direcionar esse motor em vez de Docker.

Por exemplo, para listar todas as imagens do Docker, use o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens moby, modifique o mesmo comando com um ponteiro para o motor Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O URI do motor está listado na saída do script de instalação, ou você pode encontrá-lo na seção de configurações de tempo de execução do contêiner para o arquivo config.yaml.

![moby_runtime uri em config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre comandos que você pode usar para interagir com contêineres e imagens em execução em seu dispositivo, consulte [interfaces de linha de comando docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do dispositivo Windows, use o seguinte comando em uma janela administrativa do PowerShell. Esse comando remove o runtime do IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

O comando Uninstall-IoTEdge não funciona no Windows IoT Core. Para remover o IoT Edge dos dispositivos Windows IoT Core, você precisa reimplantar sua imagem do Windows IoT Core.

Para obter mais informações sobre opções de desinstalação, use o comando `Get-Help Uninstall-IoTEdge -full`.

## <a name="verify-installation-script"></a>Verificar o script de instalação

Os comandos de instalação fornecidos neste artigo usam o cmdlet `aka.ms/iotedge-win`Invoke-WebRequest para solicitar o script de instalação de . Este link aponta`IoTEdgeSecurityDaemon.ps1` para o script da versão mais recente [do IoT Edge](https://github.com/Azure/azure-iotedge/releases). Você também pode baixar este script, ou uma versão do script de uma versão específica, para executar os comandos de instalação em seu dispositivo IoT Edge.

O script fornecido é assinado para aumentar a segurança. Você pode verificar a assinatura baixando o script para o seu dispositivo e executando o seguinte comando PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

O status de saída é **válido** se a assinatura for verificada.

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As seções anteriores introduziram cenários comuns de instalação com exemplos de como usar parâmetros para modificar o script de instalação. Esta seção fornece tabelas de referência dos parâmetros comuns usados para instalar, atualizar ou desinstalar o IoT Edge.

### <a name="deploy-iotedge"></a>Implantar-IoTEdge

O comando Deploy-IoTEdge baixa e implanta o Daemon de segurança de borda IoT e suas dependências. O comando de implantação aceita esses parâmetros comuns, entre outros. Para a lista completa, `Get-Help Deploy-IoTEdge -full`use o comando .  

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional de contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres Windows, o IoT Edge usa o motor de contêiner moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador verificará o diretório listado para os arquivos MSI da cabine IoT Edge e do VC Runtime necessários para instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você pode instalar o IoT Edge sem uma conexão com a internet. Você também pode usar este parâmetro para usar uma versão específica. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Este sinalizador permite que o script de implantação reinicie a máquina sem avisar, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura o IoT Edge com a seqüência de conexões do dispositivo e detalhes operacionais. Grande parte das informações geradas por este comando é então armazenada no arquivo iotedge\config.yaml. O comando de inicialização aceita esses parâmetros comuns, entre outros. Para a lista completa, `Get-Help Initialize-IoTEdge -full`use o comando .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhum | **Parâmetro de interruptor**. Se nenhum tipo de provisionamento for especificado, o manual é o valor padrão.<br><br>Declara que você fornecerá uma cadeia de conexão de dispositivo para provisionar o dispositivo manualmente |
| **Dps** | Nenhum | **Parâmetro de interruptor**. Se nenhum tipo de provisionamento for especificado, o manual é o valor padrão.<br><br>Declara que você fornecerá uma ID do escopo do DPS (Serviço de Provisionamento de Dispositivos) e a ID de Registro do dispositivo para provisionar por meio do DPS.  |
| **Cadeia de conexão de dispositivos** | Uma cadeia de conexão de um dispositivo do IoT Edge registrado em um Hub IoT, entre aspas simples | **Necessário** para o provisionamento manual. Se você não fornecer uma seqüência de conexão nos parâmetros do script, você será solicitado para um. |
| **ScopeId** | Uma ID do escopo de uma instância do Serviço de Provisionamento de Dispositivos associada ao Hub IoT. | **Necessário** para o provisionamento de DPS. Se você não fornecer um ID de escopo nos parâmetros do script, você será solicitado para um. |
| **RegistrationId** | Uma ID de registro gerada pelo dispositivo | **Necessário** para o provisionamento de DPS se usar tpm ou atestado de chave simétrica. **Opcional** se usar atestado de certificado X.509. |
| **X509Certificado de identidade** | O caminho URI para o certificado de identidade do dispositivo X.509 no dispositivo. | **Necessário** para o provisionamento de DPS se usar atestado de certificado X.509. |
| **X509IdentityPrivateKey** | O caminho URI para a chave de certificado de identidade do dispositivo X.509 no dispositivo. | **Necessário** para o provisionamento de DPS se usar atestado de certificado X.509. |
| **SymmetricKey** | A chave simétrica usada para provisionar a identidade do dispositivo IoT Edge ao usar DPS | **Necessário** para o provisionamento de DPS se usar atestado de chave simétrica. |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional de contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres Windows, o IoT Edge usa o motor de contêiner moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI de imagem do agente do IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca sem interrupção mais recente para a imagem do agente do IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer a própria imagem de agente. Para obter mais informações, consulte [Reconhecer as marcas do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Username** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Senha** | Cadeia de caracteres de senha de segurança | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça a senha para acessar o registro. |

### <a name="update-iotedge"></a>Atualização-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional de contêiner for especificado, o Windows será o valor padrão. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro estiver incluído, o instalador verificará o diretório listado para os arquivos MSI da cabine IoT Edge e do VC Runtime necessários para instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você pode instalar o IoT Edge sem uma conexão com a internet. Você também pode usar este parâmetro para usar uma versão específica. |
| **RestartIfNeeded** | none | Este sinalizador permite que o script de implantação reinicie a máquina sem avisar, se necessário. |

### <a name="uninstall-iotedge"></a>Desinstalar-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Força** | none | Esta bandeira força a desinstalação caso a tentativa anterior de desinstalação não tenha sido bem sucedida.
| **RestartIfNeeded** | none | Este sinalizador permite que o script de desinstalação reinicie a máquina sem avisar, se necessário. |

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação correta do IoT Edge, consulte a página de [solução de problemas](troubleshoot.md).

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
