---
title: Criar um fragmento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 339de4930242e35c40b034af2ce6ba47fe1543af
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122459"
---
# <a name="creating-a-fragment"></a>Criar um fragmento

Para criar um fragmento, uma classe deve herdar de `Android.App.Fragment` e, em seguida, substituir o `OnCreateView` método. `OnCreateView` será chamado pela atividade de hospedagem quando é hora de colocar o fragmento na tela e retornará um `View`. Um típico `OnCreateView` irá criá-lo `View` aumentando um arquivo de layout e, em seguida, anexá-lo a um contêiner pai. Características do contêiner são importantes, pois o Android aplicará os parâmetros do layout do pai na interface do usuário do fragmento. O exemplo a seguir ilustra isto:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

O código acima irá aumentar a exibição `Resource.Layout.Example_Fragment`e adicioná-lo como um modo de exibição filho para o `ViewGroup` contêiner.


> [!NOTE]
> Classes de subpropriedades de fragmento não devem ter um padrão público, nenhum construtor de argumento.

## <a name="adding-a-fragment-to-an-activity"></a>Adicionando um fragmento para uma atividade

Há duas maneiras que um fragmento pode ser hospedado dentro de uma atividade:

-   **Declarativamente** &ndash; fragmentos podem ser usados declarativamente dentro `.axml` arquivos de layout usando o `<Fragment>` marca.

-   **Por meio de programação** &ndash; fragmentos também podem ser instanciados dinamicamente usando o `FragmentManager` API da classe.

Uso programático por meio de `FragmentManager` classe será discutido posteriormente neste guia.

### <a name="using-a-fragment-declaratively"></a>Usa um fragmento de forma declarativa

Adicionar um fragmento dentro do layout requer o uso de `<fragment>` marca e, em seguida, identificando o fragmento, fornecendo um o `class` atributo ou o `android:name` atributo. O trecho a seguir mostra como usar o `class` atributo para declarar um `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Este próximo trecho mostra como declarar uma `fragment` usando o `android:name` atributo para identificar a classe de fragmento:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando a atividade está sendo criada, o Android será criar uma instância de cada fragmento especificado no arquivo de layout e inserir o modo de exibição é criado a partir `OnCreateView` em vez do `Fragment` elemento.
Fragmentos de forma declarativa são adicionados a uma atividade são estáticos e permanecerão na atividade até que ele for destruído; não é possível substituir dinamicamente ou remover tal um fragmento durante o tempo de vida da atividade à qual ele está anexado.

Cada fragmento deve ser atribuído a um identificador exclusivo:

-  **Android: id** &ndash; assim como com outros elementos de interface do usuário em um arquivo de layout, isso é uma ID exclusiva.

-  **Android: tag** &ndash; esse atributo é uma cadeia de caracteres exclusiva.

Se nenhum dos dois métodos a anterior for usado, o fragmento assumirá a ID da exibição do contêiner. No exemplo a seguir onde nenhum dos dois `android:id` nem `android:tag` for fornecido, o Android será atribuir a ID `fragment_container` ao fragmento:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Caso de nome de pacote

Android não permite caracteres maiusculos em nomes de pacote; ele lançará uma exceção ao tentar aumentar a exibição se um nome de pacote contém um caractere maiusculo. No entanto, o xamarin. Android é mais tolerante e tolerarão caracteres maiusculos no namespace.

Por exemplo, ambos os trechos de código a seguir funcionará com o xamarin. Android. No entanto, o segundo trecho fará com que um `android.view.InflateException` seja lançada por um aplicativo Android baseado em Java puro.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OU

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo de vida do fragmento

Fragmentos têm seu próprio ciclo de vida que é um pouco independente dos, mas ainda é afetado pelo, o [ciclo de vida da atividade de hospedagem](~/android/app-fundamentals/activity-lifecycle/index.md).
Por exemplo, quando uma atividade pausa, todos os seus fragmentos associados estão em pausa. O diagrama a seguir descreve o ciclo de vida do fragmento.

[![Diagrama de fluxo que ilustra o ciclo de vida do fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de criação de fragmento

A lista a seguir mostra o fluxo dos vários retornos de chamada no ciclo de vida de um fragmento como ele está sendo criado:

-   **`OnInflate()`** &ndash; Chamado quando o fragmento que está sendo criado como parte de um layout de exibição. Isso pode ser chamado imediatamente depois que o fragmento é criado declarativamente de um arquivo de layout XML. O fragmento não está associado a sua atividade ainda, mas o **atividade**, **pacote**, e **AttributeSet** na exibição de hierarquia são passados como parâmetros. Esse método é melhor usado para analisar a **AttributeSet** e para salvar os atributos que pode ser usada posteriormente pelo fragmento.

-   **`OnAttach()`** &ndash; Chamado depois que o fragmento é associado à atividade. Isso é o primeiro método a ser executado quando o fragmento estiver pronto para ser usado. Em geral, fragmentos não devem implementar um construtor ou substituir o construtor padrão. Todos os componentes que são necessários para o fragmento devem ser inicializados neste método.

-   **`OnCreate()`** &ndash; Chamado pela atividade para criar o fragmento. Quando este método é chamado, a hierarquia de exibição da atividade de hospedagem pode não ser totalmente instanciada; assim, o fragmento não deve confiar em qualquer parte da hierarquia de exibição da atividade até posteriormente no ciclo de vida do fragmento. Por exemplo, não use esse método para executar qualquer ajustes ou ajustes na interface do usuário do aplicativo. Isso é o horário mais cedo, no qual o fragmento pode começar a coleta de dados que ele precisa. O fragmento está em execução no thread da interface do usuário neste momento, portanto, evite qualquer processamento mais extenso ou executar que o processamento em um thread em segundo plano. Esse método pode ser ignorado se **SetRetainInstance(true)** é chamado.
    Essa alternativa será descrita mais detalhadamente abaixo.

-   **`OnCreateView()`** &ndash; Cria a exibição para o fragmento.
    Esse método é chamado uma vez a atividade **OnCreate()** método for concluído. Neste ponto, é seguro interagir com a hierarquia de exibição da atividade. Esse método deve retornar o modo de exibição que será usado pelo fragmento.

-   **`OnActivityCreated()`** &ndash; Chamado após **OnCreate** foi concluída pela atividade de hospedagem.
    Ajustes finais à interface do usuário devem ser executadas no momento.

-   **`OnStart()`** &ndash; Chamado depois que a atividade contentora foi retomada. Isso torna o fragmento visíveis ao usuário. Em muitos casos, o fragmento contém código que normalmente estariam na **OnStart ()** método de uma atividade.

-   **`OnResume()`** &ndash; Isso é o último método chamado antes que o usuário pode interagir com o fragmento. Um exemplo de como o tipo de código que deve ser executado nesse método poderia ser habilitando recursos de um dispositivo que o usuário pode interagir com, como a câmera que os serviços de localização. No entanto, serviços, como eles podem causar excessiva de bateria, e um aplicativo deve minimizar seu uso para preservar a vida útil da bateria.


### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destruição de fragmento

A lista seguinte explica os métodos de ciclo de vida que são chamados como um fragmento que está sendo destruído:

-   **`OnPause()`** &ndash; O usuário não é capaz de interagir com o fragmento. Essa situação existe porque alguma outra operação de fragmento é modificar este fragmento ou a atividade de hospedagem está em pausa. É possível que a atividade de hospedagem desse fragmento ainda poderá estar visível, ou seja, a atividade em foco é parcialmente transparente ou não ocupa a tela inteira. Quando este método se torna ativo, é a primeira indicação de que o usuário está deixando o fragmento. O fragmento deve salvar as alterações.

-   **`OnStop()`** &ndash; O fragmento não está mais visível. O atividade do host pode ter sido interrompido ou uma operação de fragmento é modificá-la na atividade. Esse retorno de chamada tem a mesma finalidade, como **Activity.OnStop**.

-   **`OnDestroyView()`** &ndash; Esse método é chamado para limpar os recursos associados com o modo de exibição. Isso é chamado quando o modo de exibição associado com o fragmento foi destruído.

-   **`OnDestroy()`** &ndash; Este método é chamado quando o fragmento não estiver mais em uso. Ele ainda está associado com a atividade, mas o fragmento não estiver mais funcionando. Esse método deve liberar quaisquer recursos que estão em uso por fragmento, tal como uma [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) que podem ser usadas para uma câmera. Esse método pode ser ignorado se **SetRetainInstance(true)** é chamado. Essa alternativa será descrita mais detalhadamente abaixo.

-   **`OnDetach()`** &ndash; Esse método é chamado pouco antes do fragmento não está mais associado à atividade. A hierarquia de exibição do fragmento não existe mais, e todos os recursos que são usados pelo fragmento devem ser liberados no momento.


### <a name="using-setretaininstance"></a>Usando SetRetainInstance

É possível que um fragmento especificar que ele deve não ser completamente destruído se a atividade está sendo recriada. O `Fragment` classe fornece o método `SetRetainInstance` para essa finalidade. Se `true` é passado para esse método, em seguida, quando a atividade é reiniciada, a mesma instância do fragmento será usada. Se isso acontecer, todos os métodos de retorno de chamada serão invocados, exceto o `OnCreate` e `OnDestroy` retornos de chamada do ciclo de vida. Esse processo é ilustrado no diagrama de ciclo de vida mostrado acima (pelas linhas pontilhadas verdes).


## <a name="fragment-state-management"></a>Gerenciamento de estado de fragmento

Fragmentos de maio salvar e restaurar seu estado durante o ciclo de vida do fragmento usando uma instância de um `Bundle`. O pacote permite que um fragmento salvar dados como pares chave/valor e é útil para dados simples que não exigem a quantidade de memória. Um fragmento pode salvar seu estado com uma chamada para `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando uma nova instância de um fragmento é criado, o estado salvo no `Bundle` serão disponibilizadas para a nova instância por meio de `OnCreate`, `OnCreateView`, e `OnActivityCreated` métodos da nova instância.
O exemplo a seguir demonstra como recuperar o valor `current_choice` do `Bundle`:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

Substituindo `OnSaveInstanceState` é um mecanismo apropriado para salvar dados transitórios em um fragmento em alterações de orientação, como o `current_choice` valor no exemplo acima. No entanto, a implementação padrão de `OnSaveInstanceState` se encarrega de salvar dados transitórios na interface do usuário para cada exibição que tenha uma ID atribuída. Por exemplo, examinar um aplicativo que tenha um `EditText` elemento definido em XML da seguinte maneira:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Uma vez que o `EditText` controle tem um `id` atribuído, o fragmento salva automaticamente os dados no widget quando `OnSaveInstanceState` é chamado.


### <a name="bundle-limitations"></a>Limitações de pacote

Embora o uso `OnSaveInstanceState` torna fácil salvar dados transitórios, uso desse método tem algumas limitações:

-  Se o fragmento não é adicionado à pilha voltar, seu estado não será restaurado quando o usuário pressiona o **volta** botão.

-  Quando o pacote é usado para salvar os dados, esses dados são serializados. Isso pode levar a atrasos de processamento.


## <a name="contributing-to-the-menu"></a>Contribuindo para o Menu

Fragmentos podem contribuir com itens no menu da sua atividade de hospedagem.
Uma atividade lida com itens de menu primeiro. Se a atividade não tem um manipulador, em seguida, o evento será passado para o fragmento, que, em seguida, tratá-la.

Para adicionar itens ao menu da atividade, um fragmento deve fazer duas coisas.
Primeiro, o fragmento deve implementar o método `OnCreateOptionsMenu` e coloque seus itens no menu, conforme mostrado no código a seguir:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

No menu de trecho de código anterior está inflado de XML a seguir, localizado no arquivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Em seguida, o fragmento deve chamar `SetHasOptionsMenu(true)`. A chamada para esse método lança do Android que o fragmento tem itens de menu para contribuir com o menu de opções. A menos que a chamada para esse método é feita, os itens de menu para o fragmento não serão adicionados ao menu de opção da atividade. Isso normalmente é feito no método de ciclo de vida `OnCreate()`, conforme mostrado no seguinte trecho de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

A tela a seguir mostra a aparência desse menu:

[![Captura de tela de exemplo de aplicativo de viagens meu exibir itens de menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
