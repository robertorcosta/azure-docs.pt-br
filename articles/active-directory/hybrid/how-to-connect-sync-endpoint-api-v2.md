---
title: Versão prévia pública do ponto de extremidade da sincronização do Azure AD Connect V2 | Microsoft Docs
description: Este documento aborda as atualizações da API de pontos de extremidade da sincronização do Azure AD Connect v2.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5501153a9c54d4509aa7dd948e03e011b30d79e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713804"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>API de ponto de extremidade da sincronização do Azure AD Connect V2 (versão prévia pública) 
A Microsoft implantou um novo ponto de extremidade (API) para o Azure AD Connect que aprimora o desempenho das operações de serviço de sincronização para o Azure Active Directory. Ao utilizar o novo ponto de extremidade V2, você experimentará ganhos de desempenho perceptíveis na exportação e na importação para o Azure AD. Esse novo ponto de extremidade dá suporte ao seguinte:
    
 -  grupos de sincronização com até 250 mil membros
 - ganhos de desempenho na exportação e na importação para o Azure AD
 
> [!NOTE]
> Atualmente, o novo ponto de extremidade não tem um limite de tamanho de grupo configurado para os grupos do O365 que são gravados novamente. Isso pode ter um efeito no Active Directory e nas latências de ciclo de sincronização.  É recomendável aumentar os tamanhos de grupo de modo incremental.  


## <a name="pre-requisites"></a>Pré-requisitos  
Para usar o novo ponto de extremidade V2, você precisará usar o [Azure AD Connect versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior e seguir as etapas de implantação fornecidas abaixo para habilitar o ponto de extremidade V2 para seu servidor do Azure AD Connect.   

>[!NOTE]
>Atualmente, essa versão prévia pública só está disponível na nuvem global do Azure e não está disponível para [nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).

### <a name="public-preview-limitations"></a>Limitações da visualização pública  
Embora esta versão tenha passado por testes extensivos, você ainda poderá encontrar problemas. Uma das metas dessa versão prévia pública é encontrar e corrigir esses problemas.  

>[!IMPORTANT]
> Embora o suporte seja fornecido para essa versão prévia pública, a Microsoft nem sempre poderá corrigir todos os problemas que você possa encontrar imediatamente. Por esse motivo, é recomendável que você use o seu melhor julgamento antes de implantar esta versão em seu ambiente de produção. 

## <a name="deployment-guidance"></a>Diretrizes de implantação 
Será necessário implantar o [Azure AD Connect versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior para usar o ponto de extremidade V2. Use o link fornecido para download. 

É recomendável que você siga o método de [migração Swing](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration) para distribuir o novo ponto de extremidade em seu ambiente. Isso fornecerá um plano de contingência claro no evento, que necessita de uma grande reversão. O exemplo a seguir ilustra como uma migração Swing pode ser usada neste cenário. Para obter mais informações sobre o método de implantação de migração Swing, veja o link fornecido. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Migração Swing para implantação do ponto de extremidade V2
As etapas a seguir mostrarão a implantação do ponto de extremidade v2 usando o método Swing.

1. Implante o ponto de extremidade V2 no servidor de preparo atual. Esse servidor será conhecido como o **servidor V2** nas etapas abaixo. O servidor ativo atual continuará processando a carga de trabalho de produção usando o ponto de extremidade V1, que será chamado de servidor **V1** abaixo.
1. Valide se o **servidor V2** ainda está processando importações conforme o esperado. Nessa fase, os grupos grandes não serão provisionados no Azure AD ou no AD local, mas você poderá verificar se a atualização não resultou em nenhum outro impacto inesperado no processo de sincronização existente. 
2. Após a conclusão da validação, alterne o **servidor V2** para ser o servidor ativo e o **servidor V1** para ser o servidor de preparo. Neste momento, os grupos grandes que estão no escopo para serem sincronizados serão provisionados para o Azure AD, assim como grandes grupos unificados do O365 serão provisionados no AD, se o write-back do grupo estiver habilitado.
3. Valide se o **servidor V2** está executando e processando grupos grandes com êxito. Você pode optar por permanecer nessa etapa e monitorar o processo de sincronização por um período.
  >[!NOTE]
  > Se você precisar fazer a transição de volta para a configuração anterior, poderá executar uma migração Swing do **servidor V2** de volta para o **servidor V1**. Como o ponto de extremidade V1 não dá suporte a grupos com mais de 50 mil membros, qualquer grupo grande que tenha sido provisionado pelo Azure AD Connect, no Azure AD ou no AD local, será excluído subsequentemente. 
4. Quando tiver certeza sobre o uso do ponto de extremidade V2, atualize o **servidor V1** para começar a usar o ponto de extremidade V2. 
 

## <a name="expectations-of-performance-impact"></a>Expectativas de impacto no desempenho  
Ao usar o ponto de extremidade V2, ganhos de desempenho são uma função do número de grupos sincronizados, tamanho desses grupos e rotatividade de grupo (a atividade resultante da adição e da remoção de usuários como membros do grupo). O uso do novo ponto de extremidade, sem aumentar o número, o tamanho ou a rotatividade dos grupos sincronizados, deve resultar em tempos mais curtos para exportar e importar para o Azure AD. 
 
No entanto, os ganhos de desempenho podem ser negados pelo processamento adicional necessário ao sincronizar grupos grandes. Você poderia acabar aumentando o tempo de sincronização geral adicionando um número excessivo de grupos grandes ao processo de sincronização.  

Para obter uma melhor compreensão de como a adição dos novos grupos afetará o desempenho da sincronização, é recomendável que você comece sincronizando apenas alguns grupos grandes com menos de 100 mil membros. Em seguida, você pode aumentar o número e o tamanho dos grupos trazendo mais deles no escopo, por meio da UO, do atributo ou da filtragem de tamanho máximo do grupo. Os aprimoramentos de desempenho serão realizados nas tarefas de exportação e importação para o conector do Azure AD, não no conector do AD local. 

## <a name="deployment-step-by-step"></a>Passo a passo da implantação 
As três fases a seguir são um exemplo detalhado da implantação do novo ponto de extremidade V2.  Use as fases como uma diretriz para sua implantação.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fase 1 – instalar e validar o Azure AD Connect 
É recomendável que você primeiro execute as etapas para instalar ou atualizar para o [Azure AD Connect versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior e validar o processo de sincronização antes de ir para a segunda fase, na qual você habilitará o ponto de extremidade V2. No servidor do Azure AD Connect: 


1. [Opcional] Faça backup do banco de dados 
2. Instale ou atualize para o [Azure AD Connect versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior.
3. validar a instalação 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fase 2 – habilitar o ponto de extremidade V2 
A próxima etapa é habilitar o ponto de extremidade V2. 

> [!NOTE]
> Depois de habilitar o ponto de extremidade V2 para seu servidor, você poderá ver alguns aprimoramentos de desempenho em sua carga de trabalho existente. No entanto, você ainda não conseguirá sincronizar grupos com mais de 50 mil membros. 

Para alternar para o ponto de extremidade V2, use as seguintes etapas: 

1. Abra um prompt do PowerShell como administrador. 
2. Desabilite o agendador de sincronização depois de verificar que nenhuma operação de sincronização está em execução: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importe o novo módulo: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Alterne para o ponto de extremidade v2:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Agora você habilitou o ponto de extremidade V2 para seu servidor. Reserve algum tempo para verificar se não há resultados inesperados depois de habilitar o ponto de extremidade V2 antes de passar para a próxima fase, na qual você aumentará o limite de tamanho do grupo. 
>[!NOTE]
>Os caminhos de arquivo/módulo podem usar uma letra da unidade diferente, dependendo do caminho de instalação fornecido ao instalar o Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fase 3 – aumentar o limite de associação de grupo 
Depois de verificar que o serviço está em execução sem resultados inesperados, você poderá continuar aumentando o limite de associação de grupo. É recomendável primeiro elevar o limite de associação a um valor ligeiramente mais alto, por exemplo, 75 mil membros, para ver os grupos maiores sincronizando com o Azure AD. Quando estiver satisfeito com os resultados, você poderá aumentar ainda mais o limite do membro.  

O limite máximo é de 250 mil membros por grupo. 

As seguintes etapas podem ser usadas para aumentar o limite de associação:  

1. Abra o editor de regras de sincronização do Azure AD 
2. No editor, escolha **Saída** para Direção 
3. Clique na regra de sincronização **Saída para AAD – Ingresso no Grupo** 
4. Clique no botão **Editar** ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Clique no botão **Sim** para desabilitar a regra padrão e criar uma cópia editável.
 ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. Na janela pop-up na página **Descrição**, defina a precedência para um valor disponível entre 1 e 99 ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Na página **Transformações**, atualize o valor **Origem** para a transformação **membro**, substituindo "50000" por um valor entre 50001 e 250000. Essa substituição aumentará o tamanho máximo de associação dos grupos que serão sincronizados com o Azure AD. Sugerimos começar com um número de 100 mil para entender o impacto que a sincronização de grandes grupos terá em seu desempenho de sincronização. 
 
 **Exemplo** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Clique em Salvar 
10. Abra um prompt do PowerShell 
11. Habilite o agendador de sincronização novamente 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Se o Azure AD Connect Health não estiver habilitado, altere as configurações do log de eventos dos aplicativos do Windows para arquivar os logs, em vez de substituí-los. Os logs podem ser usados para auxiliar em futuros esforços de solução de problemas. 

>[!NOTE]
> Depois de habilitar o novo ponto de extremidade, você poderá ver erros de exportação adicionais no conector do AAD com o nome ‘dn-attributes-failure’. Haverá uma entrada de log de eventos correspondente para cada erro com a ID 6949. Os erros são informativos e não indicam um problema com a instalação, mas sim que o processo de sincronização não pôde adicionar determinados membros a um grupo no Azure AD porque o próprio objeto membro não foi sincronizado com o Azure AD. 

O novo código do ponto de extremidade V2 lida com alguns tipos de erros de exportação ligeiramente diferentes de como o código V1 fazia.  Você pode ver mais das mensagens de erro informativas ao usar o ponto de extremidade V2. 

>[!NOTE]
> Ao atualizar o Azure AD Connect, verifique se as etapas na Fase 2 são executadas novamente, pois as alterações não são preservadas por meio do processo de atualização. 

Durante aumentos subsequentes no limite de membro do grupo na regra de sincronização **Saída para AAD – Ingresso no Grupo**, uma sincronização completa não é necessária, portanto, você pode optar por suprimir a sincronização completa executando o comando a seguir no PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Se você tiver grupos unificados do O365 que tenham mais de 50 mil membros, os grupos serão lidos no Azure AD Connect e, se o write-back do grupo estiver habilitado, eles serão gravados em seu AD local. 

## <a name="rollback"></a>Reversão 
Se você tiver habilitado o ponto de extremidade v2 e precisar reverter, siga estas etapas: 

1. No servidor do Azure AD Connect: a. [Opcional] Faça backup do banco de dados 
2. Abra um prompt do PowerShell como administrador:
3. Desabilite o agendador de sincronização depois de verificar que nenhuma operação de sincronização está em execução
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Alterne para o ponto de extremidade V1* 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Abra o editor de regras de sincronização do Azure AD 
6. Exclua a cópia editável da regra de sincronização **Saída para AAD – Ingresso no Grupo** 
7. Habilite a cópia padrão da regra de sincronização **Saída para AAD – Ingresso no Grupo** 
8. Abra um prompt do PowerShell como administrador 
9. Habilite o agendador de sincronização novamente 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Ao voltar dos pontos de extremidade de V2 para V1, os grupos sincronizados com mais de 50 mil membros serão excluídos após a execução de uma sincronização completa, tanto para os grupos do AD provisionados quanto para os grupos unificados do Azure AD e do O365 provisionados no AD. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes  
**P: Um cliente pode usar esse recurso na produção?**   
</br>Sim, isso pode ser usado em ambientes de produção, com a limitação mencionada anteriormente.
 
**P: Com quem o cliente poderá entrar em contato quando algo der errado?**   
</br>Se precisar de suporte ao usar esse recurso, você deverá abrir um caso de suporte. 
 
**P: Posso esperar atualizações frequentes na versão prévia pública?**   
</br>Há um grau limitado de alterações em andamento durante uma versão prévia pública. Você deve avaliar esse risco ao implantar recursos em versão prévia pública na produção.  
 
**P: Qual o tempo para o próximo marco?**   
</br>Os recursos em versão prévia pública podem ser retirados e possivelmente reprojetados antes de outras etapas.  
 
## <a name="next-steps"></a>Próximas etapas

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
