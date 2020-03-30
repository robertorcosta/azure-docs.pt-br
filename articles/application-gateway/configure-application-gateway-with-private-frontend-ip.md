---
title: Configure um ponto final do balanceador de carga interna (ILB)
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como configurar o Application Gateway com um endereço IP frontend privado
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: f56929e14aef34f675139782328ed5c559df12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198591"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configure um gateway de aplicativo com um ponto final do balanceador de carga interna (ILB)

O Azure Application Gateway pode ser configurado com um VIP voltado para a Internet ou com um ponto final interno que não esteja exposto à Internet. Um ponto final interno usa um endereço IP privado para o frontend, que também é conhecido como um *ponto final do balanceador de carga interno (ILB).*

Configurar o gateway usando um endereço IP privado frontend é útil para aplicativos internos de linha de negócios que não estão expostos à Internet. Também é útil para serviços e níveis dentro de um aplicativo de vários níveis que estão em um limite de segurança que não está exposto à Internet, mas ainda exige distribuição de carga de round-robin, pegajosa de sessão ou terminação de Secure Sockets Layer (SSL).

Este artigo orienta você através das etapas para configurar um gateway de aplicativo com um endereço IP privado frontend usando o portal Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Microsoft Azure em <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você cria uma nova rede virtual. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

1. Expanda o menu portal e **selecione Criar um recurso**.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Digite *myAppGateway* para o nome do gateway de aplicativo e *myResourceGroupAG* para o novo grupo de recursos.
4. Para **Região,** selecione **(EUA) Central EUA**.
5. Para **Nível,** selecione **Padrão**.
6. Em **Configurar rede virtual,** selecione **Criar novas**e, em seguida, insira esses valores para a rede virtual:
   - *myVNet* – para o nome da rede virtual.
   - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
   - *myAGSubnet* – para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
   - *myBackendSubnet* - para o nome da sub-rede backend.
   - *10.0.1.0/24* - para o espaço de endereço da sub-rede backend.

    ![Criar rede virtual](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selecione **OK** para criar a rede virtual e a sub-rede.
7. Selecione **Next:Frontends**.
8. Para **o tipo de endereço IP frontend,** selecione **Private**.

   Por padrão, é uma atribuição dinâmica de endereço IP. O primeiro endereço disponível da sub-rede configurada é atribuído como endereço IP frontend.
   > [!NOTE]
   > Uma vez alocado, o tipo de endereço IP (estático ou dinâmico) não pode ser alterado posteriormente.
9. Selecione **Next:Backends**.
10. Selecione **Adicionar um pool de backend**.
11. Para **Nome,** digite *o appGatewayBackendPool*.
12. Para **adicionar pool backend sem metas,** selecione **Sim**. Você adicionará os alvos mais tarde.
13. Selecione **Adicionar**.
14. Selecione **Next:Configuration**.
15. Em **Regras de roteamento,** **selecione Adicionar uma regra**.
16. Para **nome de regra,** *digite Rrule-01*.
17. Para **o nome do ouvinte,** digite *Ouvinte-01*.
18. Para **o IP frontend,** selecione **Privado**.
19. Aceite os demais padrões e selecione a guia **'Destinos Backend'.**
20. Para **o tipo De destino,** selecione Pool **Backend**e selecione **o aplicativoGatewayBackendPool**.
21. Para **a configuração HTTP,** selecione **Criar novo**.
22. Para **nome de configuração HTTP,** digite *http-setting-01*.
23. Para **o protocolo Backend,** selecione **HTTP**.
24. Para **porta Backend,** tipo *80*.
25. Aceite os padrões restantes e selecione **Adicionar**.
26. Na página Adicionar uma regra **de roteamento,** selecione **Adicionar**.
27. Selecione **A seguir: Tags**.
28. Selecione **A seguir: Revisão + criar**.
29. Revise as configurações na página de resumo e selecione **Criar** para criar os recursos da rede e o gateway de aplicativo. Pode levar vários minutos para criar o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-pool"></a>Adicionar pool de back-end

O pool de back-end é usado para encaminhar solicitações aos servidores back-end que atendem à solicitação. O backend pode ser composto por NICs, conjuntos de escala de máquinavirtual, endereços IP públicos, endereços IP internos, nomes de domínio totalmente qualificados (FQDN) e back-ends de vários locatários, como o Azure App Service. Neste exemplo, você usa máquinas virtuais como o back-end de destino. Você pode criar máquinas virtuais ou usar as existentes. Neste exemplo, você cria duas máquinas virtuais que o Azure usa como servidores de back-end para o gateway de aplicativo.

Para fazer isto:

1. Crie duas novas máquinas virtuais, *myVM* e *myVM2,* usadas como servidores back-end.
2. Instale IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso**.
2. Selecione **Computação** e selecione **Máquina Virtual**.
4. Insira esses valores para a máquina virtual:
   - selecione *o grupo myResourceGroupAG* for **Resource**.
   - *myVM* - para **nome de máquina virtual**.
   - Selecione **o Windows Server 2019 Datacenter** for **Image**.
   - *azureadmin* - para o nome de **usuário**.
   - *Azure123456!* para a **Senha**.
5. Aceite os demais padrões e selecione **Next : Discos**.
6. Aceite os padrões e selecione **Next : Networking**.
7. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**.
8. Aceite os demais padrões e selecione **Next : Management**.
9. Selecione **Desligar** para desabilitar o diagnóstico de inicialização.
10. Aceite os demais padrões e selecione **Next : Advanced**.
11. Selecione **A seguir : Tags**.
12. Selecione **A seguir : Revisão + criar**.
13. Examine as configurações na página de resumo e, em seguida, selecione **Criar**. Pode levar vários minutos para criar a VM. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a Cloud Shell e certifique-se de que ela está definida como **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Execute o comando a seguir para instalar o IIS na máquina virtual:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você acabou de concluir. Insira myVM2 para seu nome e para VMName em Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores back-end ao pool de back-end

1. Selecione **Todos os recursos**e selecione **myAppGateway**.
2. Selecione **Pools de back-end**. Selecione **o aplicativoGatewayBackendPool**.
3. Em **Tipo de Destino,** selecione **Máquina virtual** e em **Target,** selecione o vNIC associado ao myVM.
4. Repita para adicionar MyVM2.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. selecione **Salvar.**

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Verifique o IP frontend que foi atribuído clicando na página **Configurações IP** do Frontend no portal.
    ![frontendip-5 privado](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copie o endereço IP privado e cole-o na barra de endereços do navegador em uma VM no mesmo VNet ou no local que tenha conectividade com este VNet e tente acessar o Gateway de aplicativo.

## <a name="next-steps"></a>Próximas etapas

Se você quiser monitorar a saúde do seu backend, consulte [Registros de saúde e diagnóstico back-end para Gateway de aplicativo](application-gateway-diagnostics.md).
