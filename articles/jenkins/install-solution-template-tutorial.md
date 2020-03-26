---
title: Tutorial – Criar um servidor Jenkins no Azure
description: Neste tutorial, você instalará o Jenkins em uma máquina virtual Linux do Azure com base no modelo de solução do Jenkins e criará um aplicativo Java de exemplo.
keywords: jenkins, azure, devops, portal, máquina virtual, modelo de solução
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274525"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Tutorial: Criar um servidor Jenkins em uma VM do Linux do Azure 

Este tutorial mostra como instalar o [Jenkins](https://jenkins.io) em uma VM do Ubuntu Linux com as ferramentas e os plug-ins configurados para funcionar com o Azure. Ao concluir, você terá um servidor Jenkins em execução no Azure criando um aplicativo Java de exemplo do [GitHub](https://github.com).

> [!div class="checklist"]
> * Instalar e configurar um servidor Jenkins no Azure
> * Acessar o console do Jenkins usando um túnel SSH
> * Criar um projeto Freestyle
> * Compilar o código e empacotar o aplicativo de exemplo

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]