---
title: Solução de problemas Azure NetApp Arquivos Erros do provedor de recursos | Microsoft Docs
description: Descreve causas, soluções e soluções para erros comuns do Provedor de Recursos de Arquivos Do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597203"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Solucionar problemas de erros do Provedor de Recursos do Azure NetApp Files 

Este artigo descreve erros comuns do Provedor de Recursos do Azure NetApp Files, suas causas, soluções e soluções (se disponíveis).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Erros comuns do provedor de recursos do Azure NetApp

***BareMetalTenantId não pode ser alterado.***  

Esse erro ocorre quando você tenta atualizar `BaremetalTenantId` ou corrigir um volume e a propriedade tem um valor alterado.

* Causa:   
Você está tentando atualizar um `BaremetalTenantId` volume e a propriedade tem um valor diferente do valor armazenado no Azure.
* Solução:   
Não inclua `BaremetalTenantId` no patch e na solicitação de atualização (put). Alternativamente, certifique-se de que `BaremetalTenantId` é o mesmo na solicitação.

***ServiceLevel não pode ser alterado.***  

Esse erro ocorre quando você tenta atualizar ou corrigir um pool de capacidade com um nível de serviço diferente quando o pool de capacidade já possui volumes nele.

* Causa:   
Você está tentando atualizar um nível de serviço do pool de capacidade quando o pool contém volumes.
* Solução:   
Exclua todos os volumes do pool de capacidade e altere o nível de serviço.
* Solução alternativa:   
Crie outro pool de capacidade e crie novamente os volumes no novo pool de capacidade.

***PoolId não pode ser alterado***  

Esse erro ocorre quando você tenta atualizar ou corrigir `PoolId` um pool de capacidade com uma propriedade alterada.

* Causa:   
Você está tentando atualizar uma `PoolId` propriedade do pool de capacidade. A `PoolId` propriedade é uma propriedade somente leitura e não pode ser alterada.
* Solução:   
Não inclua `PoolId` no patch e na solicitação de atualização (put).  Alternativamente, certifique-se de que `PoolId` é o mesmo na solicitação.

***CreationToken não pode ser alterado.***

Esse erro ocorre quando você tenta`CreationToken`alterar o caminho do arquivo ( ) após a criação do volume. O caminho`CreationToken`do arquivo ( ) deve ser definido quando o volume é criado e não pode ser alterado posteriormente.

* Causa:   
Você está tentando alterar o`CreationToken`caminho do arquivo ( ) depois que o volume foi criado, o que não é uma operação suportada. 
* Solução:   
Se não for necessário alterar o caminho do arquivo, considere remover o parâmetro da solicitação para rejeitar a mensagem de erro.
* Solução alternativa:   
Se você precisar alterar o`CreationToken`caminho do arquivo (), você pode criar um novo volume com um novo caminho de arquivo e, em seguida, migrar os dados para o novo volume.

***CreationToken deve ter pelo menos 16 caracteres.***

Esse erro ocorre quando`CreationToken`o caminho do arquivo ( ) não atende ao requisito de comprimento. O comprimento do caminho do arquivo deve ter pelo menos um caractere de comprimento.

* Causa:   
O caminho do arquivo está vazio.  Quando você cria um volume usando a API, um token de criação é necessário. Se você estiver usando o portal Azure, o caminho do arquivo será gerado automaticamente.
* Solução:   
Digite pelo menos um caractere`CreationToken`como o caminho do arquivo ( ).

***O nome de domínio não pode ser alterado.***

Esse erro ocorre quando você tenta alterar o nome de domínio no Active Directory.

* Causa:   
Você está tentando atualizar a propriedade do nome de domínio.
* Solução:    
Nenhum. Você não pode alterar o nome de domínio.
* Solução alternativa:   
Exclua todos os volumes usando a configuração Active Directory. Em seguida, exclua a configuração do Active Directory e recrie os volumes.

***Erro de valor duplicado para objeto ExportPolicy.Rules[RuleIndex].***

Esse erro ocorre quando a política de exportação não é definida com um índice único. Quando você define as políticas de exportação, todas as regras de política de exportação devem ter um índice único entre 1 e 5.

* Causa:   
A política de exportação definida não atende à exigência de regras de política de exportação. Você deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.
* Solução:   
Certifique-se de que o índice ainda não está utilizado e que ele está na faixa de 1 a 5.
* Solução alternativa:   
Use um índice diferente para a regra que você está tentando definir.

***Erro {ação} {resourceTypeName}***

Esse erro é exibido quando outro manuseio de erro falhou em lidar com o erro durante a execução de uma ação em um recurso.   Inclui texto 'Erro'. O `{action}` pode ser`getting`qualquer `creating` `updating`um `deleting`dos ( , , ou ).  O `{resourceTypeName}` é `resourceTypeName` o (por `capacityPool` `volume`exemplo, `netAppAccount`, , , e assim por diante).

* Causa:   
Este erro é uma exceção não tratada onde a causa não é conhecida.
* Solução:   
Entre em contato com o Azure Support Center para relatar o motivo detalhado nos registros.
* Solução alternativa:   
Nenhum.

***O nome do caminho do arquivo pode conter apenas letras, números e hífens (""-""").***

Esse erro ocorre quando o caminho do arquivo contém caracteres não suportados, por exemplo, um período ("."."), uma comma ("","), sublinhado ("_") ou sinal de dólar ("$").

* Causa:   
O caminho do arquivo contém caracteres não suportados, por exemplo, um período ("."), uma comma (","), sublinhado ("_") ou sinal de dólar ("$").
* Solução:   
Remova caracteres que não são letras alfabéticas, números ou hífens ("-") do caminho do arquivo que você inseriu.
* Solução alternativa:   
Você pode substituir um sublinhado por um hífen ou usar a capitalização em vez de espaços para indicar o início de novas palavras.  Por exemplo, use "NewVolume" em vez de "novo volume".

***FileSystemId não pode ser alterado.***

Esse erro ocorre quando `FileSystemId`você tenta mudar .  Mudar `FileSystemdId` não é uma operação suportada. 

* Causa:   
O ID do sistema de arquivos é definido quando o volume é criado. `FileSystemId`não pode ser alterado posteriormente.
* Solução:   
Não inclua `FileSystemId` em um patch e atualização (put) solicitação.  Alternativamente, certifique-se de que `FileSystemId` é o mesmo na solicitação.

***ActiveDirectory com id: '{string}' não existe.***

A `{string}` parte é o valor `ActiveDirectoryId` que você inseriu na propriedade para a conexão Active Directory.

* Causa:   
Quando você criou uma conta com a configuração active `ActiveDirectoryId` directory, você inseriu um valor para que isso deveria estar vazio.
* Solução:   
Não inclua `ActiveDirectoryId` na solicitação de criação (put).

***Versão api inválida.***

A versão da API não é submetida ou contém um valor inválido.

* Causa:   
O valor no parâmetro `api-version` de consulta contém um valor inválido.
* Solução:   
Use o valor correto da versão da API.  O provedor de recursos suporta muitas versões de API. O valor está no formato de yyyy-mm-dd.

***Um valor inválido '{value}' {1}foi recebido para .***

Esta mensagem indica um `RuleIndex`erro `AllowedClients` `UnixReadOnly`nos `UnixReadWrite` `Nfsv3`campos `Nfsv4`para , , , , e .

* Causa:   
A solicitação de validação de entrada falhou `RuleIndex`em `AllowedClients` `UnixReadOnly`pelo `UnixReadWrite` `Nfsv`menos um `Nfsv4`dos seguintes campos: , , , , 3 e .
* Solução:   
Certifique-se de definir todos os parâmetros necessários e não conflitantes na linha de comando. Por exemplo, você não `UnixReadOnly` `UnixReadWrite` pode definir os parâmetros e parâmetros ao mesmo tempo.
* Solução alternativa:   
Veja a solução acima.

***O {0} intervalo {1} IP {2} para vlan já está em uso***

Esse erro ocorre porque os registros internos dos intervalos IP usados têm um conflito com o endereço IP recém-atribuído.

* Causa:   
O endereço IP atribuído para a criação de volumejá está registrado.
A razão pode ser uma criação de volume falhada anteriormente.
* Solução:   
Entre em contato com o Centro de Suporte Azure.

***Valor faltante para '{property}'.***

Este erro indica que uma propriedade necessária está faltando na solicitação. A seqüência {propriedade} contém o nome da propriedade desaparecida.

* Causa:   
A solicitação de validação de entrada falhou em pelo menos uma das propriedades.
* Solução:   
Certifique-se de definir todas as propriedades necessárias e não conflitantes na solicitação, especialmente, a propriedade da mensagem de erro.

***O MountTargets não pode ser alterado.***

Esse erro ocorre quando um usuário está tentando atualizar ou corrigir o volume da propriedade MountTargets.

* Causa:   
Você está tentando atualizar `MountTargets` a propriedade de volume. A mudança desta propriedade não é suportada.
* Solução:   
Não inclua `MountTargets` em um patch e atualização (put) solicitação.  Alternativamente, certifique-se de que `MountTargets` é o mesmo na solicitação.

***Nome já em uso.***

Este erro indica que o nome do recurso já está em uso.

* Causa:   
Você está tentando criar um recurso com um nome que é usado para um recurso existente.
* Solução:   
Use um nome único ao criar o recurso.

***Caminho do arquivo já em uso.***

Este erro indica que o caminho do arquivo para o volume já está em uso.

* Causa:   
Você está tentando criar um volume com um caminho de arquivo que é o mesmo que um volume existente.
* Solução:   
Use um caminho de arquivo exclusivo ao criar o volume.

***Diga por muito tempo.***

Este erro indica que o nome do recurso não atende ao requisito máximo de comprimento.

* Causa:   
O nome do recurso é muito longo.
* Solução:   
Use um nome mais curto para o recurso.

***Caminho de arquivo muito longo.***

Este erro indica que o caminho do arquivo para o volume não atende ao requisito máximo de comprimento.

* Causa:   
O caminho do arquivo de volume é muito longo.
* Solução:   
Use um caminho de arquivo mais curto.

***Nome muito curto.***

Este erro indica que o nome do recurso não atende ao requisito de comprimento mínimo.

* Causa:   
O nome do recurso é muito curto.
* Solução:   
Use um nome mais longo para o recurso.

***Caminho de arquivo muito curto.***

Este erro indica que o caminho do arquivo de volume não atende ao requisito de comprimento mínimo.

* Causa:   
O caminho do arquivo de volume é muito curto.
* Solução:   
Aumente o comprimento do caminho do arquivo de volume.

***Aazure NetApp Files API inalcançável.***

A API do Azure conta com a API azure NetApp Files para gerenciar volumes. Este erro indica um problema com a conexão API.

* Causa:   
A API subjacente não está respondendo, resultando em um erro interno. É provável que este erro seja temporário.
* Solução:   
É provável que o problema seja temporário. O pedido deve ter sucesso depois de algum tempo.
* Solução alternativa:   
Nenhum. A API subjacente é essencial para o gerenciamento de volumes.

***Nenhum resultado de operação{0}foi encontrado para ' '.***

Este erro indica que um erro interno está impedindo que a operação seja completada.

* Causa:   
Ocorreu um erro interno e impediu que a operação se completasse.
* Solução:   
É provável que este erro seja temporário. Aguarde alguns minutos e tente novamente. Se o problema persistir, crie um ticket para que o suporte técnico investigue o problema.
* Solução alternativa:   
Espere alguns minutos e verifique se o problema persiste.

***Não é permitido misturar tipos de protocolo CIFS e NFS***

Esse erro ocorre quando você está tentando criar um Volume e há os tipos de protocolo CIFS (SMB) e NFS nas propriedades de volume.

* Causa:   
Os tipos de protocolo CIFS (SMB) e NFS são usados nas propriedades de volume.
* Solução:   
Remova um dos tipos de protocolo.
* Solução alternativa:   
Deixe a propriedade do tipo protocolo vazia ou nula.

***Número de itens: {value} para objeto: ExportPolicy.Rules[RuleIndex] está fora do intervalo min-max.***

Esse erro ocorre quando as regras da política de exportação não atendem ao requisito de intervalo mínimo ou máximo. Se você definir a política de exportação, ela deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.

* Causa:   
A política de exportação definida não atende ao intervalo necessário.
* Solução:   
Certifique-se de que o índice ainda não está utilizado e que está na faixa de 1 a 5.
* Solução alternativa:   
Não é obrigatório usar a política de exportação sobre os volumes. Você pode omitir a política de exportação inteiramente se você não precisar usar as regras da política de exportação.

***Apenas um diretório ativo permitido***

Esse erro ocorre quando você tenta criar uma configuração do Active Directory, e já existe uma para a assinatura na região. O erro também pode ocorrer quando você tenta criar mais de uma configuração do Active Directory.

* Causa:   
Você está tentando criar (não atualizar) um diretório ativo, mas um já existe.
* Solução:   
Se a configuração do Active Directory não estiver em uso, então você pode primeiro excluir a configuração existente e, em seguida, tentar novamente a operação criar.
* Solução alternativa:   
Nenhum. Apenas um Diretório Ativo é permitido.

***Operação '{operação}' não suportada.***

Este erro indica que a operação não está disponível para a assinatura ativa ou recurso.

* Causa:   
A operação não está disponível para assinatura ou recurso.
* Solução:   
Certifique-se de que a operação está inserida corretamente e que ela está disponível para o recurso e assinatura que você está usando.

***OwnerId não pode ser alterado***

Esse erro ocorre quando você tenta alterar a propriedade OwnerId do volume. Mudar o OwnerId não é uma operação suportada. 

* Causa:   
A `OwnerId` propriedade é definida quando o volume é criado. A propriedade não pode ser alterada posteriormente.
* Solução:   
Não inclua `OwnerId` em um patch e atualização (put) solicitação. Alternativamente, certifique-se de que `OwnerId` é o mesmo na solicitação.

***Piscina dos pais não encontrada***

Esse erro ocorre quando você tenta criar um volume e o pool de capacidade no qual você está criando o volume não é encontrado.

* Causa:   
O pool de capacidade onde o volume está sendo criado não é encontrado.
* Solução:   
Provavelmente o pool não foi totalmente criado ou já foi excluído no momento da criação do volume.

***A operação de patch não é suportada para este tipo de recurso.***

Esse erro ocorre quando você tenta alterar o alvo de montagem ou o instantâneo.

* Causa:   
O alvo de montagem é definido quando é criado e não pode ser alterado posteriormente.
Os instantâneos não contêm nenhuma propriedade que possa ser alterada.
* Solução:   
Nenhum. Esses recursos não têm nenhuma propriedade que possa ser alterada.

***Tamanho da piscina muito pequeno para o tamanho total do volume.***

Esse erro ocorre quando você está atualizando o tamanho do pool `usedBytes` de capacidade, e o tamanho é menor do que o valor total de todos os volumes nesse pool de capacidade.  Esse erro também pode ocorrer quando você está criando um novo volume ou redimensionando um volume existente, e o novo tamanho de volume excede o espaço livre no pool de capacidade.

* Causa:   
Você está tentando atualizar o pool de capacidade para um tamanho menor do que os Bytes usados em todos os volumes no pool de capacidade.  Ou, você está tentando criar um volume que é maior do que o espaço livre no pool de capacidade.  Alternativamente, você está tentando redimensionar um volume e o novo tamanho excede o espaço livre na piscina de capacidade.
* Solução:   
Defina o tamanho da piscina de capacidade para um valor maior ou crie um volume menor para um volume.
* Solução alternativa:   
Remova volumes suficientes para que o tamanho da piscina de capacidade possa ser atualizado para este tamanho.

***A propriedade: A localização para snapshot deve ser a mesma do Volume***

Esse erro ocorre quando você está criando um instantâneo com localização diferente do volume que possui o snapshot.

* Causa:   
Valor inválido na propriedade Localização para o snapshot.
* Solução:   
Defina string válida na propriedade Local.

***O nome {resourceType} deve ser o mesmo que o nome identificador de recursos.***

Esse erro ocorre quando você está criando um recurso, e você preenche `resourceId`a propriedade nome com outro valor que não a propriedade nome de .

* Causa:   
Valor inválido na propriedade nome quando você cria um recurso.
* Solução:   
Deixe a propriedade de nome vazia ou permita que ela use o mesmo valor da propriedade nome `resourceId`(entre a última barra invertida "/" e o ponto de interrogação "?") em .

***Tipo de protocolo {valor} não conhecido***

Esse erro ocorre quando você está criando um volume com um tipo de protocolo desconhecido.  Os valores válidos são "NFSv3", "NFSv4" e "CIFS".

* Causa:   
Você está tentando definir um valor `protocolType` inválido na propriedade de volume.
* Solução:   
Defina uma `protocolType`seqüência válida em .
* Solução alternativa:   
Definido `protocolType` como nulo.

***Os tipos de protocolo não podem ser alterados***

Esse erro ocorre quando você `ProtocolType` tenta atualizar ou corrigir um volume.  Mudar protocoloType não é uma operação suportada.

* Causa:   
A `ProtocolType` propriedade é definida quando o volume é criado.  Não pode ser atualizado.
* Solução:   
Nenhum.
* Solução alternativa:   
Crie outro volume com novos tipos de protocolo.

***Criar o recurso do tipo {resourceType} excederia a cota de {quota} recursos do tipo {resourceType} por {parentResourceType}. A contagem atual de recursos é {currentCount}, por favor, exclua alguns recursos desse tipo antes de criar um novo.***

Esse erro ocorre quando você está`NetAppAccount`tentando `CapacityPool` `Volume`criar `Snapshot`um recurso ( ou ), mas sua cota atingiu seu limite.

* Causa:   
Você está tentando criar um recurso, mas o `NetAppAccounts` limite de `CapacityPools` `NetAppAccount`cota é atingido (exemplo: por assinatura ou por ).
* Solução:   
Aumentar o limite de cotas.
* Solução alternativa:   
Exclua recursos não utilizados do mesmo tipo e crie-os novamente.

***Recebeu um valor para propriedade somente leitura '{propertyName}'.***

Esse erro ocorre quando você define um valor para uma propriedade que não pode ser alterada. Por exemplo, você não pode alterar o ID de volume.

* Causa:   
Você está tentando modificar um parâmetro (por exemplo, o ID de volume) que não pode ser alterado.
* Solução:   
Não modifique um valor para a propriedade.

***O {recurso] solicitado não foi encontrado.***

Esse erro ocorre quando você tenta referenciar um recurso inexistente, por exemplo, um volume ou instantâneo. O recurso pode ter sido excluído ou ter um nome de recurso errado.

* Causa:   
Você está tentando referenciar um recurso inexistente (por exemplo, um volume ou instantâneo) que já foi excluído ou tem um nome de recurso mal escrito.
* Solução:   
Verifique a solicitação de erros de ortografia para ter certeza de que está corretamente referenciada.
* Solução alternativa:   
Consulte a seção Solução acima.

***O nível de serviço '{volumeServiceLevel}' é maior do que o '{poolServiceLevel}'***

Esse erro ocorre quando você está criando ou atualizando um volume, e você definiu o nível de serviço para um nível mais alto do que o pool de capacidade que o contém.

* Causa:   
Você está tentando criar ou atualizar um volume com um nível de serviço mais alto do que o pool de capacidade dos pais.
* Solução:   
Defina o nível de serviço para o mesmo ou uma classificação mais baixa do que o pool de capacidade dos pais.
* Solução alternativa:   
Crie o volume em outro pool de capacidade com um nível de serviço correto. Alternativamente, exclua todos os volumes do pool de capacidade e defina o nível de serviço para o pool de capacidade para uma classificação mais alta.

***O nome do servidor SMB pode não ter mais de 10 caracteres.***

Esse erro ocorre quando você está criando ou atualizando uma configuração do Active Directory para uma conta.

* Causa:   
O comprimento do nome do servidor SMB excede 10 caracteres.
* Solução:   
Use um nome de servidor mais curto. O comprimento máximo é de 10 caracteres.
* Solução alternativa:   
Nenhum.  Veja a solução acima. 

***A sub-netId não pode ser alterada.***

Esse erro ocorre quando você `subnetId` tenta alterar o volume após a criação do volume.  `SubnetId`deve ser definido quando o volume é criado e não pode ser alterado mais tarde.

* Causa:   
Você está tentando `subnetId` alterar o volume depois que o volume foi criado, o que não é uma operação suportada. 
* Solução:   
Se a `subnetId` alteração não for necessária, considere remover o parâmetro da solicitação para descartar a mensagem de erro.
* Solução alternativa:   
Se você precisar `subnetId`alterar o , você pode `subnetId`criar um novo volume com um novo , e, em seguida, migrar os dados para o novo volume.

***O SubnetId está em formato inválido.***

Esse erro ocorre quando você tenta criar `subnetId` um `resourceId` novo volume, mas o não é um para uma sub-rede.

* Causa:   
Esse erro ocorre quando você tenta criar `subnetId` um novo `resourceId` volume, mas o não é um para uma sub-rede. 
* Solução:   
Verifique o valor `subnetId` para garantir que `resourceId` ele contenha um para a sub-rede utilizada.
* Solução alternativa:   
Nenhum. Veja a solução acima. 

***A sub-rede deve ter uma delegação 'Microsoft.NetApp/volumes'.***

Esse erro ocorre quando você está criando um volume e `Microsoft.NetApp/volumes`a sub-rede selecionada não é delegada para .

* Causa:   
Você tentou criar volume e selecionou uma sub-rede que não é delegada `Microsoft.NetApp/volumes`.
* Solução:   
Selecione outra sub-rede `Microsoft.NetApp/volumes`que é delegada a .
* Solução alternativa:   
Adicione uma delegação correta à sub-rede.

***O tipo de recurso especificado é desconhecido/não aplicável.***

Esse erro ocorre quando uma verificação de nome foi solicitada em um tipo de recurso não aplicável ou para um tipo de recurso desconhecido.

* Causa:   
A verificação de nome foi solicitada para um tipo de recurso desconhecido ou sem suporte.
* Solução:   
Verifique se o recurso que você está fazendo a solicitação é suportado ou não contém erros de ortografia.
* Solução alternativa:   
Veja a solução acima.

***Erro de arquivos do Azure NetApp desconhecido.***

A API do Azure conta com a API azure NetApp Files para gerenciar volumes. O erro indica um problema na comunicação com a API.

* Causa:   
A API subjacente está enviando um erro desconhecido. É provável que este erro seja temporário.
* Solução:   
É provável que a questão seja temporária, e o pedido deve ter sucesso após algum tempo. Se o problema persistir, crie um bilhete de apoio para que o problema seja investigado.
* Solução alternativa:   
Nenhum. A API subjacente é essencial para o gerenciamento de volumes.

***Valor recebido para uma propriedade desconhecida '{propertyName}'.***

Esse erro ocorre quando propriedades inexistentes são fornecidas para um recurso como o volume, snapshot ou alvo de montagem.

* Causa:   
A solicitação tem um conjunto de propriedades que podem ser usadas com cada recurso. Não é possível incluir propriedades inexistentes na solicitação.
* Solução:   
Certifique-se de que todos os nomes de propriedades estão soletrados corretamente e que as propriedades estão disponíveis para a assinatura e recurso.
* Solução alternativa:   
Reduza o número de propriedades definidas na solicitação para eliminar a propriedade que está causando o erro.

***A operação de atualização não é suportada para este tipo de recurso.***

Somente os volumes podem ser atualizados. Esse erro ocorre quando você tenta executar uma operação de atualização sem suporte, por exemplo, atualizando um instantâneo.

* Causa:   
O recurso que você está tentando atualizar não suporta a operação de atualização. Apenas os volumes podem ter suas propriedades modificadas.
* Solução:   
Nenhum. O recurso que você está tentando atualizar não suporta a operação de atualização. Portanto, não pode ser mudado.
* Solução alternativa:   
Para um volume, crie um novo recurso com a atualização no lugar e migre os dados.

***O volume não pode ser criado em um pool que não esteja no estado bem sucedido.***

Esse erro ocorre quando você tenta criar um volume em um pool que não esteja no estado de sucesso. Provavelmente, a operação de criação para o pool de capacidade falhou por algum motivo.

* Causa:   
O pool de capacidade que contém o novo volume está em estado de falha.
* Solução:   
Verifique se o pool de capacidade foi criado com sucesso e que não está em estado de falha.
* Solução alternativa:   
Crie um novo pool de capacidade e crie o volume no novo pool.

***O volume está sendo criado e não pode ser excluído no momento.***

Esse erro ocorre quando você tenta excluir um volume que ainda está sendo criado.

* Causa:   
Um volume ainda está sendo criado quando você tenta excluir o volume.
* Solução:   
Aguarde até que a criação do volume seja concluída e, em seguida, tente novamente a exclusão.
* Solução alternativa:   
Veja a solução acima.

***O volume está sendo excluído e não pode ser excluído no momento.***

Esse erro ocorre quando você tenta excluir um volume quando ele já está sendo excluído.

* Causa:   
Um volume já está sendo excluído quando você tenta excluir o volume.
* Solução:   
Aguarde até que a operação de exclusão atual esteja concluída.
* Solução alternativa:   
Veja a solução acima.

***O volume está sendo atualizado e não pode ser excluído no momento.***

Esse erro ocorre quando você tenta excluir um volume que está sendo atualizado.

* Causa:   
Um volume está sendo atualizado quando você tenta excluir o volume.
* Solução:   
Aguarde até que a operação de atualização seja concluída e, em seguida, tente novamente a exclusão.
* Solução alternativa:   
Veja a solução acima.

***O volume não foi encontrado ou não foi criado com sucesso.***

Esse erro ocorre quando a criação de volume falhou, e você está tentando alterar o volume ou criar um instantâneo para o volume.

* Causa:   
O volume não existe, ou a criação falhou.
* Solução:   
Verifique se você está alterando o volume correto e que a criação do volume foi bem sucedida. Ou, verifique se o volume para o que você está criando um instantâneo existe.
* Solução alternativa:   
Nenhum.  Veja a solução acima. 

***Token de criação especificado já existe***

Esse erro ocorre quando você tenta criar um volume e especifica um token de criação (caminho de exportação) para o qual um volume já existe.

* Causa:   
O token de criação (caminho de exportação) especificado durante a criação de volumejá está associado a outro volume. 
* Solução:   
Escolha um token de criação diferente.  Alternativamente, exclua o outro volume.

***O token de criação especificado é reservado***

Esse erro ocorre quando você tenta criar um volume e especifica "padrão" ou "nenhum" como o caminho do arquivo (token de criação).

* Causa:    
Você está tentando criar um volume e especificar "padrão" ou "nenhum" como o caminho do arquivo (token de criação).
* Solução:   
Escolha um caminho de arquivo diferente (token de criação).
 
***Credenciais do Active Directory estão em uso***

Esse erro ocorre quando você tenta excluir a configuração do Active Directory de uma conta onde pelo menos um volume de SMB ainda existe.  O volume de SMB foi criado usando a configuração Active Directory que você está tentando excluir.

* Causa:   
Você está tentando excluir a configuração do Active Directory de uma conta, mas pelo menos um volume de SMB ainda existe que foi criado inicialmente usando a configuração do Active Directory. 
* Solução:   
Primeiro, exclua todos os volumes de SMB que foram criados usando a configuração Active Directory.  Em seguida, tente novamente a exclusão de configuração.

***Não é possível modificar a atribuição da Unidade Organizacional se as credenciais estiverem em uso***

Esse erro ocorre quando você tenta alterar a unidade organizacional de uma configuração do Active Directory, mas pelo menos um volume de SMB ainda existe.  O volume de SMB foi criado usando a configuração do Active Directory que você está tentando excluir.

* Causa:   
Você está tentando alterar a unidade organizacional de uma configuração de Diretório Ativo.  Mas pelo menos um volume de SMB ainda existe que foi inicialmente criado usando a configuração active directory.
* Solução:   
 Primeiro, exclua todos os volumes de SMB que foram criados usando a configuração Active Directory.  Em seguida, tente novamente a exclusão de configuração. 

***Atualização do Active Directory já está em andamento***

Esse erro ocorre quando você tenta editar uma configuração do Active Directory para a qual uma operação de edição já está em andamento.

* Causa:   
Você está tentando editar uma configuração do Active Directory, mas outra operação de edição já está em andamento.
* Solução:   
Aguarde até que a operação de edição em execução esteja concluída.

***Exclua todos os volumes usando as credenciais selecionadas primeiro***

Esse erro ocorre quando você tenta excluir uma configuração do Active Directory, mas pelo menos um volume de SMB ainda existe.  O volume de SMB foi criado usando a configuração Active Directory que você está tentando excluir.

* Causa:   
Você está tentando excluir uma configuração do Active Directory, mas pelo menos um volume de SMB ainda existe que foi criado inicialmente usando a configuração active Directory.
* Solução:   
Primeiro, exclua todos os volumes de SMB que foram criados usando a configuração Active Directory.  Em seguida, tente novamente a exclusão de configuração. 

***Sem credenciais de Diretório Ativo encontradas na região***

Esse erro ocorre quando você tenta criar um volume de SMB, mas nenhuma configuração do Active Directory foi adicionada à conta da região.

* Causa:   
Você está tentando criar um volume de SMB, mas nenhuma configuração do Active Directory foi adicionada à conta. 
* Solução:   
Adicione uma configuração do Active Directory à conta antes de criar um volume de SMB.

***Não foi possível consultar o servidor DNS. Verifique se a configuração da rede está correta e se os servidores DNS estão disponíveis.***

Esse erro ocorre quando você tenta criar um volume de SMB, mas um servidor DNS (especificado na configuração do Active Directory) é inalcançável. 

* Causa:   
Você está tentando criar um volume de SMB, mas um servidor DNS (especificado na configuração do Active Directory) é inalcançável.
* Solução:   
Revise a configuração do Active Directory e certifique-se de que os endereços IP do servidor DNS estejam corretos e acessíveis.
Se não houver problemas com os endereços IP do servidor DNS, verifique se nenhum firewall está bloqueando o acesso.

***Muitos empregos simultâneos***

Esse erro ocorre quando você tenta criar um instantâneo quando três outras operações de criação de instantâneos já estão em andamento para a assinatura.

* Causa:   
Você está tentando criar um instantâneo quando três outras operações de criação de instantâneos já estão em andamento para a assinatura. 
* Solução:   
Os trabalhos de criação de instantâneos levam alguns segundos no máximo para terminar.  Aguarde alguns segundos e tente novamente a operação de criação de instantâneos.

***Não pode gerar empregos adicionais. Por favor, espere que os trabalhos em andamento terminem e tentem novamente***

Esse erro pode ocorrer quando você tenta criar ou excluir um volume em circunstâncias específicas.

* Causa:   
Você está tentando criar ou excluir um volume em circunstâncias específicas.
* Solução:   
Espere um minuto ou mais e tente novamente a operação.

***Volume já está em transição entre estados***

Esse erro pode ocorrer quando você tenta excluir um volume que está atualmente em um estado de transição (ou seja, atualmente no estado de criação, atualização ou exclusão).

* Causa:   
Você está tentando excluir um volume que está atualmente em um estado de transição.
* Solução:   
Aguarde até que a operação em execução atual (transição de estado) tenha terminado e, em seguida, tente novamente a operação.

***Falha ao dividir o novo volume do snapshot do volume de origem***

 Esse erro pode ocorrer quando você tenta criar um volume a partir de um snapshot.  

* Causa:   
Você tenta criar um volume a partir de um instantâneo e o volume termina em um estado de erro.
* Solução:   
Exclua o volume e tente novamente a operação de criação de volume do snapshot.

 
## <a name="next-steps"></a>Próximas etapas

* [Desenvolver para Azure NetApp Files com API REST](azure-netapp-files-develop-with-rest-api.md)
