---
title: Certificar seu dispositivo IoT Plug and Play Preview | Microsoft Docs
description: Este tutorial descreve como adicionar as informações do produto ao catálogo de dispositivos do Azure Certified para IoT, conectar seu dispositivo ao serviço de certificação IoT do Azure e, em seguida, executar os testes de certificação do IoT Plug and Play.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550155"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Tutorial: Certificar seu dispositivo IoT Plug and Play Preview

Para publicar um dispositivo IoT Plug and Play Preview no [catálogo de dispositivos do Azure Certified para IoT](https://aka.ms/iotdevcat), ele precisa passar por um conjunto de testes de certificação. Use o portal do [Azure Certified para IoT](https://aka.ms/ACFI) para enviar seu dispositivo para certificação. O [serviço de certificação IoT do Azure](https://aka.ms/azure-iot-aics) executa os testes de certificação.

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * Quais são os requisitos de certificação do IoT Plug and Play.
> * Como adicionar o nome e as informações do produto ao portal.
> * Como se conectar e descobrir interfaces IoT Plug and Play.
> * Como examinar as interfaces IoT Plug and Play e executar testes de certificação.
> * Como publicar o dispositivo IoT Plug and Play certificado no catálogo.

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você precisará:

* Uma conta do Microsoft Partner Center.
* A ID do Microsoft Partner Network.

Para obter mais informações, confira [Como fazer a integração no portal do Azure Certified para IoT](howto-onboard-portal.md).

## <a name="certification-requirements"></a>Requisitos de certificação

Para certificar seu dispositivo IoT Plug and Play, o dispositivo precisa atender aos seguintes requisitos:

* O código do dispositivo IoT Plug and Play precisa estar instalado no dispositivo.
* O código do dispositivo IoT Plug and Play é criado com o SDK de IoT do Azure.
* O código do dispositivo precisa dar suporte ao [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/about-iot-dps.md).
* O código do dispositivo precisa implementar a [Interface de Informações do Dispositivo](concepts-common-interfaces.md).
* O modelo de funcionalidade e o código do dispositivo funcionam com o IoT Central.

Todos os dispositivos atualmente no catálogo são considerados dispositivos IoT Plug and Play pré-certificados. Isso não garante a qualidade e a conformidade do produto final de componentes de software IoT Plug and Play, como SDK e a Linguagem de Definição de Gêmeo Digital.

Todos os dispositivos IoT Plug and Play pré-certificados precisam fazer uma nova certificação da disponibilidade geral do IoT Plug and Play com base na versão final dos requisitos de certificação e dos componentes de software fornecidos pela Microsoft.

## <a name="add-product-name"></a>Adicionar nome do produto

**Produto** é um nome amigável para um modelo de produto que pode ser comprado por um cliente. Para adicionar um produto:

1. Entre no portal do [Azure Certified para IoT](https://aka.ms/ACFI).
1. Acesse a página **Produtos** no portal no menu à esquerda e selecione **+Novo**.
1. Insira o nome de produto amigável e selecione **Criar**. O nome inserido aqui é diferente do nome exibido no catálogo do dispositivo.

## <a name="add-product-information"></a>Adicionar informações do produto

Depois que você criar o produto com êxito no portal, o produto será exibido na página **Produtos**. Para adicionar as informações do produto:

1. Selecione o link do produto criado localizado na página **Produto** na coluna de produtos. O estado deve ser rascunho.
1. Selecione o link **Editar** ao lado do título **Informações do produto**. Insira informações sobre seu produto na página de informações do produto e preencha todos os campos obrigatórios.
1. Clique em **Salvar**. O botão **Salvar** só é exibida quando você preenche todos os campos obrigatórios. Se os campos estiverem incompletos, o botão indicará **Salvar e concluir mais tarde**.
1. Examine o conteúdo inserido. Preencha todos os campos obrigatórios para publicar o dispositivo no catálogo de dispositivos. Você sempre pode voltar para fazer edições nas informações do produto na página de detalhes do produto clicando no link **Editar** ao lado do título **Informações do produto**.

## <a name="connect-and-discover-interfaces"></a>Conectar e descobrir interfaces

Para executar os testes de certificação, conecte o dispositivo ao [AICS](https://aka.ms/azure-iot-aics) (serviço de certificação IoT do Azure) disponível no portal.

Estas etapas são uma etapa única para a execução dos testes de certificação, não sendo necessário alterar o código do dispositivo do produto. Para começar, siga estas etapas para se conectar ao AICS:

1. Entre no portal usando sua conta do Partner Center.
1. Clique em **Conectar + testar** para iniciar o fluxo de certificação.
1. Escolha o [método de autenticação](../iot-dps/concepts-security.md#attestation-mechanism) para provisionar o dispositivo no AICS usando o [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/about-iot-dps.md).
   * Se estiver usando um [certificado X.509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), carregue o certificado X.509 gerado. É recomendável examinar o código de exemplo que mostra como usar certificados X.509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Se estiver usando uma [chave simétrica](../iot-dps/concepts-symmetric-key-attestation.md), copie e cole a chave simétrica no código do dispositivo.
   * Atualmente, não há suporte para o método de autenticação do TPM.
1. Copie e cole as IDs geradas a seguir no código do dispositivo.
   * [ID de registro](../iot-dps/use-hsm-with-sdk.md)
   * [ID do DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Ponto de extremidade DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Quando o código do dispositivo for criado e implantado no dispositivo, selecione **Conectar** para descobrir as interfaces IoT Plug and Play.
1. Quando a conexão com o AICS for bem-sucedida, selecione **Avançar** para examinar as interfaces IoT Plug and Play descobertas.

## <a name="run-tests-and-publish-the-device"></a>Executar testes e publicar o dispositivo

Na página de revisão, examine as interfaces IoT Plug and Play descobertas. Use essa página para verificar se os primitivos implementados na interface são exibidos corretamente. Verifique também a localização da interface.

1. Verifique se as entradas de conteúdo são inseridas nos campos obrigatórios. Esses campos incluem informações de conteúdo para o primitivo de comando da interface especificada.
1. Quando você inserir todas as informações necessárias, selecione **Avançar**.
1. Para executar os testes para as interfaces IoT Plug and Play implementadas, selecione **Executar testes**.
1. Todos os testes são executados automaticamente. Se algum teste falhar, selecione **Exibir logs** para exibir as mensagens de erro do AICS e a telemetria bruta enviada ao Hub IoT do Azure.
1. Para concluir os testes de certificação, selecione **Concluir**.
1. Publique o dispositivo IoT Plug and Play certificado no catálogo. Para adicionar o dispositivo certificado ao catálogo, selecione **Adicionar ao catálogo** na barra de ferramentas. Se a opção **Adicionar ao catálogo** estiver esmaecida, isso significará que as informações do produto estão incompletas ou que os testes falharam. 
1. Selecione o link "CERTIFICADO E NO CATÁLOGO" para exibir o dispositivo publicado no catálogo de dispositivos.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a certificação de dispositivos IoT Plug and Play, a próxima etapa sugerida é aprender mais sobre o gerenciamento de modelos de funcionalidades:

> [!div class="nextstepaction"]
> [Gerenciar modelos](./howto-manage-models.md)
