---
title: Cache do Azure para Redis com o link privado do Azure (versão prévia)
description: O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura ao cache do Azure para Redis da plataforma Azure link privado. Neste artigo, você aprenderá a criar um cache do Azure, uma rede virtual do Azure e um ponto de extremidade privado usando o portal do Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: e2c071ff9cf020f99e990e670cfb29cca3c1ebbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91838646"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Cache do Azure para Redis com o link privado do Azure (visualização pública)
Neste artigo, você aprenderá a criar uma rede virtual e um cache do Azure para a instância Redis com um ponto de extremidade privado usando o portal do Azure. Você também aprenderá a adicionar um ponto de extremidade privado a um cache do Azure existente para a instância do Redis.

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura ao cache do Azure para Redis da plataforma Azure link privado. 

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Para usar pontos de extremidade privados, seu cache do Azure para instância Redis precisa ter sido criado após 28 de julho de 2020.
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Criar um ponto de extremidade privado com um novo cache do Azure para instância Redis 

Nesta seção, você criará um novo cache do Azure para a instância Redis com um ponto de extremidade privado.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual 

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione criar um recurso.":::

2. Na página **novo** , selecione **rede** e, em seguida, selecione **rede virtual**.

3. Selecione **Adicionar** para criar uma rede virtual.

4. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura sob a qual criar essa rede virtual. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar sua rede virtual e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome** | Insira um nome de rede virtual. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens. | 
   | **Região** | Na lista suspensa e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão sua rede virtual. |

5. Selecione a guia **endereços IP** ou clique no botão **Avançar: endereços IP** na parte inferior da página.

6. Na guia **endereços IP** , especifique o **espaço de endereço IPv4** como um ou mais prefixos de endereço na notação CIDR (por exemplo, 192.168.1.0/24).

7. Em **nome da sub-rede**, clique em **padrão** para editar as propriedades da sub-rede.

8. No painel **Editar sub-rede** , especifique um **nome de sub-rede** , bem como o **intervalo de endereços de sub-rede**. O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Ele deve estar contido no espaço de endereço da rede virtual.

9. Selecione **Salvar**.

10. Selecione a guia **revisar + criar** ou clique no botão **revisar + criar** .

11. Verifique se todas as informações estão corretas e clique em **criar** para provisionar a rede virtual.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Criar um cache do Azure para a instância Redis com um ponto de extremidade privado
Para criar uma instância de cache, siga estas etapas.

1. Volte para a home page do portal do Azure ou abra o menu da barra lateral e, em seguida, selecione **criar um recurso**. 
   
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecione criar um recurso.":::
   
1. Na página **Novo Cache Redis**, defina as configurações para o novo cache.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Clique na lista suspensa e selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de preços** | Clique na lista suspensa e selecione um [Tipo de preço](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |

1. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

1. Na guia **rede** , selecione **ponto de extremidade privado** para o método de conectividade.

1. Clique no botão **Adicionar** para criar seu ponto de extremidade privado.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Selecione criar um recurso.":::

1. Na página **criar um ponto de extremidade privado** , defina as configurações para seu ponto de extremidade privado com a rede virtual e a sub-rede que você criou na última seção e selecione **OK**. 

1. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

1. Na guia **Avançado** de uma instância de cache Básico ou Standard, selecione a alternância Habilitar se desejar habilitar uma porta não TLS.

1. Na guia **Avançado** de uma instância de cache Premium, defina as configurações da porta não TLS, do clustering e da persistência de dados.


1. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

1. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso. 

1. Selecione  **Analisar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

1. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página  **Visão Geral**  do Cache do Azure para Redis. Quando o  **Status**  for mostrado como  **Em execução**, o cache estará pronto para uso. 
    

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Criar um ponto de extremidade privado com um cache do Azure existente para a instância do Redis 

Nesta seção, você adicionará um ponto de extremidade privado a um cache do Azure existente para a instância do Redis. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual 
Para criar uma rede virtual, siga estas etapas.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.

2. Na página **novo** , selecione **rede** e, em seguida, selecione **rede virtual**.

3. Selecione **Adicionar** para criar uma rede virtual.

4. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura sob a qual criar essa rede virtual. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar sua rede virtual e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome** | Insira um nome de rede virtual. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens. | 
   | **Região** | Na lista suspensa e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão sua rede virtual. |

5. Selecione a guia **endereços IP** ou clique no botão **Avançar: endereços IP** na parte inferior da página.

6. Na guia **endereços IP** , especifique o **espaço de endereço IPv4** como um ou mais prefixos de endereço na notação CIDR (por exemplo, 192.168.1.0/24).

7. Em **nome da sub-rede**, clique em **padrão** para editar as propriedades da sub-rede.

8. No painel **Editar sub-rede** , especifique um **nome de sub-rede** , bem como o **intervalo de endereços de sub-rede**. O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Ele deve estar contido no espaço de endereço da rede virtual.

9. Selecione **Salvar**.

10. Selecione a guia **revisar + criar** ou clique no botão **revisar + criar** .

11. Verifique se todas as informações estão corretas e clique em **criar** para provisionar a rede virtual.

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado 

Para criar um ponto de extremidade privado, siga estas etapas.

1. Na portal do Azure, procure o **cache do Azure para Redis** e pressione Enter ou selecione-o nas sugestões de pesquisa.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Selecione criar um recurso.":::

2. Selecione a instância de cache à qual você deseja adicionar um ponto de extremidade privado.

3. No lado esquerdo da tela, selecione (versão **prévia) ponto de extremidade privado**.

4. Clique no botão **ponto de extremidade privado** para criar seu ponto de extremidade privado.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Selecione criar um recurso.":::

5. Na **página criar um ponto de extremidade privado**, defina as configurações para seu ponto de extremidade privado.

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura sob a qual criar esse ponto de extremidade privado. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar seu ponto de extremidade privado e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome** | Insira um nome de ponto de extremidade privado. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens. | 
   | **Região** | Na lista suspensa e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão seu ponto de extremidade privado. |

6. Clique no botão **Avançar: recurso** na parte inferior da página.

7. Na guia **recurso** , selecione sua assinatura, escolha o tipo de recurso como `Microsoft.Cache/Redis` e, em seguida, selecione o cache ao qual você deseja conectar o ponto de extremidade privado.

8. Clique no botão **próximo: configuração** na parte inferior da página.

9. Na guia **configuração** , selecione a rede virtual e a sub-rede que você criou na seção anterior.

10. Clique no botão **próximo: marcas** na parte inferior da página.

11. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso.

12. Selecione  **Analisar + criar**. Você é levado para a guia **revisar + criar**,   na qual o Azure valida sua configuração.

13. Depois que a mensagem de **validação verde aprovada** for exibida, selecione **criar**.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o link privado do Azure, consulte a [documentação do link privado do Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). 

