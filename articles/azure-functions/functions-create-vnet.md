---
title: Integre funções do Azure com uma rede virtual Azure
description: Um tutorial passo-a-passo que mostra como conectar uma função a uma rede virtual do Azure
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433223"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: Integrar o Functions a uma rede virtual do Azure

Este tutorial mostra como usar funções do Azure para se conectar a recursos em uma rede virtual Do Zure. você criará uma função que tenha acesso tanto à internet quanto a uma VM executando o WordPress em rede virtual.

> [!div class="checklist"]
> * Crie um aplicativo de função no plano Premium
> * Implantar um site wordpress para VM em uma rede virtual
> * Conecte o aplicativo de função à rede virtual
> * Crie um proxy de função para acessar os recursos do WordPress
> * Solicite um arquivo WordPress de dentro da rede virtual

## <a name="topology"></a>Topologia

O diagrama a seguir mostra a arquitetura da solução que você cria:

 ![UI para integração de rede virtual](./media/functions-create-vnet/topology.png)

As funções em execução no plano Premium têm os mesmos recursos de hospedagem que os aplicativos web no Azure App Service, que inclui o recurso De integração VNet. Para saber mais sobre a Integração VNet, incluindo solução de problemas e configuração avançada, consulte [Integrar seu aplicativo com uma rede virtual Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que você entenda a endereçamento ip e a sub-rede. Você pode começar com [este artigo que abrange os fundamentos de endereçamento e sub-rede.](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics) Muitos outros artigos e vídeos estão disponíveis online.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Crie um aplicativo de função em um plano Premium

Primeiro, você cria um aplicativo de função no [plano Premium]. Esse plano fornece escala sem servidor enquanto oferece suporte à integração de rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Você pode fixar o aplicativo de função no painel selecionando o ícone do pino no canto superior direito. Fixar facilita o retorno a este aplicativo de função depois de criar sua VM.

## <a name="create-a-vm-inside-a-virtual-network"></a>Crie uma VM dentro de uma rede virtual

Em seguida, crie uma VM pré-configurada que execute o WordPress dentro de uma rede virtual[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Um WordPress VM é usado devido ao seu baixo custo e conveniência. Esse mesmo cenário funciona com qualquer recurso em uma rede virtual, como APIs REST, Ambientes de Serviços de Aplicativos e outros serviços do Azure. 

1. No portal, escolha **+ Crie um recurso** no painel de `WordPress LEMP7 Max Performance`navegação à esquerda, no tipo de campo de busca e pressione Enter.

1. Escolha **o Wordpress LEMP Max Performance** nos resultados da pesquisa. Selecione um plano de software do **Wordpress LEMP Max Performance for CentOS** como plano **de software** e selecione **Criar**.

1. Na guia **Básico,** use as configurações de VM conforme especificado na tabela abaixo da imagem:

    ![Guia básico para criar uma VM](./media/functions-create-vnet/create-vm-1.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura a qual seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha `myResourceGroup`ou o grupo de recursos que você criou com seu aplicativo de função. Usar o mesmo grupo de recursos para o aplicativo de função, O WordPress VM e o plano de hospedagem facilitam a limpeza de recursos quando você terminar este tutorial. |
    | **Nome da máquina virtual** | VNET-Wordpress | O nome VM precisa ser único no grupo de recursos |
    | **[Região](https://azure.microsoft.com/regions/)** | (Europa) Europa Ocidental | Escolha uma região próxima a você ou perto das funções que acessam a VM. |
    | **Tamanho** | B1s | Escolha **Alterar tamanho** e, em seguida, selecione a imagem padrão B1s, que tem 1 vCPU e 1 GB de memória. |
    | **Tipo de autenticação** | Senha | Para usar a autenticação de senha, você também deve especificar um **nome de usuário,** uma **senha**segura e, em seguida, **confirmar senha**. Para este tutorial, você não precisará fazer login na VM a menos que precise solucionar problemas. |

1. Escolha a guia **Rede** e, em Configurar, as redes virtuais selecionem **Criar novas**.

1. Em **Criar rede virtual,** use as configurações na tabela abaixo da imagem:

    ![Guia de rede de criar VM](./media/functions-create-vnet/create-vm-2.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Você pode usar o nome padrão gerado para sua rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Use um único intervalo de endereços para a rede virtual. |
    | **Nome da sub-rede** | Tutorial-Net | Nome da sub-rede. |
    | **Intervalo de endereço** (sub-rede) | 10.10.1.0/24   | O tamanho da sub-rede define quantas interfaces podem ser adicionadas à sub-rede. Esta sub-rede é usada pelo site WordPress.  Uma `/24` sub-rede fornece 254 endereços host. |

1. Selecione **OK** para criar a rede virtual.

1. De volta na guia **Rede,** escolha **Nenhum** para **IP público**.

1. Escolha a guia **Gerenciamento** e, em seguida, na **conta de armazenamento Diagnósticos,** escolha a conta Armazenamento criada com seu aplicativo de função.

1. Selecione **Revisão + criar**. Após a validação concluída, selecione **Criar**. O processo de criação de VM leva alguns minutos. A VM criada só pode acessar a rede virtual.

1. Depois que a VM for criada, escolha **Ir para recurso** para exibir a página da sua nova VM e, em seguida, escolha **Rede** em **Configurações**.

1. Verifique se não há **IP público**. Faça uma nota do **IP privado**, que você usa para se conectar à VM a partir do seu aplicativo de função.

    ![Configurações de rede na VM](./media/functions-create-vnet/vm-networking.png)

Agora você tem um site wordpress implantado inteiramente dentro de sua rede virtual. Este site não é acessível a partir da internet pública.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Conecte seu aplicativo de função à rede virtual

Com um site do WordPress em execução em uma VM em uma rede virtual, agora você pode conectar seu aplicativo de função a essa rede virtual.

1. Em seu novo aplicativo de função, selecione **Recursos** > da Plataforma**Networking**.

    ![Escolha a rede no aplicativo de função](./media/functions-create-vnet/networking-0.png)

1. Em **VNet Integration,** selecione **Clique aqui para configurar**.

    ![Status para configurar um recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração da rede virtual, selecione **Adicionar VNet (visualização)**.

    ![Adicione a pré-visualização da Integração VNet](./media/functions-create-vnet/networking-2.png)

1. Em **Status de recurso de**rede, use as configurações na tabela abaixo da imagem:

    ![Defina a rede virtual do aplicativo de função](./media/functions-create-vnet/networking-3.png)

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Esta rede virtual é a que você criou anteriormente. |
    | **Sub-rede** | Criar nova sub-rede | Crie uma sub-rede na rede virtual para que seu aplicativo de função use. A Integração VNet deve ser configurada para usar uma sub-rede vazia. Não importa que suas funções usem uma sub-rede diferente da VM. A rede virtual direciona automaticamente o tráfego entre as duas sub-redes. |
    | **Nome da sub-rede** | Rede de funções | Nome da nova sub-rede. |
    | **Bloco de endereços de rede virtual** | 10.10.0.0/16 | Escolha o mesmo bloco de endereços usado pelo site WordPress. Você deve ter apenas um bloco de endereçodefinido. |
    | **Intervalo de endereços** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de instâncias que o aplicativo de função plano Premium pode dimensionar. Este exemplo `/24` usa uma sub-rede com 254 endereços host disponíveis. Esta sub-rede é provisionada, mas fácil de calcular. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas de status de recurso de integração e recurso de rede do VNet para retornar à página do aplicativo da função.

O aplicativo de função agora pode acessar a rede virtual onde o site wordpress está sendo executado. Em seguida, você usa [Proxies funções do Azure](functions-proxies.md) para retornar um arquivo do site WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Crie um proxy para acessar recursos de VM

Com a Integração VNet ativada, você pode criar um proxy em seu aplicativo de função para encaminhar solicitações para a VM em execução na rede virtual.

1. Em seu aplicativo de função, selecione **Proxies** > **+** e use as configurações de proxy na tabela abaixo da imagem:

    ![Defina as configurações de proxy](./media/functions-create-vnet/create-proxy.png)

    | Configuração  | Valor sugerido  | Descrição      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | PlAnT | O nome pode ser de qualquer valor. É usado para identificar o proxy. |
    | **Modelo de rota** | /planta | Rota que mapeia para um recurso VM. |
    | **URL do back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Substitua pelo `<YOUR_VM_IP>` endereço IP da VM WordPress que você criou anteriormente. Este mapeamento retorna um único arquivo do site. |

1. Selecione **Criar** para adicionar o proxy ao seu aplicativo de função.

## <a name="try-it-out"></a>Experimentar

1. No seu navegador, tente acessar a URL que você usou como URL do **Backend**. Como esperado, o pedido se estende. Um intervalo ocorre porque seu site WordPress está conectado apenas à sua rede virtual e não à internet.

1. Copie o valor da **URL** proxy do seu novo proxy e cole-o na barra de endereços do seu navegador. A imagem retornada é do site WordPress em execução dentro de sua rede virtual.

    ![Arquivo de imagem de planta retornado do site WordPress](./media/functions-create-vnet/plant.png)

Seu aplicativo de função está conectado à internet e à sua rede virtual. O proxy está recebendo uma solicitação pela internet pública e, em seguida, agindo como um proxy HTTP simples para encaminhar essa solicitação para a rede virtual conectada. O proxy então retransmite a resposta para você publicamente pela internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o site wordpress serve como uma API que é chamada usando um proxy no aplicativo de função. Este cenário faz um bom tutorial porque é fácil de configurar e visualizar. Você pode usar qualquer outra API implantada dentro de uma rede virtual. Você também poderia ter criado uma função com código que chama APIs implantadas dentro da rede virtual. Um cenário mais realista é uma função que usa APIs de clientes de dados para chamar uma instância do SQL Server implantada na rede virtual.

Funções em execução em um plano Premium compartilham a mesma infra-estrutura subjacente do App Service que os aplicativos web em planos PremiumV2. Toda a documentação para [aplicativos web no Azure App Service](../app-service/overview.md) se aplica às funções do seu plano Premium.

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de rede em Funções](./functions-networking-options.md)

[Plano premium]: functions-scale.md#premium-plan
