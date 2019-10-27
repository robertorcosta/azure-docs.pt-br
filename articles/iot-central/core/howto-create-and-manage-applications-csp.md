---
title: Criar e gerenciar aplicativos de IoT Central do Azure no portal do CSP | Microsoft Docs
description: Como um CSP, como criar um aplicativo do Azure IoT Central em nome de seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 2acac429dc8f0d9981e4c952702edd24b598881b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950893"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Criar e gerenciar um aplicativo de IoT Central do Azure no portal do CSP

O programa Microsoft CSP (Provedor de Soluções na Nuvem) é um programa de Revendedor da Microsoft. Seu objetivo é fornecer a nossos parceiros de canal um programa único para revender todos os Serviços Comerciais Online da Microsoft. Saiba mais sobre o [Programa do Fornecedor de Soluções na Nuvem](https://partner.microsoft.com/cloud-solution-provider).

Como um CSP, você pode criar e gerenciar aplicativos do Microsoft Azure IoT Central em nome de seus clientes por meio da [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando os aplicativos do Azure IoT Central são criados em nome dos clientes por CSPs, assim como com outros serviços do Azure gerenciados por CSPs, os CSPs gerenciam a cobrança para os clientes. Um encargo para o Azure IoT Central aparecerão na sua fatura total no Microsoft Partner Center.

Para começar, entre em sua conta no Portal de parceiros da Microsoft e selecione um cliente para o qual você deseja criar um aplicativo do Azure IoT Central. Navegue até o Gerenciamento de serviço para o cliente da barra de navegação à esquerda.

![Microsoft Partner Center, exibição do cliente](media/howto-create-application-csp/image1.png)

O Azure IoT Central está listado como um serviço disponível para administrar. Selecione o link IoT Central do Azure na página para criar novos aplicativos ou gerenciar aplicativos existentes para esse cliente.

![Azure IoT Central disponível para gerenciar](media/howto-create-application-csp/image2.png)

Você é direcionado para a página do Gerenciador de Aplicativos do Azure IoT Central. O Azure IoT Central sabe que você veio do Microsoft Partner Center e que você veio para gerenciar esse cliente em particular. Você verá isso confirmado no cabeçalho da página do Gerenciador de Aplicativos. A partir daqui, você pode navegar para um aplicativo existente que você criou anteriormente para esse cliente gerenciar ou criar um novo aplicativo para o cliente.

![Gerenciador de Criação para CSPs](media/howto-create-application-csp/image3.png)

Para criar um aplicativo de IoT Central do Azure, selecione o bloco **novo aplicativo** . Isso carregará a página de Criação de Aplicativos. Você precisa preencher todos os campos dessa página e, em seguida, escolher **Criar**. Você encontrará mais informações sobre cada um desses campos abaixo.

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

Escolha a região ou [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo de IOT central do Azure. Normalmente, você deve escolher a região mais próxima fisicamente em seus dispositivos para obter o desempenho ideal.

> [!NOTE]
> No momento, o modelo de **aplicativo de visualização** só está disponível nas regiões **Europa setentrional** e **EUA Central** .

Para saber mais, confira [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) e [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

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