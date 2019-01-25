---
title: Nova referência de contagem de sistema no xamarin. IOS
description: Este documento descreve o sistema, habilitado em todos os aplicativos xamarin. IOS, por padrão de contagem de referência aprimorada do Xamarin.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 8c7b1a88284156cb5d4261f18d5659ed66dfaf64
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879323"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Nova referência de contagem de sistema no xamarin. IOS

Xamarin. IOS 9.2.1 introduziu a contagem de sistema para todos os aplicativos por padrão de referência aprimorada. Ele pode ser usado para eliminar muitos problemas de memória que eram difíceis de rastrear e corrigir em versões anteriores do xamarin. IOS.

## <a name="enabling-the-new-reference-counting-system"></a>Habilitando a sistema de contagem de referência novos

A partir do Xamarin 9.2.1 ref novo sistema de contagem está habilitada para **todos os** aplicativos por padrão.

Se você estiver desenvolvendo um aplicativo existente, você pode verificar o arquivo. csproj para garantir que todas as ocorrências de `MtouchUseRefCounting` são definidos como `true`, como abaixo:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Se ele for definido como `false` seu aplicativo não estará usando as novas ferramentas.

### <a name="using-older-versions-of-xamarin"></a>Usando versões mais antigas do Xamarin

Xamarin. IOS 7.2.1 e acima apresenta uma visualização avançada de nosso sistema de contagem de referência novos.

**API clássica:**

Para habilitar esse novo sistema de contagem de referência, verifique as **usar a extensão de contagem de referência** caixa de seleção encontrado na **avançado** guia do seu projeto **opções de Build do iOS** , conforme mostrado abaixo: 

[![](newrefcount-images/image1.png "Habilitar o novo sistema de contagem de referência")](newrefcount-images/image1.png#lightbox)

Observe que essas opções foram removidas em versões mais recentes do Visual Studio para Mac.

 **[API unificada:](~/cross-platform/macios/unified/index.md)**

 A nova extensão de contagem de referência é necessária para a API unificada e deve ser habilitada por padrão. Versões mais antigas do seu IDE podem não ter esse valor verificado automaticamente e você terá que colocar uma verificação por ele.

    
> [!IMPORTANT]
> Depois de uma versão anterior desse recurso em torno do que o MonoTouch 5.2, mas só estava disponível para **sgen** como uma versão prévia experimental. Essa versão nova e aprimorada agora também está disponível para o **Boehm** coletor de lixo.


Historicamente tem havido dois tipos de objetos gerenciados pelo xamarin. IOS: aqueles que foram simplesmente um wrapper em torno de um objeto nativo (objetos de ponto a ponto) e aquelas que estendido ou incorporado a nova funcionalidade (objetos derivados) - normalmente, mantendo o estado na memória extra. Anteriormente, era possível que podemos pode incrementar um objeto de ponto a ponto com estado (por exemplo, adicionando um C# manipulador de eventos), mas que deixamos o objeto vá não referenciadas e, em seguida, coletados. Isso poderia causar uma falha posteriormente (por exemplo, se o tempo de execução do Objective-C chamado de volta para o objeto gerenciado).

O novo sistema atualiza automaticamente objetos pares em objetos que são gerenciados pelo tempo de execução quando eles armazenam qualquer informação extra.

Isso resolve várias falhas que ocorreram em situações como essa:

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

Sem a extensão de contagem de referência, esse código falharia porque `cell` torna-se na coleção e, portanto, seu `TouchDown` delegar, que se traduzirá em um ponteiro pendentes.

A extensão de contagem de referência garante que o objeto gerenciado fica ativo e impede que sua coleção, desde que o objeto nativo é mantido pelo código nativo.

O novo sistema também elimina a necessidade de *a maioria dos* particular de campos usados em associações - que é a abordagem padrão para manter a instância ativa de backup. O vinculador gerenciado é inteligente o suficiente para remover todos os *desnecessários* extensão de contagem de campos de aplicativos usando a nova referência.

Isso significa que cada instância de objeto gerenciado consome menos memória do que antes. Ele também resolve um problema relacionado em que alguns campos de suporte seriam manter referências que não foram necessárias mais pelo tempo de execução Objective-C, tornando difícil recuperar memória.
