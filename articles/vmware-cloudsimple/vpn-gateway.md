---
title: Solução Azure VMware by CloudSimple - Configure um gateway VPN
description: Descreve como configurar o gateway VPN ponto-para-site e o gateway VPN site-to-site e criar conexões entre sua rede local e sua nuvem privada CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279488"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Configure gateways VPN na rede CloudSimple

Os gateways VPN permitem que você se conecte à rede CloudSimple a partir de sua rede local e de um computador cliente remotamente. Uma conexão VPN entre sua rede local e sua rede CloudSimple fornece acesso ao vCenter e cargas de trabalho em sua Nuvem Privada. O CloudSimple suporta os gateways VPN de site para site e point-to-site.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A conexão **VPN site-to-site** permite que você configure suas cargas de trabalho da Nuvem Privada para acessar serviços locais. Você também pode usar o Active Directory no local como uma fonte de identidade para autenticar o seu vCenter privado da Nuvem.  Atualmente, apenas o tipo **de VPN baseado em políticas** é suportado.
* **A** conexão VPN ponto a ponto é a maneira mais simples de se conectar à nuvem privada a partir do seu computador. Use a conectividade VPN ponto a ponto para conectar-se à Nuvem Privada remotamente. Para obter informações sobre a instalação de um cliente para uma conexão VPN ponto a ponto, consulte [Configurar uma conexão VPN com sua Nuvem Privada](set-up-vpn.md).

Em uma região, você pode criar um gateway VPN ponto a ponto e um gateway VPN site-to-site.

## <a name="automatic-addition-of-vlansubnets"></a>Adição automática de VLAN/sub-redes

Os gateways cloudSimple VPN fornecem políticas para adicionar VLAN/sub-redes a gateways VPN.  As políticas permitem especificar regras diferentes para gerenciamento de VLAN/sub-redes e vlan/sub-redes definidas pelo usuário.  As regras para gerenciamento de VLAN/sub-redes aplicam-se a quaisquer novas nuvens privadas criadas.  As regras para VLANs/sub-redes definidas pelo usuário permitem adicionar automaticamente quaisquer novas VLAN/sub-redes às nuvens privadas existentes ou novas para um gateway VPN site-to-site, você define a política para cada conexão.

As políticas sobre a adição de VLANs/sub-redes a gateways VPN se aplicam tanto a gateways VPN de site para site quanto de VPN ponto a ponto.

## <a name="automatic-addition-of-users"></a>Adição automática de usuários

Um gateway VPN ponto a ponto permite definir uma política de adição automática para novos usuários. Por padrão, todos os proprietários e colaboradores da assinatura têm acesso ao portal CloudSimple.  Os usuários são criados apenas quando o portal CloudSimple é lançado pela primeira vez.  Selecionar **automaticamente adicionar** regras permite que qualquer novo usuário acesse a rede CloudSimple usando a conexão VPN ponto a ponto.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Configure um gateway VPN site-to-site

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede**.
2. Selecione **vpn Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração do Gateway,** especifique as configurações a seguir e clique **em "Avançar "Next**"

    * Selecione **VPN site-to-site** como o tipo de gateway.
    * Digite um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço CloudSimple é implantado.
    * Opcionalmente, habilite a alta disponibilidade.

    ![Criar gateway VPN site-to-site](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > A ativação da alta disponibilidade requer que seu dispositivo VPN no local suporte a conexão a dois endereços IP. Essa opção não pode ser desativada quando o gateway VPN for implantado.

5. Crie a primeira conexão a partir de sua rede local e clique em **Next**.

    * Digite um nome para identificar a conexão.
    * Para o IP do peer, digite o endereço IP público do gateway VPN no local.
    * Digite o identificador de pares do gateway VPN no local.  O identificador de ponto geralmente é o endereço IP público do seu gateway VPN no local.  Se você configurou um identificador específico no gateway, digite o identificador.
    * Copie a chave compartilhada para usar para conexão do gateway VPN no local.  Para alterar a chave compartilhada padrão e especificar uma nova, clique no ícone de edição.
    * Para **prefixos on-premises,** insira os prefixos CIDR on-premises que acessarão a rede CloudSimple.  Você pode adicionar vários prefixos CIDR ao criar a conexão.

    ![Criar conexão de gateway VPN site-to-site](media/create-vpn-gateway-s2s-connection.png)

6. Habilite as redes VLAN/sub-redes em sua rede Private Cloud que serão acessadas a partir da rede local e clique em **Next**.

    * Para adicionar uma VLAN/sub-rede de gerenciamento, **habilite adicionar VLANs/Sub-redes de gerenciamento de nuvens privadas**.  A sub-rede de gerenciamento é necessária para as sub-redes vMotion e vSAN.
    * Para adicionar sub-redes vMotion, **habilite adicionar rede vMotion de Nuvens Privadas**.
    * Para adicionar sub-redes vSAN, **habilite adicionar sub-rede vSAN de nuvens privadas**.
    * Selecione ou desselecione VLANs específicas.

    ![Criar conexão](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Revise as configurações e clique **em Enviar**.

    ![Revisão e criação de gateway vpn site-to-site](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Criar gateway VPN ponto a ponto

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede**.
2. Selecione **vpn Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração do Gateway,** especifique as configurações a seguir e clique **em "Avançar "Next**"

    * Selecione **vpn ponto a ponto como** o tipo de gateway.
    * Digite um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço CloudSimple é implantado.
    * Especifique a sub-rede do cliente para o gateway Point-to-Site.  Os endereços DHCP serão fornecidos da sub-rede do cliente quando você se conectar.

5. Para **Conexão/Usuário,** especifique as seguintes configurações e clique **em "A seguir".**

    * Para permitir que todos os usuários atuais e futuros acessem a Nuvem Privada através do gateway Point-to-Site, selecione **Adicionar automaticamente todos os usuários**. Quando você seleciona a opção, todos os usuários da lista de usuários são selecionados automaticamente. Você pode substituir a opção automática desselecionando usuários individuais na lista.
    * Para selecionar usuários individuais, clique nas caixas de seleção na lista de usuários.

6. A seção VLANs/Subnets permite especificar VLANs/sub-redes de gerenciamento e usuário para o gateway e conexões.

    * As opções **de ajuste automático** de definir a política global para o gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área **Select.**
    * Selecione **Adicionar VLANs/Sub-redes de gerenciamento de nuvens privadas**. 
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique **em Adicionar VLANs/Subnets definidas pelo usuário**.
    * As **configurações Select** anulam as configurações globais em **Adicionar automaticamente**.

7. Clique **em Next** para revisar as configurações. Clique nos ícones Editar para fazer qualquer alteração.
8. Clique **em Criar** para criar o gateway VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Sub-rede do cliente e protocolos para gateway VPN ponto a ponto

O gateway VPN ponto a ponto permite conexões TCP e UDP.  Escolha o protocolo a ser usado quando você se conectar a partir do seu computador selecionando a configuração TCP ou UDP.

A sub-rede de cliente configurada é usada para clientes TCP e UDP.  O prefixo CIDR é dividido em duas sub-redes, uma para TCP e outra para clientes UDP. Escolha a máscara de prefixo com base no número de usuários de VPN que se conectarão simultaneamente.  

A tabela a seguir lista o número de conexões de cliente simultâneos para máscara de prefixo.

| Máscara prefixo | /24 | / 25 | / 26 | / 27 | / 28 |
|-------------|-----|-----|-----|-----|-----|
| Número de conexões TCP simultâneas | 124 | 60 | 28 | 12 | 4 |
| Número de conexões UDP simultâneas | 124 | 60 | 28 | 12 | 4 |

Para se conectar usando vpn ponto a ponto, consulte [Conecte-se ao CloudSimple usando vpn ponto a ponto](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
