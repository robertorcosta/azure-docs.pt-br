---
title: Use Aplicativo Azure gateway para proteger seus aplicativos Web na solução VMware do Azure
description: Configure Aplicativo Azure gateway para expor com segurança seus aplicativos Web em execução na solução VMware do Azure (AVS).
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: dfe55ab6b32e9c7b73b8501a16fa6cfaad5bbabe
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514207"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Use Aplicativo Azure gateway para proteger seus aplicativos Web na solução VMware do Azure

[Aplicativo Azure gateway](https://azure.microsoft.com/services/application-gateway/) é um balanceador de carga de tráfego da Web de camada 7 que permite que você gerencie o tráfego para seus aplicativos Web. Ele oferece muitos recursos: afinidade de sessão baseada em cookies, roteamento baseado em URL e WAF (firewall do aplicativo Web) para citar alguns. (Para obter uma lista completa de recursos, consulte [aplicativo Azure recursos de gateway](../application-gateway/features.md).) Ele é oferecido em duas versões, v1 e v2. Ambos foram testados com aplicativos Web em execução na solução VMware do Azure (AVS).

Neste artigo, vamos examinar um cenário comum usando o gateway de aplicativo na frente de um farm de servidores Web com um conjunto de configurações e recomendações para proteger um aplicativo Web em execução na solução VMware do Azure (AVS). 

## <a name="topology"></a>Topologia
Conforme mostrado na figura a seguir, o gateway de aplicativo pode ser usado para proteger as máquinas virtuais IaaS do Azure, conjuntos de dimensionamento de máquinas virtuais do Azure ou servidores locais. As máquinas virtuais de AVS serão tratadas como servidores locais pelo gateway de aplicativo.

![O gateway de aplicativo protege as VMs da AVS.](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> Atualmente, o gateway de Aplicativo Azure é o único método com suporte para expor aplicativos Web em execução em máquinas virtuais de AVS.

O diagrama a seguir mostra o cenário de teste usado para validar o gateway de aplicativo com aplicativos Web da AVS.

![Integração do gateway de aplicativo com AVS executando aplicativos Web.](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

A instância do gateway de aplicativo é implantada no Hub em uma sub-rede dedicada. Ele tem um endereço IP público do Azure; é recomendável ativar a proteção contra DDoS padrão para a rede virtual. O servidor Web é hospedado em uma nuvem privada de AVS por trás dos roteadores do NSX T0 e T1. O AVS usa o [ExpressRoute alcance global](../expressroute/expressroute-global-reach.md) para habilitar a comunicação com o Hub e os sistemas locais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa.
- Uma nuvem privada AVS implantada e em execução.

## <a name="deployment-and-configuration"></a>Implantação e configuração

1. No portal do Azure, procure gateway de **aplicativo** e selecione **criar gateway de aplicativo**.

2. Forneça os detalhes básicos como na figura a seguir; em seguida, selecione **Avançar: front-ends>**. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="Criação do gateway de aplicativo":::

3. Escolha o tipo de endereço IP de front-end. Para público, escolha um endereço IP público existente ou crie um novo. Selecione **Avançar: back-ends>**.

    > [!NOTE]
    > Somente os SKUs Standard e WAF (firewall do aplicativo Web) têm suporte para front-ends privados.

4. Em seguida, adicione um pool de back-end, que descreve um conjunto de instâncias que fazem parte do aplicativo ou serviço (nesse caso, máquinas virtuais em execução na infraestrutura da AVS). Forneça os detalhes dos servidores Web em execução na nuvem privada da AVS e selecione **Adicionar**; em seguida, selecione **Avançar: configuração>**.

1. Na guia **configuração** , selecione **Adicionar uma regra de roteamento**.

6. Na guia **ouvinte** , forneça os detalhes para o ouvinte. Se HTTPS for selecionado, um certificado deverá ser fornecido de um arquivo PFX ou de um certificado existente do Azure Key Vault. 

7. Selecione a guia **destinos de back-end** e selecione o pool de back-end criado anteriormente. Para o campo **configurações de http** , selecione **Adicionar novo**.

8. Configure os parâmetros para as configurações de HTTP. Selecione **Adicionar**.

9. Se você quiser configurar regras baseadas em caminho, selecione **adicionar vários destinos para criar uma regra com base no caminho**. 

10. Adicione uma regra com base no caminho e selecione **Adicionar**. Repita para adicionar mais regras com base no caminho. 

11. Quando terminar de adicionar regras com base em caminho, selecione **Adicionar** novamente; em seguida, selecione **Avançar: marcas>**. 

12. Adicione todas as marcas desejadas. Selecione **Avançar: revisar + criar>**.

13. Uma validação será executada no seu gateway de aplicativo; Se ele for bem-sucedido, selecione **criar** para implantar.

## <a name="configuration-examples"></a>Exemplos de configuração

Nesta seção, você aprenderá a configurar o gateway de aplicativo com máquinas virtuais de AVS como os pools de back-end para os seguintes casos de uso: 

- [Hospedando vários sites](#hosting-multiple-sites)
- [Roteamento por URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hospedando vários sites

Você pode usar o portal do Azure para configurar a hospedagem de vários sites ao criar um gateway de aplicativo. Neste tutorial, você define pools de endereços de back-end usando máquinas virtuais em execução em uma nuvem privada de AVS em um gateway de aplicativo existente. O gateway de aplicativo faz parte de uma rede virtual de Hub, conforme descrito em [integrar o AVS em uma arquitetura de Hub e spoke](concepts-avs-hub-and-spoke-integration.md). Este tutorial pressupõe que você possui vários domínios e usa exemplos de www.contoso.com e www.fabrikam.com.

1. Crie as máquinas virtuais. Na nuvem privada da AVS, crie dois pools diferentes de máquinas virtuais; uma delas representará a Contoso e a segunda fabrikam. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="Pool de servidores Web em AVS":::

    Para ilustrar este tutorial, usamos o Windows Server 2016 com a função Serviços de Informações da Internet (IIS) instalada. Depois que as máquinas virtuais forem instaladas, execute os seguintes comandos do PowerShell para configurar o IIS em cada uma das VMs. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Adicione os pools de back-end. Em uma instância existente do gateway de aplicativo, selecione **pools de back-end** no menu à esquerda, selecione **Adicionar**e insira os detalhes dos novos pools. Selecione **Adicionar** no painel direito.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="Configuração do pool de back-end" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. Na seção **ouvintes** , crie um novo ouvinte para cada site. Insira os detalhes de cada ouvinte e selecione **Adicionar**.

4. Na barra de navegação à esquerda, selecione **configurações de http** e selecione **Adicionar** no painel esquerdo. Preencha os detalhes para criar uma nova configuração de HTTP e selecione **salvar**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="Configuração de configurações do http" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. Crie as regras na seção **regras** do menu à esquerda. Associe cada regra ao ouvinte correspondente. Selecione **Adicionar**.

6. Configure o pool de back-end e as configurações de HTTP correspondentes. Selecione **Adicionar**.

7. Teste a conexão. Abra seu navegador preferido e navegue até os diferentes sites hospedados em seu ambiente de AVS, por exemplo http://www.fabrikam.com .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="Configuração de back-end da regra":::

### <a name="routing-by-url"></a>Roteamento por URL

Você pode usar Aplicativo Azure gateway para configurar as regras de roteamento baseadas em caminho de URL. Neste tutorial, você define pools de endereços de back-end usando máquinas virtuais em execução em uma nuvem privada de AVS em um gateway de aplicativo existente. O gateway de aplicativo faz parte de uma rede virtual de Hub, conforme descrito em [documentação de integração nativa do Azure da AVS](concepts-avs-hub-and-spoke-integration.md). Em seguida, você criará regras de roteamento para certificar-se de que o tráfego da Web chega aos servidores apropriados nos pools.

1. Crie as máquinas virtuais. Na nuvem privada da AVS, crie um pool de máquinas virtuais para representar o web farm. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Pool de servidores Web em AVS":::

    O Windows Server 2016 com a função IIS instalado foi usado para ilustrar este tutorial. Depois que as máquinas virtuais forem instaladas, execute os seguintes comandos do PowerShell para configurar o IIS para o tutorial em cada VM. 

    A primeira máquina virtual, contoso-Web-01, hospedará o site principal.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    A segunda máquina virtual, contoso-Web-02, irá hospedar o site de imagens.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    A terceira máquina virtual, contoso-Web-03, irá hospedar o site de vídeo.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Adicione os pools de back-end. Você precisará adicionar três novos pools de back-end em uma instância existente do gateway de aplicativo. Selecione **Pools de back-end** no menu esquerdo. Selecione **Adicionar** e insira os detalhes do primeiro pool, **contoso-Web**. Adicione uma VM como o destino. Selecione **Adicionar**. Repita esse processo para **contoso-images** e **contoso-Video**, adicionando uma VM exclusiva a cada como destino. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Criação de pool de back-end" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. Na seção **ouvintes** , crie um novo ouvinte do tipo básico usando a porta 8080.

4. Na barra de navegação à esquerda, selecione **configurações de http** e selecione **Adicionar** no painel esquerdo. Preencha os detalhes para criar uma nova configuração de HTTP e selecione **salvar**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Configuração de configurações do http":::

5. Crie as regras na seção **regras** do menu à esquerda. Associe cada regra ao ouvinte criado anteriormente. Em seguida, configure o pool de back-end principal e as configurações de HTTP. Selecione **Adicionar**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Configuração de back-end da regra":::

6. Teste a configuração. Acesse o gateway de aplicativo no portal do Azure e, na seção **visão geral** , copie o endereço IP público. Em seguida, abra uma nova janela do navegador e insira a URL `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Teste de configuração":::

    Altere a URL para `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Teste de configuração":::

    Altere a URL novamente para `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Teste de configuração":::

## <a name="next-steps"></a>Próximas etapas

Examine a [documentação do gateway de aplicativo Azure](https://docs.microsoft.com/azure/application-gateway/) para obter mais exemplos de configuração.
