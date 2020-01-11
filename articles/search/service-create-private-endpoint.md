---
title: Criar um ponto de extremidade privado para conexões seguras
titleSuffix: Azure Cognitive Search
description: Atualmente em visualização, você pode restringir o acesso a um ponto de extremidade de serviço de pesquisa usando um ponto de extremidade privado e uma conexão VNet segura.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865006"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Restrinja o acesso ao Azure Pesquisa Cognitiva usando o ponto de extremidade privado e uma conexão de rede virtual.

> [!IMPORTANT] 
> O suporte para o ponto de extremidade privado está disponível atualmente como uma visualização de acesso limitado. Essa visualização só está disponível para serviços de pesquisa na **camada básica**.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-10-01-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

Neste artigo, saiba como criar um novo serviço de pesquisa que seja acessível em conexões seguras, sem acesso de endereços IP públicos. As conexões de cliente são permitidas de máquinas virtuais do Azure implantadas na mesma rede virtual que o serviço.

## <a name="about-private-endpoint-support"></a>Sobre o suporte a ponto de extremidade privado

[Pontos de extremidade privados](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permitem que um cliente em uma rede virtual acesse dados com segurança por meio de um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do [espaço de endereço de rede virtual](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) para o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição à Internet pública. Para obter uma lista de serviços de PaaS que dão suporte ao link privado, verifique a [seção disponibilidade](../private-link/private-link-overview.md#availability) na documentação do produto.

Um ponto de extremidade privado para o serviço de pesquisa permite que você:

+ Bloquear todas as conexões no ponto de extremidade público para o serviço de pesquisa.
+ Aumente a segurança para a rede virtual, permitindo que você bloqueie vazamento de dados da rede virtual.
+ Conecte-se com segurança ao serviço de pesquisa de redes locais que se conectam à rede virtual usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../expressroute/expressroute-locations.md) com emparelhamento privado.

> [!NOTE]
> Quando o ponto de extremidade de serviço é privado, alguns recursos do portal são desabilitados. Você poderá exibir e gerenciar as informações de nível de serviço, mas o acesso ao portal para indexar dados e os vários componentes no serviço, como o índice, o indexador e as definições de qualificações, é restrito por motivos de segurança.

## <a name="request-access"></a>Solicitar acesso 

Clique em [solicitar acesso](https://aka.ms/SearchPrivateLinkRequestAccess) para se inscrever neste recurso de visualização. O formulário solicita informações sobre você, sua empresa e a topologia de rede geral. Depois de examinarmos sua solicitação, você receberá um email de confirmação com instruções adicionais.

## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar a VM que será usada para acessar o ponto de extremidade privado do serviço de pesquisa.

### <a name="set-up-the-virtual-network"></a>Configurar a rede virtual
1. Entre no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, selecione **criar um recurso** > **rede** > **rede virtual**.
1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork* |
    | Espaço de endereço | Digite *10.1.0.0/16* |
    | Subscription | Selecione sua assinatura|
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK** |
    | Local | Selecione **oeste dos EUA** ou qualquer região que você esteja usando|
    | Sub-rede – Nome | Inserir *Mysubnet* |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24* |
    |||

1. Deixe o restante com os valores padrão e selecione **Criar**.


### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Na parte superior esquerda da página do portal principal, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **oeste dos EUA** ou qualquer região que você esteja usando. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **Avançar: discos**.

1. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **Avançar: rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o padrão **MyVirtualNetwork**.  |
    | Espaço de endereço | Deixar o padrão **10.1.0.0/24**.|
    | Sub-rede | Deixar o padrão **mySubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Criar seu serviço de pesquisa com um ponto de extremidade privado

Nesta seção, você criará um novo serviço de Pesquisa Cognitiva do Azure com um ponto de extremidade privado. 

1. Na parte superior esquerda da página do portal principal, selecione **criar um recurso** > **Web** > **Azure pesquisa cognitiva**.

1. Em **novos serviço de pesquisa-noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | URL | Insira um nome exclusivo. |
    | Local | Selecione a região que você especificou ao solicitar acesso a esse recurso de visualização. |
    | tipo de preço | Selecione **alterar tipo de preço** e escolha **básico**. Essa camada é necessária para a versão prévia. |
    |||
  
1. Selecione **Avançar: escala**.

1. Deixe os valores como padrão e selecione **Avançar: rede**.

1. Em **nova serviço de pesquisa-rede**, selecione **privado** para **conectividade de ponto de extremidade (dados)** .

1. Em **novo serviço de pesquisa-rede**, selecione **+ Adicionar** em **ponto de extremidade privado**. 

1. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | Local | Selecione **oeste dos EUA**.|
    | Nome | Insira *myPrivateEndpoint*.  |
    | Sub-recurso de destino | Deixe o **searchService**padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource*Group. |
    | Sub-rede | Selecione  *mySubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO** |  |
    | Integrar com a zona DNS privado  | Deixe o padrão **Sim**. |
    | Zona DNS privada  | Deixe o padrão * * (New) privatelink.search.windows.net * *. |
    |||

1. Selecione **OK**. 

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 
1. Depois que o serviço for criado, navegue até o recurso que você acabou de criar.
1. Selecione **chaves** no menu conteúdo à esquerda.
1. Copie a **chave de administração primária** para uso na próxima etapa.

 
## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte forma:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso de certificado ao entrar. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Acessar o serviço de pesquisa de forma privada da VM

Nesta seção, você verificará o acesso à rede privada ao serviço de pesquisa e se conectará de forma privada à conta de armazenamento usando o ponto de extremidade privado.

1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.

1. Insira ' nslookup [nome do serviço de pesquisa]. Search. Windows. net '

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. Siga este guia de [início rápido](search-get-started-postman.md) da VM para criar um novo índice de pesquisa em seu serviço no postmaster usando a API REST.

1. Experimente várias dessas mesmas solicitações no postmaster em sua estação de trabalho local.

1. Se você conseguir concluir o início rápido da VM, mas receber um erro informando que o servidor remoto não existe na estação de trabalho local, você configurou com êxito um ponto de extremidade privado para o serviço de pesquisa.

1. Feche a conexão de área de trabalho remota para *myVM*. 

## <a name="clean-up-resources"></a>Limpar os recursos 

Quando você terminar de usar o ponto de extremidade privado, a conta de serviço de pesquisa e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal e selecione *myResourceGroup* nos resultados da pesquisa. 
1. Selecione **Excluir grupo de recursos**. 
1. Insira o grupo de *recursos* de para **digite o nome** e selecione **excluir**.

## <a name="next-steps"></a>Próximos passos

Neste artigo, você criou uma VM em uma rede virtual e um serviço de pesquisa com um ponto de extremidade privado. Você se conectou à VM da Internet e se comunica com segurança ao serviço de pesquisa usando o link privado. 

> [!div class="nextstepaction"]
> [O que é o ponto de extremidade privado do Azure?](../private-link/private-endpoint-overview.md).
