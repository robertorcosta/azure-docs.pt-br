---
title: Azure HANA Large Instances controle através do portal Azure | Microsoft Docs
description: Descreve como você pode identificar e interagir com o Azure HANA Large Instances através do portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099808"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controle do HANA em Instâncias Grandes do Azure por meio do portal do Azure
Este documento abrange a forma como as [Grandes Instâncias do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) são apresentadas no [portal Azure](https://portal.azure.com) e quais atividades podem ser conduzidas através do portal Azure com unidades HANA Large Instance que são implantadas para você. A visibilidade do HANA Large Instances no portal Azure é fornecida através de um provedor de recursos do Azure para HANA Large Instances, que atualmente está em visualização pública

## <a name="register-hana-large-instance-resource-provider"></a>Registre o provedor de recursos hana de instância grande
Normalmente, sua assinatura do Azure que você estava usando para implantações hana large instance é registrada para o Provedor de recursos hana de instância grande. No entanto, se você não puder ver que implantou unidades HANA Large Instance, você deve registrar o Provedor de recursos em sua assinatura do Azure. Existem duas maneiras de registrar o provedor de recursos hana de instância grande

### <a name="register-through-cli-interface"></a>Registre-se através da interface da CLI
Você precisa estar logado na sua assinatura do Azure, usada para a implantação da Grande Instância do HANA através da interface CLI do Azure. Você pode (re-)registrar o provedor de instâncias de grande instância hana com este comando:
    
    az provider register --namespace Microsoft.HanaOnAzure

Para obter mais informações, consulte o artigo [Provedores e tipos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registre-se através do portal Azure
Você pode (re-)registrar o provedor de recursos hana instância grande através do portal Azure. Você precisa listar sua assinatura no portal Azure e clicar duas vezes na assinatura, que foi usada para implantar sua unidade HANA Large Instance(s). Um que você está na página de visão geral da sua assinatura, selecione "Provedores de recursos" como mostrado abaixo e digite "HANA" na janela de pesquisa. 

![Registre hli RP através do portal Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Na captura de tela mostrada, o provedor de recursos já estava registrado. Caso o provedor de recursos ainda não esteja registrado, pressione "recadastramento" ou "registro".

Para obter mais informações, consulte o artigo [Provedores e tipos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Exibição de unidades hana de grande instância no portal Azure
Ao enviar uma solicitação de implantação hana de grande instância, você é solicitado a especificar a assinatura do Azure que você está conectando às instâncias grandes do HANA também. Recomenda-se usar a mesma assinatura que você está usando para implantar a camada de aplicativo SAP que funciona contra as unidades HANA Large Instance.
À medida que suas primeiras instâncias hana grandes estão sendo implantadas, um novo [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) é criado na assinatura do Azure que você enviou na solicitação de implantação para o (s) HANA Large Instance(s).  O novo grupo de recursos listará todas as suas unidades HANA Large Instance que você implantou na assinatura específica.

Para encontrar o novo grupo de recursos do Azure, você lista o grupo de recursos em sua assinatura navegando pelo painel de navegação esquerdo do portal Azure

![Painel de navegação no portal Azure](./media/hana-li-portal/portal-resource-group.png)

Na lista de grupos de recursos, você está sendo listado, você pode precisar filtrar na assinatura que você usou para ter HANA Grandes Instâncias implantadas

![Filtrar grupos de recursos no portal Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Depois de filtrar para a assinatura correta, você ainda pode ter uma longa lista de grupos de recursos. Procure por um com uma pós-correção de **-Txxx** onde "xxx" são três dígitos, como **-T050**. 

Ao encontrar o grupo de recursos, liste os detalhes dele. A lista que você recebeu pode parecer:

![Lista HLI no portal Azure](./media/hana-li-portal/portal-hli-units-list.png)

Todas as unidades listadas representam uma única unidade HANA Large Instance que foi implantada em sua assinatura. Neste caso, você olha para oito unidades diferentes hana grande instância, que foram implantados em sua assinatura.

Se você implantou vários inquilinos hana grande instância sob a mesma assinatura do Azure, você encontrará vários grupos de recursos do Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Veja os atributos de uma única Unidade HLI
Na lista das unidades HANA Large Instance, você pode clicar em uma única unidade e chegar aos detalhes da única unidade HANA Large Instance. 

Na tela de visão geral, depois de clicar em 'Mostrar mais', você está recebendo uma apresentação da unidade, que se parece com:

![Mostrar visão geral de uma unidade HLI](./media/hana-li-portal/portal-show-overview.png)

Olhando para os diferentes atributos mostrados, esses atributos parecem pouco diferentes dos atributos Do Zure VM. No cabeçalho do lado esquerdo, ele mostra o grupo Resource, região do Azure, nome de assinatura e ID, bem como algumas tags que você adicionou. Por padrão, as unidades HANA Large Instance não têm nenhuma tag atribuída. No lado direito do cabeçalho, o nome da unidade é listado como atribuído quando a implantação foi feita. O sistema operacional é mostrado, bem como o endereço IP. Como acontece com as VMs, o tipo de unidade hana de ocorrência grande com o número de threads e memória da CPU também é mostrado. Mais detalhes sobre as diferentes unidades hana large instance, são mostrados aqui:

- [SKUs disponíveis para HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Dados adicionais no lado inferior direito é a revisão do selo HANA Large Instance. Os valores possíveis são:

- Revisão 3
- Revisão 4

A Revisão 4 é a mais recente arquitetura lançada do HANA Large Instances com grandes melhorias na latência da rede entre As VMs Azure e as unidades HANA De grande instância implantadas em selos ou linhas de Revisão 4.
Outra informação muito importante é encontrada no canto inferior direito da visão geral com o nome do Grupo de Colocação de Proximidade do Azure que é criado automaticamente para cada unidade HANA Large Instance implantada. Esse Grupo de Colocação de Proximidade precisa ser referenciado ao implantar as VMs do Azure que hospedam a camada de aplicativo SAP. Usando o [grupo de colocação de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) associado à unidade HANA Large Instance, certifique-se de que as VMs do Azure sejam implantadas nas proximidades da unidade HANA Large Instance. A forma como grupos de colocação de proximidade podem ser usados para localizar a camada de aplicativo SAP no mesmo datacenter do Azure como as unidades HANA Large Instance hospedadas da Revisão 4 é descrita em [Grupos de Colocação de Proximidade do Azure para obter latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).

Um campo adicional na coluna direita do cabeçalho informa sobre o estado de potência da unidade hana de ocorrência grande.

> [!NOTE]
> O estado de potência descreve se a unidade de hardware está desligada ou desligada. Ele não dá informações sobre o sistema operacional estar em funcionamento. Ao reiniciar uma unidade HANA Large Instance, você experimentará um pequeno momento em que o estado da unidade muda para **Começar** a se mover para o estado de **Started**. Estar no estado de **Started** significa que o Sistema Operacional está sendo iniciado ou que o Sistema Operacional foi completamente iniciado. Como resultado, após a reinicialização da unidade, você não pode esperar entrar imediatamente na unidade assim que o estado mudar para **Started**.
> 

Se você pressionar 'Ver mais', informações adicionais serão mostradas. Uma informação adicional é a exibição da revisão do selo HANA Large Instance, a unidade foi implantada. Veja o artigo [O que é SAP HANA no Azure (Grandes Instâncias)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para as diferentes revisões dos selos HANA Large Instance

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Verifique as atividades de uma única unidade HANA Large Instance 
Além de dar uma visão geral das unidades hana grande instância, você pode verificar as atividades da unidade em particular. Um registro de atividades pode parecer:

![Painel de navegação no portal Azure](./media/hana-li-portal/portal-activity-list.png)

Uma das principais atividades registradas são reinicializações de uma unidade. Os dados listados incluem o status da atividade, o carimbo de hora da atividade foi acionado, o ID de assinatura do qual a atividade foi acionada e o usuário do Azure que desencadeou a atividade. 

Outra atividade que está sendo registrada são alterações na unidade nos metadados do Azure. Além da reinicialização iniciada, você pode ver a atividade de **Write HANAInstances**. Este tipo de atividade não realiza alterações na própria unidade HANA Large Instance, mas está documentando alterações nos meta dados da unidade no Azure. No caso listado, adicionamos e excluímos uma tag (veja a próxima seção).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adicionar e excluir uma tag Azure em uma unidade HANA Large Instance
Outra possibilidade que você tem é adicionar uma [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a uma unidade HANA Large Instance. A forma como as tags estão sendo atribuídas não difere de atribuir tags a VMs. Como acontece com as VMs, as tags existem nos meta dados do Azure e, para instâncias grandes do HANA, têm as mesmas restrições que as tags para VMs.

A exclusão de tags funciona da mesma forma que com as VMs. Ambas as atividades, aplicando e excluindo uma tag, serão listadas no registro de atividades da unidade HANA Large Instance em particular.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verifique as propriedades de uma unidade HANA Large Instance
A seção **Propriedades** inclui informações importantes que você recebe quando as instâncias são entregues a você. É uma seção onde você recebe todas as informações que você pode precisar em casos de suporte ou que você precisa ao configurar a configuração de instantâneo de armazenamento. Como tal, esta seção é uma coleta de dados em torno de sua instância, a conectividade da instância com o Azure e o backend de armazenamento. A parte superior da seção parece:


![parte superior das propriedades HLI no portal Azure](./media/hana-li-portal/portal-properties-top.png)

Os primeiros itens de dados, você já viu na tela de visão geral. Mas uma parte importante dos dados é o ExpressRoute Circuit ID, que você conseguiu quando as primeiras unidades implantadas foram entregues. Em alguns casos de suporte, você pode ser solicitado para esses dados. Uma entrada de dados importante é mostrada na parte inferior da captura de tela. Os dados exibidos são o endereço IP do cabeça de armazenamento NFS que isola seu armazenamento para o seu **inquilino** na pilha HANA Large Instance. Este endereço IP também é necessário quando você edita o [arquivo de configuração para backups de instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

À medida que você rola para baixo no painel de propriedades, você recebe dados adicionais como um ID de recurso exclusivo para sua unidade HANA Large Instance ou o ID de assinatura que foi atribuído à implantação.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Reiniciar uma unidade HANA Large Instance através do portal Azure
Iniciando uma reinicialização do sistema operacional Linux, houve várias situações em que o SO não conseguiu terminar uma reinicialização com sucesso. Para forçar uma reinicialização, você precisava abrir uma solicitação de serviço para que as operações da Microsoft executassem uma reinicialização de energia da unidade HANA Large Instance. A funcionalidade de uma reinicialização de energia de uma unidade HANA Large Instance está agora integrada ao portal Azure. Como você está na parte de visão geral da unidade HANA Large Instance, você vê o botão para reiniciar na parte superior da seção de dados

![Reiniciar passo #1 no portal Azure](./media/hana-li-portal/portal-restart-first-step.png)

Como você está pressionando o botão de reinicialização, você é perguntado se você realmente quer reiniciar o aparelho. Como você confirma pressionando o botão "Sim", a unidade será reiniciada.

> [!NOTE]
> No processo de reinicialização, você experimentará um pequeno momento em que o estado da unidade muda para **Começar** a se mover para o estado de **Started**. Estar no estado de **Started** significa que o Sistema Operacional está sendo iniciado ou que o Sistema Operacional foi completamente iniciado. Como resultado, após a reinicialização da unidade, você não pode esperar entrar imediatamente na unidade assim que o estado mudar para **Started**.

> [!IMPORTANT]
> Dependendo da quantidade de memória em sua unidade HANA Large Instance, uma reinicialização e reinicialização do hardware e do sistema operacional pode levar até uma hora


## <a name="open-a-support-request-for-hana-large-instances"></a>Abra uma solicitação de suporte para instâncias grandes hana
Fora da exibição do portal Azure das unidades HANA Large Instance, você pode criar solicitações de suporte especificamente para uma unidade hana grande instância também. À medida que você segue o link **Nova solicitação de suporte** 

![iniciar a solicitação de serviço #1 no portal Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Para obter o serviço de Grandes Instâncias SAP HANA listado na próxima tela, talvez seja necessário selecionar 'Todos os Serviços' como mostrado abaixo

![Selecione todos os serviços no portal Azure](./media/hana-li-portal/portal-create-service-request.png)

Na lista de serviços, você pode encontrar o serviço **SAP HANA Large Instance**. Ao escolher esse serviço, você pode selecionar tipos de problemas específicos como mostrado:


![Selecione a classe de problemas no portal Azure](./media/hana-li-portal/portal-select-problem-class.png)

Sob cada um dos diferentes tipos de problemas, é oferecida uma seleção de subtipos de problemas que você precisa selecionar para caracterizar ainda mais o seu problema. Depois de selecionar o subtipo, agora você pode nomear o assunto. Depois de terminar o processo de seleção, você pode passar para a próxima etapa da criação. Na seção **Soluções,** você é apontado para a documentação em torno de HANA Large Instances, o que pode dar um ponteiro para uma solução do seu problema. Se você não consegue encontrar uma solução para o seu problema na documentação sugerida, você vai para o próximo passo. No próximo passo, você será questionado se o problema é com VMs ou com unidades HANA Large Instance. Essas informações ajudam a direcionar a solicitação de suporte para os especialistas corretos. 

![Detalhes do caso de suporte no portal Azure](./media/hana-li-portal/portal-support-request-details.png)

Como você respondeu às perguntas e forneceu detalhes adicionais, você pode ir o próximo passo a fim de rever a solicitação de suporte e enviá-la.

## <a name="next-steps"></a>Próximas etapas

- [Como monitorar o SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitoramento e solução de problemas no lado do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

