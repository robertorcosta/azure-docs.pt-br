---
title: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure (versão prévia)
description: Este artigo explica como se conectar de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure (versão prévia).
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 09/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ccbcdbe9204120e1cf181136f566556ec30be871
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054527"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint-preview"></a>Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure (versão prévia)

O ponto de extremidade privado do Azure (versão prévia) é o bloco de construção fundamental para o link privado do Azure. Usando o ponto de extremidade privado, você pode se conectar de forma privada ao seu aplicativo Web. Neste artigo, você aprenderá a implantar um aplicativo Web usando o ponto de extremidade privado e, em seguida, conectar-se ao aplicativo Web de uma VM (máquina virtual).

Para obter mais informações, consulte [usar pontos de extremidade privados para um aplicativo Web do Azure][privateendpointwebapp].

> [!Note]
> O ponto de extremidade privado (versão prévia) está disponível em regiões públicas para aplicativos Web do Windows PremiumV2, aplicativos Web do Linux e o plano Azure Functions Premium (às vezes conhecido como o plano Premium elástico). 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Antes de começar, entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Criar uma rede virtual e uma máquina virtual

Nesta seção, você cria uma rede virtual e uma sub-rede para hospedar uma VM que você usará para acessar um aplicativo Web por meio de um ponto de extremidade privado.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Para criar a rede virtual e a sub-rede, faça o seguinte:

1. No painel esquerdo, selecione **criar um recurso**  >  **Networking**  >  **rede redes virtuais**.

1. No painel **criar rede virtual** , selecione a guia **noções básicas** e, em seguida, insira as informações mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do painel "criar rede virtual" no portal do Azure.][1]

1. Selecione a guia **endereços IP** e insira as informações que são mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da guia "endereços IP" no painel criar rede virtual.][2]

1. Na seção **sub-rede** , selecione **Adicionar sub-rede**, insira as informações mostradas aqui e, em seguida, selecione **Adicionar**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do painel "Adicionar sub-rede".][3]

1. Selecione **Examinar + criar**.

1. Após a validação bem-sucedida, selecione **criar**.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Para criar a máquina virtual, faça o seguinte:

1. No portal do Azure, no painel esquerdo, selecione **criar um recurso**  >  **computação**  >  **máquina virtual**.

1. No painel **criar uma máquina virtual-básico** , insira as informações que são mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do painel "criar uma máquina virtual".][4]

1. Selecione **Avançar: Discos**.

1. No painel **discos** , mantenha as configurações padrão e, em seguida, selecione **Avançar: rede**.

1. No painel **rede** , insira as informações que são mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da guia "rede" no painel "criar uma máquina virtual".][5]

1. Selecione **Examinar + criar**.

1. Após a validação bem-sucedida, selecione **criar**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Criar um aplicativo Web e um ponto de extremidade privado

Nesta seção, você cria um aplicativo Web privado que usa um ponto de extremidade privado.

> [!Note]
> O recurso de ponto de extremidade privado está disponível apenas para a camada PremiumV2.

### <a name="create-the-web-app"></a>Criar o aplicativo Web

1. No portal do Azure, no painel esquerdo, selecione **criar um recurso**  >  **Web**  >  **aplicativo Web**.

1. No painel **aplicativo Web** , selecione a guia **noções básicas** e, em seguida, insira as informações mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da guia "Noções básicas" no painel "aplicativo Web".][6]

1. Selecione **Examinar + criar**.

1. Após a validação bem-sucedida, selecione **criar**.

### <a name="create-the-private-endpoint"></a>Criar o ponto de extremidade privado

1. Em Propriedades do aplicativo Web, em **configurações**, selecione **rede**e, em conexões de **ponto de extremidade privado (versão prévia)**, selecione **configurar suas conexões de ponto de extremidade privado**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do link "configurar suas conexões de ponto de extremidade privado" no painel de rede do aplicativo Web.][7]

1. No assistente **conexões de ponto de extremidade privado (versão prévia)** , selecione **Adicionar**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do botão Adicionar no assistente "conexões de ponto de extremidade privado (visualização)".][8]

1. Selecione as informações corretas nas listas suspensas **assinatura**, **rede virtual**e **sub-rede** e, em seguida, selecione **OK**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do painel "Adicionar ponto de extremidade privado (visualização)".][9]

1. Monitore o progresso da criação do ponto de extremidade privado.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do progresso da adição do ponto de extremidade privado. ][10]
   >  ![ Captura de tela do ponto de extremidade privado recém-criado.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Conectar-se à VM da Internet

1. Na caixa de **pesquisa** portal do Azure, digite **myVm**.
1. Selecione **conectar**e, em seguida, selecione **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do botão "RDP" no painel "myVM".][12]

1. No painel **conectar com RDP** , selecione **baixar arquivo RDP**.  

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do botão "baixar arquivo RDP" no painel "conectar com RDP".][13]

   O Azure cria um arquivo de protocolo RDP (RDP) e o baixa em seu computador.   

1. Abra o arquivo RDP baixado.

   a. No prompt, selecione **conectar**.  
   b. Insira o nome de usuário e a senha que você especificou quando criou a VM.

     > [!Note]
     > Para usar essas credenciais, talvez seja necessário selecionar **mais escolhas**  >  **usar uma conta diferente**.

1. Selecione **OK**.

   > [!Note]
   > Se você receber um aviso de certificado durante o processo de entrada, selecione **Sim** ou **continuar**.

1. Quando a janela área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.

## <a name="access-the-web-app-privately-from-the-vm"></a>Acessar o aplicativo Web de forma privada da VM

Nesta seção, você se conecta de modo privado ao aplicativo Web usando o ponto de extremidade privado.

1. Para obter o IP privado do seu ponto de extremidade privado, na caixa de **pesquisa** , digite **link privado** e, na lista de resultados, selecione **link privado**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do link "link privado" na lista de resultados da pesquisa.][14]

1. No Central de links privado, no painel esquerdo, selecione **pontos de extremidade privados** para exibir seus pontos de extremidade privados.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da lista de pontos de extremidade particulares no centro de links privado.][15]

1. Selecione o ponto de extremidade privado que se vincula ao seu aplicativo Web e à sua sub-rede.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do painel Propriedades de um ponto de extremidade privado.][16]

1. Copie o IP privado do seu ponto de extremidade privado e o FQDN (nome de domínio totalmente qualificado) do seu aplicativo Web. No exemplo anterior, a ID particular é *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. No painel **myVM** , verifique se o aplicativo Web está inacessível por meio do IP público. Para fazer isso, abra um navegador e cole o nome do aplicativo Web. A página deve exibir uma mensagem "erro 403-Proibido".

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de uma página de erro "erro 403-Proibido".][17]

   > [!Important]
   > Como esse recurso está em versão prévia, você precisa gerenciar manualmente a entrada DNS (serviço de nomes de domínio).

   Para o DNS, siga um destes procedimentos:
 
   - Use o serviço de zona privada do DNS do Azure.  

     a. Crie uma zona DNS privada denominada *`privatelink.azurewebsites.net`* e, em seguida, vincule-a à rede virtual.  
     b. Crie os dois registros a (isto é, o nome do aplicativo e o nome do Gerenciador de controle de serviço [SCM]) com o endereço IP do seu ponto de extremidade privado.  
     > [!div class="mx-imgBorder"]
     > ![Captura de tela de registros de zona privada DNS.][21]  

   - Use o arquivo de *hosts* da VM.  

     a. Crie a entrada hosts, abra o explorador de arquivos e procure o arquivo *hosts* .  
     > [!div class="mx-imgBorder"]
     > ![Captura de tela mostrando o arquivo de hosts no explorador de arquivos.][18]  
     b. Adicione uma entrada que contenha o endereço IP privado e o nome público do seu aplicativo Web editando o arquivo de *hosts* em um editor de texto.  
     > [!div class="mx-imgBorder"]
     > ![Captura de tela do texto do arquivo de hosts.][19]  
     c. Salve o arquivo.

1. Em um navegador, digite a URL do seu aplicativo Web.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de um navegador exibindo um aplicativo Web.][20]

Agora você está acessando seu aplicativo Web por meio do ponto de extremidade privado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de usar o ponto de extremidade privado, o aplicativo Web e a VM, exclua o grupo de recursos e todos os recursos que ele contém.

1. Na portal do Azure, na caixa de **pesquisa** , digite **Ready-RG**e, em seguida, selecione **Ready-RG** na lista de resultados.

1. Selecione **Excluir grupo de recursos**.

1. Em **digite o nome do grupo de recursos**, digite **Ready-RG**e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma VM em uma rede virtual, um aplicativo Web e um ponto de extremidade privado. Você se conectou a uma VM da Internet e se comunica com segurança ao aplicativo Web usando o link privado. 

Para saber mais sobre o ponto de extremidade privado (versão prévia), consulte [o que é o ponto de extremidade privado do Azure?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
