---
title: Criando um fragmento
ms.topic: article
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: c1dd3495b0d7f76197126094cfd10e50d0ca760d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-fragment"></a>Criando um fragmento

Para criar um fragmento, uma classe deve herdar de `Android.App.Fragment` e, em seguida, substituir o `OnCreateView` método. `OnCreateView` será chamado pela atividade de hospedagem quando ele é hora de colocar o fragmento na tela e retornará um `View`. Um típico `OnCreateView` vai criá-lo `View` inflacionando um arquivo de layout e, em seguida, anexá-lo para um contêiner pai. Características do contêiner são importantes para Android aplicará os parâmetros do layout do pai para a interface do usuário do fragmento. O exemplo a seguir ilustra isto:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

O código acima irá aumentar o modo de exibição `Resource.Layout.Example_Fragment`e adicioná-lo como um filho de modo de exibição para o `ViewGroup` contêiner.


> [!NOTE]
> **Observação:** classes sub fragmento não devem ter um padrão público construtor nenhum argumento.

## <a name="adding-a-fragment-to-an-activity"></a>Adição de um fragmento de uma atividade

Há duas maneiras que um fragmento pode ser hospedado dentro de uma atividade:

-   **Declarativamente** &ndash; fragmentos podem ser usados declarativamente dentro `.axml` arquivos de layout usando o `<Fragment>` marca.

-   **Programaticamente** &ndash; fragmentos também podem ser instanciados dinamicamente usando a `FragmentManager` API da classe.

Uso programático por meio de `FragmentManager` classe será discutida posteriormente neste guia.

### <a name="using-a-fragment-declaratively"></a>Usar um fragmento declarativamente

Adição de um fragmento dentro do layout requer o uso de `<fragment>` marca e, em seguida, identificando o fragmento, fornecendo um o `class` atributo ou o `android:name` atributo. O trecho a seguir mostra como usar o `class` atributo para declarar um `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Este trecho de código seguinte mostra como declarar uma `fragment` usando o `android:name` atributo para identificar a classe de fragmento:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando a atividade está sendo criada, o Android será criar uma instância de cada fragmento especificado no arquivo de layout e inserir o modo de exibição é criado a partir `OnCreateView` em vez do `Fragment` elemento.
Fragmentos de forma declarativa são adicionados a uma atividade são estáticos e permanecem na atividade até que ele será destruído; não é possível substituir dinamicamente ou remover esse um fragmento durante o tempo de vida da atividade à qual ele está conectado.

Um identificador exclusivo deve ser atribuído a cada fragmento:

-  **Android: id** &ndash; assim como com outros elementos de interface do usuário em um arquivo de layout, isso é uma ID exclusiva.

-  **Android: marca** &ndash; esse atributo é uma cadeia de caracteres exclusiva.

Se nenhum dos dois métodos a anterior for usado, o fragmento assumirá a ID da exibição de contêiner. No exemplo a seguir onde nenhum `android:id` nem `android:tag` for fornecido, Android atribuirá a ID `fragment_container` ao fragmento:

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

Android não permite caracteres maiusculos em nomes de pacote; ela irá gerar uma exceção ao tentar aumentar o modo de exibição, se um nome de pacote contém um caractere maiusculo. No entanto, xamarin é mais tolerante com e será tolerar caracteres maiusculos no namespace.

Por exemplo, ambos os trechos de código a seguir funciona com xamarin. No entanto, o trecho de segundo fará com que um `android.view.InflateException` seja gerada por um aplicativo do Android baseados em Java puro.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OU

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo de vida de fragmento

Fragmentos têm seu próprios ciclo de vida que é independente da, mas ainda é afetado pelo, o [ciclo de vida da atividade de hospedagem](~/android/app-fundamentals/activity-lifecycle/index.md).
Por exemplo, quando uma atividade pausa, todos os seus fragmentos associados foram pausados. O diagrama a seguir descreve o ciclo de vida do fragmento.

[![Diagrama de fluxo que ilustra o ciclo de vida de fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png)


### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de criação de fragmento

A lista a seguir mostra o fluxo dos vários retornos de chamada no ciclo de vida de um fragmento que ele é criado:

-   **`OnInflate()`** &ndash; Chamado quando o fragmento está sendo criado como parte de um layout de exibição. Isso pode ser chamado imediatamente após a criação declarativamente o fragmento de um arquivo de layout XML. O fragmento não está associado a sua atividade, mas o **atividade**, **pacote**, e **AttributeSet** na exibição de hierarquia são passados como parâmetros. Este método é melhor utilizado para analisar o **AttributeSet** e para salvar os atributos que pode ser usada posteriormente por fragmento.

-   **`OnAttach()`** &ndash; Chamado depois que o fragmento é associado à atividade. Este é o primeiro método a ser executado quando o fragmento está pronto para ser usado. Em geral, fragmentos não devem implementar um construtor ou substituir o construtor padrão. Todos os componentes necessários para o fragmento devem ser inicializados neste método.

-   **`OnCreate()`** &ndash; Chamado pela atividade para criar o fragmento. Quando este método é chamado, a hierarquia do modo de exibição da atividade de hospedagem pode não ser completamente instanciada, portanto o fragmento não deve confiar em qualquer partes da hierarquia do modo de exibição da atividade até posteriormente no ciclo de vida do fragmento. Por exemplo, não use esse método para executar qualquer ajustes ou ajustes para a interface do usuário do aplicativo. Essa é a primeira hora em que o fragmento pode começar a coletar os dados necessários. O fragmento está em execução no thread de interface do usuário neste momento, para evitar qualquer processamento longo ou executar que o processamento em um thread em segundo plano. Esse método pode ser ignorado se **SetRetainInstance(true)** é chamado.
    Essa alternativa será descrita em mais detalhes abaixo.

-   **`OnCreateView()`** &ndash; Cria a exibição do fragmento.
    Este método é chamado uma vez a atividade **OnCreate()** método for concluído. Neste ponto, é seguro interagir com a hierarquia do modo de exibição da atividade. Esse método deve retornar o modo de exibição que será usado pelo fragmento.

-   **`OnActivityCreated()`** &ndash; Chamado após **OnCreate** foi concluída pela atividade de hospedagem.
    Ajustes finais para a interface do usuário devem ser executadas no momento.

-   **`OnStart()`** &ndash; Chamado depois que a atividade que contém foi retomada. Isso torna o fragmento visíveis para o usuário. Em muitos casos, o fragmento contém código que estaria no **OnStart ()** método de uma atividade.

-   **`OnResume()`** &ndash; Este é o último método chamado antes que o usuário pode interagir com o fragmento. Um exemplo do tipo de código que deve ser executado nesse método poderia ser habilitação dos recursos de um dispositivo que o usuário pode interagir com, como a câmera que os serviços de localização. Serviços como essas podem causar excessiva de bateria, porém, e um aplicativo deve minimizar seu uso para preservar a vida útil da bateria.


### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destruição de fragmento

A seguinte lista explica os métodos de ciclo de vida que são chamados como um fragmento está sendo destruído:

-   **`OnPause()`** &ndash; O usuário não é capaz de interagir com o fragmento. Essa situação existe porque alguma outra operação de fragmento está modificando desse fragmento ou a atividade de hospedagem está em pausa. É possível que a atividade de hospedagem desse fragmento ainda pode estar visível, ou seja, a atividade em foco é parcialmente transparente ou não ocupa a tela inteira. Quando este método se torna ativo, é a primeira indicação de que o usuário está saindo do fragmento. O fragmento deve salvar as alterações.

-   **`OnStop()`** &ndash; O fragmento não está mais visível. O atividade do host pode ter sido interrompido ou uma operação de fragmento é modificá-lo na atividade. Esse retorno de chamada tem a mesma finalidade como **Activity.OnStop**.

-   **`OnDestroyView()`** &ndash; Esse método é chamado para limpar os recursos associados com a exibição. Isso é chamado quando o modo de exibição associado ao fragmento foi destruído.

-   **`OnDestroy()`** &ndash; Esse método é chamado quando o fragmento não está mais em uso. Ele ainda está associado a atividade, mas o fragmento não estiver mais funcionando. Esse método deve liberar quaisquer recursos que estão em uso por um fragmento, como um [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) que pode ser usado para uma câmera. Esse método pode ser ignorado se **SetRetainInstance(true)** é chamado. Essa alternativa será descrita em mais detalhes abaixo.

-   **`OnDetach()`** &ndash; Este método é chamado antes do fragmento não está mais associado à atividade. A hierarquia de visualização do fragmento não existe, e todos os recursos que são usados pelo fragmento devem ser liberados neste momento.


### <a name="using-setretaininstance"></a>Usando SetRetainInstance

É possível que um fragmento especificar que ele deve ser completamente destruído se a atividade estiver sendo recriada. O `Fragment` classe fornece o método `SetRetainInstance` para essa finalidade. Se `true` é passado para este método quando a atividade for reiniciada, a mesma instância do fragmento será usado. Se isso acontecer, todos os métodos de retorno de chamada serão invocados exceto o `OnCreate` e `OnDestroy` retornos de chamada do ciclo de vida. Esse processo é ilustrado no diagrama de ciclo de vida mostrado acima (pelas linhas pontilhadas verdes).


## <a name="fragment-state-management"></a>Gerenciamento de estado de fragmento

Fragmentos pode salvar e restaurar o estado durante o ciclo de vida do fragmento usando uma instância de um `Bundle`. O pacote permite que um fragmento salvar dados como pares chave/valor e é útil para dados simples que não exigem muita memória. Um fragmento pode salvar seu estado com uma chamada para `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando uma nova instância de um fragmento é criado, o estado salvo no `Bundle` estará disponível para a nova instância por meio de `OnCreate`, `OnCreateView`, e `OnActivityCreated` métodos da nova instância.
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

Substituindo `OnSaveInstanceState` é um mecanismo apropriado para salvar dados temporários em um fragmento entre as alterações de orientação, como o `current_choice` valor no exemplo acima. No entanto, a implementação padrão de `OnSaveInstanceState` cuida de salvar dados transitórios na interface de usuário para cada modo de exibição que tenha uma ID atribuída. Por exemplo, procure em um aplicativo que tenha um `EditText` elemento definido no XML da seguinte maneira:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Como o `EditText` controle tem um `id` atribuído, o fragmento salva automaticamente os dados no widget quando `OnSaveInstanceState` é chamado.


### <a name="bundle-limitations"></a>Limitações de pacote

Embora o uso `OnSaveInstanceState` torna fácil a salvar dados transitórios, use esse método tem algumas limitações:

-  Se o fragmento não é adicionado à pilha de volta, seu estado não será restaurado quando o usuário pressiona o **novamente** botão.

-  Quando o pacote é usado para salvar os dados, que dados são serializados. Isso pode levar a atrasos de processamento.


## <a name="contributing-to-the-menu"></a>Que contribuem para o Menu

Fragmentos podem contribuir itens no menu de sua atividade de hospedagem.
Uma atividade manipula primeiro os itens de menu. Se a atividade não tem um manipulador, em seguida, o evento será passado ao fragmento, que, em seguida, tratá-la.

Para adicionar itens de menu da atividade, um fragmento deve fazer duas coisas.
Primeiro, o fragmento deve implementar o método `OnCreateOptionsMenu` e coloque seus itens no menu, conforme mostrado no código a seguir:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

No menu do trecho de código anterior é inflado de XML a seguir, localizado no arquivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Em seguida, o fragmento deve chamar `SetHasOptionsMenu(true)`. A chamada a este método lança para Android que o fragmento tem itens de menu para contribuir com a opção menu. A menos que a chamada para esse método é feita, os itens de menu do fragmento não serão adicionados ao menu de opções da atividade. Isso geralmente é feito no método do ciclo de vida `OnCreate()`, conforme mostrado no seguinte trecho de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

A tela a seguir mostra como esse menu seria:

[![Captura de tela de exemplo de viagens de meu aplicativo exibindo itens de menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png)
