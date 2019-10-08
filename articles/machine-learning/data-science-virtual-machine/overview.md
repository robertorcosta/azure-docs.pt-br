---
title: O que é a Máquina Virtual de Ciência de Dados do Azure
description: Principais cenários de análise e componentes para Máquinas Virtuais de Ciência de Dados do Windows e Linux.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 8359a8407e93a2b31466342b82539cef04b0fe01
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802187"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>O que é a Máquina Virtual de Ciência de Dados do Azure para Linux e Windows?

A DSVM (Máquina Virtual de Ciência de Dados) é uma imagem de VM personalizada plataforma de nuvem do Azure especificamente criada para ciência de dados. Ela tem muitas ferramentas populares de ciência de dados pré-instaladas e pré-configuradas para iniciar rapidamente a criação de aplicativos inteligentes para análise avançada. 

O DSVM está disponível em:
+ Windows Server 2016
+ Ubuntu 16.04 LTS e CentOS 7.4

> [!NOTE]
> Todas as ferramentas de VM para aprendizado profundo foram incluídas na Máquina Virtual de Ciência de Dados. 


## <a name="why-choose-the-dsvm"></a>Por que escolher a DSVM?
A meta da Máquina Virtual de Ciência de Dados é fornecer aos profissionais de dados de todos os níveis de habilidades e em todos os setores um ambiente de ciência de dados descomplicado e pré-configurado. Em vez de distribuir um workspace comparável por conta própria, você pode provisionar uma DSVM. Essa escolha pode economizar dias ou até mesmo _semanas_ necessárias para os processos de instalação, configuração e gerenciamento de pacotes. Depois de alocar o DSVM, você pode começar a trabalhar imediatamente no seu projeto de ciência de dados.

## <a name="sample-use-cases"></a>Casos de uso de exemplo

Abaixo, ilustramos alguns casos de uso comuns para clientes da DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Como mover cargas de trabalho de ciência de dados para a nuvem

A DSVM fornece uma configuração de linha de base para as equipes de ciência de dados que desejam substituir suas áreas de trabalho locais por uma área de trabalho em nuvem gerenciada, garantindo que todos os cientistas de dados de uma equipe tenham uma configuração consistente com a qual seja possível verificar experimentos e promover a colaboração. Ela também reduz os custos, reduzindo a carga de sysadmin. Essa redução no fardo poupa o tempo necessário para avaliar, instalar e manter pacotes de software para análise avançada.

### <a name="data-science-training-and-education"></a>Educação e treinamento de ciência de dados
Os treinadores corporativos e educadores que dão aulas de ciência de dados geralmente fornecem uma imagem de máquina virtual. A imagem garante que os alunos tenham uma configuração consistente e que as amostras funcionem de maneira previsível. 

A DSVM cria um ambiente sob demanda com uma configuração consistente que facilita o suporte e os desafios de incompatibilidade. Nos casos em que esses ambientes precisam ser criados com frequência, especialmente para aulas rápidas de treinamento, os alunos são consideravelmente beneficiados.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica sob demanda para projetos em larga escala
As maratonas/competições de ciência de dados ou modelagem e exploração de dados em larga escala exigem que a capacidade de hardware seja escalada horizontalmente, geralmente por um curto período. A DSVM pode ajudar a replicar o ambiente de ciência de dados rapidamente em servidores escalados horizontalmente sob demanda, o que permite a realização de experimentos que recursos de computação altamente potentes podem executar.

### <a name="custom-compute-power-for-azure-notebooks"></a>O poder da computação personalizada para o Azure Notebooks
O [Azure Notebooks](../../notebooks/azure-notebooks-overview.md) é um serviço hospedado gratuito para desenvolver, executar e compartilhar os notebooks do Jupyter na nuvem sem instalação. A camada de serviço gratuita está limitada a 4 GB de memória e 1 GB de dados. 

Para liberar todos os limites, você pode anexar um projeto do Notebooks a uma DSVM ou a qualquer outra VM que esteja em execução em um servidor Jupyter. Se você entrar no Azure Notebooks com uma conta usando o Azure Active Directory (como uma conta corporativa), os Notebooks exibirão automaticamente as DSVMs em todas as assinaturas associadas a essa conta. Você pode [anexar uma DSVM ao Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) para expandir o poder de computação disponível.

### <a name="short-term-experimentation-and-evaluation"></a>Avaliação e experimento de curto prazo
Use a DSVM para avaliar ou aprender a usar novas [ferramentas](./tools-included.md) de ciência de dados, especialmente vendo algumas de nossas [amostras e nossos passos a passos](./dsvm-samples-and-walkthroughs.md) publicados.


### <a name="deep-learning-with-gpus"></a>Aprendizado profundo com GPUs
Na DSVM, seus modelos de treinamento podem usar algoritmos de aprendizado profundo em hardware baseado em GPUs (unidades de processamento gráfico). Aproveitando os recursos de dimensionamento de VM da plataforma Azure, a DSVM ajuda a usar o hardware baseado em GPU na nuvem de acordo com suas necessidades. É possível mudar para uma VM baseada em GPU durante o treinamento de modelos grandes ou quando houver necessidade de cálculos em alta velocidade mantendo o mesmo disco do SO. Escolha um dos SKUs de máquina virtual habilitada para GPU da série N com a DSVM. Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitada para GPU.

A edição do Windows Server 2016 da DSVM vem pré-instalada com drivers de GPU, estruturas e versões de GPU de estruturas de aprendizado profundo. Na edição para Linux, o aprendizado profundo em GPUs está habilitada nos DSVMs CentOS e Ubuntu. 

Você também pode implantar a edição para Ubuntu, CentOS ou Windows 2016 da DSVM em uma máquina virtual do Azure não baseada em GPUs. Nesse caso, todas as estruturas de aprendizado profundo farão o fallback para o modo de CPU.
 
[Saiba mais sobre o aprendizado profundo e as estruturas de IA disponíveis](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>O que está incluso na DSVM?

Confira uma lista completa de ferramentas em DSVMs do Windows e do Linux [aqui](tools-included.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais com estes artigos:

+ Windows:
  + [Configurar uma DSVM do Windows](provision-vm.md)
  + [Dez coisas que você pode fazer em uma DSVM do Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurar uma DSVM do Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurar uma DSVM do Linux (CentOS)](linux-dsvm-intro.md)
  + [Ciência de dados em uma DSVM do Linux](linux-dsvm-walkthrough.md)
