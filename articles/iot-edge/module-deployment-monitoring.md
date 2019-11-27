---
title: Implantação automática para grupos de dispositivos - Azure IoT Edge | Microsoft Docs
description: Use implantações automáticas do Azure IoT Edge para gerenciar grupos de dispositivos com base em marcas compartilhadas
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456737"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala

Os dispositivos do Azure IoT Edge seguem um [ciclo de vida do dispositivo](../iot-hub/iot-hub-device-management-overview.md) semelhante ao de outros tipos de dispositivos IoT:

1. Provisionar novos dispositivos IoT Edge, um dispositivo com um sistema operacional de geração de imagens e instalar [runtime do IoT Edge](iot-edge-runtime.md).
2. Configurar os dispositivos a serem executados [módulos do IoT Edge](iot-edge-modules.md)e, em seguida, monitorar sua integridade. 
3. Por fim, desative dispositivos quando eles forem substituídos ou ficarem obsoletos.  

O Azure IoT Edge fornece duas maneiras de configurar os módulos para execução nos dispositivos do IoT Edge: uma para desenvolvimento e iterações rápidas em um único dispositivo (usada nos [tutoriais](tutorial-deploy-function.md) do Azure IoT Edge) e outra para gerenciar grandes conjuntos de dispositivos do IoT Edge. Essas duas abordagens estão disponíveis no Portal do Azure e programaticamente. Para direcionar grupos ou um grande número de dispositivos, você pode especificar em quais dispositivos deseja implantar seus módulos usando [marcas](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) no dispositivo gêmeo. As etapas a seguir falam sobre uma implantação em um grupo de dispositivos do estado de Washington identificado pela propriedade de marcas. 

Este artigo se concentra nos estágios de configuração e monitoramento de frotas de dispositivos, coletivamente chamados de implantações automáticas do IoT Edge. As etapas de implantação geral são as seguintes: 

1. Um operador define uma implantação que descreve um conjunto de módulos e os dispositivos de destino. Cada implantação tem um manifesto de implantação que reflete essas informações. 
2. O serviço Hub IoT se comunica com todos os dispositivos de destino para configurá-los com os módulos desejados. 
3. O serviço do Hub IoT recupera o status dos dispositivos do IoT Edge e as disponibiliza para o operador.  Por exemplo, um operador pode ver quando um dispositivo de borda não é configurado com êxito ou se um módulo falha durante o tempo de execução. 
4. A qualquer momento, novos dispositivos IoT Edge que atendem às condições de destino são configurados para a implantação. Por exemplo, uma implantação que se destina a todos os dispositivos IoT Edge em Washington configura automaticamente um novo dispositivo IoT Edge assim que ele é configurado e adicionado ao grupo de dispositivos de Washington. 
 
Este artigo descreve cada componente envolvido na configuração e no monitoramento de uma implantação. Para obter instruções de criação e atualização de uma implantação, confira [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Implantação

Uma implantação automática do IoT Edge atribui imagens do módulo do IoT Edge para execução como instâncias em um conjunto de dispositivos IoT Edge de destino. Ele funciona com a configuração de um manifesto de implantação do IoT Edge para incluir uma lista de módulos com os parâmetros de inicialização. Uma implantação pode ser atribuída a um único dispositivo (com base na ID do dispositivo) ou a um grupo de dispositivos (com base nas marcas). Quando um dispositivo IoT Edge recebe um manifesto de implantação, ele baixa e instala as imagens de contêiner dos repositórios do respectivo contêiner e as configura adequadamente. Depois que uma implantação é criada, um operador pode monitorar o status da implantação para ver se os dispositivos de destino estão configurados corretamente.

Somente os dispositivos do IoT Edge podem ser configurados com uma implantação. Os pré-requisitos seguintes devem estar no dispositivo antes de ele poder receber a implantação:

* O sistema operacional base
* Um sistema de gerenciamento de contêiner, como Moby ou Docker
* Provisionamento do runtime do IoT Edge 

### <a name="deployment-manifest"></a>Manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve os módulos a serem configurados nos dispositivos IoT Edge de destino. Ele contém os metadados de configuração para todos os módulos, incluindo os módulos do sistema necessários (especificamente o agente do IoT Edge e o hub IoT Edge).  

Os metadados de configuração para cada módulo incluem: 

* Versão 
* Digite 
* Status (por exemplo, em execução ou parado) 
* Política de reinicialização 
* Imagem ou registro de contêiner
* Rotas para dados de entrada e saída 

Se o módulo de imagem for armazenado em um registro de contêiner privado, o agente IoT Edge segura as credenciais de registro. 

### <a name="target-condition"></a>Condição de destino

A condição de destino é avaliada continuamente durante o tempo de vida da implantação. Os novos dispositivos que atendem aos requisitos são incluídos e qualquer dispositivo existente não é removido. A implantação é reativada se o serviço detectar qualquer alteração de condição de destino. 

Por exemplo, você tem uma implantação A com uma condição de destino tags.environment = 'prod'. Quando você iniciar a implantação, há 10 dispositivos de produção. Os módulos são instalados com êxito nesses 10 dispositivos. O Status do agente IoT Edge é mostrado como 10 dispositivos totais, 10 respostas bem sucedidas, 0 respostas com falhas e 0 respostas pendentes. Agora, adicione mais 5 dispositivos com tags.environment = 'prod'. O serviço detecta a alteração e o Status do agente de IoT Edge se torna 15 dispositivos totais, 10 respostas bem sucedidas, 0 respostas com falhas e 5 respostas pendentes ao tentar implantar em cinco novos dispositivos.

Use qualquer condição booliana em marcas de dispositivo gêmeo ou deviceId para selecionar os dispositivos de destino. Se você quiser usar a condição com marcas, você precisa adicionar a seção "tags":{} no dispositivo gêmeo no mesmo nível como propriedades. [Saiba mais sobre as marcas em dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md)

Exemplos de condição de destino:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' E tags.location = 'westus'
* tags.environment = 'prod' OU tags.location = 'westus'
* tags.operator = 'John' E tags.environment = 'prod' NÃO deviceId = 'linuxprod1'

Encontre aqui algumas restrições ao construir uma condição de destino:

* Em dispositivo gêmeo, você só pode compilar uma condição de destino usando marcas ou deviceId.
* Aspas duplas não são permitidas em nenhuma parte da condição de destino. Use aspas simples.
* Aspas simples representam os valores da condição de destino. Portanto, você deve usar as aspas simples com outras aspas simples se isso fizer parte do nome do dispositivo. Por exemplo, para um dispositivo de destino chamado `operator'sDevice`, escreva `deviceId='operator''sDevice'`.
* Números, letras e os caracteres a seguir são permitidos nos valores de condição de destino: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implantação deve ser aplicada a um dispositivo de destino em relação a outras implantações. Uma prioridade de implantação é um inteiro positivo, e os maiores números indicam as prioridades mais altas. Se um dispositivo IoT Edge é afetado por mais de uma implantação, a implantação com a prioridade mais alta será aplicada.  Implantações com prioridades inferiores não são aplicadas, nem mescladas.  Se um dispositivo for direcionado a duas ou mais implantações com prioridade igual, a implantação criada mais recentemente (determinada pelo carimbo de data/hora de criação) se aplicará.

### <a name="labels"></a>Rótulos 

Rótulos são pares de chave/valor de cadeia de caracteres que você pode usar para filtrar e agrupar as implantações. Uma implantação pode ter vários rótulos. Os rótulos são opcionais e não afetam a configuração real dos dispositivos IoT Edge. 

### <a name="deployment-status"></a>Status da Implantação

Uma implantação pode ser monitorada para determinar se ela foi aplicada com êxito a um dispositivo IoT Edge de destino.  Um dispositivo de borda de destino aparecerá em uma ou mais das seguintes categorias de status: 

* **Destino** mostra os dispositivos IoT Edge que correspondem à implantação da condição de destino.
* **Real** mostra os dispositivos IoT Edge de destino que não são o destino de outra implantação de prioridade mais alta.
* **Íntegro** mostra os dispositivos IoT Edge que indicaram ao serviço que os módulos foram implantados com êxito. 
* **Não Íntegro** mostra os dispositivos IoT Edge que indicaram ao serviço que um ou mais módulos não foram implantados com êxito. Para investigar o erro mais detalhadamente, conecte-se remotamente a esses dispositivos e exiba os arquivos de log.
* **Desconhecido** mostra os dispositivos IoT Edge que não indicaram status referentes a essa implantação. Para investigar melhor, exiba arquivos de log e informações de serviço.

## <a name="phased-rollout"></a>Distribuição em fases 

Uma distribuição em fases é um processo geral no qual um operador implanta alterações em um conjunto maior de dispositivos IoT Edge. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala.  

Uma distribuição em fases é executada nas seguintes fases e etapas: 

1. Estabeleça um ambiente de teste de dispositivos IoT Edge provisionando-os e definindo uma marca de gêmeo de dispositivo como `tag.environment='test'`. O ambiente de teste deve espelhar o ambiente de produção que a implantação eventualmente terá como destino. 
2. Crie uma implantação, incluindo as configurações e os módulos desejados. A condição de destino deve ter como destino o ambiente de teste do dispositivo IoT Edge.   
3. Valide a nova configuração do módulo no ambiente de teste.
4. Atualize a implantação para incluir um subconjunto de dispositivos IoT Edge de produção, adicionando uma nova marca à condição de destino. Além disso, verifique se a prioridade da implantação é maior do que a de outras implantações atualmente destinadas a esses dispositivos 
5. Verifique se a implantação foi bem-sucedida nos dispositivos IoT de destino exibindo o status da implantação.
6. Atualize a implantação para ter como destino todos os dispositivos IoT Edge de produção restantes.

## <a name="rollback"></a>Reversão

As implantações podem ser revertidas em se você receber erros ou problemas de configuração.  Como uma implantação define a configuração de módulo absoluta para um dispositivo IoT Edge, uma implantação adicional também deve ser direcionada para o mesmo dispositivo em uma prioridade mais baixa, mesmo se a meta for remover todos os módulos.  

Execute as reversões na sequência abaixo: 

1. Confirme que uma segunda implantação também tem como destino o mesmo conjunto de dispositivos. Se a meta da reversão é remover todos os módulos, a segunda implantação não deve incluir todos os módulos. 
2. Modifique ou remova a expressão de condição de destino da implantação que você deseja reverter para que os dispositivos não atendam à condição de destino.
3. Verifique se a reversão teve êxito exibindo o status da implantação.
   * A implantação revertida não deve mostrar o status dos dispositivos que foram revertidos.
   * A segunda implantação agora deve incluir o status da implantação dos dispositivos que foram revertidos.


## <a name="next-steps"></a>Próximas etapas

* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
* Saiba mais sobre outros conceitos de IoT Edge, como o [runtime do IoT Edge](iot-edge-runtime.md) e os [módulos do IoT Edge](iot-edge-modules.md).

