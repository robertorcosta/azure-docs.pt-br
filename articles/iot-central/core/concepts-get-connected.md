---
title: Conectividade de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 82d797189096994e02c77e9d342c00b13dfa187d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337085"
---
# <a name="get-connected-to-azure-iot-central"></a>Conectar-se ao Azure IoT Central

*Este artigo se aplica a operadores e desenvolvedores de dispositivos.*

Este artigo descreve as opções para conectar seus dispositivos a um aplicativo Azure IoT Central.

Geralmente, você deve registrar um dispositivo em seu aplicativo antes que ele possa se conectar. No entanto, o IoT Central tem suporte a cenários em que [dispositivos podem se conectar sem terem sido registrados](#connect-without-registering-devices).

O IoT Central usa o [DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT](../../iot-dps/about-iot-dps.md) para gerenciar o processo de conexão. Primeiro, o dispositivo se conecta a um ponto de extremidade do DPS para recuperar as informações necessárias para se conectar ao seu aplicativo. Internamente, seu aplicativo IoT Central usa um hub IoT para tratar da conectividade do dispositivo. O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Para proteger a comunicação entre um dispositivo e seu aplicativo, o IoT Central tem suporte para SAS (assinaturas de acesso compartilhado) e para certificados X.509. Os certificados X.509 são recomendados para ambientes de produção.

Este artigo descreve os seguintes casos de uso:

- [Conectar um único dispositivo usando SAS](#connect-a-single-device)
- [Conectar dispositivos em escala usando SAS](#connect-devices-at-scale-using-sas)
- [Conecte dispositivos em escala usando certificados X.509](#connect-devices-using-x509-certificates): a abordagem recomendada para ambientes de produção.
- [Conectar dispositivos sem registrá-los antes](#connect-without-registering-devices)
- [Conectar dispositivos que usam registros individuais do DPS](#individual-enrollment-based-device-connectivity)
- [Associar um dispositivo a um modelo de dispositivo automaticamente](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Conectar um único dispositivo

Essa abordagem é útil quando você estiver experimentando o IoT Central ou dispositivos de teste. Você pode usar as chaves SAS de conexão do dispositivo do seu aplicativo IoT Central para conectar um dispositivo ao esse aplicativo. Copie a _chave SAS do dispositivo_ das informações de conexão de um dispositivo registrado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

Para saber mais, consulte o tutorial [Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central (Node.js)](./tutorial-connect-device-nodejs.md).

## <a name="connect-devices-at-scale-using-sas"></a>Conectar dispositivos em escala usando SAS

Para conectar dispositivos ao IoT Central em escala usando chaves SAS, você precisa registrar e configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registrar dispositivos em massa

Para registrar um grande número de dispositivos com seu aplicativo IoT Central, use um arquivo CSV para [importar IDs e nomes de dispositivo](howto-manage-devices.md#import-devices).

Para recuperar as informações de conexão dos dispositivos importados, [exporte um arquivo CSV do seu aplicativo IoT Central](howto-manage-devices.md#export-devices). O arquivo CSV exportado inclui as IDs de dispositivo e as chaves SAS.

### <a name="set-up-your-devices"></a>Configurar seus dispositivos

Use as informações de conexão do arquivo de exportação no seu código do dispositivo para permitir que seus dispositivos se conectem e enviem dados ao IoT para seu aplicativo IoT Central. Você também precisa do **escopo da ID** do DPS para seu aplicativo. Você pode encontrar esse valor em **Administração > Conexão de dispositivo**.

> [!NOTE]
> Para saber como é possível conectar dispositivos sem antes precisar registrá-los no IoT Central, consulte [Conectar dispositivos sem registrá-los antes](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Conectar dispositivos usando certificados X.509

Em um ambiente de produção, o uso de certificados X.509 é o mecanismo de autenticação de dispositivos recomendado para o IoT Central. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Para conectar um dispositivo com um certificado X. 509 ao seu aplicativo:

1. Crie um *grupo de registro* que usa o tipo de atestado **certificados (X. 509)** .
2. Adicione e verifique um certificado X. 509 raiz ou intermediário no grupo de registro.
3. Registre e conecte dispositivos que usam certificados X. 509 de folha gerados a partir do certificado raiz ou intermediário no grupo de registro.

### <a name="create-an-enrollment-group"></a>Criar um grupo de registros

Um [grupo de registro](../../iot-dps/concepts-service.md#enrollment) é um grupo de dispositivos que compartilham o mesmo tipo de atestado. Os dois tipos de atestado com suporte são certificados X. 509 e SAS:

- Em um grupo de registro X. 509, todos os dispositivos que se conectam ao IoT Central usam certificados folha X. 509 gerados a partir do certificado raiz ou intermediário no grupo de registro.
- Em um grupo de registro de SAS, todos os dispositivos que se conectam ao IoT Central usam um token SAS gerado do token SAS no grupo de registro.

Os dois grupos de registro padrão em cada aplicativo IoT Central são grupos de registro de SAS – um para dispositivos IoT e outro para dispositivos Azure IoT Edge. Para criar um grupo de registro X. 509, navegue até a página **conexão do dispositivo** e selecione **+ Adicionar grupo de registro**:

:::image type="content" source="media/concepts-get-connected/add-enrollment-group.png" alt-text="Adicionar uma captura de tela do grupo de registro X. 509":::

### <a name="add-and-verify-a-root-or-intermediate-x509-certificate"></a>Adicionar e verificar um certificado X. 509 de raiz ou intermediário

Para adicionar e verificar um certificado raiz ou intermediário ao seu grupo de registros:

1. Navegue até o grupo de registro X. 509 que você acabou de criar. Você tem a opção de adicionar os certificados X. 509 primários e secundários. Selecione **+ gerenciar primário**.

1. Na **página certificado primário**, carregue o certificado X. 509 primário. Este é o certificado raiz ou intermediário:

    :::image type="content" source="media/concepts-get-connected/upload-primary-certificate.png" alt-text="Captura de tela do certificado primário":::

1. Use o **código de verificação** para gerar um código de verificação na ferramenta que você está usando. Em seguida, selecione **verificar** para carregar o certificado de verificação.

1. Quando a verificação for bem-sucedida, você verá a seguinte confirmação:

    :::image type="content" source="media/concepts-get-connected/verified-primary-certificate.png" alt-text="Captura de tela do certificado primário verificado":::

A verificação da propriedade do certificado garante que a pessoa que está carregando o certificado tenha a chave privada dele.

Caso ocorra uma violação de segurança ou caso seu certificado principal esteja definido como expirar, use o certificado secundário para reduzir o tempo de inatividade. Você pode continuar a provisionar dispositivos usando o certificado secundário enquanto atualiza o certificado primário.

### <a name="register-and-connect-devices"></a>Registrar e conectar dispositivos

Para conectar dispositivos em massa usando certificados X. 509, primeiro registre os dispositivos em seu aplicativo usando um arquivo CSV para [importar os nomes de dispositivo e as IDs de dispositivo](howto-manage-devices.md#import-devices). Todas as IDs de dispositivo devem estar em letras minúsculas.

Gere certificados de folha X. 509 para seus dispositivos usando o certificado raiz ou intermediário que você carregou no grupo de registro X. 509. Use a **ID do dispositivo** como o valor `CNAME` nos certificados de folha. Seu código do dispositivo precisa do valor do **escopo da ID** do aplicativo, a **ID do dispositivo** e o certificado de dispositivo correspondente.

#### <a name="sample-device-code"></a>Código do dispositivo de amostra

A amostra a seguir do [SDK do Node.JS do Azure IoT](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) mostra como um cliente do dispositivo Node.js usa um certificado de folha X.509 e um DPS para se registrar com um aplicativo IoT Central:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Para obter uma amostra C equivalente, consulte [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) no [SDK do cliente do dispositivo de provisionamento C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Apenas para fins de teste

Apenas para testes, você pode usar os utilitários a seguir para gerar certificados raiz, intermediários e de dispositivo:

- [Ferramentas para o SDK do dispositivo de provisionamento de dispositivos IoT do Azure](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): uma coleção de ferramentas do Node.js que você pode usar para gerar e verificar certificados e chaves X.509.
- Se você estiver usando um dispositivo DevKit, essa [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gerará um certificado de Autoridade de Certificação que você pode adicionar ao seu aplicativo IoT Central para verificar os certificados.
- [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): uma coleção de scripts do PowerShell e Bash para:
  - Criar uma cadeia de certificados.
  - Salve os certificados como arquivos .cer para carregar em seu aplicativo IoT Central.
  - Use o código de verificação do aplicativo IoT Central para gerar o certificado de verificação.
  - Crie certificados de folha para seus dispositivos usando suas IDs de dispositivo como um parâmetro para a ferramenta.

## <a name="connect-without-registering-devices"></a>Conectar dispositivos sem registrá-los

Todos os cenários descritos anteriormente exigem que você registre dispositivos em seu aplicativo antes de conectá-los. O IoT Central também permite que os OEMs fabriquem dispositivos em massa que podem ser conectados sem registrá-los primeiro. Um OEM gera credenciais de dispositivo adequadas e configura os dispositivos na fábrica. Quando um cliente ativa um dispositivo pela primeira vez, ele se conecta ao DPS, o qual, por sua vez, conecta automaticamente o dispositivo ao aplicativo IoT Central correto. Um operador do IoT Central deve aprovar o dispositivo antes de começar a enviar dados para o aplicativo.

O fluxo é ligeiramente diferente dependendo de se os dispositivos usam tokens SAS ou certificados X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Conectar dispositivos que usam tokens SAS sem registrá-los

1. Copie a chave primária do grupo do grupo de registro **SAS-IOT-Devices** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Chave primária do grupo do grupo de registros SAS-IoT-Devices":::

1. Use a ferramenta [dps-keygen](https://www.npmjs.com/package/dps-keygen) para gerar as chaves SAS do dispositivo. Use a chave primária do grupo da etapa anterior. As IDs do dispositivo devem estar em letras minúsculas:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. O OEM fornece a cada dispositivo uma ID de dispositivo, uma chave SAS de dispositivo gerada e o valor do **escopo de ID** do aplicativo.

1. Ao ligar um dispositivo, primeiro ele se conecta ao DPS para recuperar suas informações de registro do IoT Central.

    Inicialmente, o dispositivo tem um status de dispositivo **Não associado** na página **Dispositivos** e não está atribuído a um modelo de dispositivo. Na página **Dispositivos**, **Migre** o dispositivo para o modelo de dispositivo apropriado. O provisionamento do dispositivo foi concluído, agora o status do dispositivo está como **Provisionado**, e o dispositivo pode começar a enviar dados.

    Na página **Administração > Conexão do dispositivo**, a opção **Aprovar automaticamente** controla se você precisa aprovar o dispositivo manualmente antes que ele possa começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [Associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Conectar dispositivos que usam certificados X.509 sem registrá-los

1. [Crie um grupo de registro](#create-an-enrollment-group) e, em seguida, [adicione e verifique um certificado X. 509 intermediário ou raiz](#add-and-verify-a-root-or-intermediate-x509-certificate) para seu aplicativo IOT central.

1. Gere os certificados de folha para seus dispositivos usando o certificado raiz ou intermediário que você adicionou ao seu aplicativo IoT Central. Use IDs de dispositivo em letras minúsculas como o `CNAME` nos certificados de folha.

1. O OEM fornece a cada dispositivo uma ID de dispositivo, um certificado X.509 de folha gerado e o valor do **escopo de ID** do aplicativo.

1. Ao ligar um dispositivo, primeiro ele se conecta ao DPS para recuperar suas informações de registro do IoT Central.

    Inicialmente, o dispositivo tem um status de dispositivo **Não associado** na página **Dispositivos** e não está atribuído a um modelo de dispositivo. Na página **Dispositivos**, **Migre** o dispositivo para o modelo de dispositivo apropriado. O provisionamento do dispositivo foi concluído, agora o status do dispositivo está como **Provisionado**, e o dispositivo pode começar a enviar dados.

    Na página **Administração > Conexão do dispositivo**, a opção **Aprovar automaticamente** controla se você precisa aprovar o dispositivo manualmente antes que ele possa começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [Associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Conectividade de dispositivo baseada em registros individuais

Para clientes que conectam dispositivos com suas próprias credenciais de autenticação, use registros individuais. Um registro individual é uma entrada para um único dispositivo que tem permissão para se conectar. Os registros individuais podem usar certificados X.509 de folha ou tokens SAS (de um Trusted Platform Module físico ou virtual) como mecanismos de certificação. A ID do dispositivo (também conhecida como ID de registro) em um registro individual é alfanumérica, com letras minúsculas, e pode conter hifens. Para obter mais informações, consulte [Registro individual do DPS](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Ao criar um registro individual para um dispositivo, ele tem precedência sobre as opções de registro de grupo padrão em seu aplicativo IoT Central.

### <a name="create-individual-enrollments"></a>Criar registros individuais

O IoT Central tem suporte para os seguintes mecanismos de atestado de registros individuais:

- **Atestado de chave simétrica:** O atestado de chave simétrica é uma abordagem simples para autenticar o dispositivo com a instância do DPS. Para criar um registro individual que usa chaves simétricas, abra a página **Conexão de Dispositivo**, selecione **Registro individual** como o método de conexão e **Assinatura de acesso compartilhado (SAS)** como o mecanismo. Insira as chaves primária e secundária codificadas da base64 e salve as alterações. Use o **escopo da ID**, a **ID do dispositivo** e a chave primária ou secundária para conectar seu dispositivo.

    > [!TIP]
    > Para fazer testes, você pode usar o **OpenSSL** para gerar chaves base64 codificadas: `openssl rand -base64 64`

- **Certificados X.509:** Para criar um registro individual com certificados X.509, abra a página **Conexão de Dispositivo**, selecione **Registro individual** como o método de conexão e **Certificados (X.509)** como o mecanismo. Os certificados de dispositivo usados com uma entrada de registro individual têm como requisito que o emissor e o certificado CN estejam definidos para a ID do dispositivo.

    > [!TIP]
    > Para fazer testes, você pode usar [Ferramentas do SDK do dispositivo de provisionamento de dispositivos IoT do Azure para Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) para gerar um certificado autoassinado: `node create_test_cert.js device "mytestdevice"`

- **Atestado TPM (Trusted Platform Module):** Um [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) é um tipo de módulo de segurança de hardware. Usar um TPM é uma das maneiras mais seguras de se conectar um dispositivo. Este artigo pressupõe que você está usando um TPM discreto, de firmware ou integrado. Os TPMs emulados para software são adequados para fazer protótipos ou teste, mas não oferecem o mesmo nível de segurança dos TPMs discretos, de firmware ou integrados. Não use TPMs de software na produção. Para criar um registro individual que use um TPM, abra a página **Conexão de Dispositivo**, selecione **Registro individual** como o método de conexão e **TPM** como o mecanismo. Insira a chave de endosso de TPM e salve as informações de conexão do dispositivo.

## <a name="automatically-associate-with-a-device-template"></a>Associar a um modelo de dispositivo automaticamente

Um dos principais recursos do IoT Central é a capacidade de associar modelos de dispositivo automaticamente à conexão do dispositivo. Junto com as credenciais dos dispositivos, estes podem enviar um **CapabilityModelId** como parte da chamada de registro do dispositivo. O **CapabilityModelID** é um URN que identifica o modelo de funcionalidade implementado pelo dispositivo. O aplicativo IoT Central pode usar o **CapabilityModelID** para identificar o modelo de dispositivo a ser usado e, em seguida, associar automaticamente o dispositivo ao modelo de dispositivo. O processo de descoberta funciona da seguinte maneira:

1. Caso modelo de dispositivo já esteja publicado no aplicativo IoT Central, o dispositivo será associado ao modelo de dispositivo.
1. Para dispositivos IoT Plug and Play pré-certificados, caso o modelo de dispositivo ainda não esteja publicado no aplicativo IoT Central, o modelo de dispositivo será obtido no repositório público.

Os trechos de código a seguir mostram o formato do conteúdo adicional que o dispositivo deve enviar durante a chamada de registro do DPS para que a associação automática funcione.

Este é o formato para dispositivos que usam o SDK do dispositivo geralmente disponível:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Este é o formato para dispositivos que usam o SDK do dispositivo de visualização pública:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> A opção **Aprovar automaticamente** em **Administração > Conexão do dispositivo** deve estar habilitada para que os dispositivos se conectem automaticamente, descubram o modelo do dispositivo e comecem a enviar dados.

## <a name="device-status-values"></a>Valores de status do dispositivo

Quando um dispositivo real se conectar ao seu aplicativo IoT Central, o status do dispositivo será alterado da seguinte maneira:

1. O primeiro status do dispositivo é **Registrado**. Esse status significa que o dispositivo foi criado no IoT Central e tem uma ID de dispositivo. Um dispositivo é registrado quando:
    - Um novo dispositivo real é adicionado na página **Dispositivos**.
    - Um conjunto de dispositivos é adicionado usando a opção **Importação** na página **Dispositivos**.

1. O status do dispositivo muda para **Provisionado** quando o dispositivo conectado ao seu aplicativo IoT Central com credenciais válidas conclui a etapa de provisionamento. Nesta etapa, o dispositivo usa o DPS para, automaticamente, recuperar uma cadeia de conexão do Hub IoT usado pelo seu aplicativo IoT Central. Agora o dispositivo pode se conectar ao IoT Central e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo é bloqueado, ele não pode enviar dados para o aplicativo IoT Central. Os dispositivos bloqueados apresentam o status **Bloqueado**. Um operador deve redefinir o dispositivo antes de poder retomar o envio de dados. Quando um operador desbloquear um dispositivo, o status retornará ao seu valor anterior: **Registrado** ou **Provisionado**.

1. Se o status do dispositivo for **Aguardando aprovação**, isso significa que a opção **Aprovação automática** está desabilitada. Um operador deve aprovar explicitamente um dispositivo antes que ele comece a enviar dados. Dispositivos que não estejam registrados manualmente na página **Dispositivos**, mas que estejam conectados com credenciais válidas apresentarão **Aguardando aprovação** como status de dispositivo. Os operadores podem aprovar esses dispositivos na página **Dispositivos** usando o botão **Aprovar**.

1. Caso o status do dispositivo for seja **Não associado**, isso significa que o dispositivo que está se conectando ao IoT Central não tem um modelo de dispositivo associado. Essa situação geralmente ocorre nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando a opção **Importar** na página **Dispositivos** sem especificar o modelo do dispositivo.
    - Um dispositivo foi registrado manualmente na página **Dispositivos** sem especificar o modelo de dispositivo. Em seguida, o dispositivo foi conectado com as credenciais válidas.  

    O operador pode associar um dispositivo a um modelo de dispositivo da página **Dispositivos** usando o botão **Migrar**.

## <a name="best-practices"></a>Práticas recomendadas

Não persista nem armazene em cache a cadeia de conexão do dispositivo retornada pelo DPS quando você conectar o dispositivo pela primeira vez. Para reconectar um dispositivo, percorra o fluxo de registro de dispositivo padrão para obter a cadeia de conexão de dispositivo correta. Caso o dispositivo armazene a cadeia de conexão em cache, o software do dispositivo correrá o risco de ter uma cadeia de conexão obsoleta caso o IoT Central atualize o Hub IoT subjacente do Azure que ele usa.

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs de dispositivo do Azure fornecem a forma mais fácil de implementar o código do dispositivo. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do IoT do Azure para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do IoT do Azure para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do IoT do Azure para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do IoT do Azure para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens de dispositivo para nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivos gêmeos](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Telemetria | Mensagens do dispositivo para a nuvem |
| Propriedade | Propriedades relatadas do dispositivo gêmeo |
| Propriedade (gravável) | Propriedades desejadas e relatadas do dispositivo gêmeo |
| Comando | Métodos diretos |

### <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, será possível usar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para transferir o processamento para a borda do aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [Controlar o acesso ao Hub IoT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas são:

- Saiba como [Monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
- Saiba como [Definir um novo tipo de dispositivo IoT em seu aplicativo do Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [Dispositivos do Azure IoT Edge e do Azure IoT Central](./concepts-iot-edge.md)
