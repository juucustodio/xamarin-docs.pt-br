---
title: ViewPager com exibição
description: ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário do swipeable com ViewPager e PagerTabStrip, usando exibições como páginas de dados (um guia subsequente aborda como usar fragmentos para as páginas).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e4f4243c06d98eac6f3501c41b48508f260d4633
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456984"
---
# <a name="viewpager-with-views"></a>ViewPager com exibição

_ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário do swipeable com ViewPager e PagerTabStrip, usando exibições como páginas de dados (um guia subsequente aborda como usar fragmentos para as páginas)._

## <a name="overview"></a>Visão geral

Este guia é explicativo que fornece uma demonstração passo a passo sobre como usar o `ViewPager` para implementar uma galeria de imagens de árvore decídua e verde. Nesse aplicativo, o usuário passa o dedo para a esquerda e para a direita por meio de um "catálogo de árvore" para exibir imagens de árvore. Na parte superior de cada página do catálogo, o nome da árvore é listado em um `PagerTabStrip` , e uma imagem da árvore é exibida em um `ImageView` . Um adaptador é usado para interagir `ViewPager` com o modelo de dados subjacente. Este aplicativo implementa um adaptador derivado de `PagerAdapter` . 

Embora `ViewPager` aplicativos baseados em muitas vezes sejam implementados com `Fragment` s, há alguns casos de uso relativamente simples em que a complexidade extra de `Fragment` s não é necessária. Por exemplo, o aplicativo da Galeria de imagens básico ilustrado neste passo a passos não requer o uso de `Fragment` s. Como o conteúdo é estático e o usuário passa o dedo apenas para frente e para trás entre imagens diferentes, a implementação pode ser mantida mais simples usando exibições e layouts do Android padrão. 

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Crie um novo projeto Android chamado **TreePager** (consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obter mais informações sobre como criar novos projetos Android). Em seguida, inicie o Gerenciador de pacotes NuGet. (Para obter mais informações sobre a instalação de pacotes do NuGet, consulte [Walkthrough: incluindo um NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough)). Localize e instale a **biblioteca de suporte do Android v4**: 

[![Captura de tela de suporte NuGet v4 selecionado no Gerenciador de pacotes NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Isso também instalará todos os pacotes adicionais reaquired pela **biblioteca de suporte do Android v4**.

## <a name="add-an-example-data-source"></a>Adicionar uma fonte de dados de exemplo

Neste exemplo, a fonte de dados do catálogo de árvore (representada pela `TreeCatalog` classe) fornece o `ViewPager` conteúdo do item. 
`TreeCatalog` contém uma coleção pronta de imagens de árvore e títulos de árvore que o adaptador usará para criar `View` s. O `TreeCatalog` Construtor não requer argumentos:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

A coleção de imagens no `TreeCatalog` é organizada de forma que cada imagem possa ser acessada por um indexador. Por exemplo, a linha de código a seguir recupera a ID de recurso de imagem para a terceira imagem na coleção: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Como os detalhes de implementação do `TreeCatalog` não são relevantes para a compreensão `ViewPager` , o `TreeCatalog` código não é listado aqui. O código-fonte para `TreeCatalog` está disponível em [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Baixe esse arquivo de origem (ou copie e cole o código em um novo arquivo **TreeCatalog.cs** ) e adicione-o ao seu projeto. Além disso, baixe e descompacte os [arquivos de imagem](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) em seus **recursos/pasta desenhável** e inclua-os no projeto. 

## <a name="create-a-viewpager-layout"></a>Criar um layout ViewPager

Abra **Resources/layout/Main. axml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>
```

Esse XML define um `ViewPager` que ocupa a tela inteira. Observe que você deve usar o nome totalmente qualificado **Android. support. v4. View. ViewPager** porque `ViewPager` o é empacotado em uma biblioteca de suporte. `ViewPager` está disponível somente na [biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); Ele não está disponível na SDK do Android. 

## <a name="set-up-viewpager"></a>Configurar o ViewPager

Edite **MainActivity.cs** e adicione a seguinte `using` instrução:

```csharp
using Android.Support.V4.View;
```

Substitua o método `OnCreate` pelo seguinte código:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

O código faz o seguinte:

1. Define a exibição do recurso de layout **Main. axml** .

2. Recupera uma referência para o `ViewPager` do layout.

3. Cria uma instância de uma nova `TreeCatalog` como a fonte de dados.

Quando você cria e executa esse código, você deve ver uma exibição semelhante à captura de tela a seguir: 

[![Captura de tela do aplicativo exibindo um ViewPager vazio](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

Neste ponto, o `ViewPager` está vazio porque não há um adaptador para acessar o conteúdo em **TreeCatalog**. Na próxima seção, um **PagerAdapter** é criado para conectar o `ViewPager` ao **TreeCatalog**. 

## <a name="create-the-adapter"></a>Criar o adaptador

`ViewPager` usa um objeto de controlador de adaptador que fica entre o `ViewPager` e a fonte de dados (consulte a ilustração em [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter)). Para acessar esses dados, `ViewPager` é necessário fornecer um adaptador personalizado derivado de `PagerAdapter` . Esse adaptador popula cada `ViewPager` página com conteúdo da fonte de dados. Como essa fonte de dados é específica do aplicativo, o adaptador personalizado é o código que sabe como acessar os dados. À medida que o usuário passa pelas páginas do `ViewPager` , o adaptador extrai as informações da fonte de dados e carrega-as nas páginas para `ViewPager` exibição. 

Ao implementar um `PagerAdapter` , você deve substituir o seguinte:

- **InstantiateItem** &ndash; Cria a página ( `View` ) para uma determinada posição e a adiciona à `ViewPager` coleção de exibições. 

- **DestroyItem** &ndash; Remove uma página de uma determinada posição.

- **Contagem** &ndash; de Propriedade somente leitura que retorna o número de exibições (páginas) disponíveis. 

- **IsViewFromObject** &ndash; Determina se uma página está associada a um objeto de chave específico. (Esse objeto é criado pelo `InstantiateItem` método.) Neste exemplo, o objeto de chave é o `TreeCatalog` objeto de dados.

Adicione um novo arquivo chamado **TreePagerAdapter.cs** e substitua seu conteúdo pelo código a seguir: 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

Esse código stub da implementação essencial `PagerAdapter` . Nas seções a seguir, cada um desses métodos é substituído pelo código de trabalho. 

### <a name="implement-the-constructor"></a>Implementar o Construtor

Quando o aplicativo instancia o `TreePagerAdapter` , ele fornece um contexto (o `MainActivity` ) e uma instância `TreeCatalog` . Adicione as seguintes variáveis de membro e o Construtor à parte superior da `TreePagerAdapter` classe em **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

A finalidade desse construtor é armazenar o contexto e a `TreeCatalog` instância que o `TreePagerAdapter` usará. 

### <a name="implement-count"></a>Implementar contagem

A `Count` implementação é relativamente simples: ela retorna o número de árvores no catálogo de árvore. Substitua `Count` pelo seguinte código:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

A `NumTrees` propriedade de `TreeCatalog` retorna o número de árvores (número de páginas) no conjunto de dados.

### <a name="implement-instantiateitem"></a>Implementar InstantiateItem

O `InstantiateItem` método cria a página para uma determinada posição. Ele também deve adicionar a exibição recém-criada à `ViewPager` coleção do modo de exibição. Para tornar isso possível, o `ViewPager` passa a si mesmo como o parâmetro de contêiner. 

Substitua o método `InstantiateItem` pelo seguinte código:

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

O código faz o seguinte:

1. Instancia um novo `ImageView` para exibir a imagem de árvore na posição especificada. O aplicativo `MainActivity` é o contexto que será passado para o `ImageView` Construtor.

2. Define o `ImageView` recurso para a `TreeCatalog` ID de recurso de imagem na posição especificada.

3. Converte o contêiner passado `View` em uma `ViewPager` referência.
    Observe que você deve usar `JavaCast<ViewPager>()` o para executar essa conversão corretamente (isso é necessário para que o Android execute uma conversão de tipo verificada em tempo de execução).

4. Adiciona a instanciada `ImageView` ao `ViewPager` e retorna o `ImageView` ao chamador.

Quando o `ViewPager` exibe a imagem em `position` , ela é exibida `ImageView` . Inicialmente, `InstantiateItem` é chamado duas vezes para preencher as duas primeiras páginas com exibições. À medida que o usuário rola, ele é chamado novamente para manter exibições apenas atrás e à frente do item exibido no momento. 

### <a name="implement-destroyitem"></a>Implementar DestroyItem

O `DestroyItem` método remove uma página da posição especificada. Em aplicativos nos quais a exibição em qualquer posição determinada pode ser alterada, `ViewPager` deve ter alguma forma de remover uma exibição obsoleta nessa posição antes de substituí-la por uma nova exibição. No `TreeCatalog` exemplo, a exibição em cada posição não é alterada, portanto, uma exibição removida por `DestroyItem` será simplesmente adicionada novamente quando `InstantiateItem` for chamada para essa posição. (Para obter maior eficiência, é possível implementar um pool para reciclar `View` os s que serão exibidos novamente na mesma posição.) 

Substitua o método `DestroyItem` pelo seguinte código: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

O código faz o seguinte:

1. Converte o contêiner passado `View` em uma `ViewPager` referência.

2. Converte o objeto Java passado ( `view` ) em um C# `View` ( `view as View` );

3. Remove a exibição do `ViewPager` . 

### <a name="implement-isviewfromobject"></a>Implementar IsViewFromObject

À medida que o usuário desliza para a esquerda e direita por meio de páginas de conteúdo, `ViewPager` chama `IsViewFromObject` para verificar se o filho `View` na posição fornecida está associado ao objeto do adaptador para essa mesma posição (portanto, o objeto do adaptador é chamado de *chave de objeto*). Para aplicativos relativamente simples, a associação é uma das identidades que &ndash; a chave de objeto do adaptador nessa instância é a exibição retornada anteriormente ao `ViewPager` via `InstantiateItem` . No entanto, para outros aplicativos, a chave de objeto pode ser outra instância de classe específica de adaptador que está associada (mas não igual a) a exibição filho `ViewPager` exibida nessa posição. Somente o adaptador sabe se a exibição passada e a chave de objeto estão associadas. 

`IsViewFromObject` deve ser implementado para `PagerAdapter` que o funcione corretamente. Se `IsViewFromObject` retornar `false` para uma determinada posição, `ViewPager` o não exibirá a exibição nessa posição. No `TreePager` aplicativo, a chave de objeto retornada por `InstantiateItem` *é* a página `View` de uma árvore, portanto, o código só precisa verificar a identidade (ou seja, a chave de objeto e a exibição são uma e a mesma). Substitua `IsViewFromObject` pelo seguinte código: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>Adicionar o adaptador ao ViewPager

Agora que o `TreePagerAdapter` está implementado, é hora de adicioná-lo ao `ViewPager` . No **MainActivity.cs**, adicione a seguinte linha de código ao final do `OnCreate` método:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Esse código instancia o `TreePagerAdapter` , passando o `MainActivity` como o contexto ( `this` ). A instanciada `TreeCatalog` é passada para o segundo argumento do construtor. A `ViewPager` `Adapter` Propriedade do é definida como o objeto instanciado `TreePagerAdapter` ; isso conecta o ao `TreePagerAdapter` `ViewPager` . 

A implementação principal agora está completa &ndash; e executa o aplicativo. Você deve ver a primeira imagem do catálogo de árvore aparecer na tela, conforme mostrado à esquerda na próxima captura. Passe o dedo para a esquerda para ver mais exibições de árvore e, em seguida, passe o dedo para a direita para voltar ao catálogo de árvore: 

[![Capturas de tela do aplicativo TreePager passando por imagens de árvore](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>Adicionar um indicador de pager

Essa `ViewPager` implementação mínima exibe as imagens do catálogo de árvore, mas não fornece nenhuma indicação de onde o usuário está dentro do catálogo. A próxima etapa é adicionar um `PagerTabStrip` . O `PagerTabStrip` informa o usuário sobre qual página é exibida e fornece o contexto de navegação exibindo uma dica das páginas anteriores e posteriores. `PagerTabStrip` o deve ser usado como um indicador para a página atual de a `ViewPager` ; ele rola e atualiza à medida que o usuário passa por cada página. 

Abra **Resources/layout/Main. axml** e adicione um `PagerTabStrip` ao layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` e `PagerTabStrip` foram projetados para trabalhar juntos. Quando você declarar um `PagerTabStrip` dentro de um `ViewPager` layout, o `ViewPager` irá localizar automaticamente o `PagerTabStrip` e conectá-lo ao adaptador. Ao compilar e executar o aplicativo, você verá o vazio `PagerTabStrip` exibido na parte superior de cada tela: 

[![Captura de tela de closeup de um PagerTabStrip vazio](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>Exibir um título

Para adicionar um título a cada guia de página, implemente o `GetPageTitleFormatted` método na `PagerAdapter` classe derivada. `ViewPager` chamadas `GetPageTitleFormatted` (se implementadas) para obter a cadeia de título que descreve a página na posição especificada. Adicione o seguinte método à `TreePagerAdapter` classe em **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Esse código recupera a cadeia de caracteres de legenda da árvore da página especificada (posição) no catálogo de árvore, converte-a em um Java `String` e a retorna para o `ViewPager` . Quando você executa o aplicativo com esse novo método, cada página exibe a legenda da árvore no `PagerTabStrip` . Você deve ver o nome da árvore na parte superior da tela sem um sublinhado: 

[![Capturas de tela de páginas com guias PagerTabStrip preenchidas por texto](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Você pode passar o dedo para cima e para trás para exibir cada imagem de árvore legendada no catálogo. 

### <a name="pagertitlestrip-variation"></a>Variação de PagerTitleStrip

`PagerTitleStrip` é muito semelhante a `PagerTabStrip` Except, exceto que `PagerTabStrip` adiciona um sublinhado à guia selecionada no momento. Você pode substituir `PagerTabStrip` por `PagerTitleStrip` no layout acima e executar o aplicativo novamente para ver como ele se parece com `PagerTitleStrip` : 

[![PagerTitleStrip com sublinhados removidos do texto](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Observe que o sublinhado é removido quando você converte para `PagerTitleStrip` . 

## <a name="summary"></a>Resumo

Este tutorial forneceu um exemplo passo a passo de como criar um `ViewPager` aplicativo baseado em básico sem usar `Fragment` s. Ele apresentou uma fonte de dados de exemplo contendo imagens e cadeias de caracteres de legendas, um `ViewPager` layout para exibir as imagens e uma `PagerAdapter` subclasse que conecta a `ViewPager` à fonte de dados. Para ajudar o usuário a navegar pelo conjunto de dados, foram incluídas instruções que explicam como adicionar um `PagerTabStrip` ou `PagerTitleStrip` exibir a legenda da imagem na parte superior de cada página. 

## <a name="related-links"></a>Links Relacionados

- [TreePager (exemplo)](/samples/xamarin/monodroid-samples/userinterface-treepager)