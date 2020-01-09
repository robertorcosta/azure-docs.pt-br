---
title: Infraestrutura de Atualização do Red Hat | Microsoft Docs
description: Saiba mais sobre a Infraestrutura de Atualização do Red Hat para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 40b9bc0fb63e99d9cfb0b5447b5e4300af421aed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479430"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de Atualização do Red Hat para as VMs Red Hat Enterprise do Linux sob demanda no Azure
 A RHUI ([Infraestrutura de Atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure)) permite que os provedores de nuvem, como o Azure, espelhem o conteúdo do repositório hospedado pelo Red Hat, criem repositórios personalizados com conteúdo específico ao Azure e o disponibilizem para as VMs do usuário final.

As imagens PAYG (Pagas conforme o uso) do RHEL (Red Hat Enterprise Linux) vêm pré-configuradas para acessar o RHUI do Azure. Nenhuma configuração adicional é necessária. Para obter as atualizações mais recentes, execute `sudo yum update` depois que sua instância do RHEL estiver pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

Informações adicionais sobre imagens do RHEL no Azure, incluindo políticas de publicação e retenção, estão disponíveis [aqui](./redhat-images.md).

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o RHUI do Azure

* O Azure RHUI é a infraestrutura de atualização que dá suporte a todas as VMs PAYG do RHEL criadas no Azure. Isso não impede que você registre suas VMs PAYG RHEL com o Gerenciador de assinaturas ou satélite ou outra fonte de atualizações, mas fazer isso com uma VM PAYG resultará em uma cobrança dupla indireta. Consulte o seguinte ponto para obter detalhes.
* O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem PAYG do RHEL. Cancelar o registro de uma VM RHEL PAYG do RHUI hospedado no Azure não converte a máquina virtual em uma VM do tipo BYOL (traga sua própria licença). Se você registrar a mesma VM com outra origem de atualizações, você pode incorrer em encargos duplos _indiretos_. Você será cobrado pela primeira vez pela taxa de software RHEL do Azure. Você será cobrado pela segunda vez por assinaturas do Red Hat adquiridas anteriormente. Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere o registro para usar as [imagens do RHEL BYOS](https://aka.ms/rhel-byos).

* As imagens de PAYG do RHEL para SAP no Azure (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) são conectadas a canais de RHUI dedicados que permanecem na versão secundária específica do RHEL, conforme necessário para certificação SAP.

* O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se você estiver encaminhando por proxy todo o tráfego de VM por meio da infraestrutura de rede local, talvez você precise configurar rotas definidas pelo usuário para as VMs PAYG do RHEL para acessar o RHUI do Azure. Se esse for o caso, as rotas definidas pelo usuário precisarão ser adicionadas para _todos os_ endereços IP RHUI.

## <a name="image-update-behavior"></a>Comportamento de atualização de imagem

A partir de abril de 2019, o Azure oferece imagens RHEL que estão conectadas a repositórios EUS (suporte de atualização estendida) por padrão e imagens RHEL que são conectadas aos repositórios regulares (não EUS) por padrão. Mais detalhes sobre o RHEL EUS estão disponíveis na documentação do [ciclo de vida da versão](https://access.redhat.com/support/policy/updates/errata) do Red Hat e na [documentação do eus](https://access.redhat.com/articles/rhel-eus). O comportamento padrão de `sudo yum update` variará dependendo de qual imagem do RHEL você provisionou, como imagens diferentes estão conectadas a repositórios diferentes.

Para obter uma lista de imagens completa, execute `az vm image list --publisher redhat --all` usando o CLI do Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Imagens conectadas a repositórios não EUS

Se você provisionar uma VM de uma imagem RHEL que está conectada a repositórios não EUS, você será atualizado para a versão secundária mais recente do RHEL quando executar `sudo yum update`. Por exemplo, se você provisionar uma VM de uma imagem RHEL 7,4 PAYG e executar `sudo yum update`, você acabará com uma VM RHEL 7,7 (a versão secundária mais recente na família RHEL7).

As imagens que estão conectadas a repositórios não EUS não conterá um número de versão secundário na SKU. O SKU é o terceiro elemento no URN (nome completo da imagem). Por exemplo, todas as imagens a seguir são anexadas a repositórios não EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Observe que os SKUs são 7-LVM ou 7-RAW. A versão secundária é indicada na versão (quarto elemento no URN) dessas imagens.

### <a name="images-connected-to-eus-repositories"></a>Imagens conectadas a repositórios do EUS

Se você provisionar uma VM de uma imagem RHEL conectada a repositórios EUS, você não será atualizado para a versão secundária mais recente do RHEL quando executar `sudo yum update`. Isso ocorre porque as imagens conectadas a repositórios EUS também são bloqueadas por versão para sua versão secundária específica.

As imagens conectadas a repositórios EUS conterá um número de versão secundário na SKU. Por exemplo, todas as imagens a seguir são anexadas a repositórios EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>EUS de RHEL e bloqueio de versão de VMs do RHEL

Os repositórios de suporte de atualização estendida (EUS) estão disponíveis para clientes que desejam bloquear suas VMs RHEL para uma determinada versão secundária do RHEL após o provisionamento da VM. É possível bloquear a versão da VM do RHEL para uma versão secundária específica, atualizando os repositórios para apontar os repositórios do Suporte de Atualização Estendida. Você também pode desfazer a operação de bloqueio de versão do EUS.

>[!NOTE]
> Não há suporte para EUS em extras de RHEL. Isso significa que, se você estiver instalando um pacote que geralmente está disponível no canal de extras do RHEL, não poderá fazer isso enquanto estiver em EUS. O ciclo de vida do produto Red Hat extras é detalhado [aqui](https://access.redhat.com/support/policy/updates/extras/).

No momento da redação deste artigo, o suporte do EUS foi encerrado para o RHEL < = 7,4. Consulte a seção "Red Hat Enterprise Linux mais suporte a Complementos" na documentação do [Red Hat](https://access.redhat.com/support/policy/updates/errata/) para obter mais detalhes.
* O suporte a RHEL 7,4 EUS termina em 31 de agosto de 2019
* O suporte a RHEL 7,5 EUS termina em 30 de abril de 2020
* O suporte a RHEL 7,6 EUS termina em 31 de outubro de 2020
* O suporte a RHEL 7,7 EUS termina em 30 de agosto de 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Alternar uma VM RHEL para EUS (bloqueio de versão para uma versão secundária específica)
Use as instruções a seguir para bloquear uma VM RHEL para uma versão secundária específica (executar como raiz):

>[!NOTE]
> Isso se aplica somente a versões do RHEL para as quais o EUS está disponível. No momento da redação deste artigo, isso inclui o RHEL 7.2-7.7. Mais detalhes estão disponíveis na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

1. Desabilite repositórios não EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicione repositórios EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloquear a variável releasever (executar como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueará a versão secundária do RHEL para a versão secundária atual. Insira uma versão secundária específica, caso queira atualizar e bloquear para uma versão secundária posterior que não seja a mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará a versão do RHEL para RHEL 7.5

1. Atualizar a VM do RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Mudar uma VM RHEL de volta para não EUS (remover um bloqueio de versão)
Execute o seguinte como raiz:
1. Remova o arquivo releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Desabilitar EUS repositórios:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Configurar a VM RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Atualizar a VM do RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de distribuição de conteúdo do RHUI

O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/) e as regiões Microsoft Azure Alemanha e Governo dos EUA do Azure.

Se você estiver usando uma configuração de rede para restringir o acesso de VMs PAYG do RHEL, verifique se os seguintes IPs são permitidos para que `yum update` funcione dependendo do ambiente em que você está:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infraestrutura RHUI do Azure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualizar o certificado de cliente RHUI expirado em uma VM

Se você estiver usando uma imagem de VM RHEL mais antiga, por exemplo, RHEL 7.4 (URN de imagem: `RedHat:RHEL:7.4:7.4.2018010506`), terá problemas de conectividade ao RHUI devido a um certificado de cliente SSL expirado. O erro que você vê pode parecer _"o par SSL rejeitou seu certificado como expirado"_ ou _"erro: não é possível recuperar os metadados do repositório (repomd. xml) para o repositório:... Verifique seu caminho e tente novamente "_ . Para resolver esse problema, atualize o pacote do cliente de RHUI na VM usando o comando a seguir:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Como alternativa, a execução de `sudo yum update` também atualizará o pacote do certificado do cliente (dependendo da sua versão do RHEL), apesar dos erros de “certificado SSL expirado” que você verá em outros repositórios. Se a atualização for bem-sucedida, a conectividade normal a outros repositórios de RHUI deverá ser restaurada, portanto, será possível executar `sudo yum update` com êxito.

Se você encontrar um erro 404 ao executar um `yum update`, tente o seguinte para atualizar o cache yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Solução de problemas de conexão com o RHUI do Azure
Se você estiver tendo problemas para se conectar ao RHUI do Azure de sua VM PAYG do Azure RHEL, siga estas etapas:

1. Inspecione a configuração da VM para o ponto de extremidade do RHUI do Azure:

    1. Verifique se o arquivo `/etc/yum.repos.d/rh-cloud.repo` contém uma referência para `rhui-[1-3].microsoft.com` na `baseurl` da seção `[rhui-microsoft-azure-rhel*]` do arquivo. Se esse é o caso, significa que você está usando o novo RHUI do Azure.

    1. No entanto, se ele estiver apontando para um local com o padrão a seguir, será necessária uma atualização da configuração: `mirrorlist.*cds[1-4].cloudapp.net`. Você está usando o instantâneo de VM antigo e precisa atualizá-lo o para que ele aponte para o novo RHUI do Azure.

1. Acesso ao RHUI hospedado no Azure é limitado às VMs dentro dos [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Se estiver usando a nova configuração, verifique se a VM se conecta do intervalo de IP do Azure e, se ainda não puder se conectar ao RHUI do Azure, registre um caso de suporte na Microsoft ou no Red Hat.

### <a name="infrastructure-update"></a>Atualização de infraestrutura

Em setembro de 2016, implantamos uma RHUI atualizada do Azure. Em abril de 2017, desligamos o antigo RHUI do Azure. Se você usa as imagens PAYG do RHEL (ou seus instantâneos) de setembro de 2016 ou posterior, você está se conectando automaticamente ao novo RHUI do Azure. No entanto, se houver instantâneos mais antigos em suas VMs, você precisará atualizar manualmente a configuração deles para acessar o RHUI do Azure, conforme descrito na seção a seguir.

Os novos servidores do Azure RHUI são implantados com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). No Gerenciador de Tráfego, um único ponto de extremidade (rhui-1.microsoft.com) pode ser usado por qualquer VM, independentemente da região.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para usar os servidores do RHUI do Azure
Esse procedimento é fornecido apenas para referência. Imagens RHEL PAYG já tem a configuração correta para se conectar ao Azure RHUI. Para atualizar manualmente a configuração para usar os servidores de RHUI do Azure, conclua as seguintes etapas:

- Para RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Para RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Próximos passos
* Para criar uma VM do Red Hat Enterprise Linux por meio de uma imagem PAYG do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Para saber mais sobre as imagens de Red Hat no Azure, acesse a [página da documentação](./redhat-images.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
