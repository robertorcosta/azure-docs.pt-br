---
title: Imagens do Red Hat Enterprise Linux no Azure | Microsoft Docs
description: Saiba mais sobre imagens do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 211ac68fd10cd745faf68a5efae7392345008d7b
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941457"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Visão geral de imagens de Red Hat Enterprise Linux
Este artigo descreve as imagens de Red Hat Enterprise Linux (RHEL) disponíveis no Azure Marketplace, juntamente com as políticas em torno de seus nomes e retenção.

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata). Para obter detalhes de preços, visite a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Atualmente, as imagens RHEL disponíveis no Azure Marketplace dão suporte aos modelos de licenciamento BYOS (Bring-Your-Own-Subscription) ou PAYG (Pagamento Conforme o Uso). Não há suporte para o [Benefício de Uso Híbrido do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) a troca dinâmica entre BYOS e PAYG. O modo de licenciamento de troca exige a reimplantação da VM com base na imagem correspondente.

>[!NOTE]
> Para qualquer problema relacionado a imagens RHEL no Azure Marketplace, registre um tíquete de suporte com a Microsoft.

## <a name="images-available-in-azure"></a>Imagens disponíveis no Azure
Ao Pesquisar "Red Hat" no Marketplace ou ao criar um recurso na interface do usuário portal do Azure, você verá apenas um subconjunto de todas as imagens RHEL disponíveis. Você sempre pode obter o conjunto completo de imagens VM disponíveis usando a CLI/PowerShell/API do Azure.

Para ver o conjunto completo de imagens disponíveis do Red Hat no Azure, execute o seguinte comando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenção de nomenclatura
Imagens de VM no Azure são organizadas por Editor, oferta, SKU e versão. A combinação de Publisher:Offer:SKU:Version é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:7-LVM:7.6.2018103108` se refere a uma imagem RHEL 7,6 LVM particionada criada em 31 de outubro de 2018.

Abaixo está um exemplo de como criar uma VM do RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"
A API REST do Azure permite o uso do moniker "mais recente" para a versão em vez da versão específica. Usar "mais recente" provisionará a imagem mais recente disponível para determinado publicador, oferta e SKU.

Por exemplo, `RedHat:RHEL:7-LVM:latest` refere-se à imagem mais recente do LVM de família RHEL 7 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a versão mais recente segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="rhel-6-image-types"></a>Tipos de imagem RHEL 6
Para imagens RHEL 6. x, os tipos de imagem são os seguintes:

|Publicador | Oferta | Valor de SKU | Versão | Detalhes
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versão secundária (por exemplo, 6,9) | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.9.2018010506) | Todas as imagens padrão RHEL 6. x seguem essa Convenção
|RedHat | RHEL-BYOS | RHEL-raw69 | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.9.20181023) | Esta imagem é uma imagem RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.8.2017053118) | Esta é uma imagem RHEL 6,8 para aplicativos SAP. Ele tem o direito de acessar repositórios de aplicativos SAP, bem como de repositórios de RHEL base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.7.2017053121) | Esta é uma imagem RHEL 6,7 para SAP HANA. Ele tem o direito de acessar repositórios SAP HANA, bem como de repositórios de RHEL base.

### <a name="rhel-7-image-types"></a>Tipos de imagem RHEL 7
Para imagens RHEL 7. x, há alguns tipos de imagens diferentes. A tabela a seguir mostra os diferentes conjuntos de imagens que oferecemos. Uma lista completa pode ser exibida com o comando AZ CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> Salvo indicação em contrário, todas as imagens são particionadas pelo LVM e se conectarão a repositórios RHEL regulares (ou seja, não EUS, não E4S). No futuro, estamos migrando para publicar apenas imagens de particionamento LVM, mas estão abertas para comentários sobre essa decisão. Detalhes sobre o suporte de atualização estendida e os serviços de atualização para SAP estão disponíveis na [página ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Publicador | Oferta | Valor de SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versão secundária (por exemplo, 7,6) | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019102813) | As imagens publicadas antes de abril de 2019 serão anexadas aos repositórios padrão do RHEL. As imagens publicadas após abril de 2019 serão anexadas aos repositórios do EUS (suporte estendido de atualização) do Red Hat para permitir o bloqueio de versão de uma versão secundária específica. Os clientes que desejam repositórios regulares devem usar as imagens que contêm 7-LVM ou 7-RAW no valor de SKU (detalhes abaixo). RHEL 7,7 e imagens posteriores serão particionadas pelo LVM. Todas as outras imagens nesta categoria são de particionamento bruto.
|RedHat | RHEL | 7-RAW | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019102813) | Essas imagens são particionadas em formato bruto (ou seja, nenhum volume lógico foi adicionado).
|RedHat | RHEL | 7-RAW-CI | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019072418) | Essas imagens são particionadas de forma bruta (ou seja, nenhum volume lógico foi adicionado) e usará Cloud-init para provisionamento.
|RedHat | RHEL | 7-LVM | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019062414) | Essas imagens são particionadas em LVM.
|RedHat | RHEL-BYOS | RHEL-{LVM, RAW} | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.7.20190819) | Essas imagens são as imagens do RHEL 7 BYOS. Eles não são anexados a nenhum repositório e não cobram a taxa Premium do RHEL. Se você estiver interessado nas imagens do RHEL BYOS, [solicite o acesso](https://aka.ms/rhel-byos). Os valores de SKU terminam com a versão secundária e denotam se a imagem é RAW ou com particionamento de LVM. Por exemplo, um valor de SKU de RHEL-lvm77 indica uma imagem de RHEL 7,7 particionada por LVM.
|RedHat | RHEL | RHEL-SAP | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019071300) | Essas imagens são RHEL for SAP images. Eles têm o direito de acessar os repositórios de SAP HANA e de aplicativos, bem como repositórios E4S do RHEL. A cobrança inclui o RHEL Premium e o SAP Premium sobre a taxa de computação base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019062320) | Essas imagens são RHEL for SAP com alta disponibilidade e imagens de serviços de atualização. Eles têm o direito de acessar os repositórios de SAP HANA e de aplicativos e os repositórios de alta disponibilidade, bem como os repositórios de E4S RHEL. A cobrança inclui o RHEL Premium, o SAP Premium e o HA Premium acima da taxa de computação base.
|RedHat | RHEL | RHEL-HA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019062019) | Essas são imagens RHEL que também estão qualificadas para acessar o complemento de alta disponibilidade. Eles cobrarão um pouco mais sobre o RHEL e a taxa de computação base devido ao complemento de alta disponibilidade Premium.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.3.2017053118) | Essas imagens estão desatualizadas, pois os aplicativos SAP e SAP HANA repositórios foram combinados nos repositórios SAP. Essas são as imagens do RHEL for SAP Applications. Eles têm o direito de acessar repositórios de aplicativos SAP, bem como repositórios de RHEL base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.3.2018051421) | Essas imagens estão desatualizadas, pois os aplicativos SAP e SAP HANA repositórios foram combinados nos repositórios SAP. Essas são RHEL for SAP HANA imagens. Eles têm o direito de acessar repositórios SAP HANA, bem como de repositórios de RHEL base.

### <a name="rhel-8-image-types"></a>Tipos de imagem RHEL 8
Os detalhes dos tipos de imagem RHEL 8 estão abaixo.

|Publicador | Oferta | Valor de SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 8.0.20191023) | Essas imagens são imagens RHEL 8,0 com particionamento de LVM conectadas a repositórios Red Hat padrão.
|RedHat | RHEL | 8-Gen2 | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 8.0.20191024) | Essas imagens são imagens do Hyper-V geração 2 RHEL 8,0 LVM-partições conectadas a repositórios Red Hat padrão. Mais informações sobre VMs de geração 2 no Azure estão disponíveis [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="extended-update-support-eus"></a>Suporte de atualização estendida (EUS)
A partir de abril de 2019, as imagens RHEL estão disponíveis que são anexadas aos repositórios EUS (suporte de atualização estendida) por padrão. Mais detalhes sobre o RHEL EUS estão disponíveis na [documentação do Red Hat](https://access.redhat.com/articles/rhel-eus).

É possível alternar para repositórios EUS e tem suporte. Instruções sobre como mudar sua VM para EUS e mais detalhes sobre as datas de fim de suporte do EUS estão disponíveis [aqui](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> Não há suporte para EUS em extras de RHEL. Isso significa que, se você estiver instalando um pacote que geralmente está disponível no canal de extras do RHEL, não poderá fazer isso enquanto estiver em EUS. O ciclo de vida do produto Red Hat extras é detalhado [aqui](https://access.redhat.com/support/policy/updates/extras/).

### <a name="differentiating-between-regular-and-eus-images"></a>Diferenciando imagens regulares e EUSs.
Os clientes que desejam usar imagens anexadas a repositórios EUS devem usar a imagem RHEL que contém um número de versão secundária do RHEL no SKU.

Por exemplo, você pode ver as duas seguintes imagens RHEL 7,4 disponíveis:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
Nesse caso, os `RedHat:RHEL:7.6:7.6.2019102813` serão anexados aos repositórios do EUS por padrão (valor de SKU de 7,4) e `RedHat:RHEL:7-LVM:7.6.2019062414` serão anexados a repositórios não EUS por padrão (valor de SKU de 7-LVM).

Se você quiser usar repositórios regulares (não EUS), implante usando uma imagem que não contenha um número de versão secundário na SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS
A tabela a seguir será aplicada a imagens RHEL conectadas a repositórios do EUS.

>[!NOTE]
> No momento da gravação, apenas RHEL 7,4 e versões secundárias posteriores têm suporte EUS. EUS não tem mais suporte para RHEL < = 7,3.
>
> Mais detalhes sobre a disponibilidade EUS do RHEL podem ser encontrados [aqui](https://access.redhat.com/support/policy/updates/errata).

Versão secundária |Exemplo de imagem EUS              |Status do EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | As imagens publicadas em abril de 2019 e posteriores serão EUS por padrão|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | As imagens publicadas em junho de 2019 e posteriores serão EUS por padrão |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | As imagens publicadas podem ser 2019 e posteriores serão EUS por padrão  |
RHEL 8,0      |N/D                            | Nenhum EUS disponível no Red Hat                               |





### <a name="other-available-offers-and-skus"></a>Outras SKUs e ofertas disponíveis
A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais além do que está listado na tabela acima, por exemplo, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Essas ofertas podem ser usadas para fornecer suporte a soluções específicas do marketplace, ou devem ser publicadas para fins de teste e visualizações. Podem ser alteradas ou removidas a qualquer momento sem aviso. Não use, a menos que sua presença foi documentada publicamente pela Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Política de publicação
A Microsoft e Red Hat atualizam as imagens quando novas versões secundárias são lançadas, conforme necessário para resolver CVEs específicos ou para alterações/atualizações ocasionais de configuração. Nos esforçamos para fornecer imagens atualizadas assim que possível, em três dias úteis após uma versão ou uma correção CVE.

Podemos atualizar somente a versão secundária atual em uma família de determinada imagem. Com o lançamento de uma versão secundária mais recente, podemos parar de atualizar a versão secundária mais antiga. Por exemplo, com o lançamento do RHEL 7.6, imagens de RHEL 7.5 não serão atualizadas.

>[!NOTE]
> As máquinas virtuais do Active Azure provisionadas das imagens com pagamento Conforme o Uso são conectadas ao RHUI do Azure e podem receber atualizações e correções assim que forem liberadas pela Red Hat e replicadas para o RHUI do Azure (geralmente em menos de 24 horas após a liberação oficial pela Red Hat). Essas VMs não exigem uma nova imagem publicada para obter as atualizações e os clientes têm controle total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem
Nossa política atual é manter todas as imagens publicadas anteriormente. Nós nos reservamos ao direito de remover as imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido à plataforma subsequente ou atualizações de componentes, podem ser removidas. Imagens que podem ser removidas seguirão a política atual do Marketplace para fornecer notificações de até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Próximos passos
* Exiba a lista completa de [imagens RHEL no Azure](./redhat-imagelist.md).
* Saiba mais sobre a Infraestrutura de atualização do Red Hat do Azure [aqui](https://aka.ms/rhui-update).
* Saiba mais sobre a [oferta BYOS do RHEL](./byos.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
