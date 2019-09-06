---
title: Novo sistema de contagem de referência no Xamarin. iOS
description: Este documento descreve o sistema de contagem de referência aprimorada do Xamarin, habilitado em todos os aplicativos Xamarin. iOS por padrão.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 8b1b82a1707a4aa58ef1e3dadbaeb79ada1ad6a1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291880"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Novo sistema de contagem de referência no Xamarin. iOS

O Xamarin. iOS 9.2.1 introduziu o sistema de contagem de referência aprimorado para todos os aplicativos por padrão. Ele pode ser usado para eliminar muitos problemas de memória que eram difíceis de rastrear e corrigir em versões anteriores do Xamarin. iOS.

## <a name="enabling-the-new-reference-counting-system"></a>Habilitando o novo sistema de contagem de referência

A partir do Xamarin 9.2.1, o novo sistema de contagem de referência é habilitado para **todos os** aplicativos por padrão.

Se você estiver desenvolvendo um aplicativo existente, poderá verificar o arquivo. csproj para garantir que todas as ocorrências de `MtouchUseRefCounting` estejam definidas como `true`, como abaixo:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Se ele estiver definido como `false` seu aplicativo não estará usando as novas ferramentas.

### <a name="using-older-versions-of-xamarin"></a>Usando versões mais antigas do Xamarin

O Xamarin. iOS 7.2.1 e superior apresenta uma visualização aprimorada do nosso novo sistema de contagem de referência.

**API Clássica:**

Para habilitar esse novo sistema de contagem de referência, marque a caixa de seleção **usar a extensão de contagem de referência** encontrada na guia **avançado** das **Opções de Build do IOS**do seu projeto, conforme mostrado abaixo: 

[![](newrefcount-images/image1.png "Habilitar o novo sistema de contagem de referência")](newrefcount-images/image1.png#lightbox)

Observe que essas opções foram removidas em versões mais recentes do Visual Studio para Mac.

 **[API Unificada:](~/cross-platform/macios/unified/index.md)**

 A nova extensão de contagem de referência é necessária para o API Unificada e deve ser habilitada por padrão. As versões mais antigas do IDE podem não ter esse valor marcado automaticamente e talvez você precise fazer uma verificação por conta própria.


> [!IMPORTANT]
> Uma versão anterior desse recurso já existe desde o MonoTouch 5,2, mas só estava disponível para o **SGen** como uma visualização experimental. Essa nova versão aprimorada agora também está disponível para o coletor de lixo **Boehm** .


Historicamente, houve dois tipos de objetos gerenciados pelo Xamarin. iOS: aqueles que eram meramente um wrapper em um objeto nativo (objetos pares) e aqueles que ampliaram ou incorporaram novas funcionalidades (objetos derivados) – geralmente mantendo o estado de memória extra. Anteriormente, era possível aumentar um objeto de mesmo nível com o estado (por exemplo, adicionando um C# manipulador de eventos), mas que permitimos que o objeto passasse sem referência e, em seguida, coletado. Isso pode causar uma falha posteriormente (por exemplo, se o tempo de execução de Objective-C tiver sido chamado de volta para o objeto gerenciado).

O novo sistema atualiza automaticamente os objetos pares em objetos que são gerenciados pelo tempo de execução quando armazenam informações adicionais.

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

Sem a extensão de contagem de referência, esse código `cell` falharia porque se tornasse `TouchDown` uma coleção e, portanto, seu delegado, que será convertido em um ponteiro pendente.

A extensão de contagem de referência garante que o objeto gerenciado permaneça ativo e impeça sua coleção, desde que o objeto nativo seja retido pelo código nativo.

O novo sistema também elimina a necessidade de *mais* campos particulares de backup usados em associações – que é a abordagem padrão para manter a instância ativa. O vinculador gerenciado é inteligente o suficiente para remover todos os campos *desnecessários* dos aplicativos que usam a nova extensão de contagem de referência.

Isso significa que cada instância de objeto gerenciado consome menos memória do que antes. Ele também resolve um problema relacionado em que alguns campos de apoio poderiam manter referências que não eram mais necessárias pelo tempo de execução Objective-C, dificultando a recuperação de alguma memória.
