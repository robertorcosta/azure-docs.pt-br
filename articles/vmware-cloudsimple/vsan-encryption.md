---
title: Solução Azure VMware by CloudSimple - Configure criptografia vSAN para nuvem privada
description: Descreve como configurar o recurso de criptografia de software vSAN para que o cloudsimple private cloud possa trabalhar com um servidor de gerenciamento de chaves em execução em sua rede virtual Do Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020634"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Configure a criptografia vSAN para CloudSimple Private Cloud

Você pode configurar o recurso de criptografia de software vSAN para que o CloudSimple Private Cloud possa trabalhar com um servidor de gerenciamento de chaves em execução em sua rede virtual Do Azure.

O VMware requer o uso de uma ferramenta kmip 1.1 compatível com servidor de gerenciamento de chaves de terceiros (KMS) compatível com KMIP ao usar criptografia vSAN. Você pode aproveitar qualquer KMS suportado que seja certificado pela VMware e esteja disponível para o Azure.

Este guia descreve como usar o HyTrust KeyControl KMS em execução em uma rede virtual Do Zure. Uma abordagem semelhante pode ser usada para qualquer outra solução KMS certificada de terceiros para vSAN.

Esta solução KMS requer que você:

* Instale, configure e gerencie uma ferramenta KMS de terceiros certificada pela VMware em sua rede virtual Azure.
* Forneça suas próprias licenças para a ferramenta KMS.
* Configure e gerencie a criptografia vSAN em sua Nuvem Privada usando a ferramenta KMS de terceiros em execução em sua rede virtual Azure.

## <a name="kms-deployment-scenario"></a>Cenário de implantação do KMS

O cluster de servidor KMS é executado em sua rede virtual Azure e é acessível por IP a partir do vCenter da Nuvem Privada sobre a conexão Azure ExpressRoute configurada.

![.. /media/KMS cluster na rede virtual Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Como implantar a solução

O processo de implantação tem as seguintes etapas:

1. [Verifique se os pré-requisitos são atendidos](#verify-prerequisites-are-met)
2. [Portal CloudSimple: Obtenha informações de peering expressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portal Azure: Conecte sua rede virtual à Nuvem Privada](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portal Azure: Implante um Cluster HyTrust KeyControl em sua rede virtual](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Configure o servidor KMIP](#hytrust-webui-configure-the-kmip-server)
6. [iU do vCenter: Configure a criptografia vSAN para usar o cluster KMS em sua rede virtual Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Verifique os pré-requisitos são atendidos

Verifique o seguinte antes da implantação:

* O fornecedor, a ferramenta e a versão selecionadas do KMS estão na lista de compatibilidade vSAN.
* O fornecedor selecionado suporta uma versão da ferramenta para ser executada no Azure.
* A versão Azure da ferramenta KMS é compatível com KMIP 1.1.
* Um Gerenciador de Recursos do Azure e uma rede virtual já estão criados.
* Um CloudSimple Private Cloud já foi criado.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portal CloudSimple: Obtenha informações de peering expressRoute

Para continuar a configuração, você precisa da chave de autorização e do circuito de pares URI para ExpressRoute, além de acesso à sua Assinatura Azure. Essas informações estão disponíveis na página Conexão de Rede Virtual no portal CloudSimple. Para obter instruções, [consulte Configurar uma conexão de rede virtual com a Nuvem Privada](virtual-network-connection.md). Se você tiver algum problema para obter as informações, abra uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portal Azure: Conecte sua rede virtual à sua Nuvem Privada

1. Crie um gateway de rede virtual para sua rede virtual seguindo as instruções em [Configure um gateway de rede virtual para ExpressRoute usando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Vincule sua rede virtual ao circuito CloudSimple ExpressRoute seguindo as instruções em [Conectar uma rede virtual a um circuito ExpressRoute usando o portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Use as informações do circuito CloudSimple ExpressRoute recebidas em seu e-mail de boas-vindas da CloudSimple para vincular sua rede virtual ao circuito CloudSimple ExpressRoute no Azure.
4. Digite a chave de autorização e o circuito de pares URI, dê um nome à conexão e clique em **OK**.

![Forneça uri de circuito de pares CS ao criar a rede virtual](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Portal Azure: Implante um cluster HyTrust KeyControl no Gerenciador de Recursos do Azure em sua rede virtual

Para implantar um cluster HyTrust KeyControl no Azure Resource Manager em sua rede virtual, execute as seguintes tarefas. Consulte a documentação da [HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) para obter detalhes.

1. Crie um grupo de segurança de rede Azure (nsg-hytrust) com regras de entrada especificadas seguindo as instruções na documentação hyTrust.
2. Gerar um par de chaves SSH no Azure.
3. Implante o nó keycontrol inicial da imagem no Azure Marketplace.  Use a chave pública do par de chaves gerado e selecione **nsg-hytrust** como o grupo de segurança de rede para o nó KeyControl.
4. Converta o endereço IP privado do KeyControl em um endereço IP estático.
5. SSH para o KeyControl VM usando seu endereço IP público e a chave privada do par de chaves mencionado anteriormente.
6. Quando solicitado na camada SSH, selecione `No` para definir o nó como o nó inicial KeyControl.
7. Adicione os nós KeyControl adicionais repetindo as etapas `Yes` 3-5 deste procedimento e selecionando quando solicitado sobre a adição a um cluster existente.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Configure o servidor KMIP

Vá para https://*public-ip*, onde *public-ip* é o endereço IP público da VM do nó KeyControl. Siga estas etapas da [documentação hyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Configurando um servidor KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Criando um pacote de certificados para criptografia VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>iU do vCenter: Configure a criptografia vSAN para usar o cluster KMS em sua rede virtual Azure

Siga as instruções do HyTrust para [criar um cluster KMS no vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Adicione detalhes do cluster KMS no vCenter](media/vsan-config01.png)

No vCenter, vá para **Cluster > Configure** e selecione **Opção Geral** para vSAN. Habilite a criptografia e selecione o cluster KMS que foi adicionado anteriormente ao vCenter.

![Habilite a criptografia vSAN e configure o cluster KMS no vCenter](media/vsan-config02.png)

## <a name="references"></a>Referências

### <a name="azure"></a>Azure

[Configurar um gateway de rede virtual para ExpressRoute usando o portal do Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Conectar uma rede virtual a um circuito ExpressRoute usando o portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl e Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Configurando um servidor KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Criando um pacote de certificados para criptografia VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Criando o Cluster KMS no vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
