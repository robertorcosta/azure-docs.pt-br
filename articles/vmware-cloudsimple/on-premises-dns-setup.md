---
title: Soluções VMware do Azure (AVS)-configurar o DNS para a nuvem privada de AVS
description: Descreve como configurar a resolução de nomes DNS para acesso ao vCenter Server em uma nuvem privada de AVS de estações de trabalho locais
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eadedcea7e6010cf93d118b3781630053609d29f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019597"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configurar o DNS para a resolução de nomes para acesso ao vCenter de nuvem privada da AVS de estações de trabalho locais

Para acessar o vCenter Server em uma nuvem privada de AVS de estações de trabalho locais, você deve configurar a resolução de endereço DNS para que o servidor vCenter possa ser resolvido pelo nome do host, bem como pelo endereço IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>Obter o endereço IP do servidor DNS para sua nuvem privada da AVS

1. Entre no portal da [AVS](access-cloudsimple-portal.md).

2. Navegue até **recursos** > **nuvens privadas da AVS** e selecione a nuvem privada AVS à qual você deseja se conectar.

3. Na página **Resumo** da nuvem privada da AVS em **informações básicas**, copie o endereço IP do servidor DNS da nuvem privada da AVS.

    ![Servidores DNS da nuvem privada AVS](media/private-cloud-dns-server.png)


Use qualquer uma dessas opções para a configuração de DNS.

* [Crie uma zona no servidor DNS para *. AVS.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Crie um encaminhador condicional no servidor DNS local para resolver *. AVS.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-avsio"></a>Crie uma zona no servidor DNS para *. AVS.io

Você pode configurar uma zona como uma zona de stub e apontar para os servidores DNS na nuvem privada para resolução de nomes. Esta seção fornece informações sobre como usar um servidor DNS BIND ou um servidor DNS do Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Criar uma zona em um servidor DNS de associação

O arquivo e os parâmetros específicos a serem configurados podem variar com base na configuração de DNS individual.

Por exemplo, para a configuração de servidor BIND padrão, edite o arquivo/etc/named.conf no servidor DNS e adicione as informações de zona a seguir.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Criar uma zona em um servidor DNS do Microsoft Windows

1. Clique com o botão direito do mouse no servidor DNS e selecione **nova zona**. 
  
    ![Nova zona](media/DNS01.png)
2. Selecione **zona de stub** e clique em **Avançar**.

    ![Nova zona](media/DNS02.png)
3. Selecione a opção apropriada dependendo do seu ambiente e clique em **Avançar**.

    ![Nova zona](media/DNS03.png)
4. Selecione **zona de pesquisa direta** e clique em **Avançar**.

    ![Nova zona](media/DNS01.png)
5. Insira o nome da zona e clique em **Avançar**.

    ![Nova zona](media/DNS05.png)
6. Insira os endereços IP dos servidores DNS para sua nuvem privada da AVS que você obteve no portal da AVS.

    ![Nova zona](media/DNS06.png)
7. Clique em **Avançar** conforme necessário para concluir a instalação do assistente.

## <a name="create-a-conditional-forwarder"></a>Criar um encaminhador condicional

Um encaminhador condicional encaminha todas as solicitações de resolução de nomes DNS para o servidor designado. Com essa configuração, qualquer solicitação para *. AVS.io é encaminhado para os servidores DNS localizados na nuvem privada da AVS. Os exemplos a seguir mostram como configurar encaminhadores em diferentes tipos de servidores DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Criar um encaminhador condicional em um servidor DNS de associação

O arquivo e os parâmetros específicos a serem configurados podem variar com base na configuração de DNS individual.

Por exemplo, para a configuração de servidor BIND padrão, edite o arquivo/etc/named.conf no servidor DNS e adicione as seguintes informações de encaminhamento condicional.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Criar um encaminhador condicional em um servidor DNS do Microsoft Windows

1. Abra o Gerenciador DNS no servidor DNS.
2. Clique com o botão direito do mouse em **encaminhadores condicionais** e selecione a opção para adicionar um novo encaminhador condicional.

    ![Encaminhador condicional 1 DNS do Windows](media/DNS08.png)
3. Insira o domínio DNS e o endereço IP dos servidores DNS na nuvem privada da AVS e clique em **OK**.
