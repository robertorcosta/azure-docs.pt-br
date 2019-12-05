---
title: 'Tutorial: Habilitar o domínio personalizado e SSL para um site estático no Azure'
description: Saiba como configurar um domínio personalizado para hospedagem de site estático.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327506"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Tutorial: Habilitar o domínio personalizado e SSL para um site estático no Azure

Este tutorial é a parte dois de uma série. Nele, você aprenderá a habilitar um ponto de extremidade de domínio personalizado com SSL para seu site estático. 

O tutorial mostra como usar a [CDN do Azure](../../cdn/cdn-overview.md) para configurar o ponto de extremidade de domínio personalizado para o site estático. Com a CDN do Azure, é possível provisionar certificados SSL personalizados, usar um domínio personalizado e configurar regras de reescrita personalizadas ao mesmo tempo. Configurar a CDN do Azure resulta em encargos adicionais, mas oferece baixas latências consistentes a seu site em qualquer lugar do mundo. A CDN do Azure também fornece criptografia SSL com o próprio certificado. Para saber mais sobre os preços da CDN do Azure, confira [Azure CDN pricing](https://azure.microsoft.com/pricing/details/cdn/) (Preços da CDN do Azure).

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Criar um ponto de extremidade da CDN no ponto de extremidade de site estático
> * Habilitar domínio personalizado e SSL

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, conclua a parte um, [Tutorial: Hospedar um site estático no Armazenamento de Blobs](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/) para começar.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Criar um ponto de extremidade da CDN no ponto de extremidade de site estático

1. Localize sua conta de armazenamento no portal do Azure e exiba a visão geral da conta.
1. Selecione **CDN do Azure** no menu **Serviço Blob** para configurar a CDN do Azure.
1. Na seção **Perfil CDN**, especifique um perfil CDN novo ou existente. Para saber mais, confira [Início Rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](../../cdn/cdn-create-new-endpoint.md).
1. Especifique um tipo de preço para o ponto de extremidade CDN. Este tutorial usa o tipo de preço **Akamai Standard**, pois ele é propagado rapidamente, normalmente em poucos minutos. Outros tipos de preços podem levar mais tempo para serem propagados, mas também podem oferecer outras vantagens. Para obter mais informações, confira [Comparar os recursos do produto CDN do Azure](../../cdn/cdn-features.md).
1. No campo **Nome do ponto de extremidade CDN**, especifique um nome para o ponto de extremidade CDN. O ponto de extremidade CDN precisa ser exclusivo no Azure.
1. Especifique o ponto de extremidade do site estático no campo **Nome do host de origem**. Para encontrar o ponto de extremidade do site estático, navegue até as configurações de **Site estático** de sua conta de armazenamento. Copie o ponto de extremidade primário e cole-o na configuração da CDN, removendo o identificador de protocolo (*por exemplo*, HTTPS).

    A seguinte imagem mostra uma configuração de ponto de extremidade de exemplo:

    ![Captura de tela mostrando uma configuração de ponto de extremidade CDN de exemplo](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Crie o ponto de extremidade CDN e aguarde para que ele seja propagado.
1. Para verificar se o ponto de extremidade CDN está configurado corretamente, clique no ponto de extremidade para navegar até as configurações. Na visão geral da CDN de sua conta de armazenamento, localize o nome do host do ponto de extremidade e navegue até o ponto de extremidade, conforme mostrado na imagem a seguir. O formato do ponto de extremidade CDN será semelhante a `https://staticwebsitesamples.azureedge.net`.

    ![Captura de tela mostrando uma visão geral do ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Após a conclusão da propagação do ponto de extremidade CDN, navegar até o ponto de extremidade CDN exibirá o conteúdo do arquivo index.html que você carregou anteriormente no site estático.

1. Para examinar as configurações de origem do ponto de extremidade CDN, navegue até **Origem** na seção **Configurações** do ponto de extremidade CDN. Você verá que o campo **Tipo de origem** é definido como *Origem Personalizada* e que o campo **Nome do host de origem** exibe o ponto de extremidade do site estático.

    ![Captura de tela mostrando as Configurações de origem para o ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Habilitar domínio personalizado e SSL

1. Criar um registro CNAME com o provedor de nomes de domínio para redirecionar o domínio personalizado para o ponto de extremidade da CDN. O registro CNAME para o subdomínio *www* deve ser semelhante ao seguinte:

    ![Especificar registro CNAME para subdomínio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. No portal do Azure, exiba as configurações de seu ponto de extremidade CDN. Navegue até **Domínios personalizados** em **Configurações** para configurar o domínio personalizado e o certificado SSL.
1. Selecione **Adicionar domínio personalizado**, insira o nome de domínio e clique em **Adicionar**.
1. Selecione o novo mapeamento de domínio personalizado para provisionar um certificado SSL.
1. Defina **HTTPS de Domínio Personalizado** como **ATIVADO** e, em seguida, clique em **Salvar**. Podem ser necessárias várias horas para configurar o domínio personalizado. O portal exibe o andamento, conforme mostrado na imagem a seguir.

    ![Captura de tela mostrando o progresso da configuração de domínio personalizado](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Teste o mapeamento do site estático para o domínio personalizado acessando a URL do domínio personalizado.

Para obter mais informações sobre como habilitar o HTTPS em domínios personalizados, confira [Tutorial: Configurar o HTTPS em um domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Próximas etapas

Na segunda parte deste tutorial, você aprendeu como configurar um domínio personalizado com SSL na CDN do Azure para o site estático.

Para obter mais informações sobre como configurar e usar a CDN do Azure, confira [O que é a CDN do Azure?](../../cdn/cdn-overview.md).