---
title: Dados personalizados e máquinas virtuais do Azure
description: Detalhes sobre o uso de dados personalizados e cloud-init em máquinas virtuais do Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759128"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dados personalizados e Cloud-Init em Máquinas Virtuais do Azure

## <a name="what-is-custom-data"></a>O que são dados personalizados?

Os clientes muitas vezes perguntam como podem injetar um script ou outros metadados em uma máquina virtual do Microsoft Azure no momento da provisão.  Em outras nuvens, esse conceito é frequentemente referido como dados do usuário.  No Microsoft Azure, temos um recurso semelhante chamado dados personalizados. 

Os dados personalizados só são disponibilizados para a VM durante a primeira inicial de inicialização/configuração inicial, chamamos isso de 'provisionamento'. Provisionamento é o processo onde os parâmetros vm create (por exemplo, nome de host, nome de usuário, senha, certificados, dados personalizados, chaves etc.) são disponibilizados para a VM e um agente de provisionamento os processa, como o [Agente Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) e [o cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Passando dados personalizados para a VM
Para usar dados personalizados, você deve basear64 codificar o conteúdo primeiro antes de passá-lo para a API, a menos que você esteja usando uma ferramenta CLI que faz a conversão para você, como AZ CLI. O tamanho não pode exceder 64 KB.

Na CLI, você pode passar seus dados personalizados como um arquivo, e ele será convertido para base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

No Azure Resource Manager (ARM), há uma [função base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

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

## <a name="processing-custom-data"></a>Processamento de dados personalizados
Os agentes de provisionamento instalados nas VMs lidam com a interligação com a plataforma e a colocam no sistema de arquivos. 

### <a name="windows"></a>Windows
Os dados personalizados são colocados em *%SYSTEMDRIVE%\AzureData\CustomData.bin* como um arquivo binário, mas não são processados. Se você deseja processar este arquivo, você precisará construir uma imagem personalizada e escrever código para processar o CustomData.bin.

### <a name="linux"></a>Linux  
Nos SO's do Linux, os dados personalizados são passados para o VM através do arquivo ovf-env.xml, que é copiado para o diretório */var/lib/waagent* durante o provisionamento.  Versões mais recentes do Microsoft Azure Linux Agent também copiarão os dados codificados pelo base64 para */var/lib/waagent/CustomData,* bem como para conveniência.

Atualmente, o Azure suporta dois agentes de provisionamento:
* Agente Linux - Por padrão, o agente não processará dados personalizados, você precisará construir uma imagem personalizada com ele ativado. As configurações relevantes, conforme a [documentação](https://github.com/Azure/WALinuxAgent#configuration) são:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Quando você habilita dados personalizados e executa um script, ele atrasará o relatório vm que está pronto ou que o provisionamento foi bem sucedido até que o script tenha sido concluído. Se o script exceder o subsídio total de tempo de provisionamento da VM de 40 min, o VM Create falhará. Observe: se o script não for executado ou erros durante a execução, ele não será considerado uma falha fatal de provisionamento, você precisará criar um caminho de notificação para alertá-lo sobre o estado de conclusão do script.

Para solucionar problemas de execução de dados personalizados, revise */var/log/waagent.log*

* cloud-init - Por padrão processará dados personalizados por padrão, o cloud-init aceita vários formatos de dados [personalizados,](https://cloudinit.readthedocs.io/en/latest/topics/format.html) como configuração de nuvem,scripts etc. Semelhante ao Agente Linux, quando a nuvem-init processa os dados personalizados. Se houver erros durante a execução do processamento de configuração ou scripts, não é considerada uma falha fatal de provisionamento, e você precisará criar um caminho de notificação para alertá-lo sobre o estado de conclusão do script. No entanto, diferente do Agente Linux, o cloud-init não espera que as configurações de dados personalizados do usuário sejam concluídas antes de informar à plataforma que a VM está pronta. Para obter mais informações sobre cloud-init no azure, revise a [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Para solucionar problemas na execução de dados personalizados, revise a [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)de solução de problemas .


## <a name="faq"></a>Perguntas frequentes
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Posso atualizar dados personalizados após a criação da VM?
Para VMs únicos, os dados personalizados no modelo VM não podem ser atualizados, mas para o VMSS, você pode atualizar dados personalizados vMSS via API REST (não aplicável para clientes PS ou AZ CLI). Quando você atualiza dados personalizados no modelo VMSS:
* As instâncias existentes no VMSS não receberão os dados personalizados atualizados, apenas até que sejam recapturados.
* As instâncias existentes no VMSS que são atualizadas não receberão os dados personalizados atualizados.
* Novas instâncias receberão os novos dados personalizados.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Posso colocar valores sensíveis em dados personalizados?
Aconselhamos **não** armazenar dados confidenciais em dados personalizados. Para obter mais informações, consulte [as práticas recomendadas de segurança e criptografia do Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Os dados personalizados são disponibilizados no IMDS?
Não, este recurso não está disponível no momento.
