---
title: Avaliação de solução de problemas e visualização de dependência no Azure Migrate
description: Obtenha ajuda com avaliação de solução de problemas e visualização de dependência no Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127668"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Solucionar problemas de visualização de avaliação/dependência

Este artigo ajuda você a solucionar problemas com avaliação e visualização de dependência com [o Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemas de prontidão de avaliação

Corrigir os problemas de prontidão de avaliação da seguinte forma:

**Problema** | **Corrigir**
--- | ---
Tipo de inicialização sem suporte | O Azure não suporta VMs com um tipo de inicialização EFI. Recomendamos converter o tipo de inicialização em BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração do servidor Azure Migrate para lidar com a migração dessas VMs. Ele converterá o tipo de inicialização da VM para BIOS durante a migração.
Sistema operacional Windows suportado condicionalmente | O sistema operacional passou a sua data de suporte final e precisa de um Contrato de Suporte Personalizado (CSA) para [suporte no Azure](https://aka.ms/WSosstatement). Considere atualizar antes de migrar para o Azure.
Sistema operacional Windows sem suporte | O Azure suporta apenas [versões selecionadas do Sistema Operacional Windows](https://aka.ms/WSosstatement). Considere atualizar a máquina antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas [versões selecionadas do Sistema Operacional Linux](../virtual-machines/linux/endorsed-distros.md). Considere atualizar a máquina antes de migrar para o Azure.
SO Linux não endossado | A máquina pode começar no Azure, mas o Azure não oferece suporte ao sistema operacional. Considere atualizar para uma [versão linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure.
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como "Outros" no vCenter Server. Esse comportamento impede o Azure Migrate de verificar a prontidão do Azure da VM. Certifique-se de que o sistema operacional é [suportado](https://aka.ms/azureoslist) pelo Azure antes de migrar a máquina.
Versão bit sem suporte | VMs com sistemas operacionais de 32 bits podem ser iniciados no Azure, mas recomendamos que você atualize para 64 bits antes de migrar para o Azure.
Requer uma assinatura do Microsoft Visual Studio | A máquina está executando um sistema operacional cliente Windows, que é suportado apenas através de uma assinatura do Visual Studio.
VM não encontrado para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e throughput) necessários para a máquina excede o suporte ao Azure VM. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrado para o desempenho de rede necessário | O desempenho de rede (entrada/saída) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de rede para a máquina.
VM não encontrado no local especificado | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados | Um ou mais discos conectados à VM não atendem aos requisitos do Azure. Um<br/><br/> Azure Migrate: A avaliação do servidor atualmente não suporta discos Ultra SSD e avalia os discos com base nos limites de disco para discos gerenciados premium (32 TB).<br/><br/> Para cada disco conectado à VM, certifique-se de que o tamanho do disco esteja < 64 TB (suportado por discos Ultra SSD).<br/><br/> Se não for, reduza o tamanho do disco antes de migrar para o Azure ou use vários discos no Azure e [liste-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais altos. Certifique-se de que o desempenho (IOPS e throughput) necessário sporto de cada disco seja suportado por [discos de máquina virtual gerenciados](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)pelo Azure .
Um ou mais adaptadores de rede inadequados. | Remova os adaptadores de rede não utilizados do computador antes da migração.
A contagem de disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | Azure Migrate: A avaliação do servidor atualmente não suporta discos Ultra SSD e avalia os discos com base nos limites de disco premium (32 TB).<br/><br/> No entanto, o Azure suporta discos com tamanho de até 64 TB (suportado por discos Ultra SSD). Reduza os discos para menos de 64 TB antes da migração ou use vários discos no Azure e [liste-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais altos.
O disco não está disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
O disco não está disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco por causa de um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não encontrou um tipo de VM adequado. Reduza a memória e o número de núcleos da máquina local antes de migrar.
Não foi possível determinar a adequação da VM por causa de um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.

## <a name="linux-vms-are-conditionally-ready"></a>Os VMs Linux estão "condicionalmente prontos"

A Avaliação do Servidor marca as VMs Linux como "condicionadas" devido a uma lacuna conhecida na avaliação do servidor.

- A lacuna impede que ele detecte a versão menor do Sistema Operacional Linux instalada nas VMs locais.
- Por exemplo, para RHEL 6.10, atualmente o Server Assessment detecta apenas O RHEL 6 como a versão do SISTEMA OPERACIONAL.
-  Como o Azure endossa apenas versões específicas do Linux, as VMs linux estão atualmente marcadas como condicionadas na Avaliação do Servidor.
- Você pode determinar se o Sistema Operacional Linux em execução na VM local é endossado no Azure, revisando o [suporte ao Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Depois de verificar a distribuição endossada, você pode ignorar este aviso.

## <a name="azure-skus-bigger-than-on-premises"></a>SKUs Azure maiores do que no local

A avaliação do servidor Azure Migrate pode recomendar SKUs Azure VM com mais núcleos e memória do que a alocação atual no local com base no tipo de avaliação:


- A recomendação VM SKU depende das propriedades de avaliação.
- Isso é afetado pelo tipo de avaliação que você realiza na Avaliação do Servidor: *Baseada em desempenho*ou como no *local*.
- Para avaliações baseadas em desempenho, a Avaliação do Servidor considera os dados de utilização das VMs locais (utilização de CPU, memória, disco e rede) para determinar o VM VM de destino certo para suas VMs no local. Também adiciona um fator de conforto ao determinar uma utilização eficaz.
- Para dimensionamento no local, os dados de desempenho não são considerados, e o SKU alvo é recomendado com base na alocação no local.

Para mostrar como isso pode afetar as recomendações, vamos dar um exemplo:

Temos uma VM no local com quatro núcleos e oito GB de memória, com 50% de utilização de CPU e 50% de utilização de memória, e um fator de conforto especificado de 1.3.

-  Se a avaliação **for Como no local, recomenda-se**um Azure VM SKU com quatro núcleos e 8 GB de memória.
- Se a avaliação for baseada em desempenho, baseada na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de memória de 8 GB * 1,3 = memória de 5,3 GB), o VM SKU mais barato de quatro núcleos (contagem de núcleo sustal mais próxima) e oito GB de memória (mais próximo suportado tamanho da memória) é recomendado.
- [Saiba mais](concepts-assessment-calculation.md#types-of-assessments) sobre o dimensionamento de avaliações.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKUs de disco Azure maiores que os locais

A avaliação do servidor azure migrate pode recomendar um disco maior com base no tipo de avaliação.
- O dimensionamento de disco na avaliação do servidor depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
- Se os critérios de dimensionamento **forem baseados em desempenho**e o tipo de armazenamento for definido como **Automático,** os valores de IOPS e de throughput do disco são considerados ao identificar o tipo de disco de destino (HDD padrão, SSD padrão ou Premium). Um sku de disco do tipo de disco é então recomendado, e a recomendação considera os requisitos de tamanho do disco local.
- Se o critério de dimensionamento for **baseado em desempenho**e o tipo de armazenamento for **Premium,** um SKU de disco premium no Azure é recomendado com base nos requisitos de IOPS, throughput e tamanho do disco local. A mesma lógica é usada para realizar o dimensionamento de disco quando os critérios de dimensionamento são **As no local** e o tipo de armazenamento é Standard **HDD,** **Standard SSD**ou **Premium**.

Como exemplo, se você tem um disco on-premises com 32 GB de memória, mas o IOPS de leitura e gravação agregado para o disco é 800 IOPS, server Assessment recomenda um disco premium (devido aos requisitos mais altos do IOPS), e então recomenda um SKU de disco que pode suportar o IOPS e tamanho necessários. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco local fosse de 32 GB, o Server Assessment recomenda um disco maior devido à alta exigência de IOPS do disco local.

## <a name="utilized-corememory-percentage-missing"></a>Porcentagem de núcleo/memória utilizada ausente

A avaliação do servidor relata "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" quando o aparelho Azure Migrate não pode coletar dados de desempenho para as VMs locais relevantes.

- Isso pode ocorrer se as VMs forem desligadas durante a duração da avaliação. O aparelho não pode coletar dados de desempenho para uma VM quando está desligado.
- Se apenas os contadores de memória estiverem faltando e você estiver tentando avaliar as VMs Hyper-V, verifique se você tem memória dinâmica ativada nessas VMs. Há um problema conhecido apenas para VMs Hyper-V, no qual um aparelho Azure Migrate não pode coletar dados de utilização de memória para VMs que não têm memória dinâmica ativada.
- Se algum dos contadores de desempenho estiver faltando, a avaliação do servidor Azure Migrate volta para os núcleos e a memória alocados e recomenda um tamanho de VM correspondente.
- Se todos os contadores de desempenho estiverem faltando, certifique-se de que os requisitos de acesso à porta para avaliação sejam atendidos. Saiba mais sobre os requisitos de acesso à porta para [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) e avaliação [física](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) do servidor.

## <a name="is-the-operating-system-license-included"></a>A licença do sistema operacional está incluída?

A avaliação do servidor do Azure Migrate atualmente considera o custo da licença do sistema operacional apenas para máquinas Windows. Os custos de licença para máquinas Linux não são considerados atualmente.

## <a name="how-does-performance-based-sizing-work"></a>Como funciona o dimensionamento baseado em desempenho?

A Avaliação de Servidor coleta continuamente os dados de desempenho dos computadores locais e o utiliza para recomendar a SKU da VM e a SKU de disco no Azure. [Saiba como os](concepts-assessment-calculation.md#calculate-sizing-performance-based) dados baseados em desempenho são coletados.


## <a name="dependency-visualization-in-azure-government"></a>Visualização de dependência no Governo Azure

O Azure Migrate depende do Mapa de Serviço sustais para a funcionalidade de visualização de dependência. Como o Mapa de Serviços está atualmente indisponível no Governo Azure, essa funcionalidade não está disponível no Governo Azure.

## <a name="dependencies-dont-show-after-agent-install"></a>Dependências não aparecem após a instalação do agente

Depois de instalar os agentes de visualização de dependência em VMs no local, o Azure Migrate normalmente leva de 15 a 30 minutos para exibir as dependências no portal. Se você esperou por mais de 30 minutos, certifique-se de que o Microsoft Monitoring Agent (MMA) possa se conectar ao espaço de trabalho do Log Analytics.

Para VMs do Windows:
1. No Painel de Controle, inicie o MMA.
2. Nas **propriedades** > do Microsoft Monitoring**Agent, o Azure Log Analytics (OMS)** garante que o **Status** para o espaço de trabalho esteja verde.
3. Se o status não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

    ![Status do MMA](./media/troubleshoot-assessment/mma-properties.png)

Para VMs Linux, certifique-se de que os comandos de instalação para MMA e o agente de dependência foram bem sucedidos.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

- **Agente MMS**: Revise os sistemas operacionais [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) suportados.
- **Agente de dependência:** os sistemas operacionais [Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) suportados.

## <a name="visualize-dependencies-for--hour"></a>Visualize dependências por > hora

Embora o Azure Migrate permita que você volte a uma data específica no último mês, a duração máxima para a qual você pode visualizar as dependências é de uma hora.

Por exemplo, você pode usar a funcionalidade de duração de tempo no mapa de dependência para visualizar dependências para ontem, mas você pode visualizá-las por um período de uma hora apenas.

No entanto, você pode usar os registros do Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração mais longa.

## <a name="visualized-dependencies-for--10-machines"></a>Dependências visualizadas para > 10 máquinas

Na Avaliação do Servidor Migrado do Azure, você pode [visualizar dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) com até 10 VMs. Para grupos maiores, recomendamos que você divida as VMs em grupos menores para visualizar dependências.

## <a name="machines-show-install-agent"></a>Máquinas mostram "Instalar agente"

Depois de migrar máquinas com visualização de dependência habilitada para o Azure, as máquinas podem mostrar a ação "Instalar agente" em vez de "Exibir dependências" devido ao seguinte comportamento:


- Após a migração para o Azure, as máquinas no local são desligadas e as VMs equivalentes são giradas no Azure. Essas máquinas adquirem um endereço MAC diferente.
- As máquinas também podem ter um endereço IP diferente, com base em se você reteve o endereço IP no local ou não.
- Se os endereços MAC e IP forem diferentes dos locais, o Azure Migrate não associa as máquinas locais a nenhum dadependência do Mapa de Serviço. Neste caso, ele mostrará a opção de instalar o agente em vez de visualizar dependências.
- Após uma migração de teste para o Azure, as máquinas locais permanecem ligadas como esperado. Máquinas equivalentes giradas no Azure adquirem diferentes endereços MAC e podem adquirir diferentes endereços IP. A menos que você bloqueie o tráfego de log do Azure Monitor a partir dessas máquinas, o Azure Migrate não associará as máquinas no local a quaisquer dados de dependência do Mapa de Serviço e, portanto, mostrará a opção de instalar agentes, em vez de visualizar dependências.


## <a name="capture-network-traffic"></a>Capturar tráfego de rede

Coletar registros de tráfego de rede da seguinte forma:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Pressione F12 para iniciar ferramentas de desenvolvedor. Se necessário, limpe as **entradas Limpar na configuração de navegação.**
3. Selecione a guia **Rede** e comece a capturar o tráfego da rede:
   - No Chrome, selecione **Preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se o círculo vermelho não aparecer, selecione o círculo preto para iniciar.
   - No Microsoft Edge e no Internet Explorer, a gravação deve ser iniciada automaticamente. Caso isso não aconteça, selecione o botão de reprodução verde.
4. Tente reproduzir o erro.
5. Depois que você tiver encontrado o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade registrada:
   - No Chrome, clique com o botão direito do mouse e selecione **Salvar como HAR com conteúdo**. Esta ação comprime e exporta os logs como um arquivo .har.
   - No Microsoft Edge ou no Internet Explorer, selecione a opção **Exportar tráfego capturado.** Esta ação comprime e exporta o registro.
6. Selecione a guia **Console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito em qualquer lugar no log do console. Selecione **Salvar como**, para exportar e fechar o registro.
   - No Microsoft Edge ou no Internet Explorer, clique com o botão direito do mouse sobre os erros e selecione **Copiar todos**.
7. Fechar as Ferramentas para Desenvolvedores.

## <a name="next-steps"></a>Próximas etapas

[Crie](how-to-create-assessment.md) ou [personalize](how-to-modify-assessment.md) uma avaliação.
