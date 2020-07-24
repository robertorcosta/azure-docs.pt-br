---
title: Cache do Azure para Redis com o link privado do Azure (versão prévia)
description: O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura ao cache do Azure para Redis da plataforma Azure link privado. Neste artigo, você aprenderá a criar um cache do Azure, uma rede virtual do Azure e um ponto de extremidade privado usando o portal do Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 59d5c6c4a9c3af70eb08e9d5a05d516f4b8b8bdd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096121"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Cache do Azure para Redis com o link privado do Azure (versão prévia)
O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura ao cache do Azure para Redis da plataforma Azure link privado. 

Neste artigo, você aprenderá a criar um cache do Azure, uma rede virtual do Azure e um ponto de extremidade privado usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
  > Este recurso está atualmente em visualização- [Fale conosco](mailto:azurecache@microsoft.com) se você estiver interessado.
  >


## <a name="create-a-cache"></a>Criar um cache
1. Para criar um cache, entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione criar um recurso.":::
   
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecione cache do Azure para Redis.":::
   
1. Na página **Novo Cache Redis**, defina as configurações para o novo cache.
   
   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* da instância do cache será * \<DNS name> . Redis.cache.Windows.net*. | 
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Clique na lista suspensa e selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de preços** | Clique na lista suspensa e selecione um [Tipo de preço](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |
   
1. Selecione **Criar**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Crie o cache do Azure para Redis.":::
   
   A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Cache do Azure para Redis criado.":::

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. Selecione **Criar um recurso**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione criar um recurso.":::

2. Na página **novo** , selecione **rede** e, em seguida, selecione **rede virtual**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Crie uma rede virtual.":::

3. Em **criar rede virtual**, insira ou selecione essas informações na guia **noções básicas** :

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Clique na lista suspensa e selecione sua assinatura.                                  |
    | Grupo de Recursos   | Menu suspenso e selecione um grupo de recursos. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Digita**\<virtual-network-name>**                                    |
    | Região           | Não**\<region-name>** |

4. Selecione a guia **endereços IP** ou selecione o botão **Avançar: endereços IP** na parte inferior da página.

5. Na guia **endereços IP** , insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Digita**\<IPv4-address-space>** |

6. Em **nome da sub-rede**, selecione a palavra **padrão**.

7. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Digita**\<subnet-name>** |
    | Intervalo de endereços da sub-rede | Digita**\<subnet-address-range>**

8. Clique em **Salvar**.

9. Selecione a guia **revisar + criar** ou selecione o botão **revisar + criar** .

10. Selecione **Criar**.


## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado 

Nesta seção, você criará um ponto de extremidade privado e o conectará ao cache criado anteriormente.

1. Pesquise o **link privado** e pressione Enter ou selecione-o nas sugestões de pesquisa.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Procure um link privado.":::

2. No lado esquerdo da tela, selecione pontos de **extremidade privados**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Selecione link privado.":::

3. Selecione o botão **+ Adicionar** para criar seu ponto de extremidade privado. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Adicione um link privado.":::

4. Na **página criar um ponto de extremidade privado**, defina as configurações para seu ponto de extremidade privado.

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Clique na lista suspensa e selecione sua assinatura. |
    | Grupo de recursos | Menu suspenso e selecione um grupo de recursos. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome |Insira um nome para seu ponto de extremidade particular.  |
    | Região |Clique na lista suspensa e selecione uma localização. |
    |||

5. Selecione o botão **Avançar: recurso** na parte inferior da página.

6. Na guia **recurso** , selecione sua assinatura, escolha o tipo de recurso como Microsoft. cache/Redis e, em seguida, selecione o cache que você criou na seção anterior.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Recursos para o link privado.":::

7. Selecione o botão **próximo: configuração** na parte inferior da página.

8. Na guia **configuração** , selecione a rede virtual e a sub-rede que você criou na seção anterior.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Configuração do link privado.":::

9. Selecione o botão **próximo: marcas** na parte inferior da página.

10. Na guia **marcas** , digite o nome e o valor se desejar categorizar o recurso. Essa etapa é opcional.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Marcas para o link privado.":::

11. Selecione  **Analisar + criar**. Você é levado para a guia **revisar + criar**,   na qual o Azure valida sua configuração.

12. Quando a mensagem de **validação verde aprovada** for exibida, selecione **criar**.
