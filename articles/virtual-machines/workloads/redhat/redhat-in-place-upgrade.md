---
title: Atualização in-loco de Red Hat Enterprise Linux imagens no Azure
description: Localize as etapas para executar a atualização in-loco das imagens do Red Hat Enterprise 7. x para a versão mais recente do 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447545"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux atualizações in-loco

Este artigo fornece instruções passo a passo sobre como executar uma atualização in-loco do Red Hat Enterprise Linux 7 para Red Hat Enterprise Linux 8 usando o utilitário ' Leapp ' no Azure. Durante a atualização in-loco, o sistema operacional RHEL 7 existente é substituído pela versão RHEL 8.

>[!Note] 
> SQL Server em Red Hat Enterprise Linux ofertas não dão suporte à atualização in-loco no Azure.

## <a name="what-to-expect-during-the-upgrade"></a>O que esperar durante a atualização
O sistema reiniciará algumas vezes durante a atualização e isso é normal. A última reinicialização atualizará a VM para a versão secundária mais recente do RHEL 8.

## <a name="preparations-for-the-upgrade"></a>Preparações para a atualização
As atualizações in-loco são a maneira oficialmente recomendada pelo Red Hat e pelo Azure para permitir que os clientes atualizem seu sistema para a próxima versão principal. Antes de executar a atualização aqui estão algumas coisas, você deve estar atento e levar em consideração. 

>[!Important] 
> Tire um instantâneo da imagem antes de executar a atualização.

>[!NOTE]
> Os comandos neste artigo precisam ser executados usando a conta raiz

* Verifique se você está usando a versão mais recente do RHEL 7, que atualmente é RHEL 7,9. Se você estiver usando uma versão bloqueada e não puder atualizar para o RHEL 7,9, poderá usar as [etapas aqui para alternar para um repositório não eus](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Execute o comando a seguir para saber como a atualização está em andamento e se ela será concluída. O comando deve gerar um arquivo em '/var/log/leapp/leapp-report.txt ' que explica o processo e o que está sendo feito e se a atualização é possível ou não
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Etapas para executar a atualização

Execute essas etapas com cuidado. Definitivamente, é recomendável tentar a atualização em um computador de teste antes das instâncias de produção.

1. Execute uma atualização do yum para buscar os pacotes de cliente mais recentes.
    ```bash
    yum update
    ```

1. Instale o leapp-Client-Package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Use o arquivo leapp-Data. tar. gz com repomap.csv e pes-events.jsem, presente no [portal do redhat](https://access.redhat.com/articles/3664871)e extraia-os. 
    1. Baixe o arquivo.
    1. Extraia o conteúdo e remova o arquivo usando o seguinte comando:
    ```bash
     tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```
    


1. Adicione o arquivo ' Answers ' para ' Leapp '.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ```
    
1. Habilitar PermitRootLogin no/etc/ssh/sshd_config
    1. Abra o arquivo/etc/ssh/sshd_config
    1. Pesquisar por ' #PermitRootLogin sim '
    1. Remova o ' # ' para remover o comentário



1. Execute a atualização de ' Leapp '.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Reinicie o serviço sshd para que as alterações entrem em vigor
    ```bash
    systemctl restart sshd
    ```
1. Comentar PermitRootLogin no/etc/ssh/sshd_config novamente
    1. Abra o arquivo/etc/ssh/sshd_config
    1. Pesquisar por ' #PermitRootLogin sim '
    1. Adicionar ' # ' para comentar

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [imagens do Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta BYOS do RHEL](./byos.md).
* As informações sobre os processos de atualização in-loco do Red Hat podem ser encontradas na documentação do Red Hat, [Atualizando do RHEL 7 para o RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).