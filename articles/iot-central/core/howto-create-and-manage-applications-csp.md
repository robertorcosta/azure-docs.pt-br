---
title: Create and manage Azure IoT Central applications from the CSP portal | Microsoft Docs
description: Como um CSP, como criar um aplicativo do Azure IoT Central em nome de seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1588a27453dbc223e6aa1dd993905d274e5e1242
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480343"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Create and manage an Azure IoT Central application from the CSP portal

O programa Microsoft CSP (Provedor de Soluções na Nuvem) é um programa de Revendedor da Microsoft. Seu objetivo é fornecer a nossos parceiros de canal um programa único para revender todos os Serviços Comerciais Online da Microsoft. Saiba mais sobre o [Programa do Fornecedor de Soluções na Nuvem](https://partner.microsoft.com/cloud-solution-provider).

Como um CSP, você pode criar e gerenciar aplicativos do Microsoft Azure IoT Central em nome de seus clientes por meio da [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando os aplicativos do Azure IoT Central são criados em nome dos clientes por CSPs, assim como com outros serviços do Azure gerenciados por CSPs, os CSPs gerenciam a cobrança para os clientes. Um encargo para o Azure IoT Central aparecerão na sua fatura total no Microsoft Partner Center.

Para começar, entre em sua conta no Portal de parceiros da Microsoft e selecione um cliente para o qual você deseja criar um aplicativo do Azure IoT Central. Navigate to Service Management for the customer from the left nav.

![Microsoft Partner Center, exibição do cliente](media/howto-create-application-csp/image1.png)

O Azure IoT Central está listado como um serviço disponível para administrar. Select the Azure IoT Central link on the page to create new applications or manage existing applications for this customer.

![Azure IoT Central disponível para gerenciar](media/howto-create-application-csp/image2.png)

Você é direcionado para a página do Gerenciador de Aplicativos do Azure IoT Central. O Azure IoT Central sabe que você veio do Microsoft Partner Center e que você veio para gerenciar esse cliente em particular. Você verá isso confirmado no cabeçalho da página do Gerenciador de Aplicativos. A partir daqui, você pode navegar para um aplicativo existente que você criou anteriormente para esse cliente gerenciar ou criar um novo aplicativo para o cliente.

![Gerenciador de Criação para CSPs](media/howto-create-application-csp/image3.png)

To create an Azure IoT Central application, select **Build** in the left menu. Choose one of the industry templates, or choose **Custom app** to create an application from scratch. Isso carregará a página de Criação de Aplicativos. Você precisa preencher todos os campos dessa página e, em seguida, escolher **Criar**. Você encontrará mais informações sobre cada um desses campos abaixo.

![Página Criar Aplicativo para CSPs](media/howto-create-application-csp/image4.png)

![Página Criar Aplicativo para CSPs](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Plano de Pagamento

Você só pode criar aplicativos Pay-As-You-Go como CSP. Para demonstrar o Azure IoT Central para o seu cliente, você pode criar um aplicativo de avaliação separadamente. Saiba mais sobre os aplicativos Trial e Pay-As-You-Go na [página de preços da Microsoft IoT Central do Azure](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome do Aplicativo

O nome do aplicativo é exibido na página **Gerenciador de Aplicativos** e em cada aplicativo Azure IoT Central. É possível escolher qualquer nome para o aplicativo Azure IoT Central. Escolha um nome que faça sentido a você e às outras pessoas na organização.

## <a name="application-url"></a>URL do Aplicativo

A URL do aplicativo é o link para o aplicativo. Você pode salvar um indicador no navegador ou compartilhá-lo com outras pessoas.

Quando você insere o nome para seu aplicativo, a URL do aplicativo é gerado automaticamente. Se preferir, você pode escolher uma URL diferente para o aplicativo. Cada URL do Azure IoT Central deverá ser exclusiva dentro do Azure IoT Central. Se a URL escolhida já tiver sido utilizada, uma mensagem de erro será exibida.

## <a name="directory"></a>Diretório

Como o Azure IoT Central tem contexto fornecidas para gerenciar o cliente selecionado no Portal de parceiros da Microsoft, você pode ver apenas o locatário do Azure Active Directory para esse cliente no campo de diretório. 

Um locatário do Azure Active Directory contém identidades de usuário, credenciais e outras informações organizacionais. Várias assinaturas do Azure podem ser associadas a um único locatário do Azure Active Directory.

Para saber mais, consulte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Assinatura do Azure

Uma assinatura do Azure permite que você crie instâncias de serviços do Azure. O Azure IoT Central localiza automaticamente todas as Assinaturas do Azure do cliente às quais você tem acesso e as exibe em uma lista suspensa na página **Criar Aplicativo**. Escolha uma assinatura do Azure para criar um novo aplicativo Azure IoT Central.

Se você não tiver uma assinatura do Azure, poderá criar uma no Microsoft Partner Center. Após criar a assinatura do Azure, navegue de volta para a página **Criar Aplicativo**. A nova assinatura aparece na lista suspensa **Assinatura do Azure**.

Para saber mais, consulte [Assinaturas do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Região

Choose the region or [geography](https://azure.microsoft.com/global-infrastructure/geographies/) where you’d like to create your Azure IoT Central Application. Typically, you should choose the region that's closest physically to your devices to get optimal performance.

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **US** locations.

To learn more, see [Azure regions](https://azure.microsoft.com/global-infrastructure/regions/) and [Azure geographies](https://azure.microsoft.com/global-infrastructure/geographies/).

É possível visualizar as regiões nas quais o Azure IoT Central está disponível, na página [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central).

> [!Note]
> Após escolher uma região, não será mais possível mover posteriormente o aplicativo para uma região diferente.

## <a name="application-template"></a>Modelo de aplicativo

Você pode escolher um dos modelos de aplicativo disponíveis para o novo aplicativo Azure IoT Central. Um modelo de aplicativo pode conter itens predefinidos, como modelos de dispositivo e painéis, para ajudá-lo a começar.

| Modelo de aplicativo | Descrição |
| -------------------- | ----------- |
| Aplicativo personalizado   | Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos. |
| Exemplo Contoso       | Cria um aplicativo que inclui um modelo de dispositivo para um dispositivo conectado simples. Use esse modelo para começar a explorar o Azure IoT Central. |
| Exemplo Devkits       | Cria um aplicativo com modelos de dispositivos prontos para você conectar um dispositivo MXChip ou Raspberry Pi. Use esse modelo se você for um desenvolvedor de dispositivos que está experimentando o código em um desses dispositivos. |

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como criar um aplicativo Azure IoT Central como um CSP, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)
