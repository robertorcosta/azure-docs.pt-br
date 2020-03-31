---
title: Configure nomes De DNS com gerenciador de tráfego
description: Saiba como configurar um domínio personalizado para um aplicativo azure App Service que se integra ao Traffic Manager para balanceamento de carga.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944102"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Configure um nome de domínio personalizado no Azure App Service com integração do Traffic Manager

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name.md).

Quando você usa [o Azure Traffic Manager](/azure/traffic-manager/) para carregar o tráfego de equilíbrio para o [Azure App Service,](overview.md)o aplicativo App Service pode ser acessado usando ** \<o-manager de tráfego>.trafficmanager.net**. Você pode atribuir um nome de\.domínio personalizado, como www contoso.com, com seu aplicativo App Service, a fim de fornecer um nome de domínio mais reconhecível para seus usuários.

Este artigo mostra como configurar um nome de domínio personalizado com um aplicativo app service integrado ao [Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Somente os registros [CNAME](https://en.wikipedia.org/wiki/CNAME_record) são suportados quando você configura um nome de domínio usando o ponto final do Gerenciador de tráfego. Como os registros A não são suportados, um mapeamento de domínio raiz, como contoso.com também não é suportado.
> 

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo integrado ao Azure Traffic Manager, o plano de Serviço de [Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo da Web deve estar no nível **Padrão** ou superior. Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicativos.**

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Na navegação à esquerda da página do aplicativo, selecione **Dimensionar (plano de serviço de aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo está no nível **Padrão** ou acima (qualquer nível na categoria **Produção** ou **Isolado).** Se sim, feche a página **'Dimensionar'** e pule para [Criar o mapeamento CNAME](#create-the-cname-mapping).

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

Se você precisar escalar seu aplicativo, selecione qualquer um dos níveis de preços na categoria **Produção.** Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

## <a name="create-traffic-manager-endpoint"></a>Criar ponto final do Gerenciador de Tráfego

Seguindo as etapas em [Adicionar ou Excluir Pontos Finais,](../traffic-manager/traffic-manager-endpoints.md)adicione seu aplicativo App Service como um ponto final no perfil do Gerenciador de Tráfego.

Uma vez que seu aplicativo App Service está em um nível de preço suportado, ele aparece na lista de alvos disponíveis do App Service quando você adiciona o ponto final. Se o seu aplicativo não estiver listado, [verifique a camada de preços do seu aplicativo](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Crie o mapeamento CNAME
> [!NOTE]
> Para configurar um [domínio do App Service que você comprou,](manage-custom-dns-buy-domain.md)pule esta seção e vá para Ativar domínio [personalizado](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Embora as especificidades de cada provedor de domínio variem, você mapeia desde o nome *de* domínio personalizado (como **contoso.com)** *até* o nome de domínio do Traffic Manager **(contoso.trafficmanager.net)** integrado ao seu aplicativo.

> [!NOTE]
> Se um registro já está em uso e você precisa associar preventivamente seus aplicativos a ele, pode criar um registro CNAME adicional. Por exemplo, para vincular preventivamente **o www\.contoso.com** ao seu aplicativo, crie um registro CNAME de **awverify.www** a **contoso.trafficmanager.net**. Em seguida, você\.pode adicionar "www contoso.com" ao seu aplicativo sem a necessidade de alterar o registro "www" CNAME. Para obter mais informações, consulte [Migrar um nome DNS ativo para Azure App Service](manage-custom-dns-migrate-domain.md).

Depois de terminar a adição ou a modificação de registros DNS no provedor de domínios, salve as alterações.

## <a name="enable-custom-domain"></a>Habilitar domínio personalizado
Depois que os registros do seu nome de domínio forem propagados, use o navegador para verificar se seu nome de domínio personalizado se resolve para o aplicativo App Service.

> [!NOTE]
> Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. Você pode usar um <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> serviço para verificar se o CNAME está disponível.
> 
> 

1. Uma vez que a resolução de domínio seja bem sucedida, volte para a página do aplicativo no [Portal Azure](https://portal.azure.com)
2. Na navegação à esquerda, selecione **Domínios personalizados** > **Adicionar nome de host**.
4. Digite o nome de domínio personalizado que você mapeou anteriormente e **selecione Validar**.
5. Certifique-se de que **Tipo de registro do nome do host** esteja definido como **CNAME (www\.exemplo.com ou qualquer subdomínio)**.

6. Uma vez que o aplicativo App Service agora está integrado com um ponto final do Gerenciador de Tráfego, você deve ver o nome de domínio do Gerenciador de tráfego na **configuração CNAME**. Selecione-o e clique **em Adicionar domínio personalizado**.

    ![Adicionar nome DNS para o aplicativo](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
