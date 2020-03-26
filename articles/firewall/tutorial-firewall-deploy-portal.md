---
title: 'Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure'
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223643"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure

O controle do acesso à saída de rede é uma parte importante de um plano geral de segurança de rede. Por exemplo, você talvez queira limitar o acesso a sites. Ou você talvez queira limitar os endereços IP e portas de saída que podem ser acessados.

Uma maneira de controlar o acesso à saída de rede em uma sub-rede do Azure é com o Firewall do Azure. Com o Firewall do Azure, você pode configurar:

* Regras de aplicativo que definem FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados em uma sub-rede.
* Regras de rede que definem endereço de origem, protocolo, porta de destino e endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando o tráfego de rede para o firewall foi roteado como a sub-rede de gateway padrão.

Para este tutorial, você criará uma única VNET simplificada com três sub-redes para facilitar a implantação. Para implantações de produção, é recomendado um [modelo de hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). O firewall está na própria VNet. Os servidores de carga de trabalho estão em VNets emparelhadas na mesma região que uma ou mais sub-redes.

* **AzureFirewallSubnet**: o firewall está nesta sub-rede.
* **Workload-SN**: o servidor de carga de trabalho está nessa sub-rede. O tráfego de rede dessa sub-rede passa pelo firewall.
* **Jump-SN** -o servidor “jump” está nessa sub-rede. O servidor jump tem um endereço IP público a que você pode se conectar usando a Área de Trabalho Remota. A partir daí, você pode se conectar (usando outra Área de Trabalho Remota) ao servidor de carga de trabalho.

![Infraestrutura de rede do tutorial](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configure uma regra de aplicativo para permitir acesso a www.google.com
> * Configurar uma regra de rede para permitir o acesso a servidores DNS externos
> * Testar o firewall

Se preferir, você pode concluir este tutorial usando o [Azure PowerShell](deploy-ps.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Primeiro, crie um grupo de recursos para conter os recursos necessários à implantação do firewall. Em seguida, crie uma VNET, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No menu do portal do Azure, selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos* em qualquer página. Em seguida, selecione**Adicionar**.
3. Em **Nome do grupo de recursos**, insira *Test-FW-RG*.
4. Em **Assinatura**, selecione sua assinatura.
5. Em **Local do grupo de recursos**, selecione um local. Todos os demais recursos criados devem estar na mesma localização.
6. Selecione **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNET

Essa VNET conterá três sub-redes.

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
1. Selecione **Rede** > **Rede virtual**.
1. Em **Nome**, digite **Test-FW-VN**.
1. Em **Espaço de endereço**, digite **10.0.0.0/16**.
1. Em **Assinatura**, selecione sua assinatura.
1. Para **Grupo de recursos**, selecione **Test-FW-RG**.
1. Em **local**, selecione o mesmo local usado anteriormente.
1. Em **Sub-rede**, digite **AzureFirewallSubnet** em **Nome**. O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
1. Em **Intervalo de endereços**, digite **10.0.1.0/26**.
1. Aceite as outras configurações padrão e selecione **Criar**.

### <a name="create-additional-subnets"></a>Criar sub-redes adicionais

Em seguida, crie sub-redes para o servidor jump e uma sub-rede para os servidores de carga de trabalho.

1. No menu do portal do Azure, selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos* em qualquer página. Em seguida, selecione **Test-FW-RG**.
2. Selecione a rede virtual **Test-FW-VN**.
3. Selecione **Sub-redes** >  **+Sub-rede**.
4. Em **Nome**, digite **Workload-SN**.
5. Em **Intervalo de endereços**, digite **10.0.2.0/24**.
6. Selecione **OK**.

Crie outra sub-rede denominada **Jump-SN**, intervalo de endereços **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie as máquinas virtuais de jump e carga de trabalho e coloque-as nas sub-redes apropriadas.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em Destaque.
3. Insira esses valores para a máquina virtual:

   |Configuração  |Valor  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Nome da máquina virtual     |**Srv-Jump**|
   |Região     |Igual ao anterior|
   |Nome de usuário administrador     |**azureuser**|
   |Senha     |**Azure123456!**|

4. Em **Regras de porta de entrada**, para **Portas de entrada públicas**, selecione **Permitir portas selecionadas**.
5. Em **Selecionar portas de entrada**, selecione **RDP (3389)** .

6. Aceite os outros padrões e selecione **Próximo: Discos**.
7. Aceite os padrões de disco e selecione **Avançar: Rede**.
8. Verifique se **Test-FW-VN** está selecionado para a rede virtual e se a sub-rede é **Jump-SN**.
9. Para **IP público**, aceite o novo nome de endereço IP público padrão (Srv-Jump-ip).
11. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.
12. Selecione **Desligar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione **Examinar + criar**.
13. Examine as configurações na página de resumo e, em seguida, selecione **Criar**.

Use as informações na tabela a seguir para definir outra máquina virtual chamada **Srv-Work**. O restante da configuração é o mesmo da máquina virtual Srv-Jump.

|Configuração  |Valor  |
|---------|---------|
|Sub-rede|**Workload-SN**|
|IP público|**Nenhuma**|
|Porta de entrada públicas|**Nenhuma**|

## <a name="deploy-the-firewall"></a>Implantar o firewall

Implante o firewall na VNET.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
2. Digite **firewall** na caixa de pesquisa e pressione **Enter**.
3. Selecione **Firewall** e, em seguida, selecione **Criar**.
4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:

   |Configuração  |Valor  |
   |---------|---------|
   |Subscription     |\<sua assinatura\>|
   |Resource group     |**Test-FW-RG** |
   |Nome     |**Test-FW01**|
   |Location     |Selecionar o mesmo local usado anteriormente|
   |Escolher uma rede virtual     |**Usar existente**: **Test-FW-VN**|
   |Endereço IP público     |**Adicionar nova**. O endereço IP público deve ser do tipo SKU Standard.|

5. Selecione **Examinar + criar**.
6. Examine o resumo e selecione **Criar** para criar o firewall.

   Isso levará alguns minutos para ser implantado.
7. Após a implantação ser concluída, vá para o grupo de recursos **Test-FW-RG** e selecione o firewall **Test-FW01**.
8. Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Para a sub-rede **Workload-SN**, configure a rota de saída padrão para atravessar o firewall.

1. No menu do portal do Azure, selecione **Todos os recursos** ou, em qualquer página, pesquise por *Todos os serviços* e selecione essa opção.
2. Em **Rede**, selecione **Tabelas de rotas**.
3. Selecione **Adicionar**.
4. Em **Nome**, digite **Firewall-route**.
5. Em **Assinatura**, selecione sua assinatura.
6. Para **Grupo de recursos**, selecione **Test-FW-RG**.
7. Em **local**, selecione o mesmo local usado anteriormente.
8. Selecione **Criar**.
9. Selecione **Atualizar** e, em seguida, selecione a tabela de rotas **Firewall-route**.
10. Selecione **Sub-redes** e, em seguida, selecione **Associar**.
11. Clique em **Rede virtual** > **Test-FW-VN**.
12. Para **Sub-rede**, selecione **Workload-SN**. Não deixe de selecionar apenas a sub-rede **Workload-SN** para essa rota, caso contrário, o firewall não funcionará corretamente.

13. Selecione **OK**.
14. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
15. Para **Nome da rota**, digite **fw-dg**.
16. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
17. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
18. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
19. Selecione **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicativo

Essa é a regra de aplicativo que permite o acesso de saída para www.google.com.

1. Abra **Test-FW-RG** e selecione o firewall **Test-FW01**.
2. Na página **Test-FW01**, em **Configurações**, selecione **Regras**.
3. Selecione a guia **Coleção de regras de aplicativo**.
4. Selecione **Adicionar coleção de regras de aplicativo**.
5. Em **Nome**, digite **App-Coll01**.
6. Digite **200** em **Prioridade**.
7. Em **Ação**, selecione **Permitir**.
8. Em **Regras**, **FQDNs de Destino**, para **Nome**, digite **Allow-Google**.
9. Em **Tipo de origem**, selecione **Endereço IP**.
10. Em **Origem**, digite **10.0.2.0/24**.
11. Em **Protocol:port**, digite **http, https**.
12. Para **FQDNS de destino**, digite **www.google.com**
13. Selecione **Adicionar**.

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. Para saber mais, veja [FQDNs de infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Essa é a regra de rede que permite o acesso de saída para dois endereços IP na porta 53 (DNS).

1. Selecione a guia **Coleção de regras de rede**.
2. Selecione **Adicionar coleção de regras de rede**.
3. Em **Nome**, digite **Net-Coll01**.
4. Digite **200** em **Prioridade**.
5. Em **Ação**, selecione **Permitir**.
6. Em **Regras**, **Endereços IP**, para de **Nome**, digite **Allow-DNS**.
7. Em **Protocolo**, selecione **UDP**.
9. Em **Tipo de origem**, selecione **Endereço IP**.
1. Em **Origem**, digite **10.0.2.0/24**.
2. Em **Endereço de destino**, digite **209.244.0.3,209.244.0.4**

   Esses são servidores DNS públicos operados pelo CenturyLink.
1. Em **Portas de Destino**, digite **53**.
2. Selecione **Adicionar**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Altere os endereços DNS primário e secundário para o adaptador de rede **Srv-Wprk**

Para fins de teste neste tutorial, você vai configurar os endereços DNS primários e secundários do servidor. Isso não é um requisito geral do Firewall do Azure.

1. No menu do portal do Azure, selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos* em qualquer página. Selecione o grupo de recursos **Test-FW-RG**.
2. Selecione o adaptador de rede da máquina virtual **Srv-Work**.
3. Em **Configurações**, selecione **Servidores DNS**.
4. Em **Servidores DNS**, selecione **Personalizado**.
5. Digite **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Clique em **Salvar**.
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar o firewall

Agora teste o firewall para confirmar se ele funciona conforme o esperado.

1. No portal do Azure, reveja as configurações de rede da máquina virtual **Srv-Work** e anote o endereço IP privado.
2. Conecte uma área de trabalho remota à máquina virtual **Srv-Jump** e entre. De lá, abra uma conexão de área de trabalho remota para o endereço IP privado **Srv-Work**.
3. Abra o Internet Explorer e navegue até https://www.google.com.
4. Selecione **OK** > **Fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver a página inicial do Google.

5. Navegue até https://www.microsoft.com.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se as regras de firewall estão funcionando:

* Você pode navegar para o FQDN permitido, mas não para os outros.
* É possível resolver nomes DNS usando o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **Test-FW-RG** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
