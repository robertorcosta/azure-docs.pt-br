---
title: Instalar o Azure IoT Edge no Linux | Microsoft Docs
description: Azure IoT Edge instruções de instalação em dispositivos Linux que executam o Ubuntu ou o Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: kgremban
ms.openlocfilehash: d73f3a37bb084533733b27b49ac171747cee814c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85321879"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o Azure IoT Edge Runtime em um dispositivo com x64, ARM32 ou ARM64 Linux. Fornecemos pacotes de instalação para o Ubuntu Server 16, 4, Ubuntu Server 18, 4 e Raspbian Stretch. Consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems) para obter uma lista de arquiteturas e sistemas operacionais Linux com suporte.

> [!NOTE]
> Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/ *package-name *). Leia os termos da licença antes de usar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

## <a name="install-iot-edge-and-container-runtimes"></a>Instalar IoT Edge e tempos de execução de contêiner

Use as seções a seguir para instalar a versão mais recente do Azure IoT Edge Runtime em seu dispositivo.

>[!NOTE]
>O suporte para dispositivos ARM64 está em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrar a chave da Microsoft e o feed do repositório de software

Prepare seu dispositivo para a instalação do IoT Edge Runtime.

Instale a configuração do repositório. Escolha o comando **16, 4** ou **18, 4** que corresponde ao sistema operacional do seu dispositivo:

* **Ubuntu Server 16, 4**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18, 4**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instale um runtime do contêiner

O Azure IoT Edge depende de um runtime de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, recomendamos que você use o mecanismo [baseado em Moby](https://mobyproject.org/) fornecido abaixo. O mecanismo Moby é o único mecanismo de contêiner com suporte oficial com Azure IoT Edge. Imagens de contêiner do docker CE/EE são compatíveis com o runtime Moby.

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

Se você receber erros ao instalar o tempo de execução do contêiner do Moby, siga as etapas para [verificar a compatibilidade do kernel do Linux para Moby](#verify-your-linux-kernel-for-moby-compatibility), fornecida mais adiante neste artigo.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

O **daemon de segurança do IOT Edge** fornece e mantém os padrões de segurança no dispositivo IOT Edge. O daemon é iniciado a cada inicialização e inicializa o dispositivo iniciando o restante do runtime do IoT Edge.

Atualize as listas de pacotes em seu dispositivo.

   ```bash
   sudo apt-get update
   ```

Verifique para ver quais versões do IoT Edge estão disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se você quiser instalar a versão mais recente do daemon de segurança, use o seguinte comando que também instala a versão mais recente do pacote **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Se você quiser instalar uma versão específica do daemon de segurança, especifique a versão da saída da lista de apt. Especifique também a mesma versão para o pacote **libiothsm-STD** , que, caso contrário, instalaria sua versão mais recente. Por exemplo, o seguinte comando instala a versão mais recente da versão 1.0.8:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Se a versão que você deseja instalar não estiver listada, siga as etapas em [instalar o tempo de execução usando os ativos de liberação](#install-runtime-using-release-assets). Essa seção mostra como direcionar qualquer versão anterior do daemon de segurança do IoT Edge ou versões Release Candidate.

Depois que o IoT Edge for instalado com êxito em `/etc/iotedge/` , a saída solicitará que você atualize o arquivo de configuração. Continue na próxima seção para concluir o provisionamento de dispositivos.

## <a name="configure-the-security-daemon"></a>Configurar o daemon de segurança

Configure o runtime do IoT Edge para vincular seu dispositivo físico com uma identidade do dispositivo existente em um Hub IoT do Azure.

O daemon pode ser configurado usando o arquivo de configuração no `/etc/iotedge/config.yaml`. O arquivo está protegido contra gravação por padrão, permissões elevadas talvez sejam necessárias para editá-lo.

Um único dispositivo Azure IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivo para provisionar automaticamente os dispositivos, que é útil quando você tem muitos dispositivos para provisionar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado.

### <a name="option-1-manual-provisioning"></a>Opção 1: provisionamento manual

Para provisionar um dispositivo manualmente, é necessário fornecer a ele uma [cadeia de conexão do dispositivo](how-to-register-device.md#register-in-the-azure-portal), que poderá ser criada registrando um novo dispositivo no Hub IoT.

Abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da seção **configuração de provisionamento manual** . Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge. Verifique se qualquer outra seção de provisionamento foi comentada. Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Para colar o conteúdo da área de transferência no nano `Shift+Right Click` ou pressione `Shift+Insert` .

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: provisionamento automático

IoT Edge dispositivos podem ser provisionados automaticamente usando o [DPS (serviço de provisionamento de dispositivos) do Hub IOT do Azure](../iot-dps/index.yml). Atualmente, o IoT Edge dá suporte a três mecanismos de atestado ao usar o provisionamento automático, mas seus requisitos de hardware podem afetar suas escolhas. Por exemplo, os dispositivos Raspberry Pi não vêm com um chip Trusted Platform Module (TPM) por padrão. Para obter mais informações, consulte os seguintes artigos:

* [Criar e provisionar um dispositivo IoT Edge com um TPM virtual em uma VM do Linux](how-to-auto-provision-simulated-device-linux.md)
* [Criar e provisionar um dispositivo IoT Edge usando certificados X. 509](how-to-auto-provision-x509-certs.md)
* [Criar e provisionar um dispositivo IoT Edge usando o atestado de chave simétrica](how-to-auto-provision-symmetric-keys.md)

Esses artigos orientam você pela configuração de registros no DPS e pela geração de certificados ou chaves apropriados para atestado. Independentemente do mecanismo de atestado escolhido, as informações de provisionamento são adicionadas ao arquivo de configuração IoT Edge em seu dispositivo IoT Edge.

Abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da seção apropriada para seu mecanismo de atestado. Verifique se qualquer outra seção de provisionamento foi comentada. O **provisionamento:** a linha não deve ter nenhum espaço em branco anterior e itens aninhados devem ser recuados em dois espaços. Atualize o valor de **scope_id** com o valor de sua instância do serviço de provisionamento de dispositivos do Hub IOT e forneça os valores apropriados para os campos de atestado.

Atestado de TPM:

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

Atestado X. 509:

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

Para colar o conteúdo da área de transferência no nano `Shift+Right Click` ou pressione `Shift+Insert` .

Salve e feche o arquivo. `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se você tiver usado as etapas de **configuração manual** na seção anterior, o runtime do IoT Edge deve ser provisionado e em execução no seu dispositivo com êxito. Se você tiver usado as etapas de **configuração automática**, você precisará concluir algumas etapas adicionais para que o runtime pode registrar seu dispositivo no hub IoT em seu nome. Para as próximas etapas, consulte [criar e provisionar um dispositivo IOT Edge de TPM simulado em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Você pode verificar o status do daemon de IoT Edge:

```bash
systemctl status iotedge
```

Examinar os logs do daemon:

```bash
journalctl -u iotedge --no-pager --no-full
```

Execute a [ferramenta de solução de problemas](troubleshoot.md#run-the-check-command) para verificar os erros de configuração e de rede mais comuns:

```bash
sudo iotedge check
```

Até que você implante seu primeiro módulo para IoT Edge em seu dispositivo, o módulo do sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automática retornará um erro para a verificação de `Edge Hub can bind to ports on host` conectividade. Esse erro pode ser ignorado, a menos que ocorra após a implantação de um módulo no dispositivo.

Por fim, liste módulos em execução:

```bash
sudo iotedge list
```

Depois de instalar IoT Edge em seu dispositivo, o único módulo que você deve ver em execução é **edgeAgent**. Depois de criar sua primeira implantação, o outro módulo do sistema **$edgeHub** será iniciado no dispositivo também. Para obter mais informações, consulte [implantar módulos de IOT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Dicas e solução de problemas

Você precisa de privilégios elevados para executar comandos `iotedge`. Após instalar o runtime, saia da sua máquina virtual e entre novamente para atualizar suas permissões automaticamente. Até lá, use **sudo** na frente de qualquer um dos comandos `iotedge`.

Em dispositivos com restrição de recursos, é altamente recomendável que a variável de ambiente *OptimizeForPerformance* seja definida como *false* de acordo com as instruções no [guia de solução de problemas](troubleshoot.md).

Se a rede tem um servidor proxy, siga as etapas em [Configurar o dispositivo do IoT Edge para comunicar-se por meio de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifique a compatibilidade do kernel do Linux para Moby

Muitos fabricantes de dispositivos incorporados enviam imagens de dispositivos que contêm kernels Linux personalizados sem os recursos necessários para a compatibilidade do tempo de execução do contêiner. Se você encontrar problemas ao instalar o tempo de execução do contêiner Moby recomendado, poderá ser capaz de solucionar problemas de configuração do kernel do Linux usando o script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) do [repositório GitHub](https://github.com/moby/moby)oficial do Moby. Execute os seguintes comandos no dispositivo para verificar a configuração do kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Esse comando fornece uma saída detalhada que contém o status dos recursos de kernel que são usados pelo tempo de execução Moby. Você desejará garantir que todos os itens em `Generally Necessary` e `Network Drivers` estejam habilitados para garantir que o kernel seja totalmente compatível com o tempo de execução do Moby.  Se você tiver identificado quaisquer recursos ausentes, habilite-os recriando o kernel da origem e selecionando os módulos associados para inclusão no kernel. config apropriado.  Da mesma forma, se você estiver usando um gerador de configuração de kernel como `defconfig` ou `menuconfig` , localize e habilite os respectivos recursos e reconstrua o kernel de acordo.  Depois de implantar o kernel recém-modificado, execute o script check-config novamente para verificar se todos os recursos necessários foram habilitados com êxito.

## <a name="install-runtime-using-release-assets"></a>Instalar o tempo de execução usando ativos de liberação

Use as etapas nesta seção se você quiser instalar uma versão específica do Moby e o tempo de execução do Azure IoT Edge que não está disponível por meio do `apt-get install` . A lista de pacotes da Microsoft contém apenas um conjunto limitado de versões recentes e suas subversãos, portanto, essas etapas são para qualquer pessoa que queira instalar uma versão mais antiga ou uma versão Release Candidate.

Usando comandos de rotação, você pode direcionar os arquivos de componentes diretamente do repositório GitHub IoT Edge. Use as etapas a seguir para obter todos os componentes de IoT Edge em seu dispositivo: o mecanismo Moby e a CLI, o libiothsm e, por fim, o daemon de segurança do IoT Edge.

1. Navegue até o [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases)e localize a versão de lançamento que você deseja direcionar.

2. Expanda a seção **ativos** para essa versão.

3. Pode ou não haver atualizações para o mecanismo Moby em qualquer versão fornecida. Se você vir arquivos que começam com **Moby-Engine** e **Moby-CLI**, use os comandos a seguir para atualizar esses componentes. Se você não vir nenhum arquivo Moby, volte pelos ativos de lançamento mais antigos até encontrar a versão mais recente.

   1. Localize o arquivo **Moby-Engine** que corresponde à arquitetura do dispositivo de IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no comando a seguir para instalar essa versão do mecanismo Moby:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Localize o arquivo **Moby-CLI** que corresponde à arquitetura do dispositivo IOT Edge. A CLI do Moby é um componente opcional, mas pode ser útil durante o desenvolvimento. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no comando a seguir para instalar essa versão da CLI do Moby:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Cada versão deve ter novos arquivos para o daemon de segurança IoT Edge e o hsmlib. Use os comandos a seguir para atualizar esses componentes.

   1. Localize o arquivo **libiothsm-STD** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   2. Use o link copiado no comando a seguir para instalar essa versão do hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Localize o arquivo **iotedge** que corresponde à arquitetura do dispositivo IOT Edge. Clique com o botão direito do mouse no link do arquivo e copie o endereço do link.

   4. Use o link copiado no comando a seguir para instalar essa versão do daemon de segurança IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Depois que o IoT Edge for instalado com êxito em `/etc/iotedge` , a saída solicitará que você atualize o arquivo de configuração. Siga as etapas na seção [Configurar o daemon de segurança](#configure-the-security-daemon) para concluir o provisionamento de dispositivos.

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Para remover a instalação do IoT Edge do dispositivo Linux, use o seguinte comando em uma linha de comando.

Remova o runtime do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de execução de IoT Edge é removido, os contêineres que ele criou são interrompidos, mas ainda existem em seu dispositivo. Exiba todos os contêineres para ver quais deles permanecem.

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

Se você estiver tendo problemas com o tempo de execução do IoT Edge sendo instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).
