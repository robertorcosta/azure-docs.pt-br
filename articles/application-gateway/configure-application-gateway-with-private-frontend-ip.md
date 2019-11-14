---
title: Configurar um ponto de extremidade do ILB (balanceador de carga interno)
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como configurar o gateway de aplicativo com um endereço IP de front-end privado
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075210"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurar um gateway de aplicativo com um ponto de extremidade de ILB (balanceador de carga interno)

Aplicativo Azure gateway pode ser configurado com um VIP voltado para a Internet ou com um ponto de extremidade interno que não é exposto à Internet (usando um IP privado para o endereço IP de front-end), também conhecido como um ponto de extremidade ILB (balanceador de carga interno). Configurar o gateway usando um endereço IP privado de front-end é útil para aplicativos de linha de negócios internos que não são expostos à Internet. Isso também é útil para serviços e camadas em um aplicativo multicamada que reside em um limite de segurança não exposto à Internet, mas que ainda exige distribuição de carga round robin, adesão da sessão ou terminação SSL.

Este artigo orienta você pelas etapas para configurar um gateway de aplicativo com um endereço IP privado de front-end no portal do Azure.

Neste artigo, você aprenderá a:

- Criar uma configuração de IP de front-end particular para um gateway de aplicativo
- Criar um gateway de aplicativo com configuração de IP de front-end privado


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, criaremos uma nova rede virtual. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

1. Clique no botão **Novo** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Digite *myAppGateway* para o nome do gateway de aplicativo e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
5. Clique em **Escolher uma rede virtual**, clique em **Criar novo** e insira esses valores para a rede virtual:
   - myVNet * – para o nome da rede virtual.
   - 10.0.0.0/16 *-para o espaço de endereço de rede virtual.
   - *myAGSubnet* – para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Escolha a configuração de IP de front-end como privada e, por padrão, é uma atribuição de endereço IP dinâmico. O primeiro endereço disponível da sub-rede escolhida será atribuído como o endereço IP de front-end.
8. Se você quiser escolher um IP privado do intervalo de endereços de sub-rede (alocação estática), clique na caixa **escolha um endereço IP privado específico** e especifique o endereço IP.
   > [!NOTE]
   > Uma vez alocado, o tipo de endereço IP (estático ou dinâmico) não pode ser alterado posteriormente.
9. Escolha a configuração do ouvinte para o protocolo e a porta, configuração de WAF (se necessário) e clique em OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Examine as configurações na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicativo. Pode demorar vários minutos para o gateway de aplicativo ser criado, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-pool"></a>Adicionar pool de back-end

O pool de back-end é usado para rotear solicitações aos servidores back-end que atenderão à solicitação. O back-end pode ser formado por NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. Neste exemplo, usaremos máquinas virtuais como o back-end de destino. Nós podemos usar máquinas virtuais existentes ou criar novas. Neste exemplo, criaremos duas máquinas virtuais que o Azure usará como servidores back-end para o gateway de aplicativo. Para fazer isso, nós iremos:

1. Criar 2 novas VMS, *myVM* e *myVM2*, para serem usadas como servidores back-end.
2. Instale IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **Novo**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Insira esses valores para a máquina virtual:
   - *myVM* – para o nome da máquina virtual.
   - *azureuser* – para o nome de usuário do administrador.
   - *Azure123456!* para a senha.
   - Clique em **Usar existente** e selecione *myResourceGroupAG*.
4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **selecionar**.
6. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**.
7. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
8. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.
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
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
