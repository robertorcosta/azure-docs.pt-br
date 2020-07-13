---
title: 'Tutorial: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure'
description: Neste tutorial, você aprenderá a proteger seu hub virtual com o Gerenciador de Firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563658"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutorial: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure

Com o Gerenciador de Firewall do Azure, é possível criar hubs virtuais seguros para proteger o tráfego de rede na nuvem destinado a endereços IP privados, PaaS do Azure e à Internet. O roteamento de tráfego para o firewall é automatizado, portanto, não é necessário criar UDRs (rotas definidas pelo usuário).

![proteger a rede na nuvem](media/secure-cloud-network/secure-cloud-network.png)

O Gerenciador de Firewall também dá suporte à arquitetura de rede virtual de um hub. Para obter uma comparação dos tipos de arquitetura de rede virtual de hub e hub virtual seguro, confira [quais são as opções de arquitetura do Gerenciador de Firewall do Azure?](vhubs-and-vnets.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar a rede virtual spoke
> * Criar um hub virtual seguro
> * Conectar as VNets de hub e spoke
> * Criar uma política de firewall e proteger seu hub
> * Rotear o tráfego para o seu hub
> * Testar o firewall

## <a name="create-a-hub-and-spoke-architecture"></a>Criar uma arquitetura de hub e spoke

Primeiro, crie uma rede virtual spoke na qual você pode colocar seus servidores.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Criar uma rede virtual spoke e sub-redes

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
2. Em **Assinatura**, selecione sua assinatura.
1. Para **Grupo de recursos**, selecione **Criar novo** e digite **FW-Manager** para o nome e selecione **OK**.
2. Em **Nome**, digite **Spoke-01**.
3. Em **Região**, selecione **EUA (Leste dos EUA)** .
4. Selecione **Avançar: Endereços IP**.
1. Para **Espaço de endereço**, aceite o padrão **10.0.0.0/16**.
3. Em **Nome da sub-rede**, selecione **padrão**.
4. Altere o nome da sub-rede para **Workload-SN**.
5. Para **Intervalo de endereços da sub-rede**, digite **10.0.1.0/24**.
6. Selecione **Salvar**.

Em seguida, crie uma sub-rede para um servidor de salto.

1. Selecione **Adicionar sub-rede**.
4. Para **Nome da sub-rede**, digite **Jump-SN**.
5. Para **Intervalo de endereços da sub-rede**, digite **10.0.2.0/24**.
6. Selecione **Adicionar**.

Agora, crie a rede virtual.

1. Selecione **Examinar + criar**.
2. Selecione **Criar**.

### <a name="create-the-secured-virtual-hub"></a>Criar o hub virtual seguro

Crie seu hub virtual seguro usando o Gerenciador de Firewall.

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Na caixa de pesquisa, digite **Gerenciador de Firewall** e selecione **Gerenciador de Firewall**.
3. Na página **Gerenciador de Firewall**, selecione **Exibir hubs virtuais protegidos**.
4. Na página **Gerenciador de Firewall | Hubs virtuais seguros**, selecione **Criar hub virtual seguro**.
5. Para **Grupo de recursos**, selecione **FW-Manager**.
7. Em **Região**, selecione **Leste dos EUA**.
1. Para o **Nome do hub virtual seguro**, digite **Hub-01**.
2. Em **Espaço de endereço do hub**, digite **10.1.0.0/16**.
3. Para o novo nome de vWAN, digite **Vwan-01**.
4. Deixe a caixa de seleção **Incluir o gateway de VPN para habilitar Parceiros de Segurança Confiáveis** marcada.
5. Selecione **Próximo: Firewall do Azure**.
6. Aceite a configuração padrão **Firewall do Azure** **Habilitado** e, em seguida, selecione **Próximo: Parceiro de Segurança Confiável**.
7. Aceite a configuração padrão **Parceiro de Segurança Confiável** **Desabilitado** e selecione **Próximo: Revisar + criar**.
8. Selecione **Criar**. A implantação levará cerca de 30 minutos.

### <a name="connect-the-hub-and-spoke-vnets"></a>Conectar as VNets de hub e spoke

Agora você pode emparelhar as VNets de hub e spoke.

1. Selecione o grupo de recursos **FW-Manager** e, em seguida, selecione a WAN Virtual **Vwan-01**.
2. Em **Conectividade**, selecione **Conexões de rede virtual**.
3. Selecione **Adicionar conexão**.
4. Para **Nome da conexão**, digite **hub-spoke**.
5. Para **Hubs**, selecione **Hub-01**.
6. Para **Grupo de recursos**, selecione **FW-Manager**.
7. Para **Rede virtual**, selecione **Spoke-01**.
8. Selecione **Criar**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Criar uma política de firewall e proteger seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais hubs virtuais seguros. Você criará uma política de firewall e protegerá seu hub.

1. No Gerenciador de Firewall, selecione **Exibir políticas de Firewall do Azure**.
2. Selecione **Criar uma Política de Firewall do Azure**.
3. Em **Detalhes da política**, para **Nome**, digite **Policy-01** e para **Região** selecione **Leste dos EUA**.
4. Selecione **Próximo: Regras**.
5. Na guia **Regras**, selecione **Adicionar a coleção de regras**.
6. Na página **Adicionar a coleção de regras**, digite **RC-01** para **Nome**.
7. Para **Tipo de coleção de regras**, selecione **Aplicativo**.
8. Em **Prioridade**, digite **100**.
9. Verifique se a **Ação de coleção de regras** é **Permitir**.
10. Para o **Nome** da regra, digite **Allow-msft**.
11. Para o **Tipo de origem**, selecione **Endereço IP**.
12. Para **Origem**, digite **\*** .
13. Para **Protocolo**, digite **http,https**.
14. Verifique se o **Tipo de destino** é **FQDN**.
15. Para **Destino**, digite **\*.microsoft.com**.
16. Selecione **Adicionar**.
17. Selecione **Avançar: Inteligência contra ameaças**.
18. Selecione **Avançar: Hubs**.
19. Na guia **Hubs**, selecione **Associar hubs virtuais**.
20. Selecione **Hub-01** e, em seguida, selecione **Adicionar**.
21. Selecione **Examinar + criar**.
22. Selecione **Criar**.

Isso pode demorar cerca de cinco minutos ou mais para terminar.

## <a name="route-traffic-to-your-hub"></a>Rotear o tráfego para o seu hub

Agora, você deve garantir que o tráfego seja roteado por meio do firewall.

1. No Gerenciador de Firewall, selecione **Hubs virtuais seguros**.
2. Selecione **Hub-01**.
3. Em **Configurações**, escolha **Configuração de segurança**.
4. Em **Tráfego da Internet**, selecione **Firewall do Azure**.
5. Em **Tráfego privado**, selecione **Enviar por meio do Firewall do Azure**.
10. Verifique se a conexão **hub-spoke** mostra **Tráfego da Internet** como **Protegido**.
11. Clique em **Salvar**.


## <a name="test-your-firewall"></a>Testar o firewall

Para testar suas regras de firewall, você precisará implantar alguns servidores. Você implantará Workload-Srv na sub-rede Workload-SN para testar as regras de firewall, e Jump-Srv para poder usar a Área de Trabalho Remota para se conectar a partir da Internet e, em seguida, conectar-se à Workload-Srv.

### <a name="deploy-the-servers"></a>Implantar os servidores

1. No portal do Azure, selecione **Criar um recurso**.
2. Selecione **Windows Server 2016 Datacenter** na lista **Popular**.
3. Insira esses valores para a máquina virtual:

   |Configuração  |Valor  |
   |---------|---------|
   |Resource group     |**FW-Manager**|
   |Nome da máquina virtual     |**Jump-Srv**|
   |Região     |**(EUA) Leste dos EUA**|
   |Nome de usuário administrador     |digite um nome de usuário|
   |Senha     |digite uma senha|

4. Em **Regras de porta de entrada**, para **Portas de entrada públicas**, selecione **Permitir portas selecionadas**.
5. Em **Selecionar portas de entrada**, selecione **RDP (3389)** .
6. Aceite os outros padrões e selecione **Próximo: Discos**.
7. Aceite os padrões de disco e selecione **Avançar: Rede**.
8. Verifique se **Spoke-01** está selecionado para a rede virtual e se a sub-rede é **Jump-SN**.
9. Para **IP público**, aceite o novo nome de endereço IP público padrão (Jump-Srv-ip).
11. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.
12. Selecione **Desligar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione **Examinar + criar**.
13. Examine as configurações na página de resumo e, em seguida, selecione **Criar**.

Use as informações na tabela a seguir para definir outra máquina virtual chamada **Workload-Srv**. O restante da configuração é o mesmo da máquina virtual Srv-Jump.

|Configuração  |Valor  |
|---------|---------|
|Sub-rede|**Workload-SN**|
|IP público|**Nenhuma**|
|Porta de entrada públicas|**Nenhuma**|

### <a name="add-a-route-table-and-default-route"></a>Adicionar uma tabela de rotas e uma rota padrão

Para permitir uma conexão da Internet com Jump-Srv, você deve criar uma tabela de rotas e uma rota de gateway padrão para a Internet a partir da sub-rede **Jump-SN**.

1. No portal do Azure, selecione **Criar um recurso**.
2. Digite **tabela de rotas** na caixa de pesquisa e, em seguida, selecione **Tabela de rotas**.
3. Selecione **Criar**.
4. Digite **RT-01** para **Nome**.
5. Selecione sua assinatura, **FW-Manager** para o grupo de recursos e **(EUA) Leste dos EUA** para a região.
6. Selecione **Criar**.
7. Após a conclusão da implantação, selecione a tabela de rotas **RT-01**.
8. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
9. Digite **jump-to-inet** em **Nome da rota**.
10. Digite **0.0.0.0/0** para o **Prefixo de endereço**.
11. Selecione **Internet**, para o **Tipo do próximo salto**.
12. Selecione **OK**.
13. Após a conclusão da implantação, selecione **Sub-redes** e, em seguida, **Associar**.
14. Selecione **Spoke-01**, para **Rede virtual**.
15. Selecione **Jump-SN**, selecione **Sub-rede**.
16. Selecione **OK**.

### <a name="test-the-rules"></a>Testar as regras

Agora, teste as regras de firewall para confirmar se elas funcionam conforme o esperado.

1. No portal do Azure, revise as configurações de rede da máquina virtual **Workload-Srv** e anote o endereço IP privado.
2. Conecte uma área de trabalho remota à máquina virtual **Jump-Srv** e entre. De lá, abra uma conexão de área de trabalho remota para o endereço IP privado **Workload-Srv**.

3. Abra o Internet Explorer e navegue até https://www.microsoft.com.
4. Selecione **OK** > **Fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver a página inicial da Microsoft.

5. Navegue até https://www.google.com.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se as regras de firewall estão funcionando:

* Você pode navegar para o FQDN permitido, mas não para os outros.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre parceiros de segurança confiáveis](trusted-security-partners.md)
