---
title: Política de suporte do cluster do Red Hat OpenShift no Azure 4
description: Entenda os requisitos da política de suporte para o Red Hat OpenShift 4.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f496d0f38452fa7cf64bc9eef370bd0b2116cfa5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049954"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Política de suporte do Red Hat OpenShift no Azure

Determinadas configurações dos clusters do Red Hat OpenShift no Azure 4 podem afetar a compatibilidade do cluster. O Red Hat OpenShift no Azure 4 permite que os administradores do cluster façam alterações em componentes internos do cluster, mas nem todas as alterações são compatíveis. A política de suporte abaixo compartilha as modificações que violam a política e anulam o suporte da Microsoft e do Red Hat.

> [!NOTE]
> Os recursos marcados como Visualização da tecnologia na plataforma do contêiner do OpenShift não são compatíveis com o Red Hat OpenShift no Azure.

## <a name="cluster-configuration-requirements"></a>Requisitos de configuração do cluster

* Todos os operadores do cluster do OpenShift devem permanecer em um estado gerenciado. A lista de operadores do cluster pode ser retornada executando `oc get clusteroperators`.
* Não remova nem modifique os serviços Prometheus e Alertmanager do cluster.
* Não remova as regras de Alertmanager do serviço.
* Não remova nem modifique o registro em log do serviço do Red Hat OpenShift no Azure (pods mdsd).
* Não remova nem modifique o segredo de pull do cluster “arosvc.azurecr.io”.
* Todas as máquinas virtuais do cluster devem ter acesso direto à Internet de saída, pelo menos para os pontos de extremidade do Azure Resource Manager (ARM) e do registro em log do serviço (Geneva).  Não há suporte para nenhuma forma de proxy HTTPS.
* Não modifique a configuração de DNS da rede virtual do cluster. Use o resolvedor DNS do Azure padrão.
* Não substitua nenhum dos objetos MachineConfig do cluster (por exemplo, a configuração do kubelet) de forma alguma.
* O serviço do Red Hat OpenShift no Azure acessa seu cluster por meio do serviço de Link Privado.  Não remova nem modifique o acesso ao serviço.
* Não há suporte para nós de computação não RHCOS. Por exemplo, você não pode usar um nó de computação RHEL.

## <a name="supported-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais compatíveis

O Red Hat OpenShift no Azure 4 oferece suporte a instâncias de nó de trabalho nos seguintes tamanhos de máquina virtual:

### <a name="general-purpose"></a>Propósito geral

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Otimizado para memória

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Otimizado para computação

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Nós mestres

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
