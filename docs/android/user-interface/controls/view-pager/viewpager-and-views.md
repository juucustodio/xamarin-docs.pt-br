---
title: ViewPager com exibição
description: ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário deslizantes com ViewPager e PagerTabStrip, usando modos de exibição como páginas de dados (um guia subsequente sobre como usar os fragmentos para as páginas).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a8b7fa53d3384821d028e4a88ba22071a17e5bd9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61311527"
---
# <a name="viewpager-with-views"></a>ViewPager com exibição

_ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário deslizantes com ViewPager e PagerTabStrip, usando modos de exibição como páginas de dados (um guia subsequente sobre como usar os fragmentos para as páginas)._

 
## <a name="overview"></a>Visão geral

Este guia é um passo a passo que fornece uma demonstração passo a passo de como usar `ViewPager` para implementar uma galeria de imagens de árvores decídua e sempre-verde. Neste aplicativo, o usuário passa o dedo left e right por meio de"árvore" para exibir imagens de árvore. Na parte superior de cada página do catálogo, o nome da árvore está listado em um`PagerTabStrip`, e uma imagem da árvore é exibida em um `ImageView`. Um adaptador é usado para interface de `ViewPager` ao modelo de dados subjacente. Este aplicativo implementa um adaptador derivado `PagerAdapter`. 

Embora `ViewPager`-com base de aplicativos geralmente são implementados com `Fragment`s, há alguns casos de uso relativamente simples em que a complexidade extra de `Fragment`s não é necessário. Por exemplo, o aplicativo de galeria de imagens básica ilustrado neste passo a passo não requer o uso de `Fragment`s. Como o conteúdo é estático e dedo somente o usuário e para trás entre diferentes imagens, a implementação pode ser mantida mais simples usando layouts e exibições do Android padrão. 



## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto Android chamado **TreePager** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obter mais informações sobre como criar novos projetos Android). Em seguida, inicie o Gerenciador de pacotes do NuGet. (Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: Incluir um NuGet no projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Localizar e instalar **biblioteca de suporte ao Android v4**: 

[![Captura de tela de suporte v4 Nuget selecionado no Gerenciador de pacotes NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Essa ação também instalará qualquer reaquired pacotes adicionais por **biblioteca de suporte ao Android v4**.



## <a name="add-an-example-data-source"></a>Adicionar uma fonte de dados de exemplo

Neste exemplo, a fonte de dados do catálogo de árvore (representado pela `TreeCatalog` classe) fornece o `ViewPager` com o conteúdo do item. 
`TreeCatalog` contém uma coleção prontas para uso de imagens de árvore e títulos de árvore que o adaptador usará para a criação de `View`s. O `TreeCatalog` construtor não requer nenhum argumento:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

A coleção de imagens no `TreeCatalog` é organizado de modo que cada imagem possa ser acessada por um indexador. Por exemplo, a seguinte linha de código recupera a ID de recurso de imagem para a terceira imagem na coleção: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Porque os detalhes de implementação `TreeCatalog` não são relevantes para compreensão `ViewPager`, o `TreeCatalog` código não está listado aqui. O código-fonte `TreeCatalog` está disponível em [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Baixe esse arquivo de origem (ou copie e cole o código em um novo **TreeCatalog.cs** arquivo) e adicioná-lo ao seu projeto. Além disso, baixe e descompacte o [arquivos de imagem](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) em seu **recursos/drawable** pasta e incluí-los no projeto. 



## <a name="create-a-viewpager-layout"></a>Criar um Layout de ViewPager

Abra **Resources/layout/Main.axml** e substitua seu conteúdo pelo seguinte XML:

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

Esse código faz o seguinte:

1.  Define o modo de exibição do **Main. axml** recursos de layout.

2.  Recupera uma referência para o `ViewPager` do layout.

3.  Cria uma nova `TreeCatalog` como fonte de dados.

Quando você compila e executa esse código, você deverá ver uma exibição semelhante ao seguinte captura de tela: 

[![Captura de tela do aplicativo exibindo um ViewPager vazio](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

Neste ponto, o `ViewPager` está vazia porque ele está faltando um adaptador para acessar o conteúdo no **TreeCatalog**. Na próxima seção, uma **PagerAdapter** é criado para conectar o `ViewPager` para o **TreeCatalog**. 


## <a name="create-the-adapter"></a>Criar o adaptador

`ViewPager` usa um objeto de controlador de adaptador que fica entre o `ViewPager` e a fonte de dados (veja a ilustração na [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter)). Para acessar esses dados, `ViewPager` exige que você forneça um adaptador personalizado derivado de `PagerAdapter`. Esse adaptador preenche cada `ViewPager` página com conteúdo da fonte de dados. Como essa fonte de dados é específico do aplicativo, o adaptador personalizado é o código que sabe como acessar os dados. Como o dedo do usuário por meio das páginas do `ViewPager`, o adaptador extrai informações de fonte de dados e carrega-os em páginas de para o `ViewPager` para exibir. 

Quando você implementa um `PagerAdapter`, você deve substituir o seguinte:

-   **InstantiateItem** &ndash; cria a página (`View`) para uma posição especificada e adiciona-o para o `ViewPager`da coleção de exibições. 

-   **DestroyItem** &ndash; remove uma página de uma determinada posição.

-   **Contagem** &ndash; propriedade somente leitura que retorna o número de modos de exibição (páginas) disponíveis. 

-   **IsViewFromObject** &ndash; determina se uma página está associada um objeto de chave específico. (Esse objeto é criado pelo `InstantiateItem` método.) Neste exemplo, o objeto de chave é o `TreeCatalog` objeto de dados.

Adicionar um novo arquivo chamado **TreePagerAdapter.cs** e substitua seu conteúdo pelo seguinte código: 

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

Esse código criará um stub essencial `PagerAdapter` implementação. Nas seções a seguir, cada um desses métodos é substituída pelo código de trabalho. 



### <a name="implement-the-constructor"></a>Implementar o construtor

Quando o aplicativo cria uma instância de `TreePagerAdapter`, ele fornece um contexto (a `MainActivity`) e um instanciadas `TreeCatalog`. Adicione as seguintes variáveis de membro e o construtor na parte superior do `TreePagerAdapter` classe **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

A finalidade desse construtor é armazenar o contexto e `TreeCatalog` da instância que o `TreePagerAdapter` usará. 



### <a name="implement-count"></a>Contagem de implementar

O `Count` implementação é relativamente simple: ele retorna o número de árvores no catálogo de árvore. Substitua `Count` pelo seguinte código:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

O `NumTrees` propriedade de `TreeCatalog` retorna o número de árvores (número de páginas) no conjunto de dados.



### <a name="implement-instantiateitem"></a>Implementar InstantiateItem

O `InstantiateItem` método cria a página para uma determinada posição. Ele também deve adicionar a exibição recém-criada para o `ViewPager`do exibir a coleção. Para tornar isso possível, o `ViewPager` é passado como o parâmetro de contêiner. 

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

Esse código faz o seguinte:

1.  Cria uma nova `ImageView` para exibir a imagem de árvore na posição especificada. O aplicativo `MainActivity` é o contexto que será passado para o `ImageView` construtor.

2.  Define o `ImageView` recursos para o `TreeCatalog` ID de recurso na posição especificada da imagem.

3.  Converte o contêiner passado `View` para um `ViewPager` referência.
    Observe que você deve usar `JavaCast<ViewPager>()` para executar corretamente essa conversão (Isso é necessário para que o Android executa uma conversão de tipo verificada pelo tempo de execução).

4.  Adiciona o instanciado `ImageView` para o `ViewPager` e retorna o `ImageView` ao chamador.

Quando o `ViewPager` exibe a imagem na `position`, ele exibe isso `ImageView`. Inicialmente, `InstantiateItem` é chamado duas vezes para preencher as duas primeiras páginas com modos de exibição. Conforme o usuário rola, ele é chamado novamente para manter exibições apenas atrás e à frente do item exibido no momento. 



### <a name="implement-destroyitem"></a>Implementar DestroyItem

O `DestroyItem` método Remove uma página da posição determinada. Em aplicativos em que pode alterar o modo de exibição em qualquer posição determinada, `ViewPager` devem ter alguma maneira de remover um modo de exibição obsoleto naquela posição antes de substituí-la por uma nova exibição. No `TreeCatalog` exemplo, o modo de exibição em cada posição não altera, para um modo de exibição removida pelo `DestroyItem` simplesmente será adicionado novamente quando `InstantiateItem` é chamado para essa posição. (Para melhorar a eficiência, um pode implementar um pool para reciclar `View`s será exibida novamente na mesma posição.) 

Substitua o método `DestroyItem` pelo seguinte código: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Esse código faz o seguinte:

1.  Converte o contêiner passado `View` em um `ViewPager` referência.

2.  Converte o objeto passado do Java (`view`) em um C# `View` (`view as View`);

3.  Remove a exibição do `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Implementar IsViewFromObject

Como a slides de usuário para a esquerda e direita nas páginas de conteúdo, `ViewPager` chamadas `IsViewFromObject` para verificar se o filho `View` na posição determinada está associado com o objeto do adaptador para a mesma posição (portanto, o objeto do adaptador é chamado um *chave do objeto*). Para aplicativos relativamente simples, a associação é uma das identidades &ndash; chave do objeto do adaptador nessa instância é o modo de exibição que foi retornado anteriormente para o `ViewPager` por meio de `InstantiateItem`. No entanto para outros aplicativos, a chave do objeto pode ser outra instância de classe de adaptador específico que está associada (mas não igual) filho view que `ViewPager` exibe nessa posição. Somente o adaptador sabe se o modo de exibição passado e a chave de objeto estão associados. 

`IsViewFromObject` deve ser implementado para `PagerAdapter` funcione corretamente. Se `IsViewFromObject` retorna `false` para um determinado cargo, `ViewPager` não exibirá o modo de exibição nessa posição. No `TreePager` aplicativo, o objeto chave retornada pela `InstantiateItem` *é* página `View` de uma árvore, portanto, o código tem apenas verificar a identidade (ou seja, a chave do objeto e o modo de exibição são o mesmo). Substitua `IsViewFromObject` pelo seguinte código: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Adicionar o adaptador para o ViewPager

Agora que o `TreePagerAdapter` é implementado, é hora de adicioná-lo para o `ViewPager`. Na **MainActivity.cs**, adicione a seguinte linha de código ao final do `OnCreate` método:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Esse código cria uma instância de `TreePagerAdapter`, passando a `MainActivity` como o contexto (`this`). Instanciada `TreeCatalog` é passado para o segundo de argumento do construtor. O `ViewPager`do `Adapter` estiver definida para instanciada `TreePagerAdapter` objeto; este plugs a `TreePagerAdapter` no `ViewPager`. 

A implementação principal agora está concluída &ndash; compilar e executar o aplicativo. Você deve ver a primeira imagem do catálogo de árvore aparecem na tela, conforme mostrado à esquerda na próxima captura de tela. Passe o dedo para esquerda para ver mais modos de exibição de árvore, direito, em seguida, passe o dedo para mover de volta por meio do catálogo de árvore: 

[![Aplicativo de capturas de tela de TreePager passar o dedo por meio de imagens de árvore](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Adicionar um indicador de Pager

Nesse mínimo `ViewPager` implementação exibe as imagens do catálogo de árvore, mas ela fornece nenhuma indicação de onde o usuário está no catálogo. A próxima etapa é adicionar um `PagerTabStrip`. O `PagerTabStrip` informa o usuário à qual página é exibida e fornece o contexto de navegação, exibindo uma dica das páginas anteriores e posteriores. `PagerTabStrip` se destina a ser usado como um indicador para a página atual de um `ViewPager`; ele rola e atualiza conforme o dedo do usuário por meio de cada página. 

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

`ViewPager` e `PagerTabStrip` são projetados para trabalhar juntos. Quando você declara uma `PagerTabStrip` dentro de um `ViewPager` layout, o `ViewPager` localizará automaticamente o `PagerTabStrip` e conecte-se ao adaptador. Quando você compilar e executa o aplicativo, você deverá ver a esvaziar `PagerTabStrip` exibida na parte superior de cada tela: 

[![Captura de tela de Close-up de um PagerTabStrip vazio](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Exibir um título

Para adicionar um título para cada guia de página, implementar o `GetPageTitleFormatted` método no `PagerAdapter`-classe derivada. `ViewPager` chamadas `GetPageTitleFormatted` (se implementado) para obter a cadeia de caracteres do título que descreve a página na posição especificada. Adicione o seguinte método para o `TreePagerAdapter` classe **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Esse código recupera a cadeia de caracteres de legenda de árvore da página (posição) especificada no catálogo de árvore, converte-o em um Java `String`e retorna-o para o `ViewPager`. Quando você executa o aplicativo com esse novo método, cada página exibe a legenda de árvore no `PagerTabStrip`. Você deverá ver o nome da árvore na parte superior da tela sem sublinhado: 

[![Capturas de tela de páginas com guias de PagerTabStrip repleto de texto](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Você pode passe o dedo para trás e para exibir cada imagem de árvore legendado no catálogo. 



### <a name="pagertitlestrip-variation"></a>Variação de PagerTitleStrip

`PagerTitleStrip` é muito semelhante à `PagerTabStrip` , exceto que `PagerTabStrip` adiciona um sublinhado para a guia selecionada no momento. Você pode substituir `PagerTabStrip` com `PagerTitleStrip` no layout e execute o aplicativo novamente para ver a aparência dele com acima `PagerTitleStrip`: 

[![PagerTitleStrip com sublinhados removida do texto](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Observe que o sublinhado é removido quando você converte em `PagerTitleStrip`. 


 
## <a name="summary"></a>Resumo

Este passo a passo fornecido um exemplo passo a passo de como criar um basic `ViewPager`-com base em aplicativo sem usar `Fragment`s. Ela apresentada uma fonte de dados de exemplo que contém imagens e cadeias de caracteres de legenda, um `ViewPager` layout para exibir as imagens e uma `PagerAdapter` subclasse que se conecta a `ViewPager` à fonte de dados. Para ajudar o usuário a navegar pelo conjunto de dados, as instruções foram incluídas que explicam como adicionar um `PagerTabStrip` ou `PagerTitleStrip` para exibir a legenda de imagem na parte superior de cada página. 


## <a name="related-links"></a>Links relacionados

- [TreePager (amostra)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
