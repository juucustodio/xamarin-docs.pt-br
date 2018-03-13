---
title: "Novo sistema de contagem de referência"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 43b357eecb0974884db645a0b2e5c8467ddf3b5d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="new-reference-counting-system"></a>Novo sistema de contagem de referência

Xamarin 9.2.1 introduziu a referência avançada sistema para todos os aplicativos de contagem por padrão. Ele pode ser usado para eliminar muitos problemas de memória que eram difíceis de rastrear e corrigir em versões anteriores do xamarin.

## <a name="enabling-the-new-reference-counting-system"></a>Habilitando a novo sistema de contagem de referência

A partir do Xamarin 9.2.1 a ref nova contagem de sistema está habilitada para **todos os** aplicativos por padrão.

Se você estiver desenvolvendo um aplicativo existente, você pode verificar o arquivo. csproj para garantir que todas as ocorrências de `MtouchUseRefCounting` são definidos como `true`, como abaixo:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Se for definido como `false` seu aplicativo não usará a nova ferramenta.

### <a name="using-older-versions-of-xamarin"></a>Usando versões antigas do Xamarin

Xamarin 7.2.1 e acima apresenta uma visualização avançada do nosso novo sistema de contagem de referência.

**API clássico:**

Para habilitar esse novo sistema de contagem de referência, verifique o **usar a extensão de contagem de referência** caixa de seleção encontrado no **avançado** guia de seu projeto **iOS opções de compilação** , conforme mostrado abaixo: 

[![](newrefcount-images/image1.png "Ativar o novo sistema de contagem de referência")](newrefcount-images/image1.png#lightbox)

Observe que essas opções foram removidas em versões mais recentes do Visual Studio para Mac.

 **[API unificada:](~/cross-platform/macios/unified/index.md)**

 A nova extensão de contagem de referência é necessária para a API unificada e deve ser habilitada por padrão. Versões mais antigas do seu IDE podem não ter esse valor marcada automaticamente e talvez seja necessário colocar uma verificação por ele.

    
> [!IMPORTANT]
> **Observação:** uma versão anterior deste recurso já existe como MonoTouch 5.2 mas estava disponível apenas para **sgen** como uma visualização experimental. Esta versão nova e aprimorada agora também está disponível para o **Boehm** coletor de lixo.


Historicamente tem havido dois tipos de objetos gerenciados pelo xamarin: aqueles que foram simplesmente um wrapper em torno de um objeto nativo (objetos de ponto a ponto) e aqueles que estendido ou incorporado a nova funcionalidade (objetos derivados) - normalmente, mantendo o estado na memória extra. Anteriormente era possível que podemos pode ampliar um objeto de ponto a ponto com estado (por exemplo, adicionando um manipulador de eventos em c#), mas que deixamos que o objeto vá sem referência e, em seguida, coletados. Isso pode causar uma falha posteriormente (por exemplo, se o tempo de execução Objective-C chamado de volta para o objeto gerenciado).

O novo sistema atualiza automaticamente objetos pares em objetos que são gerenciados pelo tempo de execução quando eles armazenam informações extras.

Isso resolve várias falhas que ocorreram em situações como esta:

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

Sem a extensão de contagem de referência esse código deve falhar porque `cell` torna-se na coleção e, portanto seu `TouchDown` delegado, que serão convertidos em um ponteiro pendente.

A extensão de contagem de referência garante que o objeto gerenciado fica ativo e impede sua coleta, desde que o objeto nativo é retido pelo código nativo.

O novo sistema também remove a necessidade de *mais* privada fazendo campos usados em associações - que é o método padrão para manter a instância ativa. O vinculador gerenciado é inteligente o bastante para remover todos os *desnecessárias* contagem de campos de aplicativos usando a nova referência de extensão.

Isso significa que cada instância do objeto gerenciado consome menos memória do que antes. Ele também resolve um problema relacionado, onde alguns campos de backup deve conter referências que não foram necessárias mais pelo tempo de execução Objective-C, tornando difícil recuperar memória.
