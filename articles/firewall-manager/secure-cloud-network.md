---
title: 'Tutorial: Usar a Versão prévia do Gerenciador de Firewall do Azure para proteger sua rede na nuvem usando o portal do Azure'
description: Neste tutorial, você aprenderá a proteger sua rede na nuvem com o Gerenciador de Firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510030"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Tutorial: Proteja sua rede na nuvem com a Versão prévia do Gerenciador de Firewall do Azure usando o portal do Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Com a Versão prévia do Gerenciador de Firewall do Azure, é possível criar hubs seguros para proteger o tráfego de rede na nuvem destinado a endereços IP privados, PaaS do Azure e à Internet. O roteamento de tráfego para o firewall é automatizado, portanto, não é necessário criar UDRs (rotas definidas pelo usuário).

![proteger a rede na nuvem](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A Versão prévia do Gerenciador de Firewall do Azure deve ser habilitada explicitamente usando o comando `Register-AzProviderFeature` do PowerShell.

Em um prompt de comando do PowerShell, execute os seguintes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
A conclusão do registro do recurso demora até 30 minutos. Execute o seguinte comando para verificar o status do registro:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Criar uma arquitetura de hub e spoke

Primeiro, crie uma VNet spoke na qual você possa posicionar seus servidores.

### <a name="create-a-spoke-vnet-and-subnets"></a>Criar uma VNet spoke e sub-redes

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
4. Em **Nome**, digite **Spoke-01**.
5. Em **Espaço de endereço**, digite **10.0.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Para **Grupo de recursos**, selecione **Criar novo** e digite **FW-Manager** para o nome e selecione **OK**.
8. Em **Local**, selecione **(EUA) Leste dos EUA**.
9. Em **Sub-rede**, para **Nome**, digite **Workload-SN**.
10. Em **Intervalo de endereços**, digite **10.0.1.0/24**.
11. Aceite as outras configurações padrão e selecione **Criar**.

Em seguida, crie uma sub-rede para um servidor de salto.

1. Na página inicial do portal do Azure, selecione **Grupos de recursos** > **FW-Manager**.
2. Selecione a rede virtual **Spoke-01**.
3. Selecione **Sub-redes** >  **+Sub-rede**.
4. Para **Nome**, digite **Jump-SN**.
5. Em **Intervalo de endereços**, digite **10.0.2.0/24**.
6. Selecione **OK**.

### <a name="create-the-secured-virtual-hub"></a>Criar o hub virtual seguro

Crie seu hub virtual seguro usando o Gerenciador de Firewall.

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Na caixa de pesquisa, digite **Gerenciador de Firewall** e selecione **Gerenciador de Firewall**.
3. Na página**Gerenciador de Firewall**, selecione **Criar um Hub Virtual Seguro**.
4. Na página **Criar novo Hub virtual seguro**, selecione sua assinatura e o grupo de recursos **FW-Manager**.
5. Para o **Nome do hub virtual seguro**, digite **Hub-01**.
6. Em **Localização**, selecione **Leste dos EUA**.
7. Em **Espaço de endereço do hub**, digite **10.1.0.0/16**.
8. Para o novo nome de vWAN, digite **vwan-01**.
9. Deixe a caixa de seleção **Incluir o gateway de VPN para habilitar Parceiros de Segurança Confiáveis** marcada.
10. Selecione **Próximo: Firewall do Azure**.
11. Aceite a configuração padrão **Firewall do Azure** **Habilitado** e, em seguida, selecione **Próximo: Parceiro de Segurança Confiável**.
12. Aceite a configuração padrão **Parceiro de Segurança Confiável** **Desabilitado** e selecione **Próximo: Revisar + criar**.
13. Selecione **Criar**. A implantação levará cerca de 30 minutos.

### <a name="connect-the-hub-and-spoke-vnets"></a>Conectar as VNets de hub e spoke

Agora você pode emparelhar as VNets de hub e spoke.

1. Selecione o grupo de recursos **FW-Manager** e, em seguida, selecione a WAN virtual **vwan-01**.
2. Em **Conectividade**, selecione **Conexões de rede virtual**.
3. Selecione **Adicionar conexão**.
4. Para **Nome da conexão**, digite **hub-spoke**.
5. Para **Hubs**, selecione **Hub-01**.
6. Para **Rede virtual**, selecione **Spoke-01**.
7. Selecione **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Criar uma política de firewall e proteger seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais hubs virtuais seguros. Você criará uma política de firewall e protegerá seu hub.

1. No Gerenciador de Firewall, selecione **Criar uma Política de Firewall do Azure**.
2. Selecione sua assinatura e depois o grupo de recursos **FW-Manager**.
3. Em **Detalhes da política**, para **Nome**, digite **Policy-01** e para **Região** selecione **Leste dos EUA**.
4. Selecione **Próximo: Regras**.
5. Na guia **Regras**, selecione **Adicionar a coleção de regras**.
6. Na página **Adicionar a coleção de regras**, digite **RC-01** para **Nome**.
7. Para **Tipo de coleção de regras**, selecione **Aplicativo**.
8. Em **Prioridade**, digite **100**.
9. Verifique se a **Ação de coleção de regras** é **Permitir**.
10. Para o **Nome** da regra, digite **Allow-msft**.
11. Para **Endereço de origem**, digite **\*** .
12. Para **Protocolo**, digite **http,https**.
13. Verifique se o **Tipo de destino é **FQDN**.
14. Para **Destino**, digite **\*.microsoft.com**.
15. Selecione **Adicionar**.
16. Selecione **Avançar: Hubs virtuais seguros**.
17. Na guia **Hubs virtuais seguros**, selecione **Hub-01**.
19. Selecione **Examinar + criar**.
20. Selecione **Criar**.

Isso pode demorar cerca de cinco minutos ou mais para terminar.

## <a name="route-traffic-to-your-hub"></a>Rotear o tráfego para o seu hub

Agora, você deve garantir que o tráfego seja roteado por meio do firewall.

1. No Gerenciador de Firewall, selecione **Hubs virtuais seguros**.
2. Selecione **Hub-01**.
3. Em **Configurações**, selecione **Configurações de rota**.
4. Em **Tráfego da Internet**, **Tráfego de Redes Virtuais**, selecione **Enviar pelo Firewall do Azure**.
5. Em **Tráfego privado do Azure**, **Tráfego para Redes Virtuais**, selecione **Enviar pelo Firewall do Azure**.
6. Selecione **Editar os prefixos de endereço IP**.
7. Selecione **Adicionar um prefixo de endereço IP**.
8. Digite **10.0.1.0/24** como o endereço da sub-rede de carga de trabalho e selecione **Salvar**.
9. Em **Configurações**, selecione **Conexões**.
10. Selecione a conexão **hub-spoke** e, em seguida, selecione **Proteger o tráfego da Internet** e, em seguida, selecione **OK**.


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
   |Nome de usuário administrador     |**azureuser**|
   |Senha     |**Azure123456!**|

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
|IP público|**Nenhum**|
|Porta de entrada públicas|**Nenhum**|

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
