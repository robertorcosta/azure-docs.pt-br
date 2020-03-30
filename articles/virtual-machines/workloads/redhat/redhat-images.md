---
title: Imagens do Red Hat Enterprise Linux no Azure | Microsoft Docs
description: Conheça as imagens do Red Hat Enterprise Linux no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239873"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Visão geral das imagens do Red Hat Enterprise Linux

Este artigo descreve imagens disponíveis do Red Hat Enterprise Linux (RHEL) no Azure Marketplace e políticas em torno de sua nomeação e retenção.

Para obter informações sobre as políticas de suporte da Red Hat para todas as versões do RHEL, consulte o ciclo de vida do [Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata). Para obter detalhes sobre preços, consulte [a calculadora de preços do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> As imagens RHEL atualmente disponíveis no Azure Marketplace suportam modelos de licenciamento (BYOS) ou de licenciamento pay-as-you-go. O [Benefício de Uso Híbrido do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) e a troca dinâmica entre o BYOS e o licenciamento pay-as-you-go não são suportados. Para alternar o modo de licenciamento, você deve reimplantar a VM a partir da imagem correspondente.

>[!NOTE]
> Para qualquer problema relacionado a imagens RHEL no Azure Marketplace, registre um bilhete de suporte com a Microsoft.

## <a name="view-images-available-in-azure"></a>Ver imagens disponíveis no Azure

Quando você procura por "Red Hat" no Azure Marketplace ou quando você cria um recurso no portal Azure UI, você verá apenas um subconjunto de todas as imagens RHEL disponíveis. Você sempre pode obter o conjunto completo de imagens VM disponíveis usando o Azure CLI, PowerShell e API.

Para ver o conjunto completo de imagens disponíveis do Red Hat no Azure, execute o seguinte comando:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenção de nomenclatura

As imagens de VM no Azure são organizadas pelo editor, oferta, SKU e versão. A combinação de Publisher:Offer:SKU:Version é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:8-LVM:8.1.20200318` refere-se a uma imagem particionada RHEL 8.1 LVM construída em 18 de março de 2020.

Uma amostra de como criar um RHEL 8.1 VM é mostrada aqui.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"

A API Azure REST permite o uso do apelido "mais recente" para a versão em vez da versão específica. Usando as disposições "mais recentes" a imagem mais recente disponível para o editor, oferta e SKU dado.

Por exemplo, `RedHat:RHEL:8-LVM:latest` refere-se à imagem mais recente da família RHEL 8 com partição LVM disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a versão mais recente segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Esta comparação de versão de imagem é feita comparando os valores como um objeto [de versão,](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) não como uma string.

## <a name="rhel-6-image-types"></a>RHEL 6 tipos de imagem

Para imagens RHEL 6.x, os tipos de imagem são mostrados na tabela a seguir.

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 6.9) | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 6.9.2018010506) | Todas as imagens padrão RHEL 6.x seguem esta convenção.
|RedHat | rhel-byos | rhel-raw69 | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 6.9.20181023) | Esta imagem é uma imagem RHEL 6.9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 6.8.2017053118) | Esta imagem é uma imagem RHEL 6.8 para Aplicações SAP. Ele tem o direito de acessar repositórios de aplicativos SAP e repositórios base RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 6.7.2017053121) | Esta imagem é uma imagem RHEL 6.7 para imagem SAP HANA. Ele tem o direito de acessar repositórios SAP HANA e repositórios base RHEL.

## <a name="rhel-7-image-types"></a>RHEL 7 tipos de imagem

Para imagens RHEL 7.x, existem alguns tipos de imagens diferentes. A tabela a seguir mostra os diferentes conjuntos de imagens que oferecemos. Para ver uma lista completa, use `az vm image list --publisher redhat --all`o comando Azure CLI .

>[!NOTE]
> Salvo indicação em contrário, todas as imagens são particionadas por LVM e conectadas a repositórios RHEL regulares. Ou seja, os repositórios não são o Suporte de Atualização Estendida (EUS) e não são Serviços de Atualização para SAP (E4S). Daqui para frente, estamos passando a publicar apenas imagens particionadas pela LVM, mas estamos abertos a comentários sobre essa decisão. Para obter mais informações sobre serviços de atualização estendida e serviços de atualização para SAP, consulte [o ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 7.6) | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019102813) | As imagens publicadas antes de abril de 2019 são anexadas aos repositórios padrão do RHEL. As imagens publicadas após abril de 2019 são anexadas aos repositórios EUS da Red Hat para permitir o bloqueio de versão de uma versão menor específica. Os clientes que desejam repositórios regulares devem usar as imagens que contêm 7-LVM ou 7-RAW no valor SKU (detalhes a seguir). RHEL 7.7 e imagens posteriores são particionadas por LVM. Todas as outras imagens nesta categoria são particionadas cruas.
|RedHat | RHEL | 7-RAW | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019102813) | Essas imagens são particionadas cruas (por exemplo, nenhum volume lógico foi adicionado).
|RedHat | RHEL | 7-RAW-CI | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019072418) | Essas imagens são particionadas cruas (por exemplo, nenhum volume lógico foi adicionado) e usam cloud-init para provisionamento.
|RedHat | RHEL | 7-LVM | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019062414) | Estas imagens são particionadas por LVM.
|RedHat | rhel-byos | rhel-{lvm,raw} | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.7.20190819) | Estas imagens são as imagens RHEL 7 BYOS. Eles não estão ligados a nenhum repositório e não cobram a taxa de prêmio RHEL. Se você estiver interessado nas imagens RHEL BYOS, [solicite acesso](https://aka.ms/rhel-byos). Os valores SKU terminam com a versão menor e denotam se a imagem é crua ou particionada por LVM. Por exemplo, um valor SKU de rhel-lvm77 indica uma imagem RHEL 7.7 particionada por LVM.
|RedHat | RHEL | RHEL-SAP | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019071300) | Essas imagens são RHEL para imagens SAP. Eles têm o direito de acessar os repositórios SAP HANA e Applications, bem como repositórios RHEL E4S. O faturamento inclui o prêmio RHEL e o prêmio SAP em cima da taxa de computação base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019062320) | Essas imagens são RHEL para SAP com imagens de alta disponibilidade e serviços de atualização. Eles têm o direito de acessar os repositórios SAP HANA e Applications e os repositórios de alta disponibilidade, bem como repositórios RHEL E4S. O faturamento inclui o prêmio RHEL, o prêmio SAP e o premium de alta disponibilidade em cima da taxa de computação base.
|RedHat | RHEL | RHEL-HA | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.6.2019062019) | Essas imagens são imagens RHEL que também têm direito a acessar o complemento de alta disponibilidade. Eles cobram um pouco mais em cima da taxa de cálculo rhel e base devido ao prêmio adicional de alta disponibilidade.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.3.2017053118) | Essas imagens estão desatualizadas porque os aplicativos SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Essas imagens são RHEL para imagens de aplicativos SAP. Eles têm o direito de acessar repositórios de aplicativos SAP e repositórios rhel base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 7.3.2018051421) | Essas imagens estão desatualizadas porque os aplicativos SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estas imagens são RHEL para imagens SAP HANA. Eles têm o direito de acessar repositórios SAP HANA e repositórios base RHEL.

## <a name="rhel-8-image-types"></a>RHEL 8 tipos de imagem

Os detalhes para rhel 8 tipos de imagem estão abaixo.

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 8.0.20191023) | Essas imagens são imagens partitadas por RHEL 8 LVM conectadas aos repositórios padrão do Red Hat.
|RedHat | RHEL | 8-gen2 | Valores concatenadados da versão menor RHEL e da data publicada (por exemplo, 8.0.20191024) | Essas imagens são imagens com partição hyper-v geração 2 RHEL 8 LVM conectadas aos repositórios padrão do Red Hat. Para obter mais informações sobre as VMs da Geração 2 no Azure, consulte [suporte para VMs de geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="rhel-longer-support-add-ons"></a>RHEL mais longos suporte a complementos

### <a name="extended-update-support"></a>Suporte de atualização estendida

A partir de abril de 2019, estão disponíveis imagens RHEL que são anexadas aos repositórios da EUS por padrão. Mais informações sobre o RHEL EUS estão disponíveis na [documentação da Red Hat.](https://access.redhat.com/articles/rhel-eus)

A mudança para os repositórios da EUS é possível e é suportada. Para obter instruções sobre como mudar sua VM para EUS e mais informações sobre datas de fim de vida útil da EUS, consulte [RHEL EUS e VMs RHEL de bloqueio de versão](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> A EUS não é suportada no RHEL Extras. Isso significa que se você instalar um pacote que geralmente está disponível no canal RHEL Extras, você não poderá fazê-lo enquanto estiver no EUS. Para obter mais informações sobre o ciclo de vida do produto Red Hat Extras, consulte [o ciclo de vida do Red Hat Enterprise Linux Extras](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Diferenciar entre imagens regulares e eus

Os clientes que desejam usar imagens anexadas aos repositórios EUS devem usar a imagem RHEL que contém um número de versão menor RHEL no SKU.

Por exemplo, você pode ver as seguintes duas imagens RHEL 7.4 disponíveis.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

Neste caso, `RedHat:RHEL:7.6:7.6.2019102813` é anexado aos repositórios da EUS por padrão. O valor de SKU é 7.4. E `RedHat:RHEL:7-LVM:7.6.2019062414` é anexado a repositórios não-EUS por padrão. O valor sku é de 7-LVM.

Para usar repositórios regulares (não-EUS), use uma imagem que não contenha um número de versão menor no SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS

As informações na tabela a seguir se aplicam a imagens RHEL conectadas aos repositórios eus.

>[!NOTE]
> No momento da redação, apenas as versões RHEL 7.4 e posteriores menores têm suporte à EUS. A UES não é mais suportada para rhel <=7.3.
>
> Para obter mais informações sobre a disponibilidade do RHEL EUS, consulte [o ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

Versão secundária |Exemplo de imagem da EUS              |Status da UES                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | As imagens publicadas abril 2019 e posteriormente são EUS por padrão.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | As imagens publicadas em junho de 2019 e posteriores são eus por padrão. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | As imagens publicadas em maio de 2019 e posteriores são eus por padrão. |
RHEL 8.0      |N/D                            | Nenhum EUS está disponível na Red Hat.                               |

### <a name="update-services-for-sap"></a>Serviços de atualização para SAP

As mais recentes imagens RHEL para SAP serão conectadas aos Serviços de Atualização para assinaturas SAP Solutions (E4S). Para obter mais informações sobre o E4S, consulte a [documentação](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)do Red Hat .

#### <a name="rhel-images-with-e4s"></a>Imagens RHEL com E4S

As imagens das seguintes ofertas criadas após dezembro de 2019 estão conectadas aos repositórios E4S:

* RHEL-SAP (RHEL para SAP)
* RHEL-SAP-HA (RHEL para SAP com serviços de alta disponibilidade e atualização)

## <a name="other-available-offers-and-skus"></a>Outras ofertas disponíveis e SKUs

A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais além do que está listado na tabela anterior. Um exemplo é `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Essas ofertas podem ser usadas para fornecer suporte para soluções específicas de marketplace. Ou podem ser publicados para pré-visualizações e testes. Eles podem ser trocados ou removidos a qualquer momento sem aviso. Não os use a menos que sua presença seja documentada publicamente pela Microsoft ou pela Red Hat.

## <a name="publishing-policy"></a>Política de publicação

As imagens de atualização da Microsoft e da Red Hat à medida que novas versões menores são lançadas, conforme necessário para lidar com vulnerabilidades e exposições comuns específicas (CVEs) ou para alterações ou atualizações ocasionais de configuração. Nós nos esforçamos para fornecer imagens atualizadas o mais rápido possível dentro de três dias úteis após o lançamento ou disponibilidade de uma correção CVE.

Atualizamos apenas a versão menor atual em uma determinada família de imagens. Com o lançamento de uma versão secundária mais recente, podemos parar de atualizar a versão secundária mais antiga. Por exemplo, com a liberação do RHEL 7.6, as imagens RHEL 7.5 não são mais atualizadas.

>[!NOTE]
> As VMs ativas do Azure provisionadas a partir de imagens rhel pay-as-you-go estão conectadas ao Azure RHUI e podem receber atualizações e correções assim que forem lançadas pela Red Hat e replicadas para o Azure RHUI. O tempo geralmente é menos de 24 horas após o lançamento oficial pela Red Hat. Essas VMs não exigem uma nova imagem publicada para obter as atualizações. Os clientes têm controle total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem

A política atual é manter todas as imagens publicadas anteriormente. Nós nos reservamos ao direito de remover as imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido a atualizações subseqüentes da plataforma ou componentepodem ser removidas. As imagens que podem ser removidas seguem a política atual do Azure Marketplace para fornecer notificações até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Próximas etapas

* Para ver a lista completa de imagens RHEL no Azure, consulte [imagens Red Hat Enterprise Linux (RHEL) disponíveis no Azure](./redhat-imagelist.md).
* Para saber mais sobre a infra-estrutura de atualização do Chapéu Vermelho, consulte [Red Hat Update Infrastructure for on-demand RHEL VMs in Azure](https://aka.ms/rhui-update).
* Para saber mais sobre a oferta rhel BYOS, consulte [Red Hat Enterprise Linux trazer sua própria assinatura Gold Images no Azure](./byos.md).
* Para obter informações sobre as políticas de suporte da Red Hat para todas as versões do RHEL, consulte o ciclo de vida do [Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
