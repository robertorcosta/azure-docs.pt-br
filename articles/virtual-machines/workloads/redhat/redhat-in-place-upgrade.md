---
title: Atualização in-loco de Red Hat Enterprise Linux imagens no Azure
description: Localize as etapas para executar a atualização in-loco das imagens do Red Hat Enterprise 7. x para a versão mais recente do 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844550"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux atualizações in-loco

Este artigo fornece instruções passo a passo sobre como executar uma atualização in-loco do Red Hat Enterprise Linux 7 para Red Hat Enterprise Linux 8 usando o utilitário ' Leapp ' no Azure. Durante a atualização in-loco, o sistema operacional RHEL 7 existente é substituído pela versão RHEL 8.

>[!Note] 
> SQL Server em Red Hat Enterprise Linux ofertas não dão suporte à atualização in-loco no Azure.

## <a name="what-to-expect-during-the-upgrade"></a>O que esperar durante a atualização
O sistema reiniciará algumas vezes durante a atualização e isso é normal. A última reinicialização atualizará a VM para a versão secundária mais recente do RHEL 8. 

O processo de atualização pode levar de 20 minutos a algumas horas, isso depende de vários fatores, como o tamanho da VM e o número de pacotes instalados no sistema.

## <a name="preparations-for-the-upgrade"></a>Preparações para a atualização
A atualização in-loco é a maneira oficialmente recomendada pelo Red Hat e pelo Azure para permitir que os clientes atualizem o sistema para a próxima versão principal. Antes de executar a atualização aqui estão algumas coisas, você deve estar atento e levar em consideração. 

>[!Important] 
> Tire um instantâneo da imagem antes de executar a atualização.

>[!NOTE]
> Os comandos neste artigo precisam ser executados usando a conta raiz

* Verifique se você está usando a versão mais recente do RHEL 7, que atualmente é RHEL 7,9. Se você estiver usando uma versão bloqueada e não puder atualizar para o RHEL 7,9, poderá usar as [etapas aqui para alternar para um repositório não eus](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Execute o comando a seguir para saber como a atualização está em andamento e se ela será concluída. O comando deve gerar um arquivo em '/var/log/leapp/leapp-report.txt ' que explica o processo e o que está sendo feito e se a atualização é possível ou não
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Verifique se o console serial está funcional, pois isso permite o monitoramento durante o processo de atualização.

* Habilitar o acesso à raiz SSH no `/etc/ssh/sshd_config`
    1. Abra o arquivo `/etc/ssh/sshd_config`
    1. Pesquisar por ' #PermitRootLogin sim '
    1. Remova o ' # ' para remover o comentário

## <a name="steps-for-performing-the-upgrade"></a>Etapas para executar a atualização

Execute essas etapas com cuidado. Definitivamente, é recomendável tentar a atualização em um computador de teste antes das instâncias de produção.

1. Execute uma atualização do yum para buscar os pacotes de cliente mais recentes.
    ```bash
    yum update -y
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

1. Execute a atualização de ' Leapp '.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Depois que o `leapp upgrade` comando for concluído com êxito, reinicialize manualmente o sistema para concluir o processo. O sistema reiniciará algumas vezes durante a qual ele estará indisponível. Monitore o processo usando o console serial.

1.  Verifique se a atualização foi concluída com êxito.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Remova o acesso de SSH raiz após a conclusão da atualização.
    1. Abra o arquivo `/etc/ssh/sshd_config`
    1. Pesquisar por ' #PermitRootLogin sim '
    1. Adicionar ' # ' para comentar

1. Reinicie o serviço sshd para que as alterações entrem em vigor
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Problemas comuns
Essas são algumas das instâncias comuns que o ou o `leapp preupgrade` `leapp upgrade` processo pode falhar.

**Erro: nenhuma correspondência encontrada para os seguintes padrões de plug-in desabilitados**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Solução**\
Desabilite o plugin do Gerenciador de assinaturas editando o arquivo `/etc/yum/pluginconf.d/subscription-manager.conf` e alterando habilitado para `enabled=0` .

Isso é causado pelo plug-in yum do Gerenciador de assinatura que está sendo habilitado, que não é usado para VMs PAYG.

**Erro: possíveis problemas com o logon remoto usando a raiz** O `leapp preupgrade` pode falhar com o seguinte erro:
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**Solução**\
Habilite o acesso de raiz no `/etc/sshd_config` .
Isso é causado por não habilitar o acesso de SSH raiz no de acordo `/etc/sshd_config` com a seção "[preparações para a atualização](#preparations-for-the-upgrade)". 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [imagens do Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta BYOS do RHEL](./byos.md).
* As informações sobre os processos de atualização in-loco do Red Hat podem ser encontradas na documentação do Red Hat, [Atualizando do RHEL 7 para o RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
