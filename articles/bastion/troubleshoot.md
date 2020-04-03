---
title: Solução de problemas Azure Bastion | Microsoft Docs
description: Neste artigo, aprenda a solucionar problemas do Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619177"
---
# <a name="troubleshoot-azure-bastion"></a>Solucionar problemas do Azure Bastion

Este artigo mostra como solucionar problemas do Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Não é possível criar um NSG no AzureBastionSubnet

**Q:** Quando tento criar um NSG na sub-rede Azure Bastion, recebo o seguinte erro: *'Grupo de segurança de rede <NSG name> não tem regras necessárias para a sub-rede Azure Bastion AzureBastionSubnet"*.

**A:** Se você criar e aplicar um NSG no *AzureBastionSubnet,* certifique-se de ter adicionado as seguintes regras em seu NSG. Se você não adicionar essas regras, a criação/atualização do NSG falhará.

1. Controle de conectividade do avião – Entrada em 443 do GatewayManager
2. Registro de diagnósticos e outros – Saída em 443 para AzureCloud (tags regionais dentro desta tag de serviço ainda não são suportadas.)
3. Target VM – Saída para 3389 e 22 para VirtualNetwork

Um exemplo das regras do NSG está disponível para referência no [modelo quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Para obter mais informações, consulte [as orientações do NSG para o Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Não é possível usar minha chave SSH com o Azure Bastion

**Q:** Quando tento navegar no meu arquivo-chave SSH, recebo o seguinte erro: *'A tecla SSH Private deve começar com -----CHAVE PRIVADA BEGIN RSA----- e termina com -----CHAVE PRIVADA END RSA-----'*.

**A:** O Azure Bastion suporta apenas as chaves RSA SSH, neste momento. Certifique-se de navegar em um arquivo-chave que seja a chave privada RSA para SSH, com chave pública provisionada na VM de destino. 

Como exemplo, você pode usar o seguinte comando para criar uma nova chave RSA SSH:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

Saída:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Não é possível fazer login na minha máquina virtual com domínio do Windows

**Q:** Não consigo me conectar à minha máquina virtual do Windows que é acompanhada por domínio.

**A:** O Azure Bastion suporta o login de VM com adesão de domínio somente para login de domínio baseado em senha de usuário. Ao especificar as credenciais de domínio no portalusername@domainAzure, use o formato UPN ( ) em vez do formato *domain\username* para fazer login. Isso é suportado para máquinas virtuais com adesão de domínio ou híbrida (tanto a de domínio quanto de adesão ao Azure AD). Ele não é suportado para máquinas virtuais somente aazure ad..

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemas de transferência de arquivos

**Q:** A transferência de arquivos é suportada com o Azure Bastion?

**A:** A transferência de arquivos não é suportada no momento. Estamos trabalhando para adicionar apoio.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Tela preta no portal Azure

**Q:** Quando tento me conectar usando o Azure Bastion, recebo uma tela preta no portal Azure.

**A:** Isso acontece quando há um problema de conectividade de rede entre seu navegador da Web e o Azure Bastion (o firewall da Internet do seu cliente pode estar bloqueando o tráfego de WebSockets ou similares), ou entre o Azure Bastion e sua VM alvo. A maioria dos casos inclui um NSG aplicado no AzureBastionSubnet ou na sua sub-rede VM de destino que está bloqueando o tráfego RDP/SSH em sua rede virtual. Permita tráfego de WebSockets no firewall da internet do cliente e verifique os NSGs na sua sub-rede VM de destino.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Bastion FAQ](bastion-faq.md).