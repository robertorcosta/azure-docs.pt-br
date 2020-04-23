---
title: Conectividade de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1398169c44dadcd11ad037e4e3a1cc0132e21f13
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024686"
---
# <a name="get-connected-to-azure-iot-central"></a>Conecte-se ao Azure IoT Central

*Este artigo se aplica a operadores e desenvolvedores de dispositivos.*

Este artigo descreve as opções para conectar seus dispositivos a um aplicativo Central Azure IoT.

Normalmente, você deve registrar um dispositivo em seu aplicativo antes que ele possa se conectar. No entanto, a IoT Central suporta cenários onde [os dispositivos podem se conectar sem antes serem registrados](#connect-without-registering-devices).

A IoT Central usa o [Serviço de Provisionamento de Dispositivos Hub (DPS) do Azure IoT](../../iot-dps/about-iot-dps.md) para gerenciar o processo de conexão. Um dispositivo se conecta primeiro a um ponto final DPS para recuperar as informações que precisa para se conectar ao seu aplicativo. Internamente, seu aplicativo IoT Central usa um hub IoT para lidar com a conectividade do dispositivo. O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Para garantir a comunicação entre um dispositivo e seu aplicativo, a IoT Central suporta tanto as assinaturas de acesso compartilhado (SAS) quanto os certificados X.509. Os certificados X.509 são recomendados em ambientes de produção.

Este artigo descreve os seguintes casos de uso:

- [Conecte um único dispositivo usando o SAS](#connect-a-single-device)
- [Conecte dispositivos em escala usando O SAS](#connect-devices-at-scale-using-sas)
- [Conecte dispositivos em escala usando certificados X.509](#connect-devices-using-x509-certificates) - a abordagem recomendada para ambientes de produção.
- [Conecte dispositivos sem primeiro registrá-los](#connect-without-registering-devices)
- [Conecte dispositivos que usam inscrições individuais DPS](#individual-enrollment-based-device-connectivity)
- [Conecte dispositivos usando recursos ioT plug and play (visualização)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Conectar um único dispositivo

Esta abordagem é útil quando você está experimentando com ioT central ou dispositivos de teste. Você pode usar as chaves SAS de conexão do dispositivo a partir do seu aplicativo IoT Central para conectar um dispositivo ao seu aplicativo IoT Central. Copie a _chave SAS_ do dispositivo das informações de conexão para um dispositivo registrado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

Para saber mais, consulte o [Criar e conectar um aplicativo cliente Node.js ao seu tutorial de aplicativo Azure IoT Central.](./tutorial-connect-device-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>Conecte dispositivos em escala usando O SAS

Para conectar dispositivos à IoT Central em escala usando chaves SAS, você precisa se registrar e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registre dispositivos em massa

Para registrar um grande número de dispositivos com seu aplicativo IoT Central, use um arquivo CSV para [importar IDs de dispositivos e nomes de dispositivos](howto-manage-devices.md#import-devices).

Para recuperar as informações de conexão dos dispositivos importados, [exporte um arquivo CSV do seu aplicativo IoT Central](howto-manage-devices.md#export-devices). O arquivo CSV exportado inclui as IDs do dispositivo e as chaves SAS.

### <a name="set-up-your-devices"></a>Configure seus dispositivos

Use as informações de conexão do arquivo de exportação no código do dispositivo para permitir que seus dispositivos se conectem e enviem dados para IoT para seu aplicativo IoT Central. Você também precisa do escopo dps **ID** para sua aplicação. Você pode encontrar esse valor na **conexão Administração > Dispositivo**.

> [!NOTE]
> Para saber como você pode conectar dispositivos sem primeiro registrá-los na IoT Central, consulte [Conectar sem primeiro registrar dispositivos](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Conecte dispositivos usando certificados X.509

Em um ambiente de produção, o uso de certificados X.509 é o mecanismo de autenticação de dispositivo recomendado para IoT Central. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Antes de conectar um dispositivo com um certificado X.509, adicione e verifique um certificado X.509 intermediário ou raiz em seu aplicativo. Os dispositivos devem usar certificados Leaf X.509 gerados a partir do certificado raiz ou intermediário.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Adicione e verifique um certificado raiz ou intermediário

Navegue até **administração > conexão de dispositivo > Gerencie** o certificado principal e adicione o certificado raiz ou intermediário X.509 que você está usando para gerar os certificados do dispositivo.

![Configurações de conexão](media/concepts-get-connected/manage-x509-certificate.png)

Verificar a propriedade do certificado garante que a pessoa que faz o upload do certificado tenha a chave privada do certificado. Para verificar o certificado:

  1. Selecione o botão ao lado de **Código de Verificação** para gerar um código.
  1. Crie um certificado de verificação X.509 com o código de verificação que você gerou na etapa anterior. Salve o certificado como um arquivo .cer.
  1. Faça upload do certificado de verificação assinado e selecione **Verificar**. O certificado é marcado como **Verificado** quando a verificação é bem sucedida.

Se você tiver uma falha de segurança ou seu certificado principal estiver definido para expirar, use o certificado secundário para reduzir o tempo de inatividade. Você pode continuar a provisionar dispositivos usando o certificado secundário enquanto atualiza o certificado principal.

### <a name="register-and-connect-devices"></a>Registre e conecte dispositivos

Para conectar dispositivos em massa usando certificados X.509, primeiro registre os dispositivos em seu aplicativo, usando um arquivo CSV para [importar os IDs do dispositivo e nomes de dispositivos](howto-manage-devices.md#import-devices). As iDs do dispositivo devem estar todas em minúsculas.

Gere certificados de folha X.509 para seus dispositivos usando o certificado raiz ou intermediário carregado. Use o **ID** `CNAME` do dispositivo como valor nos certificados de folha. O código do dispositivo precisa do valor **do escopo de ID** para sua aplicação, o **ID**do dispositivo e o certificado do dispositivo correspondente.

### <a name="for-testing-purposes-only"></a>Apenas para testar

Apenas para testes, você pode usar os seguintes utilitários para gerar certificados raiz, intermediário e dispositivo:

- Ferramentas para o Dispositivo de [Provisionamento de Dispositivos Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): uma coleção de ferramentas Node.js que você pode usar para gerar e verificar certificados e chaves X.509.
- Se você estiver usando um dispositivo DevKit, esta [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gera um certificado CA que você pode adicionar ao seu aplicativo IoT Central para verificar os certificados.
- [Gerenciar certificados ca de teste para amostras e tutoriais:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)uma coleção de scripts PowerShell e Bash para:
  - Crie uma cadeia de certificados.
  - Salve os certificados como arquivos .cer para carregar no seu aplicativo IoT Central.
  - Use o código de verificação do aplicativo IoT Central para gerar o certificado de verificação.
  - Crie certificados de folha para seus dispositivos usando seus IDs de dispositivo como parâmetro para a ferramenta.

### <a name="further-reference"></a>Referência adicional

- [Implementação de amostra para RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Cliente do dispositivo de amostra em C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Conecte-se sem registrar dispositivos

Todos os cenários descritos anteriormente exigem que você registre dispositivos em seu aplicativo antes que eles se conectem. A IoT Central também permite que os OEMs produzam dispositivos de fabricação em massa que podem se conectar sem antes serem registrados. Um OEM gera credenciais adequadas do dispositivo e configura os dispositivos na fábrica. Quando um cliente liga um dispositivo pela primeira vez, ele se conecta ao DPS, que então conecta automaticamente o dispositivo ao aplicativo Correto IoT Central. Um operador Central de IoT deve aprovar o dispositivo antes de começar a enviar dados para o aplicativo.

O fluxo é ligeiramente diferente dependendo se os dispositivos usam tokens SAS ou certificados X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Conecte dispositivos que usam tokens SAS sem registrar

1. Copie a chave primária do grupo do aplicativo IoT Central:

    ![Chave SAS principal do grupo de aplicativos](media/concepts-get-connected/group-sas-keys.png)

1. Use a ferramenta [dps-keygen](https://www.npmjs.com/package/dps-keygen) para gerar as teclas SAS do dispositivo. Use a tecla principal do grupo da etapa anterior. Os IDs do dispositivo devem ser minúsculos:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. O OEM exibe cada dispositivo com um ID do dispositivo, uma chave SAS de dispositivo gerado e o valor do **escopo do ID** do aplicativo.

1. Quando você liga um dispositivo, ele se conecta primeiro ao DPS para recuperar suas informações de registro da Central de IoT.

    O dispositivo inicialmente tem um status do dispositivo **não associado** na página **Dispositivos** e não é atribuído a um modelo de dispositivo. Na página **Dispositivos,** **migre** o dispositivo para o modelo apropriado do dispositivo. O provisionamento do dispositivo está concluído, o status do dispositivo agora está **provisionado**e o dispositivo pode começar a enviar dados.

    Na página **de conexão Administração > Dispositivo,** a opção **Auto aprova** os controles de opção se você precisa aprovar manualmente o dispositivo antes que ele possa começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [Conectar dispositivos com IoT Plug and Play (visualização)](#connect-devices-with-iot-plug-and-play-preview).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Conecte dispositivos que usam certificados X.509 sem registro

1. [Adicione e verifique um certificado X.509 raiz ou intermediário](#connect-devices-using-x509-certificates) para o seu aplicativo IoT Central. (#connect-dispositivos usando-x509 certificados)

1. Gere os certificados de folha para seus dispositivos usando o certificado raiz ou intermediário que você adicionou ao seu aplicativo IoT Central. Use ids de dispositivo `CNAME` minúsculo como os certificados de folha.

1. O OEM exibe cada dispositivo com um ID do dispositivo, um certificado X.509 esquerdo gerado e o valor do **escopo do ID** do aplicativo.

1. Quando você liga um dispositivo, ele se conecta primeiro ao DPS para recuperar suas informações de registro da Central de IoT.

    O dispositivo inicialmente tem um status do dispositivo **não associado** na página **Dispositivos** e não é atribuído a um modelo de dispositivo. Na página **Dispositivos,** **migre** o dispositivo para o modelo apropriado do dispositivo. O provisionamento do dispositivo está concluído, o status do dispositivo agora está **provisionado**e o dispositivo pode começar a enviar dados.

    Na página **de conexão Administração > Dispositivo,** a opção **Auto aprova** os controles de opção se você precisa aprovar manualmente o dispositivo antes que ele possa começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [Conectar dispositivos com IoT Plug and Play (visualização)](#connect-devices-with-iot-plug-and-play-preview).

## <a name="individual-enrollment-based-device-connectivity"></a>Conectividade individual de dispositivos baseado em matrícula

Para clientes que conectam dispositivos que possuem suas próprias credenciais de autenticação, use matrículas individuais. Uma inscrição individual é uma entrada para um único dispositivo que é permitido conectar. As inscrições individuais podem usar certificados de folha X.509 ou tokens SAS (a partir de um módulo de plataforma física ou virtual confiável) como mecanismos de atestado. O ID do dispositivo (também conhecido como ID de registro) em uma matrícula individual é alfanumérica, minúscula, e pode conter hífens. Para obter mais informações, consulte [a inscrição individual do DPS](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Quando você cria uma inscrição individual para um dispositivo, ela tem precedência sobre as opções de inscrição de grupo padrão em seu aplicativo IoT Central.

### <a name="creating-individual-enrollments"></a>Criação de matrículas individuais

A IoT Central suporta os seguintes mecanismos de atestado para matrículas individuais:

- **Atestado de chave simétrica:** Atestado de chave simétrico é uma abordagem simples para autenticar um dispositivo com a instância DPS. Para criar uma inscrição individual que use chaves simétricas, abra a página **Conexão de dispositivo,** selecione **Inscrição individual** como método de conexão e **Assinatura de acesso Compartilhado (SAS)** como mecanismo. Digite as teclas primárias e secundárias codificadas base64 e salve suas alterações. Use o **escopo de ID,** **ID do dispositivo**e a chave primária ou secundária para conectar o dispositivo.

    > [!TIP]
    > Para testes, você pode usar **o OpenSSL** para gerar chaves codificadas base64:`openssl rand -base64 64`

- **Certificados X.509:** Para criar uma inscrição individual com certificados X.509, abra a página **Conexão de Dispositivo,** selecione **Inscrição individual** como método de conexão e **Certificados (X.509)** como mecanismo. Os certificados de dispositivo usados com uma entrada de inscrição individual têm um requisito para que o emissor e o sujeito CN sejam definidos como iD do dispositivo.

    > [!TIP]
    > Para testes, você pode usar [ferramentas para o Dispositivo de Provisionamento de Dispositivos Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) para gerar um certificado auto-assinado:`node create_test_cert.js device "mytestdevice"`

- **Atestado de Módulo de Plataforma Confiável (TPM):** Um [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) é um tipo de módulo de segurança de hardware. O uso de um TPM é uma das maneiras mais seguras de conectar um dispositivo. Este artigo pressupõe que você está usando um TPM discreto, firmware ou integrado. Os TPMs emulados por software são adequados para prototipagem ou testes, mas não fornecem o mesmo nível de segurança que TPMs discretos, firmware ou integrados. Não use TPMs de software na produção. Para criar uma inscrição individual que use um TPM, abra a página **Conexão de dispositivo,** selecione **Inscrição individual** como método de conexão e **TPM** como mecanismo. Digite a tecla de endosso TPM e salve as informações de conexão do dispositivo.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Conecte dispositivos com IoT Plug and Play (visualização)

Uma das principais características do IoT Plug and Play (preview) com a IoT Central é a capacidade de associar modelos de dispositivos automaticamente na conexão do dispositivo. Juntamente com as credenciais do dispositivo, os dispositivos agora podem enviar o **CapabilityModelId** como parte da chamada de registro do dispositivo. Esse recurso permite que a IoT Central descubra e associe o modelo do dispositivo com o dispositivo. O processo de descoberta funciona da seguinte forma:

1. Associa-se ao modelo do dispositivo se ele já estiver publicado no aplicativo IoT Central.
1. Busca do repositório público de modelos de capacidade publicados e certificados.

Abaixo está o formato da carga adicional que o dispositivo enviaria durante a chamada de registro do DPS

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Observe que a opção **Auto approve** on Administration > **Device connection** deve ser habilitada para que os dispositivos se conectem automaticamente, descubram o modelo do dispositivo e comecem a enviar dados.

## <a name="device-status-values"></a>Valores de status do dispositivo

Quando um dispositivo real se conecta ao seu aplicativo IoT Central, o status do dispositivo muda da seguinte forma:

1. O status do dispositivo é **primeiro registrado**. Esse status significa que o dispositivo é criado na IoT Central e tem um ID do dispositivo. Um dispositivo é registrado quando:
    - Um novo dispositivo real é adicionado na página **Dispositivos.**
    - Um conjunto de dispositivos é adicionado usando **Importação** na página **Dispositivos.**

1. O status do dispositivo muda para **Provisionado** quando o dispositivo conectado ao seu aplicativo IoT Central com credenciais válidas completa a etapa de provisionamento. Nesta etapa, o dispositivo usa DPS para recuperar automaticamente uma seqüência de conexão do IoT Hub usado pelo seu aplicativo IoT Central. O dispositivo agora pode se conectar à Central de IoT e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo é bloqueado, ele não pode enviar dados para o seu aplicativo IoT Central. Os dispositivos bloqueados têm um status de **Bloqueado**. Um operador deve redefinir o dispositivo antes que ele possa retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o status retorna ao seu valor anterior, **Registrado** ou **Provisionado**.

1. Se o status do dispositivo estiver **aguardando aprovação,** significa que a opção **auto aprovação** está desativada. Um operador deve aprovar explicitamente um dispositivo antes de começar a enviar dados. Dispositivos não registrados manualmente na página Dispositivos, mas conectados com credenciais **válidas,** terão o status do dispositivo **à espera de aprovação**. Os operadores podem aprovar esses dispositivos a partir da página **Dispositivos** usando o botão **Aprovar.**

1. Se o status do dispositivo **estiver não associado,** significa que o dispositivo que se conecta à IoT Central não tem um modelo de dispositivo associado. Essa situação normalmente acontece nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando **Importação** na página **Dispositivos** sem especificar o modelo do dispositivo.
    - Um dispositivo foi registrado manualmente na página **Dispositivos** sem especificar o modelo do dispositivo. O dispositivo então conectado com credenciais válidas.  

    O Operador pode associar um dispositivo a um modelo de dispositivo a partir da página **Dispositivos** usando o botão **Migrar.**

## <a name="best-practices"></a>Práticas recomendadas

Não persista ou escoe a seqüência de conexão do dispositivo que o DPS retorna quando você conecta o dispositivo pela primeira vez. Para reconectar um dispositivo, passe pelo fluxo de registro padrão do dispositivo para obter a seqüência de conexão correta do dispositivo. Se o dispositivo armazenar a seqüência de conexões, o software do dispositivo corre o risco de ter uma seqüência de conexão obsoleta se a IoT Central atualizar o hub DeI do Azure subjacente que ele usa.

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs do Dispositivo Azure oferecem a maneira mais fácil para você implementar o código do seu dispositivo. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do Azure IoT para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do Azure IoT para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do Azure IoT para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens do dispositivo para a nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Gêmeos do dispositivo](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Telemetria | Mensagens do dispositivo para a nuvem |
| Propriedade | Propriedades relatadas do dispositivo gêmeo |
| Propriedade (gravável) | Propriedades desejadas e relatadas do dispositivo gêmeo |
| Comando | Métodos diretos |

Para saber mais sobre como usar os SDKs do dispositivo, consulte [Conecte um dispositivo de kit DevDiv ao seu aplicativo Azure IoT Central,](howto-connect-devkit.md) por exemplo.

### <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, será possível usar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para transferir o processamento para a borda do aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [O controle de acesso ao IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos são:

- Saiba como monitorar a [conectividade do dispositivo usando o Azure CLI](./howto-monitor-devices-azure-cli.md)
- Saiba como [definir um novo tipo de dispositivo IoT no seu aplicativo Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md)
