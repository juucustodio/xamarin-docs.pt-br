---
title: "ViewPager com modos de exibição"
description: "ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer páginas de dados. Este guia explica como implementar uma interface do usuário swipeable com ViewPager e PagerTabStrip, usando modos de exibição como as páginas de dados (guia subsequente aborda como usar fragmentos para as páginas)."
ms.topic: article
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 9c30cf9d76498e95aba6f9a003bc40c7d14e21de
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="viewpager-with-views"></a>ViewPager com modos de exibição

_ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer páginas de dados. Este guia explica como implementar uma interface do usuário swipeable com ViewPager e PagerTabStrip, usando modos de exibição como as páginas de dados (guia subsequente aborda como usar fragmentos para as páginas)._

 
## <a name="overview"></a>Visão geral

Este guia é um passo a passo que fornece uma demonstração passo a passo sobre como usar `ViewPager` para implementar uma galeria de imagens de decídua e verde. Neste aplicativo, o usuário obtém esquerda e direita por meio de"árvore" para exibir imagens de árvore. Na parte superior de cada página do catálogo, o nome da árvore está listado em um`PagerTabStrip`, e uma imagem da árvore é exibida em um `ImageView`. É usado um adaptador de interface de `ViewPager` ao modelo de dados subjacente. Este aplicativo implementa um adaptador derivado de `PagerAdapter`. 

Embora `ViewPager`-baseado em aplicativos geralmente são implementados com `Fragment`s, há alguns casos de uso relativamente simples em que a complexidade extra de `Fragment`s não é necessário. Por exemplo, o aplicativo da Galeria de imagens básica ilustrado neste passo a passo não requerem o uso de `Fragment`s. Como o conteúdo é estático e swipes apenas o usuário entre diferentes imagens, a implementação pode ser mantida mais simples usando layouts e modos de exibição padrão Android. 



## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto Android chamado **TreePager** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obter mais informações sobre como criar novos projetos Android). Em seguida, inicie o Gerenciador de pacotes do NuGet. (Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Localizar e instalar **biblioteca de suporte Android v4**: 

[![Captura de tela de suporte v4 Nuget selecionado o NuGet Package Manager](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Isso também irá instalar qualquer reaquired pacotes adicionais por **biblioteca de suporte Android v4**.



## <a name="add-an-example-data-source"></a>Adicionar uma fonte de dados de exemplo

Neste exemplo, a fonte de dados do catálogo de árvore (representado pelo `TreeCatalog` classe) fornece o `ViewPager` com o conteúdo do item. 
`TreeCatalog` contém uma coleção pronta de imagens de árvore e títulos de árvore que o adaptador usará para criar `View`s. O `TreeCatalog` construtor não exige argumentos:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

A coleção de imagens em `TreeCatalog` é organizado de modo que cada imagem pode ser acessada por um indexador. Por exemplo, a linha de código a seguir recupera a ID de recurso de imagem para a terceira imagem na coleção: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Como os detalhes da implementação `TreeCatalog` não são relevantes para compreensão `ViewPager`, o `TreeCatalog` código não está listado aqui. O código-fonte para `TreeCatalog` está disponível em [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Baixar o arquivo de origem (ou copie e cole o código em um novo **TreeCatalog.cs** arquivo) e adicioná-lo ao seu projeto. Além disso, baixe e descompacte o [arquivos de imagem](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) em seu **recursos/drawable** pasta e incluí-los no projeto. 



## <a name="create-a-viewpager-layout"></a>Criar um Layout de ViewPager

Abra **Resources/layout/Main.axml** e substitua o seu conteúdo com o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 


## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

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

Este código faz o seguinte:

1.  Define a exibição de **Main.axml** recursos de layout.

2.  Recupera uma referência para o `ViewPager` do layout.

3.  Cria um novo `TreeCatalog` como a fonte de dados.

Quando você compilar e executa esse código, você verá uma exibição semelhante a captura de tela a seguir: 

[![Captura de tela do aplicativo, exibindo um ViewPager vazio](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

Neste ponto, o `ViewPager` está vazio porque ele está faltando um adaptador para acessar o conteúdo **TreeCatalog**. Na próxima seção, uma **PagerAdapter** é criado para conectar o `ViewPager` para o **TreeCatalog**. 


## <a name="create-the-adapter"></a>Criar o adaptador

`ViewPager` usa um objeto de controlador de adaptador que fica entre o `ViewPager` e a fonte de dados (consulte a ilustração [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter)). Para acessar esses dados, `ViewPager` requer que você forneça um adaptador personalizado derivado de `PagerAdapter`. Esse adaptador preenche cada `ViewPager` página com conteúdo da fonte de dados. Como essa fonte de dados é específico do aplicativo, o adaptador personalizado é o código que sabe como acessar os dados. Como os swipes de usuário por meio de páginas do `ViewPager`, o adaptador extrai informações de fonte de dados e carrega-os em páginas para o `ViewPager` para exibir. 

Quando você implementa um `PagerAdapter`, você deve substituir o seguinte:

-   **InstantiateItem** &ndash; cria a página (`View`) para uma posição fornecida e adiciona-o para o `ViewPager`da coleção de exibições. 

-   **DestroyItem** &ndash; remove uma página de uma determinada posição.

-   **Contagem de** &ndash; propriedade somente leitura que retorna o número de modos de exibição (páginas) disponíveis. 

-   **IsViewFromObject** &ndash; determina se uma página está associada um objeto de chave específico. (Esse objeto é criado o `InstantiateItem` método.) Neste exemplo, o objeto de chave é o `TreeCatalog` objeto de dados.

Adicionar um novo arquivo chamado **TreePagerAdapter.cs** e substitua o seu conteúdo com o código a seguir: 

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

Esse código extingue essencial `PagerAdapter` implementação. Nas seções a seguir, cada um desses métodos é substituída pelo código de trabalho. 



### <a name="implement-the-constructor"></a>Implementar o construtor

Quando o aplicativo cria uma instância de `TreePagerAdapter`, ele fornece um contexto (a `MainActivity`) e um instanciadas `TreeCatalog`. Adicione as seguintes variáveis de membro e o construtor para o início do `TreePagerAdapter` classe em **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

O objetivo desse construtor é armazenar o contexto e `TreeCatalog` instância que o `TreePagerAdapter` será usado. 



### <a name="implement-count"></a>Contagem de implementação

O `Count` implementação é relativamente simple: retorna o número de árvores no catálogo de árvore. Substitua `Count` pelo seguinte código:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

O `NumTrees` propriedade `TreeCatalog` retorna o número de árvores (número de páginas) no conjunto de dados.



### <a name="implement-instantiateitem"></a>Implementar InstantiateItem

O `InstantiateItem` método cria a página para uma posição determinada. Ele também deverá adicionar o modo de exibição criado recentemente para o `ViewPager`da coleção. Para que isso aconteça, o `ViewPager` é passado como o parâmetro de contêiner. 

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

Este código faz o seguinte:

1.  Cria um novo `ImageView` para exibir a imagem de árvore na posição especificada. O aplicativo `MainActivity` é o contexto que será passado para o `ImageView` construtor.

2.  Conjuntos de `ImageView` recursos para a `TreeCatalog` ID de recurso na posição especificada da imagem.

3.  Converte o contêiner passado `View` para um `ViewPager` referência.
    Observe que você deve usar `JavaCast<ViewPager>()` para executar corretamente este cast (Isso é necessário para que o Android executa uma conversão de tipo verificado em tempo de execução).

4.  Adiciona o instanciado `ImageView` para o `ViewPager` e retorna o `ImageView` ao chamador.

Quando o `ViewPager` exibe a imagem em `position`, ele exibe `ImageView`. Inicialmente, `InstantiateItem` é chamado duas vezes para preencher as duas primeiras páginas com modos de exibição. Conforme o usuário rola, ele é chamado novamente para manter exibições apenas atrás e à frente do item exibido no momento. 



### <a name="implement-destroyitem"></a>Implementar DestroyItem

O `DestroyItem` método Remove uma página de posição fornecida. Em aplicativos em que pode alterar o modo de exibição em qualquer posição determinada, `ViewPager` deve ter alguma maneira de remover uma exibição obsoleta dessa posição antes de substituí-lo com um novo modo de exibição. No `TreeCatalog` exemplo, não alterar o modo de exibição em cada posição, para um modo de exibição removido por `DestroyItem` simplesmente será adicionado novamente quando `InstantiateItem` é chamado de posição. (Para melhorar a eficiência, um pode implementar um pool para reciclar `View`s que será exibido novamente na mesma posição.) 

Substitua o método `DestroyItem` pelo seguinte código: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Este código faz o seguinte:

1.  Converte o contêiner passado `View` em um `ViewPager` referência.

2.  Converte o objeto passado do Java (`view`) em c# `View` (`view as View`);

3.  Remove a exibição de `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Implementar IsViewFromObject

Como o slides de usuário para a esquerda e direita por meio de páginas de conteúdo, `ViewPager` chamadas `IsViewFromObject` para verificar se o filho `View` na posição fornecida está associado ao objeto do adaptador para a mesma posição (portanto, o objeto do adaptador é chamado um *chave do objeto*). Para aplicativos relativamente simples, a associação é uma identidade &ndash; chave do objeto do adaptador nessa instância é o modo de exibição que foi retornado anteriormente para o `ViewPager` via `InstantiateItem`. No entanto para outros aplicativos, a chave do objeto pode ser outra instância de classe de adaptador específico que está associada (mas não igual) o filho exibição `ViewPager` exibe naquela posição. Somente o adaptador sabe se a exibição passada e a chave do objeto estão associados. 

`IsViewFromObject` deve ser implementado para `PagerAdapter` funcione corretamente. Se `IsViewFromObject` retorna `false` para uma posição especificada, `ViewPager` não exibirá o modo de exibição da posição. No `TreePager` aplicativo, o objeto chave retornada pelo `InstantiateItem` *é* página `View` de uma árvore, portanto o código tem apenas verificar a identidade (ou seja, a chave do objeto e o modo de exibição são a mesma). Substitua `IsViewFromObject` pelo seguinte código: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Adicionar o adaptador para o ViewPager

Agora que o `TreePagerAdapter` é implementada, é hora de adicioná-lo para o `ViewPager`. Em **MainActivity.cs**, adicione a seguinte linha de código ao final do `OnCreate` método:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Esse código cria uma instância de `TreePagerAdapter`, passando o `MainActivity` como o contexto (`this`). O instanciado `TreeCatalog` é passado para o segundo argumento do construtor. O `ViewPager`do `Adapter` está definida para as instâncias `TreePagerAdapter` objeto; esse plugs o `TreePagerAdapter` no `ViewPager`. 

A implementação de núcleo está concluída &ndash; compilar e executar o aplicativo. Você deve ver a imagem de primeiro do catálogo de árvore exibida na tela, conforme mostrado à esquerda na seguinte captura de tela. Passe o dedo para ver mais modos de exibição de árvore, em seguida, direita passe o dedo para percorrer o catálogo de árvore: 

[![Aplicativo de capturas de tela de TreePager passar o dedo por meio de imagens de árvore](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Adicionar um indicador de Pager

Nesse mínimo `ViewPager` implementação exibe as imagens do catálogo de árvore, mas fornece indicação onde o usuário está no catálogo. A próxima etapa é adicionar um `PagerTabStrip`. O `PagerTabStrip` informa ao usuário como para a página é exibida e fornece o contexto de navegação, exibindo uma dica das páginas anteriores e posteriores. `PagerTabStrip` se destina a ser usado como um indicador para a página atual de um `ViewPager`; ele rola e atualiza como swipes o usuário por meio de cada página. 

Abra **Resources/layout/Main.axml** e adicione um `PagerTabStrip` ao layout:

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

`ViewPager` e `PagerTabStrip` são projetados para trabalhar juntos. Quando você declara um `PagerTabStrip` dentro de um `ViewPager` layout, o `ViewPager` localizará automaticamente o `PagerTabStrip` e conecte-se ao adaptador. Quando você compilar e executa o aplicativo, você deve ver vazio `PagerTabStrip` exibida na parte superior de cada tela: 

[![Captura de tela de Close-up de uma PagerTabStrip vazio](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Exibir um título

Para adicionar um título para cada guia, implemente o `GetPageTitleFormatted` método o `PagerAdapter`-classe derivada. `ViewPager` chamadas `GetPageTitleFormatted` (se implementado) para obter a cadeia de caracteres de título que descreve a página na posição especificada. Adicione o seguinte método para o `TreePagerAdapter` classe em **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Este código recupera a cadeia de caracteres de legenda de árvore da página especificada (posição) no catálogo de árvore, converte-o em um Java `String`e retorna-o para o `ViewPager`. Quando você executa o aplicativo com esse novo método, cada página exibe a legenda de árvore de `PagerTabStrip`. Você deve ver o nome da árvore na parte superior da tela sem sublinhado: 

[![Capturas de tela de páginas com guias de PagerTabStrip preenchido com texto](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Você pode passe o dedo para trás e para exibir cada imagem de legendado no catálogo. 



### <a name="pagertitlestrip-variation"></a>Variação de PagerTitleStrip

`PagerTitleStrip` é muito semelhante ao `PagerTabStrip` exceto que `PagerTabStrip` adiciona um sublinhado para a guia selecionada no momento. Você pode substituir `PagerTabStrip` com `PagerTitleStrip` no layout e executar o aplicativo novamente para ver como ele se parece com acima `PagerTitleStrip`: 

[![PagerTitleStrip com sublinhados removida do texto](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Observe que o sublinhado é removido quando você converte em `PagerTitleStrip`. 


 
## <a name="summary"></a>Resumo

Este passo a passo fornecido um exemplo passo a passo de como criar um basic `ViewPager`-com base em aplicativo sem usar `Fragment`s. Ela apresentada uma fonte de dados de exemplo que contém imagens e cadeias de caracteres de legenda, uma `ViewPager` layout para exibir as imagens e um `PagerAdapter` subclasse que se conecta a `ViewPager` à fonte de dados. Para ajudar o usuário navegue pelo conjunto de dados, as instruções foram incluídas que explicam como adicionar um `PagerTabStrip` ou `PagerTitleStrip` para exibir a legenda de imagem na parte superior de cada página. 


## <a name="related-links"></a>Links relacionados

- [TreePager (exemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
