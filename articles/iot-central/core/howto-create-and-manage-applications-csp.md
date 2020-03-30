---
title: Criar e gerenciar aplicativos Azure IoT Central a partir do portal CSP | Microsoft Docs
description: Como um CSP, como criar um aplicativo do Azure IoT Central em nome de seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 5d876d7db13ce73603f212ec11544cb2f680bf46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158173"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Criar e gerenciar um aplicativo Azure IoT Central a partir do portal CSP

O programa Microsoft CSP (Provedor de Soluções na Nuvem) é um programa de Revendedor da Microsoft. Seu objetivo é fornecer a nossos parceiros de canal um programa único para revender todos os Serviços Comerciais Online da Microsoft. Saiba mais sobre o [Programa do Fornecedor de Soluções na Nuvem](https://partner.microsoft.com/cloud-solution-provider).

Como um CSP, você pode criar e gerenciar aplicativos do Microsoft Azure IoT Central em nome de seus clientes por meio da [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando os aplicativos do Azure IoT Central são criados em nome dos clientes por CSPs, assim como com outros serviços do Azure gerenciados por CSPs, os CSPs gerenciam a cobrança para os clientes. Um encargo para o Azure IoT Central aparecerão na sua fatura total no Microsoft Partner Center.

Para começar, entre em sua conta no Portal de parceiros da Microsoft e selecione um cliente para o qual você deseja criar um aplicativo do Azure IoT Central. Navegue até o Gerenciamento de Serviços para o cliente a partir da navegação esquerda.

![Microsoft Partner Center, exibição do cliente](media/howto-create-and-manage-applications-csp/image1.png)

O Azure IoT Central está listado como um serviço disponível para administrar. Selecione o link Central de IoT do Azure na página para criar novos aplicativos ou gerenciar aplicativos existentes para esse cliente.

![Azure IoT Central disponível para gerenciar](media/howto-create-and-manage-applications-csp/image2.png)

Você é direcionado para a página do Gerenciador de Aplicativos do Azure IoT Central. O Azure IoT Central sabe que você veio do Microsoft Partner Center e que você veio para gerenciar esse cliente em particular. Você verá isso confirmado no cabeçalho da página do Gerenciador de Aplicativos. A partir daqui, você pode navegar para um aplicativo existente que você criou anteriormente para esse cliente gerenciar ou criar um novo aplicativo para o cliente.

![Gerenciador de Criação para CSPs](media/howto-create-and-manage-applications-csp/image3.png)

Para criar um aplicativo Azure IoT Central, **selecione Criar** no menu à esquerda. Escolha um dos modelos do setor ou escolha o **aplicativo Legado** para criar um aplicativo do zero. Isso carregará a página de Criação de Aplicativos. Você precisa preencher todos os campos dessa página e, em seguida, escolher **Criar**. Você encontrará mais informações sobre cada um desses campos abaixo.

![Página Criar Aplicativo para CSPs](media/howto-create-and-manage-applications-csp/image4.png)

![Página Criar Aplicativo para CSPs](media/howto-create-and-manage-applications-csp/image4-1.png)

![Criar página de inscrição para informações de faturamento de CSPs](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Plano de preços

Você só pode criar aplicativos que usam um plano de preços padrão como um CSP. Para mostrar o Azure IoT Central ao seu cliente, você pode criar um aplicativo que usa o plano de preços gratuitos separadamente. Saiba mais sobre os planos de preços Gratuito e Standard na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Você só pode criar aplicativos que usam um plano de preços padrão como um CSP. Para mostrar o Azure IoT Central ao seu cliente, você pode criar um aplicativo que usa o plano de preços gratuitos separadamente. Saiba mais sobre os planos de preços Gratuito e Standard na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome do aplicativo

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

Se você não tiver uma assinatura do Azure, você pode criar uma no Microsoft Partner Center. Após criar a assinatura do Azure, navegue de volta para a página **Criar Aplicativo**. A nova assinatura aparece na lista suspensa **Assinatura do Azure**.

Para saber mais, consulte [Assinaturas do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Location

**Localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar o aplicativo. Normalmente, você deve escolher a localização fisicamente mais próxima de seus dispositivos para obter um desempenho ideal. Atualmente, você pode criar uma aplicação IoT Central nas geografias **Austrália**, **Ásia-Pacífico**, **Europa**, **Estados Unidos,** **Reino Unido**e **Japão.** Depois de escolher uma localização, você não poderá mover o aplicativo posteriormente para outra localização.

## <a name="application-template"></a>Modelo de aplicativo

Escolha o modelo de aplicativo que deseja usar para sua aplicação.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar um aplicativo Azure IoT Central como um CSP, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Administrar seu aplicativo](howto-administer.md)
