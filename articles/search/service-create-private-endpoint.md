---
title: Crie um ponto final privado para uma conexão segura
titleSuffix: Azure Cognitive Search
description: Configure um ponto final privado em uma rede virtual para uma conexão segura com um serviço de pesquisa cognitiva do Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945817"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Crie um ponto final privado para uma conexão segura com a Pesquisa Cognitiva do Azure (Preview)

Neste artigo, use o portal para criar uma nova instância de serviço de pesquisa cognitiva do Azure que não pode ser acessada através de um endereço IP público. Em seguida, configure uma máquina virtual Azure na mesma rede virtual e use-a para acessar o serviço de pesquisa através de um ponto final privado.

> [!Important]
> O suporte privado do Endpoint para o Azure Cognitive Search está disponível [mediante solicitação](https://aka.ms/SearchPrivateLinkRequestAccess) como uma pré-visualização de acesso limitado. Os recursos de visualização são fornecidos sem um contrato de nível de serviço e não são recomendados para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Uma vez que você tenha acesso à pré-visualização, você poderá configurar pontos finais privados para o seu serviço usando o portal Azure ou a [versão de API Management REST 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Por que usar o Private Endpoint para acesso seguro?

[Os pontos finais privados](../private-link/private-endpoint-overview.md) para a Pesquisa Cognitiva do Azure permitem que um cliente em uma rede virtual acesse com segurança dados em um índice de pesquisa sobre um [Link Privado](../private-link/private-link-overview.md). O ponto final privado usa um endereço IP do espaço de [endereço de rede virtual](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de busca atravessa a rede virtual e um link privado na rede backbone da Microsoft, eliminando a exposição da internet pública. Para obter uma lista de outros serviços PaaS que suportam o Private Link, verifique a [seção de disponibilidade](../private-link/private-link-overview.md#availability) na documentação do produto.

Pontos finais privados para o serviço de pesquisa permitem:

- Bloqueie todas as conexões no ponto final público do seu serviço de pesquisa.
- Aumente a segurança da rede virtual, permitindo bloquear a exfiltração de dados da rede virtual.
- Conecte-se com segurança ao seu serviço de pesquisa a partir de redes locais que se conectam à rede virtual usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com peering privado.

> [!NOTE]
> Existem atualmente algumas limitações na pré-visualização que você deve estar ciente:
> * Disponível apenas para serviços de pesquisa no nível **Basic.** 
> * Disponível nas regiões oeste dos EUA 2, Centro-Oeste dos EUA, Leste dos EUA, Centro-Sul dos EUA, Austrália Oriental e Austrália Sudeste.
> * Quando o ponto final do serviço é privado, alguns recursos do portal são desativados. Você poderá visualizar e gerenciar informações de nível de serviço, mas o acesso do portal aos dados do índice e aos vários componentes do serviço, como as definições de índice, indexador e skillset, é restrito por razões de segurança.
> * Quando o ponto final do serviço for privado, você deve usar a [API Search REST](https://docs.microsoft.com/rest/api/searchservice/) para carregar documentos para o índice.
> * Você deve usar o seguinte link para ver a opção de suporte de ponto final privado no portal Azure:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Solicitar acesso 

Clique [em solicitar acesso](https://aka.ms/SearchPrivateLinkRequestAccess) para se inscrever neste recurso de visualização. O formulário solicita informações sobre você, sua empresa e topologia de rede geral. Assim que analisarmos sua solicitação, você receberá um e-mail de confirmação com instruções adicionais.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar a VM que será usada para acessar o ponto final privado do seu serviço de pesquisa.

1. Na guia home do portal Azure, selecione **Criar uma** > **rede virtual**de rede**de** > recursos .

1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Digite *MyVirtualNetwork* |
    | Espaço de endereço | Digite *10.1.0.0/16* |
    | Subscription | Selecione sua assinatura|
    | Resource group | Selecione **Criar novo,** insira *myResourceGroup*e selecione **OK** |
    | Location | Selecione **o Oeste dos EUA** ou qualquer região que você esteja usando|
    | Sub-rede – Nome | Digite *mySubnet* |
    | Sub-rede – Intervalo de endereços | Digite *10.1.0.0/24* |
    |||

1. Deixe o restante com os valores padrão e selecione **Criar**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Crie um serviço de pesquisa com um ponto final privado

Nesta seção, você criará um novo serviço de pesquisa cognitiva do Azure com um ponto final privado. 

1. No lado superior esquerdo da tela no portal Azure, selecione **Criar um recurso** > **Web** > **Azure Cognitive Search**.

1. Em **Novo Serviço de Pesquisa - Noções Básicas,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | URL | Insira um nome exclusivo. |
    | Location | Selecione a região que você especificou ao solicitar acesso a este recurso de visualização. |
    | Tipo de preço | Selecione **Alterar o nível de preços e** escolha **Basic**. Este nível é necessário para a visualização. |
    |||
  
1. Selecione **A seguir: Escala**.

1. Deixe os valores como padrão e selecione **Next: Networking**.

1. Em **Novo Serviço de Pesquisa - Rede**, selecione **Privado** para **conectividade endpoint (dados)**.

1. Em **Novo Serviço de Pesquisa - Rede,** selecione + **Adicione** em ponto **final privado**. 

1. Em **Criar ponto final privado,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | Location | Selecione **Oeste dos EUA**.|
    | Nome | Digite *myPrivateEndpoint*.  |
    | Sub-recurso de destino | Deixe o serviço **de pesquisa**padrão . |
    | **Rede** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *myResourceGroup*. |
    | Sub-rede | Selecione  *mySubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO** |  |
    | Integrar com a zona DNS privado  | Deixe o padrão **Sim**. |
    | Zona DNS privada  | Deixe o padrão ** (Novo) privatelink.search.windows.net**. |
    |||

1. Selecione **OK**. 

1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 

1. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**. 

1. Uma vez que o provisionamento do seu novo serviço esteja concluído, navegue até o recurso que você acabou de criar.

1. Selecione **Teclas** no menu de conteúdo à esquerda.

1. Copie a **tecla de admin principal** para depois, ao conectar-se ao serviço.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo da tela no portal Azure, selecione Criar uma**máquina Virtual****de computação** >  **de recursos** > .

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **o Oeste dos EUA** ou qualquer região que você esteja usando. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DO ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
    | **REGRAS DA PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Deixe o RDP padrão **(3389)**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **A seguir: Discos**.

1. Em **Criar uma máquina virtual - Discos**, deixe os padrões e selecione **Next: Networking**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão .  |
    | Espaço de endereço | Deixar o padrão **10.1.0.0/24**.|
    | Sub-rede | Deixar o padrão **mySubnet (10.1.0.0/24)**.|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**. 


## <a name="connect-to-the-vm"></a>Conectar-se à VM

Baixe e conecte-se ao *vVm da* seguinte forma:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Você pode precisar selecionar **Mais opções** > **Use uma conta diferente,** para especificar as credenciais inseridas quando criou a VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  


## <a name="test-connections"></a>Conexões de teste

Nesta seção, você verificará o acesso da rede privada ao serviço de pesquisa e se conectará privadamente ao uso do Private Endpoint.

Lembre-se da introdução de que todas as interações com o serviço de pesquisa requerem a [API Search REST](https://docs.microsoft.com/rest/api/searchservice/). O portal e o .NET SDK não são suportados nesta pré-visualização.

1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.

1. Digite 'nslookup [nome do serviço de pesquisa].search.windows.net'

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. A partir da VM, conecte-se ao serviço de pesquisa e crie um índice. Você pode seguir este [quickstart](search-get-started-postman.md) para criar um novo índice de pesquisa em seu serviço no Carteiro usando a API REST. A configuração de solicitações do Carteiro requer o ponto final do serviço de pesquisa (https://[nome do serviço de pesquisa].search.windows.net) e a api-key de admin que você copiou em uma etapa anterior.

1. Completar o quickstart da VM é a confirmação de que o serviço está totalmente operacional.

1. Feche a conexão remota da área de trabalho para *myVM*. 

1. Para verificar se seu serviço não está acessível em um ponto final público, abra o Carteiro em sua estação de trabalho local e tente as primeiras várias tarefas no quickstart. Se você receber um erro de que o servidor remoto não existe, você configurou com sucesso um ponto final privado para o seu serviço de pesquisa.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar de usar o Private Endpoint, o serviço de pesquisa e a VM, exclua o grupo de recursos e todos os recursos que ele contém:
1. Digite *myResourceGroup* na caixa **pesquisar** na parte superior do portal e selecione *myResourceGroup* nos resultados da pesquisa. 
1. Selecione **Excluir grupo de recursos**. 
1. Digite *myResourceGroup* para **digitar o nome do grupo de recursos e** **selecione Excluir**.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou uma VM em uma rede virtual e um serviço de pesquisa com um Ponto Final Privado. Você se conectou à VM a partir da internet e comunicou com segurança ao serviço de pesquisa usando o Private Link. Para saber mais sobre o Private Endpoint, veja [O que é o Ponto Final Privado do Azure?](../private-link/private-endpoint-overview.md).
