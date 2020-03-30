---
title: Solução Azure VMware by CloudSimple - Configure DNS for CloudSimple Private Cloud
description: Descreve como configurar a resolução de nomes DNS para acesso ao servidor vCenter em uma Nuvem Privada Cloud simples a partir de estações de trabalho no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246104"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configure o DNS para resolução de nomes para acesso ao Private Cloud vCenter a partir de estações de trabalho no local

Para acessar o servidor vCenter em uma Nuvem Privada Cloud de estações de trabalho no local, você deve configurar a resolução do endereço DNS para que o servidor vCenter possa ser endereçado pelo nome do host, bem como por endereço IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Obtenha o endereço IP do servidor DNS para sua Nuvem Privada

1. Faça login no [portal CloudSimple](access-cloudsimple-portal.md).

2. Navegue para **Recursos** > **Nuvens Privadas** e selecione a Nuvem Privada à sua vontade de se conectar.

3. Na página **Resumo** da Nuvem Privada em **Informações Básicas,** copie o endereço IP do servidor DNS da Nuvem Privada.

    ![Servidores DNS privados em nuvem](media/private-cloud-dns-server.png)


Use qualquer uma dessas opções para a configuração dns.

* [Criar uma região no servidor DNS para *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Crie um encaminhador condicional no servidor DNS no local para resolver *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Criar uma região no servidor DNS para *.cloudsimple.io

Você pode configurar uma região como uma zona de stub e apontar para os servidores DNS na Nuvem Privada para resolução de nomes. Esta seção fornece informações sobre o uso de um servidor BIND DNS ou um servidor Microsoft Windows DNS.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Criar uma região em um servidor Bind DNS

O arquivo específico e os parâmetros a serem configurados podem variar de acordo com a configuração individual do DNS.

Por exemplo, para a configuração padrão do servidor BIND, edite /etc/named.conf arquivo no servidor DNS e adicione as seguintes informações da região.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Crie uma região em um servidor Microsoft Windows DNS

1. Clique com o botão direito do mouse no servidor DNS e selecione **Nova Região**. 
  
    ![Nova Zona](media/DNS01.png)
2. Selecione **'Zona de stub'** e clique **em Next**.

    ![Nova Zona](media/DNS02.png)
3. Selecione a opção apropriada dependendo do seu ambiente e clique **em Next**.

    ![Nova Zona](media/DNS03.png)
4. Selecione **Avançar na zona de procurar** e clique em **Avançar**.

    ![Nova Zona](media/DNS01.png)
5. Digite o nome da região e clique **em Next**.

    ![Nova Zona](media/DNS05.png)
6. Digite os endereços IP dos servidores DNS para sua Nuvem Privada que você obteve no portal CloudSimple.

    ![Nova Zona](media/DNS06.png)
7. Clique **em Next** conforme necessário para concluir a configuração do assistente.

## <a name="create-a-conditional-forwarder"></a>Crie um encaminhador condicional

Um encaminhador condicional encaminha todas as solicitações de resolução de nomes DNS para o servidor designado. Com esta configuração, qualquer solicitação de *.cloudsimple.io é encaminhada para os servidores DNS localizados na Nuvem Privada. Os exemplos a seguir mostram como configurar os encaminhadores em diferentes tipos de servidores DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Crie um encaminhador condicional em um servidor Bind DNS

O arquivo específico e os parâmetros a serem configurados podem variar de acordo com a configuração individual do DNS.

Por exemplo, para a configuração padrão do servidor BIND, edite /etc/named.conf arquivo no servidor DNS e adicione as seguintes informações de encaminhamento condicional.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Crie um encaminhador condicional em um servidor Microsoft Windows DNS

1. Abra o DNS Manager no servidor DNS.
2. Clique com o botão **direito do mouse em Encaminhadores Condicionais** e selecione a opção para adicionar um novo encaminhador condicional.

    ![Encaminhador condicional 1 Windows DNS](media/DNS08.png)
3. Digite o domínio DNS e o endereço IP dos servidores DNS na Nuvem Privada e clique em **OK**.
