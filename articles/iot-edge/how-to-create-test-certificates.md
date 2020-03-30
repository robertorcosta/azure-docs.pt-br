---
title: Criar certificados de teste - Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste e aprenda a instalá-los em um dispositivo Azure IoT Edge para se preparar para a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5afb9b7a6ba1ffb99df064c9f92780dc820b2e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535979"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Crie certificados de demonstração para testar os recursos do dispositivo IoT Edge

Os dispositivos IoT Edge exigem certificados para comunicação segura entre o tempo de execução, os módulos e quaisquer dispositivos a jusante.
Se você não tiver uma autoridade de certificado para criar os certificados necessários, você pode usar certificados de demonstração para experimentar os recursos do IoT Edge em seu ambiente de teste.
Este artigo descreve a funcionalidade dos scripts de geração de certificados que o IoT Edge fornece para testes.

Esses certificados expiram em 30 dias e não devem ser utilizados em nenhum cenário de produção.

Você pode criar certificados em qualquer máquina e, em seguida, copiá-los para o seu dispositivo IoT Edge.
É mais fácil usar sua máquina primária para criar os certificados em vez de gerá-los em seu próprio dispositivo IoT Edge.
Usando sua máquina principal, você pode configurar os scripts uma vez e, em seguida, repetir o processo para criar certificados para vários dispositivos.

Siga estas etapas para criar certificados de demonstração para testar seu cenário de IoT Edge:

1. [Configure scripts](#set-up-scripts) para geração de certificados em seu dispositivo.
2. [Crie o certificado de CA raiz](#create-root-ca-certificate) que você usa para assinar todos os outros certificados para o seu cenário.
3. Gerar os certificados necessários para o cenário que deseja testar:
   * [Crie certificados de identidade de dispositivo IoT Edge](#create-iot-edge-device-identity-certificates) para testar o provisionamento automático com o Serviço de Provisionamento de Dispositivos IoT Hub.
   * [Crie certificados CA do dispositivo IoT Edge](#create-iot-edge-device-ca-certificates) para testar cenários de produção ou cenários de gateway.
   * [Crie certificados de dispositivo a jusante](#create-downstream-device-certificates) para testar a autenticação de dispositivos a jusante no IoT Hub em um cenário de gateway.

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento com Git instalada.

## <a name="set-up-scripts"></a>Configurar scripts

O repositório IoT Edge no GitHub inclui scripts de geração de certificados que você pode usar para criar certificados de demonstração.
Esta seção fornece instruções para preparar os scripts para serem executados no seu computador, seja no Windows ou linux.
Se você estiver em uma máquina Linux, pule para [configurar no Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Configuração no Windows

Para criar certificados de demonstração em um dispositivo Windows, você precisa instalar o OpenSSL e, em seguida, clonar os scripts de geração e configurá-los para serem executados localmente no PowerShell.

#### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL para Windows no computador que você está usando para gerar os certificados.
Se você já tiver o OpenSSL instalado no seu dispositivo Windows, você pode pular essa etapa, mas certifique-se de que o openssl.exe esteja disponível na variável de ambiente PATH.

Existem várias maneiras de instalar o OpenSSL, incluindo as seguintes opções:

* **Mais fácil:** Baixe e instale quaisquer [binários OpenSSL de terceiros,](https://wiki.openssl.org/index.php/Binaries)por exemplo, do [OpenSSL no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe à variável de ambiente PATH.

* **Recomendado:** Faça o download do código-fonte do OpenSSL e construa os binários em sua máquina sozinho ou por meio do [vcpkg](https://github.com/Microsoft/vcpkg). As instruções a seguir usam vcpkg para baixar o código-fonte, compilação e instalar o OpenSSL em seu computador Windows com etapas simples.

   1. Navegue para um diretório onde você deseja instalar vcpkg. Siga as instruções para fazer o download e instalar o [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Uma vez que o vcpkg esteja instalado, execute o seguinte comando de um prompt PowerShell para instalar o pacote OpenSSL para windows x64. A instalação normalmente leva cerca de 5 minutos para ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Adicione `<vcpkg path>\installed\x64-windows\tools\openssl` à variável de ambiente PATH para que o arquivo openssl.exe fique disponível para invocação.

#### <a name="prepare-scripts-in-powershell"></a>Preparar scripts no PowerShell

O repositório git Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste.
Nesta seção, você clona o repo IoT Edge e executa os scripts.

1. Abra uma janela do PowerShell no modo de administrador.

2. Clone o ioT Edge git repo, que contém scripts para gerar certificados de demonstração. Use o comando `git clone` ou [faça o download do ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue até o diretório no qual você deseja trabalhar. Ao longo deste artigo, chamaremos este diretório * \<de WRKDIR>*. Todos os certificados e chaves serão criados neste diretório de trabalho.

4. Copie a configuração e os arquivos de script do repo clonado em seu diretório de trabalho.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se você baixou o repo como um `iotedge-master` ZIP, então o nome da pasta é e o resto do caminho é o mesmo.

5. Habilite o PowerShell para executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Traga as funções usadas pelos scripts para o namespace global da PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   A janela PowerShell exibirá um aviso de que os certificados gerados por este script são apenas para fins de teste e não devem ser usados em cenários de produção.

7. Verifique se o OpenSSL foi instalado corretamente e certifique-se de que não haverá colisões de nomes com certificados existentes. Se houver problemas, a saída de script deve descrever como corrigi-los em seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Configurar no Linux

Para criar certificados de demonstração em um dispositivo Windows, você precisa clonar os scripts de geração e configurá-los para serem executados localmente em bash.

1. Clone o ioT Edge git repo, que contém scripts para gerar certificados de demonstração.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue até o diretório no qual você deseja trabalhar. Vamos nos referir a este diretório ao longo do artigo como * \<WRKDIR>*. Todos os arquivos de certificado e chave serão criados neste diretório.
  
3. Copie os arquivos config e script do repo IoT Edge clonado em seu diretório de trabalho.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Criar certificado de CA raiz

O certificado de CA raiz é usado para fazer todos os outros certificados de demonstração para testar um cenário de IoT Edge.
Você pode continuar usando o mesmo certificado de CA raiz para fazer certificados de demonstração para vários dispositivos IoT Edge ou downstream.

Se você já tem um certificado DE CA raiz em sua pasta de trabalho, não crie um novo.
O novo certificado de CA raiz substituirá o antigo, e quaisquer certificados a jusante feitos do antigo deixarão de funcionar.
Se você quiser vários certificados de CA raiz, certifique-se de gerenciá-los em pastas separadas.

Antes de prosseguir com as etapas nesta seção, siga as etapas da seção [Configurar scripts](#set-up-scripts) para preparar um diretório de trabalho com os scripts de geração de certificado de demonstração.

### <a name="windows"></a>Windows

1. Navegue até o diretório de trabalho onde você colocou os scripts de geração de certificados.

1. Crie o certificado de CA raiz e faça com que ele assine um certificado intermediário. Os certificados estão todos colocados em seu diretório de trabalho.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Este comando script cria vários arquivos de certificado e de tecla, mas quando os artigos pedem o **certificado DE CA raiz,** use o seguinte arquivo:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navegue até o diretório de trabalho onde você colocou os scripts de geração de certificados.

1. Crie o certificado de CA raiz e um certificado intermediário.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Este comando script cria vários arquivos de certificado e de tecla, mas quando os artigos pedem o **certificado DE CA raiz,** use o seguinte arquivo:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Criar certificados CA do dispositivo IoT Edge

Todos os dispositivos IoT Edge que vão para a produção precisam de um certificado CA do dispositivo que é referenciado a partir do arquivo config.yaml.
O certificado CA do dispositivo é responsável pela criação de certificados para módulos em execução no dispositivo.
É também como o dispositivo IoT Edge verifica sua identidade ao se conectar a dispositivos a jusante.

Os certificados ca do dispositivo vão para a seção **Certificado** do arquivo config.yaml no dispositivo IoT Edge.

Antes de prosseguir com as etapas nesta seção, siga as etapas dos [scripts de configuração](#set-up-scripts) e crie seções [de certificado de CA raiz.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

1. Navegue até o diretório de trabalho que possui os scripts de geração de certificados e o certificado de CA raiz.

2. Crie o certificado CA do dispositivo IoT Edge e a chave privada com o seguinte comando. Forneça um nome para o certificado CA, por exemplo **MyEdgeDeviceCA**, que é usado para nomear os arquivos de saída.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Este comando script cria vários arquivos de certificado e chave. O seguinte certificado e par de chaves precisa ser copiado para um dispositivo IoT Edge e referenciado no arquivo config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

O nome do dispositivo de gateway passado para esses scripts não deve ser o mesmo que o parâmetro "hostname" em config.yaml, ou o ID do dispositivo no IoT Hub.
Os scripts ajudam a evitar qualquer problema anexando uma cadeia ".ca" ao nome do dispositivo de gateway para evitar a colisão de nomes no caso de um usuário configurar o IoT Edge usando o mesmo nome em ambos os locais.
No entanto, é uma boa prática evitar usar o mesmo nome.

### <a name="linux"></a>Linux

1. Navegue até o diretório de trabalho que possui os scripts de geração de certificados e o certificado de CA raiz.

2. Crie o certificado CA do dispositivo IoT Edge e a chave privada com o seguinte comando. Forneça um nome para o certificado CA, por exemplo **MyEdgeDeviceCA**, que é usado para nomear os arquivos de saída.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Este comando script cria vários arquivos de certificado e chave. O seguinte certificado e par de chaves precisa ser copiado para um dispositivo IoT Edge e referenciado no arquivo config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

O nome do dispositivo de gateway passado para esses scripts não deve ser o mesmo que o parâmetro "hostname" em config.yaml, ou o ID do dispositivo no IoT Hub.
Os scripts ajudam a evitar qualquer problema anexando uma cadeia ".ca" ao nome do dispositivo de gateway para evitar a colisão de nomes no caso de um usuário configurar o IoT Edge usando o mesmo nome em ambos os locais.
No entanto, é uma boa prática evitar usar o mesmo nome.

## <a name="create-iot-edge-device-identity-certificates"></a>Criar certificados de identidade de dispositivo IoT Edge

Os certificados de identidade do dispositivo são usados para provisionar dispositivos IoT Edge através do [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml).

Os certificados de identidade do dispositivo vão para a seção **Provisioning** do arquivo config.yaml no dispositivo IoT Edge.

Antes de prosseguir com as etapas nesta seção, siga as etapas dos [scripts de configuração](#set-up-scripts) e crie seções [de certificado de CA raiz.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

Crie o certificado de identidade do dispositivo IoT Edge e a chave privada com o seguinte comando:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

O nome que você passar para este comando será o ID do dispositivo para o dispositivo IoT Edge no IoT Hub.

O novo comando de identidade do dispositivo cria vários arquivos de certificado e chave, incluindo dois que você usará ao criar uma inscrição individual no DPS e instalar o tempo de execução do IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Crie o certificado de identidade do dispositivo IoT Edge e a chave privada com o seguinte comando:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

O nome que você passar para este comando será o ID do dispositivo para o dispositivo IoT Edge no IoT Hub.

O script cria vários arquivos de certificado e chave, incluindo dois que você usará ao criar uma inscrição individual no DPS e instalar o tempo de execução do IoT Edge:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Criar certificados de dispositivo a jusante

Se você estiver configurando um dispositivo IoT a jusante para um cenário de gateway, você pode gerar certificados de demonstração para autenticação X.509.
Existem duas maneiras de autenticar um dispositivo IoT usando certificados X.509: usando certs auto-assinados ou usando certs assinados pela Autoridade de Certificado (CA).
Para a autenticação auto-assinada X.509, às vezes chamada de autenticação de impressão digital, você precisa criar novos certificados para colocar em seu dispositivo IoT.
Esses certificados têm uma impressão digital que você compartilha com o IoT Hub para autenticação.
Para autenticação assinada pela Autoridade de Certificado X.509 (CA), você precisa de um certificado DE CA raiz registrado no IoT Hub que você usa para assinar certificados para o seu dispositivo IoT.
Qualquer dispositivo que use um certificado emitido pelo certificado ca raiz ou qualquer um de seus certificados intermediários será autorizado a autenticar.

Os scripts de geração de certificados podem ajudá-lo a fazer certificados de demonstração para testar qualquer um desses cenários de autenticação.

Antes de prosseguir com as etapas nesta seção, siga as etapas dos [scripts de configuração](#set-up-scripts) e crie seções [de certificado de CA raiz.](#create-root-ca-certificate)

### <a name="self-signed-certificates"></a>Certificados autoassinados

Quando você autentica um dispositivo IoT com certificados auto-assinados, você precisa criar certificados de dispositivo com base no certificado DE CA raiz para sua solução.
Em seguida, você recupera uma "impressão digital" hexadecimal dos certificados para fornecer ao IoT Hub.
Seu dispositivo IoT também precisa de uma cópia de seus certificados de dispositivo para que ele possa autenticar com IoT Hub.

#### <a name="windows"></a>Windows

1. Navegue até o diretório de trabalho que possui os scripts de geração de certificados e o certificado de CA raiz.

2. Crie dois certificados (primário e secundário) para o dispositivo a jusante. Uma convenção de nomeação fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, o rótulo principal ou secundário. Por exemplo: 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Este comando script cria vários arquivos de certificado e chave. Os seguintes pares de certificados e chaves precisam ser copiados para o dispositivo IoT a jusante e referenciados nos aplicativos que se conectam ao IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada de impressão digital nos contextos do IoT Hub) de cada certificado. A impressão digital é uma seqüência de caracteres hexadecimal de 40. Use o seguinte comando openssl para visualizar o certificado e encontrar a impressão digital:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Execute este comando duas vezes, uma para o certificado principal e outra para o certificado secundário. Você fornece impressões digitais para ambos os certificados quando registra um novo dispositivo IoT usando certificados X.509 auto-assinados.

#### <a name="linux"></a>Linux

1. Navegue até o diretório de trabalho que possui os scripts de geração de certificados e o certificado de CA raiz.

2. Crie dois certificados (primário e secundário) para o dispositivo a jusante. Uma convenção de nomeação fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, o rótulo principal ou secundário. Por exemplo: 

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Este comando script cria vários arquivos de certificado e chave. Os seguintes pares de certificados e chaves precisam ser copiados para o dispositivo IoT a jusante e referenciados nos aplicativos que se conectam ao IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada de impressão digital nos contextos do IoT Hub) de cada certificado. A impressão digital é uma seqüência de caracteres hexadecimal de 40. Use o seguinte comando openssl para visualizar o certificado e encontrar a impressão digital:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Você fornece a impressão digital primária e secundária ao registrar um novo dispositivo IoT usando certificados X.509 auto-assinados.

### <a name="ca-signed-certificates"></a>Certificados assinados por CA

Quando você autentica um dispositivo IoT com certificados auto-assinados, você precisa carregar o certificado de CA raiz para sua solução no IoT Hub.
Em seguida, você realiza uma verificação para provar ao IoT Hub que você possui o certificado DE CA raiz.
Finalmente, você usa o mesmo certificado de CA raiz para criar certificados de dispositivo para colocar em seu dispositivo IoT para que ele possa autenticar com IoT Hub.

Os certificados nesta seção são para as etapas [de segurança Configuração X.509 em seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Faça upload do arquivo de certificado `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`CA raiz do seu diretório de trabalho, para o seu hub de IoT.

2. Use o código fornecido no portal Azure para verificar se você possui esse certificado de CA raiz.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Crie uma cadeia de certificados para o dispositivo a jusante. Use o mesmo ID do dispositivo com o que o dispositivo está registrado no IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Este comando script cria vários arquivos de certificado e chave. Os seguintes pares de certificados e chaves precisam ser copiados para o dispositivo IoT a jusante e referenciados nos aplicativos que se conectam ao IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Faça upload do arquivo de certificado `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`CA raiz do seu diretório de trabalho, para o seu hub de IoT.

2. Use o código fornecido no portal Azure para verificar se você possui esse certificado de CA raiz.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Crie uma cadeia de certificados para o dispositivo a jusante. Use o mesmo ID do dispositivo com o que o dispositivo está registrado no IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Este comando script cria vários arquivos de certificado e chave. Os seguintes pares de certificados e chaves precisam ser copiados para o dispositivo IoT a jusante e referenciados nos aplicativos que se conectam ao IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
