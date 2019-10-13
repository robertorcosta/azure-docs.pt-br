---
title: Instalar o gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Saiba como instalar e configurar um Gateway de dados local.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6f182976ac655692f8c50d4e7560aa21444ce848
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298654"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados local

Um gateway de dados local é necessário quando um ou mais servidores do Azure Analysis Services na mesma região se conectam a fontes de dados locais.  Embora o gateway que você instalar seja o mesmo usado por outros serviços como Power BI, Power apps e aplicativos lógicos, ao instalar o para Azure Analysis Services, há algumas etapas adicionais que você precisa concluir. Este artigo de instalação é específico para **Azure Analysis Services**.

Para saber mais sobre o gateway e como ele é usado pelo Azure Analysis Services, consulte [conectando-se a fontes de dados locais](analysis-services-gateway.md).

## <a name="prerequisites"></a>Pré-requisitos

**Requisitos mínimos:**

* .NET 4.5 Framework
* Versão de 64 bits do Windows 7/Windows Server 2008 R2 (ou posterior)

**Recomendados:**

* CPU de 8 núcleos
* Memória de 8 GB
* Versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a instalação, ao registrar o gateway no Azure, a região padrão de sua assinatura será selecionada. Você pode escolher uma região diferente. Se tiver servidores em mais de uma região, você precisará instalar um gateway para cada região. 
* O gateway não pode ser instalado em um controlador de domínio.
* Apenas um gateway pode ser instalado em um computador.
* Instale o gateway em um computador que permanece ligado e não entra em suspensão.
* Não instale o gateway em um computador sem fio conectado à rede. O desempenho pode ser prejudicado.
* Ao instalar o gateway, a conta de usuário com a qual você está conectado ao computador precisa ter privilégios de Fazer logon como um serviço. Quando a instalação é concluída, o serviço de gateway de dados local usa a conta NT SERVICE\PBIEgwService para fazer logon como um serviço. Uma conta diferente pode ser especificada durante a instalação ou nos Serviços após a instalação ser concluída. Assegure-se que as configurações de Política de Grupo permitam que tanto a conta com a qual você está conectado ao instalar quanto a conta de serviço escolhida tenham privilégios de Fazer logon como um serviço.
* Entre no Azure com uma conta do Azure AD que seja do mesmo [locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) que a assinatura que você está usando para registrar o gateway. Contas B2B (de convidado) do Azure não têm suporte durante a instalação e registro do gateway.
* Se a fontes de dados estiverem em uma Rede Virtual (VNet) do Azure, você deverá configurar a propriedade de servidor [AlwaysUseGateway](analysis-services-vnet-gateway.md).
* O gateway (unificado) descrito aqui não tem suporte nas regiões da Alemanha do Microsoft Azure. Em vez disso, use o **Gateway local dedicado para Azure Analysis Services**, instalado a partir do **Início Rápido** no portal. 


## <a name="download"></a>Baixar

 [Baixe o gateway](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Instalar

1. Execute a instalação.

2. Selecione **Gateway de dados local**.

   ![Selecionar](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecione um local, aceite os termos e, em seguida, clique em **Instalar**.

   ![Instale os termos de licença e de local](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Entre no Azure. A conta deve estar no Azure Active Directory de seu locatário. Essa conta é usada para o administrador do gateway. Contas B2B (de convidado) do Azure não têm suporte durante a instalação e registro do gateway.

   ![Entrar no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se você entrar com uma conta de domínio, ela será mapeada para sua conta organizacional no Azure AD. Sua conta organizacional é usada como administrador do gateway.

## <a name="register"></a>Registrar

Para criar um recurso de gateway no Azure, você precisa registrar a instância local que instalou com o Serviço de Nuvem do Gateway. 

1.  Selecione **Registrar um novo gateway neste computador**.

    ![Registrar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e uma chave de recuperação para o gateway. Por padrão, o gateway usa a região padrão de sua assinatura. Se precisar selecionar uma região diferente, selecione **Alterar Região**.

    > [!IMPORTANT]
    > Salve sua chave de recuperação em um local seguro. A chave de recuperação é necessária para controlar, migrar ou restaurar um gateway. 

   ![Registrar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Criar um recurso de gateway do Azure

Após ter instalado e registrado seu gateway, você precisa criar um recurso de gateway em sua assinatura do Azure. Entre no Azure com a mesma conta usada ao registrar o gateway.

1. Em portal do Azure, clique em **criar um recurso**, procure por **Gateway de dados local**e, em seguida, clique em **criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Em **Criar gateway de conexão**, insira estas configurações:

   * **Nome**: insira um nome para o recurso do gateway. 

   * **Assinatura**: selecione a assinatura do Azure a associar ao seu recurso de gateway. 
   
     A assinatura padrão baseia-se na conta do Azure utilizada para entrar.

   * **Grupo de recursos**: Crie um grupo de recursos ou selecione um grupo de recursos existente.

   * **Localização**: selecione a região em que você registrou o gateway.

   * **Nome de Instalação**: Se a instalação do gateway ainda não estiver selecionada, selecione o gateway que você instalou no computador e registrado. 

     Quando tiver concluído, clique em **Criar**.

## <a name="connect-servers"></a>Conectar servidores ao recurso de gateway

1. Na visão geral de seu servidor do Azure Analysis Services, clique em **Gateway de Dados Local**.

   ![Conectar servidor ao gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Escolha um Gateway de Dados Local para conectar**, selecione o recurso de gateway e, em seguida, clique em **Conectar gateway selecionado**.

   ![Conectar servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, provavelmente seu servidor não está na mesma região que você especificou ao registrar o gateway.

    Quando a conexão entre o seu servidor e o recurso de gateway for bem-sucedida, o status mostrará **conectado**.


    ![Êxito ao conectar o servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

É isso. Se precisar abrir portas ou solucionar qualquer problema, não deixe de conferir [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar o Analysis Services](analysis-services-manage.md)   
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)   
* [Usar gateway para fontes de dados em uma Rede Virtual do Azure](analysis-services-vnet-gateway.md)
