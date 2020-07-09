---
title: Criar plano de migração com migrações para Azure | Microsoft Docs
description: Fornece orientação sobre a criação de um plano de migração com as migrações para Azure.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: e80ec71bef8c34331fac076b7b08be48a9571c2e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107692"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Criar um plano de migração com as Migrações para Azure

Este artigo fornece um guia rápido para ajudar a criar seu plano de migração para o Azure com as [migrações para Azure](migrate-services-overview.md).Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre a [migração do servidor](common-questions-server-migration.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)

## <a name="define-the-goals-of-cloud-migration"></a>Definir as metas de migração na nuvem

Antes de criar um plano de migração, é importante entender e avaliar a [motivação](/azure/cloud-adoption-framework/strategy/motivations) para migrar para a nuvem que pode ajudar a produzir resultados de negócios mais bem-sucedidos. Como a [estrutura de adoção de nuvem para o Azure](/azure/cloud-adoption-framework) explica, pode haver diferentes gatilhos e abordagens de migração adequados para sua empresa:  

**Eventos comerciais críticos** | **Resultado da migração**
--- | ---
Saída do datacenter | Economia de custos
Fusão, aquisição ou divestiture | Redução no fornecedor ou na complexidade técnica
Redução nas despesas de capital | Otimização de operações internas
Fim do suporte para tecnologias de missão crítica | Aumento na agilidade dos negócios
Resposta às alterações de conformidade regulatória | Preparação para novos recursos técnicos
Novos requisitos de soberania de dados | Dimensionamento para atender às demandas de mercado
Redução de interrupções e melhoria da estabilidade de ti | Dimensionamento para atender às demandas geográficas

Sua motivação de migração também pode ajudá-lo a refletir sobre as metas estratégicas e os resultados que você gostaria de obter migrando para o Azure. A próxima etapa é identificar e planejar um caminho de migração para o Azure que é adequado para suas cargas de trabalho. Migrações para Azure: a ferramenta de avaliação do servidor ajuda a avaliar as cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar.

## <a name="understand-your-digital-estate"></a>Entenda seu espaço digital

Comece entendendo sua infraestrutura, seus aplicativos e suas dependências locais para ajudar a identificar as cargas de trabalho que você deseja migrar para o Azure e obter projeções de custo otimizadas. A ferramenta de avaliação do servidor o ajudará a responder as seguintes perguntas:

### <a name="what-workloads-are-in-use"></a>Quais cargas de trabalho estão em uso?

Use o dispositivo de migrações leves do Azure para executar uma descoberta sem agente de suas VMs VMware locais, VMs Hyper-V e servidores físicos. A descoberta contínua coleta metadados de configuração e desempenho do computador e também pode ser usada para obter o inventário de aplicativos instalados e funções/recursos em execução em seus computadores locais. O dispositivo de migrações para Azure coleta:

- Detalhes de metadados dos computadores, discos e NICs

- Aplicativos instalados, incluindo aplicativos e funções/recursos  

- Dados de desempenho, incluindo utilização de CPU e memória, IOPS de disco e taxa de transferência

Em seguida, exporte a lista de inventário de aplicativos para descobrir todas as instâncias de SQL Server em execução em suas cargas de trabalho e use a ferramenta migrações para Azure: avaliação de banco de dados para entender sua preparação.

 ![Inventário de aplicativos no portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Exportação de inventário de aplicativos](./media/concepts-migration-planning/application-inventory-export.png)

Junto com os dados de descoberta da ferramenta de avaliação do servidor, use os dados existentes do CMDB para criar nossa visão do seu servidor e do banco do dados e compreender a distribuição do servidor entre unidades de negócios, proprietários de aplicativos, geografias, etc., que podem, por sua vez, ajudar a priorizar as cargas de trabalho a serem migradas.

### <a name="what-dependencies-exist-between-workloads"></a>Quais dependências existem entre as cargas de trabalho?

Depois de descobrir seus servidores, use o mapeamento de dependência sem agente para visualizar e identificar dependências entre servidores e estratégias de otimização para mover servidores interdependentes para o Azure. A visualização ajuda a entender se determinadas máquinas estão em uso ou se elas podem ser descomissionadas em vez de serem migradas.  Certifique-se de analisar as dependências para garantir que nada seja deixado para trás e evite interrupções surpresa durante a migração. Depois de executar o inventário de aplicativos e o mapeamento de dependências, você poderá criar grupos de alta confiança e começar a avaliar seus servidores.

 ![Mapeamento de dependências](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Eles são otimizados e dimensionados corretamente?

Como o Azure fornece flexibilidade para redimensionar sua capacidade de nuvem ao longo do tempo, a migração é uma oportunidade para otimizar os recursos de CPU e memória alocados para seus servidores. Crie uma avaliação no grupo identificado antes de entender o histórico de desempenho das cargas de trabalho, o que será muito crucial em recomendações de discos e SKUs de VM com direitos no Azure.

## <a name="assess-your-readiness-for-migration"></a>Avalie sua preparação para a migração

### <a name="readiness-and-suitability-analysis-for-azure"></a>Preparação e análise de adequação para o Azure
Exporte o relatório de avaliação de VM do Azure e filtre pelas seguintes categorias de preparação para entender a preparação da VM para o Azure:

- **Pronto para o Azure**: você pode migrar esses computadores no estado em que se encontram para o Azure sem nenhuma alteração  

- **Condicionalmente pronto para o Azure**: você pode migrar esses computadores para o Azure, mas precisará de pequenas alterações nesses servidores, de acordo com as diretrizes de correção fornecidas na avaliação

- **Não está pronto para o Azure**: você não pode migrar esses computadores para o Azure como estão e precisa corrigir os problemas de acordo com as diretrizes de correção antes da migração

- **Preparação desconhecida**: as migrações para Azure não podem determinar a prontidão do computador devido a metadados insuficientes

Usando as avaliações de banco de dados, você pode avaliar a prontidão para migrar seu SQL Server de data para o banco de dados SQL do Azure ou instâncias gerenciadas do Azure SQL. Você pode ver a porcentagem de status de preparação de migração para cada uma de suas instâncias do SQL Server. Além disso, para cada uma das instâncias, você pode ver o destino recomendado no Azure, os bloqueadores de migração potenciais, a contagem de alterações significativas, a preparação para o banco de BD SQL do Azure/VM do SQL e o nível de compatibilidade. Você pode se aprofundar para entender o impacto dos bloqueadores de migração e recomendações para corrigi-los.

 ![Avaliações de banco de dados](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Recomendações de dimensionamento

Depois que o computador estiver marcado como pronto para o Azure, a avaliação do servidor fará recomendações de dimensionamento para identificar a VM do Azure e o SKU de disco para suas VMs. Você pode optar por ver a recomendação de dimensionamento com base no histórico de desempenho (para que você esteja otimizando os recursos durante a migração) ou com base em sua configuração local sem considerar o histórico de desempenho. Para bancos de dados do, você pode ver as recomendações em relação ao SKU do Database, tipo de preço e nível de computação em sua avaliação de banco de dados.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Computar Avaliações para obter custos estimados para executar as cargas de trabalho no Azure

A opção de dimensionamento à direita *baseada em desempenho* nas avaliações permite que você otimize as cargas de trabalho do Azure. Além dos direitos, há alguns outros caminhos para ajudar a economizar custos:

- **Instâncias reservadas**: com instâncias reservadas, você pode reduzir significativamente os custos em comparação com os preços pagos conforme o uso com termos de 1 ou 3 anos no Windows e Linux (VMS)

- **Benefícios híbridos do Azure**: você pode colocar licenças locais do Windows Server com o Software Assurance no Azure e combiná-las com as opções de instâncias reservadas

- **Enterprise Agreement oferta (ea)**: a Enterprise Agreement oferece economias internas aplicáveis à sua assinatura

- **Ofertas**: há várias ofertas do Azure, por exemplo, Desenvolvimento/Teste Pago Conforme o Uso e desenvolvimento/teste Enterprise que fornecem taxas mais baixas para VMs de desenvolvimento ou teste

- **Tempo de atividade da VM**: você pode mencionar a duração em dias por mês e horas por dia em que as VMs do Azure serão executadas para reduzir os custos (não aplicável à ri)

- **Região de destino**: você pode criar várias avaliações em regiões diferentes para comparar se a migração para uma determinada região em uma geografia pode ser mais econômica

- **Recomendações baseadas em desempenho**: como prática recomendada, tente usar as recomendações de VM do Azure com direitos que ajudarão você a economizar os custos de nuvem

### <a name="visualize-data"></a>Visualizar dados

Você pode ver o relatório de avaliação do servidor com a preparação do Azure e a distribuição de custo mensal no portal, bem como exportar a avaliação para aplicar mais visualizações nos dados de descoberta e avaliação para tornar o plano de migração mais avançado. Você pode criar várias avaliações para diferentes combinações de propriedades e escolher o conjunto de propriedades que funcionam melhor para sua empresa.  

 ![Visão geral de avaliações](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Avaliar lacunas e bloqueadores potenciais

Ao determinar os aplicativos e a infraestrutura subjacente para migrar, identifique as restrições de tempo de inatividade desses aplicativos e procure quaisquer dependências operacionais, entre seus aplicativos e a infraestrutura subjacente. Essa análise pode ajudá-lo a planejar migrações que atendam ao seu RTO (objetivo de tempo de recuperação) e garantir uma perda mínima de dados. Antes de migrar, recomendamos que você revise e atenue quaisquer problemas de compatibilidade ou recursos sem suporte que possam bloquear a migração de seus servidores e bancos de dados SQL com a ajuda do relatório de avaliação do servidor e migrações para Azure: recomendações de avaliação de banco de dados.

### <a name="first-workloads-to-target-and-approach"></a>Primeiras cargas de trabalho para destino e abordagem

Agora que você tem todas as informações cruciais para tomar sua decisão de migração, você deve priorizar quais aplicativos e cargas de trabalho devem ser migradas primeiro. Desenvolva uma abordagem "aplicar e aprender" para migrar seus aplicativos pretendidos de maneira sistemática e controlável para que você possa passar por falhas nessa estratégia antes de embarcar em uma migração em escala completa. Você também pode usar fatores estratégicos como-complexidade e tempo para migrar, a urgência dos negócios, o ambiente de produção/não de produção, a conformidade e os requisitos de segurança, o conhecimento do aplicativo, etc. para priorizar quais grupos de aplicativos migrar.

Algumas estratégias de migração recomendadas são:

- **Priorize seu WINS rápido**: você pode usar os relatórios de avaliação para identificar frutas de baixa interrupções, incluindo os servidores e bancos de dados que estão totalmente prontos e exigem um mínimo de esforço para migrar para o Azure:
    - Pronto para o Azure: exporte seu relatório de avaliação e filtre todos os computadores que estão "prontos para o Azure". Esse pode ser seu primeiro grupo de computadores que você pode mover e mudar com a ferramenta migrações para Azure: Server Migration.
    - Fim do suporte do so: exporte seu relatório de avaliação e filtre todos os computadores que estão executando os sistemas operacionais Windows Server 2008 e Windows Server 2008 R2. Essas SKUs são o fim do suporte e apenas o Azure fornece três anos de atualizações de segurança gratuitas quando você as migra para o Azure. Quando você combina, Benefício Híbrido do Azure e usa instâncias reservadas, o salvamento pode ser muito maior.
    - Migração de SQL Server: Use as recomendações de avaliação de banco de dados para migrar os bancos de dados prontos para os bancos de dados SQL do Azure usando as migrações para Azure: migração de banco de dados e os banco de dados prontos para a VM do SQL Azure usando o migrações para Azure: migração de servidor.
    - Fim do suporte do software: exporte seu inventário de aplicativos e filtre qualquer um dos softwares/extensões que possam estar atingindo o fim do suporte. Você deve priorizar esses aplicativos.
    - VMs excessivas provisionadas: exporte seu relatório de avaliação e filtre computadores com baixa utilização da CPU (%) e utilização de memória (%).  Você pode usar essa oportunidade para migrar para uma VM com direitos no Azure e salvar o que você paga por recursos subutilizados.
    - Restrições de capacidade: exportar o relatório de avaliação e filtrar computadores com alta utilização da CPU (%) e utilização de memória (%).  Você pode impedir que as VMs sobrerestritas quebrem e aumentem o desempenho migrando-as para o Azure e utilizem a capacidade de dimensionamento automático para atender à demanda. Você também pode examinar o relatório de avaliação para entender suas restrições de armazenamento analisando o IOPS de disco e a taxa de transferência e encontrar o tipo de disco recomendado que melhor atenderá às suas necessidades.

- **Comece pequeno e vá muito bem**: comece a mover aplicativos e cargas de trabalho que são mínimos de risco e menos complexos para criar confiança em sua estratégia de migração. Você também pode Interseccionar suas recomendações de avaliação de migrações para Azure com o repositório CMDB de sua organização para localizar e migrar cargas de trabalho de ambiente de desenvolvimento/teste em suas migrações do piloto. Os aprendizados desses pilotos podem ser usados ao migrar cargas de trabalho de produção.  

- **Cumpra seus requisitos regulatórios/do setor**: o Azure mantém o maior portfólio de conformidade em todo o setor, em termos de amplitude e profundidade de ofertas. Use isso como uma oportunidade de priorizar suas migrações para o Azure e obedecer aos padrões e leis nacionais, regionais e específicos do setor. Isso é especialmente verdadeiro para organizações que lidam com informações confidenciais de negócios ou de retenção ou que estão em setores altamente regulamentados, nos quais os padrões e regulamentos abound e, em determinados casos, podem ser alterados com frequência, dificultando a manutenção.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Finalizar o plano de migração e se preparar para a migração

Antes de finalizar seu plano de migração, certifique-se de que essas considerações importantes de migração não reproduzem empecilhos ao seu planejamento de migração:

- Avaliar restrições de largura de banda de rede e latência que podem causar atrasos imprevistos e interromper a velocidade de replicação da migração.

- Buffer no tempo para realizar o teste de desempenho e de aceitação do usuário nos aplicativos migrados ou executar qualquer ajuste de aplicativo após a migração, como atualização de cadeias de conexão de banco de dados e configurações de servidor Web, execução de transferência e limpeza, etc.

- Examine as permissões do Azure recomendadas e suas funções de acesso de servidor/banco de dados e o modelo de permissões necessários para a migração.

- Prepare sua organização e garanta o alinhamento da força de obra com a transformação digital. Uma base sólida de treinamento é importante para uma mudança organizacional bem-sucedida. Confira o treinamento gratuito disponível em [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), incluindo cursos sobre conceitos básicos do Azure, arquitetura da solução e segurança. Incentive sua equipe a explorar a [certificação do Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   também.  

- Obtenha suporte para sua implementação, se necessário. Muitas organizações optam por uma ajuda externa para dar suporte à migração na nuvem. Para migrar para o Azure de forma rápida e confiável com assistência personalizada, considere um [provedor de serviços gerenciado pelo especialista do Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   ou [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Crie um plano de migração de nuvem eficaz que inclua informações detalhadas sobre a lista/grupos de aplicativos que você deseja migrar, a disponibilidade do aplicativo e do banco de dados e as restrições de tempo de inatividade e as etapas de migração desejadas. Seu plano de migração também deve considerar quanto tempo a cópia de dados levará e incluirá um buffer considerem para atividades de transferência e teste após a migração. Os testes após a migração devem incluir casos funcionais, de integração, de segurança e de teste de desempenho para garantir que os aplicativos migrados funcionem conforme o esperado e que todos os objetos de banco de dados e as relações com a nuvem tenham sido transferidos com êxito. Os testes após a migração devem incluir casos funcionais, de integração, de segurança e de teste de desempenho para garantir que os aplicativos migrados funcionem conforme o esperado e que todos os objetos de banco de dados e as relações com a nuvem tenham sido transferidos com êxito.  

Use essa análise para criar um roteiro de migração e declarar uma janela de manutenção para migrar seus aplicativos e bancos de dados com tempo de inatividade mínimo a zero e limitar o impacto operacional/comercial em potencial durante a migração.  

Recomendamos que você sempre teste e continue usando o recurso de *migração de teste* do migrações para Azure antes de iniciar as migrações completas para o Azure. Esses dados reais o ajudarão a estimar o tempo real envolvido e fazer ajustes necessários em seu plano de migração. A migração de teste também oferece uma oportunidade de descobrir possíveis problemas com o plano de migração e corrigi-los antes que a migração seja realizada.  

Quando você estiver pronto para migrar, use a *ferramenta de migração de servidor* do migrações para Azure e o serviço de migração de *dados* do Azure migrar para uma experiência de migração direta e integrada com acompanhamento de ponta a ponta. A ferramenta de migração de servidor dá suporte à migração de VMs e servidores hospedados localmente no datacenter dos clientes ou em qualquer outra nuvem privada ou pública, incluindo AWS, GCP, etc., com cerca de zero tempo de inatividade. O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo.  

> [!NOTE]
> Para VMs VMware, a avaliação do servidor usa o sistema operacional especificado para a VM no vCenter Server para lidar com a análise do SO convidado. Para VMs do Linux em execução no VMware, atualmente ele não identifica a versão exata do kernel do sistema operacional convidado.

## <a name="next-steps"></a>Próximas etapas

- Investigue a [jornada de migração na nuvem](/azure/architecture/cloud-adoption/getting-started/migrate)   na estrutura de adoção de nuvem do Azure.
- [Introdução](https://youtu.be/wFfq3YPxYHE) às migrações para Azure.
- Crie uma avaliação para VMs [VMware](tutorial-assess-vmware.md) ou [VMs do Hyper-V](tutorial-assess-hyper-v.md).
