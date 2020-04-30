---
title: Dados personalizados e máquinas virtuais do Azure
description: Detalhes sobre como usar dados personalizados e inicialização de nuvem em máquinas virtuais do Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759128"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dados personalizados e inicialização de nuvem em máquinas virtuais do Azure

## <a name="what-is-custom-data"></a>O que são dados personalizados?

Os clientes geralmente perguntam como eles podem injetar um script ou outros metadados em uma máquina virtual Microsoft Azure no momento do provisionamento.  Em outras nuvens, esse conceito é geralmente chamado de dados do usuário.  No Microsoft Azure, temos um recurso semelhante chamado dados personalizados. 

Os dados personalizados só são disponibilizados para a VM durante a primeira configuração de inicialização/inicial, chamamos isso de ' provisionamento '. O provisionamento é o processo no qual os parâmetros de criação de VM (por exemplo, nome de host, nome de usuário, senha, certificados, dados personalizados, chaves etc.) são disponibilizados para a VM e um agente de provisionamento os processa, como o [agente do Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) e a [inicialização de nuvem](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Passando dados personalizados para a VM
Para usar dados personalizados, você deve codificar o conteúdo em base64 primeiro antes de passá-lo para a API, a menos que esteja usando uma ferramenta CLI que faça a conversão para você, como AZ CLI. O tamanho não pode exceder 64 KB.

Na CLI, você pode passar seus dados personalizados como um arquivo e ele será convertido em base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Em Azure Resource Manager (ARM), há uma [função Base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Processando dados personalizados
Os agentes de provisionamento instalados nas VMs manipulam a interface com a plataforma e o colocando no sistema de arquivos. 

### <a name="windows"></a>Windows
Os dados personalizados são colocados em *%systemdrive%\AzureData\CustomData.bin* como um arquivo binário, mas não são processados. Se você quiser processar esse arquivo, será necessário criar uma imagem personalizada e escrever o código para processar o CustomData. bin.

### <a name="linux"></a>Linux  
No SO Linux, os dados personalizados são passados para a VM por meio do arquivo OVF-env. xml, que é copiado para o diretório */var/lib/waagent* durante o provisionamento.  As versões mais recentes do agente do Microsoft Azure Linux também copiarão os dados codificados em base64 para */var/lib/waagent/CustomData* , para sua conveniência.

Atualmente, o Azure dá suporte a dois agentes de provisionamento:
* Agente do Linux – por padrão, o agente não processará dados personalizados, você precisará criar uma imagem personalizada com ela habilitada. As configurações relevantes, de acordo com a [documentação](https://github.com/Azure/WALinuxAgent#configuration) são:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Quando você habilitar dados personalizados e executar um script, ele atrasará o relatório de VM pronto ou o provisionamento terá êxito até que o script seja concluído. Se o script exceder a provisão total do tempo de provisionamento da VM de 40 minutos, a criação da VM falhará. Observe que, se o script não for executado, ou erros durante a execução, não será considerado uma falha de provisionamento fatal, você precisará criar um caminho de notificação para alertá-lo quanto ao estado de conclusão do script.

Para solucionar problemas de execução de dados personalizados, examine */var/log/waagent.log*

* Cloud-init-por padrão processará dados personalizados por padrão, Cloud-init aceita [vários formatos](https://cloudinit.readthedocs.io/en/latest/topics/format.html) de dados personalizados, como configuração de Cloud-init, scripts, etc. Semelhante ao agente do Linux, quando Cloud-init processa os dados personalizados. Se houver erros durante a execução do processamento de configuração ou scripts, não será considerada uma falha de provisionamento fatal e você precisará criar um caminho de notificação para alertá-lo para o estado de conclusão do script. No entanto, diferente do agente do Linux, o Cloud-init não aguarda a conclusão das configurações de dados personalizados do usuário antes de relatar para a plataforma que a VM está pronta. Para obter mais informações sobre a inicialização de nuvem no Azure, examine a [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Para solucionar problemas de execução de dados personalizados, examine a [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)de solução de problemas.


## <a name="faq"></a>Perguntas frequentes
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Posso atualizar dados personalizados depois que a VM tiver sido criada?
Para VMs únicas, os dados personalizados no modelo de VM não podem ser atualizados, mas, para VMSS, você pode atualizar os dados personalizados do VMSS por meio da API REST (não aplicável a clientes PS ou AZ CLI). Quando você atualiza dados personalizados no modelo VMSS:
* As instâncias existentes no VMSS não receberão os dados personalizados atualizados, somente até que eles tenham a imagem recriada.
* As instâncias existentes no VMSS que são atualizadas não receberão os dados personalizados atualizados.
* Novas instâncias receberão os novos dados personalizados.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Posso inserir valores confidenciais em dados personalizados?
Aconselhamos **não** armazenar dados confidenciais em dados personalizados. Para obter mais informações, consulte [práticas recomendadas de segurança e criptografia do Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Os dados personalizados são disponibilizados no IMDS?
Não, esse recurso não está disponível no momento.
