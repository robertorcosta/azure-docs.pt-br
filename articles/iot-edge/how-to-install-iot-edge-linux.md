---
title: Instalar o Azure IoT Edge no Linux | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge em dispositivos Linux executando Ubuntu ou Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535894"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. Para saber mais, consulte [Entenda o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge em um dispositivo Linux X64, ARM32 ou ARM64. Fornecemos pacotes de instalação para o Ubuntu Server 16.04, O Servidor Ubuntu 18.04 e o Raspbian Stretch. Consulte [os sistemas suportados pelo Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas e arquiteturas Linux suportados.

> [!NOTE]
> Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/ *package-name *). Leia os termos da licença antes de usar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

## <a name="install-the-latest-runtime-version"></a>Instale a versão de tempo de execução mais recente

Use as seguintes seções para instalar a versão mais recente do tempo de execução do Azure IoT Edge no seu dispositivo.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrar a chave da Microsoft e o feed do repositório de software

Prepare seu dispositivo para a instalação em tempo de execução do IoT Edge.

Instale a configuração do repositório. Escolha o comando **16.04** ou **18.04** que corresponda ao sistema operacional do dispositivo:

* **Servidor Ubuntu 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Servidor Ubuntu 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Trecho raspbiaano**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública do Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instalar o runtime de contêiner

O Azure IoT Edge depende de um runtime de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, recomendamos que você use o motor [baseado em Moby](https://mobyproject.org/) fornecido abaixo. O motor Moby é o único motor de contêiner oficialmente suportado com Azure IoT Edge. Imagens de contêiner do docker CE/EE são compatíveis com o runtime Moby.

Atualize as listas de pacotes em seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o mecanismo de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Instale a interface de linha de comando Moby (CLI). A CLI é útil para desenvolvimento, mas é opcional para implantações de produção.

   ```bash
   sudo apt-get install moby-cli
   ```

Se você tiver erros ao instalar o tempo de execução do contêiner Moby, siga as etapas para [verificar o kernel Linux para compatibilidade Moby,](#verify-your-linux-kernel-for-moby-compatibility)fornecido posteriormente neste artigo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

O **daemon de segurança IoT Edge** fornece e mantém padrões de segurança no dispositivo IoT Edge. O daemon é iniciado a cada inicialização e inicializa o dispositivo iniciando o restante do runtime do IoT Edge.

O comando de instalação também instala a versão padrão do **libiothsm** se ainda não estiver presente.

Atualize as listas de pacotes em seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale o daemon de segurança. O pacote está instalado em `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Uma vez que o IoT Edge seja instalado com sucesso, a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na [Seção Configurar daemon de segurança](#configure-the-security-daemon) para completar o provisionamento do dispositivo.

## <a name="install-a-specific-runtime-version"></a>Instale uma versão específica em tempo de execução

Se você quiser instalar uma versão específica do Moby e do tempo de execução do Azure IoT Edge em vez de usar as versões mais recentes, você pode segmentar os arquivos componentes diretamente do repositório IoT Edge GitHub. Use as seguintes etapas para obter todos os componentes ioT edge em seu dispositivo: o motor Moby e cli, o libiothsm e, finalmente, o daemon de segurança IoT Edge. Pule para a próxima seção, [Configure o daemon de segurança,](#configure-the-security-daemon)se você não quiser alterar para uma versão de tempo de execução específica.

1. Navegue até as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)e encontre a versão de lançamento que você deseja segmentar.

2. Expanda a seção **Ativos** para essa versão.

3. Pode ou não haver atualizações para o motor Moby em qualquer versão dada. Se você ver arquivos que começam com **moby-engine** e **moby-cli,** use os seguintes comandos para atualizar esses componentes. Se você não ver nenhum arquivo Moby, volte para os ativos de versão mais antigos até encontrar a versão mais recente.

   1. Encontre o **arquivo moby-engine** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no seguinte comando para instalar a versão do motor Moby:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Encontre o arquivo **moby-cli** que corresponde à arquitetura do seu dispositivo IoT Edge. O Moby CLI é um componente opcional, mas pode ser útil durante o desenvolvimento. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no seguinte comando para instalar essa versão do Cli Moby:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Cada versão deve ter novos arquivos para o daemon de segurança IoT Edge e o hsmlib. Use os seguintes comandos para atualizar esses componentes.

   1. Encontre o arquivo **libiothsm-std** que corresponde à arquitetura do dispositivo IoT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no seguinte comando para instalar a versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Encontre o arquivo **iotedge** que corresponde à arquitetura do seu dispositivo IoT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no comando a seguir para instalar essa versão do daemon de segurança ioT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Uma vez que o IoT Edge seja instalado com sucesso, a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na próxima seção para completar o provisionamento do dispositivo.

## <a name="configure-the-security-daemon"></a>Configure o daemon de segurança

Configure o runtime do IoT Edge para vincular seu dispositivo físico com uma identidade do dispositivo existente em um Hub IoT do Azure.

O daemon pode ser configurado usando o arquivo de configuração no `/etc/iotedge/config.yaml`. O arquivo está protegido contra gravação por padrão, permissões elevadas talvez sejam necessárias para editá-lo.

Um único dispositivo Azure IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivo para provisionar automaticamente os dispositivos, que é útil quando você tem muitos dispositivos para provisionar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: provisionamento manual

Para provisionar um dispositivo manualmente, é necessário fornecer a ele uma [cadeia de conexão do dispositivo](how-to-register-device.md#register-in-the-azure-portal), que poderá ser criada registrando um novo dispositivo no Hub IoT.

Abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de provisionamento do arquivo e não comente a seção **configuração de provisionamento manual.** Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge. Certifique-se de que quaisquer outras seções de provisionamento sejam comentadas. Certifique-se de que o **provisionamento:** a linha não tem espaço em branco anterior e que os itens aninhados são recuados por dois espaços.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Para colar o conteúdo da `Shift+Right Click` prancheta em Nano ou pressionar `Shift+Insert`.

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: provisionamento automático

Os dispositivos IoT Edge podem ser provisionados automaticamente usando o [DPS (Azure IoT Hub Device Provisioning Service, serviço de provisionamento de dispositivos de hub) do Azure](../iot-dps/index.yml). Atualmente, o IoT Edge suporta dois mecanismos de atestado ao usar provisionamento automático, mas seus requisitos de hardware podem afetar suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip TPM (Trusted Platform Module, módulo de plataforma confiável) por padrão. Para obter mais informações, consulte os seguintes artigos:

* [Criar e provisionar um dispositivo IoT Edge com um TPM virtual em um VM Linux](how-to-auto-provision-simulated-device-linux.md)
* [Criar e provisionar um dispositivo IoT Edge usando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e provisionar um dispositivo IoT Edge usando atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Esses artigos o iniciam através da criação de matrículas em DPS, e gerando os certificados ou chaves adequadas para atestado. Independentemente do mecanismo de atestado que você escolher, as informações de provisionamento são adicionadas ao arquivo de configuração IoT Edge em seu dispositivo IoT Edge.

Abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de provisionamento do arquivo e não comente a seção apropriada para o mecanismo de atestado. Certifique-se de que quaisquer outras seções de provisionamento sejam comentadas. O **provisionamento:** a linha não deve ter espaço em branco anterior, e os itens aninhados devem ser recuados por dois espaços. Atualize o valor de **scope_id** com o valor da sua instância de serviço de provisionamento de dispositivos ioT hub e forneça os valores apropriados para os campos de atestado.

Atestação TPM:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Atestado X.509:

```yml
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

Atestado de chave simétrica:

```yml
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

Para colar o conteúdo da `Shift+Right Click` prancheta em Nano ou pressionar `Shift+Insert`.

Salve e feche o arquivo. `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se você tiver usado as etapas de **configuração manual** na seção anterior, o runtime do IoT Edge deve ser provisionado e em execução no seu dispositivo com êxito. Se você tiver usado as etapas de **configuração automática**, você precisará concluir algumas etapas adicionais para que o runtime pode registrar seu dispositivo no hub IoT em seu nome. Para os próximos passos, consulte [Criar e provisionar um dispositivo TPM IoT Edge simulado em uma máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Você pode verificar o status do Daemon de borda IoT:

```bash
systemctl status iotedge
```

Examine os registros de daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Execute uma verificação automatizada para os erros de configuração e rede mais comuns:

```bash
sudo iotedge check
```

Até que você implante seu primeiro módulo para IoT Edge em seu dispositivo, o módulo do sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automatizada retornará um erro para a verificação de `Edge Hub can bind to ports on host` conectividade. Este erro pode ser ignorado a menos que ocorra após a implantação de um módulo no dispositivo.

Finalmente, liste módulos em execução:

```bash
sudo iotedge list
```

Depois de instalar o IoT Edge no seu dispositivo, o único módulo que você deve ver em execução é **edgeAgent**. Depois de criar sua primeira implantação, o outro módulo do sistema **$edgeHub** começará no dispositivo também. Para obter mais informações, consulte [implantar módulos IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Dicas e solução de problemas

Você precisa de privilégios elevados para executar comandos `iotedge`. Após instalar o runtime, saia da sua máquina virtual e entre novamente para atualizar suas permissões automaticamente. Até lá, use **sudo** na frente de qualquer um dos comandos `iotedge`.

Em dispositivos com restrição de recursos, é altamente recomendável que a variável de ambiente *OptimizeForPerformance* seja definida como *false* de acordo com as instruções no [guia de solução de problemas](troubleshoot.md).

Se a rede tem um servidor proxy, siga as etapas em [Configurar o dispositivo do IoT Edge para comunicar-se por meio de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique seu kernel Linux para compatibilidade moby

Muitos fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm núcleos Linux personalizados sem os recursos necessários para compatibilidade de tempo de execução de contêineres. Se você encontrar problemas durante a instalação do tempo de execução do contêiner Moby recomendado, você poderá solucionar problemas na configuração do kernel Linux usando o script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) do [repositório oficial do Moby GitHub](https://github.com/moby/moby). Execute os seguintes comandos no dispositivo para verificar a configuração do kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Este comando fornece uma saída detalhada que contém o status dos recursos do kernel que são usados pelo tempo de execução do Moby. Você vai querer garantir que todos `Generally Necessary` `Network Drivers` os itens sob e estão habilitados para garantir que seu kernel seja totalmente compatível com o tempo de execução do Moby.  Se você tiver identificado quaisquer recursos ausentes, habilite-os reconstruindo seu kernel a partir da origem e selecionando os módulos associados para inclusão no kernel apropriado .config.  Da mesma forma, se você estiver `defconfig` `menuconfig`usando um gerador de configuração de kernel como ou , encontre e habilite os respectivos recursos e reconstrua seu kernel de acordo.  Depois de implantar seu kernel recém-modificado, execute novamente o script check-config para verificar se todos os recursos necessários foram habilitados com sucesso.

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Para remover a instalação do IoT Edge do dispositivo Linux, use o seguinte comando em uma linha de comando.

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de execução do IoT Edge é removido, os recipientes que ele criou são interrompidos, mas ainda existem no seu dispositivo. Exiba todos os contêineres para ver quais deles permanecem.

```bash
sudo docker ps -a
```

Exclua os contêineres do seu dispositivo, incluindo dois contêineres de runtime.

```bash
sudo docker rm -f <container name>
```

Por fim, remova o runtime do contêiner do seu dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação de tempo de execução do IoT Edge corretamente, confira a página [de solução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
