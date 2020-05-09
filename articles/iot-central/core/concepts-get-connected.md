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
ms.openlocfilehash: ddbb1c6fd705e658867c0d594981e87bc8cd6afe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930481"
---
# <a name="get-connected-to-azure-iot-central"></a>Conectar-se ao Azure IoT Central

*Este artigo se aplica a operadores e desenvolvedores de dispositivos.*

Este artigo descreve as opções para conectar seus dispositivos a um aplicativo IoT Central do Azure.

Normalmente, você deve registrar um dispositivo em seu aplicativo antes que ele possa se conectar. No entanto, o IoT Central oferece suporte a cenários em que os [dispositivos podem se conectar sem primeiro serem registrados](#connect-without-registering-devices).

IoT Central usa o [DPS (serviço de provisionamento de dispositivos) do Hub IOT do Azure](../../iot-dps/about-iot-dps.md) para gerenciar o processo de conexão. Um dispositivo se conecta pela primeira vez a um ponto de extremidade de DPS para recuperar as informações necessárias para se conectar ao seu aplicativo. Internamente, seu aplicativo IoT Central usa um hub IoT para lidar com a conectividade do dispositivo. O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Para proteger a comunicação entre um dispositivo e seu aplicativo, o IoT Central dá suporte a SAS (assinaturas de acesso compartilhado) e a certificados X. 509. Os certificados X. 509 são recomendados em ambientes de produção.

Este artigo descreve os seguintes casos de uso:

- [Conectar um único dispositivo usando SAS](#connect-a-single-device)
- [Conectar dispositivos em escala usando SAS](#connect-devices-at-scale-using-sas)
- [Conecte dispositivos em escala usando certificados X. 509](#connect-devices-using-x509-certificates) -a abordagem recomendada para ambientes de produção.
- [Conectar dispositivos sem primeiro registrá-los](#connect-without-registering-devices)
- [Conectar dispositivos que usam registros individuais de DPS](#individual-enrollment-based-device-connectivity)
- [Associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Conectar um único dispositivo

Essa abordagem é útil quando você está experimentando IoT Central ou dispositivos de teste. Você pode usar as chaves SAS de conexão do dispositivo do seu aplicativo IoT Central para conectar um dispositivo ao seu aplicativo IoT Central. Copie a _chave SAS do dispositivo_ das informações de conexão de um dispositivo registrado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

Para saber mais, consulte o tutorial [criar e conectar um aplicativo cliente Node. js ao seu aplicativo IOT central do Azure](./tutorial-connect-device-nodejs.md) .

## <a name="connect-devices-at-scale-using-sas"></a>Conectar dispositivos em escala usando SAS

Para conectar dispositivos a IoT Central em escala usando chaves SAS, você precisa registrar e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registrar dispositivos em massa

Para registrar um grande número de dispositivos com seu aplicativo IoT Central, use um arquivo CSV para [importar IDs de dispositivo e nomes de dispositivo](howto-manage-devices.md#import-devices).

Para recuperar as informações de conexão dos dispositivos importados, [exporte um arquivo CSV de seu aplicativo IOT central](howto-manage-devices.md#export-devices). O arquivo CSV exportado inclui as IDs de dispositivo e as chaves SAS.

### <a name="set-up-your-devices"></a>Configure seus dispositivos

Use as informações de conexão do arquivo de exportação no código do dispositivo para permitir que seus dispositivos se conectem e enviem dados para o IoT para seu aplicativo IoT Central. Você também precisa do **escopo da ID** de DPS para seu aplicativo. Você pode encontrar esse valor em **administração > conexão do dispositivo**.

> [!NOTE]
> Para saber como você pode conectar dispositivos sem primeiro registrá-los no IoT Central, consulte [conectar sem primeiro registrar os dispositivos](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Conectar dispositivos usando certificados X. 509

Em um ambiente de produção, o uso de certificados X. 509 é o mecanismo de autenticação de dispositivo recomendado para IoT Central. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Antes de conectar um dispositivo com um certificado X. 509, adicione e verifique um certificado X. 509 intermediário ou raiz em seu aplicativo. Os dispositivos devem usar certificados X. 509 de folha gerados a partir do certificado raiz ou intermediário.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Adicionar e verificar um certificado raiz ou intermediário

Navegue até **administração > conexão de dispositivo > gerenciar certificado primário** e adicione a raiz X. 509 ou o certificado intermediário que você está usando para gerar os certificados de dispositivo.

![Configurações de conexão](media/concepts-get-connected/manage-x509-certificate.png)

Verificar a propriedade do certificado garante que a pessoa que está carregando o certificado tenha a chave privada do certificado. Para verificar o certificado:

  1. Selecione o botão ao lado do **código de verificação** para gerar um código.
  1. Crie um certificado de verificação X. 509 com o código de verificação gerado na etapa anterior. Salve o certificado como um arquivo. cer.
  1. Carregue o certificado de verificação assinado e selecione **verificar**. O certificado é marcado como **verificado** quando a verificação é bem-sucedida.

Se você tiver uma violação de segurança ou se o certificado principal estiver definido como expirar, use o certificado secundário para reduzir o tempo de inatividade. Você pode continuar a provisionar dispositivos usando o certificado secundário enquanto atualiza o certificado primário.

### <a name="register-and-connect-devices"></a>Registrar e conectar dispositivos

Para conectar dispositivos em massa usando certificados X. 509, primeiro registre os dispositivos em seu aplicativo, usando um arquivo CSV para [importar os nomes de dispositivo e as IDs de dispositivo](howto-manage-devices.md#import-devices). Todas as identificações de dispositivo devem estar em letras minúsculas.

Gere certificados de folha X. 509 para seus dispositivos usando o certificado raiz ou intermediário carregado. Use a **ID do dispositivo** como `CNAME` o valor nos certificados folha. O código do dispositivo precisa do valor de **escopo da ID** para seu aplicativo, a **ID do dispositivo**e o certificado de dispositivo correspondente.

#### <a name="sample-device-code"></a>Código do dispositivo de exemplo

O exemplo a seguir do [SDK do node. js do Azure IOT](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) mostra como um cliente do dispositivo node. js usa um certificado de folha X. 509 e um DPS para registrar com um aplicativo IOT central:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Para obter um exemplo de C equivalente, consulte [prov_dev_client_sample. C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) no [SDK do cliente do dispositivo de provisionamento do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Somente para fins de teste

Somente para teste, você pode usar os seguintes utilitários para gerar certificados raiz, intermediários e de dispositivo:

- [Ferramentas para o SDK do dispositivo de provisionamento de dispositivos IOT do Azure](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): uma coleção de ferramentas do node. js que você pode usar para gerar e verificar certificados X. 509 e chaves.
- Se você estiver usando um dispositivo DevKit, essa [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gerará um certificado de autoridade de certificação que você pode adicionar ao seu aplicativo IOT central para verificar os certificados.
- [Gerenciar certificados de CA de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): uma coleção de scripts do PowerShell e bash para:
  - Crie uma cadeia de certificados.
  - Salve os certificados como arquivos. cer para carregar em seu aplicativo IoT Central.
  - Use o código de verificação do aplicativo IoT Central para gerar o certificado de verificação.
  - Crie certificados folha para seus dispositivos usando suas IDs de dispositivo como um parâmetro para a ferramenta.

## <a name="connect-without-registering-devices"></a>Conectar sem registrar dispositivos

Os cenários descritos anteriormente exigem que você registre dispositivos em seu aplicativo antes que eles se conectem. IoT Central também permite que os OEMs fabricam dispositivos em massa que podem se conectar sem antes serem registrados. Um OEM gera credenciais de dispositivo adequadas e configura os dispositivos na fábrica. Quando um cliente liga um dispositivo pela primeira vez, ele se conecta ao DPS, que conecta automaticamente o dispositivo ao aplicativo de IoT Central correto. Um operador de IoT Central deve aprovar o dispositivo antes de começar a enviar dados para o aplicativo.

O fluxo é ligeiramente diferente dependendo se os dispositivos usam tokens SAS ou certificados X. 509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Conectar dispositivos que usam tokens SAS sem registro

1. Copie a chave primária do grupo do aplicativo IoT Central:

    ![Chave SAS primária do grupo de aplicativos](media/concepts-get-connected/group-sas-keys.png)

1. Use a ferramenta [DPS-keygen](https://www.npmjs.com/package/dps-keygen) para gerar as chaves SAS do dispositivo. Use a chave primária do grupo da etapa anterior. As IDs de dispositivo devem estar em letras minúsculas:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. O OEM pisca cada dispositivo com uma ID de dispositivo, uma chave de SAS de dispositivo gerada e o valor de **escopo da ID** do aplicativo.

1. Quando você alterna um dispositivo, ele se conecta primeiro ao DPS para recuperar suas informações de registro IoT Central.

    Inicialmente, o dispositivo tem um status de dispositivo não **associado** na página **dispositivos** e não é atribuído a um modelo de dispositivo. Na página **dispositivos** , **migre** o dispositivo para o modelo de dispositivo apropriado. O provisionamento do dispositivo agora está concluído, o status do dispositivo agora é **provisionado**e o dispositivo pode começar a enviar dados.

    Na página de **conexão do dispositivo > de administração** , a opção de **aprovação automática** controla se você precisa aprovar o dispositivo manualmente antes que ele possa começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Conectar dispositivos que usam certificados X. 509 sem registro

1. [Adicione e verifique um certificado X. 509 de raiz ou intermediário](#connect-devices-using-x509-certificates) ao seu aplicativo IOT central.

1. Gere os certificados de folha para seus dispositivos usando o certificado raiz ou intermediário que você adicionou ao seu aplicativo IoT Central. Use IDs de dispositivo em minúsculas como `CNAME` o nos certificados de folha.

1. O OEM pisca cada dispositivo com uma ID de dispositivo, um certificado de folha X. 509 gerado e o valor de **escopo da ID** do aplicativo.

1. Quando você alterna um dispositivo, ele se conecta primeiro ao DPS para recuperar suas informações de registro IoT Central.

    Inicialmente, o dispositivo tem um status de dispositivo não **associado** na página **dispositivos** e não é atribuído a um modelo de dispositivo. Na página **dispositivos** , **migre** o dispositivo para o modelo de dispositivo apropriado. O provisionamento do dispositivo agora está concluído, o status do dispositivo agora é **provisionado**e o dispositivo pode começar a enviar dados.

    Na página de **conexão do dispositivo > de administração** , a opção de **aprovação automática** controla se você precisa aprovar o dispositivo manualmente antes que ele possa começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Conectividade de dispositivo baseada em registro individual

Para clientes que conectam dispositivos que têm suas próprias credenciais de autenticação, use registros individuais. Um registro individual é uma entrada para um único dispositivo que tem permissão para se conectar. Registros individuais podem usar certificados de folha X. 509 ou tokens SAS (de um módulo de plataforma confiável virtual ou física) como mecanismos de atestado. A ID do dispositivo (também conhecida como ID de registro) em um registro individual é alfanumérica, em letras minúsculas e pode conter hifens. Para obter mais informações, consulte [DPS individual registro](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Quando você cria um registro individual para um dispositivo, ele tem precedência sobre as opções de registro de grupo padrão em seu aplicativo IoT Central.

### <a name="create-individual-enrollments"></a>Criar registros individuais

O IoT Central dá suporte aos seguintes mecanismos de atestado para registros individuais:

- **Atestado de chave simétrica:** O atestado de chave simétrica é uma abordagem simples para autenticar um dispositivo com a instância do DPS. Para criar um registro individual que usa chaves simétricas, abra a página **conexão do dispositivo** , selecione **registro individual** como o método de conexão e **SAS (assinatura de acesso compartilhado)** como o mecanismo. Insira as chaves primárias e secundárias codificadas na Base64 e salve as alterações. Use o **escopo da ID**, a **ID do dispositivo**e a chave primária ou secundária para conectar seu dispositivo.

    > [!TIP]
    > Para teste, você pode usar o **OpenSSL** para gerar chaves codificadas em base64:`openssl rand -base64 64`

- **Certificados X. 509:** Para criar um registro individual com certificados X. 509, abra a página **conexão do dispositivo** , selecione **registro individual** como o método de conexão e **certificados (X. 509)** como o mecanismo. Os certificados de dispositivo usados com uma entrada de registro individual têm um requisito de que o emissor e o certificado CN estejam definidos para a ID do dispositivo.

    > [!TIP]
    > Para teste, você pode usar [ferramentas para o SDK do dispositivo de provisionamento de dispositivos IOT do Azure para node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) para gerar um certificado autoassinado:`node create_test_cert.js device "mytestdevice"`

- **Atestado de Trusted Platform Module (TPM):** Um [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) é um tipo de módulo de segurança de hardware. Usar um TPM é uma das maneiras mais seguras de conectar um dispositivo. Este artigo pressupõe que você esteja usando um TPM discreto, de firmware ou integrado. O software emulado TPMs é adequado para o protótipo ou teste, mas eles não fornecem o mesmo nível de segurança que o TPMs integrado, de firmware ou discreto. Não use TPMs de software em produção. Para criar um registro individual que usa um TPM, abra a página **conexão do dispositivo** , selecione **registro individual** como o método de conexão e **TPM** como o mecanismo. Insira a chave de endosso do TPM e salve as informações de conexão do dispositivo.

## <a name="automatically-associate-with-a-device-template"></a>Associar automaticamente a um modelo de dispositivo

Um dos principais recursos do IoT Central é a capacidade de associar modelos de dispositivo automaticamente na conexão do dispositivo. Junto com as credenciais do dispositivo, os dispositivos podem enviar um **CapabilityModelId** como parte da chamada de registro do dispositivo. O **CapabilityModelID** é um urn que identifica o modelo de funcionalidade que o dispositivo implementa. O aplicativo IoT Central pode usar o **CapabilityModelID** para identificar o modelo de dispositivo a ser usado e, em seguida, associar automaticamente o dispositivo ao modelo de dispositivo. O processo de descoberta funciona da seguinte maneira:

1. Se o modelo de dispositivo já estiver publicado no aplicativo IoT Central, o dispositivo será associado ao modelo de dispositivo.
1. Para dispositivos IoT Plug and Play pré-certificados, se o modelo de dispositivo ainda não estiver publicado no aplicativo IoT Central, o modelo de dispositivo será obtido do repositório público.

Os trechos de código a seguir mostram o formato da carga adicional que o dispositivo deve enviar durante a chamada de registro de DPS para que a associação automática funcione.

Este é o formato para dispositivos que usam o SDK do dispositivo geralmente disponível que não dá suporte a Plug and Play IoT:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Este é o formato para dispositivos que usam o SDK do dispositivo de visualização pública que oferece suporte a Plug and Play IoT:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> A opção de **aprovação automática** na **Administração > conexão de dispositivo** deve ser habilitada para que os dispositivos se conectem automaticamente, descubram o modelo de dispositivo e comecem a enviar dados.

## <a name="device-status-values"></a>Valores de status do dispositivo

Quando um dispositivo real se conecta ao seu aplicativo IoT Central, o status do dispositivo é alterado da seguinte maneira:

1. O status do dispositivo é **registrado**primeiro. Esse status significa que o dispositivo foi criado em IoT Central e tem uma ID de dispositivo. Um dispositivo é registrado quando:
    - Um novo dispositivo real é adicionado na página **dispositivos** .
    - Um conjunto de dispositivos é adicionado usando a **importação** na página **dispositivos** .

1. O status do dispositivo muda para **provisionado** quando o dispositivo conectado ao seu aplicativo IOT central com credenciais válidas conclui a etapa de provisionamento. Nesta etapa, o dispositivo usa o DPS para recuperar automaticamente uma cadeia de conexão do Hub IoT usado pelo seu aplicativo IoT Central. Agora, o dispositivo pode se conectar a IoT Central e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo é bloqueado, ele não pode enviar dados para seu aplicativo IoT Central. Os dispositivos bloqueados têm um status de **bloqueado**. Um operador deve redefinir o dispositivo antes que ele possa retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o status retorna ao seu valor anterior, **registrado** ou **provisionado**.

1. Se o status do dispositivo estiver **aguardando aprovação**, significa que a opção de **aprovação automática** está desabilitada. Um operador deve aprovar explicitamente um dispositivo antes de começar a enviar dados. Os dispositivos não registrados manualmente na página **dispositivos** , mas conectados com credenciais válidas terão o status do dispositivo **aguardando aprovação**. Os operadores podem aprovar esses dispositivos na página **dispositivos** usando o botão **aprovar** .

1. Se o status do dispositivo for não **associado**, significa que o dispositivo que está se conectando ao IOT central não tem um modelo de dispositivo associado. Essa situação normalmente ocorre nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando a **importação** na página **dispositivos** sem especificar o modelo de dispositivo.
    - Um dispositivo foi registrado manualmente na página **dispositivos** sem especificar o modelo de dispositivo. O dispositivo então se conectou com credenciais válidas.  

    O operador pode associar um dispositivo a um modelo de dispositivo da página **dispositivos** usando o botão **migrar** .

## <a name="best-practices"></a>Práticas recomendadas

Não persista ou armazene em cache a cadeia de conexão do dispositivo que o DPS retorna quando você conecta o dispositivo pela primeira vez. Para reconectar um dispositivo, percorra o fluxo de registro de dispositivo padrão para obter a cadeia de conexão de dispositivo correta. Se o dispositivo armazenar em cache a cadeia de conexão, o software do dispositivo correrá o risco de ter uma cadeia de conexão obsoleta se IoT Central atualizar o Hub IoT subjacente do Azure que ele usa.

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs de dispositivo do Azure oferecem a maneira mais fácil de implementar o código do dispositivo. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do Azure IoT para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do Azure IoT para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do Azure IoT para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens do dispositivo para a nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivo gêmeos](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Telemetria | Mensagens do dispositivo para a nuvem |
| Propriedade | Propriedades relatadas do dispositivo gêmeo |
| Propriedade (gravável) | Propriedades desejadas e relatadas do dispositivo gêmeo |
| Comando | Métodos diretos |

Para saber mais sobre como usar os SDKs do dispositivo, consulte [conectar um dispositivo do kit do representante ao aplicativo de IOT central do Azure](howto-connect-devkit.md) para obter um exemplo de código.

### <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, será possível usar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para transferir o processamento para a borda do aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [controlar o acesso ao Hub IOT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas serão:

- Saiba como [monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
- Saiba como [definir um novo tipo de dispositivo IOT em seu aplicativo de IOT central do Azure](./howto-set-up-template.md)
- Leia sobre [dispositivos Azure IOT Edge e IOT central do Azure](./concepts-iot-edge.md)
