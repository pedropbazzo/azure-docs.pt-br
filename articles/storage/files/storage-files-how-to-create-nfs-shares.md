---
title: Criar um compartilhamento NFS-arquivos do Azure
description: Saiba como criar um compartilhamento de arquivos do Azure que pode ser montado usando o protocolo de sistema de arquivos de rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: d5b394833dbc920612f521b01f4da88af6c3e015
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220740"
---
# <a name="how-to-create-an-nfs-share"></a>Como criar um compartilhamento NFS

Os compartilhamentos de arquivos do Azure são compartilhamentos de arquivos totalmente gerenciados que residem na nuvem. Eles podem ser acessados usando o protocolo de bloqueio de mensagens do servidor ou o protocolo NFS (sistema de arquivos de rede). Este artigo aborda a criação de um compartilhamento de arquivos que usa o protocolo NFS. Para obter mais informações sobre ambos os protocolos, consulte [protocolos de compartilhamento de arquivos do Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma [conta de armazenamento](storage-how-to-create-premium-fileshare.md)de File.

    > [!IMPORTANT]
    > Os compartilhamentos NFS só podem ser acessados de redes confiáveis. As conexões com o compartilhamento NFS devem se originar de uma das seguintes fontes:

    - [Crie um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou [restrinja o acesso ao seu ponto de extremidade público](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configure uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site a site para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md).
    - Configure o [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Se pretende usar a CLI do Azure, [instale a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Registrar o protocolo NFS 4,1

Se você estiver usando o módulo Azure PowerShell ou o CLI do Azure, Registre seu recurso usando os seguintes comandos:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>Verificar se o recurso está registrado

A aprovação de registro pode levar até uma hora. Para verificar se o registro foi concluído, use os seguintes comandos:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>Criar um compartilhamento NFS

# <a name="portal"></a>[Portal](#tab/azure-portal)

Agora que você criou uma conta de armazenamento de arquivo e configurou a rede, é possível criar um compartilhamento de arquivos NFS. O processo é semelhante à criação de um compartilhamento SMB, você seleciona **NFS** em vez de **SMB** ao criar o compartilhamento.

1. Navegue até sua conta de armazenamento e selecione **Compartilhamentos de arquivo**.
1. Selecione **+ compartilhamento de arquivos** para criar um novo compartilhamento de arquivos.
1. Nomeie o compartilhamento de arquivos, selecione uma capacidade provisionada.
1. Para **protocolo** , selecione **NFS (versão prévia)**.
1. Para o **comprimir raiz** , faça uma seleção.

    - Comprimir raiz (padrão)-o acesso para o superusuário remoto (raiz) é mapeado para UID (65534) e GID (65534).
    - Nenhum superusuário remoto de comprimir (raiz) recebe o acesso como raiz.
    - Todos os acessos ao usuário de comprimido são mapeados para UID (65534) e GID (65534).
    
1. Selecione **Criar**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Captura de tela da folha de criação de compartilhamento de arquivos":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verifique se o .NET Framework está instalado. Consulte [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Feche e reabra o console do PowerShell.

1. Instale o módulo de visualização **AZ. Storage** versão **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Para criar um compartilhamento de arquivos Premium com o módulo Azure PowerShell, use o cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar um compartilhamento de arquivos Premium com o CLI do Azure, use o comando [AZ Storage share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol NFS \
    --root-access RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um compartilhamento NFS, para usá-lo, você precisa montá-lo em seu cliente Linux. Para obter detalhes, consulte [como montar um compartilhamento NFS](storage-files-how-to-mount-nfs-shares.md).

Se você tiver problemas, consulte [solucionar problemas de compartilhamentos de arquivos NFS do Azure](storage-troubleshooting-files-nfs.md).