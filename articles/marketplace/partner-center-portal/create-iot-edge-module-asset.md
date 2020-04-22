---
title: Prepare seus ativos técnicos do módulo IoT Edge - Azure Marketplace
description: Conheça os requisitos técnicos e de configuração que os ativos técnicos do módulo Edge de Internet das Coisas (IoT) devem atender antes de publicá-los no Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730707"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Prepare seus recursos técnicos do módulo IoT Edge

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas do seu módulo IoT Edge do Portal de Parceiros em Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [Prepare seus ativos técnicos do módulo IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) para o Cloud Partner Portal para gerenciar suas ofertas.

Este artigo descreve os requisitos que os ativos técnicos do módulo Edge da Internet das Coisas (IoT) devem atender antes de serem publicados no Azure Marketplace.

## <a name="get-started"></a>Introdução

Um módulo IoT Edge é um contêiner compatível com Docker que é executado em um dispositivo IoT Edge.

- Para saber mais sobre os módulos do IoT Edge, confira [Entender os módulos do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Para começar com o desenvolvimento do módulo IoT Edge, consulte [Desenvolver seus próprios módulos ioT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Requisitos técnicos

Seu módulo IoT Edge deve atender aos seguintes requisitos técnicos para ser certificado e publicado no Azure Marketplace.

### <a name="platform-support"></a>Suporte a plataforma

Seu módulo IoT Edge deve suportar uma das seguintes opções de plataforma:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas de nível 1 suportadas pelo IoT Edge

Seu módulo deve suportar todas as plataformas de Nível 1 suportadas pelo IoT Edge (conforme registrado no [suporte ao Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Recomendamos essa opção porque fornece uma melhor experiência do cliente. Serão apresentados módulos que atendam a esse critério. Um módulo usando esta opção de plataforma deve:

- Forneça uma tag mais recente e uma tag de versão (por exemplo, 1.0.1) que são tags manifesto construídas com a [ferramenta Manifesto GitHub](https://github.com/estesp/manifest-tool).

- Use a guia de listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link na seção **Links Úteis** ao [catálogo de dispositivos Certificados azure IoT Edge](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto das plataformas de nível 1 com suporte do IoT Edge

Seu módulo deve suportar um subconjunto (pelo menos um) de plataformas de Nível 1 suportadas pelo IoT Edge (conforme registrado no [suporte ao Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Um módulo usando esta opção de plataforma deve:

- Forneça uma tag mais recente e uma tag de versão (por exemplo, 1.0.1) que são tags manifesto construídas com a [ferramenta de manifesto](https://github.com/estesp/manifest-tool) do GitHub se mais de uma plataforma for suportada. Tags de manifesto são opcionais somente quando uma plataforma é suportada
- Use a guia de listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link na seção **Links Úteis** a pelo menos um dispositivo IoT Edge do catálogo de [dispositivos Certificados Azure IoT Edge](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Esta é uma imagem da seção Listagem de ofertas dentro do Partner Center":::

### <a name="device-dimensions"></a>Device dimensions

As dimensões do módulo IoT Edge (como CPU, RAM, armazenamento e GPU) em dispositivos IoT Edge direcionados devem atender aos seguintes requisitos:

- O módulo deve funcionar com pelo menos um dispositivo IoT Edge do catálogo de [dispositivos Certificados Azure IoT Edge](https://catalog.azureiotsolutions.com/).

- Os requisitos mínimos de hardware devem ser documentados como o último parágrafo na descrição da oferta (na guia de listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace)). Opcionalmente, você também pode listar os requisitos de hardware recomendados se eles forem significativamente diferentes. Por exemplo, adicione a seguinte seção no final da descrição da sua oferta:

Copie este texto HTML ou use as funções de texto rico correspondentes na janela de edição.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configuração

Seu módulo deve incluir configurações padrão para tornar a implantação em um dispositivo IoT Edge o mais simples possível. Essas informações podem ser fornecidas na página **de configuração técnica** do plano no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). O contêiner também pode incluir o IoT Edge Module SDK para permitir a comunicação com o Hub de borda e o IoT Hub.

#### <a name="default-configuration"></a>Configuração padrão

Os módulos IoT Edge devem ser capazes de iniciar com as configurações padrão fornecidas na página **de configuração técnica** para o plano no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). As seguintes configurações padrão estão disponíveis:

- Padrão **rotas**
- Propriedades **desejadas do módulo padrão twin**
- Padrão **variáveis de ambiente**
- Opções de criação de **contêiner padrão**

Em um cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), adicione um parâmetro como o valor padrão. Este valor é maiúsculo e incluído entre parênteses. Para este exemplo, você configuraria a seguinte variável de ambiente padrão:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as configurações de um módulo IoT Edge devem estar claramente documentadas. Por exemplo, você deve documentar como usar suas rotas, propriedades desejadas duplas, variáveis de ambiente, createOptions e assim por diante. Você deve fornecer um link para sua documentação ou torná-lo parte de sua oferta ou descrição do plano. Você pode fornecer essas informações na página de listagem de **ofertas** e **planos** no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Marcas e controle de versão

Os clientes devem ser capazes de implantar facilmente um módulo e obter automaticamente atualizações do mercado (em um cenário de desenvolvedor). Eles também devem ser capazes de usar e congelar uma versão exata que testaram (em um cenário de produção).

Para atender a essas expectativas dos clientes e ser publicado no mercado, os módulos IoT Edge devem atender aos seguintes requisitos

- Inclua uma tag mais recente manifesto que aponta para a versão mais recente em todas as plataformas suportadas.
- Faça tags de versão no formulário X.Y.Z, onde X, Y e Z são inteiros.
- Inclua uma tag "versão", como 1.0.1, que aponta para uma versão específica em todas as plataformas suportadas.
- Não atualize tags de "versão", como 1.0.1, porque elas não devem ser alteradas.

> [!NOTE]
> Opcionalmente, a versão pode incluir tags "versão rolando", como 2.0 e 1.0. Isso suporta a manutenção de várias versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Os módulos que usam o Módulo IoT SDK devem definir o identificador exclusivo do módulo para PublisherId.OfferId.SkuId para fins de telemetria. Um identificador exclusivo ajuda o Azure Marketplace a identificar o número de instâncias de módulo que estão sendo executados.

Use um dos seguintes métodos dos SDKs do Módulo IoT para definir o ProductInfo para este identificador:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Para módulos que não usam o Módulo IoT SDK, insights menos precisos estão disponíveis através do Partner Center, como o número de downloads.

### <a name="security"></a>Segurança

Os módulos IoT Edge devem evitar [módulos privilegiados](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Em vez disso, peça o acesso menos privilegiado ao host possível.

### <a name="module-iot-sdk"></a>SDK de IoT de módulo

Incluir o SDK do Módulo IoT não é um pré-requisito para a certificação. No entanto, incluir o SDK do Módulo do IoT pode fornecer uma melhor experiência ao usuário. Por exemplo, para dar suporte ao roteamento ou ao enviar mensagens para a nuvem.

O Módulo IoT SDK é necessário para obter dados de telemetria sobre o número de instâncias do módulo que estão sendo executados.

## <a name="recertification-process"></a>Processo recertificação

Os parceiros são notificados sempre que houver uma mudança de ruptura que afete seus módulos, tais como:

- Matriz de suporte ao sistema operacional/arco de nível 1 suportada por IoT Edge
- Módulo do IoT do SDK
- runtime do IoT Edge
- Diretrizes de certificação do módulo IoT Edge

Os parceiros devem atualizar e recertificar suas ofertas republicando-as no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

Sua oferta também será recertificada se você atualizá-la, como adicionar novas tags de imagem.

## <a name="host-module-in-azure-container-registry"></a>Módulo host no Registro de Contêineres do Azure

Para carregar seu módulo IoT Edge no Azure Marketplace, primeiro você precisa hospedá-lo em um [ACR (ACR) de contêineres do Azure.](https://azure.microsoft.com/services/container-registry/) O módulo deve incluir todas as tags que você deseja publicar, incluindo as tags de imagem que são referenciadas por uma tag manifesto. Para obter mais informações, consulte o tutorial [Criar um registro de contêiner Do Zure e empurrar uma imagem de contêiner](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma oferta de módulo do IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)